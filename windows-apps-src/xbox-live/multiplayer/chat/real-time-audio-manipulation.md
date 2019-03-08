---
title: Обработка звука в режиме реального времени
description: Узнайте, как управлять и обработку звукового сигнала чата, охватываемым 2 чата игры.
ms.date: 05/10/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, игр чаты 2, чат, игры, голосовой связи, буфером, аудио манипуляции
ms.localizationpriority: medium
ms.openlocfilehash: 7746080ea8a9698993a679b425f41442e4a6d943
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57643899"
---
# <a name="real-time-audio-manipulation"></a>Обработка звука в режиме реального времени

Игры чата 2 дает разработчикам возможность включить себя в конвейере аудио чата изучать и манипулировать игроков чата звуковых данных. Это может быть полезно для применения звуковые эффекты к голоса игроков в игре. Конвейер аудио манипуляции игр Chat 2 — взаимодействовать через объекты аудиопоток, которые можно опрашивать на предмет звуковых данных. В отличие от использования обратных вызовов, эта модель позволяет разработчикам для проверки или управлять звука на любой поток, обрабатывающий наиболее удобный для них.

Краткое Пошаговое руководство, с помощью обработки в реальном времени аудио представлено ниже, содержащий в следующих разделах:

1. [Инициализация конвейера обработки звука](#initializing-the-audio-manipulation-pipeline)
2. [Изменения состояния звукового потока обработки](#processing-audio-stream-state-changes)
3. [Управление предварительно кодировать звук чата](#manipulating-pre-encode-chat-audio-data)
4. [Обработка после декодирования аудио чата](#manipulating-post-decode-chat-audio-data)
5. [Время существования пользователя чата](#chat-user-lifetimes)

## <a name="initializing-the-audio-manipulation-pipeline"></a>Инициализация конвейера обработки звука

Игры Chat 2 по умолчанию не включит в реальном времени аудио манипуляции. Для включения в реальном времени аудио манипуляции, приложение необходимо указать какие виды аудио манипуляции она нужна включено в `chat_manager::initialize()` , задав для параметра audioManipulationMode.

В настоящее время поддерживаются следующие формы аудио манипуляции:

* `game_chat_audio_manipulation_mode_flags::none` — Отключает аудио манипуляции. Это конфигурация по умолчанию. В этом режиме чата звук будет проходить без перерыва.
* `game_chat_audio_manipulation_mode_flags::pre_encode_stream_manipulation` – Обеспечивает предварительное кодирование звука манипуляции. В этом режиме все аудио чата, создаваемые локальные пользователи будут передаваться по конвейеру обработки звука, перед кодированием. Даже если приложение является только проверка чата аудиозаписи и не преобразовывая их, это по-прежнему ответственность приложения для отправки неизмененный буферов аудио обратно до 2 чата игры, чтобы кодировать и передачи.
* `game_chat_audio_manipulation_mode_flags::post_decode_stream_manipulation` -Позволяет после декодирования аудио манипуляции. В этом режиме все чата звука, принимаемого от удаленных пользователей будут передаваться по конвейеру аудио манипуляции после она декодируется получателем, но перед его визуализацией. Даже если приложение является только проверка чата аудиозаписи и не преобразовывая их, это по-прежнему приложение отвечает за смешивать и отправить неизмененный буферов аудио обратно до 2 чата игры, чтобы их отображение.

## <a name="processing-audio-stream-state-changes"></a>Изменения состояния звукового потока обработки

Игры 2-чат предоставляет обновления состояния звуковых потоков через `game_chat_stream_state_change` структуры. Эти обновления хранить сведения о том, какие поток был обновлен, и как он был обновлен. Эти обновления можно опрашивать на предмет через вызовы к `chat_manager::start_processing_stream_state_changes()` и `chat_manager::finish_processing_stream_state_changes()` пару методов. Предоставляет эту пару методов, все последние, в очереди аудиопотока обновления состояния как массив `game_chat_stream_state_change` структуры указатели. Приложения следует выполнять итерацию по массиву и соответствующим образом обработать каждого обновления. Один раз все доступные `game_chat_stream_state_change` обработки обновлений, этот массив должны быть переданы обратно игры Chat 2 – `chat_manager::finish_processing_stream_state_changes()`. Например:

```cpp
uint32_t streamStateChangeCount;
game_chat_stream_state_change_array streamStateChanges;
chat_manager::singleton_instance().start_processing_stream_state_changes(&streamStateChangeCount, &streamStateChanges);

for (uint32_t streamStateChangeIndex = 0; streamStateChangeIndex < streamStateChangeCount; ++streamStateChangeIndex)
{
    switch (streamStateChanges[streamStateChangeIndex]->state_change_type)
    {
        case game_chat_stream_state_change_type::pre_encode_audio_stream_created:
        {
            HandlePreEncodeAudioStreamCreated(streamStateChanges[streamStateChangeIndex].pre_encode_audio_stream);
            break;
        }

        case Xs::game_chat_2::game_chat_stream_state_change_type::pre_encode_audio_stream_closed:
        {
            HandlePreEncodeAudioStreamClosed(streamStateChanges[streamStateChangeIndex].pre_encode_audio_stream);
            break;
        }

        ...
    }
}
chat_manager::singleton_instance().finish_processing_stream_state_changes(streamStateChanges);
```

## <a name="manipulating-pre-encode-chat-audio-data"></a>Управление предварительное кодирование чата звуковых данных

Игры чата 2 предоставляет доступ к предварительно кодирования чата звуковых данных для локальных пользователей через `pre_encode_audio_stream` класса.

### <a name="stream-lifetime"></a>Время существования Stream
При создании нового `pre_encode_audio_stream` экземпляр готов для использования приложением, оно будет доставлено через `game_chat_stream_state_change` структуру с его `state_change_type` поле "значение" `game_chat_stream_state_change_type::pre_encode_audio_stream_created`. После этого изменения состояния потока возвращается 2 Chat игры, станет доступным для аудиопотока предварительное кодирование звука манипуляции.

Если в имеющемся `pre_encode_audio_stream` становится недоступным для использования для манипуляций с аудио, приложение будет уведомлен через `game_chat_stream_state_change` структуру с его `state_change_type` поле "значение" `game_chat_stream_state_change_type::pre_encode_audio_stream_closed`. Это возможность запустить очистку ресурсов, связанных с этой звукового потока приложения. После этого изменения состояния потока возвращается 2 Chat игры, аудиопотока становятся недоступными для предварительное кодирование звука манипуляции.

При закрытом `pre_encode_audio_stream` все его ресурсы вернулся, уничтожении потока и приложение будет уведомлен через `game_chat_stream_state_change` структуру с его `state_change_type` поле "значение" `game_chat_stream_state_change_type::pre_encode_audio_stream_destroyed`. Все ссылки или указатели на этот поток, будет очищена. После этого изменения состояния потока возвращается 2 Chat игры, звуковой поток памяти, станут недействительными.

### <a name="stream-users"></a>Пользователи Stream
Список пользователей, связанную с потоком, можно проверить с помощью `pre_encode_audio_stream::get_users()`.

### <a name="audio-formats"></a>Форматы аудио
Звуковой формат буферов, приложение извлекает из игры Chat 2, можно проверить с помощью `pre_encode_audio_stream::get_pre_processed_format()`. Предварительно обработанного звуковой формат всегда будет mono. Приложения следует предусмотреть обработку данных, представленных в виде 32-разрядных значения с плавающей запятой, 16-разрядных целых чисел и 32-разрядных целых чисел.

Приложение необходимо сообщить игры Chat 2 аудио формате манипулируемого буферов, которые передаются в него для кодирования и передачи с помощью `pre_encode_audio_stream::set_processed_format()`. Обработанные форматы для предварительно кодировать звук потоки должны соответствовать эти предварительные условия:

* Формат должен быть mono.
* Формат должен быть 32-разрядное число с плавающей запятой PCM, 32-разрядное целое число PCM или форматы PCM 16-разрядное целое число.
* Частота выборки этого формата необходимо выполнить предварительные условия, в зависимости от его платформы. Xbox One ЭРЫ поддерживает 8 кГц, 12 кГц, 16 кГц и 24 кГц частоты выборки. Универсальная платформа Windows для Xbox One и ПК поддерживает 8 кГц, 12 кГц, 16 кГц, кГц 24, 32 кГц, 44,1 кГц и частоты выборки 48 кГц.

### <a name="retrieving-and-submitting-audio"></a>Получения и передачи аудио
Приложения могут запрашивать предварительное кодирование аудиопотоков для количество буферов, доступных для обработки с помощью `pre_encode_audio_stream::get_available_buffer_count()`. Эти сведения можно использовать, если приложению требуется отложить обработку аудио, пока не станут доступными минимальное количество буферов. Только 10 буферы будут помещаться в очередь на каждом предварительное кодирование аудиопотока и аудио задержки будет приводить к задержкам в конвейере аудио, поэтому рекомендуется приложений выполнить сток их предварительное кодирование аудиопотоков, прежде чем они более 4 буферов в очередь.

Приложения можно получить аудио буферы из предварительно кодирования аудио-поток с помощью `pre_encode_audio_stream::get_next_buffer()`. Новые аудио буферов будет предоставляться в среднем, один раз каждые 40 мс. Буферы, возвращаемого этим методом должно быть запущено в `pre_encode_audio_stream::return_buffer()` после работы используется. В очереди более 10 или unreturned буферы могут существовать в любой момент времени для предварительное кодирование аудиопотока. Когда этот предел достигнут, новых буферах, полученных при аудиоисточник игрока будут удалены, пока не будут возвращены, необработанных буферов.

Приложения могут отправлять свои буферы проверявшихся и полученные в результате изменений, обратно на 2-чат игры для кодирования и передачи с помощью `pre_encode_audio_stream::submit_buffer()`. Игр Chat 2 поддерживает на месте и месте вне манипуляции аудио, поэтому буферы отправки `pre_encode_audio_stream::submit_buffer()` не обязательно быть же буферы, полученные из `pre_encode_audio_stream::get_next_buffer()`. Конфиденциальность/привилегий для отправленного буфер будут применены на пользователей, связанных с этого потока. Каждые 40 мс, далее 40 мс звука из этого потока будет кодировать и передачи. Чтобы предотвратить аудио выдерживает любые проблемы, буферы для аудио, который должен быть слышен постоянно отправлять в этот поток с постоянной скоростью.

### <a name="stream-contexts"></a>Контексты Stream
Приложения можно управлять значениями пользовательского контекстного размера указателя на предварительное кодирование аудиопотоков с помощью `pre_encode_audio_stream::set_custom_stream_context()` и `pre_encode_audio_stream::custom_stream_context()`. Эти контексты пользовательского потока могут быть полезны для создания сопоставлений между аудиопотоков и auxillary данных игр Chat 2: потоковая передача метаданных, состояния игры и т. д.

### <a name="example"></a>Пример
Ниже приведен упрощенный пример end-to-end по использованию предварительное кодирование аудиопотоков в кадре один обработки звука:

```cpp
uint32_t streamStateChangeCount;
game_chat_stream_state_change_array streamStateChanges;
chat_manager::singleton_instance().start_processing_stream_state_changes(&streamStateChangeCount, &streamStateChanges);

for (uint32_t streamStateChangeIndex = 0; streamStateChangeIndex < streamStateChangeCount; ++streamStateChangeIndex)
{
    switch (streamStateChanges[streamStateChangeIndex]->state_change_type)
    {
        case game_chat_stream_state_change_type::pre_encode_audio_stream_created:
        {
            pre_encode_audio_stream* stream = streamStateChanges[streamStateChangeIndex]->pre_encode_audio_stream;
            stream->set_processed_audio_format(...);
            stream->set_custom_stream_context(...);
            HandlePreEncodeAudioStreamCreated(stream);
            break;
        }

        case game_chat_2::game_chat_stream_state_change_type::pre_encode_audio_stream_closed:
        {
            HandlePreEncodeAudioStreamClosed(streamStateChanges[streamStateChangeIndex].pre_encode_audio_stream);
            break;
        }

        case game_chat_2::game_chat_stream_state_change_type::pre_encode_audio_stream_destroyed:
        {
            HandlePreEncodeAudioStreamDestroyed(streamStateChanges[streamStateChangeIndex].pre_encode_audio_stream);
            break;
        }

        ...
    }
}
chat_manager::singleton_instance().finish_processing_stream_state_changes(streamStateChanges);

uint32_t preEncodeAudioStreamCount;
pre_encode_audio_stream_array preEncodeAudioStreams;
chat_manager::singleton_instance().get_pre_encode_audio_streams(&preEncodeAudioStreamCount, &preEncodeAudioStreams);
for (uint32_t preEncodeAudioStreamIndex = 0; preEncodeAudioStreamIndex < preEncodeAudioStreamCount; ++preEncodeAudioStreamIndex)
{
    pre_encode_audio_stream* stream = preEncodeAudioStreams[preEncodeAudioStreamIndex];
    StreamContext* context = reinterpret_cast<StreamContext*>(stream->custom_stream_context());

    game_chat_audio_format audio_format = stream->get_pre_processed_format();

    uint32_t preProcessedBufferByteCount;
    void* preProcessedBuffer;
    stream->get_next_buffer(&preProcessedBufferByteCount, &preProcessedBuffer);

    while (preProcessedBuffer != nullptr)
    {
        void* processedBuffer = nullptr;
        switch (audio_format.bits_per_sample)
        {
            case 16:
            {
                assert (audio_format.sample_type == game_chat_sample_type::integer);
                processedBuffer = ManipulateChatBuffer<int16_t>(preProcessedBufferByteCount, preProcessedBuffer, context);
                break;
            }

            case 32:
            {
                switch (audio_format.sample_type)
                {
                    case game_chat_sample_type::integer:
                    {
                        processedBuffer = ManipulateChatBuffer<int32_t>(preProcessedBufferByteCount, preProcessedBuffer, context);
                        break;
                    }

                    case game_chat_sample_type::ieee_float:
                    {
                        processedBuffer = ManipulateChatBuffer<float>(preProcessedBufferByteCount, preProcessedBuffer, context);
                        break;
                    }

                    default:
                    {
                        assert(false);
                        break;
                    }
                }
                break;
            }

            default:
            {
                assert(false);
                break;
            }
        }
        // processedBuffer can be the same as preProcessedBuffer (in-place manipulation) or it can be a buffer of
        // memory not managed by Game Chat 2 (out-of-place manipulation).
        stream->submit_buffer(processedBuffer);
        // Only return buffers retrieved from Game Chat 2. Do not return foreign memory to return_buffer.
        stream->return_buffer(preProcessedBuffer);
        stream->get_next_buffer(&preProcessedBufferByteCount, &preProcessedBuffer);
    }
}

Sleep(audioProcessingPeriodInMilliseconds);
```

## <a name="manipulating-post-decode-chat-audio-data"></a>Обработка после декодирования чата звуковых данных

Игры чата 2 предоставляет доступ к после расшифровки разговора звуковых данных через `post_decode_audio_source_stream` и `post_decode_audio_sink_stream` классы, таким образом, пользователи могут управлять звукового сигнала при помощи удаленным пользователям уникально для каждого локального получателя чата звука.

### <a name="sources-and-sinks"></a>Источники и приемники
В отличие от pre-encode конвейер, модель для работы с после декодирования звуковых данных разделяются на два класса: `post_decode_audio_source_stream` и `post_decode_audio_sink_stream`. Декодированный аудио от удаленных пользователей, которые могут быть получены из `post_decode_audio_source_stream` объектов, управлять и отправляемые `post_decode_audio_sink_stream` объектов для подготовки к просмотру. Таким образом, для интеграции между игры Chat 2 после декодирования конвейера обработки звука и полезные аудио по промежуточного слоя.

### <a name="stream-lifetime"></a>Время существования Stream
При создании нового `post_decode_audio_source_stream` или `post_decode_audio_sink_stream` экземпляр готов для использования приложением, оно будет доставлено через `game_chat_stream_state_change` структуру с его `state_change_type` поле "значение" `game_chat_stream_state_change_type::post_decode_audio_source_stream_created` или `game_chat_stream_state_change_type::post_decode_audio_sink_stream_created`, соответственно. После этого изменения состояния потока возвращается 2 Chat игры, станет доступным для аудиопотока после декодирования аудио манипуляции.

Если в имеющемся `post_decode_audio_source_stream` или `post_decode_audio_sink_stream` становится недоступна для обработки звука, приложение будет уведомлен через `game_chat_stream_state_change` структуру с его `state_change_type` поле "значение" `game_chat_stream_state_change_type::post_decode_audio_source_stream_closed` или `game_chat_stream_state_change_type::post_decode_audio_sink_stream`, соответственно. Это возможность запустить очистку ресурсов, связанных с этой звукового потока приложения. После этого изменения состояния потока возвращается 2 Chat игры, аудиопотока становятся недоступными для после декодирования аудио манипуляции. Для потоков источника это означает, что Дополнительные буферы не будут помещаться в очередь, для обработки. Для потоков в качестве приемника это означает, что, отправки буферы больше не будут передаваться.

При закрытом `post_decode_audio_source_stream` или `post_decode_audio_sink_stream` все его ресурсы вернулся, уничтожении потока и приложение будет уведомлен через `game_chat_stream_state_change` структуру с его `state_change_type` поле "значение" `game_chat_stream_state_change_type::post_decode_audio_source_stream_destroyed` или `game_chat_stream_state_change_type::post_decode_audio_sink_stream_destroyed`, соответственно. Все ссылки или указатели на этот поток, будет очищена. После этого изменения состояния потока возвращается 2 Chat игры, звуковой поток памяти, станут недействительными.

### <a name="stream-users"></a>Пользователи Stream
Список удаленных пользователей, связанные с исходным потоком post-decode можно проверить с помощью `post_decode_audio_source_stream::get_users()`. Список локальных пользователей, связанную с потоком приемника post-decode можно проверить с помощью `post_decode_audio_sink_stream::get_users()`.

### <a name="audio-formats"></a>Форматы аудио
Звуковой формат буферов, приложение извлекает из игры Chat 2, можно проверить с помощью `post_decode_audio_source_stream::get_pre_processed_format()`. Предварительно обработанного звуковой формат всегда будет mono 16-битное целое число PCM.

Приложение необходимо сообщить игры Chat 2 аудио формате манипулируемого буферов, которые передаются в него для рендеринга с помощью `post_decode_audio_sink_stream::set_processed_format()`. Обработанные форматы для после декодирования аудио потоки приемника должны соответствовать эти предварительные условия:

* Формат должен иметь меньше 64 каналов.
* Формат должен быть 16-разрядное целое число (оптимального) PCM, 20-разрядное целое число PCM (в контейнере 24-разрядный), 24-разрядное целое число PCM, 32-разрядное целое число PCM или 32-разрядное число с плавающей запятой PCM (предпочтительный формат после 16-разрядное целое число PCM). 
* Формат частота выборки должна составлять от 1000 до 200000 выборок в секунду.

### <a name="retrieving-and-submitting-audio"></a>Получения и передачи аудио
Приложения могут запрашивать после декодирования аудио исходные потоки для количество буферов, доступных для обработки с помощью `post_decode_audio_source_stream::get_available_buffer_count()`. Эти сведения можно использовать, если приложению требуется отложить обработку аудио, пока не станут доступными минимальное количество буферов. Только 10 буферы будут помещаться в очередь на каждом после декодирования потока источника звуковых и аудио задержки будет приводить к задержкам в конвейере аудио, поэтому рекомендуется приложений выполнить сток их после декодирования аудиопотоков, прежде чем они более 4 буферов в очередь.

Приложения можно получить аудио буферы из после декодирования потока с помощью аудиоисточник `post_decode_audio_source_stream::get_next_buffer()`. Новые аудио буферов будет предоставляться в среднем, один раз каждые 40 мс. Буферы, возвращаемого этим методом должно быть запущено в `post_decode_audio_source_stream::return_buffer()` после работы используется. В очереди более 10 или unreturned буферы могут существовать в любой момент времени для после декодирования потока аудиоисточник. Когда этот предел достигнут, новых буферах декодированные из удаленного проигрывателя будут удалены, пока не будут возвращены, необработанных буферов.

Приложения могут отправлять свои буферы проверявшихся и полученные в результате изменений обратно в игру Chat 2 – после декодирования потоки аудио приемника для рендеринга с помощью `post_decode_audio_sink_stream::submit_mixed_buffer()`. Игр Chat 2 поддерживает на месте и месте вне манипуляции аудио, поэтому буферы отправки `post_decode_audio_sink_stream::submit_mixed_buffer()` не обязательно быть же буферы, полученные из `post_decode_audio_source_stream::get_next_buffer()`. Каждые 40 мс, будут передаваться Далее 40 мс звука из текущего потока. Чтобы предотвратить аудио выдерживает любые проблемы, буферы для аудио, который должен быть слышен постоянно отправлять в этот поток с постоянной скоростью.

### <a name="privacy-and-mixing"></a>Смешивание и конфиденциальности
Из-за модель в конвейер post-decode источник приемник, отвечает за приложения смешивать буферы, полученные из `post_decode_audio_source_stream` объектов и смешанной буферов для отправки `post_decode_audio_sink_stream` объектов для подготовки к просмотру. Это также означает, что это приложение отвечает за выполнение набора с правильной конфиденциальности и прав доступа, которые применяются. Игры 2-чат предоставляет `post_decode_audio_sink_stream::can_receive_audio_from_source_stream()` чтобы сделать запрос этой информации просто и эффективно.

### <a name="chat-indicators"></a>Индикаторы чата

После декодирования аудио манипуляции не влияет на состояние индикатора чата для каждого пользователя. Например при удаленный пользователь так заметны, звук будет предоставляться в приложение, но индикатор чата для этого удаленного пользователя будут по-прежнему возникают звук. Разговоре удаленный пользователь будет предоставляться их аудио, но индикатор чата укажет разговор аудио смешиваться содержащий звукового сигнала при помощи этого пользователя не предоставляет приложение. Дополнительные сведения о пользовательского интерфейса и индикатор чата, см. в разделе [с использованием 2 чата игры](using-game-chat-2.md#ui). Если ограничения очень специфичные для конкретного приложения используются для определения пользователей, которые присутствуют в аудио смешиваться, отвечает приложения необходимо учитывать те же ограничения, при чтении индикаторы чата, предоставляемые 2 чата игры.

### <a name="stream-contexts"></a>Контексты Stream
Приложения можно управлять значениями пользовательского контекстного размера указателя на после декодирования аудиопотоков с помощью `set_custom_stream_context()` и `custom_stream_context()` методы. Эти контексты пользовательского потока могут быть полезны для создания сопоставлений между аудиопотоков и auxillary данных игр Chat 2: потоковая передача метаданных, состояния игры и т. д.

### <a name="example"></a>Пример
Ниже приведен упрощенный пример end-to-end использования после декодирования аудиопотоков в кадре один обработки звука:

```cpp
uint32_t streamStateChangeCount;
game_chat_stream_state_change_array streamStateChanges;
chat_manager::singleton_instance().start_processing_stream_state_changes(&streamStateChangeCount, &streamStateChanges);

for (uint32_t streamStateChangeIndex = 0; streamStateChangeIndex < streamStateChangeCount; ++streamStateChangeIndex)
{
    switch (streamStateChanges[streamStateChangeIndex]->state_change_type)
    {
        case game_chat_stream_state_change_type::post_decode_audio_source_stream_created:
        {
            post_decode_audio_source_stream* stream = streamStateChanges[streamStateChangeIndex]->post_decode_audio_source_stream;
            stream->set_custom_stream_context(...);
            HandlePostDecodeAudioSourceStreamCreated(stream);
            break;
        }

        case game_chat_stream_state_change_type::post_decode_audio_source_stream_closed:
        {
            HandlePostDecodeAudioSourceStreamClosed(stream);
            break;
        }

        case game_chat_stream_state_change_type::post_decode_audio_source_stream_destroyed:
        {
            HandlePostDecodeAudioSourceStreamDestroyed(stream);
            break;
        }

        case game_chat_stream_state_change_type::post_decode_audio_sink_stream_created:
        {
            post_decode_audio_sink_stream* stream = streamStateChanges[streamStateChangeIndex]->post_decode_audio_sink_stream;
            stream->set_custom_stream_context(...);
            stream->set_processed_format(...);
            HandlePostDecodeAudioSinkStreamCreated(stream);
            break;
        }

        case game_chat_stream_state_change_type::post_decode_audio_sink_stream_closed:
        {
            HandlePostDecodeAudioSinkStreamClosed(stream);
            break;
        }

        case game_chat_stream_state_change_type::post_decode_audio_sink_stream_destroyed:
        {
            HandlePostDecodeAudioSinkStreamDestroyed(stream);
            break;
        }

        ...
    }
}

chat_manager::singleton_instance().finish_processing_stream_state_changes(streamStateChanges);

uint32_t sourceStreamCount;
post_decode_audio_source_stream_array sourceStreams;
chatManager::singleton_instance().get_post_decode_audio_source_streams(&sourceStreamCount, &sourceStreams);

uint32_t sinkStreamCount;
post_decode_audio_sink_stream_array sinkStreams;
chatManager::singleton_instance().get_post_decode_audio_sink_streams(&sinkStreamCount, &sinkStreams);

//
// MixBuffer is a custom type defined as:
// struct MixBuffer
// {
//     uint32_t bufferByteCount;
//     void* buffer;
// };
//
std::vector<std::pair<post_decode_audio_source_stream*, MixBuffer>> cachedSourceBuffers;

for (uint32_t sourceStreamIndex = 0; sourceStreamIndex < sourceStreamCount; ++sourceStreamIndex)
{
    post_decode_audio_source_stream* sourceStream = sourceStreams[sourceStreamIndex];

    MixBuffer mixBuffer;
    sourceStream->get_next_buffer(&mixBuffer.bufferByteCount, &mixBuffer.buffer);
    if (buffer != nullptr)
    {
        // Stash the buffer to return after we are done with mixing. If this program was using audio middleware, now
        // would be an appropriate time to plumb the buffer through the middleware
        cachedSourceBuffer.push_back(std::pair<post_decode_audio_source_stream*, MixBuffer>{sourceStream, mixBuffer});
    }
}

// Loop over each sink stream, perform mixing, and submit
for (uint32_t sinkStreamIndex = 0; sinkStreamIndex < sinkStreamCount; ++sinkStreamIndex)
{
    post_decode_audio_sink_stream* sinkStream = sinkStreams[sinkStreamIndex];

    if (sinkStream->is_open())
    {
        std::vector<std::pair<MixBuffer, float>> buffersToMixForThisStream;

        for (const std::pair<post_decode_audio_source_stream, MixBuffer>& sourceBufferPair : cachedSourceBuffers)
        {
            float volume;
            if (sinkStream->can_receive_audio_from_source_stream(sourceBufferPair.first, &volume))
            {
                buffersToMixForThisStream.push_back(std::pair<MixBuffer, float>{sourceBufferPair.second, volume});
            }
        }

        if (buffersToMixForThisStream.size() > 0)
        {
            uint32_t mixedBufferByteCount;
            uint8_t* mixedBuffer;
            MixPostDecodeBuffers(buffersToMixForThisStream, &mixedBufferByteCount, &mixedBuffer);
            sinkStream->submit_mixed_buffer(mixedBufferByteCount, mixedBuffer);
        }
    }
}

// Return buffers after mix and submission
for (const std::pair<post_decode_audio_source_stream*, MixBuffer>& cachedSourceBuffer : cachedSourceBuffers)
{
    post_decode_audio_source_stream* sourceStream = cachedSourceBuffer.first;
    void* bufferToReturn = cachedSourceBuffer.second.buffer;
    sourceStream->return_buffer(bufferToReturn);
}

Sleep(audioProcessingPeriodInMilliseconds);
```

## <a name="chat-user-lifetimes"></a>Время существования пользователя чата

Включение в реальном времени аудио манипуляции повлияет на время существования пользователей чата. Если `chat_manager::remove_user(chatUserX)` называется chat_user объекта, на которые указывают chatUserX будет действительным, пока не будут уничтожены все звуковые потоки, которые ссылаются на chatUserX. Рассмотрим следующий сценарий.

```cpp
// At somepoint a chat user, chatUserX, leaves the game session.
chat_manager::singleton_instance().remove_user(chatUserX);

// chatUserX is still valid, but to avoid further synchronization, prevent non-audio-stream use of chatUserX
chatUserX = nullptr;

// On the audio processing thread...
uint32_t streamStateChangeCount;
game_chat_stream_state_change_array streamStateChanges;
chat_manager::singleton_instance().start_processing_stream_state_changes(&streamStateChangeCount, &streamStateChanges);
for (uint32_t streamStateChangeIndex = 0; streamStateChangeIndex < streamStateChangeCount; ++streamStateChangeIndex)
{
    switch (streamStateChanges[streamStateChangeIndex]->state_change_type)
    {
        ...

        // All of the streams associated with chatUserX will close.
        case Xs::game_chat_2::game_chat_stream_state_change_type::pre_encode_audio_stream_closed:
        {
            CleanupPreEncodeAudioStreamResources(streamStateChanges[streamStateChangeIndex].pre_encode_audio_stream);
            break;
        }

        ...
    }
}
chat_manager::singleton_instance().finish_processing_stream_state_changes(streamStateChanges);

// The next time the app processes stream state changes...
uint32_t streamStateChangeCount;
game_chat_stream_state_change_array streamStateChanges;
chat_manager::singleton_instance().start_processing_stream_state_changes(&streamStateChangeCount, &streamStateChanges);
for (uint32_t streamStateChangeIndex = 0; streamStateChangeIndex < streamStateChangeCount; ++streamStateChangeIndex)
{
    switch (streamStateChanges[streamStateChangeIndex]->state_change_type)
    {
        ...

        case Xs::game_chat_2::game_chat_stream_state_change_type::pre_encode_audio_stream_destroyed:
        {
            uint32_t chatUserCount;
            Xs::game_chat_2::chat_user_array chatUsers;
            streamStateChanges[streamStateChangeIndex].pre_encode_audio_stream->get_users(&chatUserCount, &chatUsers);
            assert(chatUserCount != 0);
            for (uint32_t chatUserIndex = 0; chatUserIndex < chatUserCount; ++chatUserIndex)
            {
                // chat_user objects such as chatUserX will still be valid while the destroyed state change is being processed.
                Log(chatUsers[chatUserIndex]->xbox_user_id());
            }
            break;
        }

        ...
    }
}
chat_manager::singleton_instance().finish_processing_stream_state_changes(streamStateChanges);
// Once the all destroyed state changes have been processed for all streams associated with chatUserX, it's memory will be invalidated.
// Do not call methods on chatUserX, e.g. chatUserX->xbox_user_id()
```
