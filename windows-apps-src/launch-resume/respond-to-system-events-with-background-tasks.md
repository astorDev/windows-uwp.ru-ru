---
author: TylerMSFT
title: Реагирование на системные события с помощью фоновых задач
description: Узнайте, как создавать фоновые задачи, отвечающие на события SystemTrigger.
ms.assetid: 43C21FEA-28B9-401D-80BE-A61B71F01A89
ms.author: twhitney
ms.date: 07/06/2018
ms.topic: article
keywords: Windows 10, uwp, фоновой задачи
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
ms.openlocfilehash: f65751e894d45bab5b39382c7bec43d5f06f18b2
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2018
ms.locfileid: "6051518"
---
# <a name="respond-to-system-events-with-background-tasks"></a>Реагирование на системные события с помощью фоновых задач

**Важные API**

- [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794)
- [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
- [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838)

Узнайте, как создавать фоновые задачи, отвечающие на события [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839).

В данном разделе предполагается, что для вашего приложения создан класс фоновой задачи, которую требуется запустить в ответ на событие, активированное системой, например при появлении доступа к Интернету или входе пользователя в систему. В этом разделе мы подробно рассмотрим класс [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839). Подробнее о написании класса фоновой задачи см. в статьях [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md) и [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md).

## <a name="create-a-systemtrigger-object"></a>Создание объекта SystemTrigger

Создайте в коде своего приложения новый объект [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838). Первый параметр, *triggerType*, задает тип триггера системного события, который будет активировать эту фоновую задачу. Список типов событий см. в разделе [**SystemTriggerType**](https://msdn.microsoft.com/library/windows/apps/br224839).

Второй параметр, *OneShot*, указывает, будет ли фоновая задача выполняться только один раз при следующем возникновении системного события или она будет выполняться всякий раз при возникновении этого события, пока не отменена регистрация задачи.

Следующий код указывает, что фоновая задача выполняется при появлении доступа к Интернету:

```csharp
SystemTrigger internetTrigger = new SystemTrigger(SystemTriggerType.InternetAvailable, false);
```

```cppwinrt
Windows::ApplicationModel::Background::SystemTrigger internetTrigger{
    Windows::ApplicationModel::Background::SystemTriggerType::InternetAvailable, false};
```

```cpp
SystemTrigger ^ internetTrigger = ref new SystemTrigger(SystemTriggerType::InternetAvailable, false);
```

## <a name="register-the-background-task"></a>Регистрация фоновой задачи

Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Узнать больше о регистрации фоновых задач можно в разделе [Регистрация фоновой задачи](register-a-background-task.md).

Следующий код регистрирует фоновую задачу для фонового процесса, выполняемого вне процесса. Если вы вызываете фоновую задачу, которая выполняется в том же процессе, что и приложение-хост, задавать `entrypoint` не требуется:

```csharp
string entryPoint = "Tasks.ExampleBackgroundTaskClass"; // Namespace name, '.', and the name of the class containing the background task
string taskName   = "Internet-based background task";

BackgroundTaskRegistration task = RegisterBackgroundTask(entryPoint, taskName, internetTrigger, exampleCondition);
```

```cppwinrt
std::wstring entryPoint{ L"Tasks.ExampleBackgroundTaskClass" }; // don't set for in-process background tasks.
std::wstring taskName{ L"Internet-based background task" };

Windows::ApplicationModel::Background::BackgroundTaskRegistration task{
    RegisterBackgroundTask(entryPoint, taskName, internetTrigger, exampleCondition) };
```

```cpp
String ^ entryPoint = "Tasks.ExampleBackgroundTaskClass"; // don't set for in-process background tasks
String ^ taskName   = "Internet-based background task";

BackgroundTaskRegistration ^ task = RegisterBackgroundTask(entryPoint, taskName, internetTrigger, exampleCondition);
```

> [!NOTE]
> Приложения универсальной платформы Windows необходимо вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров.

Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем— метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Подробнее см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

> [!NOTE]
> Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.
 
## <a name="remarks"></a>Комментарии

Чтобы увидеть регистрацию фоновой задачи в действии, скачайте [образец фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666).

Фоновые задачи можно запускать в ответ на события [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838) и [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517), но все равно необходимо [объявить фоновые задачи в манифесте приложения](declare-background-tasks-in-the-application-manifest.md). Необходимо также вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией фоновой задачи любого типа.

Приложения способны регистрировать фоновые задачи, которые выполняются в ответ на события [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843), [**PushNotificationTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700543) и [**NetworkOperatorNotificationTrigger**](https://msdn.microsoft.com/library/windows/apps/br224831). Это позволяет им поддерживать связь с пользователем в реальном времени, даже когда приложение не находится на переднем плане. Дополнительные сведения см. в разделе [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

## <a name="related-topics"></a>Связанные статьи

* [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md)
* [Создание и регистрация фоновой задачи, выполняемой внутри процесса](create-and-register-an-inproc-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях UWP (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)
