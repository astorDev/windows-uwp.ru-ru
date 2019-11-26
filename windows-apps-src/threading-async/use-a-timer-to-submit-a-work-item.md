---
ms.assetid: AAE467F9-B3C7-4366-99A2-8A880E5692BE
title: Отправка рабочего элемента по таймеру
description: Узнайте, как создать рабочий элемент, который выполняется после истечения времени таймера.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, таймер, потоки
ms.localizationpriority: medium
ms.openlocfilehash: 7bd870858bbccffa07b082384ae6ddea987b67f2
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258929"
---
# <a name="use-a-timer-to-submit-a-work-item"></a>Отправка рабочего элемента по таймеру


<b>Важные API</b>

-   [**Пространство имен Windows. UI. Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Core)
-   [**Пространство имен Windows. System. Threading**](https://docs.microsoft.com/uwp/api/Windows.System.Threading)

Узнайте, как создать рабочий элемент, который выполняется после истечения времени таймера.

## <a name="create-a-single-shot-timer"></a>Создание однократного таймера

Чтобы создать таймер для рабочего элемента, используйте метод [**CreateTimer**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.createtimer). Определите лямбда-выражение, соответствующее заданию, и используйте параметр *delay*, чтобы указать, как долго пулу потоков нужно ждать, прежде чем назначить рабочий элемент доступному потоку. Задержка определена с использованием структуры [**TimeSpan**](https://docs.microsoft.com/uwp/api/Windows.Foundation.TimeSpan).

> **Обратите внимание** ,  можно использовать [**CoreDispatcher. RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync) для доступа к пользовательскому интерфейсу и отображения хода выполнения рабочего элемента.

В следующем примере создается рабочий элемент, который запускается через три минуты:

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> TimeSpan delay = TimeSpan.FromMinutes(3);
>             
> ThreadPoolTimer DelayTimer = ThreadPoolTimer.CreateTimer(
>     (source) =>
>     {
>         //
>         // TODO: Work
>         //
>         
>         //
>         // Update the UI thread by using the UI core dispatcher.
>         //
>         Dispatcher.RunAsync(
>             CoreDispatcherPriority.High,
>             () =>
>             {
>                 //
>                 // UI components can be accessed within this scope.
>                 //
>
>             });
>
>     }, delay);
> ```
> ``` cpp
> TimeSpan delay;
> delay.Duration = 3 * 60 * 10000000; // 10,000,000 ticks per second
>
> ThreadPoolTimer ^ DelayTimer = ThreadPoolTimer::CreateTimer(
>         ref new TimerElapsedHandler([this](ThreadPoolTimer^ source)
>         {
>             //
>             // TODO: Work
>             //
>             
>             //
>             // Update the UI thread by using the UI core dispatcher.
>             //
>             Dispatcher->RunAsync(CoreDispatcherPriority::High,
>                 ref new DispatchedHandler([this]()
>                 {
>                     //
>                     // UI components can be accessed within this scope.
>                     //
>
>                     ExampleUIUpdateMethod("Timer completed.");
>
>                 }));
>
>         }), delay);
> ```

## <a name="provide-a-completion-handler"></a>Предоставление обработчика завершения

Если требуется, обрабатывайте отмену и завершение рабочего элемента с помощью обработчика [**TimerDestroyedHandler**](https://docs.microsoft.com/uwp/api/windows.system.threading.timerdestroyedhandler). Чтобы указать дополнительное лямбда-выражение, используйте перегрузку [**CreateTimer**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.createtimer). Это выполняется, когда таймер отменяется или когда рабочий элемент завершается.

В следующем примере создается таймер, который отправляет рабочий элемент и вызывает метод, когда рабочий элемент завершается или происходит отмена таймера:

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> TimeSpan delay = TimeSpan.FromMinutes(3);
>             
> bool completed = false;
>
> ThreadPoolTimer DelayTimer = ThreadPoolTimer.CreateTimer(
>     (source) =>
>     {
>         //
>         // TODO: Work
>         //
>
>         //
>         // Update the UI thread by using the UI core dispatcher.
>         //
>         Dispatcher.RunAsync(
>                 CoreDispatcherPriority.High,
>                 () =>
>                 {
>                     //
>                     // UI components can be accessed within this scope.
>                     //
>
>                 });
>
>         completed = true;
>     },
>     delay,
>     (source) =>
>     {
>         //
>         // TODO: Handle work cancellation/completion.
>         //
>
>
>         //
>         // Update the UI thread by using the UI core dispatcher.
>         //
>         Dispatcher.RunAsync(
>             CoreDispatcherPriority.High,
>             () =>
>             {
>                 //
>                 // UI components can be accessed within this scope.
>                 //
>
>                 if (completed)
>                 {
>                     // Timer completed.
>                 }
>                 else
>                 {
>                     // Timer cancelled.
>                 }
>
>             });
>     });
> ```
> ``` cpp
> TimeSpan delay;
> delay.Duration = 3 * 60 * 10000000; // 10,000,000 ticks per second
>
> completed = false;
>
> ThreadPoolTimer ^ DelayTimer = ThreadPoolTimer::CreateTimer(
>         ref new TimerElapsedHandler([&](ThreadPoolTimer ^ source)
>         {
>             //
>             // TODO: Work
>             //
>
>             //
>             // Update the UI thread by using the UI core dispatcher.
>             //
>             Dispatcher->RunAsync(CoreDispatcherPriority::High,
>                 ref new DispatchedHandler([&]()
>                 {
>                     //
>                     // UI components can be accessed within this scope.
>                     //
>
>                 }));
>
>             completed = true;
>
>         }),
>         delay,
>         ref new TimerDestroyedHandler([&](ThreadPoolTimer ^ source)
>         {
>             //
>             // TODO: Handle work cancellation/completion.
>             //
>
>             Dispatcher->RunAsync(CoreDispatcherPriority::High,
>                 ref new DispatchedHandler([&]()
>                 {
>                     //
>                     // Update the UI thread by using the UI core dispatcher.
>                     //
>
>                     if (completed)
>                     {
>                         // Timer completed.
>                     }
>                     else
>                     {
>                         // Timer cancelled.
>                     }
>
>                 }));
>         }));
> ```

## <a name="cancel-the-timer"></a>Отмена таймера

Если таймер еще отсчитывает время, а рабочий элемент больше не нужен, вызовите [**Cancel**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.cancel). Таймер отменяется, и рабочий элемент не отправляется в пул потоков.

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> DelayTimer.Cancel();
> ```
> ``` cpp
> DelayTimer->Cancel();
> ```

## <a name="remarks"></a>Примечания

Приложения универсальной платформы Windows (UWP) не могут использовать **Thread.Sleep**, поскольку это может привести к блокированию потока пользовательского интерфейса. Вместо этого для создания рабочего элемента вы можете использовать метод [**ThreadPoolTimer**](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPoolTimer); в результате задача, выполняемая рабочим элементом, будет отложена без блокировки потока пользовательского интерфейса.

Полный образец кода, демонстрирующего рабочие элементы, рабочие элементы таймеров и периодические рабочие элементы, см. на странице [образца пула потоков](https://code.msdn.microsoft.com/windowsapps/Pool-Sample-5aa60454). Образец кода был первоначально написан для Windows 8.1 но код можно использовать повторно в Windows 10.

Дополнительные сведения о повторяющихся таймерах см. в разделе [Создание периодического рабочего элемента](create-a-periodic-work-item.md).

## <a name="related-topics"></a>См. также

* [Отправка рабочего элемента в пул потоков](submit-a-work-item-to-the-thread-pool.md)
* [Рекомендации по использованию пула потоков](best-practices-for-using-the-thread-pool.md)
* [Отправка рабочего элемента по таймеру](use-a-timer-to-submit-a-work-item.md)
 

 
