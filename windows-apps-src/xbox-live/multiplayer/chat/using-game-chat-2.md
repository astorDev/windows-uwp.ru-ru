---
title: С помощью чата игр 2
description: Узнайте, как добавить голосовой связи в игру с помощью Xbox Live игр Chat 2.
ms.date: 03/20/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, игр чата 2, чат, игры, голосовой связи
ms.localizationpriority: medium
ms.openlocfilehash: 43fbd7cec037df735686aa60bc6cd57217875e33
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57639259"
---
# <a name="using-game-chat-2-c"></a>С помощью чата игр 2 (C++)

Это краткое Пошаговое руководство по использованию API C++ Game Chat 2. Игр разработчикам, желающим получить доступ к игре Chat 2 – C# увидите [проекций используйте игры Chat 2 WinRT](using-game-chat-2-winrt.md).

1. [Предварительные требования](#prerequisites)
2. [Инициализация](#initialization)
3. [Настройки пользователей](#configuring-users)
4. [Кадры обработки данных](#processing-data-frames)
5. [Обработка изменений состояния](#processing-state-changes)
6. [Текст чата](#text-chat)
7. [Специальные возможности](#accessibility)
8. [ПОЛЬЗОВАТЕЛЬСКИЙ ИНТЕРФЕЙС](#ui)
9. [Отключение звука](#muting)
10. [Ухудшают репутацию автоматическое выключение](#bad-reputation-auto-mute)
11. [Права доступа и конфиденциальности](#privilege-and-privacy)
12. [Очистка](#cleanup)
13. [Сбой модели](#failure-model)
14. [Как настроить популярные сценарии](#how-to-configure-popular-scenarios)

## <a name="prerequisites"></a>Предварительные условия

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

Компиляция 2 Chat игры требует включения основного GameChat2.h заголовка. Чтобы связать должным образом, проект необходимо также включить GameChat2Impl.h в хотя бы одна единица компиляции (common предкомпилированного заголовка рекомендуется, так как эти функции заглушки являются небольшим и простым для компилятору создавать как «встроенные»).

Интерфейс игры Chat 2 не требует проектом, чтобы выбрать между компиляция с использованием C + +/ CX и традиционного C++. он может использоваться с любым. Реализация также не выдавать исключения как средства отчетности, поэтому их можно использовать без труда из проектов без поддержки исключений при желании Некритическая ошибка. Реализация тем не менее, исключения с точки зрения Неустранимая ошибка reporting (см. в разделе [сбоя модели](#failure) для получения дополнительных сведений).

## <a name="initialization"></a>Инициализация

Вы начали, взаимодействовать с библиотекой путем инициализации игры Chat 2 одноэлементного экземпляра с параметрами, которые применяются к времени существования singleton инициализации.

```cpp
chat_manager::singleton_instance().initialize(...);
```

## <a name="configuring-users"></a>Настройки пользователей

После инициализации экземпляра локальных пользователей необходимо добавить к экземпляру 2 Chat игры. В этом примере пользователь A будет представлять локального пользователя.

```cpp
chat_user* chatUserA = chat_manager::singleton_instance().add_local_user(<user_a_xuid>);
```

Необходимо также добавить удаленных пользователей и идентификаторы, которые будут использоваться для представления «Конечной» на котором находится пользователь. «Конечной точкой» — это экземпляр приложения, выполняемого на удаленном устройстве. В этом примере пользователь Б в X конечной точки, пользователи C и D находятся на конечную точку Y. конечной точки Х произвольно присваивается идентификатор «1» и Y конечной точки произвольно присваивается идентификатор «2». Известно о том, игра Chat 2 удаленных пользователей с этими вызовами:

```cpp
chat_user* chatUserB = chat_manager::singleton_instance().add_remote_user(<user_b_xuid>, 1);
chat_user* chatUserC = chat_manager::singleton_instance().add_remote_user(<user_c_xuid>, 2);
chat_user* chatUserD = chat_manager::singleton_instance().add_remote_user(<user_d_xuid>, 2);
```

Теперь можно настроить отношения взаимодействия между каждого удаленного пользователя, а также локального пользователя. В этом примере предположим, что пользователь А и Б находятся в той же командой разработчиков и двусторонний обмен сообщениями разрешен. `c_communicationRelationshipSendAndReceiveAll` Константа, определенная в GameChat2.h для представления двунаправленный обмен данными. Установите отношения пользователя А пользователь Б с:

```cpp
chatUserA->local()->set_communication_relationship(chatUserB, c_communicationRelationshipSendAndReceiveAll);
```

Теперь предположим, что пользователи C и D, «spectators» и должны прослушивать пользователь A, но не являющихся носителями. `c_communicationRelationshipSendAll` Константа, определенная в GameChat2.h для представления этого однонаправленные взаимодействия. Установите отношения с:

```cpp
chatUserA->local()->set_communication_relationship(chatUserC, c_communicationRelationshipSendAll);
chatUserA->local()->set_communication_relationship(chatUserD, c_communicationRelationshipSendAll);
```

Если в любой момент удаленных пользователей, которые были добавлены одноэлементный экземпляр, но еще не был настроен для взаимодействия с любой локальных пользователей - это нормально. Такая ситуация возможна в сценариях, где определяются группами пользователей, или можно произвольно изменять произношения каналы. Игры Chat 2 будет кэшировать только данные (например, конфиденциальность связи и репутации) для пользователей, которые были добавлены к экземпляру, поэтому очень полезна для информирования игры Chat 2 из всех возможных пользователей, даже если они не могут общаться любые локальные пользователи в определенной точке во времени.

Наконец Предположим, что D пользователь вышел из игры и должны быть удалены из локального экземпляра игр Chat 2. Это можно сделать, вызвав:

```cpp
chat_manager::singleton_instance().remove_user(chatUserD);
```

Вызов `chat_manager::remove_user()` объекта пользователя, может сделать недействительным. Если вы используете [в реальном времени аудио манипуляции](real-time-audio-manipulation.md), обратитесь к [времени существования пользователя чата](real-time-audio-manipulation.md#chat-user-lifetimes) документации для получения дополнительных сведений. В противном случае объект пользователя становится недействительным немедленно при `chat_manager::remove_user()` вызывается. Слабая ограничение удаления пользователей подробно описана в [обработки изменений состояния](#state).

## <a name="processing-data-frames"></a>Кадры обработки данных

Игры Chat 2 имеет свой собственный транспортном уровне; Это должно быть указано в приложении. Этот подключаемый модуль находится под управлением приложения регулярных частые вызовы `chat_manager::start_processing_data_frames()` и `chat_manager::finish_processing_data_frames()` пару методов. Эти методы являются как игра Chat 2 обеспечивает выходных данных в приложение. Они предназначены для выполняются быстро, таким образом, чтобы они может быть запрошено часто в выделенном потоке сети. Это обеспечивает удобное место для извлечения всех данных в очереди, не беспокоясь о непредсказуемость времени сети и уровня сложности многопоточных обратного вызова.

Когда `chat_manager::start_processing_data_frames()` вызывается, все в очереди данные передаются в массив `game_chat_data_frame` структуры указатели. Приложения должны перебирать массив, проверить целевой объект «конечные точки» и использовать сетевой уровень приложения для доставки данных в экземпляры, соответствующие удаленному приложению. После завершения со всеми `game_chat_data_frame` структуры, массива должны быть переданы обратно 2 Chat игры, чтобы освободить ресурсы путем вызова `chat_manager:finish_processing_data_frames()`. Например:

```cpp
uint32_t dataFrameCount;
game_chat_data_frame_array dataFrames;
chat_manager::singleton_instance().start_processing_data_frames(&dataFrameCount, &dataFrames);
for (uint32_t dataFrameIndex = 0; dataFrameIndex < dataFrameCount; ++dataFrameIndex)
{
    game_chat_data_frame const* dataFrame = dataFrames[dataFrameIndex];
    HandleOutgoingDataFrame(
        dataFrame->packet_byte_count,
        dataFrame->packet_buffer,
        dataFrame->target_endpoint_identifier_count,
        dataFrame->target_endpoint_identifiers,
        dataFrame->transport_requirement
        );
}
chat_manager::singleton_instance().finish_processing_data_frames(dataFrames);
```

Чаще кадров данных, тем ниже аудио задержки, очевидным для конечного пользователя. Звук, объединенных в 40 мс кадры данных; это период предлагаемые опроса.

## <a name="processing-state-changes"></a>Обработка изменений состояния

Игры Chat 2 предоставляет обновления для него, например полученных текстовых сообщений, через приложение регулярных и своевременные вызовы `chat_manager::start_processing_state_changes()` и `chat_manager::finish_processing_state_changes()` пару методов. Они предназначены для выполняются быстро, таким образом, что они могут вызываться каждый кадр графики в цикле отрисовки пользовательского интерфейса. Это обеспечивает удобное место для получения всех изменений в очереди, не беспокоясь о непредсказуемость времени сети и уровня сложности многопоточных обратного вызова.

При `chat_manager::start_processing_state_changes()` является именем, все обновления в очереди, передаются в массив `game_chat_state_change` структуры указатели. Приложения должны перебирать массив, проверки базовой структуры для его более конкретный тип, приведение основной структуры, соответствующему более подробно введите, а затем обработать это обновление соответствующим образом. После завершения со всеми `game_chat_state_change` объектов в настоящее время недоступно, этот массив должны быть переданы обратно 2 Chat игры, чтобы освободить ресурсы путем вызова `chat_manager::finish_processing_state_changes()`. Например:

```cpp
uint32_t stateChangeCount;
game_chat_state_change_array gameChatStateChanges;
chat_manager::singleton_instance().start_processing_state_changes(&stateChangeCount, &gameChatStateChanges);

for (uint32_t stateChangeIndex = 0; stateChangeIndex < stateChangeCount; ++stateChangeIndex)
{
    switch (gameChatStateChanges[stateChangeIndex]->state_change_type)
    {
        case game_chat_state_change_type::text_chat_received:
        {
            HandleTextChatReceived(static_cast<const game_chat_text_chat_received_state_change*>(gameChatStateChanges[stateChangeIndex]));
            break;
        }

        case Xs::game_chat_2::game_chat_state_change_type::transcribed_chat_received:
        {
            HandleTranscribedChatReceived(static_cast<const Xs::game_chat_2::game_chat_transcribed_chat_received_state_change*>(gameChatStateChanges[stateChangeIndex]));
            break;
        }

        ...
    }
}
chat_manager::singleton_instance().finish_processing_state_changes(gameChatStateChanges);
```

Так как `chat_manager::remove_user()` сразу же делает недействительным в памяти, связанной с объектом пользователя и изменения состояния может содержать указатели на объекты пользователя `chat_manager::remove_user()` не должен вызываться при обработке изменений состояния.

## <a name="text-chat"></a>Текст чата

Чтобы отправить чат текста, используйте `chat_user::chat_user_local::send_chat_text()`. Например:

```cpp
chatUserA->local()->send_chat_text(L"Hello");
```

Игры Chat 2 создает кадр данных, содержащий это сообщение; целевые конечные точки для кадров данных будет элементами, связанными с пользователями, которые были настроены для получения текста из локального пользователя. При обработке данных с удаленных конечных точек, сообщение будет предоставлен через `game_chat_text_chat_received_state_change`. Как и в случае с разговора, для текста соблюдаются ограничения прав доступа и конфиденциальности. Если пара пользователей были настроены для разрешить чат текста, но ограничения прав доступа или конфиденциальности запретить обмен данными, текстовое сообщение, будут удалены.

Поддержка отображения и ввода текста чат является обязательным для объекта специальных возможностей (см. в разделе [специальных возможностей](#access) подробности).

## <a name="accessibility"></a>Специальные возможности

Поддержка текста чат ввода и отображения является обязательным. Текстовое поле является обязательным, поскольку даже на платформах или игр жанров, которые Исторически не были использовать широко физическую клавишу клавиатуры, пользователи могут настроить систему для использования преобразования текста в речь вспомогательных технологий. Аналогичным образом отображения текста не требуется, поскольку пользователи могут настроить систему для использования распознавания речи в текст. Эти параметры могут быть обнаружены в локальных пользователей путем вызова `chat_user::chat_user_local::text_to_speech_conversion_preference_enabled()` и `chat_user::chat_user_local::speech_to_text_conversion_preference_enabled()` методы соответственно, и вы можете условно включить механизмы текст.

### <a name="text-to-speech"></a>Преобразование текста в речь

Если для пользователя включен, текст в речь `chat_user::chat_user_local::text_to_speech_conversion_preference_enabled()` возвращает «true». При обнаружении этого состояния, приложению необходимо предоставить метод для ввода текста. После ввода текста, предоставляемые реальном или виртуальном клавиатуры, передайте строку для `chat_user::chat_user_local::synthesize_text_to_speech()` метод. Игры 2 Chat обнаружит и синтезировать звуковых данных, на основе строки и предпочтениями голосовой доступ пользователя. Например:

```cpp
chat_userA->local()->synthesize_text_to_speech(L"Hello");
```

Аудио, созданным в рамках этой операции будет передаваться ко всем пользователям, которые были настроены для получения аудио из этого локального пользователя. Если `chat_user::chat_user_local::synthesize_text_to_speech()` вызывается для пользователя, у которого нет преобразования текста в речь поддержкой будет 2 Chat игры не выполнять никаких действий.

### <a name="speech-to-text"></a>Преобразование речи в текст

При наличии речи в текст включен, `chat_user::chat_user_local::speech_to_text_conversion_preference_enabled()` возвратит значение true. При обнаружении этого состояния, приложение должно быть готово для предоставления пользовательского интерфейса, связанный с расшифрованного сообщения. Игры Chat 2 будет автоматически транскрипция аудио каждого удаленного пользователя и предоставить его через `game_chat_transcribed_chat_received_state_change`.

> `Windows::Xbox::UI::Accessibility` Класс Xbox One специально разработан для предоставления простое отображение текста в игре разговора с упором на вспомогательные технологии распознавания речи в текст.

### <a name="speech-to-text-performance-considerations"></a>Вопросы производительности речи в текст

При включении речи в текст экземпляра 2 Chat игры на каждом удаленном устройстве инициирует подключение веб-сокета с конечной точкой службы распознавания речи. Каждому удаленному клиенту игры Chat 2 отправляет аудио в конечную точку службы распознавания речи, через этот websocket; Конечная точка службы речи иногда возвращает сообщение расшифровка дикторского текста к удаленному устройству. Удаленное устройство затем отправляет сообщение расшифровка дикторского текста (т. е. текстовое сообщение) на локальном устройстве, где расшифрованного сообщения задается 2 Chat игры в приложение для подготовки к просмотру.

Таким образом влияние на производительность основного речи в текст — использование сети. Большая часть сетевого трафика является передачи звука кодировке. Веб-сокет отправляет аудио, которая уже была закодирована 2 чата игры в каталоге «normal» голосовой чат; приложение может управлять скоростью, с помощью `chat_manager::set_audio_encoding_type_and_bitrate`.

## <a name="ui"></a>Пользовательский интерфейс

Рекомендуется, что в любом игроков отображаются, особенно в список gamertags, например табло также отображать значки так заметны говоря обратной связи для пользователя. Это делается путем вызова `chat_user::chat_indicator()` извлекаемого `game_chat_user_chat_indicator` представляющее состояние текущего, моментальное чата для этого исполнителя. В следующем примере показано получение значения индикатора для `chat_user` объекта, на который ссылается переменная «chatUserA», чтобы определить значение константы определенный значок, чтобы присвоить переменной «iconToShow»:

```cpp
switch (chatUserA->chat_indicator())
{
   case game_chat_user_chat_indicator::silent:
   {
       iconToShow = Icon_InactiveSpeaker;
       break;
   }

   case game_chat_user_chat_indicator::talking:
   {
       iconToShow = Icon_ActiveSpeaker;
       break;
   }

   case game_chat_user_chat_indicator::local_microphone_muted:
   {
       iconToShow = Icon_MutedSpeaker;
       break;
   }
   ...
}
```

В отчете по `xim_player::chat_indicator()` ожидается изменение часто как проигрыватели start и stop разговор, например. Он предназначен для поддержки приложений, запрашивая у каждого кадра пользовательского интерфейса в результате.

## <a name="muting"></a>Отключение звука

`chat_user::chat_user_local::set_microphone_muted()` Метод можно использовать для переключения состояние отключения элемента локального пользователя "микрофон". Когда Микрофон выключен, не содержат звука из этой "микрофон" будут записаны. Если пользователь находится в общего устройства, такие как Kinect, состояние отключения применяется ко всем пользователям.

`chat_user::chat_user_local::microphone_muted()` Метод может использоваться для получения состояние отключения элемента локального пользователя "микрофон". Этот метод только отражает ли микрофон локального пользователя отключен в программном обеспечении через вызов `chat_user::chat_user_local::set_microphone_muted()`; этот метод не отражает выключить оборудования контролируется, например, с помощью кнопки на гарнитура пользователя. Не существует метода для извлечения состояние выключения звука оборудования звуковое устройство пользователя через 2-чат игры.

`chat_user::chat_user_local::set_remote_user_muted()` Метод можно использовать для переключения состояние отключения удаленных пользователей относительно определенного локального пользователя. Когда удаленный пользователь так заметны, локального пользователя не любого звука или получать текст сообщения из удаленного пользователя.

## <a name="bad-reputation-auto-mute"></a>Ухудшают репутацию автоматическое выключение

Как правило удаленные пользователи будут начинаться unmuted. Игры 2 Chat запускает пользователей в состояние выключения звука, когда (1) удаленный пользователь не друзей локального пользователя и (2) удаленного пользователя имеет флаг ухудшают репутацию. Если пользователи являются отключен из-за этой операции `chat_user::chat_indicator()` вернет `game_chat_user_chat_indicator::reputation_restricted`. Это состояние будет переопределено первый вызов `chat_user::chat_user_local::set_remote_user_muted()` , включает в себя удаленного пользователя в качестве целевого пользователя.

## <a name="privilege-and-privacy"></a>Права доступа и конфиденциальности

На основе отношения взаимодействия, задаются игры игры Chat 2 междоменной политики прав доступа и конфиденциальности. Игры 2 Chat выполняет поиск ограничение прав доступа и конфиденциальности, при первом добавлении пользователя; пользователя `chat_user::chat_indicator()` всегда будет возвращать `game_chat_user_chat_indicator::silent` до завершения этих операций. Если взаимодействие с пользователем зависит от прав доступа или конфиденциальности ограничение, пользователь `chat_user::chat_indicator()` вернет `game_chat_user_chat_indicator::platform_restricted`. Применяются ограничения взаимодействия платформ к разговору голоса и текста; никогда не будет экземпляр, где текст чата блокируется по ограничению платформы, но разговора не, или наоборот.

`chat_user::chat_user_local::get_effective_communication_relationship()` можно использовать для различения, когда пользователи не могут обмениваться данными из-за неполной права доступа и конфиденциальности операций. Он возвращает отношения взаимодействия, обеспечиваемая 2 Chat игры в виде `game_chat_communication_relationship_flags` и причиной связи не может быть равным настроенные связи в виде `game_chat_communication_relationship_adjuster`. Например, если операции поиска, все еще выполняется `game_chat_communication_relationship_adjuster` будет `game_chat_communication_relationship_adjuster::intializing`. Этот метод должен использоваться в разработки и отладки сценариев; он не должен использоваться для изменения пользовательского интерфейса (см. в разделе [пользовательского интерфейса](#UI)).

## <a name="cleanup"></a>Очистка

Если приложение больше не нужна связь через 2-чат игру, необходимо вызвать `chat_manager::cleanup()`. Это позволяет 2 Chat игры, чтобы освободить ресурсы, которые были выделены для управления обменом данными.

## <a name="failure-model"></a>Сбой модели

Реализация 2 Chat игры не вызывать исключения как средства отчетности, поэтому их можно использовать без труда из проектов без поддержки исключений при желании Некритическая ошибка. Игры 2-чат тем не менее, создает исключения для информирования о неустранимых ошибках. Эти ошибки представляют собой результат несанкционированного использования API, например добавление пользователя к экземпляру игры разговор до инициализации экземпляра, или доступа к объекту пользователя после его удаления из экземпляра 2 Chat игры. Эти ошибки должны исключаться на раннем этапе разработки и можно исправить, изменив шаблон, используемый для взаимодействия с 2 Chat игры. При возникновении такой ошибки, указание относительно причины ошибки выводится отладчик до исключения.

## <a name="how-to-configure-popular-scenarios"></a>Как настроить популярные сценарии

### <a name="push-to-talk"></a>Push-talk

Push-talk должны быть реализованы с помощью `chat_user::chat_user_local::set_microphone_muted()`. Вызовите `set_microphone_muted(false)` разрешающее речи и `set_microphone_muted(true)` ограничивать ее. Этот метод предоставит минимальной задержки ответ от 2 Chat игры.

### <a name="teams"></a>Команды

Предположим, что пользователь А и Б находятся на Team Blue, что пользователь C и D пользователя находятся на Team Red. Каждый пользователь входит в уникальный экземпляр приложения.

На устройстве пользователя А:

```cpp
chatUserA->local()->set_communication_relationship(chatUserB, c_communicationRelationshipSendAndReceiveAll);
chatUserA->local()->set_communication_relationship(chatUserC, game_chat_communication_relationship_flags::none);
chatUserA->local()->set_communication_relationship(chatUserD, game_chat_communication_relationship_flags::none);
```

На устройстве пользователя Б:

```cpp
chatUserB->local()->set_communication_relationship(chatUserA, c_communicationRelationshipSendAndReceiveAll);
chatUserB->local()->set_communication_relationship(chatUserC, game_chat_communication_relationship_flags::none);
chatUserB->local()->set_communication_relationship(chatUserD, game_chat_communication_relationship_flags::none);
```

На устройстве пользователя C:

```cpp
chatUserC->local()->set_communication_relationship(chatUserA, game_chat_communication_relationship_flags::none);
chatUserC->local()->set_communication_relationship(chatUserB, game_chat_communication_relationship_flags::none);
chatUserC->local()->set_communication_relationship(chatUserD, c_communicationRelationshipSendAndReceiveAll);
```

На устройстве пользователя D:

```cpp
chatUserD->local()->set_communication_relationship(chatUserA, game_chat_communication_relationship_flags::none);
chatUserD->local()->set_communication_relationship(chatUserB, game_chat_communication_relationship_flags::none);
chatUserD->local()->set_communication_relationship(chatUserC, c_communicationRelationshipSendAndReceiveAll);
```

### <a name="broadcast"></a>Вещание

Предположим, что пользователь A является лидером, предоставляя заказы, а пользователи B, C и D может только ожидать передачи данных. Каждый игрок находится на устройстве, уникальное.

На устройстве пользователя А:

```cpp
chatUserA->local()->set_communication_relationship(chatUserB, c_communicationRelationshipSendAll);
chatUserA->local()->set_communication_relationship(chatUserC, c_communicationRelationshipSendAll);
chatUserA->local()->set_communication_relationship(chatUserD, c_communicationRelationshipSendAll);
```

На устройстве пользователя Б:

```cpp
chatUserB->local()->set_communication_relationship(chatUserA, c_communicationRelationshipReceiveAll);
chatUserB->local()->set_communication_relationship(chatUserC, game_chat_communication_relationship_flags::none);
chatUserB->local()->set_communication_relationship(chatUserD, game_chat_communication_relationship_flags::none);
```

На устройстве пользователя C:

```cpp
chatUserC->local()->set_communication_relationship(chatUserA, c_communicationRelationshipReceiveAll);
chatUserC->local()->set_communication_relationship(chatUserB, game_chat_communication_relationship_flags::none);
chatUserC->local()->set_communication_relationship(chatUserD, game_chat_communication_relationship_flags::none);
```

На устройстве пользователя D:

```cpp
chatUserD->local()->set_communication_relationship(chatUserA, c_communicationRelationshipReceiveAll);
chatUserD->local()->set_communication_relationship(chatUserB, game_chat_communication_relationship_flags::none);
chatUserD->local()->set_communication_relationship(chatUserC, game_chat_communication_relationship_flags::none);
```
