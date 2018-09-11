---
author: TylerMSFT
title: Создание и регистрация фоновой задачи, выполняемой внутри процесса
description: Создание и регистрация внутренней задачи процесса, которая выполняется в том же процессе, что и приложение переднего плана.
ms.author: twhitney
ms.date: 11/03/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, фоновой задачи
ms.assetid: d99de93b-e33b-45a9-b19f-31417f1e9354
ms.localizationpriority: medium
ms.openlocfilehash: 5879977662dc2bd609d09e5fe53fc2a2f0b9180f
ms.sourcegitcommit: 72710baeee8c898b5ab77ceb66d884eaa9db4cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "3848668"
---
# <a name="create-and-register-an-in-process-background-task"></a>Создание и регистрация фоновой задачи, выполняемой внутри процесса

**Важные API**

-   [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794)
-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
-   [**BackgroundTaskCompletedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224781)

В этом разделе рассказывается, как создать и зарегистрировать фоновую задачу, которая будет выполняться в том же процессе, что и ваше приложение.

Выполняемые внутри процесса фоновые задачи проще реализовать, чем фоновые задачи, выполняемые вне процесса. Однако они менее устойчивы. Сбой выполнения кода в фоновой задаче внутри процесса затронет работу вашего приложения. Также обратите внимание, что триггеры [DeviceUseTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceusetrigger.aspx), [DeviceServicingTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceservicingtrigger.aspx) и **IoTStartupTask** нельзя использовать в рамках модели внутри процесса. Запуск фоновой задачи VoIP в приложении также невозможен. Использование этих триггеров и задач все еще поддерживается в рамках модели фоновой задачи, выполняемой вне процесса.

Имейте в виду, что фоновые задачи можно остановить (даже внутри фонового процесса приложения), если время их выполнения выходит за заданные пределы. Для некоторых целей обеспечение устойчивости при выделении работы в фоновую задачу, выполняющуюся в отдельном процессе, по-прежнему является целесообразным. Выделение фоновой работы в задачу, не связанную с приложением переднего плана, может быть лучшим решением, когда для работы не требуется обмен данными с приложением переднего плана.

## <a name="fundamentals"></a>Основы

Модель с выполнением внутри процесса оптимизирует жизненный цикл приложения с помощью улучшенных уведомлений, когда приложение работает на переднем плане или в фоновом режиме. Для этих переходов между режимами работы приложения доступно два новых события из объекта Application: [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground) и [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground). Эти события встраиваются в жизненный цикл приложения на основании состояния видимости вашего приложения. Дополнительные сведения об этих событиях и об их влиянии на жизненный цикл приложения см. в разделе [Жизненный цикл приложения](app-lifecycle.md).

На высоком уровне вы будете управлять событием **EnteredBackground** для запуска вашего кода, который будет выполняться одновременно с выполнением вашего приложения в фоновом режиме, и событием **LeavingBackground**, чтобы знать, когда приложение перемещается на передний план.

## <a name="register-your-background-task-trigger"></a>Регистрация триггера фоновой задачи

Регистрация фонового действия внутри процесса выполняется практически так же, как и регистрация фонового действия вне процесса. Регистрация всех фоновых триггеров выполняется с помощью класса [BackgroundTaskBuilder](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundtaskbuilder.aspx?f=255&MSPPError=-2147217396). Построитель упрощает процесс регистрации фоновой задачи путем установки всех необходимых значений в одном месте.

> [!div class="tabbedCodeSnippets"]
> ```cs
> var builder = new BackgroundTaskBuilder();
> builder.Name = "My Background Trigger";
> builder.SetTrigger(new TimeTrigger(15, true));
> // Do not set builder.TaskEntryPoint for in-process background tasks
> // Here we register the task and work will start based on the time trigger.
> BackgroundTaskRegistration task = builder.Register();
> ```

> [!NOTE]
> Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров.
> Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем— метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Дополнительные сведения см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

Для фоновых действий внутри процесса установка значения свойства `TaskEntryPoint.` не выполняется. За счет того, что значение для него не задается, появляется возможность использовать точку входа по умолчанию — новый защищенный метод в объекте Application под названием [OnBackgroundActivated()](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onbackgroundactivated.aspx).

