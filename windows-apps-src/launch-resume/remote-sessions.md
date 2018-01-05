---
author: PatrickFarley
title: "Подключение устройств с помощью удаленных сеансов"
description: "Предоставляйте общие возможности на нескольких устройствах за счет их объединения через удаленный сеанс."
ms.assetid: 1c8dba9f-c933-4e85-829e-13ad784dd3e2
ms.author: pafarley
ms.date: 06/28/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9096a93005c628a5cf1f3271fbab3cca2399b75d
ms.sourcegitcommit: f9a4854b6aecfda472fb3f8b4a2d3b271b327800
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="connect-devices-through-remote-sessions"></a>Подключение устройств с помощью удаленных сеансов

Функция удаленных сеансов позволяет приложениям подключаться к другим устройствам с помощью сеанса для явного обмена сообщениями между приложениями или для обмена управляемыми системой данными через брокер, например **[SpatialEntityStore](https://docs.microsoft.com/uwp/api/windows.perception.spatial.spatialentitystore)** для совместного использования голографических объектов на устройствах Windows Holographic.

Удаленные сеансы может создать любое устройство с Windows, а также любое такое устройство может запросить разрешение на присоединение (хотя можно сделать сеансы видимыми только по приглашению), включая устройства, вход на которых выполнен другими пользователями. В этом руководстве представлен базовый пример кода для всех основных сценариев с использованием удаленных сеансов. Этот код можно встроить в существующий проект приложения и изменять по необходимости. Пример комплексной реализации см. в [примере приложения Quiz Game](https://github.com/microsoft/Windows-appsample-remote-system-sessions)).

## <a name="preliminary-setup"></a>Предварительная настройка

### <a name="add-the-remotesystem-capability"></a>Добавление возможности remoteSystem

Чтобы приложение могло запускать другое приложение на удаленном устройстве, вам потребуется добавить возможность `remoteSystem` в манифест пакета приложений. Вы можете использовать конструктор манифестов приложений для добавления этой возможности, выбрав **Удаленная система** на вкладке **Возможности**. Кроме того, можно вручную добавить следующую строку в файл Package.appxmanifest вашего проекта.

``` xml
<Capabilities>
   <uap3:Capability Name="remoteSystem"/>
</Capabilities>
```

### <a name="enable-cross-user-discovering-on-the-device"></a>Включение обнаружения между пользователями на устройстве
Функция удаленных сеансов ориентирована на подключение нескольких разных пользователей, поэтому на участвующих в сеансе устройствах должна быть включена функция общего доступа между пользователями. Это параметр системы, который можно запросить с помощью статического метода в классе **RemoteSystem**:

```csharp
if (!RemoteSystem.IsAuthorizationKindEnabled(RemoteSystemAuthorizationKind.Anonymous)) {
    // The system is not authorized to connect to cross-user devices. 
    // Inform the user that they can discover more devices if they
    // update the setting to "Everyone nearby".
}
```

Чтобы изменить этот параметр, пользователь должен открыть приложение **Параметры**. В меню **Система** > **Общие возможности** > **Общий доступ между устройствами** имеется раскрывающийся список, где пользователь может указать, каким устройствам система может предоставлять общий доступ.

![страница параметров общих возможностей](images/shared-experiences-settings.png)

### <a name="include-the-necessary-namespaces"></a>Включение необходимых пространств имен
Для использования всех фрагментов кода в этом руководстве вам потребуются следующие операторы `using` в файлах классов.

```csharp
using System.Runtime.Serialization.Json;
using Windows.Foundation.Collections;
using Windows.System.RemoteSystems;
```

## <a name="create-a-remote-session"></a>Создание удаленного сеанса

Для создания экземпляра удаленного сеанса необходимо начать с объекта **[RemoteSystemSessionController](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.remotesystemsessioncontroller)**. Используйте следующую платформу для создания нового сеанса и обработки запросов на присоединение от других устройств.

```csharp
public async void CreateSession() {
    
    // create a session controller
    RemoteSystemSessionController manager = new RemoteSystemSessionController("Bob’s Minecraft game");
    
    // register the following code to handle the JoinRequested event
    manager.JoinRequested += async (sender, args) => {
        // Get the deferral
        var deferral = args.GetDeferral();
        
        // display the participant (args.JoinRequest.Participant) on UI, giving the 
        // user an opportunity to respond
        // ...
        
        // If the user chooses "accept", accept this remote system as a participant
        args.JoinRequest.Accept();
    };
    
    // create and start the session
    RemoteSystemSessionCreationResult createResult = await manager.CreateSessionAsync();
    
    // handle the creation result
    if (createResult.Status == RemoteSystemSessionCreationStatus.Success) {
        // creation was successful, get a reference to the session
        RemoteSystemSession currentSession = createResult.Session;
        
        // optionally subscribe to the disconnection event
        currentSession.Disconnected += async (sender, args) => {
            // update the UI, using args.Reason
            //...
        };
    
        // Use session (see later section)
        //...
    
    } else if (createResult.Status == RemoteSystemSessionCreationStatus.SessionLimitsExceeded) {
        // creation failed. Optionally update UI to indicate that there are too many sessions in progress
    } else {
        // creation failed for an unknown reason. Optionally update UI
    }
}
```

### <a name="make-a-remote-session-invite-only"></a>Создание удаленного сеанса с доступом только по приглашению

Если требуется исключить общедоступное обнаружение удаленного сеанса, его можно сделать доступным только по приглашению. Только устройства, получившие приглашение, смогут отправлять запросы на присоединение. 

Эта процедура практически идентична указанной выше, но при создании экземпляра **[RemoteSystemSessionController](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.remotesystemsessioncontroller)** следует передать настроенный объект **[RemoteSystemSessionOptions](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.RemoteSystemSessionOptions)**.

```csharp
// define the session options with the invite-only designation
RemoteSystemSessionOptions sessionOptions = new RemoteSystemSessionOptions();
sessionOptions.IsInviteOnly = true;

// create the session controller
RemoteSystemSessionController manager = new RemoteSystemSessionController("Bob's Minecraft game", sessionOptions);

//...
```

Для отправки приглашения необходима ссылка на принимающую удаленную систему (получается путем обычного обнаружения удаленной системы). Передайте эту ссылку методу **[SendInvitationAsync](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.remotesystemsession#remotesystemsession_sendinvitationasync_1664759118)** объекта сеанса. Все участники сеанса получают ссылку на удаленный сеанс (см. следующий раздел), поэтому любой участник может отправить приглашение.

```csharp
// "currentSession" is a reference to a RemoteSystemSession.
// "guestSystem" is a previously discovered RemoteSystem instance
currentSession.SendInvitationAsync(guestSystem); 
```

## <a name="discover-and-join-a-remote-session"></a>Обнаружение удаленного сеанса и присоединение к нему

Процесс обнаружения удаленных сеансов обрабатывается классом **[RemoteSystemSessionWatcher](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.remotesystemsessionwatcher)** и аналогичен обнаружению отдельных удаленных систем.

```csharp
public void DiscoverSessions() {
    
    // create a watcher for remote system sessions
    RemoteSystemSessionWatcher sessionWatcher = RemoteSystemSession.CreateWatcher();
    
    // register a handler for the "added" event
    sessionWatcher.Added += async (sender, args) => {
        
        // get a reference to the info about the discovered session
        RemoteSystemSessionInfo sessionInfo = args.SessionInfo;
        
        // Optionally update the UI with the sessionInfo.DisplayName and 
        // sessionInfo.ControllerDisplayName strings. 
        // Save a reference to this RemoteSystemSessionInfo to use when the
        // user selects this session from the UI
        //...
    };
    
    // Begin watching
    sessionWatcher.Start();
}
```

При получении экземпляра **[RemoteSystemSessionInfo](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.remotesystemsessioninfo)** его можно использовать, чтобы отправить устройству, управляющему соответствующим сеансом, запрос на присоединение. Принятый запрос на присоединение асинхронно вернет объект **[RemoteSystemSessionJoinResult](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.remotesystemsessionjoinresult)**, содержащий ссылку на соответствующий сеанс.

```csharp
public async void JoinSession(RemoteSystemSessionInfo sessionInfo) {

    // issue a join request and wait for result.
    RemoteSystemSessionJoinResult joinResult = await sessionInfo.JoinAsync();
    if (joinResult.Status == RemoteSystemSessionJoinStatus.Success) {
        // Join request was approved

        // RemoteSystemSession instance "currentSession" was declared at class level.
        // Assign the value obtained from the join result.
        currentSession = joinResult.Session;
        
        // note connection and register to handle disconnection event
        bool isConnected = true;
        currentSession.Disconnected += async (sender, args) => {
            isConnected = false;

            // update the UI with args.Reason value
        };
        
        if (isConnected) {
            // optionally use the session here (see next section)
            //...
        }
    } else {
        // Join was unsuccessful.
        // Update the UI, using joinResult.Status value to show cause of failure.
    }
}
```

Устройство может присоединиться к нескольким сеансам одновременно. По этой причине следует отделить функции присоединения от фактического взаимодействия с каждым сеансом. Если в приложении есть ссылка на экземпляр **[RemoteSystemSession](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.remotesystemsession)**, через этот сеанс можно обмениваться данными.

## <a name="share-messages-and-data-through-a-remote-session"></a>Обмен сообщениями и данными через удаленный сеанс

### <a name="receive-messages"></a>Получение сообщений

Обмениваться сообщениями и данными с другими участвующими в сеансе устройствами можно с помощью экземпляра **[RemoteSystemSessionMessageChannel](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.remotesystemsessionmessagechannel)**, который представляет единый канал обмена данными для всего сеанса. Сразу после инициализации он начинает прослушивать входящие сообщения.

>[!NOTE]
>При отправке и получении сообщения необходимо сериализовывать и десериализовывать из массивов байтов. Эти возможности включены в следующие примеры, но могут быть реализованы отдельно для дополнительной модульности кода. Пример такой реализации см. в [примере приложения](https://github.com/microsoft/Windows-appsample-remote-system-sessions)).

```csharp
public async void StartReceivingMessages() {
    
    // Initialize. The channel name must be known by all participant devices 
    // that will communicate over it.
    RemoteSystemSessionMessageChannel messageChannel = new RemoteSystemSessionMessageChannel(currentSession, 
        "Everyone in Bob's Minecraft game", 
        RemoteSystemSessionMessageChannelReliability.Reliable);
    
    // write the handler for incoming messages on this channel
    messageChannel.ValueSetReceived += async (sender, args) => {
        
        // Update UI: a message was received from the participant args.Sender
        
        // Deserialize the message 
        // (this app must know what key to use and what object type the value is expected to be)
        ValueSet receivedMessage = args.Message;
        object rawData = receivedMessage["appKey"]);
        object value = new ExpectedType(); // this must be whatever type is expected

        using (var stream = new MemoryStream((byte[])rawData)) {
            value = new DataContractJsonSerializer(value.GetType()).ReadObject(stream);
        }
        
        // do something with the "value" object
        //...
    };
}
```

### <a name="send-messages"></a>Отправка сообщений

После установки канала можно с легкостью отправлять сообщения всем участникам сеанса.

```csharp
public async void SendMessageToAllParticipantsAsync(RemoteSystemSessionMessageChannel messageChannel, object value){

    // define a ValueSet message to send
    ValueSet message = new ValueSet();
    
    // serialize the "value" object to send
    using (var stream = new MemoryStream()){
        new DataContractJsonSerializer(value.GetType()).WriteObject(stream, value);
        byte[] rawData = stream.ToArray();
            message["appKey"] = rawData;
    }
    
    // Send message to all participants. Ordering is not guaranteed.
    await messageChannel.BroadcastValueSetAsync(message);
}
```

Для отправки сообщения только определенным участникам необходимо инициализировать процесс обнаружения, чтобы получить ссылки на участвующие в сеансе удаленные системы. Эта процедура аналогична процессу обнаружения удаленных систем вне сеанса. Используйте экземпляр **[RemoteSystemSessionParticipantWatcher](https://docs.microsoft.com/uwp/api/windows.system.remotesystems.remotesystemsessionparticipantwatcher)** для поиска участвующих в сеансе устройств.

```csharp
public void WatchForParticipants() {
    // "currentSession" is a reference to a RemoteSystemSession.
    RemoteSystemSessionParticipantWatcher watcher = currentSession.CreateParticipantWatcher();

    watcher.Added += (sender, participant) => {
        // save a reference to "participant"
        // optionally update UI
    };   

    watcher.Removed += (sender, participant) => {
        // remove reference to "participant"
        // optionally update UI
    };

    watcher.EnumerationCompleted += (sender, args) => {
        // Apps can delay data model render up until this point if they wish.
    };

    // Begin watching for session participants
    watcher.Start();
}
```

После получения списка ссылок на участников сеанса можно отправить сообщение любому их набору.

Для отправки сообщения одному участнику (желательно, выбранному пользователем на экране) передайте ссылку методу, как показано ниже.

```csharp
public async void SendMessageToParticipantAsync(RemoteSystemSessionMessageChannel messageChannel, RemoteSystemSessionParticipant participant, object value) {
    
    // define a ValueSet message to send
    ValueSet message = new ValueSet();
    
    // serialize the "value" object to send
    using (var stream = new MemoryStream()){
        new DataContractJsonSerializer(value.GetType()).WriteObject(stream, value);
        byte[] rawData = stream.ToArray();
            message["appKey"] = rawData;
    }

    // Send message to the participant
    await messageChannel.SendValueSetAsync(message,participant);
}
```

Для отправки сообщения нескольким участникам (желательно, выбранному пользователем на экране) добавьте их в объект списка и передайте этот список методу, как показано ниже.

```csharp
public async void SendMessageToListAsync(RemoteSystemSessionMessageChannel messageChannel, IReadOnlyList<RemoteSystemSessionParticipant> myTeam, object value){

    // define a ValueSet message to send
    ValueSet message = new ValueSet();
    
    // serialize the "value" object to send
    using (var stream = new MemoryStream()){
        new DataContractJsonSerializer(value.GetType()).WriteObject(stream, value);
        byte[] rawData = stream.ToArray();
            message["appKey"] = rawData;
    }

    // Send message to specific participants. Ordering is not guaranteed.
    await messageChannel.SendValueSetToParticipantsAsync(message, myTeam);   
}
```

## <a name="related-topics"></a>Статьи по теме
* [Подключенные приложения и устройства (Project Rome)](connected-apps-and-devices.md)
* [Справочник по API удаленных систем](https://msdn.microsoft.com/library/windows/apps/Windows.System.RemoteSystems)
