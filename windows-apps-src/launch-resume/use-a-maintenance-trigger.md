---
author: TylerMSFT
title: "Использование триггера обслуживания"
description: "Узнайте, как использовать класс MaintenanceTrigger для выполнения облегченного кода в фоновом режиме, когда устройство подключено к сети питания."
ms.assetid: 727D9D84-6C1D-4DF3-B3B0-2204EA4D76DD
ms.author: twhitney
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 174fcd8c7413b502fe2bc2476f5688a73984d7aa
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="use-a-maintenance-trigger"></a>Использование триггера обслуживания

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517)
-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
-   [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834)

Узнайте, как использовать класс [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517) для выполнения облегченного кода в фоновом режиме, когда устройство подключено к сети.

## <a name="create-a-maintenance-trigger-object"></a>Создание объекта триггера обслуживания

Для изучения этого примера необходим облегченный код, который можно выполнить в фоновом режиме для усовершенствования вашего приложения, когда устройство подключено к сети. В этом разделе рассматривается класс [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517), который похож на [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839).

Подробнее о написании класса фоновой задачи см. в статьях [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md) и [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md).

Создайте новый объект [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843). Второй параметр— *OneShot*— указывает, однократно или периодически будет выполняться задача обслуживания. Если *OneShot* имеет значение True, первый параметр (*FreshnessTime*) задает число минут ожидания перед планированием фоновой задачи. Если *OneShot* имеет значение False, частоту выполнения фоновой задачи определяет *FreshnessTime*.

> **Примечание.**  Если параметру *FreshnessTime* задано значение меньше 15минут, при попытке зарегистрировать фоновую задачу появляется исключение.

Этот пример кода создает триггер, запускаемый один раз в час:

> [!div class="tabbedCodeSnippets"]
> ```cs
> uint waitIntervalMinutes = 60;
>
> MaintenanceTrigger taskTrigger = new MaintenanceTrigger(waitIntervalMinutes, false);
> ```
> ```cpp
> unsigned int waitIntervalMinutes = 60;
>
> MaintenanceTrigger ^ taskTrigger = ref new MaintenanceTrigger(waitIntervalMinutes, false);
> ```

## <a name="optional-add-a-condition"></a>Добавление условия (необязательно)

-   При необходимости можно также создать условие фоновой задачи, чтобы контролировать время запуска задачи. Условие не даст запустить фоновую задачу, пока оно не будет выполнено. Подробнее об этом можно узнать в разделе [Задание условий для выполнения фоновой задачи](set-conditions-for-running-a-background-task.md).

В этом примере устанавливается условие **InternetAvailable**, так что обслуживание выполняется, когда доступно интернет-соединение (или когда оно становится доступным). Список возможных условий фоновых задач см. в разделе [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

Следующий код добавляет условие для построителя задачи обслуживания:

> [!div class="tabbedCodeSnippets"]
> ```cs
> SystemCondition exampleCondition = new SystemCondition(SystemConditionType.InternetAvailable);
> ```
> ```cpp
> SystemCondition ^ exampleCondition = ref new SystemCondition(SystemConditionType::InternetAvailable);
> ```

## <a name="register-the-background-task"></a>Регистрация фоновой задачи

-   Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Узнать больше о регистрации фоновых задач можно в разделе [Регистрация фоновой задачи](register-a-background-task.md).

    Следующий код регистрирует задачу обслуживания: Обратите внимание, что в нем подразумевается, что фоновая задача выполняется в отдельном от приложения процессе, так как задается `entryPoint`. Если фоновая задача выполняется в том же процессе, что и приложение, `entryPoint` не задается.

    > [!div class="tabbedCodeSnippets"]
    > ```cs
    > string entryPoint = "Tasks.ExampleBackgroundTaskClass";
    > string taskName   = "Maintenance background task example";
    >
    > BackgroundTaskRegistration task = RegisterBackgroundTask(entryPoint, taskName, taskTrigger, exampleCondition);
    > ```
    > ```cpp
    > String ^ entryPoint = "Tasks.ExampleBackgroundTaskClass";
    > String ^ taskName   = "Maintenance background task example";
    >
    > BackgroundTaskRegistration ^ task = RegisterBackgroundTask(entryPoint, taskName, taskTrigger, exampleCondition);
    > ```

    > **Примечание.** Если устройству не хватает памяти, фоновые задачи могут быть завершены для всех семейств устройств (за исключением настольных компьютеров). Если исключение "Недостаточно памяти" не отображается или приложение не обрабатывает его, то фоновая задача будет завершена без предупреждения и без вызова события OnCanceled. Это позволяет не прерывать работу пользователя с активным приложением. Ваша фоновая задача должна поддерживать этот сценарий.

    > **Примечание**  Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров.

    Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления приложения, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем— метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Подробнее см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

    > **Примечание.**  Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.


> **Примечание.** Эта статья адресована разработчикам приложений для Windows10 на базе универсальной платформы Windows (UWP). В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

## <a name="related-topics"></a>Статьи по теме

****

* [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md)
* [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях Магазина Windows (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)
