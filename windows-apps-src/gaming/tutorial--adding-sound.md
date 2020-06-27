---
title: Добавление звука
description: Разработка простого звукового обработчика с помощью интерфейсов API XAudio2 для воспроизведения музыкальных и звуковых эффектов игры.
ms.assetid: aa05efe2-2baa-8b9f-7418-23f5b6cd2266
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, звуки
ms.localizationpriority: medium
ms.openlocfilehash: 0e624c750bfce0633bc91d440fd883341b831836
ms.sourcegitcommit: 20969781aca50738792631f4b68326f9171a3980
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409653"
---
# <a name="add-sound"></a>Добавление звука

> [!NOTE]
> Эта статья является частью серии руководств по [созданию простой универсальная платформа Windows (UWP) с помощью DirectX](tutorial--create-your-first-uwp-directx-game.md) . В разделе этой ссылки задается контекст для ряда.

В этом разделе мы создадим простой звуковый обработчик с помощью API-интерфейсов [XAudio2](/windows/desktop/xaudio2/xaudio2-introduction) . Если вы не знакомы с __XAudio2__, мы включили краткое введение в [Основные понятия аудио](#audio-concepts).

>[!Note]
>Если вы еще не скачали последний код игры для этого примера, перейдите к разделу [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](/windows/uwp/get-started/get-uwp-app-samples).

## <a name="objective"></a>Назначение

Добавьте звуки в пример игры с помощью [XAudio2](/windows/desktop/xaudio2/xaudio2-introduction).

## <a name="define-the-audio-engine"></a>Определение обработчика звука

В примере игры объекты аудио и поведения определяются в трех файлах:

* __[Audio. h](#audioh)/.КПП__: определяет __звуковой__ объект, который содержит ресурсы __XAudio2__ для воспроизведения звука. Он также определяет метод приостановки и возобновления воспроизведения звука в случаях приостановки или деактивации игры.
* __ [Медиареадер. h](#mediareaderh)/.КПП__: определяет методы для чтения файлов Audio. wav из локального хранилища.
* __ [Саундеффект. h](#soundeffecth)/.КПП__: определяет объект для воспроизведения звука в игре.

## <a name="overview"></a>Обзор

Существует три основные части настройки для воспроизведения звука в игре.

1. [Создание и инициализация звуковых ресурсов](#create-and-initialize-the-audio-resources)
2. [Загрузить звуковой файл](#load-audio-file)
3. [Связать звук с объектом](#associate-sound-to-object)

Все они определены в методе [Simple3DGame:: Initialize](#simple3dgameinitialize-method) . Давайте сначала рассмотрим этот метод, а затем подробно рассмотрим каждый из разделов.

После настройки мы научитесь запускать звуковые эффекты для воспроизведения. Дополнительные сведения см. на странице [Воспроизведение звука](#play-the-sound).

### <a name="simple3dgameinitialize-method"></a>Метод Simple3DGame:: Initialize

В __Simple3DGame:: Initialize__, где __m \_ Controller__ и __модуль \_ подготовки__ отчетов (m) также инициализируются, мы настроили звуковую подсистему и готовы к воспроизведению звуков.

 * Создайте __ \_ аудиоконтроллер m__, который является экземпляром класса [Audio](#audioh) .
 * Создайте звуковые ресурсы, необходимые с помощью метода [Audio:: креатедевицеиндепендентресаурцес](#audiocreatedeviceindependentresources-method) . Здесь два объекта __XAudio2__ &mdash; объект музыкального модуля и объект звукового обработчика, а для каждого из них был создан основной голоса. Объект музыкального модуля можно использовать для воспроизведения фоновой музыки в игре. Обработчик звука можно использовать для воспроизведения звуковых эффектов в игре. Дополнительные сведения см. [в статье Создание и инициализация звуковых ресурсов](#create-and-initialize-the-audio-resources).
 * Создайте __медиареадер__, который является экземпляром класса [медиареадер](#mediareaderh) . [Медиареадер](#mediareaderh), который является вспомогательным классом для класса [саундеффект](#soundeffecth) , считывает небольшие звуковые файлы синхронно из расположения файлов и возвращает звуковые данные в виде массива байтов.
 * Используйте [медиареадер:: лоадмедиа](#mediareaderloadmedia-method) для загрузки звуковых файлов из своего расположения и создания переменной __таржеситсаунд__ для хранения загруженных звуковых данных WAV. Дополнительные сведения см. в разделе [Загрузка звукового файла](#load-audio-file). 

Звуковые эффекты связаны с объектом Game. Поэтому при возникновении конфликта с этим игровым объектом он запускает воспроизведение звукового эффекта. В этом примере игры у нас есть звуковые эффекты для АММО (то, что мы используем для прокрутки целевых объектов) и для целевого объекта. 
    
* В классе __GameObject__ есть свойство __хитсаунд__ , которое используется для связывания звукового эффекта с объектом.
* Создайте новый экземпляр класса [саундеффект](#soundeffecth) и инициализируйте его. Во время инициализации создается исходный голоса для звукового эффекта. 
* Этот класс воспроизводит звук, используя основной голос, предоставленный из класса [Audio](#audioh) . Звуковые данные считываются из расположения файла с помощью класса [медиареадер](#mediareaderh) . Дополнительные сведения см. в разделе [связывание звука с объектом](#associate-sound-to-object).

>[!Note]
>Фактический триггер для воспроизведения звука определяется перемещением и конфликтом этих объектов игры. Таким образом, вызов для фактического воспроизведения этих звуков определен в методе [Simple3DGame:: упдатединамикс](#simple3dgameupdatedynamics-method) . Дополнительные сведения см. на странице [Воспроизведение звука](#play-the-sound).

```cppwinrt
void Simple3DGame::Initialize(
    _In_ std::shared_ptr<MoveLookController> const& controller,
    _In_ std::shared_ptr<GameRenderer> const& renderer
    )
{
    // The following member is defined in the header file:
    // Audio m_audioController;

    ...

    // Create the audio resources needed.
    // Two XAudio2 objects are created - one for music engine,
    // the other for sound engine. A mastering voice is also
    // created for each of the objects.
    m_audioController.CreateDeviceIndependentResources();

    m_ammo.resize(GameConstants::MaxAmmo);

    ...

    // Create a media reader which is used to read audio files from its file location.
    MediaReader mediaReader;
    auto targetHitSoundX = mediaReader.LoadMedia(L"Assets\\hit.wav");

    // Instantiate the targets for use in the game.
    // Each target has a different initial position, size, and orientation.
    // But share a common set of material properties.
    for (int a = 1; a < GameConstants::MaxTargets; a++)
    {
        ...
        // Create a new sound effect object and associate it
        // with the game object's (target) HitSound property.
        target->HitSound(std::make_shared<SoundEffect>());

        // Initialize the sound effect object with
        // the sound effect engine, format of the audio wave, and audio data
        // During initialization, source voice of this sound effect is also created.
        target->HitSound()->Initialize(
            m_audioController.SoundEffectEngine(),
            mediaReader.GetOutputWaveFormatEx(),
            targetHitSoundX
            );
        ...
    }

    // Instantiate a set of spheres to be used as ammunition for the game
    // and set the material properties of the spheres.
    auto ammoHitSound = mediaReader.LoadMedia(L"Assets\\bounce.wav");

    for (int a = 0; a < GameConstants::MaxAmmo; a++)
    {
        m_ammo[a] = std::make_shared<Sphere>();
        m_ammo[a]->Radius(GameConstants::AmmoRadius);
        m_ammo[a]->HitSound(std::make_shared<SoundEffect>());
        m_ammo[a]->HitSound()->Initialize(
            m_audioController.SoundEffectEngine(),
            mediaReader.GetOutputWaveFormatEx(),
            ammoHitSound
            );
        m_ammo[a]->Active(false);
        m_renderObjects.push_back(m_ammo[a]);
    }
    ...
}
```

## <a name="create-and-initialize-the-audio-resources"></a>Создание и инициализация звуковых ресурсов

* Используйте [XAudio2Create](/windows/desktop/api/xaudio2/nf-xaudio2-xaudio2create), API XAudio2, чтобы создать два новых объекта XAudio2, определяющие обработчики музыки и звуковых эффектов. Этот метод возвращает указатель на интерфейс [IXAudio2](/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2) объекта, который управляет всеми состояниями обработчика аудио, потоком обработки звука, графом голоса и т. д.
* После создания экземпляров модулей используйте [IXAudio2:: креатемастерингвоице](/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2-createmasteringvoice) , чтобы создать основной голоса для каждого из объектов звукового механизма.

Дополнительные сведения см. в подразделе [как инициализировать XAudio2](/windows/desktop/xaudio2/how-to--initialize-xaudio2).

### <a name="audiocreatedeviceindependentresources-method"></a>Метод Audio:: Креатедевицеиндепендентресаурцес

```cppwinrt
void Audio::CreateDeviceIndependentResources()
{
    UINT32 flags = 0;

    winrt::check_hresult(
        XAudio2Create(m_musicEngine.put(), flags)
        );

    HRESULT hr = m_musicEngine->CreateMasteringVoice(&m_musicMasteringVoice);
    if (FAILED(hr))
    {
        // Unable to create an audio device
        m_audioAvailable = false;
        return;
    }

    winrt::check_hresult(
        XAudio2Create(m_soundEffectEngine.put(), flags)
        );

    winrt::check_hresult(
        m_soundEffectEngine->CreateMasteringVoice(&m_soundEffectMasteringVoice)
        );

    m_audioAvailable = true;
}
```

## <a name="load-audio-file"></a>Загрузить звуковой файл

В примере игры код для чтения файлов аудио формата определен в [медиареадер. h](#mediareaderh)/cpp__.  Чтобы прочитать закодированный WAV аудио файл, вызовите [медиареадер:: лоадмедиа](#mediareaderloadmedia-method), передав имя файла. wav в качестве входного параметра.

### <a name="mediareaderloadmedia-method"></a>Метод Медиареадер:: Лоадмедиа

Данный метод использует API [Media Foundation](/windows/desktop/medfound/microsoft-media-foundation-sdk) для считывания звукового файла в формате WAV в виде буфера импульсно-кодовой модуляции (ИКМ).

#### <a name="set-up-the-source-reader"></a>Настройка модуля чтения исходного кода

1. Используйте [мфкреатесаурцереадерфромурл](/windows/desktop/api/mfreadwrite/nf-mfreadwrite-mfcreatesourcereaderfromurl) для создания модуля чтения источника мультимедиа ([имфсаурцереадер](/windows/desktop/api/mfreadwrite/nn-mfreadwrite-imfsourcereader)).
2. Используйте [мфкреатемедиатипе](/windows/desktop/api/mfapi/nf-mfapi-mfcreatemediatype) для создания объекта типа мультимедиа ([Имфмедиатипе](/windows/desktop/api/mfobjects/nn-mfobjects-imfmediatype)) (_mediaType_). Он представляет описание формата мультимедиа. 
3. Укажите, что декодированные выходные данные типа данных _mediaType_— это звук PCM, который может использоваться __XAudio2__ .
4. Задает декодированный выходной тип носителя для модуля чтения исходного кода путем вызова [имфсаурцереадер:: сеткуррентмедиатипе](/windows/desktop/api/mfreadwrite/nf-mfreadwrite-imfsourcereader-setcurrentmediatype).

Дополнительные сведения о том, почему мы используем модуль чтения исходного кода, см. в [модуле чтения исходного кода](/windows/desktop/medfound/source-reader).

#### <a name="describe-the-data-format-of-the-audio-stream"></a>Описание формата данных звукового потока

1. Используйте [имфсаурцереадер:: жеткуррентмедиатипе](/windows/desktop/api/mfreadwrite/nf-mfreadwrite-imfsourcereader-getcurrentmediatype) , чтобы получить текущий тип мультимедиа для потока.
2. Используйте [имфмедиатипе:: мфкреатевавеформатексфроммфмедиатипе](/windows/desktop/api/mfapi/nf-mfapi-mfcreatewaveformatexfrommfmediatype) для преобразования текущего типа звукового носителя в буфер [вавеформатекс](/windows/desktop/api/mmreg/ns-mmreg-twaveformatex) , используя результаты предыдущей операции в качестве входных данных. Эта структура задает формат данных звукового аудиопотока, используемого после загрузки аудио. 

Формат __вавеформатекс__ можно использовать для описания буфера PCM. По сравнению со структурой [вавеформатекстенсибле](/windows-hardware/drivers/ddi/content/ksmedia/ns-ksmedia-waveformatextensible) ее можно использовать только для описания подмножества форматов звуковой волны. Дополнительные сведения о различиях между __вавеформатекс__ и __вавеформатекстенсибле__см. в разделе [расширяемые дескрипторы Wave-формата](/windows-hardware/drivers/audio/extensible-wave-format-descriptors).

#### <a name="read-the-audio-stream"></a>Чтение аудиопотока

1.  Получите длительность (в секундах) аудиопотока, вызвав [имфсаурцереадер:: жетпресентатионаттрибуте](/windows/desktop/api/mfreadwrite/nf-mfreadwrite-imfsourcereader-getpresentationattribute) , а затем преобразуя длительность в байты.
2.  Чтение звукового файла в виде потока путем вызова [имфсаурцереадер:: реадсампле](/windows/desktop/api/mfreadwrite/nf-mfreadwrite-imfsourcereader-readsample). __Реадсампле__ считывает следующий пример из источника мультимедиа.
3.  Используйте [имфсампле:: конверттоконтигуаусбуффер](/windows/desktop/api/mfobjects/nf-mfobjects-imfsample-converttocontiguousbuffer) для копирования содержимого буфера образца аудио (_Sample_) в массив (_медиабуффер_).

```cppwinrt
std::vector<byte> MediaReader::LoadMedia(_In_ winrt::hstring const& filename)
{
    winrt::check_hresult(
        MFStartup(MF_VERSION)
        );

    // Creates a media source reader.
    winrt::com_ptr<IMFSourceReader> reader;
    winrt::check_hresult(
        MFCreateSourceReaderFromURL(
        (m_installedLocationPath + filename).c_str(),
            nullptr,
            reader.put()
            )
        );

    // Set the decoded output format as PCM.
    // XAudio2 on Windows can process PCM and ADPCM-encoded buffers.
    // When using MediaFoundation, this sample always decodes into PCM.
    winrt::com_ptr<IMFMediaType> mediaType;
    winrt::check_hresult(
        MFCreateMediaType(mediaType.put())
        );

    // Define the major category of the media as audio. For more info about major media types,
    // go to: https://msdn.microsoft.com/library/windows/desktop/aa367377.aspx
    winrt::check_hresult(
        mediaType->SetGUID(MF_MT_MAJOR_TYPE, MFMediaType_Audio)
        );

    // Define the sub-type of the media as uncompressed PCM audio. For more info about audio sub-types,
    // go to: https://msdn.microsoft.com/library/windows/desktop/aa372553.aspx
    winrt::check_hresult(
        mediaType->SetGUID(MF_MT_SUBTYPE, MFAudioFormat_PCM)
        );

    // Sets the media type for a stream. This media type defines that format that the Source Reader 
    // produces as output. It can differ from the native format provided by the media source.
    // For more info, go to https://msdn.microsoft.com/library/windows/desktop/dd374667.aspx
    winrt::check_hresult(
        reader->SetCurrentMediaType(static_cast<uint32_t>(MF_SOURCE_READER_FIRST_AUDIO_STREAM), 0, mediaType.get())
        );

    // Get the current media type for the stream.
    // For more info, go to:
    // https://msdn.microsoft.com/library/windows/desktop/dd374660.aspx
    winrt::com_ptr<IMFMediaType> outputMediaType;
    winrt::check_hresult(
        reader->GetCurrentMediaType(static_cast<uint32_t>(MF_SOURCE_READER_FIRST_AUDIO_STREAM), outputMediaType.put())
        );

    // Converts the current media type into the WaveFormatEx buffer structure.
    UINT32 size = 0;
    WAVEFORMATEX* waveFormat;
    winrt::check_hresult(
        MFCreateWaveFormatExFromMFMediaType(outputMediaType.get(), &waveFormat, &size)
        );

    // Copies the waveFormat's block of memory to the starting address of the m_waveFormat variable in MediaReader.
    // Then free the waveFormat memory block.
    // For more info, go to https://msdn.microsoft.com/library/windows/desktop/aa366535.aspx and
    // https://msdn.microsoft.com/library/windows/desktop/ms680722.aspx
    CopyMemory(&m_waveFormat, waveFormat, sizeof(m_waveFormat));
    CoTaskMemFree(waveFormat);

    PROPVARIANT propVariant;
    winrt::check_hresult(
        reader->GetPresentationAttribute(static_cast<uint32_t>(MF_SOURCE_READER_MEDIASOURCE), MF_PD_DURATION, &propVariant)
        );

    // 'duration' is in 100ns units; convert to seconds, and round up
    // to the nearest whole byte.
    LONGLONG duration = propVariant.uhVal.QuadPart;
    unsigned int maxStreamLengthInBytes =
        static_cast<unsigned int>(
            ((duration * static_cast<ULONGLONG>(m_waveFormat.nAvgBytesPerSec)) + 10000000) /
            10000000
            );

    std::vector<byte> fileData(maxStreamLengthInBytes);

    winrt::com_ptr<IMFSample> sample;
    winrt::com_ptr<IMFMediaBuffer> mediaBuffer;
    DWORD flags = 0;

    int positionInData = 0;
    bool done = false;
    while (!done)
    {
        // Read audio data.
        ...
    }

    return fileData;
}
```

## <a name="associate-sound-to-object"></a>Связать звук с объектом

Связывание звуков с объектом происходит при инициализации игры в методе [Simple3DGame:: Initialize](#simple3dgameinitialize-method) .

Повтор.
* В классе __GameObject__ есть свойство __хитсаунд__ , которое используется для связывания звукового эффекта с объектом.
* Создайте новый экземпляр объекта класса [саундеффект](#soundeffecth) и свяжите его с объектом Game. Этот класс воспроизводит звук с помощью API-интерфейсов __XAudio2__ .  В нем используется основной голос, предоставляемый классом [Audio](#audioh) . Звуковые данные можно считывать из расположения файла с помощью класса [медиареадер](#mediareaderh) .

[Саундеффект:: Initialize](#soundeffectinitialize-method) используется для инициировать экземпляра __саундеффект__ со следующими входными параметрами: указатель на объект звукового обработчика (IXAudio2 объекты, созданные в методе [Audio:: креатедевицеиндепендентресаурцес](#audiocreatedeviceindependentresources-method) ), указатель на формат WAV-файла с помощью __медиареадер:: жетаутпутвавеформатекс__, а также звуковые данные, загруженные с помощью метода [MediaReader:: LoadMedia](#mediareaderloadmedia-method) . Во время инициализации также создается исходный голоса для звукового эффекта.

### <a name="soundeffectinitialize-method"></a>Метод Саундеффект:: Initialize

```cppwinrt
void SoundEffect::Initialize(
    _In_ IXAudio2* masteringEngine,
    _In_ WAVEFORMATEX* sourceFormat,
    _In_ std::vector<byte> const& soundData)
{
    m_soundData = soundData;

    if (masteringEngine == nullptr)
    {
        // Audio is not available so just return.
        m_audioAvailable = false;
        return;
    }

    // Create a source voice for this sound effect.
    winrt::check_hresult(
        masteringEngine->CreateSourceVoice(
            &m_sourceVoice,
            sourceFormat
            )
        );
    m_audioAvailable = true;
}
```

## <a name="play-the-sound"></a>Воспроизведение звука

Триггеры для воспроизведения звуковых эффектов определяются в методе [Simple3DGame:: упдатединамикс](#simple3dgameupdatedynamics-method) , так как в этом случае изменяется перемещение объектов и определяется конфликт между объектами.

Так как взаимодействие между объектами значительно отличается в зависимости от игры, мы не будем обсуждать Dynamics объектов Game. Если вы хотите разобраться в своей реализации, перейдите к методу [Simple3DGame:: упдатединамикс](#simple3dgameupdatedynamics-method) .

В принципе, при возникновении конфликта он запускает воспроизведение звукового эффекта, вызывая **саундеффект::P лайсаунд**. Этот метод останавливает звуковые эффекты, воспроизводимые в данный момент, и помещает в очередь буфер в памяти с нужными звуковыми данными. Он использует исходный голоса для настройки тома, отправки звуковых данных и запуска воспроизведения.

### <a name="soundeffectplaysound-method"></a>Саундеффект: метод:P Лайсаунд

* Использует исходный объект Voice **m \_ саурцевоице** для запуска воспроизведения буфера звуковых данных **m \_ саунддата**
* Создает [ \_ буфер XAUDIO2](/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_buffer), с которым он предоставляет ссылку на буфер звуковых данных, а затем отправляет его с помощью вызова [IXAudio2SourceVoice:: субмитсаурцебуффер](/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2sourcevoice-submitsourcebuffer). 
* После добавления в очередь звуковых данных **SoundEffect::PlaySound** начинает воспроизведение, направляя вызов [IXAudio2SourceVoice::Start](/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2sourcevoice-start).

```cppwinrt
void SoundEffect::PlaySound(_In_ float volume)
{
    XAUDIO2_BUFFER buffer = { 0 };

    if (!m_audioAvailable)
    {
        // Audio is not available so just return.
        return;
    }

    // Interrupt sound effect if it is currently playing.
    winrt::check_hresult(
        m_sourceVoice->Stop()
        );
    winrt::check_hresult(
        m_sourceVoice->FlushSourceBuffers()
        );

    // Queue the memory buffer for playback and start the voice.
    buffer.AudioBytes = (UINT32)m_soundData.size();
    buffer.pAudioData = m_soundData.data();
    buffer.Flags = XAUDIO2_END_OF_STREAM;

    winrt::check_hresult(
        m_sourceVoice->SetVolume(volume)
        );
    winrt::check_hresult(
        m_sourceVoice->SubmitSourceBuffer(&buffer)
        );
    winrt::check_hresult(
        m_sourceVoice->Start()
        );
}
```

### <a name="simple3dgameupdatedynamics-method"></a>Метод Simple3DGame:: Упдатединамикс

Метод __Simple3DGame:: упдатединамикс__ следит за взаимодействием и конфликтом между объектами Game. При конфликте объектов (или пересечения) он запускает воспроизведение связанного звукового эффекта.

```cppwinrt
void Simple3DGame::UpdateDynamics()
{
    ...
    // Check for collisions between ammo.
#pragma region inter-ammo collision detection
if (m_ammoCount > 1)
{
    ...
    // Check collision between instances One and Two.
    ...
    if (distanceSquared < (GameConstants::AmmoSize * GameConstants::AmmoSize))
    {
        // The two ammo are intersecting.
        ...
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
        ...
        // Play the sound associated with the Ammo hitting something.
        m_objects[i]->PlaySound(impact, m_player->Position());

        if (m_objects[i]->Target() && !m_objects[i]->Hit())
        {
            // The object is a target and isn't currently hit, so mark
            // it as hit and play the sound associated with the impact.
            m_objects[i]->Hit(true);
            m_objects[i]->HitTime(timeTotal);
            m_totalHits++;

            m_objects[i]->PlaySound(impact, m_player->Position());
        }
        ...
    }
#pragma endregion

#pragma region Apply Gravity and world intersection
            // Apply gravity and check for collision against enclosing volume.
            ...
                if (position.z < limit)
                {
                    // The ammo instance hit the a wall in the min Z direction.
                    // Align the ammo instance to the wall, invert the Z component of the velocity and
                    // play the impact sound.
                    position.z = limit;
                    m_ammo[i]->PlaySound(-velocity.z, m_player->Position());
                    velocity.z = -velocity.z * GameConstants::Physics::GroundRestitution;
                }
                ...
#pragma endregion
}
```

## <a name="next-steps"></a>Следующие шаги

Мы рассмотрели платформу UWP, графику, элементы управления, Пользовательский интерфейс и звук в игре Windows 10. Следующая часть этого руководства, [расширяющая пример игры](tutorial-resources.md), объясняет другие варианты, которые можно использовать при разработке игры.

## <a name="audio-concepts"></a>Основные понятия аудио

Для разработки игр Windows 10 используйте XAudio2 версии 2,9. Эта версия поставляется с Windows 10. Дополнительные сведения см. по адресу [XAudio2 Versions](/windows/desktop/xaudio2/xaudio2-versions).

__AudioX2__ — это интерфейс API низкого уровня, который обеспечивает обработку сигнала и смешивание основы. Дополнительные сведения см. в разделе [Основные понятия XAudio2](/windows/desktop/xaudio2/xaudio2-key-concepts).

### <a name="xaudio2-voices"></a>XAudio2 голоса

Существует три типа голосовых объектов XAudio2: источник, субмикс и голосовое руководство. Голоса — это объекты, которые XAudio2 использовать для обработки, управления и воспроизведения звуковых данных. 
* Исходные голосовые объекты берутся из звуковых данных, предоставленных клиентом. 
* Данные исходных и комбинированных голосов используются для получения одного или более комбинированных или обработанных голосов. 
* Комбинированные и обработанные голоса смешивают звуковые сигналы из всех голосов, чьи данные они получают, и работают с итоговым звуком. 
* При сводном голосовом процессе получаются данные из исходных голосов и субмикс голосов, а эти данные отправляются на звуковое оборудование.

Дополнительные сведения см. на странице [XAudio2 голоса](/windows/desktop/xaudio2/xaudio2-voices).

### <a name="audio-graph"></a>Звуковая диаграмма

Audio Graph — это коллекция [голосов XAudio2](/windows/desktop/xaudio2/xaudio2-voices). Звук начинается с одной стороны аудио графа в исходных голосов, при необходимости проходит через один или несколько голосов субмикс и завершается в основной голосовой системе. В звуковой диаграмме будет содержаться исходный голос для каждого воспроизводимого звука, ноль или более субмикс голосов и один основной голос. Простейший звуковой график, а также минимум, необходимый для внесения шума в XAudio2, — это единый исходный голос, который наводится непосредственно на основной голос. Дополнительные сведения см. на странице " [звуковые графики](/windows/desktop/xaudio2/audio-graphs)".

### <a name="additional-reading"></a>Дополнительные материалы для чтения

* [Руководство: инициализация XAudio2](/windows/desktop/xaudio2/how-to--initialize-xaudio2)
* [Руководство: загрузка файлов звуковых данных в XAudio2](/windows/desktop/xaudio2/how-to--load-audio-data-files-in-xaudio2)
* [Руководство: воспроизведение звука при помощи XAudio2](/windows/desktop/xaudio2/how-to--play-a-sound-with-xaudio2)

## <a name="key-audio-h-files"></a>Файлы Key Audio. h

### <a name="audioh"></a>Audio.h

```cppwinrt
// Audio:
// This class uses XAudio2 to provide sound output. It creates two
// engines - one for music and the other for sound effects - each as
// a separate mastering voice.
// The SuspendAudio and ResumeAudio methods can be used to stop
// and start all audio playback.

class Audio
{
public:
    Audio();

    void Initialize();
    void CreateDeviceIndependentResources();
    IXAudio2* MusicEngine();
    IXAudio2* SoundEffectEngine();
    void SuspendAudio();
    void ResumeAudio();

private:
    ...
};
```

### <a name="mediareaderh"></a>Медиареадер. h

```cppwinrt
// MediaReader:
// This is a helper class for the SoundEffect class. It reads small audio files
// synchronously from the package installed folder and returns sound data as a
// vector of bytes.

class MediaReader
{
public:
    MediaReader();

    std::vector<byte> LoadMedia(_In_ winrt::hstring const& filename);
    WAVEFORMATEX* GetOutputWaveFormatEx();

private:
    winrt::Windows::Storage::StorageFolder  m_installedLocation{ nullptr };
    winrt::hstring                          m_installedLocationPath;
    WAVEFORMATEX                            m_waveFormat;
};
```

### <a name="soundeffecth"></a>SoundEffect.h

```cppwinrt
// SoundEffect:
// This class plays a sound using XAudio2. It uses a mastering voice provided
// from the Audio class. The sound data can be read from disk using the MediaReader
// class.

class SoundEffect
{
public:
    SoundEffect();

    void Initialize(
        _In_ IXAudio2* masteringEngine,
        _In_ WAVEFORMATEX* sourceFormat,
        _In_ std::vector<byte> const& soundData
        );

    void PlaySound(_In_ float volume);

private:
    ...
};
```
