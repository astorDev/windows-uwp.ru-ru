---
author: stevewhims
description: В этом разделе показаны способы, которыми можно создавать и использовать асинхронные объекты среды выполнения Windows с помощью C++/WinRT.
title: Параллельная обработка и асинхронные операции с помощью C++/WinRT
ms.author: stwhi
ms.date: 05/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c ++, cpp, winrt, проекция, параллелизм, async, асинхронный, асинхронность
ms.localizationpriority: medium
ms.openlocfilehash: 85071fb28cb87c991e2f5ba7f64b681c6850c819
ms.sourcegitcommit: a160b91a554f8352de963d9fa37f7df89f8a0e23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2018
ms.locfileid: "4127709"
---
# <a name="concurrency-and-asynchronous-operations-with-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Параллельная обработка и асинхронные операции с использованием [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)
> [!NOTE]
> **Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.**

В этом разделе показаны способы, которыми можно создавать и использовать асинхронные объекты среды выполнения Windows с помощью C++/WinRT.

## <a name="asynchronous-operations-and-windows-runtime-async-functions"></a>Асинхронные операции и функции "Async" среды выполнения Windows
Любой API-интерфейс среды выполнения Windows, завершение которого может занять более 50 миллисекунд, реализуется как асинхронная функция (с именем, оканчивающимся на "Async"). Реализация асинхронной функции инициирует работу в другом потоке и немедленно возвращает объект, представляющий асинхронную операцию. После завершения асинхронной операции возвращаемый объект, содержит любое значение, которое является результатом работы. Пространство имен среды выполнения Windows **Windows::Foundation** содержит четыре типа объектов асинхронной операции.

- [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction),
- [**IAsyncActionWithProgress&lt;TProgress&gt;**](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_),
- [**IAsyncOperation&lt;TResult&gt;**](/uwp/api/windows.foundation.iasyncoperation_tresult_) и
- [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_).

Каждый из этих типов асинхронной операции проецируется в соответствующий тип в пространстве имен C++/WinRT **winrt::Windows::Foundation**. C++/WinRT также содержит внутреннюю структуру адаптера await. Она не используется напрямую, но благодаря этой структуре вы можете написать оператор co_await, чтобы совместно ожидать результата любой функции, которая возвращает один из этих типов асинхронной операции. И вы можете создавать свои собственные сопрограммы, возвращающие эти типы.

