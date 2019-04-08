---
title: Запуск фоновой задачи в приложении
description: Описывается, как запустить фоновую задачу из приложения
ms.date: 07/06/2018
ms.topic: article
keywords: триггер фоновой задачи, фоновой задачи
ms.localizationpriority: medium
ms.openlocfilehash: 02e4bf3d7977c9bdd675f264a37e608a5082ef4c
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608099"
---
# <a name="trigger-a-background-task-from-within-your-app"></a>Запуск фоновой задачи в приложении

Узнайте, как использовать [ApplicationTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.ApplicationTrigger) для активации фоновой задачи в приложении.

Пример того, как создать триггер приложения, см. в этом [пример](https://github.com/Microsoft/Windows-universal-samples/blob/v2.0.0/Samples/BackgroundTask/cs/BackgroundTask/Scenario5_ApplicationTriggerTask.xaml.cs).

В этом разделе предполагается, что есть фоновая задача, которая требуется активировать из вашего приложения. Если у вас еще нет в фоновом режиме, это пример фоновой задачи в [BackgroundActivity.cs](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/BackgroundActivation/cs/BackgroundActivity.cs). Или выполните действия, описанные в [Создание и регистрация out-of-process фоновую задачу](create-and-register-a-background-task.md) создать ее.

## <a name="why-use-an-application-trigger"></a>Зачем использовать триггер приложения

Используйте **ApplicationTrigger** для выполнения кода в виде отдельного процесса из приложения переднего плана. **ApplicationTrigger** подходит, если ваше приложение имеет работы, которая должна выполняться в фоновом режиме, даже если пользователь закрывает приложение переднего плана. Если необходимо остановить фоновые задачи когда приложение закрыто или должен будет привязан к состояние процесса переднего плана, затем [выполнения расширенных](run-minimized-with-extended-execution.md) следует использовать,.

## <a name="create-an-application-trigger"></a>Создание триггера приложения

Создайте новый [ApplicationTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.ApplicationTrigger). Вы можете сохранить его в поле, как показано в приведенном ниже фрагменте. Это для удобства, чтобы создать новый экземпляр позже, когда мы хотим сообщить триггер не обязательно. Но вы можете использовать любой **ApplicationTrigger** экземпляр сигнала триггера.

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

Можно создать условие фоновой задачи, чтобы контролировать время запуска задачи. Условие запрещает фоновую задачу, пока не будет выполнено. См. также: [Задание условий для выполнения фоновой задачи](set-conditions-for-running-a-background-task.md).

В этом примере условие имеет значение **InternetAvailable** таким образом, чтобы после запуска, задание выполняется только после подключения к Интернету. Список возможных условий см. в статье [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

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

Более углубленно с условиями и типами фоновых триггеров можно ознакомиться в разделе [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

##  <a name="call-requestaccessasync"></a>Вызов RequestAccessAsync()

Перед регистрацией **ApplicationTrigger** фоновой задачи, вызовите [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700494) для определения уровня фоновой активности позволяет пользователю, так как пользователь отключил фоновой активности для вашего приложения. В разделе [оптимизировать фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о том, как пользователи могут управлять параметрами для фоновой активности.

```csharp
var requestStatus = await Windows.ApplicationModel.Background.BackgroundExecutionManager.RequestAccessAsync();
if (requestStatus != BackgroundAccessStatus.AlwaysAllowed)
{
   // Depending on the value of requestStatus, provide an appropriate response
   // such as notifying the user which functionality won't work as expected
}
```

## <a name="register-the-background-task"></a>Регистрация фоновой задачи

Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Дополнительные сведения о регистрации фоновых задач, а также просмотреть определение **RegisterBackgroundTask()** метод в примере кода ниже в разделе [регистрация фоновой задачи](register-a-background-task.md).

Если вы планируете с помощью триггера приложения для расширения жизненным циклом процесса переднего плана, рассмотрите возможность использования [выполнения расширенных](run-minimized-with-extended-execution.md) вместо. Для работы триггер приложения разработан для создания отдельно размещенного процесса. В следующем фрагменте кода регистрирует триггер фоновой вне процесса.

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

Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации недопустимы, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

## <a name="trigger-the-background-task"></a>Вызвать фоновую задачу

Прежде чем запустить фоновую задачу, используйте [BackgroundTaskRegistration](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration) для проверки регистрации фоновой задачи. — Самое время проверить, что все фоновые задачи регистрируются во время запуска приложения.

Запустите фоновую задачу, вызвав метод [ApplicationTrigger.RequestAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.applicationtrigger). Любой **ApplicationTrigger** экземпляр будет выполнять.

Обратите внимание, что **ApplicationTrigger.RequestAsync** невозможно вызвать из фоновой задачи сам или когда приложение находится в фоновом режиме, в рабочее состояние (см. [жизненный цикл приложения](app-lifecycle.md) Дополнительные сведения о состояниях приложения).
Он может возвращать [DisabledByPolicy](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.applicationtriggerresult) Если пользователь установил энергии или конфиденциальности политики, которые препятствуют приложение выполнение фоновой активности.
Кроме того одновременно может выполняться только один AppTrigger. Если при попытке выполнить AppTrigger, пока другой уже выполняется, функция вернет [CurrentlyRunning](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.applicationtriggerresult).

```csharp
var result = await _AppTrigger.RequestAsync();
```

## <a name="manage-resources-for-your-background-task"></a>Управление ресурсами для фоновой задачи

Используйте метод [BackgroundExecutionManager.RequestAccessAsync](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundexecutionmanager.aspx), чтобы определить, принял ли пользователь решение ограничить фоновую активность вашего приложения. Следите за расходом заряда батареи и запускайте приложения в фоновом режиме только тогда, когда необходимо завершить интересующее пользователя действие. В разделе [оптимизировать фоновой активности](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о том, как пользователи могут управлять параметрами для фоновой активности.  

- Память: Настройка использования памяти и энергии приложения крайне важно убедиться, что операционная система позволит запускать задание фона. Используйте [API-интерфейсы управления памятью](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.aspx), чтобы узнать, сколько памяти потребляет ваше фоновое задание. Чем больше памяти потребляет ваше фоновое задание, тем сложнее операционной системе поддерживать его выполнение, когда другое приложение работает на переднем плане. Пользователь полностью контролирует все фоновые действия, которые может выполнять ваше приложение, и видит, как ваше приложение влияет на расход заряда батареи.  
- Время ЦП: Фоновые задачи ограничены объемом физическое время использования им получить зависимости от типа триггера. Фоновые задачи, инициируемые триггер приложения ограничены около 10 минут.

Сведения об ограничениях ресурсов для фоновых задач см. в статье [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

## <a name="remarks"></a>Замечания

Начиная с Windows 10, он больше не необходимости добавить в приложение на экран блокировки, чтобы использовать фоновые задачи.

Фоновая задача будет запускаться только с помощью **ApplicationTrigger**, если сначала вызвано [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485).

## <a name="related-topics"></a>Статьи по теме

* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Пример кода для задач фона](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundTask)
* [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md)
* [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Освобождение памяти при переключении приложения в фоновый режим](reduce-memory-usage.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Активация приостановки, возобновления и фоновых событий для приложений универсальной платформы Windows (при отладке)](https://go.microsoft.com/fwlink/p/?linkid=254345)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Задержка приостановки приложения с помощью расширенного сеанса выполнения](run-minimized-with-extended-execution.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
