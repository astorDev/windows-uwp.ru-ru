---
ms.assetid: A1A0D99A-DCBF-4A14-80B9-7106BEF045EC
description: Для перекодирования видеофайлов из одного формата в другой можно использовать API Windows.Media.Transcoding.
title: Перекодирование файлов мультимедиа
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 38aef2779908e173712bda0f35ca9e0651fb786b
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75683877"
---
# <a name="transcode-media-files"></a>Перекодирование файлов мультимедиа



Для перекодирования видеофайлов из одного формата в другой можно использовать API [**Windows.Media.Transcoding**](https://docs.microsoft.com/uwp/api/Windows.Media.Transcoding).

*Перекодирование* — это преобразование цифрового файла мультимедиа (например, звукового или видеофайла) из одного формата в другой. Обычно этот процесс состоит из декодирования и повторного кодирования файла. Например, файл Windows Media можно преобразовать в формат MP4, чтобы его можно было воспроизводить на портативном устройстве, поддерживающем данный формат. Кроме того, можно преобразовать видеофайл высокой четкости, уменьшив его разрешение. В таком случае перекодированный файл может использовать тот же кодек, что и исходный файл, но с другим профилем кодирования.

## <a name="set-up-your-project-for-transcoding"></a>Настройка проекта для перекодирования

Чтобы перекодировать файлы мультимедиа с помощью кода в данной статье, помимо пространств имен, на которые по умолчанию ссылается шаблон проекта, вам потребуется ссылаться на указанные ниже пространства имен.

[!code-cs[Using](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetUsing)]

## <a name="select-source-and-destination-files"></a>Выбор исходного файла и файла назначения

Способ, которым приложение будет определять исходный файлах и файл назначения для перекодирования, зависит от вашей реализации. В этом примере используются элементы управления [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) и [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker), с помощью которых пользователь может выбрать исходный файл и файл назначения.

[!code-cs[TranscodeGetFile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeGetFile)]

## <a name="create-a-media-encoding-profile"></a>Создание профиля кодирования мультимедиа

Профиль кодирования содержит параметры, определяющие способ кодирования файла назначения. Именно на этом этапе перекодирования файла выбор параметров наиболее велик.

Класс [**MediaEncodingProfile**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.MediaEncodingProfile) содержит статические методы для создания указанных ниже предопределенных профилей кодирования.

### <a name="methods-for-creating-audio-only-encoding-profiles"></a>Методы для создания профилей кодирования только аудио

Метод  |Профиль  |
---------|---------|
[**креатеалак**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createalac)     |Аудио Apple Lossless Audio Codec (ALAC)         |
[**креатефлак**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createflac)     |Аудио Free Lossless Audio Codec (FLAC).         |
[**CreateM4a**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createm4a)     |Звуковые файлы в формате AAC (M4A)         |
[**CreateMp3**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createmp3)     |Звуковые файлы в формате MP3         |
[**креатевав**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createwav)     |Звуковые файлы в формате WAV         |
[**креатевмв**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createwmv)     |Звуковые файлы в формате WMA         |

### <a name="methods-for-creating-audio--video-encoding-profiles"></a>Методы для создания профилей кодирования аудио/видео

Метод  |Профиль  |
---------|---------|
[**креатеави**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createavi) |AVI |
[**креатехевк**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createhevc) |Видео High Efficiency Video Coding (HEVC), известное также как видео H.265 |
[**CreateMp4**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createmp4) |Видеофайлы в формате MP4 (видео в формате H.264 и звук в формате AAC) |
[**креатевмв**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createwmv) |Видеофайлы в формате WMV |


Указанный ниже фрагмент кода используется для создания профиля видеофайла в формате MP4.

[!code-cs[TranscodeMediaProfile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeMediaProfile)]

Статический метод [**CreateMp4**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createmp4) создает профиль кодирования для MP4. Параметр этого метода задает конечное разрешение видеофайла. В данном случае [**VideoEncodingQuality.hd720p**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.VideoEncodingQuality) означает разрешение 1280 x 720 пикселей со скоростью 30 кадров в секунду ("720p" означает 720 строк прогрессивной развертки на один кадр). Все остальные методы создания предопределенных профилей работают по тому же принципу.

В качестве альтернативы с помощью метода [**MediaEncodingProfile.CreateFromFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createfromfileasync) можно создать профиль, который соответствует существующему файлу мультимедиа. Если же вам известны точные параметры кодирования, то можно создать новый объект [**MediaEncodingProfile**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.MediaEncodingProfile) и настроить для него профиль.

## <a name="transcode-the-file"></a>Перекодировка файла

Чтобы перекодировать файл, создайте новый объект [**MediaTranscoder**](https://docs.microsoft.com/uwp/api/Windows.Media.Transcoding.MediaTranscoder) и вызовите метод [**MediaTranscoder.PrepareFileTranscodeAsync**](https://docs.microsoft.com/uwp/api/windows.media.transcoding.mediatranscoder.preparefiletranscodeasync). В качестве параметров задайте исходный файл, конечный файл и профиль кодирования. Затем вызовите метод [**TranscodeAsync**](https://docs.microsoft.com/uwp/api/windows.media.transcoding.preparetranscoderesult.transcodeasync) для объекта [**PrepareTranscodeResult**](https://docs.microsoft.com/uwp/api/Windows.Media.Transcoding.PrepareTranscodeResult), возвращенного в результате выполнения операции асинхронного перекодирования.

[!code-cs[TranscodeTranscodeFile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeTranscodeFile)]

## <a name="respond-to-transcoding-progress"></a>Реакция на ход выполнения перекодирования

Вы можете регистрировать события, чтобы реагировать на изменения в ходе выполнения асинхронного [**TranscodeAsync**](https://docs.microsoft.com/uwp/api/windows.media.transcoding.preparetranscoderesult.transcodeasync). Эти события — часть платформы асинхронного программирования для приложений универсальной платформы Windows (UWP) и не зависят от API перекодирования.

[!code-cs[TranscodeCallbacks](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeCallbacks)]


## <a name="encode-a-metadata-stream"></a>Кодирование потока метаданных
Начиная с Windows 10 версии 1803, можно включить метаданные времени при кодировании файлов мультимедиа. В отличие от приведенных выше примеров кодирования видео, использующих встроенные методы создания профилей кодирования мультимедиа, например [**медиаенкодингпрофиле. CreateMp4**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createmp4), необходимо вручную создать профиль кодирования метаданных для поддержки типа метаданных, которые вы кодируете.

Первым шагом при создании профиля создания кода метаданных является создание объекта [**тимедметадатаенкодингпропертиес**], который описывает кодировку метаданных для перекодирования. Свойство подтипа — это идентификатор GUID, указывающий тип метаданных. Сведения о кодировке для каждого типа метаданных являются собственными и не предоставляются Windows. В этом примере используется GUID для метаданных GoPro (GPRS). Затем вызывается [**сетформатусердата**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.timedmetadataencodingproperties.setformatuserdata) для установки двоичного BLOB-объекта данных, описывающего формат потока, относящийся к формату метаданных. Далее **тимедметадатастреамдескриптор**(https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatastreamdescriptor) создается из задающий свойства кодирования, а метка и имя Track позволяют приложению, читающему поток ендкодед, определить поток метаданных и при необходимости отобразить имя потока в пользовательском интерфейсе. 
 
[!code-cs[GetStreamDescriptor](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetGetStreamDescriptor)]

После создания **тимедметадатастреамдескриптор**можно создать **медиаенкодингпрофиле** , описывающий видео, аудио и метаданные, которые должны быть закодированы в файле. **Тимедметадатастреамдескриптор** , созданный в последнем примере, передается в этот пример вспомогательной функции и добавляется в **Медиаенкодингпрофиле** путем вызова [**сеттимедметадататраккс**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.settimedmetadatatracks).

[!code-cs[GetMediaEncodingProfile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetGetMediaEncodingProfile)]
 

 




