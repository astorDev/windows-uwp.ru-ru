---
title: Добавление звука
description: Разработка простого обработчика звука, используя интерфейсы API XAudio2 для воспроизведения музыкальные и звуковые эффекты.
ms.assetid: aa05efe2-2baa-8b9f-7418-23f5b6cd2266
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, звуки
ms.localizationpriority: medium
ms.openlocfilehash: 8d5a976ef65bee5efc3329afc98bf198d094b037
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57589939"
---
# <a name="add-sound"></a>Добавление звука

В этом разделе мы создадим простой обработчик звука с помощью [XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415813) API-интерфейсы. Если вы не знакомы с __XAudio2__, мы включили Краткое введение в разделе [понятия аудио](#audio-concepts).

>[!Note]
>Если вы еще не скачали последний код для игры из этого примера, перейдите в раздел [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

## <a name="objective"></a>Цель

Добавление звуков в образец игры при помощи [XAudio2](/windows/desktop/xaudio2/xaudio2-introduction).

## <a name="define-the-audio-engine"></a>Определение обработчика звука

В образце игры объекты аудио и расширения их функциональности определены в трех файлах:

* __[Audio.h](#audioh)/.cpp__: Определяет __аудио__ объект, содержащий __XAudio2__ ресурсы для воспроизведения звука. Он также определяет метод приостановки и возобновления воспроизведения звука в случаях приостановки или деактивации игры.
* __[MediaReader.h](#mediareaderh)/.cpp__: Определяет методы для чтения звуковой WAV-файлов из локального хранилища.
* __[SoundEffect.h](#soundeffecth)/.cpp__: Определяет объект, для воспроизведения звука в игре.

## <a name="overview"></a>Обзор

Существует три основные части в настройке для воспроизведения звука в игре.

1. [Создание и инициализация звуковые ресурсы](#create-and-initialize-the-audio-resources)
2. [Загрузить звуковой файл](#load-audio-file)
3. [Связать звука для объекта](#associate-sound-to-object)

Все они определены в [Simple3DGame::Initialize](#simple3dgameinitialize-method) метод. Так что давайте сначала рассмотрим этот метод и затем углубиться в подробности в каждом из разделов.

После настройки, мы узнаем, как активировать звуковые эффекты для воспроизведения. Дополнительные сведения см. в статье [воспроизведения звука](#play-the-sound).

### <a name="simple3dgameinitialize-method"></a>Метод Simple3DGame::Initialize

В __Simple3DGame::Initialize__, где __m\_контроллера__ и __m\_модуль подготовки отчетов__ являются также инициализируется, мы настроить ядро аудио и его Все готово для воспроизведения звуков.

 * Создание __m\_audioController__, который является экземпляром [аудио](#audioh) класса.
 * Создание звуковых необходимых ресурсов с помощью [Audio::CreateDeviceIndependentResources](#audiocreatedeviceindependentresources-method) метод. Здесь, двух __XAudio2__ объектов &mdash; объект ядра музыки и объект звуковой ядра и управление образами голос для каждого из них были созданы. Объект ядра музыки можно использовать для воспроизведения музыки фон для игры. Подсистема звукового можно использовать для воспроизведения звуковых эффектов в игре. Дополнительные сведения см. в разделе [создать и инициализировать звуковые ресурсы](#create-and-initialize-the-audio-resources).
 * Создание __mediaReader__, который является экземпляром [MediaReader](#mediareaderh) класса. [MediaReader](#mediareaderh), — это вспомогательный класс для [SoundEffect](#soundeffecth) класса, операции чтения небольших аудио файлов синхронно из расположение файла и возвращает звуковые данные в виде массива байтов.
 * Используйте [MediaReader::LoadMedia](#mediareaderloadmedia-method) загрузка звуковые файлы из расположения и создание __targetHitSound__ переменной для хранения загруженных .wav аудиоданных. Дополнительные сведения см. в разделе [загрузить звуковой файл](#load-audio-file). 

Звуковые эффекты связаны с объектом игр. Поэтому при возникновении конфликта с этим объектом игр, запускает звуковой для воспроизведения. В этот пример у нас есть звуковые эффекты для ammo, (что мы используем прострелить целевых объектов с) и для целевого объекта. 
    
* В __GameObject__ класса, имеется __HitSound__ свойство, которое позволяет связать звуковой эффект к объекту.
* Создайте новый экземпляр класса [SoundEffect](#soundeffecth) и инициализируйте его. Во время инициализации создается голос источника звукового эффекта. 
* Этот класс воспроизводит звук с помощью управления образами голоса, предоставленные в [аудио](#audioh) класса. Звуковые данные считываются из файла с помощью расположение [MediaReader](#mediareaderh) класса. Дополнительные сведения см. в разделе [связать звуковой объект](#associate-sound-to-object).

>[!Note]
>Фактический триггер для воспроизведения звука определяется перемещения и расчет столкновений для этих игровым объектам. Таким образом, вызов на самом деле эти звуки определяются в [Simple3DGame::UpdateDynamics](#simple3dgameupdatedynamics-method) метод. Дополнительные сведения см. в статье [воспроизведения звука](#play-the-sound).

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

## <a name="create-and-initialize-the-audio-resources"></a>Создание и инициализация звуковые ресурсы

* Используйте [XAudio2Create](https://msdn.microsoft.com/library/windows/desktop/ee419212), XAudio2 API, для создания двух объектов XAudio2, определяющие модули Музыка и звук в силу. Этот метод возвращает указатель на объект [IXAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415908) говорится в интерфейс, управляющий все аудио, аудио, обработка потоков, graph голосовой и многое другое.
* После модули были созданы, с помощью [IXAudio2::CreateMasteringVoice](https://msdn.microsoft.com/library/windows/desktop/hh405048) Создание голос Отработка навыков в службах для каждого объекта звуковой ядра.

Дополнительные сведения см. в статье [как: Инициализировать XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415779.aspx).

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

## <a name="load-audio-file"></a>Загрузить звуковой файл

В примере игры, код для чтения файлов звуковой формат определяется в [MediaReader.h](#mediareaderh)/cpp__.  Чтобы прочитать кодировке .wav звуковой файл, вызовите [MediaReader::LoadMedia](#mediareaderloadmedia-method), передавая имя файла .wav как входной параметр.

### <a name="mediareaderloadmedia-method"></a>Метод MediaReader::LoadMedia

Данный метод использует API [Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms694197) для считывания звукового файла в формате WAV в виде буфера импульсно-кодовой модуляции (ИКМ).

#### <a name="set-up-the-source-reader"></a>Настройте исходный модуль чтения

1. Используйте [MFCreateSourceReaderFromURL](https://msdn.microsoft.com/library/windows/desktop/dd388110) создания носителя для чтения источника ([IMFSourceReader](https://msdn.microsoft.com/library/windows/desktop/dd374655)).
2. Используйте [MFCreateMediaType](https://msdn.microsoft.com/library/windows/desktop/ms693861) создать тип носителя ([IMFMediaType](https://msdn.microsoft.com/library/windows/desktop/ms704850)) объекта (_mediaType_). Он представляет описание формат мультимедиа. 
3. Указать, что _mediaType_декодировать выходных данных является PCM аудио, который является звуковая строго, __XAudio2__ можно использовать.
4. Задает тип носителя декодированный выходной для чтения источника путем вызова [IMFSourceReader::SetCurrentMediaType](https://msdn.microsoft.com/library/windows/desktop/dd374667.aspx).

Узнать больше о почему мы используем исходный модуль чтения, см. в статье [исходный модуль чтения](https://msdn.microsoft.com/library/windows/desktop/dd940436.aspx).

#### <a name="describe-the-data-format-of-the-audio-stream"></a>Описание формата данных аудиопотока

1. Используйте [IMFSourceReader::GetCurrentMediaType](https://msdn.microsoft.com/library/windows/desktop/dd374660) для получения текущего типа мультимедиа для потока.
2. Используйте [IMFMediaType::MFCreateWaveFormatExFromMFMediaType](https://msdn.microsoft.com/library/windows/desktop/ms702177) для преобразования текущей звуковой тип мультимедиа, [WAVEFORMATEX](https://msdn.microsoft.com/library/windows/hardware/ff538799) буфера, используя результаты предыдущих операции в качестве входных данных. Эта структура задает формат данных аудиопотока wave, который используется после загрузки звука. 

__WAVEFORMATEX__ формат может использоваться для описания буфере PCM. По сравнению с [WAVEFORMATEXTENSIBLE](https://msdn.microsoft.com/library/windows/hardware/ff538802) структуры, он может использоваться только для описания подмножество форматов звуковой волны. Дополнительные сведения о различиях между __WAVEFORMATEX__ и __WAVEFORMATEXTENSIBLE__, см. в разделе [расширяемый дескрипторы волновой формат](https://docs.microsoft.com/windows-hardware/drivers/audio/extensible-wave-format-descriptors).

#### <a name="read-the-audio-stream"></a>Чтение аудиопотока

1.  Получить длительность, в секундах, аудиопотока путем вызова [IMFSourceReader::GetPresentationAttribute](https://msdn.microsoft.com/library/windows/desktop/dd374662) и затем преобразует длительность в байтах.
2.  Чтение звуковой файл в виде потока путем вызова [IMFSourceReader::ReadSample](https://msdn.microsoft.com/library/windows/desktop/dd374665). __ReadSample__ считывает следующий образец из источник мультимедиа.
3.  Используйте [IMFSample::ConvertToContiguousBuffer](https://msdn.microsoft.com/library/windows/desktop/ms698917.aspx) скопировать содержимое буфера аудио образца (_пример_) в массив (_mediaBuffer_).

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
## <a name="associate-sound-to-object"></a>Связать звука для объекта

Связывание звуковые фалы для объекта имеет место при инициализации игры, в [Simple3DGame::Initialize](#simple3dgameinitialize-method) метод.

Краткий обзор:
* В __GameObject__ класса, имеется __HitSound__ свойство, которое позволяет связать звуковой эффект к объекту.
* Создайте новый экземпляр класса [SoundEffect](#soundeffecth) класса объекта и свяжите ее с игровому объекту. Этот класс воспроизведение звука с помощью __XAudio2__ API-интерфейсы.  Она использует голос Отработка навыков в службах, предоставляемых [аудио](#audioh) класса. Данные могут считываться из файла с помощью расположение [MediaReader](#mediareaderh) класса.

[SoundEffect::Initialize](#soundeffectinitialize-method) используется для инициализации __SoundEffect__ экземпляр с следующие входные параметры: указатель на объект обработчика звука (IXAudio2 объекты, созданные в [аудио:: CreateDeviceIndependentResources](#audiocreatedeviceindependentresources-method) метод), указатель на формат файла .wav с помощью __MediaReader::GetOutputWaveFormatEx__, и загрузить данные с помощью [MediaReader::LoadMedia ](#mediareaderloadmedia-method) метод. Во время инициализации также создается голоса источника звукового эффекта.

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

## <a name="play-the-sound"></a>Воспроизведение звуков

В определены триггеры для воспроизведения звуковых эффектов [Simple3DGame::UpdateDynamics](#simple3dgameupdatedynamics-method) метод, так как это где обновляются перемещения объектов и конфликтов между объектами определить.

Так как взаимодействие между объектами существенно отличается в зависимости от игры, мы не собираемся обсуждать dynamics объектами игры. Если вы хотите, чтобы разобраться в его реализации, перейдите на страницу [Simple3DGame::UpdateDynamics](#simple3dgameupdatedynamics-method) метод.

В принципе, при возникновении конфликта, он активирует звуковой для воспроизведения, вызвав **SoundEffect::PlaySound**. Этот метод останавливает звуковые эффекты, которые в настоящее время воспроизведения и помещает в очередь буфера в памяти с требуемой аудиоданных. Она использует источник голосовой громкость, отправьте звуковые данные и начать воспроизведение.

### <a name="soundeffectplaysound-method"></a>Метод SoundEffect::PlaySound

* Использует исходный объект голосовой **m\_sourceVoice** для начала воспроизведения звуковых данных буфера **m\_soundData**
* Создает [XAUDIO2\_БУФЕРА](https://msdn.microsoft.com/library/windows/desktop/ee419228), к которой она предоставляет ссылку на буфер звуковых данных, а затем отправляет его с помощью вызова [IXAudio2SourceVoice::SubmitSourceBuffer](https://msdn.microsoft.com/library/windows/desktop/ee418473). 
* С помощью звуковые данные в очередь, **SoundEffect::PlaySound** начинается воспроизведение путем вызова [IXAudio2SourceVoice::Start](https://msdn.microsoft.com/library/windows/desktop/ee418471).

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

__Simple3DGame::UpdateDynamics__ метод берет на себя взаимодействие и конфликт между игровым объектам. Конфликт (или объектов пересекаются), активируется связанный звуковой для воспроизведения.

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

Мы рассмотрели UWP framework, графики, элементы управления, пользовательский интерфейс и аудио игры Windows 10. В следующей части этого учебника [расширение пример игры](tutorial-resources.md), описываются другие параметры, которые могут использоваться при разработке игры.

## <a name="audio-concepts"></a>Основные понятия аудио

Для разработки игр для Windows 10 используйте XAudio2 версии 2.9. Эта версия поставляется вместе с Windows 10. Дополнительные сведения см. в статье [XAudio2 версии](https://msdn.microsoft.com/library/windows/desktop/ee415802.aspx).

__AudioX2__ — это низкоуровневый API, который предоставляет обработки сигнала и смешивания foundation. Дополнительные сведения см. в разделе [XAudio2 основные понятия](https://msdn.microsoft.com/library/windows/desktop/ee415764.aspx).

### <a name="xaudio2-voices"></a>Голоса XAudio2

Существует три типа объектов голосовой XAudio2: источник, submix и освоению голоса. Голоса, что использовать объекты XAudio2 для обработки, для управления и для воспроизведения звуковых данных. 
* Исходные голосовые объекты берутся из звуковых данных, предоставленных клиентом. 
* Данные исходных и комбинированных голосов используются для получения одного или более комбинированных или обработанных голосов. 
* Комбинированные и обработанные голоса смешивают звуковые сигналы из всех голосов, чьи данные они получают, и работают с итоговым звуком. 
* Управление образами голоса получать данные из источника голоса и submix голоса и отправляет эти данные для устройств.

Дополнительные сведения см. в статье [XAudio2 голоса](/windows/desktop/xaudio2/xaudio2-voices).

### <a name="audio-graph"></a>Аудио graph

Аудио graph — это коллекция [XAudio2 голоса](/windows/desktop/xaudio2/xaudio2-voices). Аудио начинается с одной стороны графа аудио в голоса источника, при необходимости проходит через один или несколько submix голоса и заканчивается управление образами голос. Граф аудио будет содержать голос источника для каждого воспроизведение звука, ноль или более submix голоса и один Отработка навыков в службах голоса. Простейший аудио графа и получить необходимый минимум для слышите в XAudio2, — это единый источник голос вывод непосредственно для управления образами голос. Дополнительные сведения см. в статье [графы аудио](https://msdn.microsoft.com/library/windows/desktop/ee415739.aspx).

### <a name="additional-reading"></a>Дополнительные материалы

* [Инструкции: Инициализировать XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415779.aspx)
* [Инструкции: Загрузка файлов звуковые данные в XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415781(v=vs.85).aspx)
* [Инструкции: Воспроизведение звука с XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415787.aspx)

## <a name="key-audio-h-files"></a>Ключа аудио h-файлов

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


