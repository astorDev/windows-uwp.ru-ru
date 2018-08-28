---
author: PatrickFarley
ms.assetid: 67a46812-881c-404b-9f3b-c6786f39e72b
title: Создание пользовательского рабочего процесса печати
description: Создайте пользовательский рабочий процесс печати, чтобы удовлетворить потребности вашей организации.
ms.author: pafarley
ms.date: 08/10/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, печати
ms.localizationpriority: medium
ms.openlocfilehash: 9e53c15b01a08c8c617529fe074929ce89a68ce9
ms.sourcegitcommit: 9a17266f208ec415fc718e5254d5b4c08835150c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "2889526"
---
# <a name="customize-the-print-workflow"></a>Создание пользовательского рабочего процесса печати

## <a name="overview"></a>Обзор
Разработчики могут настраивать рабочий процесс печати, используя приложение рабочего процесса печати. Приложения рабочего процесса печати— это приложения UWP, расширяющие функциональность [приложений устройств Microsoft Store (WSDA)](https://docs.microsoft.com/windows-hardware/drivers/devapps/), поэтому целесообразно ознакомиться с WSDA, прежде чем продолжать. 

Как и в случае с WSDA, когда пользователь исходного приложения решает напечатать что-либо и осуществляет навигацию по диалоговому окну печати, система проверяет, связано ли приложение рабочего процесса с принтером. Если да, запускается приложение рабочего процесса печати (в основном, как фоновая задача, см. подробности ниже). Приложение рабочего процесса может изменить и заявку на печать (XML-документ, настраивающий параметры принтера для текущей задачи печати), и фактическое содержимое XPS для печати. Приложение может предоставить эту функциональность пользователю, запустив пользовательский интерфейс посередине процесса. Выполнив свою работу, приложение передает содержимое для печати и заявку на печать драйверу.

Поскольку приложение содержит фоновые компоненты и компоненты переднего плана и поскольку его функциональность связана с другими приложениями, приложение рабочего процесса печати может быть более сложным в реализации, чем другие категории приложений UWP. Рекомендуется изучить [пример приложения рабочего процесса](https://github.com/Microsoft/print-oem-samples), читая это руководство, чтобы лучше понять, как реализовывать разные функции. Некоторые функции, такие как различные проверки ошибок и управление пользовательским интерфейсом, отсутствуют в этом руководстве для простоты.

## <a name="getting-started"></a>Начало работы

Приложение рабочего процесса должно указать свою точку входа системе печати, чтобы его можно было запустить в нужное время. Для этого в элемент `Application/Extensions` файла *package.appxmanifest* проекта UWP вставляется следующая декларация. 

```xml
<uap:Extension Category="windows.printWorkflowBackgroundTask"  
    EntryPoint="WFBackgroundTasks.WfBackgroundTask" />
```

> [!IMPORTANT] 
> Существует много сценариев, в которых настройка печати не требует пользовательского ввода. По этой причине приложения рабочего процесса печати выполняются в виде фоновых задач по умолчанию.

Если приложение рабочего процесса связано с исходным приложением, которое запустило задание печати (см. следующий раздел для получения инструкций), система печати изучает файлы манифеста, чтобы узнать точку входа фоновой задачи.

## <a name="do-background-work-on-the-print-ticket"></a>Выполнение фоновой работы в заявке печати

В первую очередь система печати активирует фоновую задачу в приложении рабочего процесса (в данном случае это класс `WfBackgroundTask` в пространстве имен `WFBackgroundTasks`). В методе `Run` фоновой задачи необходимо привести сведения о триггере задачи в качестве экземпляра **[PrintWorkflowTriggerDetails](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowtriggerdetails)**. Это предоставляет специальные функции для фоновой задачи рабочего процесса печати. Она предоставляет свойство **[PrintWorkflowSession](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowtriggerdetails.PrintWorkflowSession)**, которое является экземпляром **[PrintWorkFlowBackgroundSession](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowbackgroundsession)**. Классы сеанса рабочего процесса печати (фоновые и переднего плана) контролируют последовательные шаги приложения рабочего процесса печати. 

Затем необходимо зарегистрировать методы обработки для двух событий, вызываемых классом сеанса. Эти методы определяются позже.

```csharp
public void Run(IBackgroundTaskInstance taskInstance) {
    // Take out a deferral here and complete once all the callbacks are done
    runDeferral = taskInstance.GetDeferral();

    // Associate a cancellation handler with the background task.
    taskInstance.Canceled += new BackgroundTaskCanceledEventHandler(OnCanceled);

    // cast the task's trigger details as PrintWorkflowTriggerDetails
    PrintWorkflowTriggerDetails workflowTriggerDetails = taskInstance.TriggerDetails as PrintWorkflowTriggerDetails;

    // Get the session manager, which is unique to this print job
    PrintWorkflowBackgroundSession sessionManager = workflowTriggerDetails.PrintWorkflowSession;

    // add the event handler callback routines
    sessionManager.SetupRequested += OnSetupRequested;
    sessionManager.Submitted += OnXpsOMPrintSubmitted;

    // Allow the event source to start
    // This call blocks until all of the workflow callbacks complete
    sessionManager.Start();
}
```

При вызове метода `Start` диспетчер сеанса сначала вызывает событие **[SetupRequested](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowbackgroundsession.SetupRequested)**. Это событие предоставляет общие сведения о задаче печати, а также заявку на печать. На этом этапе заявку на печать можно отредактировать в фоновом режиме. 

```csharp
private void OnSetupRequested(PrintWorkflowBackgroundSession sessionManager, PrintWorkflowBackgroundSetupRequestedEventArgs printTaskSetupArgs) {
    // Take out a deferral here and complete once all the callbacks are done
    Deferral setupRequestedDeferral = printTaskSetupArgs.GetDeferral();

    // Get general information about the source application, print job title, and session ID
    string sourceApplicationName = printTaskSetupArgs.Configuration.SourceAppDisplayName;
    string jobTitle = printTaskSetupArgs.Configuration.JobTitle;
    string sessionId = printTaskSetupArgs.Configuration.SessionId;

    // edit the print ticket
    WorkflowPrintTicket printTicket = printTaskSetupArgs.GetUserPrintTicketAsync();

    // ...
```

Очень важно, что приложение определяет, запускать ли компонент переднего плана, в обработке **SetupRequested**. Это может зависеть от настройки, которая была ранее сохранена в локальном хранилище, или от события, которое произошло при редактировании заявки на печать; либо это может быть статическая настройка определенного приложения.

```csharp
    // ...
    
    if (UIrequested) {
        printTaskSetupArgs.SetRequiresUI();

        // Any data that is to be passed to the foreground task must be stored the app's local storage.
        // It should be prefixed with the sourceApplicationName string and the SessionId string, so that
        // it can be identified as pertaining to this workflow app session.
    }

    // Complete the deferral taken out at the start of OnSetupRequested
    setupRequestedDeferral.Complete();
}
```

## <a name="do-foreground-work-on-the-print-job-optional"></a>Выполнение работы переднего плана в задании печати (дополнительно)

Если метод **[SetRequiresUI](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowbackgroundsetuprequestedeventargs.SetRequiresUI)** был вызван, система печати изучит файл манифеста, чтобы определить точку входа в приложение переднего плана. Элемент `Application/Extensions` файла *package.appxmanifest* должен содержать следующие строки. Замените значение `EntryPoint` именем приложения переднего плана.

```xml
<uap:Extension Category="windows.printWorkflowForegroundTask"  
    EntryPoint="MyWorkFlowForegroundApp.App" /> 
```

Затем система печати вызывает метод **OnActivated** для точки входа заданного приложения. В методе **OnActivated** файла _App.xaml.cs_ приложение рабочего процесса должно проверить тип активации, чтобы убедиться, что это активация рабочего процесса. Если так, то приложение рабочего процесса может привести аргументы активации к объекту **[PrintWorkflowUIActivatedEventArgs](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowuiactivatedeventargs)**, который предоставляет объект **[PrintWorkflowForegroundSession](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowforegroundsession)** в качестве свойства. Этот объект, как и его фоновый аналог в предыдущем разделе, содержит события, которые вызываются системой печати, и им можно назначить обработчики. В этом случае функция обработки событий будет реализована в отдельном классе с названием `WorkflowPage`.

Сначала выполните следующее в файле _App.xaml.cs_:

```csharp
protected override void OnActivated(IActivatedEventArgs args){

    if (args.Kind == ActivationKind.PrintWorkflowForegroundTask) {

        // the app should instantiate a new UI view so that it can properly handle the case when 
        // several print jobs are active at the same time.
        Frame rootFrame = new Frame();
        if (null == Window.Current.Content)
        {
            rootFrame.Navigate(typeof(WorkflowPage));
            Window.Current.Content = rootFrame;
        }

        // Get the main page
        WorkflowPage workflowPage = (WorkflowPage)rootFrame.Content;

        // Make sure the page knows it's handling a foreground task activation
        workflowPage.LaunchType = WorkflowPage.WorkflowPageLaunchType.ForegroundTask;

        // Get the activation arguments
        PrintWorkflowUIActivatedEventArgs printTaskUIEventArgs = args as PrintWorkflowUIActivatedEventArgs;

        // Get the session manager
        PrintWorkflowForegroundSession taskSessionManager = printTaskUIEventArgs.PrintWorkflowSession;

        // Add the callback handlers - these methods are in the workflowPage class
        taskSessionManager.SetupRequested += workflowPage.OnSetupRequested;
        taskSessionManager.XpsDataAvailable += workflowPage.OnXpsDataAvailable;

        // start raising the print workflow events
        taskSessionManager.Start();
    }
}
```

После прикрепления обработчиков событий к пользовательскому интерфейсу и завершению метода **OnActivated** система печати запустит событие **[SetupRequested](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowforegroundsession.SetupRequested)**, которое должно быть обработано в пользовательском интерфейсе. Это событие предоставляет те же данные, что и событие настройки фоновой задачи, включая сведения о задании печати и документ заявки на печать, но без возможности запросить запуск дополнительного пользовательского интерфейса. В файле _WorkflowPage.xaml.cs_:

```csharp
internal void OnSetupRequested(PrintWorkflowForegroundSession sessionManager, PrintWorkflowForegroundSetupRequestedEventArgs printTaskSetupArgs) {
    // If anything asynchronous is going to be done, you need to take out a deferral here,
    // since otherwise the next callback happens once this one exits, which may be premature
    Deferral setupRequestedDeferral = printTaskSetupArgs.GetDeferral();

    // Get information about the source application, print job title, and session ID
    string sourceApplicationName = printTaskSetupArgs.Configuration.SourceAppDisplayName;
    string jobTitle = printTaskSetupArgs.Configuration.JobTitle;
    string sessionId = printTaskSetupArgs.Configuration.SessionId;
    // the following string should be used when storing data that pertains to this workflow session
    // (such as user input data that is meant to change the print content later on)
    string localStorageVariablePrefix = string.Format("{0}::{1}::", sourceApplicationName, sessionID);
    
    try
    {
        // receive and store user input
        // ...
    }
    catch (Exception ex)
    {
        string errorMessage = ex.Message;
        Debug.WriteLine(errorMessage);
    }
    finally
    {
        // Complete the deferral taken out at the start of OnSetupRequested
        setupRequestedDeferral.Complete();
    }
}
```

Затем система печати создаст событие **[XpsDataAvailable](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowforegroundsession.XpsDataAvailable)** для пользовательского интерфейса. В обработчике для этого события приложение рабочего процесса может осуществлять доступ ко всем данным, доступным событию настройки, и дополнительно считывать данные XPS напрямую (в виде потока необработанных байтов или объектной модели). Благодаря доступу к данным XPS в пользовательском интерфейсе доступен предварительный просмотр файлов и пользователю предоставляется информация об операциях, которые приложение рабочего процесса будет выполнять с данными. 

В составе этого обработчика событий приложение рабочего процесса должно получить отсроченный объект, если он продолжит взаимодействовать с пользователем. Без отсрочки система печати будет считать задачу пользовательского интерфейса завершенной, когда обработчик события **XpsDataAvailable** завершит работу или вызовет асинхронный метод. Как только приложение получило всю необходимую информацию от взаимодействия пользователя с пользовательским интерфейсом, Оно должно завершить отсрочку, чтобы система печати могла продолжить работу.

```csharp
internal async void OnXpsDataAvailable(PrintWorkflowForegroundSession sessionManager, PrintWorkflowXpsDataAvailableEventArgs printTaskXpsAvailableEventArgs)
{
    // Take out a deferral
    Deferral xpsDataAvailableDeferral = printTaskXpsAvailableEventArgs.GetDeferral();

    SpoolStreamContent xpsStream = printTaskXpsAvailableEventArgs.Operation.XpsContent.GetSourceSpoolDataAsStreamContent(); 
 
    IInputStream inputStream = xpsStream.GetInputSpoolStream(); 
 
    using (var inputReader = new Windows.Storage.Streams.DataReader(inputStream)) 
    { 
        // Read the XPS data from input stream 
        byte[] xpsData = new byte[inputReader.UnconsumedBufferLength]; 
        while (inputReader.UnconsumedBufferLength > 0) 
        { 
            inputReader.ReadBytes(xpsData); 
            // Do something with the XPS data, e.g. preview 
            // ...                  
        } 
    } 

    // Complete the deferral taken out at the start of this method
    xpsDataAvailableDeferral.Complete();
}
```

Кроме того, экземпляр **[PrintWorkflowSubmittedOperation](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowsubmittedoperation)**, предоставляемый аргументами события, позволяет отменить задание печати или указать, что задание выполнено успешно, но выходное задание печати не требуется. Для этого нужно вызвать метод **[Complete](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowsubmittedoperation.Complete)** со значением **[PrintWorkflowSubmittedStatus](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowsubmittedstatus)**.

> [!NOTE]
> Если приложение рабочего процесса отменит задание печати, настоятельно рекомендуется, чтобы всплывающее уведомление проинформировало пользователя о причинах отмены задания. 


## <a name="do-final-background-work-on-the-print-content"></a>Выполнение заключительной фоновой работы с содержимым для печати

После того как пользовательский интерфейс завершил отсрочку в событии **PrintTaskXpsDataAvailable** (или если этот шаг в пользовательском интерфейсе был обойден), система печати запустит для фоновой задачи событие **[Submitted](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowbackgroundsession.Submitted)**. В обработчике для этого события приложение рабочего процесса может получить доступ ко всем данным, предоставляемым событием **XpsDataAvailable**. Однако в отличие от предыдущих событий, **Submitted** также предоставляет доступ *на запись* к содержимому заключительного задания на печать через экземпляр **[PrintWorkflowTarget](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow.printworkflowtarget)**. 

Объект, который используется для буферизации данных для окончательной печати, зависит от того, осуществляется ли доступ к исходным данным в качестве потока необработанных байтов или объектной модели XPS. Когда приложение рабочего процесса осуществляет доступ к исходным данным через поток байтов, предоставляется выходной поток байтов для записи данных финального задания. Когда приложение рабочего процесса осуществляет доступ к исходным данным через объектную модель, предоставляется средство записи документов для записи объектов в выходное задание. В любом случае приложение рабочего потока должно считать все исходные данные, изменить любые необходимые данные и записать их в выходной целевой объект.

Как только фоновая задача завершит запись данных, она должна вызвать метод **Complete** в соответствующем объекте **PrintWorkflowSubmittedOperation**. После того как приложение рабочего процесса завершит этот шаг, а обработчик события **Submitted** закончит свою работу, сеанс рабочего процесса будет закрыт, а пользователь может отслеживать состояние заключительного задания печати в стандартных диалоговых окнах печати.

## <a name="final-steps"></a>Заключительные действия

### <a name="register-the-print-workflow-app-to-the-printer"></a>Регистрация приложения рабочего процесса печати на принтере

Приложение рабочего процесса связано с принтером с использованием того же типа отправки файлов метаданных, что и WSDA. Одно приложение UWP может одновременно функционировать и как приложение рабочего процесса, и как WSDA, предоставляющее функциональность параметров задания печати. Выполните [шаги в WSDA для создания связи метаданных](https://docs.microsoft.com/windows-hardware/drivers/devapps/step-2--create-device-metadata).

Разница в том, что WSDA автоматически активируются для пользователя (приложение будет всегда запускаться, когда пользователь выполняет печать на соответствующем устройстве), а приложения рабочего процесса— нет. У них есть отдельная политика, которую необходимо задать.

### <a name="set-the-workflow-apps-policy"></a>Настройка политики приложения рабочего процесса
Политика приложения рабочего процесса задается командами PowerShell на устройстве, которое будет выполнять приложение рабочего процесса. Команды Set-Printer, Add-Printer (существующий порт) и Add-Printer (новый порт WSD) будут изменены, чтобы можно было задать политики рабочего процесса. 
* `Disabled`: приложения рабочего процесса не будут активированы.
* `Uninitialized`: приложения рабочего процесса будут активированы, если DCA рабочих процессов установлена в системе. Если приложение не установлено, печать продолжится. 
* `Enabled`: контракт рабочего процесса будет активирован, если DCA рабочих процессов установлена в системе. Если приложение не установлено, произойдет сбой печати. 

Следующая команда делает приложение рабочего процесса обязательным на указанном принтере.
```Powershell
Set-Printer –Name "Microsoft XPS Document Writer" -WorkflowPolicy On
```

Локальный пользователь может выполнить эту политику на локальном принтере, или (в корпоративных системах) администратор принтера может выполнить эту политику на сервере печати. Эта политика затем будет синхронизирована со всеми клиентскими подключениями. Администратор принтера может использовать эту политику всякий раз при добавлении нового принтера.

## <a name="see-also"></a>См. также:

[Пример приложения рабочего процесса](https://github.com/Microsoft/print-oem-samples)

[Пространство имен Windows.Graphics.Printing.Workflow](https://docs.microsoft.com/uwp/api/windows.graphics.printing.workflow)


