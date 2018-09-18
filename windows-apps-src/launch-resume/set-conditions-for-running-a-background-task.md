---
author: TylerMSFT
title: Задание условий выполнения фоновой задачи
description: Узнайте, как задать условия, которые управляют запуском выполнения фоновой задачи.
ms.assetid: 10ABAC9F-AA8C-41AC-A29D-871CD9AD9471
ms.author: twhitney
ms.date: 07/06/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, фоновой задачи
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
ms.openlocfilehash: 556a787eb1e92e4c8adb7457235afb45c02df2dc
ms.sourcegitcommit: f5321b525034e2b3af202709e9b942ad5557e193
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2018
ms.locfileid: "4017118"
---
# <a name="set-conditions-for-running-a-background-task"></a>Задание условий выполнения фоновой задачи

**Важные API**

- [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834)
- [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835)
- [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)

Узнайте, как задать условия, которые управляют запуском выполнения фоновой задачи.

В некоторых случаях фоновых задач требуется соблюдение определенных условий должны быть выполнены для успешного выполнения фоновой задачи. При регистрации фоновой задачи можно указать одно или несколько условий, определяемых типом [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835). Условия проверяются после триггера. Фоновая задача будет затем очередь, но она не будет выполняться при соблюдении всех требуемых условий.

Настройка условий для фоновых задач продлевает время работы от батареи и ЦП, так как задач без необходимости запускать. Например, если фоновая задача запускается по таймеру и требует подключения к Интернету, перед регистрацией задачи добавьте условие **InternetAvailable** в [**TaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768). Это помогает избежать ненужного использования системных ресурсов или заряда батареи, запуская выполнение фоновой задачи только при срабатывании таймера *и* наличии доступа к Интернету.

Это также можно скомбинировать несколько условий, вызвав **AddCondition** несколько раз в одном [**TaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768). Следите за тем, чтобы не добавить конфликтующие условия, например **UserPresent** и **UserNotPresent**.

## <a name="create-a-systemcondition-object"></a>Создание объекта SystemCondition

Для работы с этим разделом вы должны иметь связанную с приложением фоновую задачу, а ваше приложение должно содержать код, который создает объект [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) с именем **taskBuilder**.  Если сначала нужно создать фоновую задачу, выполните инструкции из раздела [Создание и регистрация фоновой задачи, выполняемой в процессе](create-and-register-an-inproc-background-task.md) или [Создание и регистрация фоновой задачи, выполняемой вне процесса](create-and-register-a-background-task.md).

Этот раздел применяется к фоновым задачам, которые выполняются вне процесса или в том же процессе, что и приложение переднего плана.

Прежде чем добавлять само условие, создание объекта [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834) для представления условия, которое должно быть в силу для фоновой задачи для выполнения. В конструкторе укажите условия, которое необходимо обеспечить выполнение со значением перечисления [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835) .

Следующий код создает объект [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834) , который задает **InternetAvailable** условие:

```csharp
SystemCondition internetCondition = new SystemCondition(SystemConditionType.InternetAvailable);
```

```cppwinrt
Windows::ApplicationModel::Background::SystemCondition internetCondition{
    Windows::ApplicationModel::Background::SystemConditionType::InternetAvailable };
```

```cpp
SystemCondition ^ internetCondition = ref new SystemCondition(SystemConditionType::InternetAvailable);
```

## <a name="add-the-systemcondition-object-to-your-background-task"></a>Добавление объекта SystemCondition в фоновую задачу