Пример асинхронной функции Windows — [**SyndicationClient::RetrieveFeedAsync**](https://docs.microsoft.com/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync), который возвращает объект асинхронной операции типа [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_). Рассмотрим некоторые блокирующие и не блокирующие способы использования C++/WinRT для вызова подобного API.

## <a name="block-the-calling-thread"></a>Блокировка вызывающего потока
Приведенный ниже пример кода получает объект асинхронной операцию от **RetrieveFeedAsync** и вызывает **get** для этого объекта, чтобы заблокировать вызывающий поток до тех пор, пока не будут доступны результаты выполнения асинхронной операции.

```cppwinrt
// main.cpp

#include "pch.h"
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Syndication.h>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;

void ProcessFeed()
{
    Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
    SyndicationClient syndicationClient;
    SyndicationFeed syndicationFeed = syndicationClient.RetrieveFeedAsync(rssFeedUri).get();
    // use syndicationFeed.
}

int main()
{
    winrt::init_apartment();
    ProcessFeed();
}
```

Вызов **get** обеспечивает удобство написания кода, и он идеально подходит для консольных приложений и фоновых потоков, в которых по какой-либо причине вам не хочется использовать сопрограмму. Однако он не является ни одновременным, ни асинхронным, поэтому не подходит для потока пользовательского интерфейса (и в неоптимизированных сборках при попытке его использования будет появляться уведомление об этом). Чтобы потоки операционной системы могли выполнять другую полезную работу, требуется иной способ.

## <a name="write-a-coroutine"></a>Написание сопрограммы
C++/WinRT интегрирует сопрограммы C++ в модель программирования для обеспечения естественного способа совместно ожидать результата. Вы можете создать свою собственную асинхронную операцию среды выполнения Windows путем написания сопрограммы. В следующем примере кода **ProcessFeedAsync** является сопрограммой.

> [!NOTE]
> Функция **get** существует на C + +/ WinRT проекции введите **winrt::Windows::Foundation::IAsyncAction**, поэтому можно вызвать функцию в C + +/ WinRT проекта. Не позволит найти функцию указаны в является членом интерфейса [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction) , так как **получить** не является частью поверхности приложения двоичный интерфейс (ABI) фактического типа среды выполнения Windows **IAsyncAction**.

```cppwinrt
// main.cpp

#include "pch.h"
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Syndication.h>
#include <iostream>

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

    auto processOp = ProcessFeedAsync();
    // do other work while the feed is being printed.
    processOp.get(); // no more work to do; call get() so that we see the printout before the application exits.
}
```

Сопрограмма — это функция с возможностью приостановки и возобновления. В сопрограмме **ProcessFeedAsync**, представленной выше, при достижении оператора `co_await` сопрограмма асинхронно инициирует вызов **RetrieveFeedAsync**, а затем немедленно приостанавливает себя и возвращает управление вызывающему объекту (которым в примере выше является **main**). Затем **main** может продолжить работать, пока происходит извлечение и печать веб-канала. Когда это действие будет выполнено (после завершения вызова **RetrieveFeedAsync**), сопрограмма **ProcessFeedAsync** возобновит работу на следующем операторе.

Сопрограммы можно объединять в другие сопрограммы. Или можно вызвать **get** для блокировки и дождаться завершения (и получить результат, если таковой имеется). Кроме того, можно передать сопрограмму в другой язык программирования, который поддерживает среду выполнения Windows.

Также можно обрабатывать завершенные и (или) текущие события из асинхронных действий и операций с помощью делегатов. Подробные сведения и примеры кода см. в разделе [Типы делегатов для асинхронных действий и операций](handle-events.md#delegate-types-for-asynchronous-actions-and-operations).

## <a name="asychronously-return-a-windows-runtime-type"></a>Асинхронное возвращение типа среды выполнения Windows
В следующем примере мы создаем оболочку вызова **RetrieveFeedAsync** для определенного URI, чтобы получить функцию **RetrieveBlogFeedAsync**, которая асинхронно возвращает [**SyndicationFeed **](/uwp/api/windows.web.syndication.syndicationfeed).

```cppwinrt
// main.cpp

#include "pch.h"
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Syndication.h>
#include <iostream>

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

    auto feedOp = RetrieveBlogFeedAsync();
    // do other work.
    PrintFeed(feedOp.get());
}
```

В приведенном выше примере **RetrieveBlogFeedAsync** возвращает **IAsyncOperationWithProgress**, который имеет ход выполнения и возвращаемое значение. Мы можем выполнять другую работу в то время как **RetrieveBlogFeedAsync** выполняет свою и получает канал. После этого мы вызываем **get** в этом объекте асинхронной операции для блокировки, дожидаемся его завершения, а затем получаем результаты операции.

Если вы асинхронно возвращаете тип среды выполнения Windows, то вам необходимо возвращать [**IAsyncOperation&lt;TResult&gt;**](/uwp/api/windows.foundation.iasyncoperation_tresult_) или [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_). Для этого подходит любой класс среды выполнения собственной или сторонней разработки, а также любой тип, который может передаваться в функцию среды выполнения Windows или из нее (например, `int` или **winrt::hstring**). Компилятор поможет вам, отобразив ошибку "*must be WinRT type*" ("требуется тип WinRT"), если вы попытаетесь использовать один из этих типов асинхронных операций с типом, который не является типом среды выполнения Windows.

Если в сопрограмме нет хотя бы одного оператора `co_await`, то, чтобы считаться сопрограммой, в ней должен быть хотя бы один оператор `co_return` или `co_yield`. Возникнут ситуации, в которых ваша сопрограмма может возвращать значение без добавления какой-либо асинхронности и, соответственно, без блокировки и переключения контекста. Вот пример, в котором это производится (при втором и последующих вызовах) с помощью кэширования значения.

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
Если вы асинхронно возвращаете тип, который *не* является типом среды выполнения Windows, то вам необходимо возвращать шаблон [**concurrency::task**](/cpp/parallel/concrt/reference/task-class) из библиотеки параллельных шаблонов (PPL). Мы рекомендуем **concurrency::task**, поскольку он обеспечивает более высокую производительность (и в дальнейшем улучшенную совместимость), чем **std::future**.

> [!TIP]
> Если включить `<pplawait.h>`, то вы можете использовать **concurrency::task** в качестве типа сопрограммы.

```cppwinrt
// main.cpp

#include "pch.h"
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Syndication.h>
#include <iostream>
#include <ppltasks.h>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;

concurrency::task<std::wstring> RetrieveFirstTitleAsync()
{
    return concurrency::create_task([]
    {
        Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
        SyndicationClient syndicationClient;
        SyndicationFeed syndicationFeed = syndicationClient.RetrieveFeedAsync(rssFeedUri).get();
        return std::wstring{ syndicationFeed.Items().GetAt(0).Title().Text() };
    });
}

int main()
{
    winrt::init_apartment();

    auto firstTitleOp = RetrieveFirstTitleAsync();
    // Do other work here.
    std::wcout << firstTitleOp.get() << std::endl;
}
```

## <a name="parameter-passing"></a>Передача параметров
Для синхронных функций следует использовать параметры по умолчанию `const&`. Это позволит избежать чрезмерной нагрузки, связанной с копиями (которая включает подсчет ссылок, а это означает блокировку операций увеличения и уменьшения).

```cppwinrt
// Synchronous function.
void DoWork(Param const& value);
```

Однако здесь могут возникать проблемы при передаче параметра ссылки сопрограмме.

```cppwinrt
// NOT the recommended way to pass a value to a coroutine!
IASyncAction DoWorkAsync(Param const& value)
{
    // While it's ok to access value here...
    
    co_await DoOtherWorkAsync();

    // ...accessing value here carries no guarantees of safety.
}
```

В сопрограмме выполнение происходит синхронно до первой точки приостановки, где управление возвращается вызывающей стороне. К моменту возобновления сопрограммы с исходным значением, на которое ссылается отсылочный параметр, может случиться что угодно. С точки зрения сопрограммы у отсылочного параметра неконтролируемый срок существования. Таким образом, в приведенном выше примере мы безопасно можем получить доступ к *value* до вызова `co_await`, но не после него. Мы также не можем безопасно передать *value* в **DoOtherWorkAsync**, если есть малейший риск того, что эта функция в свою очередь приостановится и затем попробует использовать *value* после возобновления. Чтобы обеспечить безопасное использование параметров после приостановки и возобновления, ваши сопрограммы должны по умолчанию использовать передачу по значению, чтобы они точно получали данные по значениям и избегали проблем со сроком существования. Ситуации, в которых можно не соблюдать эти указания, потому что вы уверены, что это безопасно, встречаются редко.

```cppwinrt
// Coroutine
IASyncAction DoWorkAsync(Param value);
```

Кроме того, передача по значению const (если вы не собираетесь переместить значение) является не лучшей практикой. Это никак не повлияет на исходное значение, копию которого вы делаете, но явно сообщает намерение и помогает в случае неумышленного изменения копии.
    
```cppwinrt
// coroutine with strictly unnecessary const (but arguably good practice).
IASyncAction DoWorkAsync(Param const value);
```

См. также раздел [Стандартные массивы и векторы](std-cpp-data-types.md#standard-arrays-and-vectors), в котором описываются методы передачи стандартного вектора в асинхронный вызываемый элемент.

## <a name="offloading-work-onto-the-windows-thread-pool"></a>Передача работы в пул потоков Windows
Перед передачей работы на вычисления в сопрограмме необходимо вернуть выполнение вызывающей стороне, чтобы не заблокировать вызывающую сторону (другими словами, создать риск приостановки). Если вы еще не делаете этого путем применения `co-await` к какой-либо другой операции, то можете применить `co-await` к функции **winrt::resume_background**. Это возвращает управление вызывающей стороне и затем сразу же возобновляет выполнение в потоке пула потоков.

Пул потоков, используемый в реализации, является низкоуровневым [пулом потоков Windows](https://msdn.microsoft.com/library/windows/desktop/ms686766), поэтому он наиболее эффективен.

```cppwinrt
IAsyncOperation<uint32_t> DoWorkOnThreadPoolAsync()
{
    co_await winrt::resume_background(); // Return control; resume on thread pool.

    uint32_t result;
    for (uint32_t y = 0; y < height; ++y)
    for (uint32_t x = 0; x < width; ++x)
    {
        // Do compute-bound work here.
    }
    co_return result;
}
```

## <a name="programming-with-thread-affinity-in-mind"></a>Программирование с учетом сходства потоков
Этот сценарий основан на предыдущем. Вы передаете работу в пул потоков, но затем необходимо отображать ход выполнения в пользовательском интерфейсе (UI).

```cppwinrt
IAsyncAction DoWorkAsync(TextBlock textblock)
{
    co_await winrt::resume_background();
    // Do compute-bound work here.

    textblock.Text(L"Done!"); // Error: TextBlock has thread affinity.
}
```

Приведенный выше код выдает исключение [**winrt::hresult_wrong_thread**](/uwp/cpp-ref-for-winrt/hresult-wrong-thread), поскольку **TextBlock** должен обновляться из создавшего его потока, а это поток пользовательского интерфейса. Одним из решений является захват контекста потока, в котором изначально была вызвана наша сопрограмма. Создайте экземпляр объекта **winrt::apartment_context**, а затем примените к нему `co_await`.

```cppwinrt
IAsyncAction DoWorkAsync(TextBlock textblock)
{
    winrt::apartment_context ui_thread; // Capture calling context.

    co_await winrt::resume_background();
    // Do compute-bound work here.

    co_await ui_thread; // Switch back to calling context.

    textblock.Text(L"Done!"); // Ok if we really were called from the UI thread.
}
```

Если приведенная выше сопрограмма вызывается из потока пользовательского интерфейса, создавшего **TextBlock**, этот метод работает. В вашем приложении будет множество ситуаций, где вы будете уверены в этом.

> [!NOTE]
> **Приведенный ниже код относится к предварительной версии продукта, в которую к моменту выпуска официальной версии могут быть внесены значительные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.**

Для получения более универсального решения для обновления пользовательского интерфейса, в котором рассматриваются случаи, где у вас нет уверенности по поводу вызывающего потока, можно установить [предварительную сборку 17661 пакета SDK Windows 10](https://www.microsoft.com/software-download/windowsinsiderpreviewSDK) или более позднюю версию. Затем вы сможете применить `co-await` к функции **winrt::resume_foreground**, чтобы переключиться на определенный поток переднего плана. В следующем примере кода мы указываем поток переднего плана, передавая объект-диспетчер, связанный с **TextBlock** (обращаясь к его свойству [**Dispatcher**](/uwp/api/windows.ui.xaml.dependencyobject.dispatcher#Windows_UI_Xaml_DependencyObject_Dispatcher)). Реализация **winrt::resume_foreground** вызывает [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync) на этом объекте-диспетчере для выполнения работы, поступающей далее в сопрограмме.

```cppwinrt
IAsyncAction DoWorkAsync(TextBlock textblock)
{
    co_await winrt::resume_background();
    // Do compute-bound work here.

    co_await winrt::resume_foreground(textblock.Dispatcher()); // Switch to the foreground thread associated with textblock.

    textblock.Text(L"Done!"); // Guaranteed to work.
}
```

## <a name="important-apis"></a>Важные API
* [Класс Concurrency::Task](/cpp/parallel/concrt/reference/task-class)
* [Интерфейс IAsyncAction](/uwp/api/windows.foundation.iasyncaction)
* [IAsyncActionWithProgress&lt;TProgress&gt; интерфейс](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_)
* [IAsyncOperation&lt;TResult&gt; интерфейс](/uwp/api/windows.foundation.iasyncoperation_tresult_)
* [IAsyncOperationWithProgress&lt;TResult, TProgress&gt; интерфейс](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_)
* [Метод SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [Класс SyndicationFeed](/uwp/api/windows.web.syndication.syndicationfeed)

## <a name="related-topics"></a>Смежные разделы
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)
* [Стандартные типы данных C++ и C++/WinRT](std-cpp-data-types.md)