После регистрации триггера он будет запущен с учетом типа триггера, указанного в методе [SetTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundtaskbuilder.settrigger.aspx). В примере кода, приведенном выше, используется триггер [TimeTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.timetrigger.aspx), который будет срабатывать через 15 минут после его регистрации.

## <a name="add-a-condition-to-control-when-your-task-will-run-optional"></a>Добавление условия, которое будет контролировать время запуска задачи (необязательно)

Вы можете добавить условие, чтобы контролировать, в какой момент времени после возникновения события триггера запустится ваша задача. Например, если вы не хотите, чтобы задача запускалась в отсутствие пользователя, используйте условие **UserPresent**. Список возможных условий см. в статье [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

Следующий пример кода назначает условие, при котором необходимо присутствие пользователя:

> [!div class="tabbedCodeSnippets"]
> ```cs
> builder.AddCondition(new SystemCondition(SystemConditionType.UserPresent));
> ```

## <a name="place-your-background-activity-code-in-onbackgroundactivated"></a>Размещение кода фонового действия в методе OnBackgroundActivated()

Поместите код фонового действия в [OnBackgroundActivated](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onbackgroundactivated.aspx) реагировать на ваш фоновый триггер при его срабатывании. **OnBackgroundActivated** может рассматриваться как [IBackgroundTask.Run](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.run.aspx?f=255&MSPPError=-2147217396). Метод имеет [BackgroundActivatedEventArgs](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.activation.backgroundactivatedeventargs.aspx) параметра, который содержит все, что обеспечивает метод **Run** . Например в файле App.xaml.cs:

``` cs
using Windows.ApplicationModel.Background;

...

sealed partial class App : Application
{
  ...

  protected override void OnBackgroundActivated(BackgroundActivatedEventArgs args)
  {
      base.OnBackgroundActivated(args);
      IBackgroundTaskInstance taskInstance = args.TaskInstance;
      DoYourBackgroundWork(taskInstance);  
  }
}
```

Более подробный пример **OnBackgroundActivated** см. в разделе [Преобразование службы приложения для запуска в одном процессе с ее основным приложением](convert-app-service-in-process.md).

## <a name="handle-background-task-progress-and-completion"></a>Обработка хода выполнения и завершения фоновых задач

Ход выполнения и завершения задачи можно отслеживать так же, как и аналогичные состояния фоновых задач с несколькими процессами (см. раздел [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)), однако, скорее всего, вы обнаружите, что эти состояния выполнения и завершения проще отслеживать, используя соответствующие переменные из вашего приложения. Это преимущество обеспечивается за счет выполнения кода фонового действия в рамках того же процесса, в котором работает приложение.

## <a name="handle-background-task-cancellation"></a>Обработка отмены фоновых задач

Отмена фоновых задач внутри процесса выполняется так же, как и отмена фоновых задач, выполняемых вне процесса (см. раздел [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)). Имейте в виду, что обработчик события **BackgroundActivated** должен выйти из процесса, перед тем как произойдет отмена. В противном случае будет завершен весь процесс. В случае непредвиденного закрытия фонового приложения при отмене фоновой задачи убедитесь, что обработчик вышел из процесса до отмены задачи.

## <a name="the-manifest"></a>Манифест

В отличие от фоновых задач, выполняемых вне процесса, добавлять сведения о фоновой задаче в манифест пакета, чтобы обеспечить выполнение фоновых задач внутри процесса, не требуется.

## <a name="summary-and-next-steps"></a>Краткая сводка и дальнейшие действия

Теперь вы знакомы с основами написания фоновой задачи, выполняемой внутри процесса.

В статьях ниже можно найти справочник по API, концептуальное руководство по фоновым задачам и подробные инструкции по созданию приложений, использующих фоновые задачи.

## <a name="related-topics"></a>Связанные разделы

**Учебные статьи с подробными сведениями о фоновых задачах**

* [Преобразование фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса](convert-out-of-process-background-task.md)
* [Создание и регистрация фоновой задачи, выполняемой вне процесса](create-and-register-a-background-task.md)
* [Воспроизведение мультимедиа в фоновом режиме](https://msdn.microsoft.com/windows/uwp/audio-video-camera/background-audio)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)

**Руководство по фоновым задачам**

* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях UWP (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)

**Справочник по API для фоновых задач**

* [**Windows.ApplicationModel.Background**](https://msdn.microsoft.com/library/windows/apps/br224847)
