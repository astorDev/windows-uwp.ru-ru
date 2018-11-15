---
author: TylerMSFT
title: Запуск фоновой задачи в приложении
description: Описывается, как запуск фоновой задачи в приложении
ms.author: twhitney
ms.date: 07/06/2018
ms.topic: article
keywords: триггер фоновой задачи, фоновой задачи
ms.localizationpriority: medium
ms.openlocfilehash: 6846cfe77272a78eff7ddc05c9a7e48dddd21fc2
ms.sourcegitcommit: e38b334edb82bf2b1474ba686990f4299b8f59c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6838749"
---
# <a name="trigger-a-background-task-from-within-your-app"></a>Запуск фоновой задачи в приложении

Узнайте, как использовать [ApplicationTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.ApplicationTrigger) для активации фоновой задачи в приложении.

Пример того, как создать триггер приложения см. в этом [примере](https://github.com/Microsoft/Windows-universal-samples/blob/v2.0.0/Samples/BackgroundTask/cs/BackgroundTask/Scenario5_ApplicationTriggerTask.xaml.cs).

В этом разделе предполагается, что у вас есть фоновая задача, которая требуется активировать из вашего приложения. Если у вас еще нет фоновой задачи, не в [BackgroundActivity.cs](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/BackgroundActivation/cs/BackgroundActivity.cs)пример фоновой задачи. Или, выполните действия, описанные в [Создание и регистрация задачи, выполняемой вне процесса фоновые](create-and-register-a-background-task.md) его созданию.

## <a name="why-use-an-application-trigger"></a>Зачем использовать триггер приложений

Используйте **ApplicationTrigger** для выполнения кода в виде отдельного процесса из приложения переднего плана. **ApplicationTrigger** подходит, если ваше приложение содержит работы, которая должна выполняться в фоновом режиме — даже если пользователь закроет приложение переднего плана. Если необходимо остановить фоновой работы при приложение закрывается, или должна быть привязана к состоянию процесса переднего плана, [Средство расширенного сеанса выполнения](run-minimized-with-extended-execution.md) следует использовать, а затем.

## <a name="create-an-application-trigger"></a>Создать триггер приложений

Создайте новый [ApplicationTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.ApplicationTrigger). Вы можете сохранить его в поле как сделано в следующем фрагменте кода. Это для удобства мы не нужно создать новый экземпляр позже, когда мы хотим сообщить триггер. Однако вы можете использовать любой экземпляр **ApplicationTrigger** сигнала триггера.

```csharp
// _AppTrigger is an ApplicationTrigger field defined at a scope that will keep it alive
// as long as you need to trigger the background task.
// Or, you could create a new ApplicationTrigger instance and use that when you want to
// trigger the background task.
_AppTrigger = new ApplicationTrigger();
```

```cppwinrt
// _AppTrigger is an ApplicationTrigger field defined at a scope that will keep it alive
// as long as you need to trigger the background task.
// Or, you could create a new ApplicationTrigger instance and use that when you want to
// trigger the background task.
Windows::ApplicationModel::Background::ApplicationTrigger _AppTrigger;
```

```cpp
// _AppTrigger is an ApplicationTrigger field defined at a scope that will keep it alive
// as long as you need to trigger the background task.
// Or, you could create a new ApplicationTrigger instance and use that when you want to
// trigger the background task.
ApplicationTrigger ^ _AppTrigger = ref new ApplicationTrigger();
```

## <a name="optional-add-a-condition"></a>Добавление условия (необязательно)

Можно создать условие фоновой задачи, чтобы контролировать время запуска задачи. Это предотвращает запуск пока не будет выполнено условие фоновой задачи. Дополнительные сведения см. в разделе [Задание условий для выполнения фоновой задачи](set-conditions-for-running-a-background-task.md).

В этом примере условию присвоено значение **InternetAvailable** таким образом, чтобы после срабатывания задача выполняется, только если доступа к Интернету. Список возможных условий см. в статье [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

```csharp
SystemCondition internetCondition = new SystemCondition(SystemConditionType.InternetAvailable);
```

```cppwinrt
Windows::ApplicationModel::Background::SystemCondition internetCondition{
    Windows::ApplicationModel::Background::SystemConditionType::InternetAvailable };
```

```cpp
SystemCondition ^ internetCondition = ref new SystemCondition(SystemConditionType::InternetAvailable)
```

Более подробные сведения об условиях и типам триггеров фоновых см. в разделе [поддержки вашего приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

##  <a name="call-requestaccessasync"></a>Вызов RequestAccessAsync()

Перед регистрацией фоновой задачи **ApplicationTrigger** , вызовите [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700494) , чтобы определить уровень фоновую активность, которое позволяет пользователю, так как пользователь отключил фоновую активность вашего приложения. См. в разделе, что [Оптимизация фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о пользователях, способы можно управлять параметрами для фоновых задач.

```csharp
var requestStatus = await Windows.ApplicationModel.Background.BackgroundExecutionManager.RequestAccessAsync();
if (requestStatus != BackgroundAccessStatus.AlwaysAllowed)
{
   // Depending on the value of requestStatus, provide an appropriate response
   // such as notifying the user which functionality won't work as expected
}
```

## <a name="register-the-background-task"></a>Регистрация фоновой задачи

Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Дополнительные сведения о регистрации фоновых задач, а также просмотреть определение метода **RegisterBackgroundTask()** в приведенном ниже примере кода см. в разделе [Регистрация фоновой задачи](register-a-background-task.md).

Если вы собираетесь с помощью триггер приложения для расширения жизненным циклом процесса вашего переднего плана, попробуйте использовать [Средство расширенного сеанса выполнения](run-minimized-with-extended-execution.md) . Триггер приложения предназначен для создания отдельно размещенный процесс будет работать в. В следующем фрагменте кода регистрирует триггер фоновой вне процесса.

```csharp
string entryPoint = "Tasks.ExampleBackgroundTaskClass";
string taskName   = "Example application trigger";

BackgroundTaskRegistration task = RegisterBackgroundTask(entryPoint, taskName, appTrigger, internetCondition);
```

```cppwinrt
std::wstring entryPoint{ L"Tasks.ExampleBackgroundTaskClass" };
std::wstring taskName{ L"Example application trigger" };

Windows::ApplicationModel::Background::BackgroundTaskRegistration task{
    RegisterBackgroundTask(entryPoint, taskName, appTrigger, internetCondition) };
```

```cpp
String ^ entryPoint = "Tasks.ExampleBackgroundTaskClass";
String ^ taskName   = "Example application trigger";

BackgroundTaskRegistration ^ task = RegisterBackgroundTask(entryPoint, taskName, appTrigger, internetCondition);
```

Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

## <a name="trigger-the-background-task"></a>Запустите фоновую задачу

Прежде чем запустить фоновую задачу, используйте [BackgroundTaskRegistration](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration) , чтобы убедиться, что фоновая задача зарегистрирована. Убедитесь, что все фоновые задачи зарегистрированы время — во время запуска приложения.

Запустите фоновую задачу, вызвав метод [ApplicationTrigger.RequestAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.applicationtrigger). Любой экземпляр **ApplicationTrigger** будет выполнять.

Обратите внимание, что **ApplicationTrigger.RequestAsync** невозможно вызвать из фоновой задачи сам или когда приложение находится в фоновом режиме под управлением состояния ( [жизненный цикл приложения](app-lifecycle.md) более подробные сведения о состояниях приложения).
Он может возвращать [DisabledByPolicy](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.applicationtriggerresult) , если пользователь задал энергии или конфиденциальности политик, которые предотвратить выполнение фоновой активности приложения.
Кроме того одновременно может выполняться только одна AppTrigger. Если при попытке запустить AppTrigger, когда другой уже работает, функция вернет [CurrentlyRunning](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.applicationtriggerresult).

```csharp
var result = await _AppTrigger.RequestAsync();
```

## <a name="manage-resources-for-your-background-task"></a>Управление ресурсами для фоновой задачи

Используйте метод [BackgroundExecutionManager.RequestAccessAsync](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundexecutionmanager.aspx), чтобы определить, принял ли пользователь решение ограничить фоновую активность вашего приложения. Следите за расходом заряда батареи и запускайте приложения в фоновом режиме только тогда, когда необходимо завершить интересующее пользователя действие. См. в разделе, что [Оптимизация фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о пользователях, способы можно управлять параметрами для фоновых задач.  

- Память: Настройка использования памяти и электроэнергии вашим приложением является ключом подтверждение того, что операционная система будет разрешать запуск фоновой задачи. Используйте [API -интерфейсы управления памятью](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.aspx) , чтобы узнать, сколько памяти потребляет фоновой задачи. Чем больше памяти потребляет фоновой задачи, тем сложнее операционной системе поддерживать его выполнение, когда другое приложение работает на переднем плане. Пользователь полностью контролирует все фоновые действия, которые может выполнять ваше приложение, и видит, как ваше приложение влияет на расход заряда батареи.  
- Времени ЦП: фоновые задачи ограничены по времени секундами использования, они получают в зависимости от типа триггера. Фоновые задачи, инициируемые триггером приложения ограничены около 10 минут.

Сведения об ограничениях ресурсов для фоновых задач см. в статье [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

## <a name="remarks"></a>Комментарии

Начиная с Windows10, он больше не пользователю возможность добавлять приложения на экране блокировки, чтобы использовать фоновые задачи.

Фоновая задача будет запускаться только с помощью **ApplicationTrigger** , если [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) вызван сначала.

## <a name="related-topics"></a>Смежные разделы

* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Пример кода фоновой задачи](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundTask)
* [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md)
* [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md)
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
