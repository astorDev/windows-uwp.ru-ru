---
author: TylerMSFT
title: Запуск фоновой задачи в приложении
description: Описание способов запуска задачи фона из приложения
ms.author: twhitney
ms.date: 07/06/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: триггер задач фона, фона задач
ms.localizationpriority: medium
ms.openlocfilehash: 5ccd171f53795ef71830ffb022d0468facb3ac4f
ms.sourcegitcommit: 9a17266f208ec415fc718e5254d5b4c08835150c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "2882752"
---
# <a name="trigger-a-background-task-from-within-your-app"></a>Запуск фоновой задачи в приложении

Узнайте, как использовать [ApplicationTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.ApplicationTrigger) для активации фоновой задачи в приложении.

В этом [примере](https://github.com/Microsoft/Windows-universal-samples/blob/v2.0.0/Samples/BackgroundTask/cs/BackgroundTask/Scenario5_ApplicationTriggerTask.xaml.cs)пример того, как создать триггер приложения, см.

В этом разделе предполагается, что у вас есть в фоновом режиме, который нужно активировать из вашего приложения. Нет в фоновом режиме, имеется ли в фоновом режиме примера в [BackgroundActivity.cs](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/BackgroundActivation/cs/BackgroundActivity.cs). Или, выполните действия, описанные в [Создать и зарегистрировать задачу вне процесса фон](create-and-register-a-background-task.md) для его создания.

## <a name="why-use-an-application-trigger"></a>Причины использования триггер приложения

Используйте **ApplicationTrigger** выполнение кода в отдельном процессе приложения переднего плана. **ApplicationTrigger** подходит, если ваше приложение обладает работы, которую необходимо выполнить в фоновом режиме — даже в том случае, если пользователь закрывает приложение переднего плана. При фоновой работы необходимо приостановить когда приложение закрывается или подключен к состояние процесса переднего плана, [Расширенные выполнения](run-minimized-with-extended-execution.md) следует использовать, а затем.

## <a name="create-an-application-trigger"></a>Создать триггер приложения

Создание нового [ApplicationTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.ApplicationTrigger). Как показано в приведенном ниже фрагменте может сохранить его в поле. Это для удобства таким образом, мы не создавать новый экземпляр позже при его нужно сигнала триггер. Однако можно использовать любой экземпляр **ApplicationTrigger** сигнала триггер.

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

Можно создать условие задач фона элемента управления при выполнении задачи. Условие запрещает в фоновом до условия. Дополнительные сведения можно [задать условия для выполнения в фоновом режиме](set-conditions-for-running-a-background-task.md).

В этом примере условие принимает значение **InternetAvailable** , чтобы один раз инициирующую задание выполняется только после доступ к Интернету доступен. Список возможных условий см. в статье [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

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

Для получения дополнительной информации об условиях и типа триггеров фона видеть [поддержки приложении с помощью фоновых задач](support-your-app-with-background-tasks.md).

##  <a name="call-requestaccessasync"></a>Вызов RequestAccessAsync()

Перед регистрацией фоновом **ApplicationTrigger** , вызовите [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700494) для определения уровня активности фона позволяет пользователя, так как пользователь отключил фоновая для вашего приложения. [Фоновая оптимизировать](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о способах пользователей могут управлять параметрами для фоновая, см.

```csharp
var requestStatus = await Windows.ApplicationModel.Background.BackgroundExecutionManager.RequestAccessAsync();
if (requestStatus != BackgroundAccessStatus.AlwaysAllowed)
{
   // Depending on the value of requestStatus, provide an appropriate response
   // such as notifying the user which functionality won't work as expected
}
```

## <a name="register-the-background-task"></a>Регистрация фоновой задачи

Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Дополнительные сведения о регистрации фоновые задачи, а также просмотреть определение метода **RegisterBackgroundTask()** в приведенном ниже примере кода в разделе [Регистрация в фоновом режиме](register-a-background-task.md).

Если вы собираетесь расширить в течение времени жизни процесса переднего плана с помощью триггер приложений, рекомендуется использовать [Расширенные выполнения](run-minimized-with-extended-execution.md) . Триггер приложения для работы в разработан для создания отдельно размещенного процесса. В следующем фрагменте кода регистрирует триггер фона ожидания процесса.

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

## <a name="trigger-the-background-task"></a>Триггер в фоновом

Перед инициировать в фоновом, можно воспользоваться [BackgroundTaskRegistration](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration) чтобы убедиться, что в фоновом зарегистрирован. Хороший времени для проверки всех задач фона регистрации — во время запуска приложения.

Запуск в фоновом путем вызова [ApplicationTrigger.RequestAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.applicationtrigger). Подойдет любой экземпляр **ApplicationTrigger** .

Обратите внимание на то, что **ApplicationTrigger.RequestAsync** не может вызываться из фоновом самого или приложение — в фоновом режиме рабочее состояние ( [жизненный цикл приложений](app-lifecycle.md) более подробные сведения о состояниях приложения).
Она может возвращать [DisabledByPolicy](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.applicationtriggerresult) , если пользователь значение электроэнергии или конфиденциальности политик, которые препятствуют приложение выполняет фоновые операции.
Кроме того только одна AppTrigger можно запустить одновременно. Если попытаться запустить AppTrigger, в то время как другой уже выполняется, функция возвращает [CurrentlyRunning](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.applicationtriggerresult).

```csharp
var result = await _AppTrigger.RequestAsync();
```

## <a name="manage-resources-for-your-background-task"></a>Управление ресурсами для фоновой задачи

Используйте метод [BackgroundExecutionManager.RequestAccessAsync](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundexecutionmanager.aspx), чтобы определить, принял ли пользователь решение ограничить фоновую активность вашего приложения. Следите за расходом заряда батареи и запускайте приложения в фоновом режиме только тогда, когда необходимо завершить интересующее пользователя действие. [Фоновая оптимизировать](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-background-activity) Дополнительные сведения о способах пользователей могут управлять параметрами для фоновая, см.  

- Память: Помощник по настройке использования памяти и энергию вашего приложения — ключ к проверка того, что операционная система позволит запускать задание фона. Используйте [Интерфейсы API для управления памяти](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.aspx) для просмотра объем памяти, с помощью фоновой задачи. Использует больше памяти фоновой задачи, тем сложнее это операционная система хранения его выполнение, когда другой приложение находится на переднем плане. Пользователь полностью контролирует все фоновые действия, которые может выполнять ваше приложение, и видит, как ваше приложение влияет на расход заряда батареи.  
- ЦП: фоновых задач ограничены по их получения на основе триггер типа время использования стандартами часов. Фоновые задачи, активируемые приложениями trigger ограничены в течение 10 минут.

Сведения об ограничениях ресурсов для фоновых задач см. в статье [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md).

## <a name="remarks"></a>Комментарии

Начиная с Windows 10, больше не требуется для пользователя, чтобы добавить приложение на экран блокировки для использования фоновых задач.

В фоновом режиме будет выполняться только с помощью **ApplicationTrigger** , если пользователь уже звонил [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) сначала.

## <a name="related-topics"></a>Еще по теме

* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Пример кода задач фона](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundTask)
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
