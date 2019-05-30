---
title: Регистрация фоновой задачи
description: Сведения о создании функции, которую можно многократно использовать для безопасной регистрации большинства фоновых задач.
ms.assetid: 8B1CADC5-F630-48B8-B3CE-5AB62E3DFB0D
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, фоновую задачу
ms.localizationpriority: medium
ms.openlocfilehash: 087f60ae3a16ad4cd38137d692fe079ce6c58bf4
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371746"
---
# <a name="register-a-background-task"></a>Регистрация фоновой задачи


**Важные API**

-   [**Класс BackgroundTaskRegistration**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration)
-   [**Класс BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder)
-   [**Класс SystemCondition**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemCondition)

Сведения о создании функции, которую можно многократно использовать для безопасной регистрации большинства фоновых задач.

Эта статья относится как к внутрипроцессным, так и к внепроцессным фоновым задачам. В этой статье предполагается, что у вас уже есть фоновая задача, которую нужно зарегистрировать. (О том, как написать фоновую задачу, см. в статье [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md) или [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md)).

Этот раздел содержит краткий обзор служебной функции, которая регистрирует фоновые задачи. Прежде чем несколько раз зарегистрировать задачу, эта служебная функция проверяет наличие существующих регистраций, чтобы избежать проблем, связанных с многократными регистрациями. Она может также применять условие системы к фоновой задаче. Этот обзор включает полный рабочий пример данной служебной функции.

**Примечание**  

Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync) перед регистрацией любых типов фоновых триггеров.

Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.removeaccess), а затем — метод [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundexecutionmanager.requestaccessasync) при запуске приложения после обновления. Дополнительные сведения см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

## <a name="define-the-method-signature-and-return-type"></a>Определение подписи и типа возвращаемых данных метода

Этот метод принимает точку входа задачи, имя задачи, предварительно построенный триггер фоновой задачи и (необязательно) условие [**SystemCondition**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemCondition) для фоновой задачи. Этот метод возвращает объект [**BackgroundTaskRegistration**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration).

> [!Important]
> `taskEntryPoint` -для фоновых задач, выполняющиеся вне процесса, это должен быть создан как имя пространства имен "." и имя класса, содержащего класс фона. Строка обрабатывается с учетом регистра.  Например, если имеется пространство имен "MyBackgroundTasks" и класс "BackgroundTask1", содержащий код вашего класса фоновой задачи, строка для `taskEntryPoint` имела бы вид "MyBackgroundTasks.BackgroundTask1".
> Если фоновая задача выполняется в том же процессе, что и приложение (т. е. представляет собой внутрипроцессную фоновую задачу), `taskEntryPoint` задавать не нужно.

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> public static BackgroundTaskRegistration RegisterBackgroundTask(
>                                                 string taskEntryPoint,
>                                                 string name,
>                                                 IBackgroundTrigger trigger,
>                                                 IBackgroundCondition condition)
> {
>     
>     // We'll add code to this function in subsequent steps.
>
> }
> ```
> ``` cpp
> BackgroundTaskRegistration^ MainPage::RegisterBackgroundTask(
>                                              Platform::String ^ taskEntryPoint,
>                                              Platform::String ^ taskName,
>                                              IBackgroundTrigger ^ trigger,
>                                              IBackgroundCondition ^ condition)
> {
>     
>     // We'll add code to this function in subsequent steps.
>
> }
> ```

## <a name="check-for-existing-registrations"></a>Проверка наличия существующих регистраций

Проверьте, не зарегистрирована ли уже задача. Это важно проверить, потому что при многократной регистрации задачи она будет выполняться несколько раз при каждом срабатывании триггера, что может привести к избыточному потреблению ресурсов ЦП и непредсказуемому поведению.

Для проверки наличия существующих регистраций можно запросить свойство [**BackgroundTaskRegistration.AllTasks**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration.alltasks) и выполнить итерацию по результату. Проверьте имя каждого экземпляра: если оно совпадает с именем регистрируемой задачи, следует прервать цикл и установить переменную флага, чтобы вашему коду удалось выбрать другой путь в следующем шаге.

> **Примечание**  использовать имена фоновой задачи, которые уникальны для приложения. Убедитесь, что каждая фоновая задача имеет уникальное имя.

Следующий код регистрирует фоновую задачу с использованием триггера [**SystemTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemTrigger), который мы создали в последнем шаге:

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> public static BackgroundTaskRegistration RegisterBackgroundTask(
>                                                 string taskEntryPoint,
>                                                 string name,
>                                                 IBackgroundTrigger trigger,
>                                                 IBackgroundCondition condition)
> {
>     //
>     // Check for existing registrations of this background task.
>     //
>
>     foreach (var cur in BackgroundTaskRegistration.AllTasks)
>     {
>
>         if (cur.Value.Name == name)
>         {
>             //
>             // The task is already registered.
>             //
>
>             return (BackgroundTaskRegistration)(cur.Value);
>         }
>     }
>     
>     // We'll register the task in the next step.
> }
> ```
> ``` cpp
> BackgroundTaskRegistration^ MainPage::RegisterBackgroundTask(
>                                              Platform::String ^ taskEntryPoint,
>                                              Platform::String ^ taskName,
>                                              IBackgroundTrigger ^ trigger,
>                                              IBackgroundCondition ^ condition)
> {
>     //
>     // Check for existing registrations of this background task.
>     //
>     
>     auto iter   = BackgroundTaskRegistration::AllTasks->First();
>     auto hascur = iter->HasCurrent;
>     
>     while (hascur)
>     {
>         auto cur = iter->Current->Value;
>         
>         if(cur->Name == name)
>         {
>             //
>             // The task is registered.
>             //
>             
>             return (BackgroundTaskRegistration ^)(cur);
>         }
>         
>         hascur = iter->MoveNext();
>     }
>     
>     // We'll register the task in the next step.
> }
> ```

