---
author: TylerMSFT
title: "Задание условий выполнения фоновой задачи"
description: "Узнайте, как задать условия, которые управляют запуском выполнения фоновой задачи."
ms.assetid: 10ABAC9F-AA8C-41AC-A29D-871CD9AD9471
translationtype: Human Translation
ms.sourcegitcommit: 7d1c160f8b725cd848bf8357325c6ca284b632ae
ms.openlocfilehash: c22fed27b77f3287dd11a05c32405fe18521af65

---

# Задание условий выполнения фоновой задачи

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834)
-   [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835)
-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)

Узнайте, как задать условия, которые управляют запуском выполнения фоновой задачи.

Иногда для успешного выполнения фоновых задач требуется соблюдение определенных условий в дополнение к событию, которое инициирует выполнение задачи. При регистрации фоновой задачи можно указать одно или несколько условий, определяемых типом [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835). Условия проверяются после срабатывания триггера. Фоновая задача помещается в очередь и выполняется только при соблюдении всех требуемых условий.

Настройка условий для фоновых задач продлевает время работы батареи и сокращает время использования ЦП, так как препятствует запуску задач без необходимости. Например, если фоновая задача запускается по таймеру и требует подключения к Интернету, перед регистрацией задачи добавьте условие **InternetAvailable** в [**TaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768). Это помогает избежать ненужного использования системных ресурсов или заряда батареи, запуская выполнение фоновой задачи только при срабатывании таймера *и* наличии доступа к Интернету.

Можно скомбинировать несколько условий, вызвав AddCondition несколько раз в одном конструкторе [**TaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768). Следите за тем, чтобы не добавить конфликтующие условия, например **UserPresent** и **UserNotPresent**.

## Создание объекта SystemCondition

Для работы с этим разделом вы должны иметь связанную с приложением фоновую задачу, а ваше приложение должно содержать код, который создает объект [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) с именем **taskBuilder**.  Если сначала нужно создать фоновую задачу, выполните инструкции из раздела [Создание и регистрация фоновой задачи, выполняемой в процессе](create-and-register-an-inproc-background-task.md) или [Создание и регистрация фоновой задачи, выполняемой вне процесса](create-and-register-an-outofproc-background-task.md).

Этот раздел применяется к фоновым задачам, которые выполняются вне процесса или в том же процессе, что и приложение переднего плана.

Прежде чем добавлять само условие, создайте объект [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834) для представления условия, которое должно соблюдаться для запуска задачи. Задайте в конструкторе необходимое для выполнения условие, указав значение перечисления [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

Следующий код создает объект [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834), указывающий доступность Интернета в качестве условного требования:

> [!div class="tabbedCodeSnippets"]
> ```cs
> SystemCondition internetCondition = new SystemCondition(SystemConditionType.InternetAvailable);
> ```
> ```cpp
> SystemCondition ^ internetCondition = ref new SystemCondition(SystemConditionType::InternetAvailable);
> ```

## Добавление объекта SystemCondition в фоновую задачу


Чтобы добавить условие, вызовите метод [**AddCondition**](https://msdn.microsoft.com/library/windows/apps/br224769) применительно к объекту [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) и передайте его объекту [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834).

Следующий код регистрирует условие фоновой задачи InternetAvailable с помощью TaskBuilder:

> [!div class="tabbedCodeSnippets"]
> ```cs
> taskBuilder.AddCondition(internetCondition);
> ```
> ```cpp
> taskBuilder->AddCondition(internetCondition);
> ```

## Регистрация фоновой задачи


Теперь вы можете зарегистрировать свою фоновую задачу с помощью метода [**Register**](https://msdn.microsoft.com/library/windows/apps/br224772), при этом задача запускается только при соблюдении заданного условия.

В следующем коде регистрируется задача и сохраняется полученный в результате объект BackgroundTaskRegistration:

> [!div class="tabbedCodeSnippets"]
> ```cs
> BackgroundTaskRegistration task = taskBuilder.Register();
> ```
> ```cpp
> BackgroundTaskRegistration ^ task = taskBuilder->Register();
> ```

> **Примечание**  Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров.

Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем— метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Подробнее см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

> **Примечание.**  Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

## Размещение в фоновой задаче нескольких условий

Чтобы добавить несколько условий, приложение многократно вызывает метод [**AddCondition**](https://msdn.microsoft.com/library/windows/apps/br224769) . Чтобы быть эффективными, такие вызовы должны предшествовать регистрации задачи.

> **Примечание.** Следите за тем, чтобы не добавлять в фоновую задачу конфликтующие условия.
 

Следующий фрагмент кода демонстрирует несколько условий в контексте создания и регистрации фоновой задачи:

> [!div class="tabbedCodeSnippets"]
```cs
> //
> // Set up the background task.
> //
>
> TimeTrigger hourlyTrigger = new TimeTrigger(60, false);
>
> var recurringTaskBuilder = new BackgroundTaskBuilder();
>
> recurringTaskBuilder.Name           = "Hourly background task";
> recurringTaskBuilder.TaskEntryPoint = "Tasks.ExampleBackgroundTaskClass";
> recurringTaskBuilder.SetTrigger(hourlyTrigger);
>
> //
> // Begin adding conditions.
> //
>
> SystemCondition userCondition     = new SystemCondition(SystemConditionType.UserPresent);
> SystemCondition internetCondition = new SystemCondition(SystemConditionType.InternetAvailable);
>
> recurringTaskBuilder.AddCondition(userCondition);
> recurringTaskBuilder.AddCondition(internetCondition);
>
> //
> // Done adding conditions, now register the background task.
> //
>
> BackgroundTaskRegistration task = recurringTaskBuilder.Register();
```
```cpp
> //
> // Set up the background task.
> //
>
> TimeTrigger ^ hourlyTrigger = ref new TimeTrigger(60, false);
>
> auto recurringTaskBuilder = ref new BackgroundTaskBuilder();
>
> recurringTaskBuilder->Name           = "Hourly background task";
> recurringTaskBuilder->TaskEntryPoint = "Tasks.ExampleBackgroundTaskClass";
> recurringTaskBuilder->SetTrigger(hourlyTrigger);
>
> //
> // Begin adding conditions.
> //
>
> SystemCondition ^ userCondition     = ref new SystemCondition(SystemConditionType::UserPresent);
> SystemCondition ^ internetCondition = ref new SystemCondition(SystemConditionType::InternetAvailable);
>
> recurringTaskBuilder->AddCondition(userCondition);
> recurringTaskBuilder->AddCondition(internetCondition);
>
> //
> // Done adding conditions, now register the background task.
> //
>
> BackgroundTaskRegistration ^ task = recurringTaskBuilder->Register();
```

## Комментарии


> **Примечание.** Выберите такие условия для фоновой задачи, чтобы она запускалась только при необходимости и никогда не выполнялась, когда не будет работать. Обзор различных условий выполнения фоновых задач см. в разделе [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

> **Примечание.** Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). Если вы разрабатываете приложения для Windows8.x или Windows Phone8.x, обратитесь к [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

## Связанные статьи

****

* [Создание и регистрация фоновой задачи, выполняемой вне процесса](create-and-register-an-outofproc-background-task.md)
* [Создание и регистрация фоновой задачи, выполняемой внутри процесса](create-and-register-an-inproc-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях Магазина Windows (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)

 

 



<!--HONumber=Nov16_HO1-->


