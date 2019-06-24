---
ms.assetid: E2A1200C-9583-40FA-AE4D-C9E6F6C32BCF
title: Отправка рабочего элемента в пул потоков
description: Узнайте, как выполнить работу в отдельном потоке, отправив рабочий элемент в пул потоков.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, потоки, пул потоков
ms.localizationpriority: medium
ms.openlocfilehash: 0ff0eca18eeab72dbf0a2f9a539e452a5923392d
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67322020"
---
# <a name="submit-a-work-item-to-the-thread-pool"></a>Отправка рабочего элемента в пул потоков

\[ Обновления для приложений универсальной платформы Windows в Windows 10. Статьи по Windows 8.x см. в разделе [архива](https://go.microsoft.com/fwlink/p/?linkid=619132) \]

<b>Важные API</b>

-   [**RunAsync**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpool.runasync)
-   [**IAsyncAction**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncAction)

Узнайте, как выполнить работу в отдельном потоке, отправив рабочий элемент в пул потоков. Используйте этот способ для поддержки реагирующих элементов пользовательского интерфейса при выполнении работы, требующей заметного количества времени, и параллельного выполнения нескольких задач.

## <a name="create-and-submit-the-work-item"></a>Создание и отправка рабочего элемента

Создайте рабочий элемент, вызвав [**RunAsync**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpool.runasync). Предоставьте делегат, выполняющий работу (можно использовать лямбда-функцию или функцию-делегат). Обратите внимание, что функция **RunAsync** возвращает объект [**IAsyncAction**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncAction). Сохраните его для использования на следующем шаге.

Доступны три версии функции [**RunAsync**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpool.runasync), поэтому вы можете при необходимости задать приоритет рабочего элемента и контролировать, выполняется ли он параллельно с другими рабочими элементами.

>[!NOTE]
>Используйте [ **CoreDispatcher.RunAsync** ](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync) для доступа в потоке пользовательского интерфейса и отображает ход выполнения задания из рабочего элемента.

В примере ниже создается рабочий элемент и предоставляется лямбда-функция для выполнения работы.

```csharp
// The nth prime number to find.
const uint n = 9999;

// A shared pointer to the result.
// We use a shared pointer to keep the result alive until the
// thread is done.
ulong nthPrime = 0;

// Simulates work by searching for the nth prime number. Uses a
// naive algorithm and counts 2 as the first prime number.
IAsyncAction asyncAction = Windows.System.Threading.ThreadPool.RunAsync(
    (workItem) =>
{
    uint  progress = 0; // For progress reporting.
    uint  primes = 0;   // Number of primes found so far.
    ulong i = 2;        // Number iterator.

    if ((n >= 0) && (n <= 2))
    {
        nthPrime = n;
        return;
    }

    while (primes < (n - 1))
    {
        if (workItem.Status == AsyncStatus.Canceled)
        {
            break;
        }

        // Go to the next number.
        i++;

        // Check for prime.
        bool prime = true;
        for (uint j = 2; j < i; ++j)
        {
            if ((i % j) == 0)
            {
                prime = false;
                break;
            }
        };

        if (prime)
        {
            // Found another prime number.
            primes++;

            // Report progress at every 10 percent.
            uint temp = progress;
            progress = (uint)(10.0*primes/n);

            if (progress != temp)
            {
                String updateString;
                updateString = "Progress to " + n + "th prime: "
                    + (10 * progress) + "%\n";

                // Update the UI thread with the CoreDispatcher.
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                    CoreDispatcherPriority.High,
                    new DispatchedHandler(() =>
                {
                    UpdateUI(updateString);
                }));
            }
        }
    }

    // Return the nth prime number.
    nthPrime = i;
});

// A reference to the work item is cached so that we can trigger a
// cancellation when the user presses the Cancel button.
m_workItem = asyncAction;
```

```cppwinrt
// The nth prime number to find.
const unsigned int n{ 9999 };

unsigned long nthPrime{ 0 };

// Simulates work by searching for the nth prime number. Uses a
// naive algorithm and counts 2 as the first prime number.

// A reference to the work item is cached so that we can trigger a
// cancellation when the user presses the Cancel button.
m_workItem = Windows::System::Threading::ThreadPool::RunAsync([&](Windows::Foundation::IAsyncAction const& workItem)
{
    unsigned int progress = 0; // For progress reporting.
    unsigned int primes = 0;   // Number of primes found so far.
    unsigned long int i = 2;   // Number iterator.

    if ((n >= 0) && (n <= 2))
    {
        nthPrime = n;
        return;
    }

    while (primes < (n - 1))
    {
        if (workItem.Status() == Windows::Foundation::AsyncStatus::Canceled)
        {
            break;
        }

        // Go to the next number.
        i++;

        // Check for prime.
        bool prime = true;
        for (unsigned int j = 2; j < i; ++j)
        {
            if ((i % j) == 0)
            {
                prime = false;
                break;
            }
        };

        if (prime)
        {
            // Found another prime number.
            primes++;

            // Report progress at every 10 percent.
            unsigned int temp = progress;
            progress = static_cast<unsigned int>(10.f*primes / n);

            if (progress != temp)
            {
                std::wstringstream updateString;
                updateString << L"Progress to " << n << L"th prime: " << (10 * progress) << std::endl;

                // Update the UI thread with the CoreDispatcher.
                Windows::ApplicationModel::Core::CoreApplication::MainView().CoreWindow().Dispatcher().RunAsync(
                    Windows::UI::Core::CoreDispatcherPriority::High,
                    Windows::UI::Core::DispatchedHandler([&]()
                {
                    UpdateUI(updateString.str());
                }));
            }
        }
    }
    // Return the nth prime number.
    nthPrime = i;
});
```

```cpp
// The nth prime number to find.
const unsigned int n = 9999;

// A shared pointer to the result.
// We use a shared pointer to keep the result alive until the
// thread is done.
std::shared_ptr<unsigned long> nthPrime = std::make_shared<unsigned long int>(0);

// Simulates work by searching for the nth prime number. Uses a
// naive algorithm and counts 2 as the first prime number.
auto workItem = ref new Windows::System::Threading::WorkItemHandler(
    \[this, n, nthPrime](IAsyncAction^ workItem)
{
    unsigned int progress = 0; // For progress reporting.
    unsigned int primes = 0;   // Number of primes found so far.
    unsigned long int i = 2;   // Number iterator.

    if ((n >= 0) && (n <= 2))
    {
        *nthPrime = n;
        return;
    }

    while (primes < (n - 1))
    {
        if (workItem->Status == AsyncStatus::Canceled)
       {
           break;
       }

       // Go to the next number.
       i++;

       // Check for prime.
       bool prime = true;
       for (unsigned int j = 2; j < i; ++j)
       {
           if ((i % j) == 0)
           {
               prime = false;
               break;
           }
       };

       if (prime)
       {
           // Found another prime number.
           primes++;

           // Report progress at every 10 percent.
           unsigned int temp = progress;
           progress = static_cast<unsigned int>(10.f*primes / n);

           if (progress != temp)
           {
               String^ updateString;
               updateString = "Progress to " + n + "th prime: "
                   + (10 * progress).ToString() + "%\n";

               // Update the UI thread with the CoreDispatcher.
               CoreApplication::MainView->CoreWindow->Dispatcher->RunAsync(
                   CoreDispatcherPriority::High,
                   ref new DispatchedHandler([this, updateString]()
               {
                   UpdateUI(updateString);
               }));
           }
       }
   }
   // Return the nth prime number.
   *nthPrime = i;
});

auto asyncAction = ThreadPool::RunAsync(workItem);

// A reference to the work item is cached so that we can trigger a
// cancellation when the user presses the Cancel button.
m_workItem = asyncAction;
```

После вызова [**RunAsync**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpool.runasync) рабочий элемент помещается в очередь пулом потоков и выполняется, когда становится доступным поток. Рабочие элементы в пуле потоков выполняются асинхронно и в любом порядке, поэтому обеспечьте независимое функционирование своих рабочих элементов.

Обратите внимание, что рабочий элемент проверяет значение свойства [**IAsyncInfo.Status**](https://docs.microsoft.com/uwp/api/windows.foundation.iasyncinfo.status) и завершает работу при отмене рабочего элемента.

## <a name="handle-work-item-completion"></a>Обработка завершения рабочего элемента

Предоставьте обработчик завершения, задав свойство [**IAsyncAction.Completed**](https://docs.microsoft.com/uwp/api/windows.foundation.iasyncaction.completed) рабочего элемента. Предоставьте делегат (можно использовать лямбда-функцию или функцию-делегат) для обработки завершения рабочего элемента. Например, используйте [**CoreDispatcher.RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync) для доступа к потоку пользовательского интерфейса и отображения результатов.

В следующем примере пользовательский интерфейс обновляется на основе результатов выполнения рабочего элемента, отправленного на шаге 1:

```cpp
asyncAction->Completed = ref new AsyncActionCompletedHandler(
    \[this, n, nthPrime](IAsyncAction^ asyncInfo, AsyncStatus asyncStatus)
{
    if (asyncStatus == AsyncStatus::Canceled)
    {
        return;
    }

    String^ updateString;
    updateString = "\n" + "The " + n + "th prime number is "
        + (*nthPrime).ToString() + ".\n";

    // Update the UI thread with the CoreDispatcher.
    CoreApplication::MainView->CoreWindow->Dispatcher->RunAsync(
        CoreDispatcherPriority::High,
        ref new DispatchedHandler([this, updateString]()
    {
        UpdateUI(updateString);
    }));
});
```

```cppwinrt
m_workItem.Completed([&](Windows::Foundation::IAsyncAction const& asyncInfo, Windows::Foundation::AsyncStatus const& asyncStatus)
{
    if (asyncStatus == Windows::Foundation::AsyncStatus::Canceled)
    {
        return;
    }

    std::wstringstream updateString;
    updateString << std::endl << L"The " << n << L"th prime number is " << nthPrime << std::endl;

    // Update the UI thread with the CoreDispatcher.
    Windows::ApplicationModel::Core::CoreApplication::MainView().CoreWindow().Dispatcher().RunAsync(
        Windows::UI::Core::CoreDispatcherPriority::High,
        Windows::UI::Core::DispatchedHandler([&]()
    {
        UpdateUI(updateString.str());
    }));
});
```

```c#
asyncAction.Completed = new AsyncActionCompletedHandler(
    (IAsyncAction asyncInfo, AsyncStatus asyncStatus) =>
{
    if (asyncStatus == AsyncStatus.Canceled)
    {
        return;
    }

    String updateString;
    updateString = "\n" + "The " + n + "th prime number is "
        + nthPrime + ".\n";

    // Update the UI thread with the CoreDispatcher.
    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
        CoreDispatcherPriority.High,
        new DispatchedHandler(()=>
    {
        UpdateUI(updateString);
    }));
});
```

Обратите внимание, что обработчик завершения проверяет, был ли отменен рабочий элемент, перед тем как выполнять обновление пользовательского интерфейса.

## <a name="summary-and-next-steps"></a>Краткая сводка и дальнейшие действия

Дополнительные сведения, загрузив код из этого краткого руководства в [Создание ThreadPool рабочий элемент образца](https://go.microsoft.com/fwlink/p/?LinkID=328569) для Windows 8.1, а также повторно использовать исходный код в win\_приложения unap Windows 10.

## <a name="related-topics"></a>См. также

* [Отправка рабочего элемента в пул потоков](submit-a-work-item-to-the-thread-pool.md)
* [Рекомендации по использованию пула потоков](best-practices-for-using-the-thread-pool.md)
* [Отправка рабочего элемента по таймеру](use-a-timer-to-submit-a-work-item.md)
 
