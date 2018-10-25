---
author: stevewhims
description: В этом разделе показаны способы, которыми можно создавать и использовать асинхронные объекты среды выполнения Windows с помощью C++/WinRT.
title: Параллельная обработка и асинхронные операции с помощью C++/WinRT
ms.author: stwhi
ms.date: 10/21/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c ++, cpp, winrt, проекция, параллелизм, async, асинхронный, асинхронность
ms.localizationpriority: medium
ms.openlocfilehash: 96a1a5d4636bc96d774071514b77ad8bde4df6be
ms.sourcegitcommit: 2c4daa36fb9fd3e8daa83c2bd0825f3989d24be8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "5521119"
---
# <a name="concurrency-and-asynchronous-operations-with-cwinrt"></a>Параллельная обработка и асинхронные операции с помощью C++/WinRT

В этом разделе показаны способы, которые можно выполнить оба Создание и использование асинхронных объектов среды выполнения Windows с помощью [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

## <a name="asynchronous-operations-and-windows-runtime-async-functions"></a>Асинхронные операции и функции "Async" среды выполнения Windows

Любой API-интерфейс среды выполнения Windows, завершение которого может занять более 50 миллисекунд, реализуется как асинхронная функция (с именем, оканчивающимся на "Async"). Реализация асинхронной функции инициирует работу в другом потоке и немедленно возвращает объект, представляющий асинхронную операцию. После завершения асинхронной операции возвращаемый объект, содержит любое значение, которое является результатом работы. Пространство имен среды выполнения Windows **Windows::Foundation** содержит четыре типа объектов асинхронной операции.

- [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction),
- [**IAsyncActionWithProgress&lt;TProgress&gt;**](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_),
- [**IAsyncOperation&lt;TResult&gt;**](/uwp/api/windows.foundation.iasyncoperation_tresult_) и
- [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_).

Каждый из этих типов асинхронной операции проецируется в соответствующий тип в пространстве имен C++/WinRT **winrt::Windows::Foundation**. C++/WinRT также содержит внутреннюю структуру адаптера await. Она не используется напрямую, но благодаря этой структуре, можно написать `co_await` инструкцию, чтобы совместно ожидать результата любой функции, которая возвращает один из этих типов асинхронной операции. И вы можете создавать свои собственные сопрограммы, возвращающие эти типы.

Пример асинхронной функции Windows — [**SyndicationClient::RetrieveFeedAsync**](https://docs.microsoft.com/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync), который возвращает объект асинхронной операции типа [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_). Давайте рассмотрим некоторые способы&mdash;первый блокировка, а затем без-&mdash;использования C + +/ WinRT для вызова подобного API.

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
    SyndicationFeed syndicationFeed{ syndicationClient.RetrieveFeedAsync(rssFeedUri).get() };
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
> Функция **get** существует на C + +/ WinRT проекции введите **winrt::Windows::Foundation::IAsyncAction**, поэтому можно вызывать функцию в C + +/ WinRT проекта. Не позволит найти функцию указаны в является членом интерфейса [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction) , так как **получить** не является частью поверхности приложения двоичный интерфейс (ABI) фактического типа среды выполнения Windows **IAsyncAction**.

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

    auto processOp{ ProcessFeedAsync() };
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

    auto feedOp{ RetrieveBlogFeedAsync() };
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

Сопрограммы — это функция, как и любой другой, так как вызывающему объекту блокируется до момента функции выполнения к нему. И первой же возможности для сопрограммы для возврата — первый `co_await`, `co_return`, или `co_yield`.

Таким образом, перед передачей работы в сопрограмме, необходимо вернуть выполнение вызывающей стороне (другими словами, создать точки приостановки) таким образом, чтобы заблокировать вызывающую. Если вы еще не делаете этого `co-await`- применения другой операции, то можете `co-await` [**winrt::resume_background**](/uwp/cpp-ref-for-winrt/resume-background) функции. Это возвращает управление вызывающей стороне и затем сразу же возобновляет выполнение в потоке пула потоков.

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
IAsyncAction DoWorkAsync(TextBlock const& textblock)
{
    co_await winrt::resume_background();
    // Do compute-bound work here.

    textblock.Text(L"Done!"); // Error: TextBlock has thread affinity.
}
```

Приведенный выше код выдает исключение [**winrt::hresult_wrong_thread**](/uwp/cpp-ref-for-winrt/hresult-wrong-thread), поскольку **TextBlock** должен обновляться из создавшего его потока, а это поток пользовательского интерфейса. Одним из решений является захват контекста потока, в котором изначально была вызвана наша сопрограмма. Для этого создайте экземпляр объекта [**winrt::apartment_context**](/uwp/cpp-ref-for-winrt/apartment-context) , выполнения фоновой задачи, а затем `co_await` **apartment_context** переключение обратно на контекст вызова.

```cppwinrt
IAsyncAction DoWorkAsync(TextBlock const& textblock)
{
    winrt::apartment_context ui_thread; // Capture calling context.

    co_await winrt::resume_background();
    // Do compute-bound work here.

    co_await ui_thread; // Switch back to calling context.

    textblock.Text(L"Done!"); // Ok if we really were called from the UI thread.
}
```

Если приведенная выше сопрограмма вызывается из потока пользовательского интерфейса, создавшего **TextBlock**, этот метод работает. В вашем приложении будет множество ситуаций, где вы будете уверены в этом.

Для получения более универсального решения для обновления пользовательского интерфейса, в котором рассматриваются случаи, где у вас нет уверенности по поводу вызывающего потока, можно `co-await` функцию [**winrt::resume_foreground**](/uwp/cpp-ref-for-winrt/resume-foreground) переключиться на определенный поток переднего плана. В следующем примере кода мы указываем поток переднего плана, передавая объект-диспетчер, связанный с **TextBlock** (обращаясь к его свойству [**Dispatcher**](/uwp/api/windows.ui.xaml.dependencyobject.dispatcher#Windows_UI_Xaml_DependencyObject_Dispatcher)). Реализация **winrt::resume_foreground** вызывает [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync) на этом объекте-диспетчере для выполнения работы, поступающей далее в сопрограмме.

```cppwinrt
#include <winrt/Windows.UI.Core.h> // necessary in order to use winrt::resume_foreground.
IAsyncAction DoWorkAsync(TextBlock const& textblock)
{
    co_await winrt::resume_background();
    // Do compute-bound work here.

    co_await winrt::resume_foreground(textblock.Dispatcher()); // Switch to the foreground thread associated with textblock.

    textblock.Text(L"Done!"); // Guaranteed to work.
}
```

## <a name="execution-contexts-resuming-and-switching-in-a-coroutine"></a>Контексты выполнения, возобновление и переключение в сопрограмме

Грубо говоря, после приостановки точки в сопрограмме, исходный поток выполнения может перейти от пользователя и возобновление могут возникать в любом потоке (другими словами, любой поток может вызвать метод **Completed** для асинхронной операции).

Однако, если вы `co-await` какие-либо из четырех типов среды выполнения Windows асинхронной операции (**IAsyncXxx**), затем C + +/ WinRT перехватывает контекст вызова в точке вы `co-await`. И это гарантирует, что вы еще этого контекста при возобновлении задача-продолжение. C + +/ WinRT делает это, проверка, является ли вы уже на контекст вызова и, если это не так, переход к нему. Если вы решите потоке однопотоковом подразделении (STA), прежде чем `co-await`, а затем можно на том же впоследствии; Если вы решите потоке многопотоковом подразделении (MTA), прежде чем `co-await`, то можно на одном впоследствии.

```cppwinrt
IAsyncAction ProcessFeedAsync()
{
    Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
    SyndicationClient syndicationClient;

    // The thread context at this point is captured...
    SyndicationFeed syndicationFeed{ co_await syndicationClient.RetrieveFeedAsync(rssFeedUri) };
    // ...and is restored at this point.
}
```

Причина, по можно полагаться на это поведение заключается C + +/ WinRT предоставляет код для адаптации этих типов асинхронной операции среды выполнения Windows для поддержки языка сопрограммы C++ (эти фрагменты кода называются адаптеры ожидания). Оставшиеся ожидание типы в C + +/ WinRT — это просто программы-оболочки пула потоков и/или облегчающих; Поэтому он выполнить в пуле потоков.

```cppwinrt
using namespace std::chrono;
IAsyncOperation<int> return_123_after_5s()
{
    // No matter what the thread context is at this point...
    co_await 5s;
    // ...we're on the thread pool at this point.
    co_return 123;
}
```

Если вы `co_await` другой тип&mdash;даже в C + +/ WinRT сопрограммы реализации&mdash;другую библиотеку предоставляет адаптеров, и вам необходимо понять, выполните эти адаптеры с точки зрения возобновлением и контексты.

Чтобы сохранить переключения контекста до как минимум, можно использовать некоторых методов, которые уже были в этой статье. Рассмотрим некоторые иллюстрации результатам этого. В следующем примере псевдокода мы покажем структуры обработчик событий, который вызывает API среды выполнения Windows для загрузки изображения, опускается на фоновый поток для обработки этого изображения, а затем возвращает в поток пользовательского интерфейса, чтобы показать изображения в пользовательском Интерфейсе.

```cppwinrt
#include <winrt/Windows.UI.Core.h> // necessary in order to use winrt::resume_foreground.
IAsyncAction MainPage::ClickHandler(IInspectable const& /* sender */, RoutedEventArgs const& /* args */)
{
    // We begin in the UI context.

    // Call StorageFile::OpenAsync to load an image file.

    // The call to OpenAsync occurred on a background thread, but C++/WinRT has restored us to the UI thread by this point.

    co_await winrt::resume_background();

    // We're now on a background thread.

    // Process the image.

    co_await winrt::resume_foreground(this->Dispatcher());

    // We're back on MainPage's UI thread.

    // Display the image in the UI.
}
```

В этом сценарии существует навыков ineffiency вокруг вызова **StorageFile::OpenAsync**. Нет необходимости контекстное переключение на фоне потоков (так, что обработчик можно вернуть выполнение вызывающей стороне), на возобновлением после какие C + +/ WinRT восстанавливает контекст потока пользовательского интерфейса. Но в этом случае нет необходимости быть в потоке пользовательского интерфейса, пока мы все обновления пользовательского интерфейса. Дополнительные API среды выполнения Windows, мы вызываем метод *перед* наш вызов к **winrt::resume_background**более ненужные переключение вперед и назад контекста, которые мы взиматься. Решением является не до этого вызвать *любой* API среды выполнения Windows. Переместите их все после **winrt::resume_background**.

```cppwinrt
#include <winrt/Windows.UI.Core.h> // necessary in order to use winrt::resume_foreground.
IAsyncAction MainPage::ClickHandler(IInspectable const& /* sender */, RoutedEventArgs const& /* args */)
{
    // We begin in the UI context.

    co_await winrt::resume_background();

    // We're now on a background thread.

    // Call StorageFile::OpenAsync to load an image file.

    // Process the image.

    co_await winrt::resume_foreground(this->Dispatcher());

    // We're back on MainPage's UI thread.

    // Display the image in the UI.
}
```

Если вы хотите сделать что-то более сложными, то можно написать собственный await адаптеров. Например, если вы хотите, чтобы `co_await` для возобновления в том же потоке, который выполнения асинхронного действия на (таким образом, нет без переключения контекста), затем можно начать с написания await адаптеры, подобных тем ниже.

> [!NOTE]
> В примере кода ниже предоставляется только; для образовательных целей Это для начала Общие сведения о том, как await рабочих адаптеры. Если вы хотите использовать метод, описанный в собственных базу кода, рекомендуется разрабатывать и тестировать собственные await struct(s) адаптер. Например можно написать **complete_on_any**, **complete_on_current**и **complete_on(dispatcher)**. Также учитывать, что делает их шаблоны, которые принимают тип **IAsyncXxx** в качестве параметра шаблона.

```cppwinrt
struct no_switch
{
    no_switch(Windows::Foundation::IAsyncAction const& async) : m_async(async)
    {
    }

    bool await_ready() const
    {
        return m_async.Status() == Windows::Foundation::AsyncStatus::Completed;
    }

    void await_suspend(std::experimental::coroutine_handle<> handle) const
    {
        m_async.Completed([handle](Windows::Foundation::IAsyncAction const& /* asyncInfo */, Windows::Foundation::AsyncStatus const& /* asyncStatus */)
        {
            handle();
        });
    }

    auto await_resume() const
    {
        return m_async.GetResults();
    }

private:
    Windows::Foundation::IAsyncAction const& m_async;
};
```

Чтобы понять, как использовать **без_переключения** await адаптеры, сначала необходимо знать, что когда компилятор C++ обнаруживает `co_await` выражение, выполняется поиск функции под названием **await_ready**, **await_suspend**и **await_resume**. C + +/ WinRT библиотека предоставляет эти функции, чтобы получить рациональные поведение по умолчанию следующим образом.

```cppwinrt
IAsyncAction async{ ProcessFeedAsync() };
co_await async;
```

Использование **без_переключения** await адаптеры, просто измените тип, `co_await` выражение из **IAsyncXxx** **без_переключения**, следующим образом.

```cppwinrt
IAsyncAction async{ ProcessFeedAsync() };
co_await static_cast<no_switch>(async);
```

Затем вместо ищет три **await_xxx** функции, которые соответствуют **IAsyncXxx**, компилятор C++ выглядит для функций, которые соответствуют **без_переключения**.

## <a name="canceling-an-asychronous-operation-and-cancellation-callbacks"></a>Отмена асинхронной операции и обратные вызовы отмены

Компоненты среды выполнения Windows для асинхронного программирования дают возможность отменить переадресации асинхронные действия или операции. Начнем с простого примера.

```cppwinrt
// pch.h
#pragma once
#include <iostream>
#include <winrt/Windows.Foundation.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"
using namespace winrt;
using namespace Windows::Foundation;
using namespace std::chrono_literals;

IAsyncAction ImplicitCancellationAsync()
{
    while (true)
    {
        std::cout << "ImplicitCancellationAsync: do some work for 1 second" << std::endl;
        co_await 1s;
    }
}

IAsyncAction MainCoroutineAsync()
{
    auto implicit_cancellation{ ImplicitCancellationAsync() };
    co_await 3s;
    implicit_cancellation.Cancel();
}

int main()
{
    winrt::init_apartment();
    MainCoroutineAsync().get();
}
```

При запуске в примере выше, то увидите **ImplicitCancellationAsync** печати одно сообщение в секунду в течение трех секунд, после чего время автоматически завершает работу в результате отмены. Это работает, поскольку, на которых успешно выполняется действие `co_await` выражение, сопрограммы проверяет, является ли она была отменена. Если Да, затем он игнорирует и если нет, затем она приостанавливает в обычном режиме.

Отмена Конечно, возможно, во время приостановки сопрограммы. Только в том случае, когда возобновления сопрограммы или обращений к другой `co_await`, будет проверять наличие отмены. Проблемы — это один из потенциально слишком грубым точное задержка реагирования на отмену.

Таким образом другой вариант — явным образом запросить отмену из в пределах вашего сопрограммы. Указывайте приведенном выше примере кода в примере ниже. В этом примере новый **ExplicitCancellationAsync** получает объект, возвращенный функцией [**winrt::get_cancellation_token**](/uwp/cpp-ref-for-winrt/get-cancellation-token) и используется для периодической проверки ли сопрограммы была отменена. До тех пор, пока не отменена сопрограммы выполняется в бесконечном цикле; После отмены цикл и функции будут выхода обычным образом. Результат соответствует тому, как предыдущего примера, но здесь выхода из происходит явным образом и контроль.

```cppwinrt
...
IAsyncAction ExplicitCancellationAsync()
{
    auto cancellation_token{ co_await winrt::get_cancellation_token() };

    while (!cancellation_token())
    {
        std::cout << "ExplicitCancellationAsync: do some work for 1 second" << std::endl;
        co_await 1s;
    }
}

IAsyncAction MainCoroutineAsync()
{
    auto explicit_cancellation{ ExplicitCancellationAsync() };
    co_await 3s;
    explicit_cancellation.Cancel();
}
...
```

Ожидание **winrt::get_cancellation_token** извлекает токен отмены изучения **IAsyncAction** , допускаемых сопрограммы от вашего имени. Можно использовать оператор вызова функции на этот маркер запрашивать состояние отмены&mdash;по сути опрос для отмены. При выполнении определенных операций передачей или итерацию большой коллекции, то это разумный прием.

### <a name="register-a-cancellation-callback"></a>Зарегистрировать обратный вызов отмены

Отмена среды выполнения Windows не затрагивают автоматически другие асинхронные объекты. Но&mdash;в версии 10.0.17763.0 (Windows 10, версия 1809) пакета Windows SDK&mdash;вы можете зарегистрировать обратный вызов отмены. Это вытеснением заинтересовать клиента, на который может распространяться отмены и делает возможным для интеграции с существующей библиотеки параллелизма.

В следующем примере кода выполняющий **NestedCoroutineAsync** , но он не предусмотрено специальный отмены механизмов, в нем. **CancellationPropagatorAsync** является по сути оболочкой на вложенных сопрограммы; оболочка pre-emptively пересылает отмены.

```cppwinrt
// pch.h
#pragma once
#include <iostream>
#include <winrt/Windows.Foundation.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"
using namespace winrt;
using namespace Windows::Foundation;
using namespace std::chrono_literals;

IAsyncAction NestedCoroutineAsync()
{
    while (true)
    {
        std::cout << "NestedCoroutineAsync: do some work for 1 second" << std::endl;
        co_await 1s;
    }
}

IAsyncAction CancellationPropagatorAsync()
{
    auto cancellation_token{ co_await winrt::get_cancellation_token() };
    auto nested_coroutine{ NestedCoroutineAsync() };

    cancellation_token.callback([&]
    {
        nested_coroutine.Cancel();
    });

    co_await nested_coroutine;
}

IAsyncAction MainCoroutineAsync()
{
    auto cancellation_propagator{ CancellationPropagatorAsync() };
    co_await 3s;
    cancellation_propagator.Cancel();
}

int main()
{
    winrt::init_apartment();
    MainCoroutineAsync().get();
}
```

**CancellationPropagatorAsync** регистрирует лямбда-функция для обратного вызова собственный отмены, а затем он ждет (она приостанавливает) до завершения работы вложенной. Когда или **CancellationPropagatorAsync** отменяется, распространяет об отмене вложенных сопрограммы. Нет необходимости выполнять опрос для отмены; ни отмены блокируется неограниченное количество раз. Этот механизм отличается достаточной гибкостью, можно использовать для взаимодействия с библиотекой сопрограммы или параллелизма, ничего не знает о C + +/ WinRT.

## <a name="reporting-progress"></a>Отчеты о ходе выполнения

Если ваша сопрограмма возвращает [**IAsyncActionWithProgress**](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_)или [**IAsyncOperationWithProgress**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_), затем можно получить объект, возвращенный функцией [**winrt::get_progress_token**](/uwp/cpp-ref-for-winrt/get-progress-token) и использовать его для сообщения о ходе выполнения обратно на ход выполнения обработчик. Приведем пример кода.

```cppwinrt
// pch.h
#pragma once
#include <iostream>
#include <winrt/Windows.Foundation.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"
using namespace winrt;
using namespace Windows::Foundation;
using namespace std::chrono_literals;

IAsyncOperationWithProgress<double, double> CalcPiTo5DPs()
{
    auto progress{ co_await winrt::get_progress_token() };

    co_await 1s;
    double pi_so_far{ 3.1 };
    progress(0.2);

    co_await 1s;
    pi_so_far += 4.e-2;
    progress(0.4);

    co_await 1s;
    pi_so_far += 1.e-3;
    progress(0.6);

    co_await 1s;
    pi_so_far += 5.e-4;
    progress(0.8);

    co_await 1s;
    pi_so_far += 9.e-5;
    progress(1.0);
    co_return pi_so_far;
}

IAsyncAction DoMath()
{
    auto async_op_with_progress{ CalcPiTo5DPs() };
    async_op_with_progress.Progress([](auto const& /* sender */, double progress)
    {
        std::wcout << L"CalcPiTo5DPs() reports progress: " << progress << std::endl;
    });
    double pi{ co_await async_op_with_progress };
    std::wcout << L"CalcPiTo5DPs() is complete !" << std::endl;
    std::wcout << L"Pi is approx.: " << pi << std::endl;
}

int main()
{
    winrt::init_apartment();
    DoMath().get();
}
```

> [!NOTE]
> Не является правильным для реализации более одного *обработчик завершения* асинхронные действия или операции. У вас есть только один делегат для события завершения, или вы можете `co_await` его. Если у вас есть оба, второй завершится ошибкой. Либо один из следующих двух видов обработчики завершения подходит; нельзя отлаживать для одного объекта async.

```cppwinrt
auto async_op_with_progress{ CalcPiTo5DPs() };
async_op_with_progress.Completed([](auto const& sender, AsyncStatus /* status */)
{
    double pi{ sender.GetResults() };
});
```

```cppwinrt
auto async_op_with_progress{ CalcPiTo5DPs() };
double pi{ co_await async_op_with_progress };
```

Дополнительные сведения о обработчики завершения см. в разделе [типы делегатов для асинхронных действий и операций](handle-events.md#delegate-types-for-asynchronous-actions-and-operations).

## <a name="fire-and-forget"></a>Запущена и забыть

В некоторых случаях у вас есть задачи, которая может выполняться параллельно с другими рабочих, и вам не нужно ждать завершения этой задачи (никакой другой работы зависит от него), и вам требуется его, чтобы возвращать значение. В этом случае можно запускать задачу и его забудете. Что можно сделать путем написания сопрограммы, чей тип возвращаемого значения является [**winrt::fire_and_forget**](/uwp/cpp-ref-for-winrt/fire-and-forget) (вместо одного из типов асинхронной операции среды выполнения Windows, или **concurrency::task**).

```cppwinrt
// pch.h
#pragma once
#include <winrt/Windows.Foundation.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"
using namespace winrt;
using namespace std::chrono_literals;

winrt::fire_and_forget CompleteInFiveSeconds()
{
    co_await 5s;
}

int main()
{
    winrt::init_apartment();
    CompleteInFiveSeconds();
    // Do other work here.
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
* [WinRT::get_cancellation_token](/uwp/cpp-ref-for-winrt/get-cancellation-token)
* [WinRT::get_progress_token](/uwp/cpp-ref-for-winrt/get-progress-token)
* [WinRT::fire_and_forget](/uwp/cpp-ref-for-winrt/fire-and-forget)

## <a name="related-topics"></a>Смежные разделы
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)
* [Стандартные типы данных C++ и C++/WinRT](std-cpp-data-types.md)