---
title: Регистрация фоновой задачи
description: Сведения о создании функции, которую можно многократно использовать для безопасной регистрации большинства фоновых задач.
ms.assetid: 8B1CADC5-F630-48B8-B3CE-5AB62E3DFB0D
---

# Регистрация фоновой задачи


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**Класс BackgroundTaskRegistration**](https://msdn.microsoft.com/library/windows/apps/br224786)
-   [**Класс BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
-   [**Класс SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834)

Сведения о создании функции, которую можно многократно использовать для безопасной регистрации большинства фоновых задач.

В этом разделе предполагается, что вы уже создали фоновую задачу, которую нужно зарегистрировать. (Сведения о написании фоновой задачи cм. в разделе [Создание и регистрация фоновой задачи](create-and-register-a-background-task.md).)

Этот раздел содержит краткий обзор служебной функции, которая регистрирует фоновые задачи. Прежде чем несколько раз зарегистрировать задачу, эта служебная функция проверяет наличие существующих регистраций, чтобы избежать проблем, связанных с многократными регистрациями. Она может также применять условие системы к фоновой задаче. Этот обзор включает полный рабочий пример данной служебной функции.

**Примечание**  

Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров.

Чтобы универсальное приложение для Windows продолжало правильно работать после выпуска обновления, необходимо вызвать метод [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471), а затем — метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) при запуске приложения после обновления. Подробнее см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

## Определение подписи и типа возвращаемых данных метода


Этот метод принимает точку входа задачи, имя задачи, предварительно построенный триггер фоновой задачи и (необязательно) условие [**SystemCondition**](https://msdn.microsoft.com/library/windows/apps/br224834) для фоновой задачи. Этот метод возвращает объект [**BackgroundTaskRegistration**](https://msdn.microsoft.com/library/windows/apps/br224786).

> [!div class="tabbedCodeSnippets"]
> ```cs
> public static BackgroundTaskRegistration RegisterBackgroundTask(
>                                                 string taskEntryPoint, 
>                                                 string name,
>                                                 IBackgroundTrigger trigger,
>                                                 IBackgroundCondition condition)
> {
>     
>     // We’ll add code to this function in subsequent steps.
> 
> }
> ```
> ```cpp
> BackgroundTaskRegistration^ MainPage::RegisterBackgroundTask(
>                                              Platform::String ^ taskEntryPoint,
>                                              Platform::String ^ taskName,
>                                              IBackgroundTrigger ^ trigger,
>                                              IBackgroundCondition ^ condition)
> {
>     
>     // We’ll add code to this function in subsequent steps.
> 
> }
> ```

## Проверка наличия существующих регистраций


Проверьте, не зарегистрирована ли уже задача. Это важно проверить, потому что при многократной регистрации задачи она будет выполняться несколько раз при каждом срабатывании триггера, что может привести к избыточному потреблению ресурсов ЦП и непредсказуемому поведению.

Для проверки наличия существующих регистраций можно запросить свойство [**BackgroundTaskRegistration.AllTasks**](https://msdn.microsoft.com/library/windows/apps/br224787) и выполнить итерацию по результату. Проверьте имя каждого экземпляра: если оно совпадает с именем регистрируемой задачи, следует прервать цикл и установить переменную флага, чтобы вашему коду удалось выбрать другой путь в следующем шаге.

> **Примечание.**  Используйте имена фоновых задач, которые уникальны для вашего приложения. Убедитесь, что каждая фоновая задача имеет уникальное имя.

Следующий код регистрирует фоновую задачу с использованием триггера [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224838), который мы создали в последнем шаге:

> [!div class="tabbedCodeSnippets"]
> ```cs
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
>     // We’ll register the task in the next step.
> }
> ```
> ```cpp
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
>     // We’ll register the task in the next step.
> }
> ```

## Регистрация фоновой задачи (или возврат существующей регистрации)


Проверьте, не найдена ли задача в списке существующих регистраций фоновых задач. Если да, верните этот экземпляр задачи.

Затем зарегистрируйте задачу с использованием нового объекта [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768). Этот код должен проверить, имеет ли параметр условия значение NULL, и, если нет, добавить условие к объекту регистрации. Верните объект [**BackgroundTaskRegistration**](https://msdn.microsoft.com/library/windows/apps/br224786), возвращенный методом [**BackgroundTaskBuilder.Register**](https://msdn.microsoft.com/library/windows/apps/br224772).

> **Примечание.**  Параметры регистрации фоновых задач проверяются во время регистрации. Если какие-либо из параметров регистрации оказываются недопустимыми, возвращается ошибка. Убедитесь, что ваше приложение корректно обрабатывает сценарии, в которых регистрация фоновой задачи завершается ошибкой. Если работа вашего приложения зависит от наличия допустимого объекта регистрации после попытки регистрации задачи, то оно может дать сбой.

В следующем примере выполняется либо возврат существующей задачи, либо добавление кода, регистрирующего фоновую задачу (включая дополнительное условие системы, если оно есть):

> [!div class="tabbedCodeSnippets"]
> ```cs
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
> ```cpp
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

## Полная служебная функция регистрации фоновой задачи


Этот пример демонстрирует полную функцию регистрации фоновой задачи. Эту функцию можно использовать для регистрации большинства фоновых задач, за исключением сетевых.

> [!div class="tabbedCodeSnippets"]
> ```cs
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
> ```cpp
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

> **Примечание.**  Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 
## Связанные разделы


****

* [Создание и регистрация фоновой задачи](create-and-register-a-background-task.md)
* [Объявление фоновых задач в манифесте приложения](declare-background-tasks-in-the-application-manifest.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Обновление живой плитки из фоновой задачи](update-a-live-tile-from-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)
* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)

****

* [Отладка фоновой задачи](debug-a-background-task.md)
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях Магазина Windows (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)

 

 





<!--HONumber=Mar16_HO1-->


