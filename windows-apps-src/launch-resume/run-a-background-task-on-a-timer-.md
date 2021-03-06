---
title: Запуск фоновой задачи по таймеру
description: Узнайте, как запланировать однократное выполнение фоновой задачи или периодически запускать фоновую задачу.
ms.assetid: 0B7F0BFF-535A-471E-AC87-783C740A61E9
ms.date: 07/06/2018
ms.topic: article
keywords: Windows 10, UWP, фоновая задача
ms.localizationpriority: medium
ms.openlocfilehash: b0d3c9401ff71475e379b2959a1f0cdc03fe8d8b
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260436"
---
# <a name="run-a-background-task-on-a-timer"></a>Запуск фоновой задачи по таймеру

Узнайте, как запланировать однократное выполнение фоновой задачи или периодически запускать фоновую задачу с помощью элемента [**TimeTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.TimeTrigger).

См. раздел **Scenario4** в статье [Пример фоновой активации](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundActivation), чтобы изучить пример реализации фоновой задачи, активируемой временем, описанный в этом разделе.

В разделе предполагается, что у вас есть фоновая задача, которая должна выполняться периодически или в определенное время. Если у вас еще нет в фоновом режиме, это пример фоновой задачи в [BackgroundActivity.cs](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/BackgroundActivation/cs/BackgroundActivity.cs). Или же выполните шаги из раздела [Создание и регистрация фоновой задачи, выполняемой в процессе](create-and-register-an-inproc-background-task.md) или [Создание и регистрация фоновой задачи, выполняемой вне процесса](create-and-register-a-background-task.md) для создания задачи.

## <a name="create-a-time-trigger"></a>Создание триггера времени

Создайте новый [**TimeTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.TimeTrigger). Второй параметр *OneShot* указывает, будет ли фоновая задача выполняться однократно или периодически. Если *OneShot* имеет значение True, первый параметр (*FreshnessTime*) задает число минут ожидания перед планированием фоновой задачи. Если *OneShot* имеет значение False, частоту выполнения фоновой задачи определяет *FreshnessTime*.

Встроенный таймер для приложений UWP, предназначенных для настольных компьютеров или мобильных устройств, выполняет фоновые задачи с интервалом в 15 минут. (Таймер запускается с интервалом 15 минут, чтобы системе нужно было пробуждаться каждые 15 минут для пробуждения приложений, которые запросили TimerTriggers, который помогают сэкономить электроэнергию.)

- Если параметру *FreshnessTime* задано значение 15 минут, а параметру *OneShot* — значение true, задача будет запланирована для однократного выполнения через 15–30 минут с момента ее регистрации. Если задано значение "25 минут" и *OneShot* имеет значение true, задача будет запланирована для однократного выполнения через 25–40 минут с момента ее регистрации.

- Если параметру *FreshnessTime* задано значение 15 минут, а параметру *OneShot* — false, задача будет запланирована для выполнения каждые 15 минут через 15–30 минут с момента ее регистрации. Если задано значение "n минут" и *OneShot* имеет значение false, задача будет запланирована для выполнения каждые n минут, начиная с промежутка между n и n + 15 после регистрации.

> [!NOTE]
> Если *фрешнесстиме* имеет значение менее 15 минут, при попытке зарегистрировать фоновую задачу выдается исключение.

Например, этот триггер приведет к запуску фоновой задачи один раз в час.

```cs
TimeTrigger hourlyTrigger = new TimeTrigger(60, false);
```

```cppwinrt
Windows::ApplicationModel::Background::TimeTrigger hourlyTrigger{ 60, false };
```

```cpp
TimeTrigger ^ hourlyTrigger = ref new TimeTrigger(60, false);
```

## <a name="optional-add-a-condition"></a>Добавление условия (необязательно)

Можно создать условие фоновой задачи, чтобы контролировать время запуска задачи. Условие запрещает фоновую задачу, пока не будет выполнено. См. также: [Задание условий для выполнения фоновой задачи](set-conditions-for-running-a-background-task.md).

В этом примере условию присвоено значение **UserPresent**, поэтому после срабатывания триггера задача выполняется, только если пользователь активен. Список возможных условий см. в статье [**SystemConditionType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemConditionType).

```cs
SystemCondition userCondition = new SystemCondition(SystemConditionType.UserPresent);
```

```cppwinrt
Windows::ApplicationModel::Background::SystemCondition userCondition{
    Windows::ApplicationModel::Background::SystemConditionType::UserPresent };
```

```cpp
SystemCondition ^ userCondition = ref new SystemCondition(SystemConditionType::UserPresent);
```

