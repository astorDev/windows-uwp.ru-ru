---
author: TylerMSFT
title: "Использование триггера обслуживания"
description: "Узнайте, как использовать класс MaintenanceTrigger для выполнения облегченного кода в фоновом режиме, когда устройство подключено к сети."
ms.assetid: 727D9D84-6C1D-4DF3-B3B0-2204EA4D76DD
ms.sourcegitcommit: 39a012976ee877d8834b63def04e39d847036132
ms.openlocfilehash: 0da08ba5431f4d5c56d06657d3d6123a67ba5079

---

# Использование триггера обслуживания


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517)
-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
-   [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834)

Узнайте, как использовать класс [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517) для выполнения облегченного кода в фоновом режиме, когда устройство подключено к сети.

## Создание объекта триггера обслуживания


Для изучения этого примера необходим облегченный код, который можно выполнить в фоновом режиме для усовершенствования вашего приложения, когда устройство подключено к сети. В этом разделе рассматривается класс [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517), который похож на [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839). Подробнее о создании класса фоновой задачи описывается в разделе [Создание и регистрация фоновой задачи](create-and-register-a-background-task.md).

Создайте новый объект [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843). Второй параметр — *OneShot* — указывает, будет ли задача обслуживания выполняться однократно или периодически. Если *OneShot* имеет значение True, первый параметр (*FreshnessTime*) задает число минут ожидания перед планированием фоновой задачи. Если *OneShot* имеет значение False, частоту выполнения фоновой задачи определяет *FreshnessTime*.

> **Примечание.**  Если параметру *FreshnessTime* задано значение меньше 15 минут, при попытке зарегистрировать фоновую задачу появляется исключение.

 

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

## [!div class="tabbedCodeSnippets"]

-   Добавление условия (необязательно) При необходимости можно также создать условие фоновой задачи, чтобы контролировать время запуска задачи.

    Условие не даст запустить фоновую задачу, пока оно не будет выполнено. Подробнее об этом можно узнать в разделе [Задание условий для выполнения фоновой задачи](set-conditions-for-running-a-background-task.md). В этом примере устанавливается условие **InternetAvailable**, так что обслуживание выполняется, когда доступно интернет-соединение (или когда оно становится доступным).

    Список возможных условий фоновых задач см. в разделе [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

    > [!div class="tabbedCodeSnippets"]
    > ```cs
    > SystemCondition exampleCondition = new SystemCondition(SystemConditionType.InternetAvailable);
    > ```
    > ```cpp
    > SystemCondition ^ exampleCondition = ref new SystemCondition(SystemConditionType::InternetAvailable);
    > ```

## Следующий код добавляет условие для построителя задачи обслуживания:


-   [!div class="tabbedCodeSnippets"] Регистрация фоновой задачи

    Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи.

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

    > Узнать больше о регистрации фоновых задач можно в разделе [Регистрация фоновой задачи](register-a-background-task.md). Следующий код регистрирует задачу обслуживания: [!div class="tabbedCodeSnippets"] **Примечание.**  Если устройству не хватает памяти, фоновые задачи могут быть завершены для всех семейств устройств (за исключением настольных компьютеров).

    > Если исключение "Недостаточно памяти" не отображается или приложение не обрабатывает его, то фоновая задача будет завершена без предупреждения и без вызова события OnCanceled.

    Это позволяет не прерывать работу пользователя с активным приложением. Ваша фоновая задача должна поддерживать этот сценарий.

    > **Примечание**  Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров. Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем — метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Подробнее см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).


> **Примечание.**  Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка.

## Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.


****

* [**Примечание.**  Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP).](create-and-register-a-background-task.md)
* [В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).](declare-background-tasks-in-the-application-manifest.md)
* [Связанные разделы](handle-a-cancelled-background-task.md)
* [Создание и регистрация фоновой задачи](monitor-background-task-progress-and-completion.md)
* [Объявление фоновых задач в манифесте приложения](register-a-background-task.md)
* [Обработка отмененной фоновой задачи](respond-to-system-events-with-background-tasks.md)
* [Отслеживание хода выполнения и завершения фоновых задач](set-conditions-for-running-a-background-task.md)
* [Регистрация фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Реагирование на системные события с помощью фоновых задач](run-a-background-task-on-a-timer-.md)
* [Задание условий выполнения фоновой задачи](guidelines-for-background-tasks.md)

****

* [Обновление живой плитки из фоновой задачи](debug-a-background-task.md)
* [Запуск фоновой задачи по таймеру](http://go.microsoft.com/fwlink/p/?linkid=254345)

 

 



<!--HONumber=Jun16_HO5-->


