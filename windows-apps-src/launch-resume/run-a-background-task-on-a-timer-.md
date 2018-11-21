---
author: TylerMSFT
title: Запуск фоновой задачи по таймеру
description: Узнайте, как запланировать однократное выполнение фоновой задачи или периодически запускать фоновую задачу.
ms.assetid: 0B7F0BFF-535A-471E-AC87-783C740A61E9
ms.author: twhitney
ms.date: 07/06/2018
ms.topic: article
keywords: Windows 10, uwp, фоновой задачи
ms.localizationpriority: medium
ms.openlocfilehash: a03a26262d03d93ea2f23f2a61ba1ee603a2d507
ms.sourcegitcommit: 93c0a60cf531c7d9fe7b00e7cf78df86906f9d6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7553365"
---
# <a name="run-a-background-task-on-a-timer"></a>Запуск фоновой задачи по таймеру

Узнайте, как использовать [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843) планировать однократное выполнение фоновой задачи или запуск периодические фоновой задачи.

См. в разделе **Scenario4** в [пример фоновой активации](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundActivation) , чтобы просмотреть пример реализации времени, запущенной фоновой задачи, описанные в этом разделе.

В этом разделе предполагается, что у вас есть фоновая задача, которая должна выполняться периодически или в определенное время. Если у вас еще нет фоновой задачи, не в [BackgroundActivity.cs](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/BackgroundActivation/cs/BackgroundActivity.cs)пример фоновой задачи. Или, выполните действия, описанные в [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md) или [Создание и регистрация задачи, выполняемой вне процесса фоновые](create-and-register-a-background-task.md) его созданию.

## <a name="create-a-time-trigger"></a>Создание триггера времени

Создайте новый [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/br224843). Второй параметр *OneShot* указывает, будет ли фоновая задача выполняться однократно или периодически. Если *OneShot* имеет значение True, первый параметр (*FreshnessTime*) задает число минут ожидания перед планированием фоновой задачи. Если *OneShot* имеет значение False, частоту выполнения фоновой задачи определяет *FreshnessTime*.

Встроенный таймер для приложений UWP, предназначенных для настольных компьютеров или мобильных устройств, выполняет фоновые задачи с интервалом в 15 минут. (Таймер запускает интервалом в 15 минут, что система нужно только пробуждения каждые 15 минут на пробуждение приложений, которые запросили TimerTriggers--которого сэкономить электроэнергию.)

- Если параметру *FreshnessTime* задано значение 15минут, а параметру *OneShot* — значение true, задача будет запланирована для однократного выполнения через 15–30минут с момента ее регистрации. Если задано значение "25 минут" и *OneShot* имеет значение true, задача будет запланирована для однократного выполнения через 25–40минут с момента ее регистрации.

- Если параметру *FreshnessTime* задано значение 15 минут, а параметру *OneShot* — false, задача будет запланирована для выполнения каждые 15минут через 15–30минут с момента ее регистрации. Если задано значение "n минут" и *OneShot* имеет значение false, задача будет запланирована для выполнения каждые n минут, начиная с промежутка между n и n+15 после регистрации.

> [!NOTE]
> Если *FreshnessTime* задано значение меньше 15 минут, — это исключение при попытке зарегистрировать фоновую задачу.
 
Например этот триггер приведет к фоновой задачи для выполнения один раз в час.

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

Можно создать условие фоновой задачи, чтобы контролировать время запуска задачи. Это предотвращает запуск пока не будет выполнено условие фоновой задачи. Дополнительные сведения см. в разделе [Задание условий для выполнения фоновой задачи](set-conditions-for-running-a-background-task.md).

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

Более подробные сведения об условиях и типам триггеров фоновых см. в разделе [поддержки вашего приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

##  <a name="call-requestaccessasync"></a>Вызов RequestAccessAsync()

Перед регистрацией фоновой задачи **ApplicationTrigger** , вызовите [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700494) , чтобы определить уровень фоновую активность, которое позволяет пользователю, так как пользователь отключил фоновую активность вашего приложения. См. в разделе, что [Оптимизация фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о пользователях, способы можно управлять параметрами для фоновых задач.

```cs
var requestStatus = await Windows.ApplicationModel.Background.BackgroundExecutionManager.RequestAccessAsync();
if (requestStatus != BackgroundAccessStatus.AlwaysAllowed)
{
    // Depending on the value of requestStatus, provide an appropriate response
    // such as notifying the user which functionality won't work as expected
}
```

## <a name="register-the-background-task"></a>Регистрация фоновой задачи

Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Дополнительные сведения о регистрации фоновых задач, а также просмотреть определение метода **RegisterBackgroundTask()** в приведенном ниже примере кода см. в разделе [Регистрация фоновой задачи](register-a-background-task.md).

> [!IMPORTANT]
> Для фоновых задач, которые выполняются в том же процессе, что и приложение, не устанавливайте `entryPoint`. Для фоновых задач, которые выполняются в отдельном процессе из вашего приложения, задайте `entryPoint` в качестве пространства имен "." и имя класса, который содержит реализацию вашей фоновой задачи.

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

Используйте метод [BackgroundExecutionManager.RequestAccessAsync](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundexecutionmanager.aspx), чтобы определить, принял ли пользователь решение ограничить фоновую активность вашего приложения. Следите за расходом заряда батареи и запускайте приложения в фоновом режиме только тогда, когда необходимо завершить интересующее пользователя действие. См. в разделе, что [Оптимизация фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о пользователях, способы можно управлять параметрами для фоновых задач.

- Память: Настройка использования памяти и электроэнергии вашим приложением является ключом подтверждение того, что операционная система будет разрешать запуск фоновой задачи. Используйте [API -интерфейсы управления памятью](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.aspx) , чтобы узнать, сколько памяти потребляет фоновой задачи. Чем больше памяти потребляет фоновой задачи, тем сложнее операционной системе поддерживать его выполнение, когда другое приложение работает на переднем плане. Пользователь полностью контролирует все фоновые действия, которые может выполнять ваше приложение, и видит, как ваше приложение влияет на расход заряда батареи.  
- Времени ЦП: фоновые задачи ограничены по времени секундами использования, они получают в зависимости от типа триггера.

Сведения об ограничениях ресурсов для фоновых задач см. в статье [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

## <a name="remarks"></a>Комментарии

Начиная с Windows10, он больше не пользователю возможность добавлять приложения на экране блокировки, чтобы использовать фоновые задачи.

Фоновая задача будет отображаться только с помощью **TimeTrigger** , если был вызван [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) сначала.

## <a name="related-topics"></a>Статьи по теме

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
