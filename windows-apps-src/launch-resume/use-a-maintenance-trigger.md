---
title: Использование триггера обслуживания
description: Узнайте, как использовать класс MaintenanceTrigger для выполнения облегченного кода в фоновом режиме, когда устройство подключено к сети.
ms.assetid: 727D9D84-6C1D-4DF3-B3B0-2204EA4D76DD
ms.date: 07/06/2018
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
ms.openlocfilehash: c155a2b80826669693c3250282076d8a1b27ee83
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259396"
---
# <a name="use-a-maintenance-trigger"></a>Использование триггера обслуживания

**Важные API**

- [**маинтенанцетригжер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.MaintenanceTrigger)
- [**баккграундтаскбуилдер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder)
- [**системкондитион**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemCondition)

Узнайте, как использовать класс [**MaintenanceTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.MaintenanceTrigger) для выполнения облегченного кода в фоновом режиме, когда устройство подключено к сети.

## <a name="create-a-maintenance-trigger-object"></a>Создание объекта триггера обслуживания

Для изучения этого примера необходим облегченный код, который можно выполнить в фоновом режиме для усовершенствования вашего приложения, когда устройство подключено к сети. В этом разделе рассматривается класс [**MaintenanceTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.MaintenanceTrigger), который похож на [**SystemTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemTriggerType).

Подробнее о написании класса фоновой задачи см. в статьях [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md) и [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md).

Создайте новый объект [**MaintenanceTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.MaintenanceTrigger). Второй параметр — *OneShot* — указывает, однократно или периодически будет выполняться задача обслуживания. Если *OneShot* имеет значение True, первый параметр (*FreshnessTime*) задает число минут ожидания перед планированием фоновой задачи. Если *OneShot* имеет значение False, частоту выполнения фоновой задачи определяет *FreshnessTime*.

> [!NOTE]
> Если *фрешнесстиме* имеет значение менее 15 минут, при попытке зарегистрировать фоновую задачу выдается исключение.

В этом примере кода создается триггер, который выполняется один раз в час.

```csharp
uint waitIntervalMinutes = 60;
MaintenanceTrigger taskTrigger = new MaintenanceTrigger(waitIntervalMinutes, false);
```

```cppwinrt
uint32_t waitIntervalMinutes{ 60 };
Windows::ApplicationModel::Background::MaintenanceTrigger taskTrigger{ waitIntervalMinutes, false };
```

```cpp
unsigned int waitIntervalMinutes = 60;
MaintenanceTrigger ^ taskTrigger = ref new MaintenanceTrigger(waitIntervalMinutes, false);
```

## <a name="optional-add-a-condition"></a>Добавление условия (необязательно)

- При необходимости можно также создать условие фоновой задачи, чтобы контролировать время запуска задачи. Условие не даст запустить фоновую задачу, пока оно не будет выполнено. Подробнее об этом можно узнать в разделе [Задание условий для выполнения фоновой задачи](set-conditions-for-running-a-background-task.md).

В этом примере устанавливается условие **InternetAvailable**, так что обслуживание выполняется, когда доступно интернет-соединение (или когда оно становится доступным). Список возможных условий фоновых задач см. в разделе [**SystemConditionType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemConditionType).

Следующий код добавляет условие для построителя задачи обслуживания:

```csharp
SystemCondition exampleCondition = new SystemCondition(SystemConditionType.InternetAvailable);
```

```cppwinrt
Windows::ApplicationModel::Background::SystemCondition exampleCondition{
    Windows::ApplicationModel::Background::SystemConditionType::InternetAvailable };
```

```cpp
SystemCondition ^ exampleCondition = ref new SystemCondition(SystemConditionType::InternetAvailable);
```

## <a name="register-the-background-task"></a>Регистрация фоновой задачи

- Зарегистрируйте фоновую задачу, вызвав функцию регистрации фоновой задачи. Узнать больше о регистрации фоновых задач можно в разделе [Регистрация фоновой задачи](register-a-background-task.md).

Следующий код регистрирует задачу обслуживания: Обратите внимание, что в нем подразумевается, что фоновая задача выполняется в отдельном от приложения процессе, так как задается `entryPoint`. Если фоновая задача выполняется в том же процессе, что и приложение, `entryPoint` не задается.

```csharp
string entryPoint = "Tasks.ExampleBackgroundTaskClass";
string taskName   = "Maintenance background task example";

BackgroundTaskRegistration task = RegisterBackgroundTask(entryPoint, taskName, taskTrigger, exampleCondition);
```

```cppwinrt
std::wstring entryPoint{ L"Tasks.ExampleBackgroundTaskClass" };
std::wstring taskName{ L"Maintenance background task example" };

Windows::ApplicationModel::Background::BackgroundTaskRegistration task{
    RegisterBackgroundTask(entryPoint, taskName, taskTrigger, exampleCondition) };
```

```cpp
String ^ entryPoint = "Tasks.ExampleBackgroundTaskClass";
String ^ taskName   = "Maintenance background task example";

BackgroundTaskRegistration ^ task = RegisterBackgroundTask(entryPoint, taskName, taskTrigger, exampleCondition);
```

> [!NOTE]
> Если устройству не хватает памяти, фоновые задачи могут быть завершены для всех семейств устройств (за исключением настольных компьютеров). Если исключение "Недостаточно памяти" не отображается или приложение не обрабатывает его, то фоновая задача будет завершена без предупреждения и без вызова события OnCanceled. Это позволяет не прерывать работу пользователя с активным приложением. Ваша фоновая задача должна поддерживать этот сценарий.

> [!NOTE]
> Универсальная платформа Windows приложения должны вызывать [**рекуестакцессасинк**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync) перед регистрацией любых типов триггеров в фоновом режиме.

Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления приложения, необходимо вызвать метод [**RemoveAccess**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.removeaccess), а затем — метод [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync) при запуске приложения после обновления. Дополнительные сведения см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

> [!NOTE]
> Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации недопустимы, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

## <a name="related-topics"></a>См. также

* [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md)
* [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Как активировать события приостановки, возобновления и фоновых событий в приложениях UWP (при отладке)](https://msdn.microsoft.com/library/windows/apps/hh974425(v=vs.110).aspx)
