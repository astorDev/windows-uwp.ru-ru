---
description: В этом разделе показаны способы, которыми можно создавать и использовать асинхронные объекты среды выполнения Windows с помощью C++/WinRT.
title: Параллельная обработка и асинхронные операции с помощью C++/WinRT
ms.date: 04/24/2019
ms.topic: article
keywords: Windows 10, uwp, стандартная, c ++, cpp, winrt, проекция, параллелизм, async, асинхронный, асинхронность
ms.localizationpriority: medium
ms.openlocfilehash: 910d7a7ca2aaebac6dd462d7104b26a989cf8814
ms.sourcegitcommit: 1f39b67f2711b96c6b4e7ed7107a9a47127d4e8f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66721656"
---
# <a name="concurrency-and-asynchronous-operations-with-cwinrt"></a>Параллельная обработка и асинхронные операции с помощью C++/WinRT

В этом разделе показано, способов, в которых оба вы можете создавать и использовать асинхронные объекты среды выполнения Windows с [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

## <a name="asynchronous-operations-and-windows-runtime-async-functions"></a>Асинхронные операции и функции "Async" среды выполнения Windows

Любой API-интерфейс среды выполнения Windows, завершение которого может занять более 50 миллисекунд, реализуется как асинхронная функция (с именем, оканчивающимся на "Async"). Реализация асинхронной функции инициирует работу в другом потоке и немедленно возвращает объект, представляющий асинхронную операцию. После завершения асинхронной операции возвращаемый объект, содержит любое значение, которое является результатом работы. Пространство имен среды выполнения Windows **Windows::Foundation** содержит четыре типа объектов асинхронной операции.

- [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction),
- [**IAsyncActionWithProgress&lt;TProgress&gt;** ](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_),
- [**IAsyncOperation&lt;TResult&gt;** ](/uwp/api/windows.foundation.iasyncoperation_tresult_), и
- [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;** ](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_).

Каждый из этих типов асинхронной операции проецируется в соответствующий тип в пространстве имен C++/WinRT **winrt::Windows::Foundation**. C++/WinRT также содержит внутреннюю структуру адаптера await. Вы не используете напрямую, но, благодаря этой структуре, можно написать `co_await` инструкцию, чтобы совместно await результат любая функция, которая возвращает одно из этих типов асинхронная операция. И вы можете создавать свои собственные сопрограммы, возвращающие эти типы.

Пример асинхронной функции Windows — [**SyndicationClient::RetrieveFeedAsync**](https://docs.microsoft.com/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync), который возвращает объект асинхронной операции типа [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;** ](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_). Давайте взглянем на некоторые варианты&mdash;первая блокировка, а затем без блокировки&mdash;использования C++/WinRT для вызова API, например.

## <a name="block-the-calling-thread"></a>Блокировка вызывающего потока

Приведенный ниже пример кода получает объект асинхронной операцию от **RetrieveFeedAsync** и вызывает **get** для этого объекта, чтобы заблокировать вызывающий поток до тех пор, пока не будут доступны результаты выполнения асинхронной операции.

Если вы хотите скопировать и вставить в этом примере непосредственно в главный файл исходного кода из **консольное приложение Windows (C++/WinRT)** проекта, а затем первый набор **не использовать предкомпилированные заголовки** в проекте свойства.

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

Вызов **get** обеспечивает удобство написания кода, и он идеально подходит для консольных приложений и фоновых потоков, в которых по какой-либо причине вам не хочется использовать сопрограмму. Однако он не является ни одновременным, ни асинхронным, поэтому не подходит для потока пользовательского интерфейса (и в неоптимизированных сборках при попытке его использования будет появляться уведомление об этом). Чтобы потоки операционной системы могли выполнять другую полезную работу, требуется иной способ.

## <a name="write-a-coroutine"></a>Написание сопрограммы

C++/WinRT интегрирует сопрограммы C++ в модель программирования для обеспечения естественного способа совместно ожидать результата. Вы можете создать свою собственную асинхронную операцию среды выполнения Windows путем написания сопрограммы. В следующем примере кода **ProcessFeedAsync** является сопрограммой.

> [!NOTE]
> **Получить** функция существует на C++тип проекции /WinRT **winrt::Windows::Foundation::IAsyncAction**, чтобы можно было вызвать функцию в любой C++/WinRT проекта. Вы не найдете функции, перечисленные как член [ **IAsyncAction** ](/uwp/api/windows.foundation.iasyncaction) интерфейс, так как **получить** не является частью области приложения двоичного интерфейса (ABI) Фактический тип среды выполнения Windows **IAsyncAction**.

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

Сопрограмма — это функция с возможностью приостановки и возобновления. В сопрограмме **ProcessFeedAsync**, представленной выше, при достижении оператора `co_await` сопрограмма асинхронно инициирует вызов **RetrieveFeedAsync**, а затем немедленно приостанавливает себя и возвращает управление вызывающему объекту (которым в примере выше является **main**). Затем **main** может продолжить работать, пока происходит извлечение и печать веб-канала. Когда это действие будет выполнено (после завершения вызова **RetrieveFeedAsync**), сопрограмма **ProcessFeedAsync** возобновит работу на следующем операторе.

Сопрограммы можно объединять в другие сопрограммы. Или можно вызвать **get** для блокировки и дождаться завершения (и получить результат, если таковой имеется). Кроме того, можно передать сопрограмму в другой язык программирования, который поддерживает среду выполнения Windows.

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

В приведенном выше примере **RetrieveBlogFeedAsync** возвращает **IAsyncOperationWithProgress**, который имеет ход выполнения и возвращаемое значение. Мы можем выполнять другую работу в то время как **RetrieveBlogFeedAsync** выполняет свою и получает канал. После этого мы вызываем **get** в этом объекте асинхронной операции для блокировки, дожидаемся его завершения, а затем получаем результаты операции.

Если вы асинхронно возвращаете тип среды выполнения Windows, то вам необходимо возвращать [**IAsyncOperation&lt;TResult&gt;** ](/uwp/api/windows.foundation.iasyncoperation_tresult_) или [**IAsyncOperationWithProgress&lt;TResult, TProgress&gt;** ](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_). Для этого подходит любой класс среды выполнения собственной или сторонней разработки, а также любой тип, который может передаваться в функцию среды выполнения Windows или из нее (например, `int` или **winrt::hstring**). Компилятор поможет вам, отобразив ошибку "*must be WinRT type*" ("требуется тип WinRT"), если вы попытаетесь использовать один из этих типов асинхронных операций с типом, который не является типом среды выполнения Windows.

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

Соподпрограмма — это функция, как и любое другое, в том, что вызывающий объект будет заблокирована, пока функция возвращает результат выполнения его. И доступны для соподпрограммой для возврата является первым `co_await`, `co_return`, или `co_yield`.

Таким образом, перед выполнением работы ограниченных по скорости вычислений в соподпрограмме, необходимо возвращать вызывающей стороне выполнения (другими словами, вводят точки приостановки), чтобы вызывающий объект не будет блокироваться. Если вы еще не делаем, `co_await`- ing некоторые другие операции, то вы можете `co_await` [ **winrt::resume_background** ](/uwp/cpp-ref-for-winrt/resume-background) функции. Это возвращает управление вызывающей стороне и затем сразу же возобновляет выполнение в потоке пула потоков.

Пул потоков, используемый в реализации, является низкоуровневым [пулом потоков Windows](https://docs.microsoft.com/windows/desktop/ProcThread/thread-pool-api), поэтому он наиболее эффективен.

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

Приведенный выше код выдает исключение [**winrt::hresult_wrong_thread**](/uwp/cpp-ref-for-winrt/error-handling/hresult-wrong-thread), поскольку **TextBlock** должен обновляться из создавшего его потока, а это поток пользовательского интерфейса. Одним из решений является захват контекста потока, в котором изначально была вызвана наша сопрограмма. Чтобы сделать это, следует создать [ **winrt::apartment_context** ](/uwp/cpp-ref-for-winrt/apartment-context) объекта, фоновая работа, а затем `co_await` **apartment_context** переключиться обратно на вызывающий контекст.

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

Для более общие решения для обновления пользовательского интерфейса, которая содержит информацию о случаях, где вы будете уверены в том, вызывающий поток, вы можете `co_await` [ **winrt::resume_foreground** ](/uwp/cpp-ref-for-winrt/resume-foreground) функции, чтобы перейти к определенной основной поток. В следующем примере кода мы указываем поток переднего плана, передавая объект-диспетчер, связанный с **TextBlock** (обращаясь к его свойству [**Dispatcher**](/uwp/api/windows.ui.xaml.dependencyobject.dispatcher#Windows_UI_Xaml_DependencyObject_Dispatcher)). Реализация **winrt::resume_foreground** вызывает [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync) на этом объекте-диспетчере для выполнения работы, поступающей далее в сопрограмме.

```cppwinrt
IAsyncAction DoWorkAsync(TextBlock textblock)
{
    co_await winrt::resume_background();
    // Do compute-bound work here.

    co_await winrt::resume_foreground(textblock.Dispatcher()); // Switch to the foreground thread associated with textblock.

    textblock.Text(L"Done!"); // Guaranteed to work.
}
```

## <a name="execution-contexts-resuming-and-switching-in-a-coroutine"></a>Контексты выполнения, возобновление и переключение в соподпрограмме

Вообще говоря, после точки приостановки в соподпрограмме исходный поток выполнения может быть удален и возобновления могут создаваться в любом потоке (другими словами, любой поток может вызвать **завершено** метод для асинхронной операции).

Но если вы `co_await` любой из четырех типов среды выполнения Windows асинхронные операции (**IAsyncXxx**), затем C++/WinRT захватывает контекст вызова в точке вы `co_await`. И гарантирует, что вы еще этого контекста при возобновлении продолжение. C++/ WinRT это достигается путем проверки не только в контексте вызова и, если нет, переключении на него. Если бы в потоке однопотоковое подразделение (STA), прежде чем `co_await`, вы будете на тот же позже; если бы в потоке многопотоковое подразделение (MTA) перед `co_await`, а затем вы сможете на одном, впоследствии.

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

Можно положиться на это поведение, так как C++/WinRT предоставляет код для этих типов асинхронной операции среды выполнения Windows для адаптации к C++ поддержка языков соподпрограмме (эти элементы кода, называются адаптеров ожидания). Остальные типы awaitable в C++/WinRT — это обычные оболочки пула потоков и/или вспомогательные функции; поэтому их завершения в пуле потоков.

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

Если вы `co_await` какое-либо иное&mdash;даже в C++реализации соподпрограмме /WinRT&mdash;другой библиотека предоставляет адаптеров, и вам потребуется понять, что делают эти адаптеры с точки зрения возобновления и контекстов.

Чтобы сохранить переключений контекста вниз, чтобы как минимум, можно использовать некоторые методы, которые мы уже видели, в этом разделе. Давайте посмотрим, некоторые иллюстрации выполнения. В следующем примере приведен код мы покажем структуры обработчика событий, который вызывает API среды выполнения Windows для загрузки изображения, отпусканием фоновый поток для обработки этого образа и возвращается в поток пользовательского интерфейса для отображения изображения в пользовательском Интерфейсе.

```cppwinrt
IAsyncAction MainPage::ClickHandler(IInspectable /* sender */, RoutedEventArgs /* args */)
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

В этом сценарии есть небольшой фрагмент ineffiency вокруг вызова **StorageFile::OpenAsync**. Отсутствует переключение контекста, необходимые для фонового потока (так, что обработчик может возвращать вызывающей стороне выполнения), на возобновление, после чего C++/WinRT восстанавливает контекст потока пользовательского интерфейса. Но в этом случае необязательно быть в потоке пользовательского интерфейса, пока мы собираемся обновить пользовательский Интерфейс. Дополнительные API среды выполнения Windows, мы вызываем *перед* наш вызов к **winrt::resume_background**, более ненужных туда и обратно контекстных переключений, мы применяем. Решением является не вызвать *любой* API среды выполнения Windows, до этого. Переместить их все в конце **winrt::resume_background**.

```cppwinrt
IAsyncAction MainPage::ClickHandler(IInspectable /* sender */, RoutedEventArgs /* args */)
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

Если вы хотите сделать нечто более сложными, а затем можно написать собственные await адаптеров. Например, если вы хотите `co_await` возобновить исполнение на том же потоке, выполнения асинхронного действия на (то есть без переключения контекста), то может начать с написания await адаптеров аналогично в соответствии с приведенными ниже.

> [!NOTE]
> В следующем примере предоставляется для целей обучения. Чтобы приступить к работе, это основные сведения о том, как await адаптеров рабочий. Если вы хотите использовать этот прием в собственной базе кода, то рекомендуется разработки и тестирования собственных await struct(s) адаптера. Например, можно написать **complete_on_any**, **complete_on_current**, и **complete_on(dispatcher)** . Также попробуйте сделать их шаблоны, которые принимают **IAsyncXxx** тип в качестве параметра шаблона.

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

Чтобы понять, как использовать **без_переключения** await адаптеров, сначала необходимо знать, что при C++ компилятор обнаруживает `co_await` выражение выполняет поиск функций, вызываемых **await_ready**, **await_suspend**, и **await_resume**. C++/Библиотеки WinRT предоставляет эти функции, чтобы получить разумное поведение по умолчанию следующим образом.

```cppwinrt
IAsyncAction async{ ProcessFeedAsync() };
co_await async;
```

Для использования **без_переключения** await адаптеров, достаточно изменить тип, `co_await` выражение из **IAsyncXxx** для **без_переключения**, следующим образом.

```cppwinrt
IAsyncAction async{ ProcessFeedAsync() };
co_await static_cast<no_switch>(async);
```

Затем, вместо поиска для трех **await_xxx** функции, соответствующие **IAsyncXxx**, C++ компилятор выполняет поиск функций, которые соответствуют **без_переключения**.

## <a name="canceling-an-asychronous-operation-and-cancellation-callbacks"></a>Отмена асинхронной операции и обратных вызовов для отмены

Компоненты среды выполнения Windows для асинхронного программирования позволяют отменить активных асинхронное действие или операцию. Ниже приведен пример, который вызывает [ **StorageFolder::GetFilesAsync** ](/uwp/api/windows.storage.storagefolder.getfilesasync) извлекаемого потенциально большое количество файлов и она сохраняет полученный объект асинхронной операции в элементе данных. Пользователь имеет возможность отменить операцию.

```cppwinrt
// MainPage.xaml
...
<Button x:Name="workButton" Click="OnWork">Work</Button>
<Button x:Name="cancelButton" Click="OnCancel">Cancel</Button>
...

// MainPage.h
...
#include <winrt/Windows.Foundation.Collections.h>
#include <winrt/Windows.Storage.Search.h>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Foundation::Collections;
using namespace Windows::Storage;
using namespace Windows::Storage::Search;
using namespace Windows::UI::Xaml;
...
struct MainPage : MainPageT<MainPage>
{
    MainPage()
    {
        InitializeComponent();
    }

    IAsyncAction OnWork(IInspectable /* sender */, RoutedEventArgs /* args */)
    {
        workButton().Content(winrt::box_value(L"Working..."));

        // Enable the Pictures Library capability in the app manifest file.
        StorageFolder picturesLibrary{ KnownFolders::PicturesLibrary() };

        m_async = picturesLibrary.GetFilesAsync(CommonFileQuery::OrderByDate, 0, 1000);

        IVectorView<StorageFile> filesInFolder{ co_await m_async };

        workButton().Content(box_value(L"Done!"));

        // Process the files in some way.
    }

    void OnCancel(IInspectable const& /* sender */, RoutedEventArgs const& /* args */)
    {
        if (m_async.Status() != AsyncStatus::Completed)
        {
            m_async.Cancel();
            workButton().Content(winrt::box_value(L"Canceled"));
        }
    }

private:
    IAsyncOperation<::IVectorView<StorageFile>> m_async;
};
...
```

Для реализации части отмены начнем с простого примера.

```cppwinrt
// main.cpp
#include <iostream>
#include <winrt/Windows.Foundation.h>

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

Если выполнить приведенную выше команду, то вы увидите **ImplicitCancellationAsync** печати одно сообщение в секунду в течение трех секунд, после чего автоматически завершает времени в результате не отменена. Это работает, поскольку в обнаружении `co_await` выражение, соподпрограмме проверяет ли она была отменена. Если Да, то он игнорирует и если не указан, он приостанавливается, в обычном режиме.

Отмены само собой, возможно, когда сопрограммы приостановлен. Только в том случае, когда сопрограммы возобновляет работу, или другой `co_await`, будет проверять наличие отмены. Проблема заключается в одной из потенциально слишком крупных фрагментов детального задержки при реагировании на отмену.

Таким образом другим вариантом является явным образом опроса для отмены из в пределах вашей сопрограммы. Добавьте код в списке ниже в примере выше. В этом примере **ExplicitCancellationAsync** извлекает объект, возвращенный [ **winrt::get_cancellation_token** ](/uwp/cpp-ref-for-winrt/get-cancellation-token) функции и использует его для периодически Проверьте, была ли отменена сопрограммы. До тех пор, пока не отменяется, сопрограммы выполняется в бесконечном цикле; После отмены цикла и функции будут выхода обычным образом. Результат будет одинаковым как предыдущего примера, но здесь выход происходит явным образом и в системе управления.

```cppwinrt
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

Ожидание **winrt::get_cancellation_token** получает маркер отмены, с опытом работы с **IAsyncAction** , производящего сопрограммы от вашего имени. Можно использовать оператор вызова функции на этот токен для запросов состояния отмены&mdash;по сути опроса для отмены. Если для выполнения некоторой операции ограниченных по скорости вычислений или перебора больших коллекций, то это разумный метод.

### <a name="register-a-cancellation-callback"></a>Зарегистрировать обратный вызов отмены

Отмена среды выполнения Windows не проходит автоматически для других асинхронных объектов. Но&mdash;появившихся в версии 10.0.17763.0 (Windows 10, версия 1809) пакета SDK Windows&mdash;можно зарегистрировать обратный вызов отмены. Это упреждающее ловушка, по которому отмены могут копироваться и дает возможность интегрировать существующие библиотеки параллелизма.

В следующем примере кода **NestedCoroutineAsync** выполняет работу, но он не имеет специальных отмены логики в нем. **CancellationPropagatorAsync** является по сути является оболочкой для вложенных сопрограммы; оболочки перенаправляет отмены заблаговременно.

```cppwinrt
// main.cpp
#include <iostream>
#include <winrt/Windows.Foundation.h>

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

    cancellation_token.callback([=]
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

**CancellationPropagatorAsync** регистры, лямбда-функцию для собственный обратный вызов отмены, а затем ожидает (он приостанавливает) до завершения вложенных работы. Если или **CancellationPropagatorAsync** является отменено, оно передает отмены для вложенных сопрограммы. Нет необходимости, чтобы выполнить опрос для отмены; и не является отмены блокировки. Этот механизм является достаточно гибким, вы сможете использовать его для взаимодействия с библиотекой сопрограммы или параллелизма, ничего не знает о C++/WinRT.

## <a name="reporting-progress"></a>Отчеты о ходе выполнения

Если в соподпрограмме возвращает либо [ **IAsyncActionWithProgress**](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_), или [ **IAsyncOperationWithProgress**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_), можно извлечь Объект, возвращаемый [ **winrt::get_progress_token** ](/uwp/cpp-ref-for-winrt/get-progress-token) работать и использовать его для отчета о ходе выполнения обратно в обработчик хода выполнения. Приведем пример кода.

```cppwinrt
// main.cpp
#include <iostream>
#include <winrt/Windows.Foundation.h>

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
> Это не так реализовать более одного *обработчик завершения* асинхронное действие или операцию. Может иметь только один делегат для события завершения, или вы можете `co_await` его. Если у вас есть оба, то второй завершится ошибкой. Либо один из следующих двух типов обработчиков завершения подходит; не одновременно для одного объекта async.

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

Дополнительные сведения об обработчиках завершения см. в разделе [делегировать типы для асинхронных действий и операций](handle-events.md#delegate-types-for-asynchronous-actions-and-operations).

## <a name="fire-and-forget"></a>Отправить и забыть

В некоторых случаях у вас есть задача, которая может быть выполнено параллельно с другой работы, и не нужно ожидать завершения выполнения этой задачи (никакие другие работы зависит от его), а также его, чтобы возвращать значение. В этом случае можно запускать задачу и его забудете. Это можно сделать, написав соподпрограмме типом возвращаемого значения является [ **winrt::fire_and_forget** ](/uwp/cpp-ref-for-winrt/fire-and-forget) (вместо одного из типов асинхронной операции среды выполнения Windows, или **concurrency::task**).

```cppwinrt
// main.cpp
#include <winrt/Windows.Foundation.h>

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
* [IAsyncAction интерфейс](/uwp/api/windows.foundation.iasyncaction)
* [IAsyncActionWithProgress&lt;TProgress&gt; интерфейса](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_)
* [IAsyncOperation&lt;TResult&gt; интерфейса](/uwp/api/windows.foundation.iasyncoperation_tresult_)
* [IAsyncOperationWithProgress&lt;TResult, TProgress&gt; интерфейса](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_)
* [Метод SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [Класс SyndicationFeed](/uwp/api/windows.web.syndication.syndicationfeed)
* [winrt::get_cancellation_token](/uwp/cpp-ref-for-winrt/get-cancellation-token)
* [winrt::get_progress_token](/uwp/cpp-ref-for-winrt/get-progress-token)
* [winrt::fire_and_forget](/uwp/cpp-ref-for-winrt/fire-and-forget)

## <a name="related-topics"></a>См. также
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)
* [Стандартные типы данных C++ и C++/WinRT](std-cpp-data-types.md)
