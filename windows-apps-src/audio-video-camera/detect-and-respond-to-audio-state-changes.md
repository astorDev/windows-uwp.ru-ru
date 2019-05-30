---
ms.assetid: EE0C1B28-EF9C-4BD9-A3C0-BDF11E75C752
description: В этой статье описывается, как приложения UWP могут обнаруживать изменения уровней аудиопотока, инициированные системой, и реагировать на них
title: Обнаружение и реагирование на изменения состояния звука
ms.date: 04/03/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1a680347e9d1a749cc6e1d86ef1f02da280b4b74
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66361782"
---
# <a name="detect-and-respond-to-audio-state-changes"></a>Обнаружение и реагирование на изменения состояния звука
Начиная с Windows 10 версии 1803 ваше приложение может определять, когда система снижает или отключает уровень звукового потока, который использует ваше приложение. Вы можете получать уведомления для захвата и воспроизведения потоков для определенного звукового устройства и категории аудио или для объекта [**MediaPlayer**](https://docs.microsoft.com/en-us/uwp/api/Windows.Media.Playback.MediaPlayer), который ваше приложение использует для воспроизведения мультимедиа. Например, система может снизить уровень звука, если включается будильник. Система отключает звук приложения, когда оно переходит в фоновый режим, если приложение не объявило возможность *backgroundMediaPlayback* в манифесте приложения. 

Шаблон обработки изменения состояния звука одинаков для всех поддерживаемых звуковых потоков. Сначала создайте экземпляр класса [**AudioStateMonitor**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor). В следующем примере приложение использует класс [**MediaCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) для записи звука для игрового чата. Фабричный метод вызывается для получения монитора состояния звука, связанного с потоком аудиозаписи игрового чата устройства связи по умолчанию.  Затем регистрируется обработчик события [**SoundLevelChanged**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor.soundlevelchanged), который активируется при изменении уровня звука связанного потока системой.

[!code-cs[DeviceIdCategoryVars](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetDeviceIdCategoryVars)]

[!code-cs[SoundLevelDeviceIdCategory](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetSoundLevelDeviceIdCategory)]

В **SoundLevelChanged** обработчик событий, проверьте [ **SoundLevel** ](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor.soundlevel) свойство **AudioStateMonitor** переданные отправителя обработчик, чтобы определить, является новый уровень звука для потока. В этом примере приложение перестает записывать аудио, если звук отключен, и возобновляет запись, когда восстанавливается полная громкость звука.

[!code-cs[GameChatSoundLevelChanged](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetGameChatSoundLevelChanged)]

Дополнительные сведения о записи звука с помощью **MediaCapture** см. в разделе [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md).

С каждым экземпляром класса [**MediaPlayer**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer) связан объект **AudioStateMonitor**, который можно использовать, чтобы определить, когда система изменяет уровень громкости воспроизводимого содержимого. Вы можете обрабатывать изменения состояния звука по-разному в зависимости от типа воспроизводимого содержимого. Например, вы можете приостановить воспроизведение подкаста при уменьшении громкости и продолжить воспроизведение, если это музыка. 

[!code-cs[AudioStateVars](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetAudioStateVars)]

[!code-cs[SoundLevelChanged](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSoundLevelChanged)]

Дополнительные сведения об использовании класса **MediaPlayer** см. в разделе [Воспроизведение аудио и видео с помощью класса MediaPlayer](play-audio-and-video-with-mediaplayer.md). 

## <a name="related-topics"></a>См. также