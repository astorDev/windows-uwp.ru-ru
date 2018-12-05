---
ms.assetid: ''
description: В этой статье показано, как одновременно фиксировать видео из нескольких источников в одном файле с несколькими встроенными видеодорожками.
title: Фиксация из нескольких источников с использованием класса MediaFrameSourceGroup
ms.date: 09/12/2017
ms.topic: article
keywords: windows 10, uwp, фиксация, видео
ms.localizationpriority: medium
ms.openlocfilehash: a654739490043b9f821e7906fa8cf9e3e7259fed
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8758804"
---
# <a name="capture-from-multiple-sources-using-mediaframesourcegroup"></a>Фиксация из нескольких источников с использованием класса MediaFrameSourceGroup

В этой статье показано, как одновременно фиксировать видео из нескольких источников в одном файле с несколькими встроенными видеодорожками. Начиная с RS3, можно задать несколько объектов **[VideoStreamDescriptor](https://docs.microsoft.com/uwp/api/windows.media.core.videostreamdescriptor)** для одного профиля **[MediaEncodingProfile](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile)**. Это позволяет кодировать несколько потоков одновременно в один файл. Видеопотоки, кодируемые в ходе этой операции, необходимо включить в один тип **[MediaFrameSourceGroup](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup)**, который задает набор камер на текущем устройстве, которые можно использовать одновременно. 

Сведения об использовании типа **[MediaFrameSourceGroup](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup)** с классом **[MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader)** для включения сценариев компьютерного видения в режиме реального времени, подразумевающих использование нескольких камер, см. в разделе [Обработка кадров мультимедиа с использованием MediaFrameReader](process-media-frames-with-mediaframereader.md).

В остальных частях этой статьи приводятся пошаговые инструкции по записи видео с двух цветных камер в один файл с несколькими видеодорожками.

## <a name="find-available-sensor-groups"></a>Поиск доступных групп датчиков
Тип **MediaFrameSourceGroup** представляет собой коллекцию источников кадров (как правило, камер), доступ к которым можно осуществлять одновременно. Наборы доступных групп источников кадров для разных устройств различаются, поэтому в качестве первого шага в этом примере является получение списка доступных групп источников и поиск группы, содержащей необходимые камеры для этого сценария (в данном случае— это две цветные камеры).

Метод **[MediaFrameSourceGroup.FindAllAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup.FindAllAsync)** возвращает все группы источников, доступные на текущем устройстве. Каждый возвращенный тип **MediaFrameSourceGroup** содержит список объектов **[MediaFrameSourceInfo](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourceinfo)**, описывающих каждый источник кадров в группе. Запрос Linq используется для поиска группы источников, которая содержит две цветные камеры: одну— для передней панели, а другую— для задней. Возвращается анонимный объект, который содержит выбранные типы **MediaFrameSourceGroup** и **MediaFrameSourceInfo** для каждой цветной камеры. Вместо того чтобы использовать синтаксис Linq, можно выполнить циклический перебор каждой группы, а затем с помощью метода **MediaFrameSourceInfo** найти группу, соответствующую вашим требованиям.

Обратите внимание, что не каждое устройство будет содержать группу источников с двумя цветными камерами, поэтому прежде чем пытаться зафиксировать видео, необходимо убедиться, что группа источников найдена.

[!code-cs[MultiRecordFindSensorGroups](./code/SimpleCameraPreview_Win10/cs/MainPage.MultiRecord.xaml.cs#SnippetMultiRecordFindSensorGroups)]

## <a name="initialize-the-mediacapture-object"></a>Инициализация объекта MediaCapture
Класс **[MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture)** представляет собой основной класс, используемый для большинства операций захвата звука, видео и фото в приложениях UWP. Инициализируйте объект, вызвав метод **[InitializeAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.InitializeAsync)** и передав объект **[MediaCaptureInitializationSettings](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings)**, который содержит параметры инициализации. В этом примере единственным заданным параметром является свойство **[SourceGroup](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings.SourceGroup)**, которое имеет значение **MediaFrameSourceGroup**, извлеченное в предыдущем примере кода.

Сведения о других операциях, которые можно выполнить с помощью **MediaCapture** и других функций приложения UWP для захвата мультимедиа, см. в разделе [Камера](camera.md).

[!code-cs[MultiRecordInitMediaCapture](./code/SimpleCameraPreview_Win10/cs/MainPage.MultiRecord.xaml.cs#SnippetMultiRecordInitMediaCapture)]

## <a name="create-a-mediaencodingprofile"></a>Создание класса MediaEncodingProfile
Класс **[MediaEncodingProfile](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile)** информирует конвейер захвата мультимедиа о том, как необходимо кодировать зафиксированные аудио и видео при записи в файл. Для стандартных сценариев захвата и перекодирования этот класс предоставляет набор статических методов для создания стандартных профилей, таких как **[CreateAvi](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createavi)** и **[CreateMp3](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createmp3)**. В этом примере профиль кодирования вручную создается с использованием контейнера Mpeg4 и кодирования видео в формате H264. Параметры кодирования видео задаются с использованием объекта **[VideoEncodingProperties](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.videoencodingproperties)**. Объект **VideoStreamDescriptor** настраивается для каждой цветной камеры, используемой в этом сценарии. Дескриптор создается с объектом **VideoEncodingProperties**, определяющим способ кодирования. Для свойства **[Label](https://docs.microsoft.com/uwp/api/windows.media.core.videostreamdescriptor.Label)** класса **VideoStreamDescriptor** необходимо задать ИД источника кадров мультимедиа, которые будут захвачены в потоке. В результате конвейер захвата будет знать, какой дескриптор потока и свойства кодирования следует использовать для каждой камеры. Идентификатор источника кадра предоставляется объектами **[MediaFrameSourceInfo](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourceinfo)**, обнаруженными в предыдущем разделе при выборе класса **MediaFrameSourceGroup**.


Начиная с Windows 10 версии 1709 вы можете задать несколько свойств кодирования в профиле **MediaEncodingProfile**, вызвав **[SetVideoTracks](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.setvideotracks)**. Вызов метода **[GetVideoTracks](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.GetVideoTracks)** позволяет извлечь список дескрипторов видеопотока. Обратите внимание, что если задано свойство **[Video](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.Video)**, которое содержит один дескриптор потока, задаваемый вызовом метода **SetVideoTracks** список дескрипторов будет заменен списком, который содержит один заданный дескриптор.


[!code-cs[MultiRecordMediaEncodingProfile](./code/SimpleCameraPreview_Win10/cs/MainPage.MultiRecord.xaml.cs#SnippetMultiRecordMediaEncodingProfile)]

### <a name="encode-timed-metadata-in-media-files"></a>Кодирование синхронизированных метаданных в файлах мультимедиа

Начиная с Windows 10 версии 1803, помимо аудио и видео, вы можете кодировать синхронизированные метаданные в файле мультимедиа, формат данных которого поддерживается. Например, метаданные GoPro (gpmd) могут храниться в MP4-файлах для передачи сведений о географическом положении, связанных с видеопотоком. 

При кодировке метаданных используется шаблон, аналогичный кодированию аудио или видео. Класс [**TimedMetadataEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.timedmetadataencodingproperties) описывает тип, подтип и свойства кодирования метаданных так же, как **VideoEncodingProperties** делает это для видео. [**TimedMetadataStreamDescriptor**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatastreamdescriptor) определяет поток метаданных так же, как **VideoStreamDescriptor** делает это для видеопотоков.  

В следующем примере показано, как инициализировать объект **TimedMetadataStreamDescriptor**. Сначала создается объект **TimedMetadataEncodingProperties**, а свойству **Subtype** задает значение GUID, определяющее тип метаданных, которые будут включены в поток. В этом примере использует идентификатор GUID для метаданных GoPro (gpmd). Метод [**SetFormatUserData**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.timedmetadataencodingproperties.setformatuserdata) вызывается для настройки данных, связанных с форматом. Для MP4-файлов данные для определенного формата хранятся в поле SampleDescription (stsd). Затем на основе свойств кодирования создается новый объект **TimedMetadataStreamDescriptor**. Свойства **Label** и **Name** задаются для идентификации кодируемого потока. 

[!code-cs[GetStreamDescriptor](./code/SimpleCameraPreview_Win10/cs/MainPage.MultiRecord.xaml.cs#SnippetGetStreamDescriptor)]

Вызовите метод [MediaEncodingProfile.SetTimedMetadataTracks](**https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.settimedmetadatatracks**), чтобы добавить дескриптор потока метаданных в профиль кодирования. В следующем примере показан вспомогательный метод, который принимает два дескриптора видеопотока, один дескриптор звукового потока и один дескриптор потока синхронизированных метаданных и возвращает объект **MediaEncodingProfile**, который можно использовать для кодирования потоков.

[!code-cs[GetMediaEncodingProfile](./code/SimpleCameraPreview_Win10/cs/MainPage.MultiRecord.xaml.cs#SnippetGetMediaEncodingProfile)]

## <a name="record-using-the-multi-stream-mediaencodingprofile"></a>Запись с использованием многопоточного профиля MediaEncodingProfile
Заключительный шаг в этом примере— запуск захвата видео посредством вызова метода **[StartRecordToStorageFileAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.startrecordtostoragefileasync)**, передача файла **StorageFile**, в который осуществляется запись мультимедиа, и профиля **MediaEncodingProfile**, созданного в предыдущем примере кода. Через несколько секунд ожидания запись останавливается, вызывается метод **[StopRecordAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.StopRecordAsync)**.

[!code-cs[MultiRecordToFile](./code/SimpleCameraPreview_Win10/cs/MainPage.MultiRecord.xaml.cs#SnippetMultiRecordToFile)]

В результате операции создается видеофайл, который содержит видео, захваченное с каждой камеры, которая кодируется в файле в качестве отдельного потока. Сведения о воспроизведении файлов мультимедиа, которые содержат несколько видеодорожек, см. в разделе [Элементы мультимедиа, списки воспроизведения и звуковые дорожки](media-playback-with-mediasource.md).

## <a name="related-topics"></a>Связанные статьи

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Обработка кадров мультимедиа с помощью MediaFrameReader](process-media-frames-with-mediaframereader.md)
* [Элементы мультимедиа, списки воспроизведения и звуковые дорожки](media-playback-with-mediasource.md)


 

 