Более углубленно с условиями и типами фоновых триггеров можно ознакомиться в разделе [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

##  <a name="call-requestaccessasync"></a>Вызов RequestAccessAsync()

Перед регистрацией **ApplicationTrigger** фоновой задачи, вызовите [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync) для определения уровня фоновой активности позволяет пользователю, так как пользователь отключил фоновой активности для вашего приложения. В разделе [оптимизировать фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о том, как пользователи могут управлять параметрами для фоновой активности.

```cs
var requestStatus = await Windows.ApplicationModel.Background.BackgroundExecutionManager.RequestAccessAsync();
if (requestStatus != BackgroundAccessStatus.AlwaysAllowed)
{
    // Depending on the value of requestStatus, provide an appropriate response
    // such as notifying the user which functionality won't work as expected
}
```

## <a name="register-the-background-task"></a>Регистрация фоновой задачи

Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Дополнительные сведения о регистрации фоновых задач, а также просмотреть определение **RegisterBackgroundTask()** метод в примере кода ниже в разделе [регистрация фоновой задачи](register-a-background-task.md).

> [!IMPORTANT]
> Для фоновых задач, выполняемых в том же процессе, что и приложение, не устанавливайте `entryPoint`. Для фоновых задач, выполняемых в отдельном процессе из приложения, задайте `entryPoint` как пространство имен, "." и имя класса, который содержит реализацию фоновой задачи.

```cs
string entryPoint = "Tasks.ExampleBackgroundTaskClass";
string taskName   = "Example hourly background task";

BackgroundTaskRegistration task = RegisterBackgroundTask(entryPoint, taskName, hourlyTrigger, userCondition);
```

```cppwinrt
std::wstring entryPoint{ L"Tasks.ExampleBackgroundTaskClass" };
std::wstring taskName{ L"Example hourly background task" };

Windows::ApplicationModel::Background::BackgroundTaskRegistration task{
    RegisterBackgroundTask(entryPoint, taskName, hourlyTrigger, userCondition) };
```

```cpp
String ^ entryPoint = "Tasks.ExampleBackgroundTaskClass";
String ^ taskName   = "Example hourly background task";

BackgroundTaskRegistration ^ task = RegisterBackgroundTask(entryPoint, taskName, hourlyTrigger, userCondition);
```

Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации недопустимы, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

## <a name="manage-resources-for-your-background-task"></a>Управление ресурсами для фоновой задачи

Используйте метод [BackgroundExecutionManager.RequestAccessAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager), чтобы определить, принял ли пользователь решение ограничить фоновую активность вашего приложения. Следите за расходом заряда батареи и запускайте приложения в фоновом режиме только тогда, когда необходимо завершить интересующее пользователя действие. В разделе [оптимизировать фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о том, как пользователи могут управлять параметрами для фоновой активности.

- Память: Настройка использования памяти и энергию ваше приложение является ключом к гарантируя, что операционная система будет разрешать запуск фоновой задачи. Используйте [API-интерфейсы управления памятью](https://docs.microsoft.com/uwp/api/windows.system.memorymanager), чтобы узнать, сколько памяти потребляет ваше фоновое задание. Чем больше памяти потребляет ваше фоновое задание, тем сложнее операционной системе поддерживать его выполнение, когда другое приложение работает на переднем плане. Пользователь полностью контролирует все фоновые действия, которые может выполнять ваше приложение, и видит, как ваше приложение влияет на расход заряда батареи.  
- Время ЦП: Фоновые задачи ограничены физическим временем использования, которое они получают в зависимости от типа триггера.

Сведения об ограничениях ресурсов для фоновых задач см. в статье [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

## <a name="remarks"></a>Примечания

Начиная с Windows 10, пользователю больше не нужно добавлять приложение на экран блокировки, чтобы использовать фоновые задачи.

Фоновая задача будет запускаться только с помощью **TimeTrigger**, если вызван метод [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync).

## <a name="related-topics"></a>См. также

* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Пример кода фоновой задачи](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundTask)
* [Создание и регистрация фоновой задачи, выполняемой внутри процесса](create-and-register-an-inproc-background-task.md)
* [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Освобождение памяти при переключении приложения в фоновый режим](reduce-memory-usage.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Как активировать события приостановки, возобновления и фоновых событий в приложениях UWP (при отладке)](https://msdn.microsoft.com/library/windows/apps/hh974425(v=vs.110).aspx)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Задержка приостановки приложения с помощью расширенного сеанса выполнения](run-minimized-with-extended-execution.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
