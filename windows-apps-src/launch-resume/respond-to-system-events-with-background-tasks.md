---
author: TylerMSFT
title: "Реагирование на системные события с помощью фоновых задач"
description: "Узнайте, как создавать фоновые задачи, отвечающие на события SystemTrigger."
ms.assetid: 43C21FEA-28B9-401D-80BE-A61B71F01A89
ms.sourcegitcommit: 39a012976ee877d8834b63def04e39d847036132
ms.openlocfilehash: f6845dce428f5e22ec68744293b1668da52002bf

---

# Реагирование на системные события с помощью фоновых задач


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794)
-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
-   [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838)

Узнайте, как создавать фоновые задачи, отвечающие на события [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839).

В данном разделе предполагается, что для вашего приложения создан класс фоновой задачи, которую требуется запустить в ответ на событие, активированное системой, например при появлении доступа к Интернету или входе пользователя в систему. В этом разделе мы подробно рассмотрим класс [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839). Подробнее о создании класса фоновой задачи описывается в разделе [Создание и регистрация фоновой задачи](create-and-register-a-background-task.md).

## Создание объекта SystemTrigger


-   Создайте новый объект [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838) в коде своего приложения. Первый параметр, *triggerType*, задает тип триггера системного события, который будет активировать эту фоновую задачу. Список типов событий см. в разделе [**SystemTriggerType**](https://msdn.microsoft.com/library/windows/apps/br224839).

    Второй параметр, *OneShot*, указывает, будет ли фоновая задача выполняться однократно при следующем возникновении системного события и активации фоновых задач или она будет выполняться всякий раз при возникновении этого события, пока не отменена регистрация задачи.

    Следующий код указывает, что фоновая задача выполняется при появлении доступа к Интернету:

    > [!div class="tabbedCodeSnippets"]
    > ```cs
    > SystemTrigger internetTrigger = new SystemTrigger(SystemTriggerType.InternetAvailable, false);
    > ```
    > ```cpp
    > SystemTrigger ^ internetTrigger = ref new SystemTrigger(SystemTriggerType::InternetAvailable, false);
    > ```

## [!div class="tabbedCodeSnippets"]


-   Регистрация фоновой задачи Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи.

    Узнать больше о регистрации фоновых задач можно в разделе [Регистрация фоновой задачи](register-a-background-task.md).

    > [!div class="tabbedCodeSnippets"]
    > ```cs
    > string entryPoint = "Tasks.ExampleBackgroundTaskClass";
    > string taskName   = "Internet-based background task";
    >
    > BackgroundTaskRegistration task = RegisterBackgroundTask(entryPoint, taskName, internetTrigger, exampleCondition);
    > ```
    > ```cpp
    > String ^ entryPoint = "Tasks.ExampleBackgroundTaskClass";
    > String ^ taskName   = "Internet-based background task";
    >
    > BackgroundTaskRegistration ^ task = RegisterBackgroundTask(entryPoint, taskName, internetTrigger, exampleCondition);
    > ```

    > Следующий код регистрирует фоновую задачу:

    [!div class="tabbedCodeSnippets"] **Примечание.** Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров.

    > Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем — метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Подробнее см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md). **Примечание.**  Параметры регистрации фоновых задач проверяются во время регистрации.

     

## Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка.


Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

Комментарии Чтобы увидеть регистрацию фоновой задачи в действии, скачайте [образец фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666).

Фоновые задачи можно запускать в ответ на события [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838) и [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517), но все равно необходимо [объявить фоновые задачи в манифесте приложения](declare-background-tasks-in-the-application-manifest.md). Необходимо также вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией фоновой задачи любого типа.

> Приложения способны регистрировать фоновые задачи, которые выполняются в ответ на события [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843), [**PushNotificationTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700543) и [**NetworkOperatorNotificationTrigger**](https://msdn.microsoft.com/library/windows/apps/br224831). Это позволяет им поддерживать связь с пользователем в реальном времени, даже когда приложение не находится на переднем плане. Дополнительные сведения см. в разделе [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

 
## **Примечание.** Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP).


****

* [В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).](create-and-register-a-background-task.md)
* [Связанные разделы](declare-background-tasks-in-the-application-manifest.md)
* [Создание и регистрация фоновой задачи](handle-a-cancelled-background-task.md)
* [Объявление фоновых задач в манифесте приложения](monitor-background-task-progress-and-completion.md)
* [Обработка отмененной фоновой задачи](register-a-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](set-conditions-for-running-a-background-task.md)
* [Регистрация фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Задание условий выполнения фоновой задачи](use-a-maintenance-trigger.md)
* [Обновление живой плитки из фоновой задачи](run-a-background-task-on-a-timer-.md)
* [Использование триггера обслуживания](guidelines-for-background-tasks.md)

****

* [Запуск фоновой задачи по таймеру](debug-a-background-task.md)
* [Руководство по работе с фоновыми задачами](http://go.microsoft.com/fwlink/p/?linkid=254345)

 

 



<!--HONumber=Jun16_HO5-->