## <a name="register-the-background-task-or-return-the-existing-registration"></a>Регистрация фоновой задачи (или возврат существующей регистрации)


Проверьте, не найдена ли задача в списке существующих регистраций фоновых задач. Если да, верните этот экземпляр задачи.

Затем зарегистрируйте задачу с использованием нового объекта [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder). Этот код должен проверить, имеет ли параметр условия значение NULL, и, если нет, добавить условие к объекту регистрации. Верните объект [**BackgroundTaskRegistration**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration), возвращенный методом [**BackgroundTaskBuilder.Register**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskbuilder.register).

> **Примечание**  параметры регистрации фоновой задачи проверяются во время регистрации. Если какие-либо из параметров регистрации недопустимы, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.
> **Примечание**. Если вы регистрируете фоновую задачу, которая выполняется в том же процессе, что и приложение, отправьте `String.Empty` или `null` для параметра `taskEntryPoint`.

В следующем примере выполняется либо возврат существующей задачи, либо добавление кода, регистрирующего фоновую задачу (включая дополнительное условие системы, если оно есть):

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> public static BackgroundTaskRegistration RegisterBackgroundTask(
>                                                 string taskEntryPoint,
>                                                 string name,
>                                                 IBackgroundTrigger trigger,
>                                                 IBackgroundCondition condition)
> {
>     //
>     // Check for existing registrations of this background task.
>     //
>
>     foreach (var cur in BackgroundTaskRegistration.AllTasks)
>     {
>
>         if (cur.Value.Name == taskName)
>         {
>             //
>             // The task is already registered.
>             //
>
>             return (BackgroundTaskRegistration)(cur.Value);
>         }
>     }
>
>     //
>     // Register the background task.
>     //
>
>     var builder = new BackgroundTaskBuilder();
>
>     builder.Name = name;
>
>     // in-process background tasks don't set TaskEntryPoint
>     if ( taskEntryPoint != null && taskEntryPoint != String.Empty)
>     {
>         builder.TaskEntryPoint = taskEntryPoint;
>     }
>     builder.SetTrigger(trigger);
>
>     if (condition != null)
>     {
>         builder.AddCondition(condition);
>     }
>
>     BackgroundTaskRegistration task = builder.Register();
>
>     return task;
> }
> ```
> ``` cpp
> BackgroundTaskRegistration^ MainPage::RegisterBackgroundTask(
>                                              Platform::String ^ taskEntryPoint,
>                                              Platform::String ^ taskName,
>                                              IBackgroundTrigger ^ trigger,
>                                              IBackgroundCondition ^ condition)
> {
>
>     //
>     // Check for existing registrations of this background task.
>     //
>
>     auto iter   = BackgroundTaskRegistration::AllTasks->First();
>     auto hascur = iter->HasCurrent;
>
>     while (hascur)
>     {
>         auto cur = iter->Current->Value;
>
>         if(cur->Name == name)
>         {
>             //
>             // The task is registered.
>             //
>
>             return (BackgroundTaskRegistration ^)(cur);
>         }
>
>         hascur = iter->MoveNext();
>     }
>
>     //
>     // Register the background task.
>     //
>
>     auto builder = ref new BackgroundTaskBuilder();
>
>     builder->Name = name;
>     builder->TaskEntryPoint = taskEntryPoint;
>     builder->SetTrigger(trigger);
>
>     if (condition != nullptr) {
>         
>         builder->AddCondition(condition);
>     }
>
>     BackgroundTaskRegistration ^ task = builder->Register();
>
>     return task;
> }
> ```

## <a name="complete-background-task-registration-utility-function"></a>Полная служебная функция регистрации фоновой задачи

Этот пример демонстрирует полную функцию регистрации фоновой задачи. Эту функцию можно использовать для регистрации большинства фоновых задач, за исключением сетевых.

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> //
> // Register a background task with the specified taskEntryPoint, name, trigger,
> // and condition (optional).
> //
> // taskEntryPoint: Task entry point for the background task.
> // taskName: A name for the background task.
> // trigger: The trigger for the background task.
> // condition: Optional parameter. A conditional event that must be true for the task to fire.
> //
> public static BackgroundTaskRegistration RegisterBackgroundTask(string taskEntryPoint,
>                                                                 string taskName,
>                                                                 IBackgroundTrigger trigger,
>                                                                 IBackgroundCondition condition)
> {
>     //
>     // Check for existing registrations of this background task.
>     //
>
>     foreach (var cur in BackgroundTaskRegistration.AllTasks)
>     {
>
>         if (cur.Value.Name == taskName)
>         {
>             //
>             // The task is already registered.
>             //
>
>             return (BackgroundTaskRegistration)(cur.Value);
>         }
>     }
>
>     //
>     // Register the background task.
>     //
>
>     var builder = new BackgroundTaskBuilder();
>
>     builder.Name = taskName;
>     builder.TaskEntryPoint = taskEntryPoint;
>     builder.SetTrigger(trigger);
>
>     if (condition != null)
>     {
>
>         builder.AddCondition(condition);
>     }
>
>     BackgroundTaskRegistration task = builder.Register();
>
>     return task;
> }
> ```
> ``` cpp
> //
> // Register a background task with the specified taskEntryPoint, name, trigger,
> // and condition (optional).
> //
> // taskEntryPoint: Task entry point for the background task.
> // taskName: A name for the background task.
> // trigger: The trigger for the background task.
> // condition: Optional parameter. A conditional event that must be true for the task to fire.
> //
> BackgroundTaskRegistration^ MainPage::RegisterBackgroundTask(Platform::String ^ taskEntryPoint,
>                                                              Platform::String ^ taskName,
>                                                              IBackgroundTrigger ^ trigger,
>                                                              IBackgroundCondition ^ condition)
> {
>
>     //
>     // Check for existing registrations of this background task.
>     //
>
>     auto iter   = BackgroundTaskRegistration::AllTasks->First();
>     auto hascur = iter->HasCurrent;
>
>     while (hascur)
>     {
>         auto cur = iter->Current->Value;
>
>         if(cur->Name == name)
>         {
>             //
>             // The task is registered.
>             //
>
>             return (BackgroundTaskRegistration ^)(cur);
>         }
>
>         hascur = iter->MoveNext();
>     }
>
>
>     //
>     // Register the background task.
>     //
>
>     auto builder = ref new BackgroundTaskBuilder();
>
>     builder->Name = name;
>     builder->TaskEntryPoint = taskEntryPoint;
>     builder->SetTrigger(trigger);
>
>     if (condition != nullptr) {
>
>         builder->AddCondition(condition);
>     }
>
>     BackgroundTaskRegistration ^ task = builder->Register();
>
>     return task;
> }
> ```

## <a name="related-topics"></a>См. также

* [Создание и регистрация внепроцессной фоновой задачи](create-and-register-a-background-task.md)
* [Создание и регистрация фоновой задачи, выполняемой внутри процесса](create-and-register-an-inproc-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Активация приостановки, возобновления и фоновых событий для приложений универсальной платформы Windows (при отладке)](https://go.microsoft.com/fwlink/p/?linkid=254345)
