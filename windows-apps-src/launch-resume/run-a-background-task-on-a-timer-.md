---
author: TylerMSFT
title: Запуск фоновой задачи по таймеру
description: Узнайте, как запланировать однократное выполнение фоновой задачи или периодически запускать фоновую задачу.
ms.assetid: 0B7F0BFF-535A-471E-AC87-783C740A61E9
ms.author: twhitney
ms.date: 07/06/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, фоновые задачи
ms.localizationpriority: medium
ms.openlocfilehash: 25e3c76ae09ed6835f89f0d98c308f11c7a99624
ms.sourcegitcommit: 7aa1933e6970f878faf50d59e1f799b90afd7cc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "3374610"
---
# <a name="run-a-background-task-on-a-timer"></a>Запуск фоновой задачи по таймеру

Узнайте, как использовать [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843) для планирования в фоновом режиме однократного или выполнения периодической фоновой задачи.

**Scenario4** в пример реализации времени триггеру фоновой задачи, описанные в этом разделе [Пример активации фона](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundActivation) см.

В этом разделе предполагается, что в фоновом режиме, необходимо запустить через заданные интервалы или в указанное время. Если фоновая задача еще нет, есть образец фоновой задачи в [BackgroundActivity.cs](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/BackgroundActivation/cs/BackgroundActivity.cs). Или выполните действия, описанные в [Создание и регистрация в работе фоновой задачи](create-and-register-an-inproc-background-task.md) или [Создание и регистрация вне процесса фоновой задачи](create-and-register-a-background-task.md) для создания.

## <a name="create-a-time-trigger"></a>Создание триггера времени

Создайте новый [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843). Второй параметр *OneShot* указывает, будет ли фоновая задача выполняться однократно или периодически. Если *OneShot* имеет значение True, первый параметр (*FreshnessTime*) задает число минут ожидания перед планированием фоновой задачи. Если *OneShot* имеет значение False, частоту выполнения фоновой задачи определяет *FreshnessTime*.

Встроенный таймер для приложений UWP, предназначенных для настольных компьютеров или мобильных устройств, выполняет фоновые задачи с интервалом в 15 минут. (Таймер запускается в 15-минутных интервалов, системе требуется только пробуждение каждые 15 минут для пробуждения в приложения, которые запросили TimerTriggers--что экономит энергию.)

- Если параметру *FreshnessTime* задано значение 15минут, а параметру *OneShot* — значение true, задача будет запланирована для однократного выполнения через 15–30минут с момента ее регистрации. Если задано значение "25 минут" и *OneShot* имеет значение true, задача будет запланирована для однократного выполнения через 25–40минут с момента ее регистрации.

- Если параметру *FreshnessTime* задано значение 15 минут, а параметру *OneShot* — false, задача будет запланирована для выполнения каждые 15минут через 15–30минут с момента ее регистрации. Если задано значение "n минут" и *OneShot* имеет значение false, задача будет запланирована для выполнения каждые n минут, начиная с промежутка между n и n+15 после регистрации.

> [!NOTE]
> Если *FreshnessTime* задано значение меньше 15 минут, это исключение при попытке регистрации фоновой задачи.
 
Например этот триггер приведет в фоновом режиме для запуска один раз в час.

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

При выполнении задачи можно создать условие фоновой задачи для элемента управления. Условие предотвращает запуск, пока не будет выполнено условие фоновой задачи. [Условия для выполнения в фоновом режиме](set-conditions-for-running-a-background-task.md)для получения дополнительных сведений см.

В этом примере условию присвоено значение **UserPresent**, поэтому после срабатывания триггера задача выполняется, только если пользователь активен. Список возможных условий см. в статье [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

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

Более подробные сведения об условиях и типы триггеров фона [поддержки своего приложения с помощью фоновых задач](support-your-app-with-background-tasks.md)см.

##  <a name="call-requestaccessasync"></a>Вызов RequestAccessAsync()

Перед регистрацией фоновой задачи **ApplicationTrigger** , вызовите [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700494) для определения уровня активности фона позволяет пользователя, поскольку пользователь отключил фоновой активности для вашего приложения. Дополнительные сведения о пользователях, способов [оптимизировать фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) могут управлять параметрами для фоновой активности, см.

```cs
var requestStatus = await Windows.ApplicationModel.Background.BackgroundExecutionManager.RequestAccessAsync();
if (requestStatus != BackgroundAccessStatus.AlwaysAllowed)
{
    // Depending on the value of requestStatus, provide an appropriate response
    // such as notifying the user which functionality won't work as expected
}
```

## <a name="register-the-background-task"></a>Регистрация фоновой задачи

Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Дополнительные сведения о регистрации фоновых задач, а также увидеть определение метода **RegisterBackgroundTask()** в приведенном ниже примере кода [в фоновом режиме](register-a-background-task.md)см.

> [!IMPORTANT]
> Не задавайте для фоновой задачи, выполняемые в рамках одного процесса, как ваше приложение, `entryPoint`. Задайте для фоновой задачи, выполняемые в отдельном от приложения, `entryPoint` пространство имен, "." и имя класса, который содержит реализации фоновой задачи.

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

Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

## <a name="manage-resources-for-your-background-task"></a>Управление ресурсами для фоновой задачи

Используйте метод [BackgroundExecutionManager.RequestAccessAsync](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundexecutionmanager.aspx), чтобы определить, принял ли пользователь решение ограничить фоновую активность вашего приложения. Следите за расходом заряда батареи и запускайте приложения в фоновом режиме только тогда, когда необходимо завершить интересующее пользователя действие. Дополнительные сведения о пользователях, способов [оптимизировать фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) могут управлять параметрами для фоновой активности, см.

- Память: Настройка использования памяти и энергию вашего приложения является залогом того, что операционная система позволит запускать задание фона. Чтобы узнать, сколько памяти с помощью фоновой задачи используйте [Интерфейсы API управления памяти](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.aspx) . Фоновая задача использует больше памяти, тем сложнее это операционная система продолжала работать, когда другое приложение находится на переднем плане. Пользователь полностью контролирует все фоновые действия, которые может выполнять ваше приложение, и видит, как ваше приложение влияет на расход заряда батареи.  
- Время ЦП: фоновые задачи ограничены по времени использования часы стены получить основании тип триггера.

Сведения об ограничениях ресурсов для фоновых задач см. в статье [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

## <a name="remarks"></a>Комментарии

Начиная с Windows 10, его нет необходимости для пользователя, чтобы добавить приложение на экран блокировки, чтобы использовать фоновые задачи.

Фоновая задача выполняется только если сначала был вызван [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) с помощью **TimeTrigger** .

## <a name="related-topics"></a>Еще по теме

* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Пример кода фоновой задачи](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundTask)
* [Создание и регистрация фоновой задачи, выполняемой внутри процесса](create-and-register-an-inproc-background-task.md)
* [Создание и регистрация фоновой задачи, выполняемой вне процесса](create-and-register-a-background-task.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Освобождение памяти при переходе приложения в фоновый режим](reduce-memory-usage.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях UWP (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Задержка приостановки приложения с помощью расширенного сеанса выполнения](run-minimized-with-extended-execution.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
