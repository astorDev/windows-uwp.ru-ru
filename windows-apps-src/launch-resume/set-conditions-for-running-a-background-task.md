---
title: Задание условий выполнения фоновой задачи
description: Узнайте, как задать условия, которые управляют запуском выполнения фоновой задачи.
ms.assetid: 10ABAC9F-AA8C-41AC-A29D-871CD9AD9471
ms.date: 07/06/2018
ms.topic: article
keywords: Windows 10, UWP, фоновая задача
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
ms.openlocfilehash: 04f351a2eed5290e31a3f40c5421addf01422154
ms.sourcegitcommit: cc645386b996f6e59f1ee27583dcd4310f8fb2a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262785"
---
# <a name="set-conditions-for-running-a-background-task"></a>Задание условий выполнения фоновой задачи

**Важные API**

- [**SystemCondition**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemCondition)
- [**SystemConditionType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemConditionType)
- [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder)

Узнайте, как задать условия, которые управляют запуском выполнения фоновой задачи.

Иногда для успешного выполнения фоновых задач требуется соблюдение определенных условий. При регистрации фоновой задачи можно указать одно или несколько условий, определяемых типом [**SystemConditionType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemConditionType). Условие будет проверяться после срабатывания триггера. Фоновая задача затем помещается в очередь и выполняется только при соблюдении всех требуемых условий.

Настройка условий для фоновых задач продлевает время работы от батареи и сокращает нагрузку ЦП, так как препятствует запуску задач без необходимости. Например, если фоновая задача запускается по таймеру и требует подключения к Интернету, перед регистрацией задачи добавьте условие **InternetAvailable** в [**TaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder). Это помогает избежать ненужного использования системных ресурсов или заряда батареи, запуская выполнение фоновой задачи только при срабатывании таймера *и* наличии доступа к Интернету.

Можно также объединить несколько условий, вызвав **аддкондитион** несколько раз в одном [**таскбуилдер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder). Следите за тем, чтобы не добавить конфликтующие условия, например **UserPresent** и **UserNotPresent**.

## <a name="create-a-systemcondition-object"></a>Создание объекта SystemCondition

Для работы с этим разделом вы должны иметь связанную с приложением фоновую задачу, а ваше приложение должно содержать код, который создает объект [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) с именем **taskBuilder**.  Если сначала нужно создать фоновую задачу, выполните инструкции из раздела [Создание и регистрация фоновой задачи, выполняемой в процессе](create-and-register-an-inproc-background-task.md) или [Создание и регистрация фоновой задачи, выполняемой вне процесса](create-and-register-a-background-task.md).

Этот раздел применяется к фоновым задачам, которые выполняются вне процесса или в том же процессе, что и приложение переднего плана.

Прежде чем добавлять само условие, создайте объект [**SystemCondition**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemCondition) для представления условия, которое должно соблюдаться для запуска задачи. Задайте в конструкторе необходимое для выполнения условие с помощью значения перечисления [**SystemConditionType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemConditionType).

Следующий код создает объект [**SystemCondition**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemCondition), указывающий условие **InternetAvailable**:

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

Чтобы добавить условие, вызовите метод [**AddCondition**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskbuilder.addcondition) применительно к объекту [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) и передайте его объекту [**SystemCondition**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemCondition).

В следующем коде используется **taskBuilder** для добавления условия **InternetAvailable**.

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

Теперь вы можете зарегистрировать свою фоновую задачу с помощью метода [**Register**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskbuilder.register), при этом фоновая задача запускается только при соблюдении заданного условия.

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
> Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации недопустимы, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

## <a name="place-multiple-conditions-on-your-background-task"></a>Размещение в фоновой задаче нескольких условий

Чтобы добавить несколько условий, приложение многократно вызывает метод [**AddCondition**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskbuilder.addcondition) . Чтобы быть эффективными, такие вызовы должны предшествовать регистрации задачи.

> [!NOTE]
> Следите за тем, чтобы не добавлять конфликтующие условия в фоновую задачу.

В следующем фрагменте кода показано несколько условий в контексте создания и регистрации фоновой задачи.

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
> Выберите условия для фоновой задачи так, чтобы она выполнялась только при необходимости и не выполнялась. Обзор различных условий выполнения фоновых задач см. в разделе [**SystemConditionType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemConditionType).

## <a name="related-topics"></a>См. также

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
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях UWP (во время отладки)](https://msdn.microsoft.com/library/windows/apps/hh974425(v=vs.110).aspx)
