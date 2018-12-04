---
title: Обработка отмененной фоновой задачи
description: Узнайте, как создать фоновую задачу, которая распознает запросы на отмену, прекращает работу и сообщает приложению об отмене, используя постоянное хранилище.
ms.assetid: B7E23072-F7B0-4567-985B-737DD2A8728E
ms.date: 07/05/2018
ms.topic: article
keywords: Windows 10, uwp, фоновой задачи
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
ms.openlocfilehash: b888bf1373dfb0cac80881117570eb23e8802142
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8479039"
---
# <a name="handle-a-cancelled-background-task"></a>Обработка отмененной фоновой задачи

**Важные API**

-   [**BackgroundTaskCanceledEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224775)
-   [**IBackgroundTaskInstance**](https://msdn.microsoft.com/library/windows/apps/br224797)
-   [**ApplicationData.Current**](https://msdn.microsoft.com/library/windows/apps/br241619)

Узнайте, как создать фоновую задачу, которая распознает запрос на отмену, прекращает работу и сообщает приложению об отмене, используя постоянное хранилище.

В этом разделе предполагается, что вы уже создали класс фоновой задачи, включая метод **Run** , используемого в качестве точки входа фоновой задачи. Чтобы быстро приступить к созданию фоновой задачи, выполните инструкции из раздела [Создание и регистрация фоновой задачи, выполняемой вне процесса](create-and-register-a-background-task.md) или [Создание и регистрация фоновой задачи, выполняемой внутри процесса](create-and-register-an-inproc-background-task.md). Более углубленно с условиями и триггерами можно ознакомиться в разделе [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

Этот раздел также применим к фоновым задачам, выполняемым внутри процесса. Однако вместо метода **Run** **OnBackgroundActivated**. В случае фоновых задач внутри процесса не требуется использовать постоянное хранилище для подачи сигнала об отмене, поскольку можно сообщить об отмене с помощью состояния приложения, так как фоновая задача выполняется в том же процессе, что и приложение переднего плана.

## <a name="use-the-oncanceled-method-to-recognize-cancellation-requests"></a>Использование метода OnCanceled для распознавания запросов на отмену

Напишите метод, который будет обрабатывать событие отмены.

> [!NOTE]
> Если устройству не хватает памяти, фоновые задачи могут быть завершены для всех семейств устройств (за исключением настольных компьютеров). Если исключение памяти не отображается или приложение не обрабатывает его, то фоновая задача будет завершена без предупреждения и без вызова события OnCanceled. Это позволяет не прерывать работу пользователя с активным приложением. Ваша фоновая задача должна поддерживать этот сценарий.

Создайте метод с именем **OnCanceled**, как показано ниже. Этот метод является точкой входа, вызываемой средой выполнения Windows при создании запроса на отмену для вашей фоновой задачи.

```csharp
private void OnCanceled(
    IBackgroundTaskInstance sender,
    BackgroundTaskCancellationReason reason)
{
    // TODO: Add code to notify the background task that it is cancelled.
}
```

```cppwinrt
void ExampleBackgroundTask::OnCanceled(
    Windows::ApplicationModel::Background::IBackgroundTaskInstance const& taskInstance,
    Windows::ApplicationModel::Background::BackgroundTaskCancellationReason reason)
{
    // TODO: Add code to notify the background task that it is cancelled.
}
```

```cpp
void ExampleBackgroundTask::OnCanceled(
    IBackgroundTaskInstance^ taskInstance,
    BackgroundTaskCancellationReason reason)
{
    // TODO: Add code to notify the background task that it is cancelled.
}
```

Добавьте флаговую переменную **\_CancelRequested** в класс фоновой задачи. Она будет использоваться для обозначения того, что был сделан запрос на отмену.

```csharp
volatile bool _CancelRequested = false;
```

```cppwinrt
private:
    volatile bool m_cancelRequested;
```

```cpp
private:
    volatile bool CancelRequested;
```

В методе **OnCanceled** , созданную на шаге 1 задайте переменной флага **\_CancelRequested** значение **true**.

Полный [пример фоновой задачи]( http://go.microsoft.com/fwlink/p/?linkid=227509) метод **OnCanceled** устанавливает **\_CancelRequested** значение **true** и записывает потенциально полезную отладочную.

```csharp
private void OnCanceled(IBackgroundTaskInstance sender, BackgroundTaskCancellationReason reason)
{
    // Indicate that the background task is canceled.
    _cancelRequested = true;

    Debug.WriteLine("Background " + sender.Task.Name + " Cancel Requested...");
}
```

```cppwinrt
void ExampleBackgroundTask::OnCanceled(
    Windows::ApplicationModel::Background::IBackgroundTaskInstance const& taskInstance,
    Windows::ApplicationModel::Background::BackgroundTaskCancellationReason reason)
{
    // Indicate that the background task is canceled.
    m_cancelRequested = true;
}
```

```cpp
void ExampleBackgroundTask::OnCanceled(IBackgroundTaskInstance^ taskInstance, BackgroundTaskCancellationReason reason)
{
    // Indicate that the background task is canceled.
    CancelRequested = true;
}
```

В метод **Run** фоновой задачи зарегистрируйте метод обработчика событий **OnCanceled** перед началом работы. В фоновой задаче внутри процесса можно выполнить эту регистрацию как часть инициализации приложения. Например используйте следующую строку кода.

```csharp
taskInstance.Canceled += new BackgroundTaskCanceledEventHandler(OnCanceled);
```

```cppwinrt
taskInstance.Canceled({ this, &ExampleBackgroundTask::OnCanceled });
```

```cpp
taskInstance->Canceled += ref new BackgroundTaskCanceledEventHandler(this, &ExampleBackgroundTask::OnCanceled);
```

## <a name="handle-cancellation-by-exiting-your-background-task"></a>Обработка отмены путем выхода из фоновой задачи

При получении запроса на отмену необходимо остановить работу метода, выполняющего фоновую работу, и выйти из него, если для переменной **\_cancelRequested** установлено значение **true**. Для внутри процесса фоновые задачи это означает возврат из метода **OnBackgroundActivated** . Для фоновых задач вне процесса это означает возврат из метода **Run** .

Измените код класса вашей фоновой задачи, чтобы проверить переменную флага во время выполнения задачи. Если **\_cancelRequested** становится значение true, остановите рабочие продолжение.

[Пример фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666) , включает проверку, которая останавливает периодический обратный вызов таймера, если фоновая задача отменена.

```csharp
if ((_cancelRequested == false) && (_progress < 100))
{
    _progress += 10;
    _taskInstance.Progress = _progress;
}
else
{
    _periodicTimer.Cancel();
    // TODO: Record whether the task completed or was cancelled.
}
```

```cppwinrt
if (!m_cancelRequested && m_progress < 100)
{
    m_progress += 10;
    m_taskInstance.Progress(m_progress);
}
else
{
    m_periodicTimer.Cancel();
    // TODO: Record whether the task completed or was cancelled.
}
```

```cpp
if ((CancelRequested == false) && (Progress < 100))
{
    Progress += 10;
    TaskInstance->Progress = Progress;
}
else
{
    PeriodicTimer->Cancel();
    // TODO: Record whether the task completed or was cancelled.
}
```

> [!NOTE]
> Приведенный выше пример кода использует [**IBackgroundTaskInstance**](https://msdn.microsoft.com/library/windows/apps/br224797). Свойства [**хода выполнения**](https://msdn.microsoft.com/library/windows/apps/br224800) , которое используется для записи хода выполнения фоновой задачи. Сведения о ходе выполнения возвращаются приложению при помощи класса [**BackgroundTaskProgressEventArgs**](https://msdn.microsoft.com/library/windows/apps/br224782).

Измените метод **Run** , таким образом, чтобы после остановки работы он записывал ли задача завершена или отменена. Этот шаг относится к фоновым задачам, которые выполняются вне процесса, поскольку требуется способ связи между двумя процессами при отмене фоновой задачи. Чтобы указать, что задача была отменена для фоновых задач внутри процесса, можно просто использовать состояние совместно с приложением.

[Пример фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666) состояние записывается в LocalSettings.

```csharp
if ((_cancelRequested == false) && (_progress < 100))
{
    _progress += 10;
    _taskInstance.Progress = _progress;
}
else
{
    _periodicTimer.Cancel();

    var settings = ApplicationData.Current.LocalSettings;
    var key = _taskInstance.Task.TaskId.ToString();

    // Write to LocalSettings to indicate that this background task ran.
    if (_cancelRequested)
    {
        settings.Values[key] = "Canceled";
    }
    else
    {
        settings.Values[key] = "Completed";
    }
        
    Debug.WriteLine("Background " + _taskInstance.Task.Name + (_cancelRequested ? " Canceled" : " Completed"));
        
    // Indicate that the background task has completed.
    _deferral.Complete();
}
```

```cppwinrt
if (!m_cancelRequested && m_progress < 100)
{
    m_progress += 10;
    m_taskInstance.Progress(m_progress);
}
else
{
    m_periodicTimer.Cancel();

    // Write to LocalSettings to indicate that this background task ran.
    auto settings{ Windows::Storage::ApplicationData::Current().LocalSettings() };
    auto key{ m_taskInstance.Task().Name() };
    settings.Values().Insert(key, (m_progress < 100) ? winrt::box_value(L"Canceled") : winrt::box_value(L"Completed"));

    // Indicate that the background task has completed.
    m_deferral.Complete();
}
```

```cpp
if ((CancelRequested == false) && (Progress < 100))
{
    Progress += 10;
    TaskInstance->Progress = Progress;
}
else
{
    PeriodicTimer->Cancel();
        
    // Write to LocalSettings to indicate that this background task ran.
    auto settings = ApplicationData::Current->LocalSettings;
    auto key = TaskInstance->Task->Name;
    settings->Values->Insert(key, (Progress < 100) ? "Canceled" : "Completed");
        
    // Indicate that the background task has completed.
    Deferral->Complete();
}
```

## <a name="remarks"></a>Примечания

Вы можете скачать [пример фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666), чтобы увидеть эти примеры кода в контексте методов.

Для наглядности в примере кода показана только часть метода **Run** (и обратный вызов таймера) из [примера фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666).

## <a name="run-method-example"></a>Пример метода Run

Выполните метод **Run** , а также код обратного вызова для таймера, из [примера фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666) показаны ниже для контекста.

```csharp
// The Run method is the entry point of a background task.
public void Run(IBackgroundTaskInstance taskInstance)
{
    Debug.WriteLine("Background " + taskInstance.Task.Name + " Starting...");

    // Query BackgroundWorkCost
    // Guidance: If BackgroundWorkCost is high, then perform only the minimum amount
    // of work in the background task and return immediately.
    var cost = BackgroundWorkCost.CurrentBackgroundWorkCost;
    var settings = ApplicationData.Current.LocalSettings;
    settings.Values["BackgroundWorkCost"] = cost.ToString();

    // Associate a cancellation handler with the background task.
    taskInstance.Canceled += new BackgroundTaskCanceledEventHandler(OnCanceled);

    // Get the deferral object from the task instance, and take a reference to the taskInstance;
    _deferral = taskInstance.GetDeferral();
    _taskInstance = taskInstance;

    _periodicTimer = ThreadPoolTimer.CreatePeriodicTimer(new TimerElapsedHandler(PeriodicTimerCallback), TimeSpan.FromSeconds(1));
}

// Simulate the background task activity.
private void PeriodicTimerCallback(ThreadPoolTimer timer)
{
    if ((_cancelRequested == false) && (_progress < 100))
    {
        _progress += 10;
        _taskInstance.Progress = _progress;
    }
    else
    {
        _periodicTimer.Cancel();

        var settings = ApplicationData.Current.LocalSettings;
        var key = _taskInstance.Task.Name;

        // Write to LocalSettings to indicate that this background task ran.
        settings.Values[key] = (_progress < 100) ? "Canceled with reason: " + _cancelReason.ToString() : "Completed";
        Debug.WriteLine("Background " + _taskInstance.Task.Name + settings.Values[key]);

        // Indicate that the background task has completed.
        _deferral.Complete();
    }
}
```

```cppwinrt
void ExampleBackgroundTask::Run(Windows::ApplicationModel::Background::IBackgroundTaskInstance const& taskInstance)
{
    // Query BackgroundWorkCost
    // Guidance: If BackgroundWorkCost is high, then perform only the minimum amount
    // of work in the background task and return immediately.
    auto cost{ Windows::ApplicationModel::Background::BackgroundWorkCost::CurrentBackgroundWorkCost() };
    auto settings{ Windows::Storage::ApplicationData::Current().LocalSettings() };
    std::wstring costAsString{ L"Low" };
    if (cost == Windows::ApplicationModel::Background::BackgroundWorkCostValue::Medium) costAsString = L"Medium";
    else if (cost == Windows::ApplicationModel::Background::BackgroundWorkCostValue::High) costAsString = L"High";
    settings.Values().Insert(L"BackgroundWorkCost", winrt::box_value(costAsString));

    // Associate a cancellation handler with the background task.
    taskInstance.Canceled({ this, &ExampleBackgroundTask::OnCanceled });

    // Get the deferral object from the task instance, and take a reference to the taskInstance.
    m_deferral = taskInstance.GetDeferral();
    m_taskInstance = taskInstance;

    Windows::Foundation::TimeSpan period{ std::chrono::seconds{1} };
    m_periodicTimer = Windows::System::Threading::ThreadPoolTimer::CreatePeriodicTimer([this](Windows::System::Threading::ThreadPoolTimer timer)
    {
        if (!m_cancelRequested && m_progress < 100)
        {
            m_progress += 10;
            m_taskInstance.Progress(m_progress);
        }
        else
        {
            m_periodicTimer.Cancel();

            // Write to LocalSettings to indicate that this background task ran.
            auto settings{ Windows::Storage::ApplicationData::Current().LocalSettings() };
            auto key{ m_taskInstance.Task().Name() };
            settings.Values().Insert(key, (m_progress < 100) ? winrt::box_value(L"Canceled") : winrt::box_value(L"Completed"));

            // Indicate that the background task has completed.
            m_deferral.Complete();
        }
    }, period);
}
```

```cpp
void ExampleBackgroundTask::Run(IBackgroundTaskInstance^ taskInstance)
{
    // Query BackgroundWorkCost
    // Guidance: If BackgroundWorkCost is high, then perform only the minimum amount
    // of work in the background task and return immediately.
    auto cost = BackgroundWorkCost::CurrentBackgroundWorkCost;
    auto settings = ApplicationData::Current->LocalSettings;
    settings->Values->Insert("BackgroundWorkCost", cost.ToString());

    // Associate a cancellation handler with the background task.
    taskInstance->Canceled += ref new BackgroundTaskCanceledEventHandler(this, &ExampleBackgroundTask::OnCanceled);

    // Get the deferral object from the task instance, and take a reference to the taskInstance.
    TaskDeferral = taskInstance->GetDeferral();
    TaskInstance = taskInstance;

    auto timerDelegate = [this](ThreadPoolTimer^ timer)
    {
        if ((CancelRequested == false) &&
            (Progress < 100))
        {
            Progress += 10;
            TaskInstance->Progress = Progress;
        }
        else
        {
            PeriodicTimer->Cancel();

            // Write to LocalSettings to indicate that this background task ran.
            auto settings = ApplicationData::Current->LocalSettings;
            auto key = TaskInstance->Task->Name;
            settings->Values->Insert(key, (Progress < 100) ? "Canceled with reason: " + CancelReason.ToString() : "Completed");

            // Indicate that the background task has completed.
            TaskDeferral->Complete();
        }
    };

    TimeSpan period;
    period.Duration = 1000 * 10000; // 1 second
    PeriodicTimer = ThreadPoolTimer::CreatePeriodicTimer(ref new TimerElapsedHandler(timerDelegate), period);
}
```

## <a name="related-topics"></a>Статьи по теме

- [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md)
- [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md)
- [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
- [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
- [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
- [Регистрация фоновой задачи](register-a-background-task.md)
- [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
- [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)
- [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
- [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
- [Использование триггера обслуживания](use-a-maintenance-trigger.md)
- [Отладка фоновой задачи](debug-a-background-task.md)
- [Вызов событий приостановки, возобновления и фоновых событий в приложениях UWP (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)
