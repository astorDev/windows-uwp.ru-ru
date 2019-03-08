---
title: Применение проекций 2 WinRT игр чата
description: Сведения об использовании Xbox Live игр Chat 2 с проекциями WinRT Добавление голосовой связи в игре.
ms.date: 04/11/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, игр чата 2, чат, игры, голосовой связи
ms.localizationpriority: medium
ms.openlocfilehash: 1cb0578151d4262d61f5fbc078bebab721fb3bfe
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57639169"
---
# <a name="using-game-chat-2-winrt-projections"></a>С помощью чата игр 2 (проекций WinRT)

Это краткое Пошаговое руководство по использованию игры Chat 2 C# API. Разработчики игр, желающих получить доступ к 2-чат игры через C++ увидите [с использованием 2 Chat игры](using-game-chat-2.md).

## <a name="prerequisites-a-nameprereq"></a>Предварительные требования <a name="prereq">

Для использования 2 Chat игру, необходимо включить [пакет nuget Microsoft.Xbox.Services.GameChat2](https://www.nuget.org/packages/Microsoft.Xbox.Game.Chat.2.WinRT.UWP/).

Прежде чем приступить к созданию кода с помощью 2-чат игры, необходимо настроить AppXManifest приложения для объявления возможность устройства «микрофон». AppXManifest возможности описаны более подробно в соответствующих подразделах документации платформы; в следующем фрагменте показан узел возможность устройства «микрофон», должна существовать в узле пакета или возможности, иначе будет заблокировано чата:

```xml
 <?xml version="1.0" encoding="utf-8"?>
 <Package ...>
   <Identity ... />
   ...
   <Capabilities>
     <DeviceCapability Name="microphone" />
   </Capabilities>
 </Package>
```

## <a name="initialization-a-nameinit"></a>Инициализация <a name="init">

Вы начали, взаимодействовать с библиотекой путем создания экземпляра `GameChat2ChatManager` ожидается, что объект с максимальным числом параллельных чата пользователей для добавления к экземпляру. Чтобы изменить это значение `GameChat2ChatManager` объект должен быть удален и создан повторно с требуемым значением. Может иметь только одну `GameChat2ChatManager` создан во время.

```cs
GameChat2ChatManager myGameChat2ChatManager = new GameChat2ChatManager(<maxChatUserCount>);
```

`GameChat2ChatManager` Объект имеет дополнительные свойства, которые могут настроить в любое время. В следующем образце кода предполагается, что переменные получает значение, прежде чем они используются для задания свойств на `myGameChat2ChatManager` объекта.

```cs
GameChat2SpeechToTextConversionMode mySpeechToTextConversionMode;
GameChat2SharedDeviceCommunicationRelationshipResolutionMode mySharedDeviceResolutionMode;
GameChat2CommunicationRelationship myDefaultLocalToRemoteCommunicationRelationship;
float myDefaultAudioRenderVolume;
GameChat2AudioEncodingTypeAndBitrate myAudioEncodingTypeAndBitRate;

...

myGameChat2ChatManager.SpeechToTextConversionMode = mySpeechToTextConversionMode;
myGameChat2ChatManager.SharedDeviceResolutionMode = mySharedDeviceResolutionMode;
myGameChat2ChatManager.DefaultLocalToRemoteCommunicationRelationship = myDefaultLocalToRemoteCommunicationRelationship;
myGameChat2ChatManager.DefaultAudioRenderVolume = myDefaultAudioRenderVolume;
myGameChat2ChatManager.AudioEncodingTypeAndBitRate = myAudioEncodingTypeAndBitRate;
```

## <a name="configuring-users-a-nameconfig"></a>Настройки пользователей <a name="config">

После инициализации экземпляра локальных пользователей необходимо добавить к экземпляру GC2. В этом примере пользователь A будет представлять локального пользователя.

```cs
string userAXuid;

...

IGameChat2ChatUser userA = myGameChat2ChatManager.AddLocalUser(userAXuid);
```

Затем необходимо добавить удаленных пользователей и идентификаторов, который будет использоваться для представления удаленного «конечные точки», для каждого удаленного пользователя. «Конечные точки», представлены объекты, принадлежащие приложению, реализующие `IGameChat2Endpoint` интерфейс. В следующем примере показано пример реализации `MyEndpoint` класс, реализующий `IGameChat2Endpoint`.

```cs
class MyEndpoint : IGameChat2Endpoint
{
    private uint endpointIdentifier;
    public MyEndpoint(uint identifier)
    {
        endpointIdentifier = identifier;
    }

    // Implementing IsSameEndpointAs, the only method on the IGameChat2Endpoint interface
    public bool IsSameEndpointAs(IGameChat2Endpoint gameChat2Endpoint)
    {
        return endpointIdentifier == ((MyEndpoint)gameChat2Endpoint).endpointIdentifier;
    }
}
```

В следующем примере кода B, C и D, удаленных пользователей, добавленных. На другом устройстве удаленного пользователя B — на одном удаленном устройстве и пользователи C и D. В этом примере предполагается, что переменные будут устанавливаться и что `myGameChat2ChatManager` является экземпляром класса `GameChat2ChatManager` и что «MyEndpoint» — это класс, реализующий `IGameChat2Endpoint`.

```cs
string userBXuid;
string userCXuid;
string userDXuid;
MyEndpoint myRemoteEndpointOne;
MyEndpoint myRemoteEndpointTwo;

...

IGameChat2ChatUser remoteUserB = myGameChat2ChatManager.AddRemoteUser(userBXuid, myRemoteEndpointOne);
IGameChat2ChatUser remoteUserC = myGameChat2ChatManager.AddRemoteUser(userCXuid, myRemoteEndpointTwo);
IGameChat2ChatUser remoteUserD = myGameChat2ChatManager.AddRemoteUser(userDXuid, myRemoteEndpointTwo);
```

Теперь можно настроить отношения взаимодействия между каждого удаленного пользователя, а также локального пользователя. В этом примере предположим, что пользователь А и Б находятся в той же командой разработчиков и двусторонний обмен сообщениями разрешен. `GameChat2CommunicationRelationship.SendAndReceiveAll` определяется для представления двунаправленный обмен данными. Установите отношения пользователя А пользователь Б с:

```cs
GameChat2ChatUserLocal localUserA = userA as GameChat2ChatUserLocal;
localUserA.SetCommunicationRelationship(remoteUserB, GameChat2CommunicationRelationship.SendAndReceiveAll);
```

Теперь предположим, что пользователи C и D, «spectators» и должны прослушивать пользователь A, но не являющихся носителями. `GameChat2CommunicationRelationship.ReceiveAll` определяется это однонаправленный взаимодействие. Установите отношения с:

```cs
localUserA.SetCommunicationRelationship(remoteUserC, GameChat2CommunicationRelationship.ReceiveAll);
localUserA.SetCommunicationRelationship(remoteUserD, GameChat2CommunicationRelationship.ReceiveAll);
```

Если в любой момент есть удаленные пользователи, которые были добавлены `GameChat2ChatManager` экземпляра, но еще не был настроен для взаимодействия с любой локальных пользователей -, хорошо! Такая ситуация возможна в сценариях, где определяются группами пользователей, или можно произвольно изменять произношения каналы. Игры Chat 2 будет кэшировать только данные (например, конфиденциальность связи и репутации) для пользователей, которые были добавлены к экземпляру, поэтому очень полезна для информирования игры Chat 2 из всех возможных пользователей, даже если они не могут общаться любые локальные пользователи в определенной точке во времени.

Наконец Предположим, что D пользователь вышел из игры и должны быть удалены из локального экземпляра игр Chat 2. Это можно сделать, вызвав:

```cs
remoteUserD.Remove();
```

Объект пользователя аннулируется сразу же при `Remove()` вызывается. Последнее состояние пользователя кэшируется в том случае, когда он удаляется. Все информационные методы, вызываемые после становится недействительным в объекте пользователя будет отражать состояние пользователя, прежде чем он был удален. Другие методы, возникнет ошибка при вызове.

## <a name="processing-data-frames-a-namedata"></a>Кадры обработки данных <a name="data">

Игры Chat 2 имеет свой собственный транспортном уровне; Это должно быть указано в приложении. Этот подключаемый модуль находится под управлением приложения регулярных частые вызовы метода `GameChat2ChatManager.GetDataFrames()`. Этот метод является, как игра Chat 2 обеспечивает выходных данных в приложение. Он позволяет быстро работать таким образом, что он может быть запрошено часто в выделенном потоке сети. Это обеспечивает удобное место для извлечения всех данных в очереди, не беспокоясь о непредсказуемость времени сети и уровня сложности многопоточных обратного вызова.

Когда `GameChat2ChatManager.GetDataFrames()` вызывается, все в очереди данные передаются в список `IGameChat2DataFrame` объектов. Приложения должны перебирать список, проверить `TargetEndpointIdentifiers`и использовать сетевой уровень приложения для доставки данных в экземпляры, соответствующие удаленному приложению. В этом примере `HandleOutgoingDataFrame` — это функция, которая отправляет данные в `Buffer` для каждого пользователя на каждой «Endpoint», указанный в `TargetEndpointIdentifiers` согласно `TransportRequirement`.

```cs
IReadOnlyList<IGameChat2DataFrame> frames = myGameChat2ChatManager.GetDataFrames();
foreach (IGameChat2DataFrame dataFrame in frames)
{
    HandleOutgoingDataFrame(
        dataFrame.Buffer,
        dataFrame.TargetEndpointIdentifiers,
        dataFrame.TransportRequirement
        );
}
```

Чаще кадров данных, тем ниже аудио задержки, очевидным для конечного пользователя. Звук, объединенных в 40 мс кадры данных; это период предлагаемые опроса.

## <a name="processing-state-changes-a-namestate"></a>Обработка изменений состояния <a name="state">

Игры Chat 2 предоставляет обновления для него, например полученных текстовых сообщений, через приложение регулярных и своевременные вызовы `GameChat2ChatManager.GetStateChanges()` метод. Он предназначен для работы быстро таким образом, что каждый кадр графики может быть вызвана в цикле отрисовки пользовательского интерфейса. Это обеспечивает удобное место для получения всех изменений в очереди, не беспокоясь о непредсказуемость времени сети и уровня сложности многопоточных обратного вызова.

Когда `GameChat2ChatManager.GetStateChanges()` является именем, в списке выводятся все обновления в очереди `IGameChat2StateChange` объектов. Приложения должны:
1. Итерацию по списку
2. Проверка базовой структуры для его более конкретный тип
3. Приведение основной структуры к соответствующему более подробные типа
4. Обрабатывать обновления соответствующим образом. 

```cs
IReadOnlyList<IGameChat2StateChange> stateChanges = myGameChat2ChatManager.GetStateChanges();
foreach (IGameChat2StateChange stateChange in stateChanges)
{
    switch (stateChange.Type)
    {
        case GameChat2StateChangeType.CommunicationRelationshipAdjusterChanged:
        {
            MyHandleCommunicationRelationshipAdjusterChanged(stateChange as GameChat2CommunicationRelationshipAdjusterChangedStateChange);
            break;
        }
        case GameChat2StateChangeType.TextChatReceived:
        {
            MyHandleTextChatReceived(stateChange as GameChat2TextChatReceivedStateChange);
            break;
        }

        ...
    }
}
```

## <a name="text-chat-a-nametext"></a>Текст чата <a name="text">

Чтобы отправить чат текста, используйте `GameChat2ChatUserLocal.SendChatText()`. Например:

```cs
localUserA.SendChatText("Hello");
```

Игры Chat 2 создает кадр данных, содержащий это сообщение; целевые конечные точки для кадров данных будет элементами, связанными с пользователями, которые были настроены для получения текста из локального пользователя. При обработке данных с удаленных конечных точек, сообщение будет предоставлен через `GameChat2TextChatReceivedStateChange`. Как и в случае с разговора, для текста соблюдаются ограничения прав доступа и конфиденциальности. Если пара пользователей были настроены для разрешить чат текста, но ограничения прав доступа или конфиденциальности запретить обмен данными, текстовое сообщение, будут удалены.

Поддержка отображения и ввода текста чат является обязательным для объекта специальных возможностей (см. в разделе [специальных возможностей](#access) подробности).

## <a name="accessibility-a-nameaccess"></a>Специальные возможности <a name="access">

Поддержка текста чат ввода и отображения является обязательным. Текстовое поле является обязательным, поскольку даже на платформах или игр жанров, которые Исторически не были использовать широко физическую клавишу клавиатуры, пользователи могут настроить систему для использования преобразования текста в речь вспомогательных технологий. Аналогичным образом отображения текста не требуется, поскольку пользователи могут настроить систему для использования распознавания речи в текст. Эти параметры могут быть обнаружены в локальных пользователей, проверив `GameChat2ChatUserLocal.TextToSpeechConversionPreferenceEnabled` и `GameChat2ChatUserLocal.SpeechToTextConversionPreferenceEnabled` свойства соответственно, и вы можете условно включить механизмы текст.

### <a name="text-to-speech"></a>Преобразование текста в речь

Если для пользователя включен, текст в речь `GameChat2ChatUserLocal.TextToSpeechConversionPreferenceEnabled` будет иметь значение «true». При обнаружении этого состояния, приложению необходимо предоставить метод для ввода текста. После ввода текста, предоставляемые реальном или виртуальном клавиатуры, передайте строку для `GameChat2ChatUserLocal.SynthesizeTextToSpeech()` метод. Игры 2 Chat обнаружит и синтезировать звуковых данных, на основе строки и предпочтениями голосовой доступ пользователя. Например:

```cs
localUserA.SynthesizeTextToSpeech("Hello");
```

Аудио, созданным в рамках этой операции будет передаваться ко всем пользователям, которые были настроены для получения аудио из этого локального пользователя. Если `GameChat2ChatUserLocal.SynthesizeTextToSpeech()` вызывается для пользователя, у которого нет преобразования текста в речь поддержкой будет 2 Chat игры не выполнять никаких действий.

### <a name="speech-to-text"></a>Преобразование речи в текст

При наличии речи в текст включен, `GameChat2ChatUserLocal.SpeechToTextConversionPreferenceEnabled` будет иметь значение true. При обнаружении этого состояния, приложение должно быть готово для предоставления пользовательского интерфейса, связанный с расшифрованного сообщения. Сборщик Мусора будет автоматически транскрипция аудио каждого удаленного пользователя и предоставить его через `GameChat2TranscribedChatReceivedStateChange`.

### <a name="speech-to-text-performance-considerations"></a>Вопросы производительности речи в текст

При включении речи в текст экземпляра 2 Chat игры на каждом удаленном устройстве инициирует подключение веб-сокета с конечной точкой службы распознавания речи. Каждому удаленному клиенту игры Chat 2 отправляет аудио в конечную точку службы распознавания речи, через этот websocket; Конечная точка службы речи иногда возвращает сообщение расшифровка дикторского текста к удаленному устройству. Удаленное устройство затем отправляет сообщение расшифровка дикторского текста (т. е. текстовое сообщение) на локальном устройстве, где расшифрованного сообщения задается 2 Chat игры в приложение для подготовки к просмотру.

Таким образом влияние на производительность основного речи в текст — использование сети. Большая часть сетевого трафика является передачи звука кодировке. Веб-сокет отправляет аудио, которая уже была закодирована 2 чата игры в каталоге «normal» голосовой чат; приложение может управлять скоростью, с помощью `GameChat2ChatManager.AudioEncodingTypeAndBitrate`.

## <a name="ui-a-nameui"></a>ПОЛЬЗОВАТЕЛЬСКИЙ ИНТЕРФЕЙС <a name="UI">

Рекомендуется, что в любом игроков отображаются, особенно в список gamertags, например табло также отображать значки так заметны говоря обратной связи для пользователя. `IGameChat2ChatUser.ChatIndicator` Свойство представляет состояние текущего, моментальное чата для этого исполнителя. В следующем примере показано получение значения индикатора для `IGameChat2ChatUser` объекта, на который ссылается переменная пользователя «a», чтобы определить значение константы определенный значок, чтобы присвоить переменной «iconToShow»:

```cs
switch (userA.ChatIndicator)
{
    case GameChat2UserChatIndicator.Silent:
    {
        iconToShow = Icon_InactiveSpeaker;
        break;
    }
    case GameChat2UserChatIndicator.Talking:
    {
        iconToShow = Icon_ActiveSpeaker;
        break;
    }
    case GameChat2UserChatIndicator.LocalMicrophoneMuted:
    {
        iconToShow = Icon_MutedSpeaker;
        break;
    }
    
    ...
}
```

Значение `IGameChat2ChatUser.ChatIndicator` ожидается изменение часто как проигрыватели start и stop разговор, например. Он предназначен для поддержки приложений, запрашивая у каждого кадра пользовательского интерфейса в результате.

## <a name="muting-a-namemute"></a>Отключение звука <a name="mute">

`GameChat2ChatUserLocal.MicrophoneMuted` Свойство может использоваться для переключения состояние отключения элемента локального пользователя "микрофон". Когда Микрофон выключен, не содержат звука из этой "микрофон" будут записаны. Если пользователь находится в общего устройства, такие как Kinect, состояние отключения применяется ко всем пользователям. Этот метод не отражает элемент управления выключения звука оборудования (например, кнопки на гарнитура пользователя). Не существует метода для извлечения состояние выключения звука оборудования звуковое устройство пользователя через 2-чат игры.

`GameChat2ChatUserLocal.SetRemoteUserMuted()` Метод можно использовать для переключения состояние отключения удаленных пользователей относительно определенного локального пользователя. Когда удаленный пользователь так заметны, локального пользователя не любого звука или получать текст сообщения из удаленного пользователя.

## <a name="bad-reputation-auto-mute-a-nameautomute"></a>Ухудшают репутацию автоматическое выключение <a name="automute">

Обычно удаленные пользователи будут начинаться unmuted. Игры 2 Chat запускает пользователей в состояние выключения звука, когда (1) удаленный пользователь не друзей локального пользователя и (2) удаленного пользователя имеет флаг ухудшают репутацию. Если пользователи являются отключен из-за этой операции `IGameChat2ChatUser.ChatIndicator` вернет `GameChat2UserChatIndicator.ReputationRestricted`. Это состояние будет переопределено первый вызов `GameChat2ChatUserLocal.SetRemoteUserMuted()` , включает в себя удаленного пользователя в качестве целевого пользователя.

## <a name="privilege-and-privacy-a-namepriv"></a>Права доступа и конфиденциальности <a name="priv">

На основе отношения взаимодействия, задаются игры игры Chat 2 междоменной политики прав доступа и конфиденциальности. Игры 2 Chat выполняет поиск ограничение прав доступа и конфиденциальности, при первом добавлении пользователя; пользователя `IGameChat2ChatUser.ChatIndicator` всегда будет возвращать `GameChat2UserChatIndicator.Silent` до завершения этих операций. Если взаимодействие с пользователем зависит от прав доступа или конфиденциальности ограничений, пользователь `IGameChat2ChatUser.ChatIndicator` вернет `GameChat2UserChatIndicator.PlatformRestricted`. Применяются ограничения взаимодействия платформ к разговору голоса и текста; никогда не будет экземпляр, где текст чата блокируется по ограничению платформы, но разговора не, или наоборот.

`GameChat2ChatUserLocal.GetEffectiveCommunicationRelationship()` можно использовать для различения, когда пользователи не могут обмениваться данными из-за неполной права доступа и конфиденциальности операций. Он возвращает отношения взаимодействия, обеспечиваемая 2 Chat игры в виде `GameChat2CommunicationRelationship` и причиной связи не может быть равным настроенные связи в виде `GameChat2CommunicationRelationshipAdjuster`. Например, если операции поиска, все еще выполняется `GameChat2CommunicationRelationshipAdjuster` будет `GameChat2CommunicationRelationshipAdjuster.Initializing`. Этот метод должен использоваться в разработки и отладки сценариев; он не должен использоваться для изменения пользовательского интерфейса (см. в разделе [пользовательского интерфейса](#UI)).

## <a name="cleanup-a-namecleanup"></a>Очистка <a name="cleanup">

Если приложение больше не нужна связь через 2-чат игру, необходимо вызвать `GameChat2ChatManager.Dispose()`. Это позволяет 2 Chat игры, чтобы освободить ресурсы, которые были выделены для управления обменом данными.

## <a name="how-to-configure-popular-scenarios"></a>Как настроить популярные сценарии

### <a name="push-to-talk"></a>Push-talk

Push-talk должны быть реализованы с помощью `GameChat2ChatUserLocal.MicrophoneMuted`. Задайте `MicrophoneMuted` значение false, чтобы разрешить речи и `MicrophoneMuted` в значение true, чтобы ограничить его. Это изменение свойства предоставит минимальной задержки ответ от 2 Chat игры.

### <a name="teams"></a>Команды

Предположим, что пользователь А и Б находятся на Team Blue, что пользователь C и D пользователя находятся на Team Red. Каждый пользователь входит в уникальный экземпляр приложения.

На устройстве пользователя А:

```cs
localUserA.SetCommunicationRelationship(remoteUserB, GameChat2CommunicationRelationship.SendAndReceiveAll);
localUserA.SetCommunicationRelationship(remoteUserC, GameChat2CommunicationRelationship.None);
localUserA.SetCommunicationRelationship(remoteUserD, GameChat2CommunicationRelationship.None);
```

На устройстве пользователя Б:

```cs
localUserB.SetCommunicationRelationship(remoteUserA, GameChat2CommunicationRelationship.SendAndReceiveAll);
localUserB.SetCommunicationRelationship(remoteUserC, GameChat2CommunicationRelationship.None);
localUserB.SetCommunicationRelationship(remoteUserD, GameChat2CommunicationRelationship.None);
```

На устройстве пользователя C:

```cs
localUserC.SetCommunicationRelationship(remoteUserA, GameChat2CommunicationRelationship.None);
localUserC.SetCommunicationRelationship(remoteUserB, GameChat2CommunicationRelationship.None);
localUserC.SetCommunicationRelationship(remoteUserD, GameChat2CommunicationRelationship.SendAndReceiveAll);
```

На устройстве пользователя D:

```cs
localUserD.SetCommunicationRelationship(remoteUserA, GameChat2CommunicationRelationship.None);
localUserD.SetCommunicationRelationship(remoteUserB, GameChat2CommunicationRelationship.None);
localUserD.SetCommunicationRelationship(remoteUserC, GameChat2CommunicationRelationship.SendAndReceiveAll);
```

### <a name="broadcast"></a>Вещание

Предположим, что пользователь A является лидером, предоставляя заказы, а пользователи B, C и D может только ожидать передачи данных. Каждый игрок находится на устройстве, уникальное.

На устройстве пользователя А:

```cs
localUserA.SetCommunicationRelationship(remoteUserB, GameChat2CommunicationRelationship.SendAll);
localUserA.SetCommunicationRelationship(remoteUserC, GameChat2CommunicationRelationship.SendAll);
localUserA.SetCommunicationRelationship(remoteUserD, GameChat2CommunicationRelationship.SendAll);
```

На устройстве пользователя Б:

```cs
localUserB.SetCommunicationRelationship(remoteUserA, GameChat2CommunicationRelationship.ReceiveAll);
localUserB.SetCommunicationRelationship(remoteUserC, GameChat2CommunicationRelationship.None);
localUserB.SetCommunicationRelationship(remoteUserD, GameChat2CommunicationRelationship.None);
```

На устройстве пользователя C:

```cs
localUserC.SetCommunicationRelationship(remoteUserA, GameChat2CommunicationRelationship.ReceiveAll);
localUserC.SetCommunicationRelationship(remoteUserB, GameChat2CommunicationRelationship.None);
localUserC.SetCommunicationRelationship(remoteUserD, GameChat2CommunicationRelationship.None);
```

На устройстве пользователя D:

```cs
localUserD.SetCommunicationRelationship(remoteUserA, GameChat2CommunicationRelationship.ReceiveAll);
localUserD.SetCommunicationRelationship(remoteUserB, GameChat2CommunicationRelationship.None);
localUserD.SetCommunicationRelationship(remoteUserC, GameChat2CommunicationRelationship.None);
```
