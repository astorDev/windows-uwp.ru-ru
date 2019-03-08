---
title: Регистрация фоновых задач группы
description: Зарегистрируйте задачу или отмените ее регистрацию в рамках группы, чтобы изолировать эти регистрации.
ms.date: 04/05/2017
ms.topic: article
keywords: windows 10, фоновая задача
ms.openlocfilehash: a70c814e5e35359746076c5418d1f1d973e61773
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57623859"
---
# <a name="group-background-task-registration"></a>Регистрация фоновых задач группы

**Важные API**

[Класс BackgroundTaskRegistrationGroup](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistrationgroup)

Теперь фоновые задачи могут быть зарегистрированы в группу, которую можно считать логическим пространством имен. Такая изоляция помогает убедиться, что различные компоненты приложения или отдельные библиотеки не мешают регистрации фоновых задач друг друга.

Когда используемые приложение и платформа (или библиотека) регистрируют фоновую задачу с тем же именем, приложение может случайно удалить регистрации фоновых задач платформы. Авторы приложения также могут случайно удалить платформы и библиотеки регистраций фоновых задач, так как они могут отменить регистрацию всех зарегистрированных фоновых задач с помощью [BackgroundTaskRegistration.AllTasks](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration.AllTasks).  С помощью групп можно изолировать ваши регистрации фоновых задач, чтобы такое не происходило.

## <a name="features-of-groups"></a>Компоненты групп

* Группы можно однозначно идентифицировать по GUID. Они также могут иметь связанное понятное имя, которое проще для восприятия во время отладки.
* В группе можно зарегистрировать несколько фоновых задач.
* Фоновые задачи, зарегистрированные в группе, не будут отображаться в [BackgroundTaskRegistration.AllTasks](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration.AllTasks). Таким образом, приложения, которые в настоящее время используют **BackgroundTaskRegistration.AllTasks** для отмены регистрации своих задач, случайно не отменят регистрацию фоновых задач, зарегистрированных в группе. См. в разделе [регистрацию фоновых задач в группе](#unregister-background-tasks-in-a-group) ниже, чтобы узнать, как отменить регистрацию всех триггеров фона, которые были зарегистрированы как часть группы.
* Каждая регистрация фоновой задачи будет иметь свойство Group, чтобы определить, с какой группой она связана.
* Регистрация в процессе фоновых задач с группой приведет к активации через [BackgroundTaskRegistrationGroup.BackgroundActivated](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistrationgroup.BackgroundActivated) событий вместо [Application.OnBackgroundActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onbackgroundactivated#Windows_UI_Xaml_Application_OnBackgroundActivated_Windows_ApplicationModel_Activation_BackgroundActivatedEventArgs_).

## <a name="register-a-background-task-in-a-group"></a>Регистрация фоновой задачи в группе

Ниже показано, как зарегистрировать фоновую задачу (активируемую при изменении часового пояса, в этом примере) в составе группы.

```csharp
private const string groupFriendlyName = "myGroup";
private const string groupId = "3F2504E0-4F89-41D3-9A0C-0305E82C3301";
private const string myTaskName = "My Background Trigger";

public static void RegisterBackgroundTaskInGroup()
{
   BackgroundTaskRegistrationGroup group = BackgroundTaskRegistration.GetTaskGroup(groupId);
   bool isTaskRegistered = false;

   // See if this task already belongs to a group
   if (group != null)
   {
       foreach (var taskKeyValue in group.AllTasks)
       {
           if (taskKeyValue.Value.Name == myTaskName)
           {
               isTaskRegistered = true;
               break;
           }
       }
   }

   // If the background task is not in a group, register it
   if (!isTaskRegistered)
   {
       if (group == null)
       {
           group = new BackgroundTaskRegistrationGroup(groupId, groupFriendlyName);
       }

       var builder = new BackgroundTaskBuilder();
       builder.Name = myTaskName;
       builder.TaskGroup = group; // we specify the group, here
       builder.SetTrigger(new SystemTrigger(SystemTriggerType.TimeZoneChange, false));

       // Because builder.TaskEntryPoint is not specified, OnBackgroundActivated() will be raised when the background task is triggered
       BackgroundTaskRegistration task = builder.Register();
   }
}
```

## <a name="unregister-background-tasks-in-a-group"></a>Отмена регистрации фоновой задачи в группе

Ниже показано, как для отменить регистрацию фоновых задач, которые зарегистрированы в составе группы.
Поскольку фоновые задачи, зарегистрированные в группе, не отображаются в [BackgroundTaskRegistration.AllTasks](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration.AllTasks), необходимо перебрать группы, найди фоновые задачи, зарегистрированные в каждой группе, и отменить их.

```csharp
private static void UnRegisterAllTasks()
{
    // Unregister tasks that are part of a group
    foreach (var groupKeyValue in BackgroundTaskRegistration.AllTaskGroups)
    {
        foreach (var groupedTask in groupKeyValue.Value.AllTasks)
        {
            groupedTask.Value.Unregister(true); // passing true to cancel currently running instances of this background task
        }
    }

    // Unregister tasks that aren't part of a group
    foreach(var taskKeyValue in BackgroundTaskRegistration.AllTasks)
    {
        taskKeyValue.Value.Unregister(true); // passing true to cancel currently running instances of this background task
    }
}
```

## <a name="register-persistent-events"></a>Регистрация постоянных событий

При использовании групп регистрации фоновых задач в рамках внутрипроцессных фоновых задач фоновые активации направляются к событию группы, а не к событию объекта Application или CoreApplication. Это активирует несколько компонентов в приложении, чтобы обработать активацию, а не поместить все пути кода активации в объект Application. Ниже показано, как зарегистрировать событие группы, активированное в фоновом режиме. Сначала проверьте [BackgroundTaskRegistration.GetTaskGroup](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration.gettaskgroup) для определения того, зарегистрирована ли группа. В противном случае создайте новую группу с идентификатором и понятным именем. Затем зарегистрируйте обработчик событий для события BackgroundActivated в группе.

```csharp
void RegisterPersistentEvent()
{
    var group = BackgroundTaskRegistration.GetTaskGroup(groupId);
    if (group == null)
    {
        group = new BackgroundTaskRegistrationGroup(groupId, groupFriendlyName);
    }

    group.BackgroundActivated += MyEventHandler;
}
```
