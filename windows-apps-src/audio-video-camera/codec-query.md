---
author: drewbatgit
ms.assetid: 0A360481-B649-4E90-9BC4-4449BA7445EF
description: Запрос кодировщиков и декодеров аудио и видео, установленных на устройстве.
title: Запрос установленных кодеков
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, кодек, кодировщик, декодер, запрос
ms.localizationpriority: medium
ms.openlocfilehash: b74ac269bcba6d15e7c4f5dcb4c34d53573deb5e
ms.sourcegitcommit: 144f5f127fc4fbd852f2f6780ef26054192d68fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5976054"
---
# <a name="query-for-codecs-installed-on-a-device"></a>Запрос кодеков, установленных на устройстве
Класс **[CodecQuery](https://docs.microsoft.com/uwp/api/windows.media.core.codecquery)** позволяет запрашивать кодеки, установленные на текущем устройстве. Список кодеков, которые входят в состав Windows 10 для различных семейств устройств, перечислен в статье [Поддерживаемые кодеки](supported-codecs.md), но поскольку пользователи и приложения могут устанавливать дополнительные кодеки на устройство, можно запросить поддержку кодеков во время выполнения, чтобы определить, какие кодеки доступны на текущем устройстве.

API CodecQuery является членом пространства имен **[Windows.Media.Core](https://docs.microsoft.com/uwp/api/windows.media.core)**, поэтому будет необходимо включить это пространство имен в приложение.

API CodecQuery является членом пространства имен **[Windows.Media.Core](https://docs.microsoft.com/uwp/api/windows.media.core)**, поэтому будет необходимо включить это пространство имен в приложение.

[!code-cs[CodecQueryUsing](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetCodecQueryUsing)]

Инициализируйте новый экземпляр класса **CodecQuery** путем вызова конструктора.

[!code-cs[NewCodecQuery](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetNewCodecQuery)]

Метод **[FindAllAsync](https://docs.microsoft.com/uwp/api/windows.media.core.codecquery.findallasync)** возвращает все установленные кодеки, которые соответствуют предоставленным параметрам. Эти параметры включают значение **[CodecKind](https://docs.microsoft.com/uwp/api/windows.media.core.codeckind)**, указывающее, какие кодеки запрашиваются: аудиокодеки, видеокодеки или оба, — значение **[CodecCategory](https://docs.microsoft.com/uwp/api/windows.media.core.codeccategory)**, указывающее, что запрашивается: кодировщики или декодировщики, — а также строка, которая представляет подтип закодированного мультимедиа, который запрашивается, например видео H.264 или аудио MP3.

Укажите пустую строку или значение null для значения подтипа, чтобы вернуть кодеки для всех подтипов. В следующем примере перечислены все кодировщики видео, установленные на устройстве.

[!code-cs[FindAllEncoders](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetFindAllEncoders)]

Строка подтипа, которая передается в **FindAllAsync**, может быть строковым представлением подтипа GUID, которое определяется системой, или кодом FOURCC для подтипа. Набор GUID подтипов поддерживаемых файлов мультимедиа перечислен в статьях [GUID подтипа аудио](https://msdn.microsoft.com/library/windows/desktop/aa372553(v=vs.85).aspx) и [GUID подтипа видео](https://msdn.microsoft.com/library/windows/desktop/aa370819(v=vs.85).aspx), но класс **[CodecSubtypes](https://docs.microsoft.com/uwp/api/windows.media.core.codecsubtypes)** предоставляет свойства, которые возвращают значения GUID для каждого поддерживаемого подтипа. Дополнительные сведения о кодах FOURCC см. в статье [Коды FOURCC](https://msdn.microsoft.com/library/windows/desktop/dd375802(v=vs.85).aspx) 

В приведенном ниже примере указан код FOURCC "H264", который определяет, установлен ли на устройство видеодекодер H.264. Этот запрос можно выполнить перед воспроизведением видеоконтента H.264. Также можно обработать неподдерживаемые кодеки во время воспроизведения. Дополнительные сведения см. в разделе [Обработка неподдерживаемых кодеков и неизвестные ошибки при открытии элементов мультимедиа](https://docs.microsoft.com/windows/uwp/audio-video-camera/media-playback-with-mediasource#handle-unsupported-codecs-and-unknown-errors-when-opening-media-items).

[!code-cs[IsH264Supported](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetIsH264Supported)]

Ниже приведен пример запроса для определения, установлен ли кодировщик аудио FLAC на текущем устройстве и, если это так, **[MediaEncodingProfile](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.MediaEncodingProfile)** создается для подтипа, который может использоваться для записи звука в файл или перекодирования звука из другой формата в звуковой файл FLAC.

[!code-cs[IsFLACSupported](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetIsFLACSupported)]

## <a name="related-topics"></a>Статьи по теме

* [Воспроизведение мультимедиа](media-playback.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Перекодирование файлов мультимедиа](transcode-media-files.md)
* [Поддерживаемые кодеки](supported-codecs.md)
 

 




