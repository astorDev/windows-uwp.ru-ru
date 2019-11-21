---
title: Добавление звука в пример Marble Maze
description: В данном документе описаны ключевые принципы, которые следует учитывать при работе со звуком, и показано, как эти принципы применяются в игре Marble Maze.
ms.assetid: 77c23d0a-af6d-17b5-d69e-51d9885b0d44
ms.date: 10/18/2017
ms.topic: article
keywords: windows 10, uwp, звук, игры, пример
ms.localizationpriority: medium
ms.openlocfilehash: 4cf803b61a280ff3ed803dc7972d7f7eb6993a64
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258559"
---
# <a name="adding-audio-to-the-marble-maze-sample"></a>Добавление звука в пример Marble Maze

В данном документе описаны ключевые принципы, которые следует учитывать при работе со звуком, и показано, как эти принципы применяются в игре Marble Maze. Marble Maze использует [Microsoft Media Foundation](https://docs.microsoft.com/windows/desktop/medfound/microsoft-media-foundation-sdk) для загрузки звуковых ресурсов из файлов и [XAudio2](https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-apis-portal) для микширования и воспроизведения звука, а также для добавления эффектов к звукозаписям.

Marble Maze воспроизводит музыку в фоновом режиме, а также использует звуки во время игрового процесса для указания на события в игре, например столкновение шарика со стеной. Важной частью реализации является то, что Marble Maze использует эффект реверберации (эхо) для имитации звука шарика при отскоке. Реализация эффекта реверберации позволяет эху доходить до ушей пользователя быстрее и более громко в небольших пространствах. Если пространство велико, эхо будет тише и дойдет до слушателя не так скоро.

> [!NOTE]
> Пример кода, соответствующего этому документу, см. в [примере игры DirectX Marble Maze](https://github.com/microsoft/Windows-appsample-marble-maze).

Некоторые из ключевых аспектов работы со звуком в игре, рассматриваемые в данном документе.

- Рассмотрите возможность использования Media Foundation для декодирования звуковых ресурсов и XAudio2 для воспроизведения звука. Однако если у вас уже есть готовый механизм загрузки звуковых ресурсов, который работает в приложении универсальной платформы Windows (UWP), то можно использовать его.

- Граф звука содержит один исходный тембр для каждого активного звука, ноль или более тембров субмикширования и один тембр мастеринга. Исходные тембры могут передаваться в тембры субмикширования и (или) тембр мастеринга. Тембры субмикширования передаются в другие тембры субмикширования и (или) тембр мастеринга.

- Если файлы фоновой музыки велики, следует подумать о потоковой передаче музыки в более мелкие буферы, чтобы расходовать меньше памяти.

- Если это имеет смысл, приостанавливайте воспроизведение звука, когда приложение утрачивает фокус, становится невидимым или приостанавливается. Возобновляйте воспроизведение, когда приложение вновь получает фокус, становится видимым или возобновляется.

- Установите категории звука для отражения роли каждого из звуков. Например, для звуковых эффектов обычно используется **аудиокатегори\_гамемедиа** для игр Background Audio и **аудиокатегори\_гамиффектс** .

- Реакция на смену устройств, в том числе наушников, должна состоять в освобождении и воссоздании всех звуковых ресурсов и интерфейсов.

- Подумайте, следует ли сжимать звуковые файлы, если требуется минимизация занимаемого пространства диска и расходов на потоковую передачу. В ином случае звук можно передавать в несжатом виде, чтобы он загружался быстрее.

## <a name="introducing-xaudio2-and-microsoft-media-foundation"></a>Знакомство с XAudio2 и Microsoft Media Foundation

XAudio2 — это низкоуровневая звуковая библиотека Windows, которая, в частности, поддерживает звук для игр. Она предоставляет обработку цифровых сигналов (DSP) и обработчик звукового графа для игр. XAudio2 расширяет возможности своих предшественников, DirectSound и XAudio, поддерживая такие направления развития вычислительной техники, как архитектуры с плавающей запятой SIMD и звук высокого разрешения. Она также поддерживает более комплексные требования к обработке звука, предъявляемые современными играми.

В документе [Ключевые концепции XAudio2](https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-key-concepts) объясняются ключевые концепции использования XAudio2. В двух словах эти концепции заключаются в следующем.

- Интерфейс [IXAudio2](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2) является ядром обработчика XAudio2. Marble Maze использует этот интерфейс для создания тембров и получения уведомлений, когда устройство вывода меняется или в нем происходит сбой.

- **Тембр** обрабатывает, корректирует и воспроизводит звуковые данные.

- **Исходный тембр** является набором звуковых каналов (моно, 5.1 и т. д.) и представляет собой единый поток звуковых данных. В XAudio2 исходный тембр также является местом, где начинается обработка звуковых данных. Обычно звуковые данные загружаются из внешнего источника, такого как файл или сеть, и отправляются исходному тембру. Marble Maze использует [Media Foundation](https://docs.microsoft.com/windows/desktop/medfound/microsoft-media-foundation-sdk) для загрузки звуковых данных из файлов. Media Foundation будет представлен ниже в данном документе.

- **Тембр субмикширования**  обрабатывает звуковые данные. Эта обработка может включать в себя изменение звукового потока или объединение нескольких потоков в один. Marble Maze использует субмикширования для создания эффекта реверберации.

- **Мастеринговый тембр** объединяет данные из исходного тембра и тембра субмикширования и отправляет все эти данные звуковому оборудованию.

- **Граф звука** содержит один исходный тембр для каждого активного звука, ноль или более тембров субмикширования и только один тембр мастеринга.

- **Обратный вызов** сообщает клиентскому коду, что в тембре или в объекте обработчика произошло какое-то событие. Применение обратных вызовов позволяет повторно использовать память, когда XAudio2 завершает работу с буфером, реагировать на изменения используемого звукового устройства (например, на подключение или отключение наушников) и прочее. В разделе [Обработка смены наушников и устройств](#handling-headphones-and-device-changes) далее в этом документе объясняется, как Marble Maze использует этот механизм для обработки смены устройств.

Marble Maze использует для обработки звука два обработчика (то есть два объекта [IXAudio2](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2)). Один из них обрабатывает фоновую музыку, а второй — звуки, связанные с игровым процессом.

Marble Maze также необходимо создать по одному тембру мастеринга для каждого обработчика. Вспомним, что обработчик мастеринга объединяет звуковые потоки в один поток и отправляет этот поток звуковому оборудованию. Поток фоновой музыки, исходный тембр, выдает данные в тембр мастеринга и в два тембра субмикширования. Тембры сумбикширования создают эффект реверберации.

Media Foundation — это библиотека мультимедиа, поддерживающая многочисленные форматы звука и видео. XAudio2 и Media Foundation дополняют друг друга. Marble Maze использует Media Foundation для загрузки звуковых ресурсов из файлов, а XAudio2 — для воспроизведения звука. Использовать Media Foundation для загрузки звуковых ресурсов не обязательно. Если у вас уже есть готовый механизм загрузки звуковых ресурсов, который работает в приложениях универсальной платформы Windows (UWP), можно использовать его. В разделе [Звук, видео и камера](../audio-video-camera/index.md) рассматривается несколько способов реализации звука в приложении UWP.

Дополнительные сведения об XAudio2 см. в [руководстве по программированию](https://docs.microsoft.com/windows/desktop/xaudio2/programming-guide). Дополнительные сведения о Media Foundation см. в разделе [Microsoft Media Foundation](https://docs.microsoft.com/windows/desktop/medfound/microsoft-media-foundation-sdk).

## <a name="initializing-audio-resources"></a>Инициализация звуковых ресурсов

Marble Maze использует файлы Windows Media Audio (WMA-файлы) для фоновой музыки и WAV-файлы для звуков, связанных с игровым процессом. Media Foundation поддерживает эти форматы. Хотя формат файлов WAV непосредственно поддерживается XAudio2, игре необходим анализ формата файла вручную для заполнения соответствующих структур данных XAudio2. Marble Maze использует Media Foundation для упрощения работы с WAV-файлами. Полный список форматов мультимедиа, поддерживаемых Media Foundation, см. в разделе [Поддерживаемые форматы мультимедиа в Media Foundation](https://docs.microsoft.com/windows/desktop/medfound/supported-media-formats-in-media-foundation). Marble Maze не использует различные звуковые форматы на этапе разработки и во время выполнения и не включает поддержку сжатия XAudio2 ADPCM. Дополнительные сведения о сжатии ADPCM в XAudio2 см. в разделе [Обзор ADPCM](https://docs.microsoft.com/windows/desktop/xaudio2/adpcm-overview).

Метод **Audio::CreateResources**, вызываемый из **MarbleMazeMain::LoadDeferredResources**, загружает звуковые потоки из файлов, инициализирует объекты обработчика XAudio2 и создает тембры: исходный, субмикширования и мастеринга.

### <a name="creating-the-xaudio2-engines"></a>Создание обработчиков XAudio2

Вспомним, что Marble Maze создает один объект [IXAudio2](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2) для представления каждого используемого обработчика звука. Для создания обработчика звука вызовите метод [XAudio2Create](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-xaudio2create). В следующем примере показано, как Marble Maze создает обработчик звука для фоновой музыки.

```cpp
// In Audio.h
class Audio
{
private:
    IXAudio2*                   m_musicEngine;
// ...
}

// In Audio.cpp
void Audio::CreateResources()
{
    try
    {
        // ...
        DX::ThrowIfFailed(
            XAudio2Create(&m_musicEngine)
            );
        // ...
    }
    // ...
}
```

Marble Maze также выполняет аналогичное действие для создания обработчика звуков игрового процесса.

Работа с интерфейсом [IXAudio2](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2) в приложении UWP имеет два отличия от классических приложений. Во-первых, нет необходимости вызывать метод [CoInitializeEx](https://docs.microsoft.com/windows/desktop/api/combaseapi/nf-combaseapi-coinitializeex) перед вызовом [XAudio2Create](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-xaudio2create). Во-вторых, интерфейс **IXAudio2** больше не поддерживает перечисление устройств. Дополнительные сведения о перечислении звуковых устройств см. в разделе [Перечисление устройств](https://docs.microsoft.com/previous-versions/windows/apps/hh464977(v=win.10)).

### <a name="creating-the-mastering-voices"></a>Создание тембров мастеринга

В следующем примере показано, как метод **Audio::CreateResources** создает тембр мастеринга для фоновой музыки с помощью метода [IXAudio2::CreateMasteringVoice](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2-createmasteringvoice). В этом примере **m\_мусикмастерингвоице** является объектом [IXAudio2MasteringVoice](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2masteringvoice) . Мы указываем два входных канала; это позволяет упростить логику для эффекта реверберации. 

В качестве входной частоты дискретизации мы указываем 48 000. Мы выбрали такую частоту дискретизации, так как она обеспечивает баланс между качеством звука и нагрузкой на ЦП. Большая частота дискретизации увеличила бы объем работы ЦП без значительного повышения качества. 

И наконец, мы указываем **AudioCategory_GameMedia** в качестве категории звукового потока, чтобы во время игры пользователи могли слушать музыку из другого приложения. При воспроизведении музыкального приложения Windows выключает все голоса, созданные с помощью параметра **аудиокатегори\_гамемедиа** . Пользователь по-прежнему слышит звуки игрового процесса, так как они создаются с помощью параметра **аудиокатегори\_гамиффектс** . Дополнительные сведения о категориях аудио см. в статье [audio\_STREAM\_Category](https://docs.microsoft.com/windows/desktop/api/audiosessiontypes/ne-audiosessiontypes-_audio_stream_category).

```cpp
// This sample plays the equivalent of background music, which we tag on the  
// mastering voice as AudioCategory_GameMedia. In ordinary usage, if we were  
// playing the music track with no effects, we could route it entirely through 
// Media Foundation. Here, we are using XAudio2 to apply a reverb effect to the 
// music, so we use Media Foundation to decode the data then we feed it through 
// the XAudio2 pipeline as a separate Mastering Voice, so that we can tag it 
// as Game Media. We default the mastering voice to 2 channels to simplify  
// the reverb logic.
DX::ThrowIfFailed(
    m_musicEngine->CreateMasteringVoice(
        &m_musicMasteringVoice,
        2,
        48000,
        0,
        nullptr,
        nullptr,
        AudioCategory_GameMedia
        )
);
```

Метод **Audio:: CreateResources** выполняет аналогичное действие для создания основного голоса для игрового процессаных звуков, за исключением того, что он указывает **аудиокатегори\_Гамиффектс** для параметра *стреамкатегори* , который является значением по умолчанию.

### <a name="creating-the-reverb-effect"></a>Создание эффекта реверберации

Для каждого тембра можно использовать XAudio2, чтобы создавать последовательности эффектов, обрабатывающих звук. Такая последовательность называется цепочкой эффектов. Цепочки эффектов используются, если нужно применить к тембру один или несколько эффектов. Цепочки эффектов могут быть разрушительны. Это значит, что каждый эффект в цепочке может перезаписывать звуковой буфер. Это свойство важно, поскольку XAudio2 не гарантирует, что буферы вывода инициализируются тишиной. Объекты эффектов представлены в XAudio2 межплатформенными объектами обработки звука (XAPO). Дополнительные сведения о XAPO см. в [Обзоре XAPO](https://docs.microsoft.com/windows/desktop/xaudio2/xapo-overview).

При создании цепочки эффектов выполните следующие действия.

1. Создайте объект эффекта.

2. Заполните [\_XAUDIO2\_структуру дескриптора](https://docs.microsoft.com/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_effect_descriptor) с данными о результате.

3. Заполните [\_XAUDIO2\_структуру цепочки](https://docs.microsoft.com/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_effect_chain) данными.

4. Примените цепочку эффектов к тембру.

5. Заполните структуру параметров эффекта и примените ее к эффекту.

6. Отключайте или включайте эффект по мере необходимости.

Класс **Audio** определяет метод **CreateReverb**, который создает цепочку эффектов, реализующую реверберацию. Этот метод вызывает метод [XAudio2CreateReverb](https://docs.microsoft.com/windows/desktop/api/xaudio2fx/nf-xaudio2fx-xaudio2createreverb) для создания объекта **ComPtr&lt;IUnknown&gt;**  — **soundEffectXAPO**, который играет роль тембра субмикширования для эффекта реверберации.

```cpp
Microsoft::WRL::ComPtr<IUnknown> soundEffectXAPO;

DX::ThrowIfFailed(
    XAudio2CreateReverb(&soundEffectXAPO)
    );
```

[XAUDIO2\_\_структура дескриптора](https://docs.microsoft.com/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_effect_descriptor) содержит сведения о ксапо для использования в цепочке эффектов, например целевое количество выходных каналов. Метод **Audio:: креатереверб** создает **XAUDIO2\_ный\_объект дескриптора** , **саундеффектдескриптор**, для которого задано состояние disabled, использует два выходных канала и ссылается на **саундеффектксапо** для перенастраиваемого действия. **soundEffectdescriptor** запускается в отключенном состоянии, поскольку игра должна задать параметры, прежде чем эффект начнет изменять игровые звуки. Marble Maze использует два канала вывода для упрощения логики эффекта реверберации.

```cpp
soundEffectdescriptor.InitialState = false;
soundEffectdescriptor.OutputChannels = 2;
soundEffectdescriptor.pEffect = soundEffectXAPO.Get();
```

Если в цепочке несколько эффектов, для каждого эффекта необходим объект. [XAUDIO2\_,\_структура цепочки](https://docs.microsoft.com/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_effect_chain) содержит массив [\_ных объектов дескриптора\_](https://docs.microsoft.com/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_effect_descriptor) , которые участвуют в этом результате. В следующем примере показано, как метод **Audio::CreateReverb** определяет один эффект для реализации реверберации.

```cpp
XAUDIO2_EFFECT_CHAIN soundEffectChain;

// ...

soundEffectChain.EffectCount = 1;
soundEffectChain.pEffectDescriptors = &soundEffectdescriptor;
```

Метод **Audio::CreateReverb** вызывает метод [IXAudio2::CreateSubmixVoice](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2-createsubmixvoice) для создания тембра субмикширования для этого эффекта. В нем указывается [XAUDIO2\_\_объект цепочки](https://docs.microsoft.com/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_effect_chain) **саундеффектчаин**, для параметра *пеффектчаин* , чтобы связать цепочку эффектов с голосовым объектом. Marble Maze также задает два канала вывода и частоту дискретизации 48 килогерц.

```cpp
DX::ThrowIfFailed(
    engine->CreateSubmixVoice(newSubmix, 2, 48000, 0, 0, nullptr, &soundEffectChain)
    );
```

> [!TIP]
> Если нужно присоединить существующую цепочку эффектов к существующему тембру субмикширования или заменить текущую цепочку эффектов, используйте метод [IXAudio2Voice::SetEffectChain](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2voice-seteffectchain).

Метод **Audio::CreateReverb** вызывает метод [IXAudio2Voice::SetEffectParameters](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2voice-seteffectparameters), чтобы задать дополнительные параметры, связанные с этим эффектом. Этот метод принимает структуру параметров, характерную для данного эффекта. Объект [параметров\_XAUDIO2FX\_](https://docs.microsoft.com/windows/desktop/api/xaudio2fx/ns-xaudio2fx-xaudio2fx_reverb_parameters) , **m_reverbParametersSmall**, который содержит параметры эффектов для переглагола, инициализируется в методе **Audio:: Initialize** , так как каждый перекомандный результат использует одни и те же параметры. В следующем примере показано, как метод **Audio::Initialize** инициализирует параметры для реверберации в ближней зоне.

```cpp
m_reverbParametersSmall.ReflectionsDelay = XAUDIO2FX_REVERB_DEFAULT_REFLECTIONS_DELAY;
m_reverbParametersSmall.ReverbDelay = XAUDIO2FX_REVERB_DEFAULT_REVERB_DELAY;
m_reverbParametersSmall.RearDelay = XAUDIO2FX_REVERB_DEFAULT_REAR_DELAY;
m_reverbParametersSmall.PositionLeft = XAUDIO2FX_REVERB_DEFAULT_POSITION;
m_reverbParametersSmall.PositionRight = XAUDIO2FX_REVERB_DEFAULT_POSITION;
m_reverbParametersSmall.PositionMatrixLeft = XAUDIO2FX_REVERB_DEFAULT_POSITION_MATRIX;
m_reverbParametersSmall.PositionMatrixRight = XAUDIO2FX_REVERB_DEFAULT_POSITION_MATRIX;
m_reverbParametersSmall.EarlyDiffusion = 4;
m_reverbParametersSmall.LateDiffusion = 15;
m_reverbParametersSmall.LowEQGain = XAUDIO2FX_REVERB_DEFAULT_LOW_EQ_GAIN;
m_reverbParametersSmall.LowEQCutoff = XAUDIO2FX_REVERB_DEFAULT_LOW_EQ_CUTOFF;
m_reverbParametersSmall.HighEQGain = XAUDIO2FX_REVERB_DEFAULT_HIGH_EQ_GAIN;
m_reverbParametersSmall.HighEQCutoff = XAUDIO2FX_REVERB_DEFAULT_HIGH_EQ_CUTOFF;
m_reverbParametersSmall.RoomFilterFreq = XAUDIO2FX_REVERB_DEFAULT_ROOM_FILTER_FREQ;
m_reverbParametersSmall.RoomFilterMain = XAUDIO2FX_REVERB_DEFAULT_ROOM_FILTER_MAIN;
m_reverbParametersSmall.RoomFilterHF = XAUDIO2FX_REVERB_DEFAULT_ROOM_FILTER_HF;
m_reverbParametersSmall.ReflectionsGain = XAUDIO2FX_REVERB_DEFAULT_REFLECTIONS_GAIN;
m_reverbParametersSmall.ReverbGain = XAUDIO2FX_REVERB_DEFAULT_REVERB_GAIN;
m_reverbParametersSmall.DecayTime = XAUDIO2FX_REVERB_DEFAULT_DECAY_TIME;
m_reverbParametersSmall.Density = XAUDIO2FX_REVERB_DEFAULT_DENSITY;
m_reverbParametersSmall.RoomSize = XAUDIO2FX_REVERB_DEFAULT_ROOM_SIZE;
m_reverbParametersSmall.WetDryMix = XAUDIO2FX_REVERB_DEFAULT_WET_DRY_MIX;
m_reverbParametersSmall.DisableLateField = TRUE;
```

В этом примере используются значения по умолчанию для большинства параметров реверберации, но для параметра **DisableLateField** значение TRUE, указывая, что это реверберация в ближней зоне, для **EarlyDiffusion** — значение 4, чтобы имитировать плоские поверхности поблизости, и для **LateDiffusion** — значение 15, чтобы имитировать рассеивающие удаленные поверхности. Плоские поверхности, расположенные поблизости, создают громкое эхо, достигающее слушателя быстрее. Рассеивающие удаленные поверхности создают тихое эхо, достигающее слушателя дольше. Со значениями реверберации можно экспериментировать, чтобы получить в игре нужный эффект, либо можно применить метод **ReverbConvertI3DL2ToNative**, чтобы использовать стандартные для отрасли параметры I3DL2 (рекомендации по обработке трехмерного звука в интерактивных приложениях, уровень 2.0).

В следующем примере показано, как метод **Audio::CreateReverb** задает параметры реверберации. **newSubmix** — это объект [IXAudio2SubmixVoice](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2submixvoice)**. **Параметры** — это [\_XAUDIO2FX переглаголы,\_параметры](https://docs.microsoft.com/windows/desktop/api/xaudio2fx/ns-xaudio2fx-xaudio2fx_reverb_parameters)* Object.

```cpp
DX::ThrowIfFailed(
    (*newSubmix)->SetEffectParameters(0, parameters, sizeof(m_reverbParametersSmall))
    );
```

Метод **Audio::CreateReverb** завершает работу, включая эффект с помощью [IXAudio2Voice::EnableEffect](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2voice-enableeffect), если установлен флаг **enableEffect**. Он также задает громкость с помощью [IXAudio2Voice::SetVolume](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2voice-setvolume) и матрицу вывода с помощью [IXAudio2Voice::SetOutputMatrix](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2voice-setoutputmatrix). Эта часть задает полную громкость (1.0), а затем определяет матрицу громкости как тишину для левого и правого входов и левого и правого динамиков вывода. Это необходимо, так как другой код затем обеспечивает перекрестное затухание двух эффектов реверберации (имитируя переход от близости к стене к пребыванию в большой комнате) или отключает обе реверберации, если это необходимо. Когда после этого путь реверберации включается, игра задает матрицу {1.0f, 0.0f, 0.0f, 1.0f}, чтобы направить левый выход реверберации на левый вход тембра мастеринга, а правый выход реверберации — на правый вход тембра мастеринга.

```cpp
if (enableEffect)
{
    DX::ThrowIfFailed(
        (*newSubmix)->EnableEffect(0)
        );    
}

DX::ThrowIfFailed(
    (*newSubmix)->SetVolume (1.0f)
    );

float outputMatrix[4] = {0, 0, 0, 0};
DX::ThrowIfFailed(
    (*newSubmix)->SetOutputMatrix(masteringVoice, 2, 2, outputMatrix)
    );
```

Marble Maze вызывает метод **Audio::CreateReverb** четыре раза: два раза для фоновой музыки и два раза для звуков игрового процесса. В следующем примере показано, как Marble Maze вызывает метод **CreateReverb** для фоновой музыки.

```cpp
CreateReverb(
    m_musicEngine, 
    m_musicMasteringVoice, 
    &m_reverbParametersSmall, 
    &m_musicReverbVoiceSmallRoom, 
    true
    );
CreateReverb(
    m_musicEngine, 
    m_musicMasteringVoice, 
    &m_reverbParametersLarge, 
    &m_musicReverbVoiceLargeRoom, 
    true
    );
```

Список возможных источников эффектов для использования с XAudio2 см. на странице [Звуковые эффекты XAudio2](https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-audio-effects).

### <a name="loading-audio-data-from-file"></a>Загрузка звуковых данных из файла

Marble Maze определяет класс **MediaStreamer**, который использует Media Foundation для загрузки звуковых ресурсов из файлов. Marble Maze использует по одному объекту **MediaStreamer** для загрузки каждого из звуковых файлов.

Marble Maze вызывает метод **MediaStreamer::Initialize** для инициализации каждого из звуковых потоков. Ниже показано, как метод **Audio::CreateResources** вызывает метод **MediaStreamer::Initialize**, чтобы инициализировать звуковой поток для фоновой музыки.

```cpp
// Media Foundation is a convenient way to get both file I/O and format decode for 
// audio assets. You can replace the streamer in this sample with your own file I/O 
// and decode routines.
m_musicStreamer.Initialize(L"Media\\Audio\\background.wma");
```

Метод **MediaStreamer::Initialize** начинает с вызова метода [MFStartup](https://docs.microsoft.com/windows/desktop/api/mfapi/nf-mfapi-mfstartup) для инициализации Media Foundation. **MF_VERSION** — это макрос, определенный в **mfapi.h**, который указывается в качестве используемой версии Media Foundation.

```cpp
DX::ThrowIfFailed(
    MFStartup(MF_VERSION)
    );
```

Метод **MediaStreamer::Initialize** затем вызывает метод [MFCreateSourceReaderFromURL](https://docs.microsoft.com/windows/desktop/api/mfreadwrite/nf-mfreadwrite-mfcreatesourcereaderfromurl), чтобы создать объект [IMFSourceReader](https://docs.microsoft.com/windows/desktop/api/mfreadwrite/nn-mfreadwrite-imfsourcereader). Объект **IMFSourceReader** — **m_reader** — считывает данные мультимедиа из файла, заданного параметром **url**.

```cpp
DX::ThrowIfFailed(
    MFCreateSourceReaderFromURL(url, nullptr, &m_reader)
    );
```

Метод **MediaStreamer::Initialize** затем создает объект [IMFMediaType](https://docs.microsoft.com/windows/desktop/api/mfobjects/nn-mfobjects-imfmediatype) с помощью функции [MFCreateMediaType](https://docs.microsoft.com/windows/desktop/api/mfapi/nf-mfapi-mfcreatemediatype), чтобы описать формат звукового потока. У звукового формата имеется два типа: основной тип и подтип. Основной тип определяет общий формат мультимедиа, то есть видео, звук, сценарий и так далее. Подтип определяет формат, например PCM, ADPCМ или WMA.

Метод **медиастреамер:: Initialize** использует метод [Имфаттрибутес:: сетгуид](https://docs.microsoft.com/windows/desktop/api/mfobjects/nf-mfobjects-imfattributes-setguid) , чтобы указать основной тип ([MF_MT_MAJOR_TYPE](https://docs.microsoft.com/windows/desktop/medfound/mf-mt-major-type-attribute)) как аудио (**мфмедиатипе\_Audio**) и дополнительный тип ([MF_MT_SUBTYPE](https://docs.microsoft.com/windows/desktop/medfound/mf-mt-subtype-attribute)) в виде несжатого звука PCM (**мфаудиоформат\_PCM**). **MF_MT_MAJOR_TYPE** и **MF_MT_SUBTYPE** — это [атрибуты Media Foundation](https://docs.microsoft.com/windows/desktop/medfound/media-foundation-attributes). **MFMediaType_Audio** и **MFAudioFormat_PCM** — это идентификаторы GUID типа и подтипа; подробнее см. в статье [Звуковые типы мультимедиа](https://docs.microsoft.com/windows/desktop/medfound/audio-media-types). Метод [IMFSourceReader::SetCurrentMediaType](https://docs.microsoft.com/windows/desktop/api/mfreadwrite/nf-mfreadwrite-imfsourcereader-setcurrentmediatype) связывает тип мультимедиа со средством чтения потока.

```cpp
// Set the decoded output format as PCM. 
// XAudio2 on Windows can process PCM and ADPCM-encoded buffers. 
// When this sample uses Media Foundation, it always decodes into PCM.

DX::ThrowIfFailed(
    MFCreateMediaType(&mediaType)
    );

DX::ThrowIfFailed(
    mediaType->SetGUID(MF_MT_MAJOR_TYPE, MFMediaType_Audio)
    );

DX::ThrowIfFailed(
    mediaType->SetGUID(MF_MT_SUBTYPE, MFAudioFormat_PCM)
    );

DX::ThrowIfFailed(
    m_reader->SetCurrentMediaType(MF_SOURCE_READER_FIRST_AUDIO_STREAM, 0, mediaType.Get())
    );
```

Метод **MediaStreamer::Initialize** затем получает полный формат вывода мультимедиа из Media Foundation с помощью [IMFSourceReader::GetCurrentMediaType](https://docs.microsoft.com/windows/desktop/api/mfreadwrite/nf-mfreadwrite-imfsourcereader-getcurrentmediatype) и вызывает метод [MFCreateWaveFormatExFromMFMediaType](https://docs.microsoft.com/windows/desktop/api/mfapi/nf-mfapi-mfcreatewaveformatexfrommfmediatype), чтобы преобразовать звуковой тип мультимедиа Media Foundation в структуру [WAVEFORMATEX](https://docs.microsoft.com/windows/desktop/api/mmreg/ns-mmreg-twaveformatex). Структура **WAVEFORMATEX** определяет формат данных, содержащих форму звуковой волны. Marble Maze использует эту структуру для создания исходных тембров и применения фильтра низких частот к звуку катящегося шарика.

```cpp
// Get the complete WAVEFORMAT from the Media Type.
DX::ThrowIfFailed(
    m_reader->GetCurrentMediaType(MF_SOURCE_READER_FIRST_AUDIO_STREAM, &outputMediaType)
    );

uint32 formatSize = 0;
WAVEFORMATEX* waveFormat;
DX::ThrowIfFailed(
    MFCreateWaveFormatExFromMFMediaType(outputMediaType.Get(), &waveFormat, &formatSize)
    );
CopyMemory(&m_waveFormat, waveFormat, sizeof(m_waveFormat));
CoTaskMemFree(waveFormat);
```

> [!IMPORTANT]
> Метод [MFCreateWaveFormatExFromMFMediaType](https://docs.microsoft.com/windows/desktop/api/mfapi/nf-mfapi-mfcreatewaveformatexfrommfmediatype) использует **CoTaskMemAlloc** для выделения памяти для объекта [WAVEFORMATEX](https://docs.microsoft.com/windows/desktop/api/mmreg/ns-mmreg-twaveformatex). Поэтому нужно обязательно вызвать функцию **CoTaskMemFree** по завершении использования объекта.

 

Метод **медиастреамер:: Initialize** завершается вычислением длины потока, **m\_максстреамленгсинбитес**, в байтах. Для этого он вызывает метод [IMFSourceReader::GetPresentationAttribute](https://docs.microsoft.com/windows/desktop/api/mfreadwrite/nf-mfreadwrite-imfsourcereader-getpresentationattribute), чтобы получить продолжительность звукового потока в единицах, равных 100 наносекундам, преобразовывает ее в секунды и умножает на среднюю скорость передачи данных в байтах в секунду. Затем Marble Maze использует это значение, чтобы выделить память под буфер, содержащий все звуки игрового процесса.

```cpp
// Get the total length of the stream, in bytes.
PROPVARIANT var;
DX::ThrowIfFailed(
    m_reader->
        GetPresentationAttribute(MF_SOURCE_READER_MEDIASOURCE, MF_PD_DURATION, &var)
    );

// duration is in 100ns units; convert to seconds, and round up
// to the nearest whole byte.
ULONGLONG duration = var.uhVal.QuadPart;
m_maxStreamLengthInBytes =
    static_cast<unsigned int>(
        ((duration * static_cast<ULONGLONG>(m_waveFormat.nAvgBytesPerSec)) + 10000000)
        / 10000000
        );
```

### <a name="creating-the-source-voices"></a>Создание исходных тембров

Marble Mazе создает исходные тембры XAudio2 для воспроизведения всех звуков и мелодий игры в соответствующих исходных тембрах. Класс **Audio** определяет объект [IXAudio2SourceVoice](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2sourcevoice) для фоновой музыки и массив объектов **SoundEffectData**, содержащих звуки игрового процесса. Структура **SoundEffectData** содержит объект **IXAudio2SourceVoice** для эффекта, а также определяет другие связанные с эффектом данные, такие как буфер звука. В **Audio.h** определено перечисление **SoundEvent**. Marble Maze использует это перечисление для определения каждого из звуков игрового процесса. Класс **Audio** также использует это перечисление, чтобы индексировать массив объектов **SoundEffectData**.

```cpp
enum SoundEvent
{
    RollingEvent        = 0,
    FallingEvent        = 1,
    CollisionEvent      = 2,
    CheckpointEvent     = 3,
    MenuChangeEvent     = 4,
    MenuSelectedEvent   = 5,
    LastSoundEvent,
};
```

В следующей таблице показаны связи между каждым из этих значений и файлом, содержащим соответствующие звуковые данные, а также краткие описания каждого звука. Звуковые файлы находятся в папке **\\Media\\Audio** .

| Значение SoundEvent  | Имя файла      | Описание                                              |
|-------------------|----------------|----------------------------------------------------------|
| RollingEvent      | MarbleRoll.wav | Воспроизводится, когда шарик катится.                              |
| FallingEvent      | MarbleFall.wav | Воспроизводится, когда шарик выпадает из лабиринта.               |
| CollisionEvent    | MarbleHit.wav  | Воспроизводится, когда шарик сталкивается со стенками лабиринта.           |
| CheckpointEvent   | Checkpoint.wav | Воспроизводится, когда шарик проходит контрольную точку.         |
| MenuChangeEvent   | MenuChange.wav | Воспроизводится, когда пользователь изменяет текущий пункт меню. |
| MenuSelectedEvent | MenuSelect.wav | Воспроизводится, когда пользователь выбирает пункт меню.           |

 

В следующем примере показано, как метод **Audio::CreateResources** создает исходный тембр для фоновой музыки. Структура [дескриптора XAUDIO2\_SEND\_](https://docs.microsoft.com/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_send_descriptor) определяет целевой нацеливание от другого голоса и указывает, следует ли использовать фильтр. Marble Maze вызывает метод **Audio::SetSoundEffectFilter**, чтобы использовать фильтры для изменения звука катящегося шарика. [XAUDIO2\_VOICE\_отправляет](https://docs.microsoft.com/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_voice_sends) структуру, определяет набор голосов для получения данных из одного выходного голоса. Marble Maze отправляет данные из исходного тембра на тембр мастеринга (для "сухой", или оставшейся неизменной, части воспроизводимого звука) и на два тембра субмикширования, которые реализуют "влажную", или реверберирующую, часть воспроизводимого звука.

Метод [IXAudio2::CreateSourceVoice](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2-createsourcevoice) создает и настраивает исходный тембр. Он принимает структуру [WAVEFORMATEX](https://docs.microsoft.com/windows/desktop/api/mmreg/ns-mmreg-twaveformatex), которая определяет формат буферов звука, отправляемых тембру. Как упоминалось ранее, Marble Maze использует формат PCM.

```cpp
XAUDIO2_SEND_DESCRIPTOR descriptors[3];
descriptors[0].pOutputVoice = m_musicMasteringVoice;
descriptors[0].Flags = 0;
descriptors[1].pOutputVoice = m_musicReverbVoiceSmallRoom;
descriptors[1].Flags = 0;
descriptors[2].pOutputVoice = m_musicReverbVoiceLargeRoom;
descriptors[2].Flags = 0;
XAUDIO2_VOICE_SENDS sends = {0};
sends.SendCount = 3;
sends.pSends = descriptors;
WAVEFORMATEX& waveFormat = m_musicStreamer.GetOutputWaveFormatEx();

DX::ThrowIfFailed(
    m_musicEngine->CreateSourceVoice(&m_musicSourceVoice, &waveFormat, 0, 1.0f, &m_voiceContext, &sends, nullptr)
    );

DX::ThrowIfFailed(
    m_musicMasteringVoice->SetVolume(0.4f)
    );
```

## <a name="playing-background-music"></a>Воспроизведение фоновой музыки


Исходный тембр создается в остановленном состоянии. Marble Maze запускает фоновую музыку в игровом цикле. Первый вызов метода **MarbleMazeMain::Update** вызывает **Audio::Start**, чтобы запустить фоновую музыку.

```cpp
if (!m_audio.m_isAudioStarted)
{
    m_audio.Start();
}
```

Метод **Audio::Start** вызывает метод [IXAudio2SourceVoice::Start](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2sourcevoice-start), чтобы запустить обработку исходного тембра для фоновой музыки.

```cpp
void Audio::Start()
{     
    if (m_engineExperiencedCriticalError)
    {
        return;
    }

    HRESULT hr = m_musicSourceVoice->Start(0);

    if SUCCEEDED(hr) {
        m_isAudioStarted = true;
    }
    else
    {
        m_engineExperiencedCriticalError = true;
    }
}
```

Исходный тембр передает звуковые данные на следующий этап графа звука. В случае Marble Maze следующий этап включает два тембра субмикширования, которые применяют к звуку два эффекта реверберации. Один из них применяет реверберацию с задержкой в ближней зоне. Второй применяет реверберацию с задержкой в дальней зоне.

Степень влияния каждого из них на окончательное микширование определяется размером и формой помещения. Реверберация в ближней зоне имеет большее влияние, если шарик находится рядом со стеной или в небольшом помещении, а реверберация в поздней, или дальней, зоне оказывает большее влияние, если шарик находится на открытом пространстве. Этот подход дает более реалистичный эффект эха при движении шарика через лабиринт. Дополнительные сведения о том, как Marble Maze реализует этот эффект, см. в описании методов **Audio::SetRoomSize** и **Physics::CalculateCurrentRoomSize** в исходном коде Marble Maze.

> [!NOTE]
> Если разница в размерах помещений в игре относительно невелика, можно использовать более простую модель реверберации. Например, можно использовать одну настройку реверберации для всех помещений или заранее определить настройку реверберации для каждого помещения.

Метод **Audio::CreateResources** использует Media Foundation для загрузки фоновой музыки. Но на этом этапе у исходного тембра нет звуковых данных для обработки. Кроме того, так как фоновая музыка повторяется циклично, исходный тембр необходимо регулярно обновлять данными, чтобы воспроизведение музыки продолжалось.

Для поддержки наполнения исходного тембра данными игровой цикл обновляет буферы звука каждый кадр. Метод **MarbleMazeMain::Render** вызывает метод **Audio::Render** для обработки звукового буфера фоновой музыки. Класс **Audio** определяет массив из трех буферов аудио, **m\_аудиобуфферс**. Каждый буфер содержит 64 КБ (65536 байт) данных. Цикл считывает данные из объекта Media Foundation и записывает их в исходный тембр до тех пор, пока в исходном тембре не будет три поставленных в очередь буфера.

> [!CAUTION]
> Marble Maze использует для музыкальных данных буфер размером 64 КБ, но вам может понадобиться буфер большего или меньшего размера. Этот объем зависит от требований создаваемой игры.

```cpp
// This sample processes audio buffers during the render cycle of the application.
// As long as the sample maintains a high-enough frame rate, this approach should
// not glitch audio. In game code, it is best for audio buffers to be processed
// on a separate thread that is not synced to the main render loop of the game.
void Audio::Render()
{
    if (m_engineExperiencedCriticalError)
    {
        m_engineExperiencedCriticalError = false;
        ReleaseResources();
        Initialize();
        CreateResources();
        Start();
        if (m_engineExperiencedCriticalError)
        {
            return;
        }
    }

    try
    {
        bool streamComplete;
        XAUDIO2_VOICE_STATE state;
        uint32 bufferLength;
        XAUDIO2_BUFFER buf = {0};

        // Use MediaStreamer to stream the buffers.
        m_musicSourceVoice->GetState(&state);
        while (state.BuffersQueued <= MAX_BUFFER_COUNT - 1)
        {
            streamComplete = m_musicStreamer.GetNextBuffer(
                m_audioBuffers[m_currentBuffer],
                STREAMING_BUFFER_SIZE,
                &bufferLength
                );

            if (bufferLength > 0)
            {
                buf.AudioBytes = bufferLength;
                buf.pAudioData = m_audioBuffers[m_currentBuffer];
                buf.Flags = (streamComplete) ? XAUDIO2_END_OF_STREAM : 0;
                buf.pContext = 0;
                DX::ThrowIfFailed(
                    m_musicSourceVoice->SubmitSourceBuffer(&buf)
                    );

                m_currentBuffer++;
                m_currentBuffer %= MAX_BUFFER_COUNT;
            }

            if (streamComplete)
            {
                // Loop the stream.
                m_musicStreamer.Restart();
                break;
            }

            m_musicSourceVoice->GetState(&state);
        }
    }
    catch (...)
    {
        m_engineExperiencedCriticalError = true;
    }
}
```

Цикл также обрабатывает ситуацию, когда объект Media Foundation достигает конца потока. В этом случае он вызывает метод [IMFSourceReader::SetCurrentPosition](https://docs.microsoft.com/windows/desktop/api/mfreadwrite/nf-mfreadwrite-imfsourcereader-setcurrentposition), чтобы сбросить позицию источника звука.

```cpp
void MediaStreamer::Restart()
{
    if (m_reader == nullptr)
    {
        return;
    }

    PROPVARIANT var = {0};
    var.vt = VT_I8;

    DX::ThrowIfFailed(
        m_reader->SetCurrentPosition(GUID_NULL, var)
        );
}
```

Чтобы реализовать циклическое воспроизведение для одного буфера (или для всего звука, полностью загруженного в память), можно задать для поля [XAUDIO2_BUFFER](https://docs.microsoft.com/windows/desktop/api/xaudio2/ns-xaudio2-xaudio2_buffer):: Лупкаунт значение **XAUDIO2\_Loop\_бесконечно** при инициализации звука. Marble Maze использует эту методику для воспроизведения звука катящегося шарика.

```cpp
if (sound == RollingEvent)
{
    m_soundEffects[sound].m_audioBuffer.LoopCount = XAUDIO2_LOOP_INFINITE;
}
```

Однако в случае фоновой музыки Marble Maze управляет буферами напрямую, чтобы лучше контролировать объем используемой памяти. Если файлы с музыкой большие, можно выполнять потоковую передачу данных музыки в меньшие по размеру буферы. Это поможет сбалансировать объем памяти с той частотой, с которой игра может обрабатывать и передавать поток звуковых данных.

> [!TIP]
> Если в игре используется низкая или переменная частота кадров, обработка звука в основном потоке может привести к неожиданным остановкам звука или щелчкам, так как обработчику звука будет недостаточно звуковых данных в буфере для работы. В играх, для которых эта проблема актуальна, можно вынести обработку в отдельный поток, не занятый отрисовкой. Этот подход особенно полезен на компьютерах с несколькими процессорами, потому что игра может использовать простаивающие процессоры.

## <a name="reacting-to-game-events"></a>Реагирование на события в игре

Класс **Audio** предоставляет методы, такие как **PlaySoundEffect**, **IsSoundEffectStarted**, **StopSoundEffect**, **SetSoundEffectVolume**, **SetSoundEffectPitch** и **SetSoundEffectFilter**, позволяющие игре контролировать воспроизведение и остановку звуков, а также свойства звуков (громкость и высоту). Например, если шарик выпадает из лабиринта, метод **MarbleMazeMain::Update** вызывает метод **Audio::PlaySoundEffect**, чтобы воспроизвести звук **FallingEvent**.

```cpp
m_audio.PlaySoundEffect(FallingEvent);
```

Метод **Audio::PlaySoundEffect** вызывает метод [IXAudio2SourceVoice::Start](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2sourcevoice-start), чтобы начать воспроизведение звука. Если метод **IXAudio2SourceVoice::Start** уже вызывался, он не запускается повторно. Затем метод **Audio::PlaySoundEffect** выполняет специальную логику для определенных звуков.

```cpp
void Audio::PlaySoundEffect(SoundEvent sound)
{
    XAUDIO2_BUFFER buf = {0};
    XAUDIO2_VOICE_STATE state = {0};

    if (m_engineExperiencedCriticalError)
    {
        // If there's an error, then we'll recreate the engine on the next
        // render pass.
        return;
    }

    SoundEffectData* soundEffect = &m_soundEffects[sound];
    HRESULT hr = soundEffect->m_soundEffectSourceVoice->Start();

    if FAILED(hr)
    {
        m_engineExperiencedCriticalError = true;
        return;
    }

    // For one-off voices, submit a new buffer if there's none queued up,
    // and allow up to two collisions to be queued up. 
    if (sound != RollingEvent)
    {
        XAUDIO2_VOICE_STATE state = {0};

        soundEffect->m_soundEffectSourceVoice->
            GetState(&state, XAUDIO2_VOICE_NOSAMPLESPLAYED);

        if (state.BuffersQueued == 0)
        {
            soundEffect->m_soundEffectSourceVoice->
                SubmitSourceBuffer(&soundEffect->m_audioBuffer);
        }
        else if (state.BuffersQueued < 2 && sound == CollisionEvent)
        {
            soundEffect->m_soundEffectSourceVoice->
                SubmitSourceBuffer(&soundEffect->m_audioBuffer);
        }

        // For the menu clicks, we want to stop the voice and replay the click
        // right away.
        // Note that stopping and then flushing could cause a glitch due to the
        // waveform not being at a zero-crossing, but due to the nature of the 
        // sound (fast and 'clicky'), we don't mind.
        if (state.BuffersQueued > 0 && sound == MenuChangeEvent)
        {
            soundEffect->m_soundEffectSourceVoice->Stop();
            soundEffect->m_soundEffectSourceVoice->FlushSourceBuffers();

            soundEffect->m_soundEffectSourceVoice->
                SubmitSourceBuffer(&soundEffect->m_audioBuffer);

            soundEffect->m_soundEffectSourceVoice->Start();
        }
    }

    m_soundEffects[sound].m_soundEffectStarted = true;
}
```

Для всех звуков, кроме звука катящегося шарика, метод **Audio::PlaySoundEffect** вызывает метод [IXAudio2SourceVoice::GetState](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2sourcevoice-getstate), чтобы определить количество буферов, воспроизводимых исходным тембром. Он вызывает метод [IXAudio2SourceVoice::SubmitSourceBuffer](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2sourcevoice-submitsourcebuffer), чтобы добавить звуковые данные для этого звука во входную очередь тембра, если нет активных буферов. Метод **Audio::PlaySoundEffect** также включает двукратное последовательное воспроизведение звука столкновения. Например, это происходит, если шарик сталкивается с углом лабиринта.

Как уже было сказано выше, класс Audio использует **XAUDIO2\_цикл\_НЕограниченный** флаг при инициализации звука для события отката. Звук начинает циклически воспроизводиться после первого вызова **Audio::PlaySoundEffect** для этого события. Для упрощения логики воспроизведения звука катящегося шарика Marble Maze не останавливает звук, а отключает его. Если шарик меняет скорость, Marble Maze изменяет высоту и громкость звука для большей реалистичности. Ниже показано, как метод **MarbleMazeMain::Update** обновляет высоту и громкость звучания шарика при изменении скорости и как он отключает звук при остановке шарика, задавая значение громкости, равное нулю.

```cpp
// Play the roll sound only if the marble is actually rolling.
if (ci.isRollingOnFloor && volume > 0)
{
    if (!m_audio.IsSoundEffectStarted(RollingEvent))
    {
        m_audio.PlaySoundEffect(RollingEvent);
    }

    // Update the volume and pitch by the velocity.
    m_audio.SetSoundEffectVolume(RollingEvent, volume);
    m_audio.SetSoundEffectPitch(RollingEvent, pitch);

    // The rolling sound has at most 8000Hz sounds, so we linearly
    // ramp up the low-pass filter the faster we go.
    // We also reduce the Q-value of the filter, starting with a
    // relatively broad cutoff and get progressively tighter.
    m_audio.SetSoundEffectFilter(
        RollingEvent,
        600.0f + 8000.0f * volume,
        XAUDIO2_MAX_FILTER_ONEOVERQ - volume*volume
        );
}
else
{
    m_audio.SetSoundEffectVolume(RollingEvent, 0);
}
```

## <a name="reacting-to-suspend-and-resume-events"></a>Реагирование на события приостановки и возобновления

В статье [Структура приложения Marble Maze](marble-maze-application-structure.md) описано, как игра Marble Maze поддерживает приостановку и возобновление. Если игра приостановлена, она приостанавливает звук. При возобновлении игры звук возобновляется с момента остановки. Такая схема позволяет следовать рекомендации: не использовать ресурсы там, где они заведомо не требуются.

Метод **Audio::SuspendAudio** вызывается при приостановке игры. Этот метод вызывает метод [IXAudio2::StopEngine](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2-stopengine), чтобы полностью прекратить звук. Хотя **IXAudio2::StopEngine** немедленно останавливает весь вывод звука, он сохраняет граф звука и параметры эффектов (например, эффект реверберации, применяемый при отскоке шарика).

```cpp
// Uses the IXAudio2::StopEngine method to stop all audio immediately.  
// It leaves the audio graph untouched, which preserves all effect parameters   
// and effect histories (like reverb effects) voice states, pending buffers,  
// cursor positions and so on. 
// When the engines are restarted, the resulting audio will sound as if it had  
// never been stopped except for the period of silence. 
void Audio::SuspendAudio()
{
    if (m_engineExperiencedCriticalError)
    {
        return;
    }

    if (m_isAudioStarted)
    {
        m_musicEngine->StopEngine();
        m_soundEffectEngine->StopEngine();
    }

    m_isAudioStarted = false;
}
```

Метод **Audio::ResumeAudio** вызывается, когда игра возобновляется. Этот метод вызывает метод [IXAudio2::StartEngine](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2-startengine), чтобы возобновить воспроизведение звука. Так как вызов метода [IXAudio2::StopEngine](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2-stopengine) сохраняет граф звука и параметры эффектов, вывод звука возобновляется с момента остановки.

```cpp
// Restarts the audio streams. A call to this method must match a previous call
// to SuspendAudio. This method causes audio to continue where it left off.
// If there is a problem with the restart, the m_engineExperiencedCriticalError
// flag is set. The next call to Render will recreate all the resources and
// reset the audio pipeline.
void Audio::ResumeAudio()
{
    if (m_engineExperiencedCriticalError)
    {
        return;
    }

    HRESULT hr = m_musicEngine->StartEngine();
    HRESULT hr2 = m_soundEffectEngine->StartEngine();

    if (FAILED(hr) || FAILED(hr2))
    {
        m_engineExperiencedCriticalError = true;
    }
}
```

## <a name="handling-headphones-and-device-changes"></a>Обработка смены устройств и наушников

Marble Maze использует обратные вызовы обработчика, чтобы обрабатывать сбои XAudio2, например при смене звукового устройства. Вероятная причина смены устройства — пользователь игры подключил или отключил наушники. Рекомендуется реализовать обратный вызов обработчика, который обрабатывает смену устройств. В противном случае при включении или отключении наушников игра не будет воспроизводить звук, пока не будет перезапущена.

В **Audio.h** определен класс **AudioEngineCallbacks**. Этот класс реализует интерфейс [IXAudio2EngineCallback](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2enginecallback).

```cpp
class AudioEngineCallbacks: public IXAudio2EngineCallback
{
private:
    Audio* m_audio;

public :
    AudioEngineCallbacks(){};
    void Initialize(Audio* audio);

    // Called by XAudio2 just before an audio processing pass begins.
    void _stdcall OnProcessingPassStart(){};

    // Called just after an audio processing pass ends.
    void  _stdcall OnProcessingPassEnd(){};

    // Called when a critical system error causes XAudio2
    // to be closed and restarted. The error code is given in Error.
    void  _stdcall OnCriticalError(HRESULT Error);
};
```

Интерфейс [IXAudio2EngineCallback](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2enginecallback) позволяет коду получать уведомления при возникновении событий обработки звука и критических ошибках обработчика. Для регистрации обратных вызовов Marble Maze вызывает метод [IXAudio2::RegisterForCallbacks](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2-registerforcallbacks) в **Audio::CreateResources** — после создания объекта [IXAudio2](https://docs.microsoft.com/windows/desktop/api/xaudio2/nn-xaudio2-ixaudio2) для обработчика музыки.

```cpp
m_musicEngineCallback.Initialize(this);
m_musicEngine->RegisterForCallbacks(&m_musicEngineCallback);
```

Marble Maze не требует уведомления при начале или окончании обработки звука. Поэтому в игре реализованы методы [IXAudio2EngineCallback::OnProcessingPassStart](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2enginecallback-onprocessingpassstart) и [IXAudio2EngineCallback::OnProcessingPassEnd](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2enginecallback-onprocessingpassend), которые не делают ничего. Для метода [IXAudio2EngineCallback:: Онкритикалеррор](https://docs.microsoft.com/windows/desktop/api/xaudio2/nf-xaudio2-ixaudio2enginecallback-oncriticalerror) мрамор лабиринт вызывает метод **сетенгиникспериенцедкритикалеррор** , который устанавливает флаг **m\_енгиникспериенцедкритикалеррор** .

```cpp
// Audio.cpp

// Called when a critical system error causes XAudio2 
// to be closed and restarted. The error code is given in Error. 
void  _stdcall AudioEngineCallbacks::OnCriticalError(HRESULT Error)
{
    m_audio->SetEngineExperiencedCriticalError();
}
```

```cpp
// Audio.h (Audio class)

// This flag can be used to tell when the audio system 
// is experiencing critial errors.
// XAudio2 gives a critical error when the user unplugs
// the headphones and a new speaker configuration is generated.
void SetEngineExperiencedCriticalError()
{
    m_engineExperiencedCriticalError = true;
}
```

При возникновении критической ошибки обработка звука останавливается и все последующие вызовы к XAudio2 заканчиваются сбоями. Для восстановления после этой ситуации необходимо высвободить текущий экземпляр XAudio2 и создать новый. Метод **Audio:: Render** , который вызывается из каждого кадра игры, сначала проверяет флаг **m\_енгиникспериенцедкритикалеррор** . Если этот флаг установлен, он снимает его, высвобождает текущий экземпляр XAudio2, инициализирует ресурсы и затем запускает фоновую музыку.

```cpp
if (m_engineExperiencedCriticalError)
{
    m_engineExperiencedCriticalError = false;
    ReleaseResources();
    Initialize();
    CreateResources();
    Start();
    if (m_engineExperiencedCriticalError)
    {
        return;
    }
}
```

Лабиринт мрамора также использует флаг **m\_енгиникспериенцедкритикалеррор** , чтобы защититься от вызова в XAudio2, когда нет доступного звукового устройства. Например, метод **MarbleMazeMain::Update** не обрабатывает звук для событий катящегося шарика или столкновения, если установлен этот флаг. Приложение пытается восстановить каждый кадр обработчика звука, если это необходимо; Однако флаг **m\_енгиникспериенцедкритикалеррор** может быть задан всегда, если на компьютере нет звукового устройства или если наушники не подключены и нет другого доступного звукового устройства.

> [!CAUTION]
> Как правило, не следует выполнять блокирующие операции в теле обратного вызова обработчика. Это может привести к проблемам с производительностью. Marble Maze задает флаг в обратном вызове **OnCriticalError** и затем обрабатывает ошибку во время обычной стадии обработки звука. Дополнительные сведения об обратных вызовах XAudio2 см. в разделе [Обратные вызовы XAudio2](https://docs.microsoft.com/windows/desktop/xaudio2/xaudio2-callbacks).

## <a name="conclusion"></a>Заключение

На этом рассмотрение игры-примера Marble Maze можно считать завершенным. Хотя эта игра относительно проста, она содержит многие из важных компонентов, присутствующих в любой игре UWP на базе DirectX, и служит хорошим примером, которому вы можете следовать при создании своей собственной игры.

Теперь, когда мы полностью рассмотрели игру, попробуйте поэкспериментировать с ее исходным кодом и посмотреть, что произойдет. Или переходите к статье [Создание простой игры UWP с использованием DirectX](tutorial--create-your-first-uwp-directx-game.md), где рассматривается другой пример игры UWP на базе DirectX.

Готовы углубиться в работу с DirectX? Ознакомьтесь с нашими руководствами по [программированию в DirectX](directx-programming.md).

Если же вас интересует разработка игр для UWP в целом, обратитесь к документации в разделе [Программирование игр](index.md).

## <a name="related-topics"></a>Статьи по теме

* [Добавление входных и интерактивных данных в образец лабиринта мрамора](adding-input-and-interactivity-to-the-marble-maze-sample.md)
* [Разработка лабиринта мрамора, игры UWP C++ в и DirectX](developing-marble-maze-a-windows-store-game-in-cpp-and-directx.md)