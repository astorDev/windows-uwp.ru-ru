---
description: Чтобы продолжить сетевое взаимодействие, пока оно не в фоновом режиме, приложение может использовать фоновые задачи и посредник сокетов или триггеры канала управления.
title: Передача данных по сети в фоновом режиме
ms.assetid: 537F8E16-9972-435D-85A5-56D5764D3AC2
ms.date: 06/14/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b0246e338c13027f8afc8da4aa919faa0911b39c
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66371622"
---
# <a name="network-communications-in-the-background"></a>Передача данных по сети в фоновом режиме
Чтобы продолжить сетевое взаимодействие, пока оно не на переднем плане, приложение может использовать фоновые задачи и один из таких двух вариантов.
- Брокер сокетов. Если ваши приложения используют сокеты для продолжительных соединений, они могут делегировать право собственности системному брокеру сокетов, когда покидают передний план. Затем брокер активирует приложение, когда трафик поступает в сокет, передает право собственности приложению, и приложение обрабатывает входящий трафик.
- Триггеры канала управления. 

## <a name="performing-network-operations-in-background-tasks"></a>Выполнение сетевых операций в фоновых задачах
- Используйте [SocketActivityTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.socketactivitytrigger), чтобы активировать фоновую задачу при получении пакета и возникновении необходимости выполнить кратковременную задачу. После выполнения задачи фоновая задача должна завершить работу для экономии энергии.
- Используйте [ControlChannelTrigger](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger), чтобы активировать фоновую задачу при получении пакета и возникновении необходимости выполнить долговременную задачу.

**Условия и флаги, связанные с сетью**

- Добавьте условие **InternetAvailable** в фоновую задачу [BackgroundTaskBuilder.AddCondition](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder), чтобы задержать активацию фоновой задачи до запуска сетевого стека. Это условие экономит энергию, так как фоновая задача не будет выполняться, пока сеть недоступна. Это условие не поддерживает активацию в режиме реального времени.

Независимо от используемого триггера задайте условие [IsNetworkRequested](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskbuilder) для фоновой задачи, чтобы обеспечить сохранность подключения к сети, пока выполняется фоновая задача. Это указывает инфраструктуре фоновых задач на необходимость поддержания соединения во время выполнения задачи, даже если устройство переходит в режим ожидания с подключением. Если ваша фоновая задача не использует **IsNetworkRequested**, она не сможет получить доступ к сети в режиме ожидания с подключением (например, при выключении экрана телефона).

## <a name="socket-broker-and-the-socketactivitytrigger"></a>Брокер сокетов и SocketActivityTrigger
Если ваше приложение использует соединения [**DatagramSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.DatagramSocket), [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket) или [**StreamSocketListener**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocketListener), необходимо использовать [**SocketActivityTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SocketActivityTrigger) и брокер сокетов, чтобы получить уведомление при поступлении трафика для вашего приложения, когда оно не находится на переднем плане.

Чтобы ваше приложение получило и обработало данные, полученные на сокете, когда оно неактивно, оно должно выполнить одноразовую настройку при запуске, а затем передать право собственности на сокет брокеру сокетов, когда оно переходит в состояние, в котором оно неактивно.

Перечисленные ниже однократные действия настройки предназначены для создания триггера, регистрации фоновой задачи для триггера и включения сокета для брокера сокетов.
  - Создайте **SocketActivityTrigger** и зарегистрируйте фоновую задачу для триггера с помощью параметра TaskEntryPoint, настроенного согласно вашему коду для обработки полученного пакета.
```csharp
            var socketTaskBuilder = new BackgroundTaskBuilder();
            socketTaskBuilder.Name = _backgroundTaskName;
            socketTaskBuilder.TaskEntryPoint = _backgroundTaskEntryPoint;
            var trigger = new SocketActivityTrigger();
            socketTaskBuilder.SetTrigger(trigger);
            _task = socketTaskBuilder.Register();
```
  - Вызовите **EnableTransferOwnership** на сокете, прежде чем привязывать сокет.
```csharp
           _tcpListener = new StreamSocketListener();

           // Note that EnableTransferOwnership() should be called before bind,
           // so that tcpip keeps required state for the socket to enable connected
           // standby action. Background task Id is taken as a parameter to tie wake pattern
           // to a specific background task.  
           _tcpListener. EnableTransferOwnership(_task.TaskId,SocketActivityConnectedStandbyAction.Wake);
           _tcpListener.ConnectionReceived += OnConnectionReceived;
           await _tcpListener.BindServiceNameAsync("my-service-name");
```

