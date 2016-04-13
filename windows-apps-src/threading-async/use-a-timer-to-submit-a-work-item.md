---
ms.assetid: AAE467F9-B3C7-4366-99A2-8A880E5692BE
title: Отправка рабочего элемента по таймеру
description: Узнайте, как создать рабочий элемент, который выполняется после истечения времени таймера.
---
# Отправка рабочего элемента по таймеру

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

** Важные API **

-   [**Пространство имен Windows.UI.Core**](https://msdn.microsoft.com/library/windows/apps/BR208383)
-   [**Пространство имен Windows.System.Threading**](https://msdn.microsoft.com/library/windows/apps/BR229642)

Узнайте, как создать рабочий элемент, который выполняется после истечения времени таймера.

## Создание однократного таймера

Чтобы создать таймер для рабочего элемента, используйте метод [**CreateTimer**](https://msdn.microsoft.com/library/windows/apps/Hh967921). Определите лямбда-выражение, соответствующее заданию, и используйте параметр *delay*, чтобы указать, как долго пулу потоков нужно ждать, прежде чем назначить рабочий элемент доступному потоку. Задержка определена с использованием структуры [**TimeSpan**](https://msdn.microsoft.com/library/windows/apps/BR225996).

> **Примечание.** Для доступа к пользовательскому интерфейсу и отображения хода выполнения рабочего элемента можно использовать [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/Hh750317).

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
>                     ExampleUIUpdateMethod(&quot;Timer completed.&quot;);
> 
>                 }));
> 
>         }), delay);
> ```

## Предоставление обработчика завершения

Если требуется, обрабатывайте отмену и завершение рабочего элемента с помощью обработчика [**TimerDestroyedHandler**](https://msdn.microsoft.com/library/windows/apps/Hh967926). Чтобы указать дополнительное лямбда-выражение, используйте перегрузку [**CreateTimer**](https://msdn.microsoft.com/library/windows/apps/Hh967921). Это выполняется, когда таймер отменяется или когда рабочий элемент завершается.

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
>         ref new TimerElapsedHandler([&amp;](ThreadPoolTimer ^ source)
>         {
>             // 
>             // TODO: Work
>             // 
> 
>             // 
>             // Update the UI thread by using the UI core dispatcher.
>             // 
>             Dispatcher->RunAsync(CoreDispatcherPriority::High,
>                 ref new DispatchedHandler([&amp;]()
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
>         ref new TimerDestroyedHandler([&amp;](ThreadPoolTimer ^ source)
>         {
>             // 
>             // TODO: Handle work cancellation/completion.
>             // 
> 
>             Dispatcher->RunAsync(CoreDispatcherPriority::High,
>                 ref new DispatchedHandler([&amp;]()
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

## Отмена таймера

Если таймер еще отсчитывает время, а рабочий элемент больше не нужен, вызовите [**Cancel**](https://msdn.microsoft.com/library/windows/apps/BR230588). Таймер отменяется, и рабочий элемент не отправляется в пул потоков.

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> DelayTimer.Cancel();
> ```
> ``` cpp
> DelayTimer->Cancel();
> ```

## Комментарии

Приложения универсальной платформы Windows (UWP) не могут использовать **Thread.Sleep**, поскольку это может привести к блокированию потока пользовательского интерфейса. Вместо этого для создания рабочего элемента вы можете использовать метод [**ThreadPoolTimer**](https://msdn.microsoft.com/library/windows/apps/BR230587); в результате задача, выполняемая рабочим элементом, будет отложена без блокировки потока пользовательского интерфейса.

Полный образец кода, демонстрирующего рабочие элементы, рабочие элементы таймеров и периодические рабочие элементы, см. на странице [образца пула потоков](http://go.microsoft.com/fwlink/p/?linkid=255387). Пример кода изначально написан для Windows 8.1, но код можно использовать и в Windows 10.

Дополнительные сведения о повторяющихся таймерах см. в разделе [Создание периодического рабочего элемента](create-a-periodic-work-item.md).

## Связанные разделы

* [Отправка рабочего элемента в пул потоков](submit-a-work-item-to-the-thread-pool.md)
* [Рекомендации по использованию пула потоков](best-practices-for-using-the-thread-pool.md)
* [Отправка рабочего элемента по таймеру](use-a-timer-to-submit-a-work-item.md)
 

 



<!--HONumber=Mar16_HO1-->


