---
title: Добавление звука
description: Разработка простой звуковые подсистемы с помощью API XAudio2 для воспроизведения музыки в игре и звуковые эффекты.
ms.assetid: aa05efe2-2baa-8b9f-7418-23f5b6cd2266
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, звуки
ms.localizationpriority: medium
ms.openlocfilehash: 94044e3d10df15cb1cb256d86ced798395e6af6f
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7973919"
---
# <a name="add-sound"></a>Добавление звука

В этом разделе мы создадим простое звуковой подсистемы с помощью [XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415813) API-интерфейсы. Если вы не знакомы с __XAudio2__, которые мы включили Краткое введение в [концепции звука](#audio-concepts).

>[!Note]
>Если вы еще не скачали последний код для игры из этого примера, перейдите в раздел [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

## <a name="objective"></a>Цель

Добавьте звуки в примере игры, с помощью [XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415813).

## <a name="define-the-audio-engine"></a>Определение обработчика звука

В образце игры объекты аудио и расширения их функциональности определены в трех файлах:

* __[Audio.h](#audioh)/.cpp__: Определяет объект __воспроизведения звука__ , который содержит ресурсы __XAudio2__ для воспроизведения звука. Он также определяет метод приостановки и возобновления воспроизведения звука в случаях приостановки или деактивации игры.
* __ [MediaReader.h](#mediareaderh)/.cpp__: Определяет методы чтения звуковых WAV-файлов из локального хранилища.
* __ [SoundEffect.h](#soundeffecth)/.cpp__: Определяет объект для воспроизведения звука в игре.

## <a name="overview"></a>Обзор

Существует три основных частей в Подготовка для воспроизведения звука в игре.

1. [Создание и инициализация звуковых ресурсов](#create-and-initialize-the-audio-resources)
2. [Загрузка звукового файла](#load-audio-file)
3. [Связывание звук объект](#associate-sound-to-object)

Все они определены в методе [Simple3DGame::Initialize](#simple3dgameinitialize-method) . Поэтому давайте сначала рассмотрим этот метод и затем углубиться в подробности в каждом из разделов.

После настройки, мы Узнайте, как активировать звуковые эффекты для воспроизведения. Дополнительные сведения перейдите к [воспроизведения звука](#play-the-sound).

### <a name="simple3dgameinitialize-method"></a>Метод Simple3DGame::Initialize

В __Simple3DGame::Initialize__, где __m\_controller__ и __m\_renderer__ также инициализируются, мы Настройка звука и подготовьтесь к звуки.

 * Создайте __m\_audioController__, которое является экземпляром класса [звука](#audioh) .
 * Создание звуковых ресурсов, необходимых, используя метод [Audio::CreateDeviceIndependentResources](#audiocreatedeviceindependentresources-method) . В данном случае — двух объектов __XAudio2__ &mdash; были созданы объекте обработчика музыки и объект звуковая подсистема и управляющее аудиоустройство для каждого из них. Для воспроизведения фоновой музыки в игре используется объекте обработчика музыки. Звуковая подсистема может использоваться для воспроизведения звуковых эффектов в игру. Дополнительные сведения см. в разделе [Создание и инициализация звуковых ресурсов](#create-and-initialize-the-audio-resources).
 * Создайте __mediaReader__, которое является экземпляром класса [MediaReader](#mediareaderh) . [MediaReader](#mediareaderh), который является вспомогательный класс для класса [SoundEffect](#soundeffecth) , синхронно считывает небольшое звуковые файлы из расположения и возвращает звуковых данных как массив байтов.
 * Используйте [MediaReader::LoadMedia](#mediareaderloadmedia-method) для загрузки звуковых файлов из места расположения и создайте переменную __targetHitSound__ для хранения загруженного формате WAV звуковых данных. Дополнительные сведения см. в разделе [Загрузка звукового файла](#load-audio). 

Звуковые эффекты связаны с игровой объект. Поэтому в случае конфликта с помощью этого объекта игры активирует звуковой эффект, который будет воспроизводиться. В этом примере игры у нас есть звуковые эффекты для сферы, (что мы используем для прокрутить целевых объектов с), а также для целевого объекта. 
    
* В классе __GameObject__ существует свойство __HitSound__ , используемом для связывания звуковой эффект к объекту.
* Создайте новый экземпляр класса [SoundEffect](#soundeffecth) и инициализировать его. Во время инициализации создается исходный тембр звукового эффекта. 
* Этот класс воспроизводится звук с помощью управляющей речи, предоставляемый по класс [Audio](#audioh) . Звуковые данные считывается из расположения с помощью класса [MediaReader](#mediareaderh) . Дополнительные сведения см. в разделе [связать звук для объекта](#associate-sound-to-object).

>[!Note]
>Фактическое триггер для воспроизведения звука, определяется движения и столкновения этих игровых объектов. Следовательно вызов фактически эти звуки определены в методе [Simple3DGame::UpdateDynamics](#simple3dgameupdatedynamics-method) . Дополнительные сведения перейдите к [воспроизведения звука](#play-the-sound).

```cpp
void Simple3DGame::Initialize(
    _In_ MoveLookController^ controller,
    _In_ GameRenderer^ renderer
    )
{
    // ...
    // Create a new Audio class object.
    m_audioController = ref new Audio;

    // Create the audio resources needed.
    // Two XAudio2 objects are created - one for music engine,
    // the other for sound engine. A mastering voice is also
    // created for each of the objects.
    m_audioController->CreateDeviceIndependentResources();

    m_ammo = std::vector<Sphere^>(GameConstants::MaxAmmo);

    // ..
    // Create a media reader which is used to read audio files from its file location.
    MediaReader^ mediaReader = ref new MediaReader;
    auto targetHitSound = mediaReader->LoadMedia("Assets\\hit.wav");

    // Instantiate the targets for use in the game.
    // Each target has a different initial position, size, and orientation.
    // But share a common set of material properties.
    for (int a = 1; a < GameConstants::MaxTargets; a++)
    {
        // ...
        // Create a new sound effect object and associate it
        // with the game object's (target) HitSound property.
        target->HitSound(ref new SoundEffect());

        // Initialize the sound effect object with
        // the sound effect engine, format of the audio wave, and audio data
        // During initialization, source voice of this sound effect is also created.
        target->HitSound()->Initialize(
            m_audioController->SoundEffectEngine(),
            mediaReader->GetOutputWaveFormatEx(),
            targetHitSound
            );
        // ...
    }

    // Instantiate a set of spheres to be used as ammunition for the game
    // and set the material properties of the spheres.
    auto ammoHitSound = mediaReader->LoadMedia("Assets\\bounce.wav");

    for (int a = 0; a < GameConstants::MaxAmmo; a++)
    {
        m_ammo[a] = ref new Sphere;
        m_ammo[a]->Radius(GameConstants::AmmoRadius);
        m_ammo[a]->HitSound(ref new SoundEffect());
        m_ammo[a]->HitSound()->Initialize(
            m_audioController->SoundEffectEngine(),
            mediaReader->GetOutputWaveFormatEx(),
            ammoHitSound
            );
        m_ammo[a]->Active(false);
        m_renderObjects.push_back(m_ammo[a]);
    }
    // ...
}
```

## <a name="create-and-initialize-the-audio-resources"></a>Создание и инициализация звуковых ресурсов

* Используйте [XAudio2Create](https://msdn.microsoft.com/library/windows/desktop/ee419212), XAudio2 API, для создания двух объектов XAudio2, которые определяют подсистемы музыки и звуковых эффектов. Этот метод возвращает указатель на интерфейс [IXAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415908) объекта, который управляет все состояния звука, звук, обработки потока, на графике голосовой связи и многое другое.
* После модули были созданы, используйте [IXAudio2::CreateMasteringVoice](https://msdn.microsoft.com/library/windows/desktop/hh405048) , чтобы создать тембр мастеринга для каждого из объектов звуковая подсистема.

Дополнительные сведения см. в [как: инициализация XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415779.aspx).

### <a name="audiocreatedeviceindependentresources-method"></a>Метод Audio::CreateDeviceIndependentResources

```cpp

void Audio::CreateDeviceIndependentResources()
{
    UINT32 flags = 0;

    DX::ThrowIfFailed(
        XAudio2Create(&m_musicEngine, flags)
        );

    HRESULT hr = m_musicEngine->CreateMasteringVoice(&m_musicMasteringVoice);
    if (FAILED(hr))
    {
        // Unable to create an audio device
        m_audioAvailable = false;
        return;
    }

    DX::ThrowIfFailed(
        XAudio2Create(&m_soundEffectEngine, flags)
        );

    DX::ThrowIfFailed(
        m_soundEffectEngine->CreateMasteringVoice(&m_soundEffectMasteringVoice)
        );

    m_audioAvailable = true;
}
```

## <a name="load-audio-file"></a>Загрузка звукового файла

В образце игры код для чтения файлов в формате аудио определяется в [MediaReader.h](#mediareaderh)/cpp__.  Считывать закодированный WAV-файл, вызовите метод [MediaReader::LoadMedia](#mediareaderloadmedia-method), передав filename формате WAV как входного параметра.

### <a name="mediareaderloadmedia-method"></a>Метод MediaReader::LoadMedia

Данный метод использует API [Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms694197) для считывания звукового файла в формате WAV в виде буфера импульсно-кодовой модуляции (ИКМ).

#### <a name="set-up-the-source-reader"></a>Настройка устройства чтения источника

1. Используйте [MFCreateSourceReaderFromURL](https://msdn.microsoft.com/library/windows/desktop/dd388110) для создания носителя для устройства чтения источника ([IMFSourceReader](https://msdn.microsoft.com/library/windows/desktop/dd374655)).
2. Используйте [MFCreateMediaType](https://msdn.microsoft.com/library/windows/desktop/ms693861) для создания объекта ([IMFMediaType](https://msdn.microsoft.com/library/windows/desktop/ms704850)) тип мультимедиа (_mediaType_). Он представляет описание формата мультимедиа. 
3. Укажите, что _тип носителя_декодированный вывод звуковой файл PCM, представляющий собой тип звукового, который может использовать __XAudio2__ .
4. Наборы тип декодированного выходящего мультимедиа для устройства чтения источника путем вызова [IMFSourceReader::SetCurrentMediaType](https://msdn.microsoft.com/library/windows/desktop/dd374667.aspx).

Дополнительные сведения о почему мы используем устройства чтения источника перейдите к [Устройства чтения источника](https://msdn.microsoft.com/library/windows/desktop/dd940436.aspx).

#### <a name="describe-the-data-format-of-the-audio-stream"></a>Описать формат звукового потока данных

1. Используйте [IMFSourceReader::GetCurrentMediaType](https://msdn.microsoft.com/library/windows/desktop/dd374660) , чтобы получить текущий тип носителя для потока.
2. Используйте [IMFMediaType::MFCreateWaveFormatExFromMFMediaType](https://msdn.microsoft.com/library/windows/desktop/ms702177) для преобразования звуковых носитель в буфер [WAVEFORMATEX](https://msdn.microsoft.com/library/windows/hardware/ff538799) , используя результаты предыдущих операции в качестве входных данных. Эта структура задает формат данных первыми звукового потока, используемый после загрузки звука. 

Формат __WAVEFORMATEX__ может использоваться для описания буфера PCM. По сравнению с структуры [WAVEFORMATEXTENSIBLE](https://msdn.microsoft.com/library/windows/hardware/ff538802) она может использоваться только для описания подмножество форматов звуковой волны. Дополнительные сведения о различиях между __WAVEFORMATEX__ и __WAVEFORMATEXTENSIBLE__см. в разделе [Расширяемый формат звукозаписи дескрипторов](https://docs.microsoft.com/windows-hardware/drivers/audio/extensible-wave-format-descriptors).

#### <a name="read-the-audio-stream"></a>Чтение звукового потока

1.  Получите продолжительность в секундах, звукового потока, вызвав метод [IMFSourceReader::GetPresentationAttribute](https://msdn.microsoft.com/library/windows/desktop/dd374662) , а затем преобразует продолжительность в байтах.
2.  Чтение звукового файла в виде потока, вызвав метод [IMFSourceReader::ReadSample](https://msdn.microsoft.com/library/windows/desktop/dd374665). Следующий пример считывает __ReadSample__ из источника мультимедиа.
3.  Используйте [IMFSample::ConvertToContiguousBuffer](https://msdn.microsoft.com/library/windows/desktop/ms698917.aspx) , чтобы скопировать содержимое буфера аудиоданные (_Пример_) в массив (_mediaBuffer_).

```cpp
Platform::Array<byte>^ MediaReader::LoadMedia(_In_ Platform::String^ filename)
{
    DX::ThrowIfFailed(
        MFStartup(MF_VERSION)
        );

    // Creates a media source reader.
    ComPtr<IMFSourceReader> reader;
    DX::ThrowIfFailed(
        MFCreateSourceReaderFromURL(
            Platform::String::Concat(m_installedLocationPath, filename)->Data(),
            nullptr,
            &reader
            )
        );

    // Set the decoded output format as PCM.
    // XAudio2 on Windows can process PCM and ADPCM-encoded buffers.
    // When using MediaFoundation, this sample always decodes into PCM.
    Microsoft::WRL::ComPtr<IMFMediaType> mediaType;
    DX::ThrowIfFailed(
        MFCreateMediaType(&mediaType)
        );

    // Define the major category of the media as audio. For more info about major media types,
    // go to: https://msdn.microsoft.com/library/windows/desktop/aa367377.aspx
    DX::ThrowIfFailed(
        mediaType->SetGUID(MF_MT_MAJOR_TYPE, MFMediaType_Audio)
        );

    // Define the sub-type of the media as uncompressed PCM audio. For more info about audio sub-types,
    // go to: https://msdn.microsoft.com/library/windows/desktop/aa372553.aspx
    DX::ThrowIfFailed(
        mediaType->SetGUID(MF_MT_SUBTYPE, MFAudioFormat_PCM)
        );
    
    // Sets the media type for a stream. This media type defines that format that the Source Reader 
    // produces as output. It can differ from the native format provided by the media source.
    // For more info, go to https://msdn.microsoft.com/library/windows/desktop/dd374667.aspx
    DX::ThrowIfFailed(
        reader->SetCurrentMediaType(static_cast<uint32>(MF_SOURCE_READER_FIRST_AUDIO_STREAM), 0, mediaType.Get())
        );

    // Get the current media type for the stream.
    // For more info, go to:
    // https://msdn.microsoft.com/library/windows/desktop/dd374660.aspx
        Microsoft::WRL::ComPtr<IMFMediaType> outputMediaType;
    DX::ThrowIfFailed(
        reader->GetCurrentMediaType(static_cast<uint32>(MF_SOURCE_READER_FIRST_AUDIO_STREAM), &outputMediaType)
        );

    // Converts the current media type into the WaveFormatEx buffer structure.
    UINT32 size = 0;
    WAVEFORMATEX* waveFormat;
    DX::ThrowIfFailed(
        MFCreateWaveFormatExFromMFMediaType(outputMediaType.Get(), &waveFormat, &size)
        );

    // Copies the waveFormat's block of memory to the starting address of the m_waveFormat variable in MediaReader.
    // Then free the waveFormat memory block.
    // For more info, go to https://msdn.microsoft.com/library/windows/desktop/aa366535.aspx and
    // https://msdn.microsoft.com/library/windows/desktop/ms680722.aspx
    CopyMemory(&m_waveFormat, waveFormat, sizeof(m_waveFormat));
    CoTaskMemFree(waveFormat);

    PROPVARIANT propVariant;
    DX::ThrowIfFailed(
        reader->GetPresentationAttribute(static_cast<uint32>(MF_SOURCE_READER_MEDIASOURCE), MF_PD_DURATION, &propVariant)
        );

    // 'duration' is in 100ns units; convert to seconds, and round up
    // to the nearest whole byte.
    LONGLONG duration = propVariant.uhVal.QuadPart;
    unsigned int maxStreamLengthInBytes =
        static_cast<unsigned int>(
            ((duration * static_cast<ULONGLONG>(m_waveFormat.nAvgBytesPerSec)) + 10000000) /
            10000000
            );

    Platform::Array<byte>^ fileData = ref new Platform::Array<byte>(maxStreamLengthInBytes);

    ComPtr<IMFSample> sample;
    ComPtr<IMFMediaBuffer> mediaBuffer;
    DWORD flags = 0;

    int positionInData = 0;
    bool done = false;
    while (!done)
    {
        //...
        // Read audio data.
    }

    return fileData;
}
```
## <a name="associate-sound-to-object"></a>Связывание звук объект

Связывание звуков, чтобы объект имеет место при инициализации игры в методе [Simple3DGame::Initialize](#simple3dgameinitialize-method) .

Краткий обзор:
* В классе __GameObject__ существует свойство __HitSound__ , используемом для связывания звуковой эффект к объекту.
* Создайте новый экземпляр объекта класса [SoundEffect](#soundeffecth) и связать его с игровой объект. Этот класс воспроизводится звук с помощью __XAudio2__ API-интерфейсы.  Он использует аудиоустройстве, предоставленных классом [звука](#audioh) . Звуковые данные могут считываться из расположения с помощью класса [MediaReader](#mediareaderh) .

[SoundEffect::Initialize](#soundeffectinitialize-method) используется для инициализации экземпляра __SoundEffect__ со следующими параметрами ввода: указатель на объекте обработчика звука (IXAudio2 объекты, созданные в методе [Audio::CreateDeviceIndependentResources](#audiocreatedeviceindependentresources-method) ), указатель для форматирования из формате WAV файл с помощью __MediaReader::GetOutputWaveFormatEx__и звуковые данные загружаются с помощью метода [MediaReader::LoadMedia](#mediareaderloadmedia-method) . Во время инициализации также создается исходный тембр для звукового эффекта.

### <a name="soundeffectinitialize-method"></a>Метод SoundEffect::Initialize

```cpp
void SoundEffect::Initialize(
    _In_ IXAudio2 *masteringEngine,
    _In_ WAVEFORMATEX *sourceFormat,
    _In_ Platform::Array<byte>^ soundData)
{
    m_soundData = soundData;

    if (masteringEngine == nullptr)
    {
        // Audio is not available so just return.
        m_audioAvailable = false;
        return;
    }

    // Create a source voice for this sound effect.
    DX::ThrowIfFailed(
        masteringEngine->CreateSourceVoice(
            &m_sourceVoice,
            sourceFormat
            )
        );
    m_audioAvailable = true;
}
```

## <a name="play-the-sound"></a>Воспроизведение звука

Триггеров для воспроизведения звуковых эффектов определяются в метод [Simple3DGame::UpdateDynamics](#simple3dgameupdatedynamics-method) , поскольку именно здесь обновляются перемещения объектов и определяется столкновений между объектами.

Так как взаимодействие между объектами существенно отличается в зависимости от игры, мы не будем обсудим dynamics здесь игровых объектов. Если вы хотите понять его реализации, перейдите к [Simple3DGame::UpdateDynamics](#simple3dgameupdatedynamics-method) метод.

В принципе когда происходит, он инициирует звуковой эффект для воспроизведения, вызвав метод [SoundEffect::PlaySound]((soundeffectplaysound-method). Этот метод останавливает все звуковые эффекты, которые очереди буфер в памяти с помощью нужный звуковые данные и воспроизводится в данный момент. Он использует исходный тембр громкость, отправке звуковых данных и воспроизведения.

### <a name="soundeffectplaysound-method"></a>Метод SoundEffect::PlaySound

* Использует объект исходный тембр **m\_sourceVoice** для воспроизведения буфера звуковых данных **m\_soundData**
* Создает [XAUDIO2\_BUFFER](https://msdn.microsoft.com/library/windows/desktop/ee419228), в котором оно предоставляет ссылку на буфер звуковых данных, а затем отправляет ее вызова [IXAudio2SourceVoice::SubmitSourceBuffer](https://msdn.microsoft.com/library/windows/desktop/ee418473). 
* После добавления в очередь звуковых данных **SoundEffect::PlaySound** начинает воспроизведение, направляя вызов [IXAudio2SourceVoice::Start](https://msdn.microsoft.com/library/windows/desktop/ee418471).

```cpp
void SoundEffect::PlaySound(_In_ float volume)
{
    XAUDIO2_BUFFER buffer = {0};
    XAUDIO2_VOICE_STATE state = {0};

    if (!m_audioAvailable)
    {
        // Audio is not available, so just return.
        return;
    }

    // Interrupt sound effect if currently playing.
    DX::ThrowIfFailed(
        m_sourceVoice->Stop()
        );
    DX::ThrowIfFailed(
        m_sourceVoice->FlushSourceBuffers()
        );

    // Queue in-memory buffer for playback and start the voice.
    buffer.AudioBytes = m_soundData->Length;
    buffer.pAudioData = m_soundData->Data;
    buffer.Flags = XAUDIO2_END_OF_STREAM;

    m_sourceVoice->SetVolume(volume);
    DX::ThrowIfFailed(
        m_sourceVoice->SubmitSourceBuffer(&buffer)
        );
    DX::ThrowIfFailed(
        m_sourceVoice->Start()
        );
}
```

### <a name="simple3dgameupdatedynamics-method"></a>Метод Simple3DGame::UpdateDynamics

Метод __Simple3DGame::UpdateDynamics__ берет на себя взаимодействие и столкновений между игровым объектам. Когда объекты сталкиваться (или пересекаются), активирует связанные звуковой эффект для воспроизведения.

```cpp
void Simple3DGame::UpdateDynamics()
{
    // ...
    // Check for collisions between ammo.
#pragma region inter-ammo collision detection
    if (m_ammoCount > 1)
    {
       // ...
       // Check collision between instances One and Two.
       // ...
       if (distanceSquared < (GameConstants::AmmoSize * GameConstants::AmmoSize))
       {
           // The two ammo are intersecting.
           // ...
           // Start playing the sounds for the impact between the two balls.
              m_ammo[one]->PlaySound(impact, m_player->Position());
              m_ammo[two]->PlaySound(impact, m_player->Position());

       }
    }
#pragma endregion

#pragma region Ammo-Object intersections
    // Check for intersections between the ammo and the other objects in the scene.
    // ...
    // Ball is in contact with Object.
    // ...

    // Make sure that the ball is actually headed towards the object. At grazing angles there
    // could appear to be an impact when the ball is actually already hit and moving away.
    if (impact > 0.0f)
    {
        // ...
        // Play the sound associated with the Ammo hitting something.
           m_ammo[one]->PlaySound(impact, m_player->Position());

        if (m_objects[i]->Target() && !m_objects[i]->Hit())
        {
            // The object is a target and isn't currently hit, so mark it as hit and
            // play the sound associated with the impact.
             m_objects[i]->Hit(true);
             m_objects[i]->HitTime(timeTotal);
             m_totalHits++;

             m_objects[i]->PlaySound(impact, m_player->Position());
        }
        // ...
    }
#pragma endregion

#pragma region Apply Gravity and world intersection
    // Apply gravity and check for collision against enclosing volume.
    // ...
    if (position.z < limit)
    {
        // The ammo instance hit the a wall in the min Z direction.
        // Align the ammo instance to the wall, invert the Z component of the velocity and
        // play the impact sound.
           position.z = limit;
           m_ammo[i]->PlaySound(-velocity.z, m_player->Position());
           velocity.z = -velocity.z * GameConstants::Physics::GroundRestitution;
    }
    // ...
#pragma endregion
}
```
## <a name="next-steps"></a>Дальнейшие действия

Мы рассмотрели UWP framework, графики, элементы управления, пользовательский интерфейс и звук игры для Windows 10. В следующей части этого учебника, [образец расширения игры](tutorial-resources.md), объясняется другие параметры, которые можно использовать при разработке игр.

## <a name="audio-concepts"></a>Понятий аудио

Разработка игр для Windows 10 используйте XAudio2 версия 2.9. Эта версия поставляется вместе с Windows 10. Дополнительные сведения перейдите к [Версиям XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415802.aspx).

__AudioX2__ — это низкоуровневый API, который обеспечивает обработку сигналов и Микширование звука. Дополнительные сведения см. в разделе [Ключевые концепции XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415764.aspx).

### <a name="xaudio2-voices"></a>Речь в XAudio2

Существует три типа голосовых объектов XAudio2: исходные, комбинированные и обработанные. Голоса являются объекты XAudio2, использующие для обработки, для управления и для воспроизведения звуковых данных. 
* Исходные голосовые объекты берутся из звуковых данных, предоставленных клиентом. 
* Данные исходных и комбинированных голосов используются для получения одного или более комбинированных или обработанных голосов. 
* Комбинированные и обработанные голоса смешивают звуковые сигналы из всех голосов, чьи данные они получают, и работают с итоговым звуком. 
* Обработанные голоса получать данные из исходного тембра и тембра субмикширования и отправляет эти данные звуковому оборудованию.

Дополнительные сведения перейдите к [речь в XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415824.aspx).

### <a name="audio-graph"></a>Граф звука

Граф звука — это коллекция [речь в XAudio2](#xaudio2-voice-objects). Звук начинается с одной стороны звукового графа в соответствующих исходных тембрах, при необходимости проходит через один или более тембров субмикширования и заканчивается в управляющей речи. Граф звука будет содержать исходный тембр для каждого воспроизведение звука, ноль или более тембров субмикширования и один тембр мастеринга. Простой граф звука и по меньшей мере, необходимые для принятия шум в XAudio2, — это один исходный тембр выводить непосредственно к управляющему аудиоустройству. Дополнительные сведения перейдите к [Звуковые графы](https://msdn.microsoft.com/library/windows/desktop/ee415739.aspx).

### <a name="additional-reading"></a>Дополнительные данные

* [Руководство: инициализация XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415779.aspx)
* [Руководство: загрузка файлов звуковых данных в XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415781(v=vs.85).aspx)
* [Руководство: воспроизведение звука при помощи XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415787.aspx)

## <a name="key-audio-h-files"></a>H ключа аудио-файлы

### <a name="audioh"></a>Audio.h

```cpp
// Audio:
// This class uses XAudio2 to provide sound output.  It creates two
// engines - one for music and the other for sound effects - each as
// a separate mastering voice.
// The SuspendAudio and ResumeAudio methods can be used to stop
// and start all audio playback.
public:
    Audio();

    void Initialize();
    void CreateDeviceIndependentResources();
    IXAudio2* MusicEngine();
    IXAudio2* SoundEffectEngine();
    void SuspendAudio();
    void ResumeAudio();

protected:
    // ...
};
```
### <a name="mediareaderh"></a>MediaReader.h

```cpp
// MediaReader:
// This is a helper class for the SoundEffect class.  It reads small audio files
// synchronously from the package installed folder and returns sound data as a
// byte array.

ref class MediaReader
{
internal:
    MediaReader();

    Platform::Array<byte>^          LoadMedia(_In_ Platform::String^ filename);
    WAVEFORMATEX*                   GetOutputWaveFormatEx();

protected private:
    Windows::Storage::StorageFolder^ m_installedLocation;
    Platform::String^               m_installedLocationPath;
    WAVEFORMATEX                    m_waveFormat;
};
```
### <a name="soundeffecth"></a>SoundEffect.h

```cpp
// SoundEffect:
// This class plays a sound using XAudio2.  It uses a mastering voice provided
// from the Audio class.  The sound data can be read from disk using the MediaReader
// class.

ref class SoundEffect
{
internal:
    SoundEffect();

    void Initialize(
        _In_ IXAudio2*              masteringEngine,
        _In_ WAVEFORMATEX*          sourceFormat,
        _In_ Platform::Array<byte>^ soundData
        );

    void PlaySound(_In_ float volume);

protected private:
    //...

};
```


