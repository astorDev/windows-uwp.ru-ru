---
title: Задание условий выполнения фоновой задачи
description: Узнайте, как задать условия, которые управляют запуском выполнения фоновой задачи.
ms.assetid: 10ABAC9F-AA8C-41AC-A29D-871CD9AD9471
ms.date: 07/06/2018
ms.topic: article
keywords: Windows 10, uwp, фоновую задачу
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
ms.openlocfilehash: 8740829ab95b804afba564110f38116f2c90b416
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57650939"
---
# <a name="set-conditions-for-running-a-background-task"></a>Задание условий выполнения фоновой задачи

**Важные API**

- [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834)
- [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835)
- [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)

Узнайте, как задать условия, которые управляют запуском выполнения фоновой задачи.

Иногда для успешного выполнения фоновых задач требуется соблюдение определенных условий. При регистрации фоновой задачи можно указать одно или несколько условий, определяемых типом [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835). Условие будет проверяться после срабатывания триггера. Фоновая задача затем помещается в очередь и выполняется только при соблюдении всех требуемых условий.

Настройка условий для фоновых задач продлевает время работы от батареи и сокращает нагрузку ЦП, так как препятствует запуску задач без необходимости. Например, если фоновая задача запускается по таймеру и требует подключения к Интернету, перед регистрацией задачи добавьте условие **InternetAvailable** в [**TaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768). Это помогает избежать ненужного использования системных ресурсов или заряда батареи, запуская выполнение фоновой задачи только при срабатывании таймера *и* наличии доступа к Интернету.

Также можно скомбинировать несколько условий, вызвав **AddCondition** несколько раз в одном [**TaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768). Следите за тем, чтобы не добавить конфликтующие условия, например **UserPresent** и **UserNotPresent**.

## <a name="create-a-systemcondition-object"></a>Создание объекта SystemCondition

Для работы с этим разделом вы должны иметь связанную с приложением фоновую задачу, а ваше приложение должно содержать код, который создает объект [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) с именем **taskBuilder**.  Если сначала нужно создать фоновую задачу, выполните инструкции из раздела [Создание и регистрация фоновой задачи, выполняемой в процессе](create-and-register-an-inproc-background-task.md) или [Создание и регистрация фоновой задачи, выполняемой вне процесса](create-and-register-a-background-task.md).

Этот раздел применяется к фоновым задачам, которые выполняются вне процесса или в том же процессе, что и приложение переднего плана.

Прежде чем добавлять само условие, создайте объект [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834) для представления условия, которое должно соблюдаться для запуска задачи. Задайте в конструкторе необходимое для выполнения условие с помощью значения перечисления [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

Следующий код создает объект [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834), указывающий условие **InternetAvailable**:

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

Теперь вы можете зарегистрировать свою фоновую задачу с помощью метода [**Register**](https://msdn.microsoft.com/library/windows/apps/br224772), при этом фоновая задача запускается только при соблюдении заданного условия.

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

Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем — метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Дополнительные сведения см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

> [!NOTE]
> Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации недопустимы, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

## <a name="place-multiple-conditions-on-your-background-task"></a>Размещение в фоновой задаче нескольких условий

Чтобы добавить несколько условий, приложение многократно вызывает метод [**AddCondition**](https://msdn.microsoft.com/library/windows/apps/br224769) . Чтобы быть эффективными, такие вызовы должны предшествовать регистрации задачи.

> [!NOTE]
> Постарайтесь не добавить конфликтующие условия в фоновом режиме.

В следующем фрагменте показано несколько условий в контексте создания и регистрации в фоновом режиме.

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

## <a name="remarks"></a>Замечания

> [!NOTE]
> Выберите условия для фоновой задачи, чтобы ее можно запускать только когда он необходим и не запускается, когда его не следует. Обзор различных условий выполнения фоновых задач см. в разделе [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

## <a name="related-topics"></a>Статьи по теме

* [Создание и регистрация вне процесса фоновой задачи](create-and-register-a-background-task.md)
* [Создание и регистрация в процесс фоновой задачи](create-and-register-an-inproc-background-task.md)
* [Объявите фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Обработка отменена фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживать ход выполнения задач в фоновом режиме и завершения](monitor-background-task-progress-and-completion.md)
* [Зарегистрировать фоновую задачу](register-a-background-task.md)
* [Реакция на системные события с фоновыми задачами](respond-to-system-events-with-background-tasks.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Выполнять фоновую задачу по таймеру](run-a-background-task-on-a-timer-.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладить фоновую задачу](debug-a-background-task.md)
* [Активация приостановки, возобновления и фоновых событий для приложений универсальной платформы Windows (при отладке)](https://go.microsoft.com/fwlink/p/?linkid=254345)
