---
description: В этом разделе показаны способы, которыми можно создавать и использовать асинхронные объекты среды выполнения Windows с помощью C++/WinRT.
title: Параллельные обработка и выполнение асинхронных операций с помощью C++/WinRT
ms.date: 07/08/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, concurrency, async, asynchronous, asynchrony
ms.localizationpriority: medium
ms.openlocfilehash: 1dd6ac2760189578932fc22db89c7091f2e527ab
ms.sourcegitcommit: 8179902299df0f124dd770a09a5a332397970043
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68428640"
---
# <a name="concurrency-and-asynchronous-operations-with-cwinrt"></a>Параллельные обработка и выполнение асинхронных операций с помощью C++/WinRT

В этом разделе показаны способы, которыми можно создавать и использовать асинхронные объекты среды выполнения Windows с помощью [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

После прочтения этой статьи ознакомьтесь с дополнительными сведениями о [более сложных сценариях параллелизма и асинхронности](concurrency-2.md).

## <a name="asynchronous-operations-and-windows-runtime-async-functions"></a>Асинхронные операции и функции "Async" среды выполнения Windows

Любой API-интерфейс среды выполнения Windows, для выполнения которого может потребоваться более 50 миллисекунд, реализуется как асинхронная функция (с именем, оканчивающимся на "Async"). Реализация асинхронной функции инициирует работу в другом потоке и немедленно возвращает объект, представляющий асинхронную операцию. После выполнения асинхронной операции возвращаемый объект, содержит любое значение, которое является результатом работы. Пространство имен среды выполнения Windows **Windows::Foundation** содержит четыре типа объектов асинхронной операции.

- [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction),
- [**IAsyncActionWithProgress&lt;TProgress&gt;** ](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_),
- [**IAsyncOperation&lt;TResult&gt;** ](/uwp/api/windows.foundation.iasyncoperation_tresult_) и
- [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;** ](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_).

Каждый из этих типов асинхронной операции проецируется в соответствующий тип в пространстве имен C++/WinRT **winrt::Windows::Foundation**. C++/WinRT также содержит внутреннюю структуру адаптера ожидания. Она не используется напрямую, но благодаря этой структуре вы можете написать оператор `co_await`, чтобы совместно ожидать результата любой функции, которая возвращает один из этих типов асинхронной операции. И вы можете создавать свои собственные соподпрограммы, возвращающие эти типы.

Пример асинхронной функции Windows — функция [**SyndicationClient::RetrieveFeedAsync**](https://docs.microsoft.com/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync), которая возвращает объект асинхронной операции типа [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;** ](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_). Рассмотрим сперва некоторые &mdash;блокирующие, а затем не блокирующие&mdash; способы использования C++/WinRT для вызова подобного API.

## <a name="block-the-calling-thread"></a>Блокировка вызывающего потока

Приведенный ниже пример кода получает объект асинхронной операции от **RetrieveFeedAsync** и вызывает **get** для этого объекта, чтобы заблокировать вызывающий поток до тех пор, пока не будут доступны результаты выполнения асинхронной операции.

Если вы хотите скопировать и вставить этот пример непосредственно в главный файл исходного кода проекта **консольного приложения Windows (C++/WinRT)** , сначала задайте параметр **Не использовать предварительно скомпилированные заголовки** в свойствах проекта.

```cppwinrt
// main.cpp
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Syndication.h>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;

void ProcessFeed()
{
    Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
    SyndicationClient syndicationClient;
    SyndicationFeed syndicationFeed{ syndicationClient.RetrieveFeedAsync(rssFeedUri).get() };
    // use syndicationFeed.
}

int main()
{
    winrt::init_apartment();
    ProcessFeed();
}
```

Вызов **get** обеспечивает удобство написания кода и идеально подходит для консольных приложений, а также фоновых потоков, в которых по какой-либо причине не следует использовать соподпрограмму. Однако он не является ни одновременным, ни асинхронным, поэтому не подходит для потока пользовательского интерфейса (и в неоптимизированных сборках при попытке его использования будет выдано утверждение). Чтобы потоки операционной системы могли выполнять другую полезную работу, требуется иной способ.

## <a name="write-a-coroutine"></a>Написание соподпрограммы

C++/WinRT интегрирует соподпрограммы C++ в модель программирования для обеспечения естественного способа совместного ожидания результата. Вы можете создать свою собственную асинхронную операцию среды выполнения Windows путем написания соподпрограммы. В следующем примере кода **ProcessFeedAsync** является соподпрограммой.

> [!NOTE]
> Функция **get** существует в типе проекции C++/WinRT **winrt::Windows::Foundation::IAsyncAction**, так что ее можно вызвать в любом проекте C++/WinRT. Эта функция отсутствует в перечне элементов интерфейса [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction), поскольку **get** не является частью области двоичного интерфейса приложения фактического типа среды выполнения Windows **IAsyncAction**.

```cppwinrt
// main.cpp
#include <iostream>
#include <winrt/Windows.Foundation.Collections.h>
#include <winrt/Windows.Web.Syndication.h>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;

void PrintFeed(SyndicationFeed const& syndicationFeed)
{
    for (SyndicationItem const& syndicationItem : syndicationFeed.Items())
    {
        std::wcout << syndicationItem.Title().Text().c_str() << std::endl;
    }
}

IAsyncAction ProcessFeedAsync()
{
    Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
    SyndicationClient syndicationClient;
    SyndicationFeed syndicationFeed{ co_await syndicationClient.RetrieveFeedAsync(rssFeedUri) };
    PrintFeed(syndicationFeed);
}

int main()
{
    winrt::init_apartment();

    auto processOp{ ProcessFeedAsync() };
    // do other work while the feed is being printed.
    processOp.get(); // no more work to do; call get() so that we see the printout before the application exits.
}
```

Соподпрограмма — это функция с возможностью приостановки и возобновления. В соподпрограмме **ProcessFeedAsync**, представленной выше, при достижении оператора `co_await` соподпрограмма асинхронно инициирует вызов **RetrieveFeedAsync**, а затем немедленно приостанавливает себя и возвращает управление вызывающему объекту (которым в примере выше является **main**). Затем **main** может продолжить работать, пока происходит извлечение и печать веб-канала. Когда это действие будет выполнено (после завершения вызова **RetrieveFeedAsync**), соподпрограмма **ProcessFeedAsync** возобновит работу на следующем операторе.

Соподпрограммы можно объединять в другие соподпрограммы. Или можно вызвать **get** для блокировки и дождаться завершения (а также получить результат, если таковой имеется). Кроме того, можно передать соподпрограмму в другой язык программирования, который поддерживает среду выполнения Windows.

Также можно обрабатывать завершенные и (или) текущие события из асинхронных действий и операций с помощью делегатов. Подробные сведения и примеры кода см. в разделе [Типы делегатов для асинхронных действий и операций](handle-events.md#delegate-types-for-asynchronous-actions-and-operations).

## <a name="asychronously-return-a-windows-runtime-type"></a>Асинхронное возвращение типа среды выполнения Windows

В следующем примере мы создаем оболочку вызова **RetrieveFeedAsync** для определенного URI, чтобы получить функцию **RetrieveBlogFeedAsync**, которая асинхронно возвращает [**SyndicationFeed** ](/uwp/api/windows.web.syndication.syndicationfeed).

```cppwinrt
// main.cpp
#include <iostream>
#include <winrt/Windows.Foundation.Collections.h>
#include <winrt/Windows.Web.Syndication.h>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;

void PrintFeed(SyndicationFeed const& syndicationFeed)
{
    for (SyndicationItem const& syndicationItem : syndicationFeed.Items())
    {
        std::wcout << syndicationItem.Title().Text().c_str() << std::endl;
    }
}

IAsyncOperationWithProgress<SyndicationFeed, RetrievalProgress> RetrieveBlogFeedAsync()
{
    Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
    SyndicationClient syndicationClient;
    return syndicationClient.RetrieveFeedAsync(rssFeedUri);
}

int main()
{
    winrt::init_apartment();

    auto feedOp{ RetrieveBlogFeedAsync() };
    // do other work.
    PrintFeed(feedOp.get());
}
```

В приведенном выше примере **RetrieveBlogFeedAsync** возвращает **IAsyncOperationWithProgress** с данными о ходе выполнения и возвращаемым значением. Мы можем выполнять другую работу пока **RetrieveBlogFeedAsync** выполняет свою и получает канал. После этого мы вызываем **get** для этого объекта асинхронной операции с целью блокировки, дожидаемся его завершения, а затем получаем результаты операции.

Если вы асинхронно возвращаете тип среды выполнения Windows, то вам необходимо возвращать [**IAsyncOperation&lt;TResult&gt;** ](/uwp/api/windows.foundation.iasyncoperation_tresult_) или [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;** ](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_). Для этого подходит любой класс среды выполнения собственной или сторонней разработки, а также любой тип, который может передаваться в функцию среды выполнения Windows или из нее (например, `int` или **winrt::hstring**). Компилятор поможет вам, отобразив ошибку "*must be WinRT type*" ("требуется тип WinRT"), если вы попытаетесь использовать один из этих типов асинхронных операций с типом, который не является типом среды выполнения Windows.

Если в соподпрограмме нет хотя бы одного оператора `co_await`, чтобы считаться соподпрограммой, в ней должен быть хотя бы один оператор `co_return` или `co_yield`. При этом будут возникать ситуации, в которых ваша соподпрограмма может возвращать значение без добавления какой-либо асинхронности и, соответственно, без блокировки и переключения контекста. Вот пример, в котором это реализовано (при втором и последующих вызовах) за счет кэширования значения.

```cppwinrt
winrt::hstring m_cache;

IAsyncOperation<winrt::hstring> ReadAsync()
{
    if (m_cache.empty())
    {
        // Asynchronously download and cache the string.
    }
    co_return m_cache;
}
``` 

## <a name="asychronously-return-a-non-windows-runtime-type"></a>Асинхронное возвращение типа, не являющегося типом среды выполнения Windows

Если вы асинхронно возвращаете тип, который *не* является типом среды выполнения Windows, вам необходимо возвращать класс [**concurrency::task**](/cpp/parallel/concrt/reference/task-class) из библиотеки параллельных шаблонов (PPL). Мы рекомендуем **concurrency::task**, поскольку он обеспечивает более высокую производительность (и в дальнейшем улучшенную совместимость), чем **std::future**.

> [!TIP]
> Если включить `<pplawait.h>`, то вы можете использовать **concurrency::task** в качестве типа соподпрограммы.

```cppwinrt
// main.cpp
#include <iostream>
#include <ppltasks.h>
#include <winrt/Windows.Foundation.Collections.h>
#include <winrt/Windows.Web.Syndication.h>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;

concurrency::task<std::wstring> RetrieveFirstTitleAsync()
{
    return concurrency::create_task([]
        {
            Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
            SyndicationClient syndicationClient;
            SyndicationFeed syndicationFeed{ syndicationClient.RetrieveFeedAsync(rssFeedUri).get() };
            return std::wstring{ syndicationFeed.Items().GetAt(0).Title().Text() };
        });
}

int main()
{
    winrt::init_apartment();

    auto firstTitleOp{ RetrieveFirstTitleAsync() };
    // Do other work here.
    std::wcout << firstTitleOp.get() << std::endl;
}
```

## <a name="parameter-passing"></a>Передача параметров

Для синхронных функций следует использовать заданные по умолчанию параметры `const&`. Это позволит избежать дополнительной нагрузки, связанной с копиями (в частности подсчета ссылок, предполагающего непрерывающееся выполнение операций увеличения и уменьшения).

```cppwinrt
// Synchronous function.
void DoWork(Param const& value);
```

Однако здесь могут возникать проблемы при передаче параметра ссылки соподпрограмме.

```cppwinrt
// NOT the recommended way to pass a value to a coroutine!
IASyncAction DoWorkAsync(Param const& value)
{
    // While it's ok to access value here...

    co_await DoOtherWorkAsync(); // (this is the first suspension point)...

    // ...accessing value here carries no guarantees of safety.
}
```

В сопрограмме выполнение происходит синхронно до первой точки приостановки, где управление возвращается вызывающему объекту, и соответствующий фрейм больше не используется. К моменту возобновления соподпрограммы с исходным значением, на которое ссылается отсылочный параметр, может случиться что угодно. С точки зрения соподпрограммы у отсылочного параметра неконтролируемый срок существования. Таким образом, в приведенном выше примере мы безопасно можем получить доступ к *value* до вызова `co_await`, но не после него. Если *value* уничтожается вызывающим объектом, пытающимся получить доступ к внутренней сопрограмме, это приводит к повреждению памяти. Мы также не можем безопасно передать *value* в **DoOtherWorkAsync**, если есть малейший риск того, что эта функция в свою очередь приостановится, а затем попытается использовать *value* после возобновления.

Чтобы обеспечить безопасное использование параметров после приостановки и возобновления, сопрограммы должны по умолчанию использовать передачу по значению. Так данные будут собираться только по значению, предотвращая появление проблем с временем существования. Ситуации, в которых можно не соблюдать эти указания в связи с уверенностью в безопасности такого подхода, встречаются редко.

```cppwinrt
// Coroutine
IASyncAction DoWorkAsync(Param value); // not const&
```

При передаче по значению требуется, чтобы аргумент было несложно перемещать или копировать, что характерно для смарт-указателя.

Кроме того, не рекомендуется использовать передачу по значению const (если вы не собираетесь переместить значение). Это никак не влияет на исходное значение, копию которого вы создаете, но явно сообщает намерение и помогает в случае неумышленного изменения копии.

```cppwinrt
// coroutine with strictly unnecessary const (but arguably good practice).
IASyncAction DoWorkAsync(Param const value);
```

С дополнительными сведениями можно также ознакомиться в разделе [Стандартные массивы и векторы](std-cpp-data-types.md#standard-arrays-and-vectors), в котором описываются методы передачи стандартного вектора в асинхронный вызываемый элемент.

Если нельзя изменить подпись сопрограммы, но можно изменить реализацию, создайте локальную копию перед первым экземпляром `co_await`.

```cppwinrt
IASyncAction DoWorkAsync(Param const& value)
{
    auto safe_value = value;
    // It's ok to access both safe_value and value here.

    co_await DoOtherWorkAsync();

    // It's ok to access only safe_value here (not value).
}
```

Если `Param` сложно копировать, извлеките только те элементы, которые необходимы перед первым экземпляром `co_await`.

```cppwinrt
IASyncAction DoWorkAsync(Param const& value)
{
    auto safe_data = value.data;
    // It's ok to access safe_data, value.data, and value here.

    co_await DoOtherWorkAsync();

    // It's ok to access only safe_data here (not value.data, nor value).
}
```

## <a name="safely-accessing-the-this-pointer-in-a-class-member-coroutine"></a>Безопасный доступ к указателю *this* в сопрограмме членов класса

См. статью [Сильные и слабые ссылки в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/weak-references#safely-accessing-the-this-pointer-in-a-class-member-coroutine).

## <a name="important-apis"></a>Важные API
* [Класс concurrency::task](/cpp/parallel/concrt/reference/task-class)
* [Интерфейс IAsyncAction](/uwp/api/windows.foundation.iasyncaction)
* [Интерфейс IAsyncActionWithProgress&lt;TProgress&gt;](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_)
* [Интерфейс IAsyncOperation&lt;TResult&gt;](/uwp/api/windows.foundation.iasyncoperation_tresult_)
* [Интерфейс IAsyncOperationWithProgress&lt;TResult, TProgress&gt;](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_)
* [Метод SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [Класс SyndicationFeed](/uwp/api/windows.web.syndication.syndicationfeed)

## <a name="related-topics"></a>Статьи по теме
* [Более сложные сценарии параллельной обработки и асинхронности](concurrency-2.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)
* [Стандартные типы данных C++ и C++/WinRT](std-cpp-data-types.md)