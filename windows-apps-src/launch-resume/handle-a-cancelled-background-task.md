---
author: TylerMSFT
title: "Обработка отмененной фоновой задачи"
description: "Узнайте, как создать фоновую задачу, которая распознает запросы на отмену, прекращает работу и сообщает приложению об отмене, используя постоянное хранилище."
ms.assetid: B7E23072-F7B0-4567-985B-737DD2A8728E
ms.sourcegitcommit: 39a012976ee877d8834b63def04e39d847036132
ms.openlocfilehash: ab575415e5e6a091fb45dab49af21d0552834406

---

# Обработка отмененной фоновой задачи

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**BackgroundTaskCanceledEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224775)
-   [**IBackgroundTaskInstance**](https://msdn.microsoft.com/library/windows/apps/br224797)
-   [**ApplicationData.Current**](https://msdn.microsoft.com/library/windows/apps/br241619)

Узнайте, как создать фоновую задачу, которая распознает запросы на отмену, прекращает работу и сообщает приложению об отмене, используя постоянное хранилище.

> **Примечание.** Если устройству не хватает памяти, фоновые задачи могут быть завершены для всех семейств устройств (за исключением настольных компьютеров). Если исключение "Недостаточно памяти" не отображается или приложение не обрабатывает его, то фоновая задача будет завершена без предупреждения и без вызова события OnCanceled. Это позволяет не прерывать работу пользователя с активным приложением. Ваша фоновая задача должна поддерживать этот сценарий.

В этом разделе предполагается, что класс фоновой задачи уже создан, включая метод Run, который используется в качестве точки входа фоновой задачи. См. раздел [Создание и регистрация фоновой задачи](create-and-register-a-background-task.md), чтобы научиться быстро создавать фоновые задачи. Более углубленно с условиями и триггерами можно ознакомиться в разделе [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

## Использование метода OnCanceled для распознавания запросов на отмену

Напишите метод, который будет обрабатывать событие отмены.

Создайте метод с именем OnCanceled, использующий следующий объем памяти. Этот метод — это точка входа, вызываемая средой выполнения Windows при каждом создании запроса на отмену для вашей фоновой задачи.

Метод OnCanceled должен использовать следующий объем памяти:

> [!div class="tabbedCodeSnippets"]
> ```cs
>    private void OnCanceled(IBackgroundTaskInstance sender, BackgroundTaskCancellationReason reason)
>    {
>        // TODO: Add code to notify the background task that it is cancelled.
>    }
> ```
> ```cpp
>    void ExampleBackgroundTask::OnCanceled(IBackgroundTaskInstance^ taskInstance, BackgroundTaskCancellationReason reason)
>    {
>        // TODO: Add code to notify the background task that it is cancelled.
>    }
> ```

[!div class="tabbedCodeSnippets"] Добавьте флаговую переменную **\_CancelRequested** в класс фоновой задачи.

> [!div class="tabbedCodeSnippets"]
> ```cs
>   volatile bool _CancelRequested = false;
> ```
> ```cpp
>   private:
>     volatile bool CancelRequested;
> ```

Она будет использоваться для обозначения того, что был сделан запрос на отмену.

[!div class="tabbedCodeSnippets"]

> [!div class="tabbedCodeSnippets"]
> ```cs
>     private void OnCanceled(IBackgroundTaskInstance sender, BackgroundTaskCancellationReason reason)
>     {
>         //
>         // Indicate that the background task is canceled.
>         //
>
>         _cancelRequested = true;
>
>         Debug.WriteLine("Background " + sender.Task.Name + " Cancel Requested...");
>     }
> ```
> ```cpp
>     void SampleBackgroundTask::OnCanceled(IBackgroundTaskInstance^ taskInstance, BackgroundTaskCancellationReason reason)
>     {
>         //
>         // Indicate that the background task is canceled.
>         //
>
>         CancelRequested = true;
>     }
> ```

В методе OnCanceled, созданном на этапе 1, установите для переменной флага **\_CancelRequested** значение **true**. Весь метод OnCanceled [примера фоновой задачи]( http://go.microsoft.com/fwlink/p/?linkid=227509) устанавливает для поля **\_CancelRequested** значение **true** и записывает потенциально полезную отладочную информацию:

> [!div class="tabbedCodeSnippets"]
> ```cs
>     taskInstance.Canceled += new BackgroundTaskCanceledEventHandler(OnCanceled);
> ```
> ```cpp
>     taskInstance->Canceled += ref new BackgroundTaskCanceledEventHandler(this, &SampleBackgroundTask::OnCanceled);
> ```

## [!div class="tabbedCodeSnippets"]


Перед началом работы зарегистрируйте метод обработчика событий OnCanceled в методе Run фоновой задачи.

Например, используйте следующую строку кода: [!div class="tabbedCodeSnippets"]

Обработка события с помощью выхода из метода Run

> [!div class="tabbedCodeSnippets"]
> ```cs
>     if ((_cancelRequested == false) && (_progress < 100))
>     {
>         _progress += 10;
>         _taskInstance.Progress = _progress;
>     }
>     else
>     {
>         _periodicTimer.Cancel();
>
>         // TODO: Record whether the task completed or was cancelled.
>     }
> ```
> ```cpp
>     if ((CancelRequested == false) && (Progress < 100))
>     {
>         Progress += 10;
>         TaskInstance->Progress = Progress;
>     }
>     else
>     {
>         PeriodicTimer->Cancel();
>
>         // TODO: Record whether the task completed or was cancelled.
>     }
> ```

> При получении запроса на отмену необходимо остановить работу метода Run и выйти из него, если для переменной **\_cancelRequested** установлено значение **true**. Измените код класса вашей фоновой задачи, чтобы проверить переменную флага во время выполнения задачи.

Если для переменной **\_cancelRequested** установлено значение true, остановите выполнение работы.

[Пример фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666) содержит проверку, которая останавливает периодический обратный вызов таймера, если фоновая задача отменена:

> [!div class="tabbedCodeSnippets"]
> ```cs
>     if ((_cancelRequested == false) && (_progress < 100))
>     {
>         _progress += 10;
>         _taskInstance.Progress = _progress;
>     }
>     else
>     {
>         _periodicTimer.Cancel();
>
>         var settings = ApplicationData.Current.LocalSettings;
>         var key = _taskInstance.Task.TaskId.ToString();
>
>         //
>         // Write to LocalSettings to indicate that this background task ran.
>         //
>
>         if (_cancelRequested)
>         {
>             settings.Values[key] = "Canceled";
>         }
>         else
>         {
>             settings.Values[key] = "Completed";
>         }
>         
>         Debug.WriteLine("Background " + _taskInstance.Task.Name + (_cancelRequested ? " Canceled" : " Completed"));
>         
>         //
>         // Indicate that the background task has completed.
>         //
>
>         _deferral.Complete();
>     }
> ```
> ```cpp
>     if ((CancelRequested == false) && (Progress < 100))
>     {
>         Progress += 10;
>         TaskInstance->Progress = Progress;
>     }
>     else
>     {
>         PeriodicTimer->Cancel();
>         
>         //
>         // Write to LocalSettings to indicate that this background task ran.
>         //
>         
>         auto settings = ApplicationData::Current->LocalSettings;
>         auto key = TaskInstance->Task->Name;
>         settings->Values->Insert(key, (Progress < 100) ? "Canceled" : "Completed");
>         
>         //
>         // Indicate that the background task has completed.
>         //
>         
>         Deferral->Complete();
>     }
> ```

## [!div class="tabbedCodeSnippets"]

**Примечание.** Показанный ниже пример кода использует свойство [**IBackgroundTaskInstance**](https://msdn.microsoft.com/library/windows/apps/br224797).[**Progress**](https://msdn.microsoft.com/library/windows/apps/br224800), которое применяется для записи хода выполнения фоновой задачи.

Сведения о ходе выполнения возвращаются приложению при помощи класса [**BackgroundTaskProgressEventArgs**](https://msdn.microsoft.com/library/windows/apps/br224782).

## Измените метод Run, чтобы после остановки работы он записывал, была ли задача завершена или отменена.

В [примере фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666) состояние записывается в LocalSettings:

> [!div class="tabbedCodeSnippets"]
> ```cs
> //
> // The Run method is the entry point of a background task.
> //
> public void Run(IBackgroundTaskInstance taskInstance)
> {
>     Debug.WriteLine("Background " + taskInstance.Task.Name + " Starting...");
>
>     //
>     // Query BackgroundWorkCost
>     // Guidance: If BackgroundWorkCost is high, then perform only the minimum amount
>     // of work in the background task and return immediately.
>     //
>     var cost = BackgroundWorkCost.CurrentBackgroundWorkCost;
>     var settings = ApplicationData.Current.LocalSettings;
>     settings.Values["BackgroundWorkCost"] = cost.ToString();
>
>     //
>     // Associate a cancellation handler with the background task.
>     //
>     taskInstance.Canceled += new BackgroundTaskCanceledEventHandler(OnCanceled);
>
>     //
>     // Get the deferral object from the task instance, and take a reference to the taskInstance;
>     //
>     _deferral = taskInstance.GetDeferral();
>     _taskInstance = taskInstance;
>
>     _periodicTimer = ThreadPoolTimer.CreatePeriodicTimer(new TimerElapsedHandler(PeriodicTimerCallback), TimeSpan.FromSeconds(1));
> }
>
> //
> // Simulate the background task activity.
> //
> private void PeriodicTimerCallback(ThreadPoolTimer timer)
> {
>     if ((_cancelRequested == false) && (_progress < 100))
>     {
>         _progress += 10;
>         _taskInstance.Progress = _progress;
>     }
>     else
>     {
>         _periodicTimer.Cancel();
>
>         var settings = ApplicationData.Current.LocalSettings;
>         var key = _taskInstance.Task.Name;
>
>         //
>         // Write to LocalSettings to indicate that this background task ran.
>         //
>         settings.Values[key] = (_progress < 100) ? "Canceled with reason: " + _cancelReason.ToString() : "Completed";
>         Debug.WriteLine("Background " + _taskInstance.Task.Name + settings.Values[key]);
>
>         //
>         // Indicate that the background task has completed.
>         //
>         _deferral.Complete();
>     }
> }
> ```
> ```cpp
> void SampleBackgroundTask::Run(IBackgroundTaskInstance^ taskInstance)
> {
>     //
>     // Query BackgroundWorkCost
>     // Guidance: If BackgroundWorkCost is high, then perform only the minimum amount
>     // of work in the background task and return immediately.
>     //
>     auto cost = BackgroundWorkCost::CurrentBackgroundWorkCost;
>     auto settings = ApplicationData::Current->LocalSettings;
>     settings->Values->Insert("BackgroundWorkCost", cost.ToString());
>
>     //
>     // Associate a cancellation handler with the background task.
>     //
>     taskInstance->Canceled += ref new BackgroundTaskCanceledEventHandler(this, &SampleBackgroundTask::OnCanceled);
>
>     //
>     // Get the deferral object from the task instance, and take a reference to the taskInstance.
>     //
>     TaskDeferral = taskInstance->GetDeferral();
>     TaskInstance = taskInstance;
>
>     auto timerDelegate = [this](ThreadPoolTimer^ timer)
>     {
>         if ((CancelRequested == false) &&
>             (Progress < 100))
>         {
>             Progress += 10;
>             TaskInstance->Progress = Progress;
>         }
>         else
>         {
>             PeriodicTimer->Cancel();
>
>             //
>             // Write to LocalSettings to indicate that this background task ran.
>             //
>             auto settings = ApplicationData::Current->LocalSettings;
>             auto key = TaskInstance->Task->Name;
>             settings->Values->Insert(key, (Progress < 100) ? "Canceled with reason: " + CancelReason.ToString() : "Completed");
>
>             //
>             // Indicate that the background task has completed.
>             //
>             TaskDeferral->Complete();
>         }
>     };
>
>     TimeSpan period;
>     period.Duration = 1000 * 10000; // 1 second
>     PeriodicTimer = ThreadPoolTimer::CreatePeriodicTimer(ref new TimerElapsedHandler(timerDelegate), period);
> }
> ```

> [!div class="tabbedCodeSnippets"] Комментарии

## Вы можете скачать [пример фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666), чтобы увидеть эти примеры кода в контексте методов.

* [Для наглядности в примере кода показана только часть метода Run (и обратный вызов таймера) из [примера фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666).](create-and-register-a-background-task.md)
* [Пример метода Run](declare-background-tasks-in-the-application-manifest.md)
* [Весь метод Run, а также код обратного вызова для таймера из [примера фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666) показаны ниже для контекста.](guidelines-for-background-tasks.md)
* [[!div class="tabbedCodeSnippets"]](monitor-background-task-progress-and-completion.md)
* [**Примечание.** Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP).](register-a-background-task.md)
* [В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).](respond-to-system-events-with-background-tasks.md)
* [Связанные разделы](run-a-background-task-on-a-timer-.md)
* [Создание и регистрация фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Объявление фоновых задач в манифесте приложения](update-a-live-tile-from-a-background-task.md)
* [Руководство по работе с фоновыми задачами](use-a-maintenance-trigger.md)

* [Отслеживание хода выполнения и завершения фоновых задач](debug-a-background-task.md)
* [Регистрация фоновой задачи](http://go.microsoft.com/fwlink/p/?linkid=254345)



<!--HONumber=Jun16_HO5-->