Чтобы добавить условие, вызовите метод [**AddCondition**](https://msdn.microsoft.com/library/windows/apps/br224769) применительно к объекту [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) и передайте его объекту [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834).

Следующий код использует **taskBuilder** , чтобы добавить условие **InternetAvailable** .

```csharp
taskBuilder.AddCondition(internetCondition);
```

```cppwinrt
taskBuilder.AddCondition(internetCondition);
```

```cpp
taskBuilder->AddCondition(internetCondition);
```

## <a name="register-your-background-task"></a>Регистрация фоновой задачи

Теперь вы можете зарегистрировать фоновую задачу с помощью метода [**регистрации**](https://msdn.microsoft.com/library/windows/apps/br224772) , и фоновая задача не будет запущена до заданного условия.

В следующем коде регистрируется задача и сохраняется полученный в результате объект BackgroundTaskRegistration:

```csharp
BackgroundTaskRegistration task = taskBuilder.Register();
```

```cppwinrt
Windows::ApplicationModel::Background::BackgroundTaskRegistration task{ recurringTaskBuilder.Register() };
```

```cpp
BackgroundTaskRegistration ^ task = taskBuilder->Register();
```

> [!NOTE]
> Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров.

Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем— метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Подробнее см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

> [!NOTE]
> Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

## <a name="place-multiple-conditions-on-your-background-task"></a>Размещение в фоновой задаче нескольких условий

Чтобы добавить несколько условий, приложение многократно вызывает метод [**AddCondition**](https://msdn.microsoft.com/library/windows/apps/br224769) . Чтобы быть эффективными, такие вызовы должны предшествовать регистрации задачи.

> [!NOTE]
> Будьте внимательны, чтобы не добавлять в фоновую задачу конфликтующие условия.

В следующем фрагменте показано несколько условий в контексте создания и регистрации фоновой задачи.

```csharp
// Set up the background task.
TimeTrigger hourlyTrigger = new TimeTrigger(60, false);

var recurringTaskBuilder = new BackgroundTaskBuilder();

recurringTaskBuilder.Name           = "Hourly background task";
recurringTaskBuilder.TaskEntryPoint = "Tasks.ExampleBackgroundTaskClass";
recurringTaskBuilder.SetTrigger(hourlyTrigger);

// Begin adding conditions.
SystemCondition userCondition     = new SystemCondition(SystemConditionType.UserPresent);
SystemCondition internetCondition = new SystemCondition(SystemConditionType.InternetAvailable);

recurringTaskBuilder.AddCondition(userCondition);
recurringTaskBuilder.AddCondition(internetCondition);

// Done adding conditions, now register the background task.

BackgroundTaskRegistration task = recurringTaskBuilder.Register();
```

```cppwinrt
// Set up the background task.
Windows::ApplicationModel::Background::TimeTrigger hourlyTrigger{ 60, false };

Windows::ApplicationModel::Background::BackgroundTaskBuilder recurringTaskBuilder;

recurringTaskBuilder.Name(L"Hourly background task");
recurringTaskBuilder.TaskEntryPoint(L"Tasks.ExampleBackgroundTaskClass");
recurringTaskBuilder.SetTrigger(hourlyTrigger);

// Begin adding conditions.
Windows::ApplicationModel::Background::SystemCondition userCondition{
    Windows::ApplicationModel::Background::SystemConditionType::UserPresent };
Windows::ApplicationModel::Background::SystemCondition internetCondition{
    Windows::ApplicationModel::Background::SystemConditionType::InternetAvailable };

recurringTaskBuilder.AddCondition(userCondition);
recurringTaskBuilder.AddCondition(internetCondition);

// Done adding conditions, now register the background task.
Windows::ApplicationModel::Background::BackgroundTaskRegistration task{ recurringTaskBuilder.Register() };
```

```cpp
// Set up the background task.
TimeTrigger ^ hourlyTrigger = ref new TimeTrigger(60, false);

auto recurringTaskBuilder = ref new BackgroundTaskBuilder();

recurringTaskBuilder->Name           = "Hourly background task";
recurringTaskBuilder->TaskEntryPoint = "Tasks.ExampleBackgroundTaskClass";
recurringTaskBuilder->SetTrigger(hourlyTrigger);

// Begin adding conditions.
SystemCondition ^ userCondition     = ref new SystemCondition(SystemConditionType::UserPresent);
SystemCondition ^ internetCondition = ref new SystemCondition(SystemConditionType::InternetAvailable);

recurringTaskBuilder->AddCondition(userCondition);
recurringTaskBuilder->AddCondition(internetCondition);

// Done adding conditions, now register the background task.
BackgroundTaskRegistration ^ task = recurringTaskBuilder->Register();
```

## <a name="remarks"></a>Комментарии

> [!NOTE]
> Выберите условия для фоновой задачи, чтобы его только выполняется, когда он необходим и не выполнялась, когда он не следует. Обзор различных условий выполнения фоновых задач см. в разделе [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

## <a name="related-topics"></a>Еще по теме

* [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md)
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
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях UWP (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)
