---
author: TylerMSFT
title: "Запуск фоновой задачи по таймеру"
description: "Узнайте, как запланировать однократное выполнение фоновой задачи или периодически запускать фоновую задачу."
ms.assetid: 0B7F0BFF-535A-471E-AC87-783C740A61E9
translationtype: Human Translation
ms.sourcegitcommit: 16202eeb37421acf75a9032dfc1eec397d23ce4f
ms.openlocfilehash: dd0d0fe0081eac112ce22e8a035b4bb70be3bef0

---

# Запуск фоновой задачи по таймеру

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
-   [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843)
-   [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700494)

Узнайте, как запланировать однократное выполнение фоновой задачи или периодически запускать фоновую задачу.

-   В примере предполагается, что есть фоновая задача, которая должна выполняться периодически или в определенное время для поддержки вашего приложения. Фоновая задача будет запускаться только с помощью [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843), если вызван метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485).
-   В этом разделе предполагается, что вы уже создали класс фоновой задачи. Чтобы приступить к быстрому созданию фоновой задачи, выполните инструкции из раздела [Создание и регистрация фоновой задачи, которая запускается в одном процессе](create-and-register-a-singleprocess-background-task.md) или [Создание и регистрация фоновой задачи, которая запускается в отдельном процессе](create-and-register-a-background-task.md). Более углубленно с условиями и триггерами можно ознакомиться в разделе [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

## Создание триггера времени

-   Создайте новый [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843). Второй параметр *OneShot* указывает, будет ли фоновая задача выполняться однократно или периодически. Если *OneShot* имеет значение True, первый параметр (*FreshnessTime*) задает число минут ожидания перед планированием фоновой задачи. Если *OneShot* имеет значение False, частоту выполнения фоновой задачи определяет *FreshnessTime*.

    Встроенный таймер для приложений UWP, предназначенных для настольных компьютеров или мобильных устройств, выполняет фоновые задачи с интервалом в 15 минут.

    -   Если параметру *FreshnessTime* задано значение 15минут, а параметру *OneShot* — значение true, задача будет запланирована для однократного выполнения через 15–30минут с момента ее регистрации. Если задано значение "25 минут" и *OneShot* имеет значение true, задача будет запланирована для однократного выполнения через 25–40минут с момента ее регистрации.

    -   Если параметру *FreshnessTime* задано значение 15 минут, а параметру *OneShot* — false, задача будет запланирована для выполнения каждые 15минут через 15–30минут с момента ее регистрации. Если задано значение "n минут" и *OneShot* имеет значение false, задача будет запланирована для выполнения каждые n минут, начиная с промежутка между n и n+15 после регистрации.

    **Примечание.** Если параметру *FreshnessTime* задано значение меньше 15минут, при попытке зарегистрировать фоновую задачу появляется исключение.
 

    Например, данный триггер запускает выполнение фоновой задачи один раз в час:

    > [!div class="tabbedCodeSnippets"]
    > ```cs
    > TimeTrigger hourlyTrigger = new TimeTrigger(60, false);
    > ```
    > ```cpp
    > TimeTrigger ^ hourlyTrigger = ref new TimeTrigger(60, false);
    > ```

## Добавление условия (необязательно)

-   При необходимости можно также создать условие фоновой задачи, чтобы контролировать время запуска задачи. Условие не даст запустить фоновую задачу, пока это условие не будет выполнено. Подробнее можно узнать в разделе [Задание условий для выполнения фоновой задачи](set-conditions-for-running-a-background-task.md).

    В этом примере условию присвоено значение **UserPresent**, поэтому после срабатывания триггера задача выполняется, только если пользователь активен. Список возможных условий см. в статье [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

    > [!div class="tabbedCodeSnippets"]
    > ```cs
    > SystemCondition userCondition = new SystemCondition(SystemConditionType.UserPresent);
    > ```
    > ```cpp
    > SystemCondition ^ userCondition = ref new SystemCondition(SystemConditionType::UserPresent)
    > ```

##  Вызов RequestAccessAsync()

-   Перед регистрацией фоновой задачи [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843) вызовите [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700494).

    > [!div class="tabbedCodeSnippets"]
    > ```cs
    > BackgroundExecutionManager.RequestAccessAsync();
    > ```
    > ```cpp
    > BackgroundExecutionManager::RequestAccessAsync();
    > ```

## Регистрация фоновой задачи

-   Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Узнать больше о регистрации фоновых задач можно в разделе [Регистрация фоновой задачи](register-a-background-task.md).

> [!Important]
> Для фоновых задач, которые выполняются в том же процессе, что и приложение, не устанавливайте `entryPoint` для фоновых задач, которые выполняются в отдельном процессе из приложения; задайте `entryPoint` в качестве пространства имен ('.') и задайте имя класса, который содержит реализацию вашей фоновой задачи.

    The following code registers a background task that runs in a separate process:

    > > [!div class="tabbedCodeSnippets"]
    > ```cs
    > string entryPoint = "Tasks.ExampleBackgroundTaskClass";
    > string taskName   = "Example hourly background task";
    >
    > BackgroundTaskRegistration task = RegisterBackgroundTask(entryPoint, taskName, hourlyTrigger, userCondition);
    > ```
    > ```cpp
    > String ^ entryPoint = "Tasks.ExampleBackgroundTaskClass";
    > String ^ taskName   = "Example hourly background task";
    >
    > BackgroundTaskRegistration ^ task = RegisterBackgroundTask(entryPoint, taskName, hourlyTrigger, userCondition);
    > ```

    > **Note**  Background task registration parameters are validated at the time of registration. An error is returned if any of the registration parameters are invalid. Ensure that your app gracefully handles scenarios where background task registration fails - if instead your app depends on having a valid registration object after attempting to register a task, it may crash.


## Комментарии

> **Примечание.** Начиная c Windows10, пользователю больше не потребуется добавлять приложения на экран блокировки, чтобы использовать фоновые задачи. Руководство по таким типам триггеров фоновых задач см. в разделе [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

> **Примечание.** Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). При разработке приложений для Windows8.x или Windows Phone8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

## Связанные разделы

* [Создание и регистрация фоновой задачи, которая запускается в одном процессе](create-and-register-a-singleprocess-background-task.md).
* [Создание и регистрация фоновой задачи, которая запускается в отдельном процессе](create-and-register-a-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях Магазина Windows (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)



<!--HONumber=Sep16_HO2-->


