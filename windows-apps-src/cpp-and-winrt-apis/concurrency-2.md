---
description: Более сложные сценарии с параллельной обработкой и асинхронностью в C++/WinRT.
title: Более сложные сценарии с параллельной обработкой и асинхронными операциями в C++/WinRT
ms.date: 07/23/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, concurrency, async, asynchronous, asynchrony
ms.localizationpriority: medium
ms.openlocfilehash: 4a671a319be49e07d3a8fcdacb569c4ae76e299b
ms.sourcegitcommit: 0426013dc04ada3894dd41ea51ed646f9bb17f6d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78853411"
---
# <a name="more-advanced-concurrency-and-asynchrony-with-cwinrt"></a>Более сложные сценарии с параллельной обработкой и асинхронными операциями в C++/WinRT

В этой статье описываются более сложные сценарии с параллельной обработкой и асинхронными операциями в C++/WinRT.

Дополнительные сведения см. в статье о [параллелизме и асинхронных операциях](concurrency.md).

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
using namespace std::chrono_literals;
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

## <a name="a-deeper-dive-into-winrtresume_foreground"></a>Подробные сведения о **winrt::resume_foreground**

В [C++/WinRT 2.0](/windows/uwp/cpp-and-winrt-apis/newsnews#news-and-changes-in-cwinrt-20) выполнение функции [**winrt::resume_foreground**](/uwp/cpp-ref-for-winrt/resume-foreground) приостанавливается, даже если она вызывается из потока диспетчера (в предыдущих версиях в некоторых сценариях могли возникать взаимоблокировки, так как выполнение приостанавливалось, только если функция не находилась в потоке диспетчера).

Текущее поведение означает, что вы можете использовать очистку стека и повторное помещение в очередь. Это важно для стабильной работы системы, особенно при использовании низкоуровневого системного кода. Последний фрагмент кода из раздела [Программирование с учетом сходства потоков](#programming-with-thread-affinity-in-mind) (см. выше) иллюстрирует выполнение некоторых сложных вычислений в фоновом потоке с последующим переключением на соответствующий поток пользовательского интерфейса для его обновления.

Вот как выглядит функция **winrt::resume_foreground**.

```cppwinrt
auto resume_foreground(...) noexcept
{
    struct awaitable
    {
        bool await_ready() const
        {
            return false; // Queue without waiting.
            // return m_dispatcher.HasThreadAccess(); // The C++/WinRT 1.0 implementation.
        }
        void await_resume() const {}
        void await_suspend(coroutine_handle<> handle) const { ... }
    };
    return awaitable{ ... };
};
```

Разница между предыдущим и текущим поведением аналогична разнице между [**PostMessage**](/windows/win32/api/winuser/nf-winuser-postmessagew) и [**SendMessage**](/windows/win32/api/winuser/nf-winuser-sendmessage) при разработке приложений Win32. **PostMessage** помещает задания в очередь, а затем очищает стек, не дожидаясь выполнения работы. Очистка стека очень важна.

Функция **winrt::resume_foreground** изначально также поддерживала только [**CoreDispatcher**](/uwp/api/windows.ui.core.coredispatcher) (в связке с [**CoreWindow**](/uwp/api/windows.ui.core.corewindow)). Это было реализовано в Windows до версии 10. Сейчас используется более гибкий и эффективный диспетчер — [**DispatcherQueue**](/uwp/api/windows.system.dispatcherqueue). Вы можете создать **DispatcherQueue** для реализации своих целей. Рассмотрим это простое консольное приложение.

```cppwinrt
using namespace Windows::System;

winrt::fire_and_forget RunAsync(DispatcherQueue queue);
 
int main()
{
    auto controller{ DispatcherQueueController::CreateOnDedicatedThread() };
    RunAsync(controller.DispatcherQueue());
    getchar();
}
```

В приведенном выше примере в закрытом потоке создается очередь (содержащаяся в контроллере), а затем контроллер передается сопрограмме. Сопрограмма может использовать очередь для ожидания (приостановки и возобновления) в закрытом потоке. Другим распространенным применением **DispatcherQueue** является создание очереди в текущем потоке пользовательского интерфейса для классического приложения или приложения Win32.

```cppwinrt
DispatcherQueueController CreateDispatcherQueueController()
{
    DispatcherQueueOptions options
    {
        sizeof(DispatcherQueueOptions),
        DQTYPE_THREAD_CURRENT,
        DQTAT_COM_STA
    };
 
    ABI::Windows::System::IDispatcherQueueController* ptr{};
    winrt::check_hresult(CreateDispatcherQueueController(options, &ptr));
    return { ptr, take_ownership_from_abi };
}
```

В этом примере показано, как можно вызывать и включать функции Win32 в свои проекты C++/WinRT, просто вызывая функцию [**CreateDispatcherQueueController**](/windows/win32/api/dispatcherqueue/nf-dispatcherqueue-createdispatcherqueuecontroller) (как для приложений Win32) для создания контроллера и последующей передачи полученного контроллера очередей вызывающему объекту в качестве объекта WinRT. Имено так можно эффективно работать с очередями в классическом приложении Win32 в стиле Ч. Петцольда.

```cppwinrt
winrt::fire_and_forget RunAsync(DispatcherQueue queue);
 
int main()
{
    Window window;
    auto controller{ CreateDispatcherQueueController() };
    RunAsync(controller.DispatcherQueue());
    MSG message;
 
    while (GetMessage(&message, nullptr, 0, 0))
    {
        DispatchMessage(&message);
    }
}
```

Выше выполнение простой функции **main** начинается с создания окна. Можно представить, что при этом регистрируется соответствующий класс окна и вызывается [**CreateWindow**](/windows/win32/api/winuser/nf-winuser-createwindoww) для создания окна классического приложения (высокоуровневый интерфейс). Затем вызывается функция **CreateDispatcherQueueController** для создания контроллера очереди перед вызовом определенной сопрограммы с использованием очереди диспетчера, связанной с этим контроллером. Когда в этом потоке выполнение сопрограммы возобновляется, реализуется стандартный конвейер сообщений. После этого вы можете вернуться к использованию сопрограмм для выполнения асинхронного рабочего процесса или рабочего процесса на основе сообщений в вашем приложении.

```cppwinrt
winrt::fire_and_forget RunAsync(DispatcherQueue queue)
{
    ... // Begin on the calling thread...
 
    co_await winrt::resume_foreground(queue);
 
    ... // ...resume on the dispatcher thread.
}
```

Вызов **winrt::resume_foreground** всегда предполагает использование *очереди* с последующей очисткой стека. При необходимости вы можете задать приоритет возобновления.

```cppwinrt
winrt::fire_and_forget RunAsync(DispatcherQueue queue)
{
    ...
 
    co_await winrt::resume_foreground(queue, DispatcherQueuePriority::High);
 
    ...
}
```

Либо вы можете использовать порядок помещения в очередь по умолчанию.

```cppwinrt
winrt::fire_and_forget RunAsync(DispatcherQueue queue)
{
    ...
 
    co_await queue;
 
    ...
}
```

Или, как в этом случае, обнаружение завершения очереди с последующей корректной обработкой.

```cppwinrt
winrt::fire_and_forget RunAsync(DispatcherQueue queue)
{
    ...
 
    if (co_await queue)
    {
        ... // Resume on dispatcher thread.
    }
    else
    {
        ... // Still on calling thread.
    }
}
```

Выражение `co_await` возвращает `true`, указывая на возобновление выполнения в потоке диспетчера. Иными словами, это указывает на успешное использование очереди. И наоборот, если получено `false`, это указывает на то, что выполнение и дальше происходит в вызывающем потоке, так как контроллер очереди завершает работу и больше не обслуживает запросы очереди.

Итак, сопрограммы, используемые в C++/WinRT, — это очень мощный и эффективный инструмент, особенно при разработке традиционных классических приложений в стиле Ч. Петцольда.

## <a name="canceling-an-asynchronous-operation-and-cancellation-callbacks"></a>Отмена асинхронной операции и обратные вызовы для отмены

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

Однако есть другой вариант — выполнить явный опрос на отмену в пределах соподпрограммы. Обновите приведенный выше пример указанным ниже кодом. В этом примере **ExplicitCancellationAsync** извлекает объект, возвращенный функцией [**winrt::get_cancellation_token**](/uwp/cpp-ref-for-winrt/get-cancellation-token), и использует его, чтобы периодически проверять, была ли отменена соподпрограмма. До тех пор, пока соподпрограмма не отменена, она выполняется в бесконечном цикле; после отмены, цикл и функция завершаются как обычно. Результат будет таким же, как и для предыдущего примера, но завершение работы осуществляется явным образом и контролируется.

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

Если соподпрограмма возвращает [**IAsyncActionWithProgress**](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_) или [**IAsyncOperationWithProgress**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_), можно извлечь объект, возвращенный функцией [**winrt::get_progress_token**](/uwp/cpp-ref-for-winrt/get-progress-token), и использовать его для отправки отчета о ходе выполнения в обработчик хода выполнения. Приведем пример кода.

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

В некоторых случаях у вас может быть задача, которую можно выполнить одновременно с другой работой. При этом не нужно ждать завершения этой задачи (от нее не зависит никакая другая работа) и не требуется возвращать значение. В этом случае для нее можно воспользоваться принципом "Выполнил и забыл". Это можно сделать, написав соподпрограмму с типом возвращаемого значения [**winrt :: fire_and_forget**](/uwp/cpp-ref-for-winrt/fire-and-forget) (вместо одного из типов асинхронных операций среды выполнения Windows или **concurrency :: task**).

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

**winrt::fire_and_forget** также полезно использовать в качестве возвращаемого типа обработчика событий, когда нужно выполнить асинхронную операцию. См. следующий пример, а также статью [Сильные и слабые ссылки в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/weak-references#safely-accessing-the-this-pointer-in-a-class-member-coroutine).

```cppwinrt
winrt::fire_and_forget MyClass::MyMediaBinder_OnBinding(MediaBinder const&, MediaBindingEventArgs args)
{
    auto lifetime{ get_strong() }; // Prevent *this* from prematurely being destructed.
    auto ensure_completion{ unique_deferral(args.GetDeferral()) }; // Take a deferral, and ensure that we complete it.

    auto file{ co_await StorageFile::GetFileFromApplicationUriAsync(Uri(L"ms-appx:///video_file.mp4")) };
    args.SetStorageFile(file);

    // The destructor of unique_deferral completes the deferral here.
}
```

Первому аргументу (*sender*) имя не задано, так как он не используется. Поэтому мы можем оставить его в качестве ссылки. Но обратите внимание, что *args* передается по значению. См. раздел [Передача параметров](concurrency.md#parameter-passing) выше.

## <a name="awaiting-a-kernel-handle"></a>Ожидание дескриптора ядра

C++/WinRT предоставляет класс **resume_on_signal**, который можно использовать для приостановки до тех пор, пока не будет получен сигнал, связанный с событием ядра. За то, чтобы этот дескриптор оставался действительным до получения `co_await resume_on_signal(h)`, отвечает пользователь. Класс **resume_on_signal** сам по себе не может сделать это, так как этот дескриптор может быть утерян даже до запуска **resume_on_signal**, как в первом примере.

```cppwinrt
IAsyncAction Async(HANDLE event)
{
    co_await DoWorkAsync();
    co_await resume_on_signal(event); // The incoming handle is not valid here.
}
```

Входящий дескриптор **HANDLE** действителен только до возвращения функции, которая является сопрограммой и которая возвращается в первую точку приостановки (в этом примере — первый экземпляр `co_await`). Когда ожидающий элемент управления **DoWorkAsync** возвращается вызывающему объекту, вызывающий фрейм становится недействительным, и вы не будете знать, будет ли этот дескриптор допустимым при возобновлении работы сопрограммы.

Технически наша сопрограмма получает свои параметры по значению (см. подробнее о [ передаче параметров](concurrency.md#parameter-passing) выше). Но в этом случае нам нужно перейти к следующему шагу, чтобы вы могли вникнуть в саму *суть* этого руководства, а не просто формально пройти его. Нам нужно передать строгую ссылку (иными словами, владение) вместе с дескриптором. Вот как это сделать.

```cppwinrt
IAsyncAction Async(winrt::handle event)
{
    co_await DoWorkAsync();
    co_await resume_on_signal(event); // The incoming handle *is* not valid here.
}
```

Передача [**winrt::handle**](/uwp/cpp-ref-for-winrt/handle) по значению обеспечивает семантику владения, гарантируя, что дескриптор ядра будет действительным в течение времени существования сопрограммы.

Вот так вы можете вызвать эту сопрограмму.

```cppwinrt
namespace
{
    winrt::handle duplicate(winrt::handle const& other, DWORD access)
    {
        winrt::handle result;
        if (other)
        {
            winrt::check_bool(::DuplicateHandle(::GetCurrentProcess(),
                other.get(), ::GetCurrentProcess(), result.put(), access, FALSE, 0));
        }
        return result;
    }

    winrt::handle make_manual_reset_event(bool initialState = false)
    {
        winrt::handle event{ ::CreateEvent(nullptr, true, initialState, nullptr) };
        winrt::check_bool(static_cast<bool>(event));
        return event;
    }
}

IAsyncAction SampleCaller()
{
    handle event{ make_manual_reset_event() };
    auto async{ Async(duplicate(event)) };

    ::SetEvent(event.get());
    event.close(); // Our handle is closed, but Async still has a valid handle.

    co_await async; // Will wake up when *event* is signaled.
}
```

## <a name="asynchronous-timeouts-made-easy"></a>Обработка ожиданий для асинхронных объектов

Сопрограммы С++ широко используются в C++/WinRT. Они трансформируют сам процесс написания параллельно выполняемого кода. В этом разделе обсуждаются случаи, когда вам нужно сфокусироваться не на деталях, а на результатах асинхронного выполнения. По этой причине реализация в C++/WinRT асинхронного интерфейса среды выполнения Windows [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction) включает функцию **get**, которая соответствует **std::function**.

```cppwinrt
using namespace winrt::Windows::Foundation;
int main()
{
    IAsyncAction async = ...
    async.get();
    puts("Done!");
}
```

Функция **get** выполняет блокировку на неограниченное время, — пока асинхронный объект не будет выполнен. Асинхронные объекты, как правило, имеют очень короткий срок существования, так что зачастую это все, что вам нужно.

Но бывают случаи, когда этого недостаточно, и вам нужно завершить ожидания через некоторое время. Хотя это всегда можно было сделать с помощью стандартных блоков, предоставляемых средой выполнения Windows, сейчас, благодаря функции **wait_for**, предоставляемой в C++/WinRT, задача значительно упрощается. Она также реализована в **IAsyncAction** и, снова-таки, соответствует **std::function**.

```cppwinrt
using namespace std::chrono_literals;
int main()
{
    IAsyncAction async = ...
 
    if (async.wait_for(5s) == AsyncStatus::Completed)
    {
        puts("done");
    }
}
```

> [!NOTE]
> Функция **wait_for** использует **std::chrono::duration** в интерфейсе, но она ограничена диапазоном, меньшим длительности **std::chrono::duration** (около 49,7 дней).

В этом примере функция **wait_for** ожидает примерно пять секунд, а затем проверяет состояние выполнения. Если состояние соответствует ожидаемому, вы можете быть уверены в том, что объект успешно выполнен. При этом, чтобы получить определенный результат, вы можете использовать функцию **get**.

```cppwinrt
int main()
{
    IAsyncOperation<int> async = ...
 
    if (async.wait_for(5s) == AsyncStatus::Completed)
    {
        printf("result %d\n", async.get());
    }
}
```

Так как выполнение асинхронного объекта к тому моменту завершится, функция **get** сразу же вернет результат. Как видите, функция **wait_for** возвращает состояние асинхронного объекта. Таким образом, вы можете использовать ее для более точного управления.

```cppwinrt
switch (async.wait_for(5s))
{
case AsyncStatus::Completed:
    printf("result %d\n", async.get());
    break;
case AsyncStatus::Canceled:
    puts("canceled");
    break;
case AsyncStatus::Error:
    puts("failed");
    break;
case AsyncStatus::Started:
    puts("still running");
    break;
}
```

- Помните, что **AsyncStatus::Completed** означает, что асинхронный объект выполнен успешно, и вы можете вызвать функцию **get**, чтобы получить нужный результат.
- **AsyncStatus::Canceled** означает, что асинхронный объект был отменен. Отмена обычно запрашивается вызывающим объектом, поэтому такое состояние обрабатывается редко. Как правило, отмененный асинхронный объект просто удаляется.
- **AsyncStatus::Error** означает, что выполнение асинхронного объекта завершилось сбоем. При необходимости с помощью функции **get** вы можете повторно вызвать исключение.
- **AsyncStatus::Started** означает, что асинхронный объект все еще выполняется. Асинхронный шаблон среды выполнения Windows не допускает ни множественных ожиданий, ни множественных ожидающих объектов. Это означает, что вы не можете вызывать функцию **wait_for** в цикле. Если время ожидания истекло, вы можете выбрать несколько вариантов. Можно отказаться от выполнения объекта или запросить его состояние до вызова **get** и получения результатов. Но лучше всего на этом этапе просто отменить его выполнение.

## <a name="returning-an-array-asynchronously"></a>Асинхронное возвращение массива

Ниже приведен пример [MIDL 3.0](/uwp/midl-3/), который порождает ошибку *error MIDL2025: [msg]syntax error [context]: expecting > or, near "["* (Ошибка MIDL2025: [сообщение]: ошибка синтаксиса; [контекст]: ожидается > or, near "[").

```idl
Windows.Foundation.IAsyncOperation<Int32[]> RetrieveArrayAsync();
```

Причина в том, что не допускается использование массива в качестве аргумента типа параметра для параметризованного интерфейса. Поэтому нужен менее очевидный способ достижения цели — асинхронной передачи массива обратно из метода класса среды выполнения. 

Вы можете вернуть массив, упакованный в объект [PropertyValue](/uwp/api/windows.foundation.propertyvalue). Затем вызывающий код его распакует. Ниже приведен пример кода, который можно попробовать применить, добавив класс среды выполнения **SampleComponent** в проект **компонента среды выполнения Windows (C++/WinRT)** , а затем использовав его (например) из проекта **приложения основных компонентов (C++/WinRT)** .

```cppwinrt
// SampleComponent.idl
namespace MyComponentProject
{
    runtimeclass SampleComponent
    {
        Windows.Foundation.IAsyncOperation<IInspectable> RetrieveCollectionAsync();
    };
}

// SampleComponent.h
...
struct SampleComponent : SampleComponentT<SampleComponent>
{
    ...
    Windows::Foundation::IAsyncOperation<Windows::Foundation::IInspectable> RetrieveCollectionAsync()
    {
        co_return Windows::Foundation::PropertyValue::CreateInt32Array({ 99, 101 }); // Box an array into a PropertyValue.
    }
}
...

// SampleCoreApp.cpp
...
MyComponentProject::SampleComponent m_sample_component;
...
auto boxed_array{ co_await m_sample_component.RetrieveCollectionAsync() };
auto property_value{ boxed_array.as<winrt::Windows::Foundation::IPropertyValue>() };
winrt::com_array<int32_t> my_array;
property_value.GetInt32Array(my_array); // Unbox back into an array.
...
```

## <a name="important-apis"></a>Важные API
* [Интерфейс IAsyncAction](/uwp/api/windows.foundation.iasyncaction)
* [Интерфейс IAsyncActionWithProgress&lt;TProgress&gt;](/uwp/api/windows.foundation.iasyncactionwithprogress_tprogress_)
* [Интерфейс IAsyncOperation&lt;TResult&gt;](/uwp/api/windows.foundation.iasyncoperation_tresult_)
* [Интерфейс IAsyncOperationWithProgress&lt;TResult, TProgress&gt;](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_)
* [Метод SyndicationClient::RetrieveFeedAsync](/uwp/api/windows.web.syndication.syndicationclient.retrievefeedasync)
* [winrt::fire_and_forget](/uwp/cpp-ref-for-winrt/fire-and-forget)
* [winrt::get_cancellation_token](/uwp/cpp-ref-for-winrt/get-cancellation-token)
* [winrt::get_progress_token](/uwp/cpp-ref-for-winrt/get-progress-token)
* [winrt::resume_foreground](/uwp/cpp-ref-for-winrt/resume-foreground)

## <a name="related-topics"></a>Связанные темы
* [Параллельная обработка и асинхронные операции](concurrency.md)
* [Обработка событий с помощью делегатов в C++/WinRT](handle-events.md)