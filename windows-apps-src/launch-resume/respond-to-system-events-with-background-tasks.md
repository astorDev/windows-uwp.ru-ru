---
author: TylerMSFT
title: "Реагирование на системные события с помощью фоновых задач"
description: "Узнайте, как создавать фоновые задачи, отвечающие на события SystemTrigger."
ms.assetid: 43C21FEA-28B9-401D-80BE-A61B71F01A89
translationtype: Human Translation
ms.sourcegitcommit: 7d1c160f8b725cd848bf8357325c6ca284b632ae
ms.openlocfilehash: a3d7ac01724b1e8dcabe3219855eabe172924764

---

# Реагирование на системные события с помощью фоновых задач

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794)
-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
-   [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838)

Узнайте, как создавать фоновые задачи, отвечающие на события [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839).

В данном разделе предполагается, что для вашего приложения создан класс фоновой задачи, которую требуется запустить в ответ на событие, активированное системой, например при появлении доступа к Интернету или входе пользователя в систему. В этом разделе мы подробно рассмотрим класс [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839). Подробнее о написании класса фоновой задачи см. в статьях [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md) и [Создание и регистрация внепроцессной фоновой задачи](create-and-register-an-outofproc-background-task.md).

## Создание объекта SystemTrigger

-   Создайте в коде своего приложения новый объект [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838). Первый параметр, *triggerType*, задает тип триггера системного события, который будет активировать эту фоновую задачу. Список типов событий см. в разделе [**SystemTriggerType**](https://msdn.microsoft.com/library/windows/apps/br224839).

    Второй параметр, *OneShot*, указывает, будет ли фоновая задача выполняться только один раз при следующем возникновении системного события или она будет выполняться всякий раз при возникновении этого события, пока не отменена регистрация задачи.

    Следующий код указывает, что фоновая задача выполняется при появлении доступа к Интернету:

    > [!div class="tabbedCodeSnippets"]
    > ```cs
    > SystemTrigger internetTrigger = new SystemTrigger(SystemTriggerType.InternetAvailable, false);
    > ```
    > ```cpp
    > SystemTrigger ^ internetTrigger = ref new SystemTrigger(SystemTriggerType::InternetAvailable, false);
    > ```

## Регистрация фоновой задачи

-   Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Узнать больше о регистрации фоновых задач можно в разделе [Регистрация фоновой задачи](register-a-background-task.md).

    Следующий код регистрирует фоновую задачу для фонового процесса, выполняемого вне процесса. Если вы вызываете фоновую задачу, которая выполняется в том же процессе, что и приложение-хост, задавать `entrypoint` не требуется:

    > [!div class="tabbedCodeSnippets"]
    > ```cs
    > string entryPoint = "Tasks.ExampleBackgroundTaskClass"; // Namespace name, '.', and the name of the class containing the background task
    > string taskName   = "Internet-based background task";
    >
    > BackgroundTaskRegistration task = RegisterBackgroundTask(entryPoint, taskName, internetTrigger, exampleCondition);
    > ```
    > ```cpp
    > String ^ entryPoint = "Tasks.ExampleBackgroundTaskClass"; // don't set for in-process background tasks
    > String ^ taskName   = "Internet-based background task";
    >
    > BackgroundTaskRegistration ^ task = RegisterBackgroundTask(entryPoint, taskName, internetTrigger, exampleCondition);
    > ```

    > **Примечание** Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров.

    Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем— метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Подробнее см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

    > **Примечание.**  Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.
 
## Комментарии

Чтобы увидеть регистрацию фоновой задачи в действии, скачайте [образец фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666).

Фоновые задачи можно запускать в ответ на события [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838) и [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517), но все равно необходимо [объявить фоновые задачи в манифесте приложения](declare-background-tasks-in-the-application-manifest.md). Необходимо также вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией фоновой задачи любого типа.

Приложения способны регистрировать фоновые задачи, которые выполняются в ответ на события [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843), [**PushNotificationTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700543) и [**NetworkOperatorNotificationTrigger**](https://msdn.microsoft.com/library/windows/apps/br224831). Это позволяет им поддерживать связь с пользователем в реальном времени, даже когда приложение не находится на переднем плане. Дополнительные сведения см. в разделе [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

> **Примечание.** Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). Если вы разрабатываете приложения для Windows8.x или Windows Phone8.x, обратитесь к [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

## Статьи по теме

****

* [Создание и регистрация внепроцессной фоновой задачи](create-and-register-an-outofproc-background-task.md)
* [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md)
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
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях Магазина Windows (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)



<!--HONumber=Nov16_HO1-->


