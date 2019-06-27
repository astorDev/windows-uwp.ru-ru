---
description: В этом разделе показаны способы, которыми можно создавать и использовать асинхронные объекты среды выполнения Windows с помощью C++/WinRT.
title: Параллельные обработка и выполнение асинхронных операций с помощью C++/WinRT
ms.date: 04/24/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, concurrency, async, asynchronous, asynchrony
ms.localizationpriority: medium
ms.openlocfilehash: 910d7a7ca2aaebac6dd462d7104b26a989cf8814
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66721656"
---
# <a name="concurrency-and-asynchronous-operations-with-cwinrt"></a>Параллельные обработка и выполнение асинхронных операций с помощью C++/WinRT

В этом разделе показаны способы, которыми можно создавать и использовать асинхронные объекты среды выполнения Windows с помощью [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

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

    co_await DoOtherWorkAsync();

    // ...accessing value here carries no guarantees of safety.
}
```

В соподпрограмме выполнение происходит синхронно до первой точки приостановки, где управление возвращается вызывающему объекту. К моменту возобновления соподпрограммы с исходным значением, на которое ссылается отсылочный параметр, может случиться что угодно. С точки зрения соподпрограммы у отсылочного параметра неконтролируемый срок существования. Таким образом, в приведенном выше примере мы безопасно можем получить доступ к *value* до вызова `co_await`, но не после него. Мы также не можем безопасно передать *value* в **DoOtherWorkAsync**, если есть малейший риск того, что эта функция в свою очередь приостановится, а затем попытается использовать *value* после возобновления. Чтобы обеспечить безопасное использование параметров после приостановки и возобновления, ваши соподпрограммы должны по умолчанию использовать передачу по значению, чтобы они собирали данные только по значениям и избегали проблем со сроком существования. Ситуации, в которых можно не соблюдать эти указания в связи с уверенностью в безопасности такого подхода, встречаются редко.

```cppwinrt
// Coroutine
IASyncAction DoWorkAsync(Param value);
```

Кроме того, не рекомендуется использовать передачу по значению const (если вы не собираетесь переместить значение). Это никак не влияет на исходное значение, копию которого вы создаете, но явно сообщает намерение и помогает в случае неумышленного изменения копии.

```cppwinrt
// coroutine with strictly unnecessary const (but arguably good practice).
IASyncAction DoWorkAsync(Param const value);
```

С дополнительными сведениями можно также ознакомиться в разделе [Стандартные массивы и векторы](std-cpp-data-types.md#standard-arrays-and-vectors), в котором описываются методы передачи стандартного вектора в асинхронный вызываемый элемент.

## <a name="offloading-work-onto-the-windows-thread-pool"></a>Передача работы в пул потоков Windows

Соподпрограмма является функцией, поскольку, как и в любой другой, вызывающий объект блокируется, пока функция возвращает результат его выполнения. И первой возможностью для возврата соподпрограммой является первый экземпляр `co_await`, `co_return` или `co_yield`.

В этом случае перед передачей работы по вычислению в соподпрограмме необходимо вернуть выполнение вызывающему объекту (другими словами, создать риск приостановки), чтобы не заблокировать вызывающий объект. Если для этого еще не применяется `co_await` к какой-либо другой операции, можно применить `co_await` к функции [**winrt::resume_background**](/uwp/cpp-ref-for-winrt/resume-background). В результате управление вернется вызывающему объекту, а затем сразу же возобновится выполнение в потоке пула потоков.

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

Этот сценарий основан на предыдущем. Вы передаете работу в пул потоков, но затем необходимо отобразить ход выполнения в пользовательском интерфейсе (UI).

```cppwinrt
IAsyncAction DoWorkAsync(TextBlock textblock)
{
    co_await winrt::resume_background();
    // Do compute-bound work here.

    textblock.Text(L"Done!"); // Error: TextBlock has thread affinity.
}
```

Приведенный выше код выдает исключение [**winrt::hresult_wrong_thread**](/uwp/cpp-ref-for-winrt/error-handling/hresult-wrong-thread), поскольку **TextBlock** должен обновляться из создавшего его потока, а это поток пользовательского интерфейса. Одним из решений является захват контекста потока, в котором изначально была вызвана наша соподпрограмма. Чтобы сделать это, следует создать экземпляр объекта [**winrt::apartment_context**](/uwp/cpp-ref-for-winrt/apartment-context), выполнить операцию в фоновом режиме, а затем переключить `co_await`**apartment_context** обратно на вызывающий контекст.

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

Если приведенная выше соподпрограмма вызывается из потока пользовательского интерфейса, создавшего **TextBlock**, этот метод работает. В вашем приложении будет множество ситуаций, где вы сможете в этом убедиться.

Для более общего решения по обновлению пользовательского интерфейса, который охватывает случаи, когда вы не уверены в вызывающем потоке, вы можете применить `co_await` к функции [**winrt :: resume_foreground**](/uwp/cpp-ref-for-winrt/resume-foreground), чтобы переключиться на конкретный поток переднего плана. В следующем примере кода мы указываем поток переднего плана, передавая объект-диспетчер, связанный с **TextBlock** (обращаясь к его свойству [**Dispatcher**](/uwp/api/windows.ui.xaml.dependencyobject.dispatcher#Windows_UI_Xaml_DependencyObject_Dispatcher)). Реализация **winrt::resume_foreground** вызывает [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync) на этом объекте-диспетчере для выполнения работы, поступающей в дальнейшем в соподпрограмме.

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

В целом, после точки приостановки в соподпрограмме исходный поток выполнения может исчезнуть, а возобновление может произойти в любом другом потоке (иными словами, любой поток может вызвать метод **Completed** для асинхронной операции).

Но если вы используете `co_await` для любой из четырех типов асинхронных операций среды выполнения Windows (**IAsyncXxx**), C++/WinRT захватывает вызывающий контекст в заданной вами точке `co_await`. Это гарантирует пребывание в этом контексте при возобновлении задачи продолжения. C++/ WinRT достигает этого путем проверки пребывания в контексте вызова и переключения на него в случае, если это не так. Если вы были в потоке однопотокового подразделения (STA) до `co_await`, то после этого вы будете находится в нем же. Если вы были в потоке многопоточного подразделения (MTA) до `co_await`, то после этого вы будете находиться в одном из них.

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

Причина, по которой вы можете рассчитывать на такое поведение, заключается в том, что C ++/WinRT предоставляет код для адаптации этих типов асинхронных операций среды выполнения Windows к поддержке языка соподпрограммы C ++ (эти фрагменты кода называются адаптерами ожидания). Остальные доступные типы в C++/WinRT — это обычные оболочки пула потоков и (или) вспомогательные приложения. Поэтому они выполняются в пуле потоков.

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

Если вы применяете `co_await` к какому-либо другому типу &mdash; даже в реализации соподпрограммы C++/WinRT &mdash; адаптеры предоставляет другая библиотека, и необходимо понять, что эти адаптеры делают с точки зрения возобновления и контекстов.

Чтобы свести к минимуму переключение контекста, можно использовать методы, которые уже были представлены в этом разделе. Рассмотрим ряд иллюстрации их применения. В следующем примере с псевдокодом мы покажем структуру обработчика событий, который вызывает API среды выполнения Windows для загрузки изображения, помещает его в фоновый поток для обработки этого изображения, а затем возвращается в поток пользовательского интерфейса для его отображения в пользовательском интерфейсе.

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

Этот сценарий несколько неэффективен с точки зрения вызова **StorageFile::OpenAsync**. При этом необходимо выполнить переключение контекста на фоновый поток (чтобы обработчик мог вернуть выполнение вызывающей стороне). После возобновления C++/WinRT восстанавливает контекст потока пользовательского интерфейса. Однако в данном случае необязательно быть в потоке пользовательского интерфейса, пока нет необходимости обновлять пользовательский интерфейс. Чем больше дополнительных API среды выполнения Windows мы вызываем *перед* вызовом **winrt::resume_background**, тем больше мы применяем ненужных контекстных переключений вперед и назад. Решение заключается в том, чтобы не вызывать до этого *никакие* API среды выполнения Windows. Их все нужно переместить после **winrt::resume_background**.

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

Если вы хотите сделать что-то более сложное, можете записать свои собственные адаптеры ожидания. Например, если вы хотите применить `co_await` для возобновления исполнения на том же потоке, где выполняется асинхронное действие (то есть без переключения контекста), можете для начала написать адаптеры ожидания, подобные приведенным ниже.

> [!NOTE]
> Пример кода ниже предоставляется только в целях обучения. Он поможет вам понять, как работают адаптеры ожидания. Если вы хотите использовать этот прием в собственной базе кода, рекомендуем разработать и протестировать собственные структуры адаптера ожидания. Например, можно написать **complete_on_any**, **complete_on_current** и **complete_on(dispatcher)** . Также постарайтесь сделать их шаблонами, которые принимают тип **IAsyncXxx** в качестве параметра шаблона.

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

Чтобы понять, как использовать параметры ожидания **no_switch**, необходимо сначала понять, что если компилятор C++ обнаруживает выражение `co_await`, он выполняет поиск функций, именуемых **await_ready**, **await_suspend** и **await_resume**. Библиотека C++/WinRT предоставляет эти функции, так что у вас по умолчанию будет все необходимое, как в примере ниже.

```cppwinrt
IAsyncAction async{ ProcessFeedAsync() };
co_await async;
```

Чтобы использовать адаптеры ожидания **no_switch**, достаточно изменить для `co_await` тип выражения с **IAsyncXxx** на **no_switch** подобно тому, как показано ниже.

```cppwinrt
IAsyncAction async{ ProcessFeedAsync() };
co_await static_cast<no_switch>(async);
```

Затем, вместо поиска трех функций **await_xxx**, соответствующих **IAsyncXxx**, компилятор C++ выполнит поиск функций, которые соответствуют **no_switch**.

## <a name="canceling-an-asychronous-operation-and-cancellation-callbacks"></a>Отмена асинхронной операции и обратные вызовы для отмены

Компоненты среды выполнения Windows для асинхронного программирования позволяют отменять выполняемые асинхронные действия или операции. Ниже приведен пример, который вызывает [**StorageFolder::GetFilesAsync**](/uwp/api/windows.storage.storagefolder.getfilesasync), чтобы извлечь потенциально большую коллекцию файлов. При этом полученный объект асинхронной операции сохраняется в элементе данных. Пользователь может отменить операцию.

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

Начнем с простого примера, чтобы узнать, как реализовать отмену.

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

При выполнении приведенной выше команды вы увидите, что **ImplicitCancellationAsync** выводит одно сообщение в секунду в течение трех секунд, после чего автоматически завершается в результате отмены. Это работает, поскольку при обнаружении выражения `co_await` соподпрограмма проверяет, была ли она отменена. Если была отменена —выполняется по упрощенной схеме, а если нет — приостанавливается, как обычно.

Выполнение отмены, конечно же, происходит при приостановке соподпрограммы. Соподпрограмма будет проверять наличие отмены только при возобновлении работы или выполнении другой операции `co_await`. Проблема заключается в потенциально слишком большой задержке при отмене.

Однако есть другой вариант — выполнить явный опрос на отмену в пределах соподпрограммы. Обновите приведенный выше пример указанным ниже кодом. В этом примере **ExplicitCancellationAsync** извлекает объект, возвращенный функцией [ **winrt::get_cancellation_token** ](/uwp/cpp-ref-for-winrt/get-cancellation-token), и использует его, чтобы периодически проверять, была ли отменена соподпрограмма. До тех пор, пока соподпрограмма не отменена, она выполняется в бесконечном цикле; после отмены, цикл и функция завершаются как обычно. Результат будет таким же, как и для предыдущего примера, но завершение работы осуществляется явным образом и контролируется.

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

При ожидании **winrt :: get cancellationtoken** получает токен отмены с информацией о действии **IAsyncAction**, которое соподпрограмма создает от вашего имени. Для этого токена можно использовать оператор вызова функции, чтобы выполнить запрос на состояние отмены &mdash; по сути, опрос для отмены. Этот метод подходит для выполнения операции по вычислению или перебора больших коллекций.

### <a name="register-a-cancellation-callback"></a>Регистрация обратного вызова отмены

Отмена среды выполнения Windows не выполняется автоматически для других асинхронных объектов. Однако,&mdash;начиная с версии 10.0.17763.0 (Windows 10, версия 1809) пакета SDK Windows,&mdash;появилась возможность регистрировать обратный вызов отмены. Это приоритетный способ, с помощью которого можно распространять отмену, и который позволяет выполнять интеграцию с существующими библиотеками функций параллелизма.

В следующем примере кода **NestedCoroutineAsync** выполняет работу, но не содержит специальной логики отмены. **CancellationPropagatorAsync** по сути является оболочкой для вложенной соподпрограммы. Оболочка направляет отмену в приоритетном порядке.

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

**CancellationPropagatorAsync** регистрирует лямбда-функцию для собственного обратного вызова отмены, а затем переходит в состояние ожидания (приостанавливается) до завершения вложенной работы. При отмене **CancellationPropagatorAsync** отмена распространяется на вложенную соподпрограмму. При этом нет необходимости выполнять опрос для отмены, а отмена не блокируется на неопределенное время. Этот механизм является достаточно гибким, чтобы использовать его для взаимодействия с библиотекой соподпрограмм или функций параллелизма, которая не содержит сведений о работе с C++/WinRT.

## <a name="reporting-progress"></a>Отчеты о ходе выполнения

Если соподпрограмма возвращает [ **IAsyncActionWithProgress**](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_) или [**IAsyncOperationWithProgress**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_), можно извлечь объект, возвращенный функцией [ **winrt::get_progress_token** ](/uwp/cpp-ref-for-winrt/get-progress-token), и использовать его для отправки отчета о ходе выполнения в обработчик хода выполнения. Приведем пример кода.

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
> Для асинхронного действия или операции нельзя реализовывать несколько *обработчиков завершения*. Вы может использовать только один делегат для события завершения или применить для него `co_await`. Если их два, второй завершится ошибкой. Для одного и того же асинхронного объекта подходит только один из двух типов обработчиков завершения. Оба одновременно использовать нельзя.

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

Дополнительные сведения об обработчиках завершения см. в разделе [Типы делегатов для асинхронных действий и операций](handle-events.md#delegate-types-for-asynchronous-actions-and-operations).

## <a name="fire-and-forget"></a>Принцип "Выполнил и забыл"

В некоторых случаях у вас может быть задача, которую можно выполнить одновременно с другой работой. При этом не нужно ждать завершения этой задачи (от нее не зависит никакая другая работа) и не требуется возвращать значение. В этом случае для нее можно воспользоваться принципом "Выполнил и забыл". Это можно сделать, написав соподпрограмму с типом возвращаемого значения [ **winrt :: fire_and_forget** ](/uwp/cpp-ref-for-winrt/fire-and-forget) (вместо одного из типов асинхронных операций среды выполнения Windows или **concurrency :: task**).

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
* [Класс concurrency::task](/cpp/parallel/concrt/reference/task-class)
* [Интерфейс IAsyncAction](/uwp/api/windows.foundation.iasyncaction)
* [Интерфейс IAsyncActionWithProgress&lt;TProgress&gt;](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_)
* [Интерфейс IAsyncOperation&lt;TResult&gt;](/uwp/api/windows.foundation.iasyncoperation_tresult_)
* [Интерфейс IAsyncOperationWithProgress&lt;TResult, TProgress&gt;](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_)
* [Метод SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [Класс SyndicationFeed](/uwp/api/windows.web.syndication.syndicationfeed)
* [winrt::get_cancellation_token](/uwp/cpp-ref-for-winrt/get-cancellation-token)
* [winrt::get_progress_token](/uwp/cpp-ref-for-winrt/get-progress-token)
* [winrt::fire_and_forget](/uwp/cpp-ref-for-winrt/fire-and-forget)

## <a name="related-topics"></a>Статьи по теме
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)
* [Стандартные типы данных C++ и C++/WinRT](std-cpp-data-types.md)
