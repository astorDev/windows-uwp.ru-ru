---
author: TylerMSFT
ms.assetid: 1B077801-0A58-4A34-887C-F1E85E9A37B0
title: "Создание периодического рабочего элемента"
description: "Узнайте, как создать периодически повторяющийся рабочий элемент."
translationtype: Human Translation
ms.sourcegitcommit: 36bc5dcbefa6b288bf39aea3df42f1031f0b43df
ms.openlocfilehash: f5b16c62b591481c52cfa4bb7e1ddb162bf18f42

---
# Создание периодического рабочего элемента

\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

** Важные API **

-   [**CreatePeriodicTimer**](https://msdn.microsoft.com/library/windows/apps/Hh967915)
-   [**ThreadPoolTimer**](https://msdn.microsoft.com/library/windows/apps/BR230587)

Узнайте, как создать периодически повторяющийся рабочий элемент.

## Создание периодического рабочего элемента

Для создания периодического рабочего элемента используется метод [**CreatePeriodicTimer**](https://msdn.microsoft.com/library/windows/apps/Hh967915). Создайте лямбда-функцию, выполняющую работу, и используйте параметр *period* для указания интервала между отправками. Период указывается с помощью структуры [**TimeSpan**](https://msdn.microsoft.com/library/windows/apps/BR225996). Рабочий элемент будет отправляться каждый раз по истечении периода времени, поэтому убедитесь, что этот период является достаточным для завершения работы.

[
              **CreateTimer**
            ](https://msdn.microsoft.com/en-us/library/windows/apps/windows.system.threading.threadpooltimer.createtimer.aspx) возвращает объект [**ThreadPoolTimer**](https://msdn.microsoft.com/library/windows/apps/BR230587). Храните этот объект на случай, если таймер придется отменить.

> **Примечание**  Избегайте указания для интервала нулевого значения (или значения меньше одной миллисекунды). Это приведет к тому, что периодический таймер будет вести себя как одиночный.

> **Примечание**  Для доступа к пользовательскому интерфейсу и отображения хода выполнения рабочего элемента можно использовать [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/Hh750317).

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

## Обработка отмены периодического рабочего элемента (необязательно)

При необходимости можно обработать отмену периодического таймера с помощью [**TimerDestroyedHandler**](https://msdn.microsoft.com/library/windows/apps/Hh967926). Используйте перегрузку [**CreatePeriodicTimer**](https://msdn.microsoft.com/library/windows/apps/Hh967915) для создания дополнительной лямбда-функции, которая обрабатывает отмену периодического рабочего элемента.

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

## Отмена таймера

При необходимости вызовите метод [**Cancel**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.system.threading.threadpooltimer.cancel.aspx), чтобы отменить повторы периодического рабочего элемента. Если рабочий элемент запущен во время отмены периодического таймера, ему будет разрешено завершиться. Метод [**TimerDestroyedHandler**](https://msdn.microsoft.com/library/windows/apps/Hh967926) (если предоставлен) вызывается, когда все экземпляры периодического рабочего элемента завершили работу.

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> PeriodicTimer.Cancel();
> ```
> ``` cpp
> PeriodicTimer->Cancel();
> ```

## Примечания.

Подробнее об одиночных таймерах см. в разделе [Отправка рабочего элемента по таймеру](use-a-timer-to-submit-a-work-item.md).

## Ссылки по теме

* [Отправка рабочего элемента в пул потоков](submit-a-work-item-to-the-thread-pool.md)
* [Рекомендации по использованию пула потоков](best-practices-for-using-the-thread-pool.md)
* [Отправка рабочего элемента по таймеру](use-a-timer-to-submit-a-work-item.md)
 




<!--HONumber=Jun16_HO4-->


