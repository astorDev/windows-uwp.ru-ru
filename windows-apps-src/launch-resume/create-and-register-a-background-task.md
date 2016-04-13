---
title: Создание и регистрация фоновой задачи
description: Создайте класс фоновой задачи и зарегистрируйте его выполнение, когда приложение не работает на переднем плане.
ms.assetid: 4F98F6A3-0D3D-4EFB-BA8E-30ED37AE098B
---

# Создание и регистрация фоновой задачи


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794)
-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
-   [**BackgroundTaskCompletedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224781)

Создайте класс фоновой задачи и зарегистрируйте его выполнение, когда приложение не работает на переднем плане.

## Создание класса фоновой задачи


Для выполнения кода в фоновом режиме можно создавать классы, в которых реализован интерфейс [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794). Этот код будет запускаться при активации определенного события, например при помощи [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839) или [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517).

Далее будет показано, как создать новый класс, реализующий интерфейс [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794). Перед началом работы создайте в вашем решении новый проект для фоновых задач. Добавьте новый пустой класс для фоновой задачи и импортируйте пространство имен [Windows.ApplicationModel.Background](https://msdn.microsoft.com/library/windows/apps/br224847).

1.  Создайте новый проект для фоновых задач и добавьте его в решение. Для этого щелкните правой кнопкой мыши узел решений в **обозревателе решений** и выберите «Добавить» -> «Новый проект». Затем выберите тип проекта **Компонент среды выполнения Windows (универсальные приложения для Windows)**, задайте имя проекта и нажмите кнопку «ОК».
2.  Создайте ссылку на проект фоновых задач в проекте приложения UWP.

    В случае с приложением на C++ щелкните в проекте приложения правой кнопкой мыши и выберите **Свойства**. Перейдите в **Общие свойства** и выберите **Добавить новую ссылку**, установите флажок рядом с вашим проектом фоновых задач и нажмите **ОК** в обоих диалоговых окнах.

    В случае приложения на C# в проекте приложения щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить новую ссылку**. В разделе **Решение** выберите **Проекты**, затем выберите имя своего проекта фоновых задач и нажмите **ОК**.

3.  Создайте новый класс, реализующий интерфейс [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794). Метод [**Run**](https://msdn.microsoft.com/library/windows/apps/br224811) — это обязательная точка входа, которая будет вызываться при активации указанного события. Этот метод необходим в каждой фоновой задаче.

    > **Примечание.** Класс фоновой задачи сам по себе и все остальные классы в фоновой задаче должны быть **открытыми** **запечатанными** классами.

    В следующем примере кода показана очень простая отправная точка для класса фоновой задачи.

> [!div class="tabbedCodeSnippets"]
> ```cs
>     //
>     // ExampleBackgroundTask.cs
>     //
> 
>     using Windows.ApplicationModel.Background;
> 
>     namespace Tasks
>     {
>         public sealed class ExampleBackgroundTask : IBackgroundTask
>         {
>             public void Run(IBackgroundTaskInstance taskInstance)
>             {
>                 
>             }        
>         }
>     }
> ```
> ```cpp
>     //
>     // ExampleBackgroundTask.cpp
>     //
> 
>     #include "ExampleBackgroundTask.h"
> 
>     using namespace Tasks;
> 
>     void ExampleBackgroundTask::Run(IBackgroundTaskInstance^ taskInstance)
>     {
> 
>     }
>  ```

    
> ```cpp
>     //
>     // ExampleBackgroundTask.h
>     //
> 
>     #pragma once
> 
>     using namespace Windows::ApplicationModel::Background;
> 
>     namespace RuntimeComponent1
>     {
>         public ref class ExampleBackgroundTask sealed : public IBackgroundTask
>         {
> 
>         public:
>             ExampleBackgroundTask();
> 
>             virtual void Run(IBackgroundTaskInstance^ taskInstance);
>             void OnCompleted(
>                     BackgroundTaskRegistration^ task,
>                     BackgroundTaskCompletedEventArgs^ args
>                     );
>         };
>     }
> ```

4.  Если в вашей фоновой задаче выполняется асинхронный код, для нее необходимо использовать задержку. Если вы не используете задержку и если метод Run будет выполнен до завершения вызова асинхронного метода, то процесс фоновой задачи может неожиданно прерваться.

    Запросите задержку в методе Run перед вызовом асинхронного метода. Чтобы к задержке можно было получить доступ из асинхронного метода, сохраните ее в глобальной переменной. Объявите задержку завершенной после выполнения асинхронного кода.

    Следующий пример кода получает задержку, сохраняет и освобождает ее после выполнения асинхронного кода:

> [!div class="tabbedCodeSnippets"]
> ```cs
>     BackgroundTaskDeferral _deferral = taskInstance.GetDeferral(); // Note: define at class scope
>     public async void Run(IBackgroundTaskInstance taskInstance)
>     {
>         //
>         // TODO: Insert code to start one or more asynchronous methods using the
>         //       await keyword, for example:
>         //
>         // await ExampleMethodAsync();
>         //
>         
>         _deferral.Complete();
>     }
> ```
> ```cpp
>     BackgroundTaskDeferral^ deferral = taskInstance->GetDeferral(); // Note: define at class scope
>     void ExampleBackgroundTask::Run(IBackgroundTaskInstance^ taskInstance)
>     {
>         //
>         // TODO: Modify the following line of code to call a real async function.
>         //       Note that the task<void> return type applies only to async
>         //       actions. If you need to call an async operation instead, replace
>         //       task<void> with the correct return type.
>         //
>         task<void> myTask(ExampleFunctionAsync());
>         
>         myTask.then([=] () {
>             deferral->Complete();
>         });
>     }
> ```

    **Note**  In C#, your background task's asynchronous methods can be called using the **async/await** keywords. In C++, a similar result can be achieved by using a task chain.

    For more information on asynchronous patterns, see [Asynchronous programming](https://msdn.microsoft.com/library/windows/apps/mt187335). For additional examples of how to use deferrals to keep a background task from stopping early, see the [background task sample](http://go.microsoft.com/fwlink/p/?LinkId=618666).

Следующие действия выполняются в одном из классов вашего приложения (например, MainPage.xaml.cs).

> **Примечание.** Также можно создать функцию для регистрации фоновых задач — см. раздел [Регистрация фоновой задачи](register-a-background-task.md). В этом случае вместо выполнения следующих трех шагов вы можете просто создать триггер и назначить его функции регистрации, указав имя задачи, точку входа задачи и (не обязательно) условие.

 
**Регистрация фоновой задачи для запуска**

1.  Чтобы узнать, зарегистрирована ли уже фоновая задача, выполните итерацию по свойству [**BackgroundTaskRegistration.AllTasks**](https://msdn.microsoft.com/library/windows/apps/br224787). Это важный шаг: если приложение не проверяет, зарегистрирована ли уже фоновая задача, оно может выполнить регистрацию несколько раз, вызывая проблемы производительности и полное использование доступного задаче времени ЦП до завершения работы.

    В следующем примере выполняется итерация по свойству AllTasks и устанавливается флаг переменной, если задача уже зарегистрирована:

> [!div class="tabbedCodeSnippets"]
> ```cs
>     var taskRegistered = false;
>     var exampleTaskName = "ExampleBackgroundTask";
> 
>     foreach (var task in BackgroundTaskRegistration.AllTasks)
>     {
>         if (task.Value.Name == exampleTaskName)
>         {
>             taskRegistered = true;
>             break;
>         }
>     }
> ```
> ```cpp
>     boolean taskRegistered = false;
>     Platform::String^ exampleTaskName = "ExampleBackgroundTask";
> 
>     auto iter = BackgroundTaskRegistration::AllTasks->First();
>     auto hascur = iter->HasCurrent;
> 
>     while (hascur)
>     {
>         auto cur = iter->Current->Value;
> 
>         if(cur->Name == exampleTaskName)
>         {
>             taskRegistered = true;
>             break;
>         }
> 
>         hascur = iter->MoveNext();
>     }
> ```

2.  Если фоновая задача еще не зарегистрирована, используйте [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) для создания экземпляра фоновой задачи. Точка входа задачи является именем класса фоновых задач, перед которым располагается пространство имен.

    Триггер фоновой задачи определяет, когда должна быть запущена фоновая задача. Список возможных триггеров см. в статье [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839).

    Например, этот код создает новую фоновую задачу и запускает ее, когда срабатывает триггер **TimeZoneChanged**:

> [!div class="tabbedCodeSnippets"]
> ```cs
>     var builder = new BackgroundTaskBuilder();
> 
>     builder.Name = exampleTaskName;
>     builder.TaskEntryPoint = "RuntimeComponent1.ExampleBackgroundTask";
>     builder.SetTrigger(new SystemTrigger(SystemTriggerType.TimeZoneChange, false));
> ```
> ```cpp
>     auto builder = ref new BackgroundTaskBuilder();
> 
>     builder->Name = exampleTaskName;
>     builder->TaskEntryPoint = "RuntimeComponent1.ExampleBackgroundTask";
>     builder->SetTrigger(ref new SystemTrigger(SystemTriggerType::TimeZoneChange, false));
> ```

3.  Вы можете добавить условие, чтобы контролировать, в какой момент времени после возникновения события триггера запустится ваша задача (не обязательно). Например, если вы не хотите, чтобы задача запускалась в отсутствие пользователя, используйте условие **UserPresent**. Список возможных условий см. в статье [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

    Следующий пример кода назначает условие, при котором необходимо присутствие пользователя:

> [!div class="tabbedCodeSnippets"]
> ```cs
>     builder.AddCondition(new SystemCondition(SystemConditionType.UserPresent));
> ```
> ```cpp
>     builder->AddCondition(ref new SystemCondition(SystemConditionType::UserPresent));
> ```

4.  Зарегистрируйте фоновую задачу, вызвав метод Register в объекте [**BackgroundTaskBuilder **](https://msdn.microsoft.com/library/windows/apps/br224768). Сохраните результат выполнения [**BackgroundTaskRegistration**](https://msdn.microsoft.com/library/windows/apps/br224786), чтобы использовать его в следующем шаге.

    Следующий код регистрирует фоновую задачу и сохраняет результат:

> [!div class="tabbedCodeSnippets"]
>     ```cs
>     BackgroundTaskRegistration task = builder.Register();
>     ```
>     ```cpp
>     BackgroundTaskRegistration^ task = builder->Register();
>     ```

    > **Note**  Universal Windows apps must call [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) before registering any of the background trigger types.

    To ensure that your Universal Windows app continues to run properly after you release an update, you must call [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471) and then call [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) when your app launches after being updated. For more information, see [Guidelines for background tasks](guidelines-for-background-tasks.md).

## Обработка завершения фоновой задачи с помощью обработчиков событий


Следует зарегистрировать метод с помощью [**BackgroundTaskCompletedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224781), чтобы ваше приложение могло получить результаты от фоновой задачи. При запуске или возобновлении приложения будет вызван метод mark, если фоновая задача завершилась с момента последней работы приложения на переднем плане. (Метод OnCompleted будет вызван немедленно, если фоновая задача завершается во время работы приложения на переднем плане в настоящее время.)

1.  Создайте метод OnCompleted для обработки завершения фоновых задач. Например, результат фоновой задачи может быть причиной обновления пользовательского интерфейса. Представленный здесь объем памяти метода необходим для метода обработчика событий OnCompleted, даже если в этом примере не используется параметр *args*.

    Следующий пример кода распознает завершение фоновой задачи и вызывает пример метода (принимающего строку сообщения) для обновления пользовательского интерфейса.

> [!div class="tabbedCodeSnippets"]
>     ```cs
>     private void OnCompleted(IBackgroundTaskRegistration task, BackgroundTaskCompletedEventArgs args)
>     {
>         var settings = Windows.Storage.ApplicationData.Current.LocalSettings;
>         var key = task.TaskId.ToString();
>         var message = settings.Values[key].ToString();
>         UpdateUI(message);
>     }
>     ```
>     ```cpp
>     void ExampleBackgroundTask::OnCompleted(BackgroundTaskRegistration^ task, BackgroundTaskCompletedEventArgs^ args)
>     {
>         auto settings = ApplicationData::Current->LocalSettings->Values;
>         auto key = task->TaskId.ToString();
>         auto message = dynamic_cast<String^>(settings->Lookup(key));
>         UpdateUI(message);
>     }
>     ```

    > **Note**  UI updates should be performed asynchronously, to avoid holding up the UI thread. For an example, see the UpdateUI method in the [background task sample](http://go.microsoft.com/fwlink/p/?LinkId=618666).

     

2.  Вернитесь к тому месту, где вы регистрировали фоновую задачу. После этой строки кода добавьте новый объект [**BackgroundTaskCompletedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224781). Предоставьте свой метод OnCompleted в качестве параметра для конструктора **BackgroundTaskCompletedEventHandler**.

    Следующий пример кода добавляет [**BackgroundTaskCompletedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224781) в [**BackgroundTaskRegistration**](https://msdn.microsoft.com/library/windows/apps/br224786).

> [!div class="tabbedCodeSnippets"]
>     ```cs
>     task.Completed += new BackgroundTaskCompletedEventHandler(OnCompleted);
>     ```
>     ```cpp
>     task->Completed += ref new BackgroundTaskCompletedEventHandler(this, &amp;ExampleBackgroundTask::OnCompleted);
>     ```

## Объявление использования приложением фоновых задач в манифесте приложения


Чтобы приложение могло выполнять фоновые задачи, вам необходимо сперва объявить каждую такую задачу в манифесте приложения. Если приложение попытается зарегистрировать фоновую задачу с триггером, который не указан в манифесте, регистрация не будет выполнена.

1.  Откройте конструктор манифеста пакета, запустив файл Package.appxmanifest.
2.  Перейдите на вкладку **Объявления**.
3.  В раскрывающемся списке **Доступные объявления** выберите **Фоновые задачи** и щелкните **Добавить**.
4.  Установите флажок **Системное событие**.
5.  В текстовом поле **Точка входа:** введите пространство имен и имя вашего фонового класса (в этом примере использовано имя RuntimeComponent1.ExampleBackgroundTask).
6.  Закройте конструктор манифестов.

    Следующий элемент расширений добавляется в файл Package.appxmanifest для регистрации фоновой задачи:

    ```xaml
    <Extensions>
      <Extension Category="windows.backgroundTasks" EntryPoint="RuntimeComponent1.ExampleBackgroundTask">
        <BackgroundTasks>
          <Task Type="systemEvent" />
        </BackgroundTasks>
      </Extension>
    </Extensions>
    ```

## Сводка и дальнейшие действия


Теперь вы понимаете, как создавать класс фоновой задачи, как регистрировать фоновую задачу из приложения и как сделать так, чтобы приложение распознавало ее завершение. Вы также знаете, как обновить манифест приложения, чтобы приложение могло успешно регистрировать фоновые задачи.

> **Примечание.** Скачайте [пример фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666), чтобы ознакомиться с похожими примерами кода в контексте полного и надежного приложения UWP, использующего фоновые задачи.

 

В статьях ниже можно найти справочник по API, концептуальное руководство по фоновым задачам и подробные инструкции по созданию приложений, использующих фоновые задачи.

> **Примечание.** Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

## Ссылки по теме


**Учебные статьи с подробными сведениями о фоновых задачах**

* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)

**Руководство по фоновым задачам**

* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Активация событий приостановки, возобновления и перевода в фоновый режим приложений Магазина Windows (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)

**Справочник по API для фоновых задач**

* [**Windows.ApplicationModel.Background**](https://msdn.microsoft.com/library/windows/apps/br224847)

 

 





<!--HONumber=Mar16_HO1-->


