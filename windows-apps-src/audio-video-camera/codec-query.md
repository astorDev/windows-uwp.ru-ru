---
author: drewbatgit
ms.assetid: 0A360481-B649-4E90-9BC4-4449BA7445EF
description: "Запрос кодировщиков и декодеров аудио и видео, установленных на устройстве."
title: "Запрос установленных кодеков"
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, кодек, кодировщик, декодер, запрос"
ms.openlocfilehash: 1bde2c24db6faa843d9118f330867e7f66b22e7e
ms.sourcegitcommit: 64cfb79fd27b09d49df99e8c9c46792c884593a7
translationtype: HT
---
# <a name="query-for-installed-codecs"></a>Запрос установленных кодеков
В этой статье показано, как сделать запрос кодировщиков и декодеров звука и видео, установленных на устройстве. В статье [Поддерживаемые кодеки](supported-codecs.md) перечислены кодеки, включенные в ОС для каждого семейства устройств. На отдельном устройстве пользователем или приложением могут быть установлены дополнительные кодеки. Класс [CodecQuery](https://docs.microsoft.com/en-us/uwp/api/windows.media.core.codecquery) можно использовать для перечисления текущего набора кодировщиков и декодеров, установленных на устройстве с запущенным приложением.

Класс **CodecQuery** входит в пространство имен [windows.media.core](https://docs.microsoft.com/en-us/uwp/api/windows.media.core) и предоставляет конструктор, не принимающий аргументы.

[!code-cs[CodecQueryUsing](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetCodecQueryUsing)]

[!code-cs[NewCodecQuery](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetNewCodecQuery)]

Метод [FindAllAsync](https://docs.microsoft.com/en-us/uwp/api/windows.media.core.codecquery#Windows_Media_Core_CodecQuery_FindAllAsync_Windows_Media_Core_CodecKind_Windows_Media_Core_CodecCategory_System_String_) класса **CodecQuery** возвращает все установленные кодеки, соответствующие указанным параметрам. Эти параметры включают значение из перечисления [CodecKind](https://docs.microsoft.com/en-us/uwp/api/windows.media.core.codeckind), указывающее, нужно ли возвращать аудио- или видеокодеки, и значение [CodecCategory](https://docs.microsoft.com/en-us/uwp/api/windows.media.core.codeccategory), указывающее, нужно ли возвращать аудио- или видеокодеки.

Последний параметр является строкой, представляющей подтип носителя, например видео H264 или аудио FLAC, которые должны поддерживаться любым кодеком из числа возвращаемых **FindAllAsync**. Вы можете указать для этого параметра значение null или пустую строку, чтобы возвращались кодеки для всех подтипов мультимедиа. В следующем примере перечислены все кодировщики видео, установленные на текущем устройстве.

[!code-cs[FindAllEncoders](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetFindAllEncoders)]

При указании подтипа носителя необходимо использовать строковое представление одного из подтипов GUID, перечисленных в [GUID подтипа аудио](https://msdn.microsoft.com/library/windows/desktop/aa372553(v=vs.85).aspx) или [GUID подтипа видео](https://msdn.microsoft.com/library/windows/desktop/aa370819(v=vs.85).aspx). Класс [CodecSubtypes](https://docs.microsoft.com/en-us/uwp/api/windows.media.core.codecsubtypes) предоставляет свойства, для большинства поддерживаемых подтипов мультимедиа, возвращающих строковое представление подтипа GUID. Можно также указать код FOURCC для параметра подтипа мультимедиа. Дополнительные сведения см. в разделе [Коды FOURCC](https://msdn.microsoft.com/library/windows/desktop/dd375802(v=vs.85).aspx). 

В следующем примере используется код FOURCC для запроса декодеров видео, поддерживающих видео в формате H.264. Пример сценария для выполнения этой операции: проверить, поддерживается ли формат видео, прежде чем пытаться воспроизвести видеофайл.

[!code-cs[IsH264Supported](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetIsH264Supported)]

В следующем примеры отправляется запрос кодировщиков аудио, поддерживающих подтип носителя FLAC. Затем в этом примере создается объект AudioEncodinAudioEncodingProperties и MediaEncodingProfile для подтипа носителя. Это можно использовать для записи звука в указанном формате или перекодирования звука из другого формата. Подробнее см. в статьях [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md) и [Перекодирование файлов мультимедиа](transcode-media-files.md).

[!code-cs[IsFLACSupported](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetIsFLACSupported)]

## <a name="related-topics"></a>Еще по теме

* [Поддерживаемые кодеки](supported-codecs.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Перекодирование файлов мультимедиа](transcode-media-files.md)
 

 




