---
ms.assetid: 1B077801-0A58-4A34-887C-F1E85E9A37B0
title: Создание периодического рабочего элемента
description: Узнайте, как создать периодически повторяющийся рабочий элемент.
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, периодический рабочий элемент, потоки, таймеры
ms.localizationpriority: medium
ms.openlocfilehash: cf3a5817e459c7089eafb8f2c38d58b0e8eef03c
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371564"
---
# <a name="create-a-periodic-work-item"></a>Создание периодического рабочего элемента


<b>Важные API</b>

-   [**CreatePeriodicTimer**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.createperiodictimer)
-   [**ThreadPoolTimer**](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPoolTimer)

Узнайте, как создать периодически повторяющийся рабочий элемент.

## <a name="create-the-periodic-work-item"></a>Создание периодического рабочего элемента

Для создания периодического рабочего элемента используется метод [**CreatePeriodicTimer**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.createperiodictimer). Создайте лямбда-функцию, выполняющую работу, и используйте параметр *period* для указания интервала между отправками. Период указывается с помощью структуры [**TimeSpan**](https://docs.microsoft.com/uwp/api/Windows.Foundation.TimeSpan). Рабочий элемент будет отправляться каждый раз по истечении периода времени, поэтому убедитесь, что этот период является достаточным для завершения работы.

[**CreateTimer** ](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.createtimer) возвращает [ **ThreadPoolTimer** ](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPoolTimer) объекта. Храните этот объект на случай, если таймер придется отменить.

> **Примечание**  не рекомендуется указывать значение, равное нулю (или любое значение менее одной миллисекунды) для интервала. Это приведет к тому, что периодический таймер будет вести себя как одиночный.

> **Примечание**  можно использовать [ **CoreDispatcher.RunAsync** ](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.windows) доступ к пользовательскому Интерфейсу и отображает ход выполнения задания из рабочего элемента.

В следующем фрагменте кода создается рабочий элемент, который запускается каждые 60 секунд.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> TimeSpan period = TimeSpan.FromSeconds(60);
>
> ThreadPoolTimer PeriodicTimer = ThreadPoolTimer.CreatePeriodicTimer((source) =>
>     {
>         //
>         // TODO: Work
>         //
>         
>         //
>         // Update the UI thread by using the UI core dispatcher.
>         //
>         Dispatcher.RunAsync(CoreDispatcherPriority.High,
>             () =>
>             {
>                 //
>                 // UI components can be accessed within this scope.
>                 //
>
>             });
>
>     }, period);
> ```
> ``` cpp
> TimeSpan period;
> period.Duration = 60 * 10000000; // 10,000,000 ticks per second
>
> ThreadPoolTimer ^ PeriodicTimer = ThreadPoolTimer::CreatePeriodicTimer(
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
>                 }));
>
>         }), period);
> ```

## <a name="handle-cancellation-of-the-periodic-work-item-optional"></a>Обработка отмены периодического рабочего элемента (необязательно)

При необходимости можно обработать отмену периодического таймера с помощью [**TimerDestroyedHandler**](https://docs.microsoft.com/uwp/api/windows.system.threading.timerdestroyedhandler). Используйте перегрузку [**CreatePeriodicTimer**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.createperiodictimer) для создания дополнительной лямбда-функции, которая обрабатывает отмену периодического рабочего элемента.

В следующем фрагменте кода выполняется создание периодического рабочего элемента, который повторяется каждые 60 секунд, и предоставляет обработчик отмены.

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> using Windows.System.Threading;
>
>     TimeSpan period = TimeSpan.FromSeconds(60);
>
>     ThreadPoolTimer PeriodicTimer = ThreadPoolTimer.CreatePeriodicTimer((source) =>
>     {
>         //
>         // TODO: Work
>         //
>         
>         //
>         // Update the UI thread by using the UI core dispatcher.
>         //
>         Dispatcher.RunAsync(CoreDispatcherPriority.High,
>             () =>
>             {
>                 //
>                 // UI components can be accessed within this scope.
>                 //
>
>             });
>     },
>     period,
>     (source) =>
>     {
>         //
>         // TODO: Handle periodic timer cancellation.
>         //
>
>         //
>         // Update the UI thread by using the UI core dispatcher.
>         //
>         Dispatcher->RunAsync(CoreDispatcherPriority.High,
>             ()=>
>             {
>                 //
>                 // UI components can be accessed within this scope.
>                 //                 
>
>                 // Periodic timer cancelled.
>
>             }));
>     });
> ```
> ``` cpp
> using namespace Windows::System::Threading;
> using namespace Windows::UI::Core;
>
> TimeSpan period;
> period.Duration = 60 * 10000000; // 10,000,000 ticks per second
>
> ThreadPoolTimer ^ PeriodicTimer = ThreadPoolTimer::CreatePeriodicTimer(
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
>                 }));
>
>         }),
>         period,
>         ref new TimerDestroyedHandler([&](ThreadPoolTimer ^ source)
>         {
>             //
>             // TODO: Handle periodic timer cancellation.
>             //
>
>             Dispatcher->RunAsync(CoreDispatcherPriority::High,
>                 ref new DispatchedHandler([&]()
>                 {
>                     //
>                     // UI components can be accessed within this scope.
>                     //
>
>                     // Periodic timer cancelled.
>
>                 }));
>         }));
> ```

## <a name="cancel-the-timer"></a>Отмена таймера

При необходимости вызовите метод [**Cancel**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.cancel), чтобы отменить повторы периодического рабочего элемента. Если рабочий элемент запущен во время отмены периодического таймера, ему будет разрешено завершиться. Метод [**TimerDestroyedHandler**](https://docs.microsoft.com/uwp/api/windows.system.threading.timerdestroyedhandler) (если предоставлен) вызывается, когда все экземпляры периодического рабочего элемента завершили работу.

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> PeriodicTimer.Cancel();
> ```
> ``` cpp
> PeriodicTimer->Cancel();
> ```

## <a name="remarks"></a>Примечания

Подробнее об одиночных таймерах см. в разделе [Отправка рабочего элемента по таймеру](use-a-timer-to-submit-a-work-item.md).

## <a name="related-topics"></a>См. также

* [Отправка рабочего элемента в пул потоков](submit-a-work-item-to-the-thread-pool.md)
* [Рекомендации по использованию пула потоков](best-practices-for-using-the-thread-pool.md)
* [Отправка рабочего элемента по таймеру](use-a-timer-to-submit-a-work-item.md)
 