После надлежащей настройки сокета, когда ваше приложение будет близко к приостановке работы, вызовите **TransferOwnership** на сокете для его передачи брокеру сокетов. Брокер отслеживает сокет и активирует вашу фоновую задачу при получении данных. Следующий пример содержит служебную функцию **TransferOwnership** для выполнения передачи для сокетов **StreamSocketListener**. (Обратите внимание, что разные типы сокетов имеют собственный метод **TransferOwnership**. Поэтому необходимо вызывать метод, соответствующий сокету, право собственности на который вы передаете. Ваш код, возможно, содержит перегруженный вспомогательный элемент **TransferOwnership** с одной реализацией для каждого используемого типа сокета, чтобы код **OnSuspending** оставался легким для чтения.)

Приложение передает право собственности сокета брокеру сокетов и передает идентификатор фоновой задачи с помощью наиболее подходящего из следующих методов:
-   один из методов [**TransferOwnership**](https://docs.microsoft.com/uwp/api/windows.networking.sockets.datagramsocket.transferownership) в [**DatagramSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.DatagramSocket);
-   один из методов [**TransferOwnership**](https://docs.microsoft.com/uwp/api/windows.networking.sockets.streamsocket.transferownership) в [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket);
-   один из методов [**TransferOwnership**](https://docs.microsoft.com/uwp/api/windows.networking.sockets.streamsocketlistener.transferownership) в [**StreamSocketListener**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocketListener).

```csharp

// declare int _transferOwnershipCount as a field.

private void TransferOwnership(StreamSocketListener tcpListener)
{
    await tcpListener.CancelIOAsync();

    var dataWriter = new DataWriter();
    ++_transferOwnershipCount;
    dataWriter.WriteInt32(transferOwnershipCount);
    var context = new SocketActivityContext(dataWriter.DetachBuffer());
    tcpListener.TransferOwnership(_socketId, context);
}

private void OnSuspending(object sender, SuspendingEventArgs e)
{
    var deferral = e.SuspendingOperation.GetDeferral();

    TransferOwnership(_tcpListener);
    deferral.Complete();
}
```
В обработчике событий фоновой задачи
   -  Сначала получите отсрочку фоновой задачи, чтобы вы могли обрабатывать событие с использованием асинхронных методов.
```csharp
var deferral = taskInstance.GetDeferral();
```
   -  Далее, извлеките SocketActivityTriggerDetails из аргументов события и найдите причину возникновения события:
```csharp
var details = taskInstance.TriggerDetails as SocketActivityTriggerDetails;
    var socketInformation = details.SocketInformation;
    switch (details.Reason)
```
   -   Если событие возникло из-за действий сокета, создайте DataReader на сокете, асинхронно загрузите приложение для чтения, а затем используйте данные согласно дизайну приложения. Обратите внимание, что следует вернуть право собственности на сокет брокеру сокетов, чтобы получать уведомления о дальнейших действиях сокета.

   В следующем примере текст, полученный на сокете, отображается во всплывающем уведомлении.

```csharp
case SocketActivityTriggerReason.SocketActivity:
            var socket = socketInformation.StreamSocket;
            DataReader reader = new DataReader(socket.InputStream);
            reader.InputStreamOptions = InputStreamOptions.Partial;
            await reader.LoadAsync(250);
            var dataString = reader.ReadString(reader.UnconsumedBufferLength);
            ShowToast(dataString);
            socket.TransferOwnership(socketInformation.Id); /* Important! */
            break;
```

   -   Если событие возникло из-за истечения срока действия таймера активности, то ваш код должен отправить некоторые данные через сокет для обновления таймера активности и перезагрузки. Опять таки, важно вернуть право собственности на сокет брокеру сокетов, чтобы получать дальнейшие уведомления о событиях:

```csharp
case SocketActivityTriggerReason.KeepAliveTimerExpired:
            socket = socketInformation.StreamSocket;
            DataWriter writer = new DataWriter(socket.OutputStream);
            writer.WriteBytes(Encoding.UTF8.GetBytes("Keep alive"));
            await writer.StoreAsync();
            writer.DetachStream();
            writer.Dispose();
            socket.TransferOwnership(socketInformation.Id); /* Important! */
            break;
```

   -   Если событие возникло из-за закрытия сокета, повторно создайте сокет, убедившись, что после создания нового сокета вы передаете право собственности на него брокеру сокетов. В этом примере имя узла и порт хранятся в локальных параметрах, и их можно использовать для установки нового подключения через сокет:

```csharp
case SocketActivityTriggerReason.SocketClosed:
            socket = new StreamSocket();
            socket.EnableTransferOwnership(taskInstance.Task.TaskId, SocketActivityConnectedStandbyAction.Wake);
            if (ApplicationData.Current.LocalSettings.Values["hostname"] == null)
            {
                break;
            }
            var hostname = (String)ApplicationData.Current.LocalSettings.Values["hostname"];
            var port = (String)ApplicationData.Current.LocalSettings.Values["port"];
            await socket.ConnectAsync(new HostName(hostname), port);
            socket.TransferOwnership(socketId);
            break;
```

   -   Не забудьте выполнить свою отсрочку сразу после завершения обработки уведомлений о событиях:

```csharp
  deferral.Complete();
```

Полный пример, демонстрирующий использование [**SocketActivityTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SocketActivityTrigger) и брокера сокетов, см. в разделе [Пример SocketActivityStreamSocket](https://go.microsoft.com/fwlink/p/?LinkId=620606). Инициализация сокета выполняется в Scenario1\_Connect.xaml.cs, а реализация фоновой задачи — в SocketActivityTask.cs.

Возможно, вы заметите, что пример вызывает элемент **TransferOwnership**, как только создает новый или получает существующий сокет, вместо использования с этой целью обработчика событий **OnSuspending**, как описано в данном разделе. Это связано с тем, что в примере акцент сделан на демонстрации [**SocketActivityTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SocketActivityTrigger), а не использовании сокета для любых других действий, когда он запущен. Вероятно, ваше приложение будет более сложным и в нем необходимо будет использовать **OnSuspending** для определения того, когда вызывать **TransferOwnership**.

## <a name="control-channel-triggers"></a>Триггеры канала управления
Сначала убедитесь, что триггеры канала управления (CCT) используются соответствующим образом. Если вы используете соединения [**DatagramSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.DatagramSocket), [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket) или [**StreamSocketListener**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocketListener), мы рекомендуем использовать [**SocketActivityTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SocketActivityTrigger). Вы можете использовать CCT для **StreamSocket**, но они используют больше ресурсов и могут не работать в режиме ожидания с подключением.

Если вы используете WebSockets, [**IXMLHTTPRequest2**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nn-msxml6-ixmlhttprequest2), [**System.Net.Http.HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) или [**Windows.Web.Http.HttpClient**](/uwp/api/windows.web.http.httpclient), вы должны использовать [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger).

## <a name="controlchanneltrigger-with-websockets"></a>ControlChannelTrigger с WebSockets
При использовании [**MessageWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.MessageWebSocket) или [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) действуют особые условия. Существует ряд специфичных для транспорта шаблонов использования и рекомендаций, которыми следует руководствоваться при использовании **MessageWebSocket** или **StreamWebSocket** с **ControlChannelTrigger**. Кроме того, эти аспекты влияют на способ, которым обрабатываются запросы на получение пакетов в **StreamWebSocket**. Запросы на получение пакетов в **MessageWebSocket** остаются без изменений.

Ниже приведены шаблоны использования и рекомендации, которыми следует руководствоваться при использовании [**MessageWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.MessageWebSocket) или [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger).

-   Состояние получения ожидающего сокета должно поддерживаться в любом случае. Это необходимо для обеспечения запуска задач push-уведомлений.
-   Протокол WebSocket определяет стандартную модель для сообщений проверки активности. Класс [**WebSocketKeepAlive**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.WebSocketKeepAlive) может отправлять серверу инициированные клиентом сообщения проверки активности по протоколу WebSocket. Приложение должно зарегистрировать класс **WebSocketKeepAlive** в качестве TaskEntryPoint для KeepAliveTrigger.

Некоторые особые аспекты влияют на способ, которым обрабатываются запросы на получение пакетов в [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket). В частности, при использовании **StreamWebSocket** с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) приложение должно использовать необработанный асинхронный шаблон для обработки операций чтения вместо модели **await** в C# и VB.NET или задач в C++. Необработанный асинхронный шаблон проиллюстрирован в примере кода далее в этом разделе.

Использование необработанного асинхронного шаблона позволяет Windows синхронизировать метод [**IBackgroundTask.Run**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.) в фоновой задаче для [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) с возвратом обратного вызова завершения получения. Метод **Run** вызывается после возврата обратного вызова завершения. Это гарантирует, что приложение получит данные или ошибки до вызова метода **Run**.

Важно отметить, что приложению нужно зарегистрировать еще одну операцию чтения, прежде чем оно вернет управление от обратного вызова завершения. Кроме того, [**DataReader**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.DataReader) нельзя напрямую использовать с транспортом [**MessageWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.MessageWebSocket) или [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket), так как это нарушает синхронизацию, описанную выше. Использование метода [**DataReader.LoadAsync**](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader.loadasync) непосредственно с транспортом не поддерживается. Вместо этого объект [**IBuffer**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.IBuffer), возвращенный методом [**IInputStream.ReadAsync**](https://docs.microsoft.com/uwp/api/windows.storage.streams.iinputstream.readasync) в свойстве [**StreamWebSocket.InputStream**](https://docs.microsoft.com/uwp/api/windows.networking.sockets.streamwebsocket.inputstream), можно позднее передать в метод [**DataReader.FromBuffer**](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader.frombuffer) для дальнейшей обработки.

В следующем примере показано, как использовать необработанный асинхронный шаблон для обработки операций чтения в [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket).

```csharp
void PostSocketRead(int length)
{
    try
    {
        var readBuf = new Windows.Storage.Streams.Buffer((uint)length);
        var readOp = socket.InputStream.ReadAsync(readBuf, (uint)length, InputStreamOptions.Partial);
        readOp.Completed = (IAsyncOperationWithProgress<IBuffer, uint>
            asyncAction, AsyncStatus asyncStatus) =>
        {
            switch (asyncStatus)
            {
                case AsyncStatus.Completed:
                case AsyncStatus.Error:
                    try
                    {
                        // GetResults in AsyncStatus::Error is called as it throws a user friendly error string.
                        IBuffer localBuf = asyncAction.GetResults();
                        uint bytesRead = localBuf.Length;
                        readPacket = DataReader.FromBuffer(localBuf);
                        OnDataReadCompletion(bytesRead, readPacket);
                    }
                    catch (Exception exp)
                    {
                        Diag.DebugPrint("Read operation failed:  " + exp.Message);
                    }
                    break;
                case AsyncStatus.Canceled:

                    // Read is not cancelled in this sample.
                    break;
           }
       };
   }
   catch (Exception exp)
   {
       Diag.DebugPrint("failed to post a read failed with error:  " + exp.Message);
   }
}
```

Обработчик завершения чтения гарантированно сработает до вызова метода [**IBackgroundTask.Run**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.) в фоновой задаче для [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger). Внутренний механизм синхронизации Windows ожидает, пока приложение вернет управление от обратного вызова завершения чтения. Приложение обычно быстро обрабатывает данные или ошибку, полученные от [**MessageWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.MessageWebSocket) или [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket), в обратном вызове завершения чтения. Само сообщение обрабатывается в контексте метода **IBackgroundTask.Run**. В приведенном ниже примере этот момент проиллюстрирован с использованием очереди сообщений, в которую обработчик завершения чтения помещает сообщение и которую позднее обрабатывает фоновая задача.

В следующем примере показано, как использовать обработчик завершения чтения с необработанным асинхронным шаблоном для обработки операций чтения в [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket).

```csharp
public void OnDataReadCompletion(uint bytesRead, DataReader readPacket)
{
    if (readPacket == null)
    {
        Diag.DebugPrint("DataReader is null");

        // Ideally when read completion returns error,
        // apps should be resilient and try to
        // recover if there is an error by posting another recv
        // after creating a new transport, if required.
        return;
    }
    uint buffLen = readPacket.UnconsumedBufferLength;
    Diag.DebugPrint("bytesRead: " + bytesRead + ", unconsumedbufflength: " + buffLen);

    // check if buffLen is 0 and treat that as fatal error.
    if (buffLen == 0)
    {
        Diag.DebugPrint("Received zero bytes from the socket. Server must have closed the connection.");
        Diag.DebugPrint("Try disconnecting and reconnecting to the server");
        return;
    }

    // Perform minimal processing in the completion
    string message = readPacket.ReadString(buffLen);
    Diag.DebugPrint("Received Buffer : " + message);

    // Enqueue the message received to a queue that the push notify
    // task will pick up.
    AppContext.messageQueue.Enqueue(message);

    // Post another receive to ensure future push notifications.
    PostSocketRead(MAX_BUFFER_LENGTH);
}
```

Дополнительным компонентом для WebSocket является обработчик проверки активности. Протокол WebSocket определяет стандартную модель для сообщений проверки активности.

При использовании [**MessageWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.MessageWebSocket) или [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket) зарегистрируйте экземпляр класса [**WebSocketKeepAlive**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.WebSocketKeepAlive) как [**TaskEntryPoint**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskbuilder.taskentrypoint) для KeepAliveTrigger, чтобы приложение было возобновлено и периодически отправляло на сервер (на удаленную конечную точку) сообщения проверки активности. Это следует сделать в коде приложения фоновой регистрации, а также в манифесте пакета.

Данная точка входа задачи [**Windows.Sockets.WebSocketKeepAlive**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.WebSocketKeepAlive) должна быть указана в двух местах:

-   при создании триггера KeepAliveTrigger в исходном коде (см. пример ниже);
-   в манифесте пакета приложения для объявления фоновой задачи проверки активности.

В следующем примере в манифест приложения под элементом &lt;Application&gt; добавляются уведомление сетевого триггера и триггер проверки активности.

```xml
  <Extensions>
    <Extension Category="windows.backgroundTasks"
         Executable="$targetnametoken$.exe"
         EntryPoint="Background.PushNotifyTask">
      <BackgroundTasks>
        <Task Type="controlChannel" />
      </BackgroundTasks>
    </Extension>
    <Extension Category="windows.backgroundTasks"
         Executable="$targetnametoken$.exe"
         EntryPoint="Windows.Networking.Sockets.WebSocketKeepAlive">
      <BackgroundTasks>
        <Task Type="controlChannel" />
      </BackgroundTasks>
    </Extension>
  </Extensions>
```

Необходимо соблюдать особую осторожность при использовании оператора **await** в контексте [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) и асинхронной операции в [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket), [**MessageWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.MessageWebSocket) или [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket). При помощи объекта **Task&lt;bool&gt;** можно зарегистрировать **ControlChannelTrigger** для push-уведомлений и проверок активности WebSocket в **StreamWebSocket** и подключить транспорт. В рамках регистрации транспорт **StreamWebSocket** устанавливается как транспорт для **ControlChannelTrigger** и регистрируется операция чтения. **Task.Result** блокирует текущий поток, пока не будут выполнены все шаги задачи, и возвращает операторы в тексте сообщения. Задача не будет решена, пока метод не вернет либо «true», либо «false». Это гарантирует, что метод будет выполнен полностью. **Task** может содержать несколько операторов **await**, защищенных при помощи **Task**. Эту схему следует использовать с объектом **ControlChannelTrigger**, когда в качестве транспорта применяется **StreamWebSocket** или **MessageWebSocket**. В случае длительных операций (например, типичной операции асинхронного чтения) приложение должно использовать необработанный асинхронный шаблон, о котором шла речь выше.

В следующем примере [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) регистрируется для получения push-уведомлений и сообщений проверки активности по протоколу WebSocket в [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket).

```csharp
private bool RegisterWithControlChannelTrigger(string serverUri)
{
    // Make sure the objects are created in a system thread
    // Demonstrate the core registration path
    // Wait for the entire operation to complete before returning from this method.
    // The transport setup routine can be triggered by user control, by network state change
    // or by keepalive task
    Task<bool> registerTask = RegisterWithCCTHelper(serverUri);
    return registerTask.Result;
}

async Task<bool> RegisterWithCCTHelper(string serverUri)
{
    bool result = false;
    socket = new StreamWebSocket();

    // Specify the keepalive interval expected by the server for this app
    // in order of minutes.
    const int serverKeepAliveInterval = 30;

    // Specify the channelId string to differentiate this
    // channel instance from any other channel instance.
    // When background task fires, the channel object is provided
    // as context and the channel id can be used to adapt the behavior
    // of the app as required.
    const string channelId = "channelOne";

    // For websockets, the system does the keepalive on behalf of the app
    // But the app still needs to specify this well known keepalive task.
    // This should be done here in the background registration as well
    // as in the package manifest.
    const string WebSocketKeepAliveTask = "Windows.Networking.Sockets.WebSocketKeepAlive";

    // Try creating the controlchanneltrigger if this has not been already
    // created and stored in the property bag.
    ControlChannelTriggerStatus status;

    // Create the ControlChannelTrigger object and request a hardware slot for this app.
    // If the app is not on LockScreen, then the ControlChannelTrigger constructor will
    // fail right away.
    try
    {
        channel = new ControlChannelTrigger(channelId, serverKeepAliveInterval,
                                   ControlChannelTriggerResourceType.RequestHardwareSlot);
    }
    catch (UnauthorizedAccessException exp)
    {
        Diag.DebugPrint("Is the app on lockscreen? " + exp.Message);
        return result;
    }

    Uri serverUriInstance;
    try
    {
        serverUriInstance = new Uri(serverUri);
    }
    catch (Exception exp)
    {
        Diag.DebugPrint("Error creating URI: " + exp.Message);
        return result;
    }

    // Register the apps background task with the trigger for keepalive.
    var keepAliveBuilder = new BackgroundTaskBuilder();
    keepAliveBuilder.Name = "KeepaliveTaskForChannelOne";
    keepAliveBuilder.TaskEntryPoint = WebSocketKeepAliveTask;
    keepAliveBuilder.SetTrigger(channel.KeepAliveTrigger);
    keepAliveBuilder.Register();

    // Register the apps background task with the trigger for push notification task.
    var pushNotifyBuilder = new BackgroundTaskBuilder();
    pushNotifyBuilder.Name = "PushNotificationTaskForChannelOne";
    pushNotifyBuilder.TaskEntryPoint = "Background.PushNotifyTask";
    pushNotifyBuilder.SetTrigger(channel.PushNotificationTrigger);
    pushNotifyBuilder.Register();

    // Tie the transport method to the ControlChannelTrigger object to push enable it.
    // Note that if the transport' s TCP connection is broken at a later point of time,
    // the ControlChannelTrigger object can be reused to plug in a new transport by
    // calling UsingTransport API again.
    try
    {
        channel.UsingTransport(socket);

        // Connect the socket
        //
        // If connect fails or times out it will throw exception.
        // ConnectAsync can also fail if hardware slot was requested
        // but none are available
        await socket.ConnectAsync(serverUriInstance);

        // Call WaitForPushEnabled API to make sure the TCP connection has
        // been established, which will mean that the OS will have allocated
        // any hardware slot for this TCP connection.
        //
        // In this sample, the ControlChannelTrigger object was created by
        // explicitly requesting a hardware slot.
        //
        // On systems that without connected standby, if app requests hardware slot as above,
        // the system will fallback to a software slot automatically.
        //
        // On systems that support connected standby,, if no hardware slot is available, then app
        // can request a software slot by re-creating the ControlChannelTrigger object.
        status = channel.WaitForPushEnabled();
        if (status != ControlChannelTriggerStatus.HardwareSlotAllocated
            && status != ControlChannelTriggerStatus.SoftwareSlotAllocated)
        {
            throw new Exception(string.Format("Neither hardware nor software slot could be allocated. ChannelStatus is {0}", status.ToString()));
        }

        // Store the objects created in the property bag for later use.
        CoreApplication.Properties.Remove(channel.ControlChannelTriggerId);

        var appContext = new AppContext(this, socket, channel, channel.ControlChannelTriggerId);
        ((IDictionary<string, object>)CoreApplication.Properties).Add(channel.ControlChannelTriggerId, appContext);
        result = true;

        // Almost done. Post a read since we are using streamwebsocket
        // to allow push notifications to be received.
        PostSocketRead(MAX_BUFFER_LENGTH);
    }
    catch (Exception exp)
    {
         Diag.DebugPrint("RegisterWithCCTHelper Task failed with: " + exp.Message);

         // Exceptions may be thrown for example if the application has not
         // registered the background task class id for using real time communications
         // broker in the package manifest.
    }
    return result
}
```

Дополнительные сведения об использовании [**MessageWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.MessageWebSocket) или [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) см. в [Примере ControlChannelTrigger StreamWebSocket](https://go.microsoft.com/fwlink/p/?linkid=251232).

## <a name="controlchanneltrigger-with-httpclient"></a>ControlChannelTrigger с HttpClient
При использовании [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) действуют особые условия. Существует ряд специфичных для транспорта шаблонов использования и рекомендаций, которыми следует руководствоваться при использовании [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) с **ControlChannelTrigger**. Кроме того, эти аспекты влияют на способ, которым обрабатываются запросы на получение пакетов в [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637).

**Примечание.**            Класс   [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637), использующий SSL, в настоящее время не поддерживается с помощью функции сетевых триггеров и [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger).
 
Ниже приведены шаблоны использования и рекомендации, которыми следует руководствоваться при использовании [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger).

-   Возможно, до отправки запроса на конкретный универсальный код ресурса (URI) приложению потребуется настроить различные свойства и заголовки для объекта [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) или [HttpClientHandler](https://go.microsoft.com/fwlink/p/?linkid=241638) в пространстве имен [System.Net.Http](https://go.microsoft.com/fwlink/p/?linkid=227894).
-   Приложению может потребоваться выполнить предварительный запрос для проверки и правильной настройки транспорта, прежде чем оно создаст транспорт [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) для использования с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger). После того как приложение проверит правильность настройки транспорта, можно настроить объект [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) в качестве транспортного объекта для использования с объектом **ControlChannelTrigger**. Цель этой процедуры — предотвратить сценарии разрыва подключения, установленного по транспорту. Если используется SSL с проверкой сертификата, возможно, приложение должно будет вывести диалоговое окно для ввода ПИН-кода или выбора сертификата, если в системе их несколько. Возможно, возникнет необходимость в проверке подлинности прокси-сервера и сервера. По истечении срока действия кэшированных учетных данных прокси-сервера или сервера подключение может быть разорвано. Один из способов предотвратить проблемы, связанные с истечением срока действия кэшированных учетных данных, — это установка таймера. При необходимости перенаправления HTTP не гарантируется, что второе подключение будет установлено надежно. Предварительный тестовый запрос поможет убедиться в том, что приложение может использовать актуальный URL-адрес перенаправления, до использования объекта [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) в качестве транспорта для объекта **ControlChannelTrigger**.

В отличие от других видов сетевого транспорта объект [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) нельзя напрямую передать в метод [**UsingTransport**](https://docs.microsoft.com/uwp/api/windows.networking.sockets.controlchanneltrigger.usingtransport) объекта [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger). Вместо этого объект [HttpRequestMessage](https://go.microsoft.com/fwlink/p/?linkid=259153) должен быть специально построен для использования с объектом [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) и **ControlChannelTrigger**. Для создания объекта [HttpRequestMessage](https://go.microsoft.com/fwlink/p/?linkid=259153) используется метод [RtcRequestFactory.Create](https://go.microsoft.com/fwlink/p/?linkid=259154). Созданный объект [HttpRequestMessage](https://go.microsoft.com/fwlink/p/?linkid=259153) затем передается в метод **UsingTransport**.

В следующем примере показано, как построить объект [HttpRequestMessage](https://go.microsoft.com/fwlink/p/?linkid=259153) для использования с объектом [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) и [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger).

```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;
using Windows.Networking.Sockets;

public HttpRequestMessage httpRequest;
public HttpClient httpClient;
public HttpRequestMessage httpRequest;
public ControlChannelTrigger channel;
public Uri serverUri;

private void SetupHttpRequestAndSendToHttpServer()
{
    try
    {
        // For HTTP based transports that use the RTC broker, whenever we send next request, we will abort the earlier
        // outstanding http request and start new one.
        // For example in case when http server is taking longer to reply, and keep alive trigger is fired in-between
        // then keep alive task will abort outstanding http request and start a new request which should be finished
        // before next keep alive task is triggered.
        if (httpRequest != null)
        {
            httpRequest.Dispose();
        }

        httpRequest = RtcRequestFactory.Create(HttpMethod.Get, serverUri);

        SendHttpRequest();
    }
        catch (Exception e)
    {
        Diag.DebugPrint("Connect failed with: " + e.ToString());
        throw;
    }
}
```

Некоторые особые аспекты определяют способ обработки запросов на отправку HTTP-запросов в [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) для инициирования получения ответа. В частности, при использовании [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) приложение должно использовать для обработки отправок задачу вместо модели **await**.

При использовании [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) синхронизация с методом [**IBackgroundTask.Run**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.) в фоновой задаче для [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) с возвращением обратного вызова завершения получения не выполняется. Поэтому приложение может использовать только блокирующий прием HttpResponseMessage в методе **Run** и ожидать, пока не будет получен весь запрос.

Использование [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) заметно отличается от транспорта [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket), [**MessageWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.MessageWebSocket) или [**StreamWebSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamWebSocket). Обратный вызов получения [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) передается через задачу в приложение после кода [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637). Это означает, что задача push-уведомления **ControlChannelTrigger** будет запущена сразу после отправки приложению данных или ошибки. В следующем примере код сохраняет объект responseTask, возвращенный методом [HttpClient.SendAsync](https://go.microsoft.com/fwlink/p/?linkid=241637), в глобальном хранилище, которое задача push-уведомления будет выбирать и обрабатывать встроенными средствами.

В следующем примере показано, как обрабатывать запросы на отправку в [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) при использовании с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger).

```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;
using Windows.Networking.Sockets;

private void SendHttpRequest()
{
    if (httpRequest == null)
    {
        throw new Exception("HttpRequest object is null");
    }

    // Tie the transport method to the controlchanneltrigger object to push enable it.
    // Note that if the transport' s TCP connection is broken at a later point of time,
    // the controlchanneltrigger object can be reused to plugin a new transport by
    // calling UsingTransport API again.
    channel.UsingTransport(httpRequest);

    // Call the SendAsync function to kick start the TCP connection establishment
    // process for this http request.
    Task<HttpResponseMessage> httpResponseTask = httpClient.SendAsync(httpRequest);

    // Call WaitForPushEnabled API to make sure the TCP connection has been established,
    // which will mean that the OS will have allocated any hardware slot for this TCP connection.
    ControlChannelTriggerStatus status = channel.WaitForPushEnabled();
    Diag.DebugPrint("WaitForPushEnabled() completed with status: " + status);
    if (status != ControlChannelTriggerStatus.HardwareSlotAllocated
        && status != ControlChannelTriggerStatus.SoftwareSlotAllocated)
    {
        throw new Exception("Hardware/Software slot not allocated");
    }

    // The HttpClient receive callback is delivered via a Task to the app.
    // The notification task will fire as soon as the data or error is dispatched
    // Enqueue the responseTask returned by httpClient.sendAsync
    // into a queue that the push notify task will pick up and process inline.
    AppContext.messageQueue.Enqueue(httpResponseTask);
}
```

В следующем примере показано, как считывать ответы, полученные в [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) при использовании с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger).

```csharp
using System.Net;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

public string ReadResponse(Task<HttpResponseMessage> httpResponseTask)
{
    string message = null;
    try
    {
        if (httpResponseTask.IsCanceled || httpResponseTask.IsFaulted)
        {
            Diag.DebugPrint("Task is cancelled or has failed");
            return message;
        }
        // We' ll wait until we got the whole response.
        // This is the only supported scenario for HttpClient for ControlChannelTrigger.
        HttpResponseMessage httpResponse = httpResponseTask.Result;
        if (httpResponse == null || httpResponse.Content == null)
        {
            Diag.DebugPrint("Cannot read from httpresponse, as either httpResponse or its content is null. try to reset connection.");
        }
        else
        {
            // This is likely being processed in the context of a background task and so
            // synchronously read the Content' s results inline so that the Toast can be shown.
            // before we exit the Run method.
            message = httpResponse.Content.ReadAsStringAsync().Result;
        }
    }
    catch (Exception exp)
    {
        Diag.DebugPrint("Failed to read from httpresponse with error:  " + exp.ToString());
    }
    return message;
}
```

Подробнее об использовании [HttpClient](https://go.microsoft.com/fwlink/p/?linkid=241637) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) см. в [Примере ControlChannelTrigger HttpClient](https://go.microsoft.com/fwlink/p/?linkid=258323).

## <a name="controlchanneltrigger-with-ixmlhttprequest2"></a>ControlChannelTrigger с IXMLHttpRequest2
При использовании [**IXMLHTTPRequest2**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nn-msxml6-ixmlhttprequest2) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) действуют особые условия. Существует ряд специфичных для транспорта шаблонов использования и рекомендаций, которыми следует руководствоваться при использовании **IXMLHTTPRequest2** с **ControlChannelTrigger**. Использование **ControlChannelTrigger** не влияет на способ, которым обрабатываются запросы на отправку или получение HTTP-запросов в **IXMLHTTPRequest2**.

Шаблоны использования и рекомендации при использовании [**IXMLHTTPRequest2**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nn-msxml6-ixmlhttprequest2) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger)

-   При использовании в качестве транспорта объект [**IXMLHTTPRequest2**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nn-msxml6-ixmlhttprequest2) существует только в течение одного цикла «запрос — ответ». При использовании с объектом [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) удобно один раз создать и настроить объект **ControlChannelTrigger**, а затем несколько раз вызвать метод [**UsingTransport**](https://docs.microsoft.com/uwp/api/windows.networking.sockets.controlchanneltrigger.usingtransport) с привязкой нового объекта **IXMLHTTPRequest2** при каждом вызове. Приложение должно удалить предыдущий объект **IXMLHTTPRequest2** до передачи нового объекта **IXMLHTTPRequest2**, чтобы гарантировать, что приложение не превысит ограничения на выделенные ресурсы.
-   Возможно, перед вызовом метода [**Send**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nf-msxml6-ixmlhttprequest2-send) приложению придется вызвать методы [**SetProperty**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nf-msxml6-ixmlhttprequest2-setproperty) и [**SetRequestHeader**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nf-msxml6-ixmlhttprequest2-setrequestheader) для настройки транспорта HTTP.
-   Приложению может потребоваться выполнить предварительный запрос [**Send**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nf-msxml6-ixmlhttprequest2-send) для проверки и правильной настройки транспорта, прежде чем оно создаст транспорт для использования с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger). После того как приложение подтвердит правильную настройку транспорта, можно настроить объект [**IXMLHTTPRequest2**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nn-msxml6-ixmlhttprequest2) в качестве транспортного объекта для использования с **ControlChannelTrigger**. Цель этой процедуры — предотвратить сценарии разрыва подключения, установленного по транспорту. Если используется SSL с проверкой сертификата, возможно, приложение должно будет вывести диалоговое окно для ввода ПИН-кода или выбора сертификата, если в системе их несколько. Возможно, возникнет необходимость в проверке подлинности прокси-сервера и сервера. По истечении срока действия кэшированных учетных данных прокси-сервера или сервера подключение может быть разорвано. Один из способов предотвратить проблемы, связанные с истечением срока действия кэшированных учетных данных, — это установка таймера. При необходимости перенаправления HTTP не гарантируется, что второе подключение будет установлено надежно. Предварительный тестовый запрос поможет убедиться в том, что приложение может использовать актуальный URL-адрес перенаправления, до использования объекта **IXMLHTTPRequest2** в качестве транспорта для объекта **ControlChannelTrigger**.

Подробнее об использовании [**IXMLHTTPRequest2**](https://docs.microsoft.com/previous-versions/windows/desktop/api/msxml6/nn-msxml6-ixmlhttprequest2) с [**ControlChannelTrigger**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger) см. в [Примере ControlChannelTrigger с IXMLHTTPRequest2](https://go.microsoft.com/fwlink/p/?linkid=258538).

## <a name="important-apis"></a>Важные API
* [SocketActivityTrigger](/uwp/api/Windows.ApplicationModel.Background.SocketActivityTrigger)
* [ControlChannelTrigger](/uwp/api/Windows.Networking.Sockets.ControlChannelTrigger)
