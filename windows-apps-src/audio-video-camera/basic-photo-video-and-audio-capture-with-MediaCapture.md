---
author: drewbatgit
ms.assetid: 66d0c3dc-81f6-4d9a-904b-281f8a334dd0
description: В этой статье описан самый простой способ записи фотографий и видео с помощью класса MediaCapture.
title: Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 7204c01cc80c50dacb2009b2138a7c046974dc62
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6674645"
---
# <a name="basic-photo-video-and-audio-capture-with-mediacapture"></a>Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture


В этой статье описан самый простой способ записи фотографий и видео с помощью класса [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture). Класс **MediaCapture** предоставляет широкий набор API, которые обеспечивают низкоуровневое управление конвейером захвата и поддерживают расширенные сценарии захвата, но цель этой статьи— помочь быстро и просто добавлять в приложение основные функции захвата мультимедиа. Дополнительные сведения о возможностях класса **MediaCapture** см. в разделе [**Камера**](camera.md).

Если вы хотите просто захватить фотографию или видео и не планируете добавлять дополнительные возможности захвата мультимедиа или не хотите создать собственный пользовательский интерфейс камеры, можно использовать класс [**CameraCaptureUI**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.CameraCaptureUI), который позволяет просто запустить встроенное приложение камеры Windows и получить созданную фотографию или видео. Дополнительные сведения см. в разделе [**Фото- и видеосъемка с помощью встроенного пользовательского интерфейса камеры в Windows**](capture-photos-and-video-with-cameracaptureui.md).

В этой статье используется код, адаптированный из примера [**Начальный набор камеры**](https://go.microsoft.com/fwlink/?linkid=619479). Вы можете скачать этот пример, чтобы просмотреть код в контексте или использовать пример как отправную точку для настройки собственного приложения.

## <a name="add-capability-declarations-to-the-app-manifest"></a>Добавление объявлений возможностей в манифест приложения

Чтобы ваше приложение получило доступ к камере устройства, необходимо объявить, что оно использует возможности устройства *webcam* и *microphone*. Если нужно сохранить захваченные фотографии и видео в библиотеке изображений или видео пользователя, следует также объявить возможности *picturesLibrary* и *videosLibrary*.

**Добавление возможностей в манифест приложения**

1.  В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest** в **Обозревателе решений**.
2.  Перейдите на вкладку **Возможности**.
3.  Выставьте флажок для пункта **Веб-камера** и поле для параметра **Микрофон**.
4.  Для доступа к библиотеке изображений и видео установите флажки **Библиотека изображений** и **Библиотека видео**.


## <a name="initialize-the-mediacapture-object"></a>Инициализация объекта MediaCapture
Для всех методов захвата, описанных в этой статье, сначала требуется инициализировать объект [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture), вызвав конструктор и затем — [**InitializeAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture.InitializeAsync). Так как доступ к объекту **MediaCapture** будет осуществляться из различных областей приложения, объявите переменную класса для размещения объекта.  Реализуйте обработчик для свойства [**Failed**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture.Failed) объекта **MediaCapture**, чтобы получить уведомление, если операция захвата завершится ошибкой.

[!code-cs[DeclareMediaCapture](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetDeclareMediaCapture)]

[!code-cs[InitMediaCapture](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetInitMediaCapture)]

## <a name="set-up-the-camera-preview"></a>Настройка просмотра камеры
Можно захватывать фотографии, видео и звук с помощью **MediaCapture** без показа изображения камеры, но обычно это необходимо, чтобы пользователь видел, что именно записывается. Кроме того, для использования некоторых функций **MediaCapture**, таких как автоматическая фокусировка, экспозиция и регулировка баланса белого, необходим поток предварительного просмотра. Сведения о настройке предварительного просмотра камеры см. в разделе [**Отображение просмотра камеры**](simple-camera-preview-access.md).

## <a name="capture-a-photo-to-a-softwarebitmap"></a>Захват фотографии в классе SoftwareBitmap
Класс [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Imaging.SoftwareBitmap) появился в Windows 10 и используется в качестве общего представления изображений для многих функций. Если вы хотите записать фотографию, а затем сразу использовать полученное изображение в приложение, например показать его в коде XAML, вместо записи в файл следует записать его в класс **SoftwareBitmap**. Вы по-прежнему сможете сохранить изображение на диск позднее.

После инициализации объекта **MediaCapture** вы можете записать фотографию в **SoftwareBitmap** с помощью класса [**LowLagPhotoCapture**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagPhotoCapture). Получите экземпляр класса, вызвав метод [**PrepareLowLagPhotoCaptureAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture.PrepareLowLagPhotoCaptureAsync) и передав объект [**ImageEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaProperties.ImageEncodingProperties), указывающий нужный формат изображения. [**CreateUncompressed**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaProperties.ImageEncodingProperties.CreateUncompressed) создает несжатую кодировку с заданным форматом пикселя. Чтобы захватить фотографию, вызовите метод [**CaptureAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagPhotoCapture.CaptureAsync), который возвращает объект [**CapturedPhoto**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.CapturedPhoto). Получите объект **SoftwareBitmap** с помощью свойств [**Frame**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.CapturedPhoto.Frame) и [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.CapturedFrame.SoftwareBitmap).

При желании можно получить несколько фотографий, повторно вызывая **CaptureAsync**. Получив нужные данные, вызовите [**FinishAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.AdvancedPhotoCapture.FinishAsync) для завершения сеанса **LowLagPhotoCapture** и освобождения связанных ресурсов. Чтобы начать захват фотографий после вызова метода **FinishAsync**, потребуется снова вызвать [**PrepareLowLagPhotoCaptureAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture.PrepareLowLagPhotoCaptureAsync) для повторной инициализации сеанса захвата перед вызовом [**CaptureAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagPhotoCapture.CaptureAsync).

[!code-cs[CaptureToSoftwareBitmap](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetCaptureToSoftwareBitmap)]

Начиная с Windows версии 1803, вы можете получить доступ к свойству [**BitmapProperties**](https://docs.microsoft.com/uwp/api/windows.media.capture.capturedframe.bitmapproperties) класса **CapturedFrame**, полученного из метода **CaptureAsync** для извлечения метаданных о записанной фотографии. Эти данные можно передать в **BitmapEncoder**, чтобы сохранения метаданные в файл. Ранее не было возможности получить доступ к этим данным для изображений в несжатых форматах. Также вы можете использовать свойство [**ControlValues**](https://docs.microsoft.com/uwp/api/windows.media.capture.capturedframe.controlvalues), чтобы получить объект [**CapturedFrameControlValues**](https://docs.microsoft.com/uwp/api/windows.media.capture.capturedframecontrolvalues), который описывает контрольные значения, например, экспозицию и баланс белого в записанном кадре.

Дополнительные сведения о работе с **BitmapEncoder** и объектом **SoftwareBitmap**, включая метод отображения такого объекта на XAML-странице, см. в статье [**Создание, редактирование и запись растровых изображений**](imaging.md). 

Дополнительные сведения о задании контрольных значений устройства захвата см. в разделе [Контрольные значения устройства захвата фотографий и видео](capture-device-controls-for-photo-and-video-capture.md).

Начиная с Windows 10 версии 1803, вы можете получить метаданные, такие как EXIF, для фотографий, записанных в формате без сжатия, использовав свойство [**BitmapProperties**](https://docs.microsoft.com/uwp/api/windows.media.capture.capturedframe.bitmapproperties) свойство объекта **CapturedFrame**, возвращаемого **MediaCapture**. В предыдущих выпусках эти данные были доступны только в заголовке фотографий, записанных в файловых форматах со сжатием. Эти данные можно передать в [**BitmapEncoder**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder) при записи файла изображения вручную. Дополнительные сведения про запись растровых изображений см. в статье [Создание, редактирование и запись растровых изображений](imaging.md).  Можно также получить доступ к контрольным значениям кадра, например, параметрам экспозиции и вспышки, которые применялись при записи кадра, обратившись к свойству [**ControlValues**](https://docs.microsoft.com/en-us/uwp/api/windows.media.capture.capturedframe.controlvalues). Дополнительные сведения см. в разделе [Контрольные значения устройства захвата фотографий и видео](capture-device-controls-for-photo-and-video-capture.md).

## <a name="capture-a-photo-to-a-file"></a>Фотозахват в файл
Типичное фотоприложение сохраняет полученные фотографии на диск или в облачное хранилище, а также добавляет метаданные, например сведения об ориентации фотографии, в файл. В следующем примере показано, как записать фотографию в файл. Вы по-прежнему сможете создать объект **SoftwareBitmap** на основе изображения позже. 

Способ, показанный в этом примере, позволяет получить фотографию в поток в памяти и преобразовать фотографию из потока в файл на диске. В этом примере используется метод [**GetLibraryAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Storage.StorageLibrary.GetLibraryAsync), чтобы получить библиотеку изображений пользователя, а затем свойство [**SaveFolder**](https://msdn.microsoft.com/library/windows/apps/Windows.Storage.StorageLibrary.SaveFolder), чтобы получить папку для сохранения по умолчанию. Не забывайте добавить возможность **Библиотека изображений** в манифест приложения для доступа к этой папке. [**CreateFileAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Storage.StorageFolder.CreateFileAsync) создает объект [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/Windows.Storage.StorageFile), в котором будет сохранена фотография.

Создайте объект [**InMemoryRandomAccessStream**](https://msdn.microsoft.com/library/windows/apps/Windows.Storage.Streams.InMemoryRandomAccessStream) и вызовите метод [**CapturePhotoToStreamAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture.CapturePhotoToStreamAsync), чтобы записать фотографию в поток, передав методу поток и объект [**ImageEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaProperties.ImageEncodingProperties), указывающий формат изображения. Вы можете создавать пользовательские свойства кодирования, инициализируя объект самостоятельно, но класс предоставляет статические методы, такие как [**ImageEncodingProperties.CreateJpeg**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaProperties.ImageEncodingProperties.CreateJpeg), для распространенных форматов кодирования. Затем создайте файловый поток для выходного файла, вызвав метод [**OpenAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Storage.StorageFile.OpenAsync). Создайте объект [**BitmapDecoder**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Imaging.BitmapDecoder) для декодирования изображения из потока в памяти, а затем создайте [**BitmapEncoder**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Imaging.BitmapEncoder) для кодировки изображения в файл, вызвав метод [**CreateForTranscodingAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Imaging.BitmapEncoder.CreateForTranscodingAsync).

Вы также можете создать объект [**BitmapPropertySet**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Imaging.BitmapPropertySet) и затем вызвать метод [**SetPropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/br226252.aspx) кодировщика изображения, чтобы добавить метаданные фотографии в файл изображения. Подробнее о свойствах кодирования см. в разделе [**Метаданные изображения**](image-metadata.md). Для большинства приложений важно правильно определять ориентацию приложения. Подробнее: [**Обработка ориентации устройства и экрана с помощью MediaCapture**](handle-device-orientation-with-mediacapture.md).

Наконец, вызовите метод [**FlushAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Imaging.BitmapEncoder.FlushAsync) объекта кодировщика, чтобы перекодировать фотографию из потока в памяти в файл.

[!code-cs[CaptureToFile](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetCaptureToFile)]

Дополнительные сведения о работе с файлами и папками см. в разделе [**Файлы, папки и библиотеки**](https://msdn.microsoft.com/windows/uwp/files/index).

## <a name="capture-a-video"></a>Видеозахват
Вы можете быстро реализовать возможности видеозахвата в свое приложение с помощью класса [**LowLagMediaRecording**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording). Сначала объявите переменную класса для объекта.

[!code-cs[LowLagMediaRecording](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetLowLagMediaRecording)]

Затем создайте объект **StorageFile**, в котором будет сохранено видео. Обратите внимание, что для сохранения в видеобиблиотеке пользователя, как показано в этом примере, необходимо добавить возможность **Библиотека видео** в манифест приложения. Вызовите метод [**PrepareLowLagRecordToStorageFileAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture.PrepareLowLagRecordToStorageFileAsync), чтобы инициализировать запись мультимедиа, передав файл и объект [**MediaEncodingProfile**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaProperties.MediaEncodingProfile), определяющий кодировку видео. Этот класс содержит статические методы, такие как [**CreateMp4**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaProperties.MediaEncodingProfile.CreateMp4), для создания профилей кодирования популярных форматов.

Наконец, вызовите метод [**StartAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.StartAsync), чтобы приступить к записи видео.

[!code-cs[StartVideoCapture](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetStartVideoCapture)]

Чтобы остановить запись видео, вызовите метод [**StopAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.StopAsync).

[!code-cs[StopRecording](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetStopRecording)]

Вы можете продолжить вызывать методы **StartAsync** и **StopAsync** для записи дополнительных видео. Закончив захват видео, вызовите метод [**FinishAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.FinishAsync), чтобы закрыть сеанса захвата и освободить связанные ресурсы. Затем необходимо вызвать метод **PrepareLowLagRecordToStorageFileAsync** снова, чтобы повторно инициализировать сеанс захвата перед вызовом **StartAsync**.

[!code-cs[FinishAsync](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetFinishAsync)]

При захвате видео вам нужно зарегистрировать обработчик события [**RecordLimitationExceeded**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture.RecordLimitationExceeded) объекта **MediaCapture**, которое будет вызвано операционной системой, если вы подавляете временное ограничение для одной записи (в настоящее время — это три часа). В обработчике события необходимо также завершить запись, вызвав метод [**StopAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.StopAsync).

[!code-cs[RecordLimitationExceeded](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetRecordLimitationExceeded)]

[!code-cs[RecordLimitationExceededHandler](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetRecordLimitationExceededHandler)]

### <a name="play-and-edit-captured-video-files"></a>Воспроизведение и редактирование записанных видеофайлов
Записав видео в файл, его можно загрузить и воспроизвести в пользовательском интерфейсе вашего приложения. Это можно сделать с помощью элемента управления XAML **[MediaPlayerElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement)** и связанным с ним **[MediaPlayer](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer)**. Дополнительные сведения о воспроизведения медиа на XAML-странице см. в разделе [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md).

Вы также можете создать объект **[MediaClip](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip)** из видеофайла, вызвав метод **[CreateFromFileAsync](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.createfromfileasync)**.  Объект **[MediaComposition](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition)** предоставляет основные функции для редактирования видео, например, расстановка последовательности объектов **MediaClip**, уменьшение продолжительности видео, создание слоев, добавление фоновой музыки и применение видеоэффектов. Дополнительные сведения о работе с медиакомпозициями см. в разделе [Медиакомпозиции и их редактирование](media-compositions-and-editing.md).

## <a name="pause-and-resume-video-recording"></a>Приостановка и возобновление видеозаписи
Вы можете приостановить видеозапись, а затем продолжить ее, не создавая отдельный выходной файл, вызвав метод [**PauseAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.PauseAsync), а затем — [**ResumeAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.ResumeAsync).

[!code-cs[PauseRecordingSimple](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetPauseRecordingSimple)]

[!code-cs[ResumeRecordingSimple](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetResumeRecordingSimple)]

Начиная с Windows 10 версии 1607 вы можете приостановить видеозапись и получить последний кадр, записанный до приостановки. Затем можно накладывать этот кадр в режиме предварительного просмотра камеры пользователь мог, чтобы выровнять камеру с приостановлено кадра, прежде чем возобновлять запись. Метод [**PauseWithResultAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.PauseWithResultAsync) возвращает объект [**MediaCapturePauseResult**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapturePauseResult). Свойство [**LastFrame**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapturePauseResult.LastFrame) — это объект [**VideoFrame**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.VideoFrame), представляющий последний кадр. Чтобы показать кадр в XAML, получите представление кадра в форме класса **SoftwareBitmap**. В данный момент поддерживаются только изображения в формате BGRA8 с предварительно умноженным или пустым альфа-каналом, поэтому вызовите метод [**Convert**](https://msdn.microsoft.com/library/windows/apps/Windows.Graphics.Imaging.SoftwareBitmap.Covert) при необходимости, чтобы получить правильный формат.  Создайте объект [**SoftwareBitmapSource**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Media.Imaging.SoftwareBitmapSource) и вызовите метод [**SetBitmapAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Media.Imaging.SoftwareBitmapSource.SetBitmapAsync), чтобы инициализировать его. Наконец настройте свойство **Source** XAML-элемента управления [**Image**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.Image), чтобы показать изображения. Чтобы это сработало, изображение должно быть выровнено с элементом управления **CaptureElement**, а значение непрозрачности должно быть меньше одного. Не забывайте, что изменить интерфейс можно только в потоке пользовательского интерфейса, поэтому этот вызов следует выполнить в пределах [**RunAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Core.CoreDispatcher.RunAsync).

Метод **PauseWithResultAsync** также возвращает длительность записанного в предыдущем сегменте видео, если вам нужно отслеживать общее время записи.

[!code-cs[PauseCaptureWithResult](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetPauseCaptureWithResult)]

Если вы продолжаете запись, вы можете указать источник изображения как NULL и скрыть его.

[!code-cs[ResumeCaptureWithResult](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetResumeCaptureWithResult)]

Обратите внимание, что вы также можете получить стоп-кадр после остановки видео с помощью метода [**StopWithResultAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.StopWithResultAsync).


## <a name="capture-audio"></a>Запись звука 
Вы можете быстро добавить в приложение возможности записи звука, используя способ захвата видео, описанный выше. В примере ниже создается объект **StorageFile** создается в папке данных приложения. Вызовите метод [**PrepareLowLagRecordToStorageFileAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.MediaCapture.PrepareLowLagRecordToStorageFileAsync), чтобы инициализировать сеанс захвата, передав файл и объект [**MediaEncodingProfile**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaProperties.MediaEncodingProfile), который в этом примере создается статическим методом [**CreateMp3**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaProperties.MediaEncodingProfile.CreateMp3). Чтобы начать запись, вызовите метод [**StartAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.StartAsync).

[!code-cs[StartAudioCapture](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetStartAudioCapture)]


Вызовите метод [**StopAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagPhotoSequenceCapture.StopAsync), чтобы остановить запись звука.

## <a name="related-topics"></a>Статьи по теме

* [Камера](camera.md)
[!code-cs[StopRecording](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetStopRecording)]

Вы можете вызвать методы **StartAsync** и **StopAsync** несколько раз, чтобы записать несколько звуковых файлов. Закончив захват звука, вызовите метод [**FinishAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.LowLagMediaRecording.FinishAsync), чтобы закрыть сеанса захвата и освободить связанные ресурсы. Затем необходимо вызвать метод **PrepareLowLagRecordToStorageFileAsync** снова, чтобы повторно инициализировать сеанс захвата перед вызовом **StartAsync**.

[!code-cs[FinishAsync](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetFinishAsync)]


## <a name="detect-and-respond-to-audio-level-changes-by-the-system"></a>Обнаружение изменения громкости звука системой и реагирование на это событие
Начиная с Windows 10 версии 1803, ваше приложение может обнаружить, что система уменьшила громкость звука или отключило звук для потоков записи и обработки вашего приложения. Например, система может отключить звук в потоках вашего приложения, при переводе последнего в фоновый режим. В классе [**AudioStateMonitor**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor) можно зарегистрировать обработчик события, отправляемого когда система изменяет громкость звукового потока. Получите экземпляр **AudioStateMonitor** для отслеживания потоков записи звука, вызвав метод [**CreateForCaptureMonitoring**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor.createforcapturemonitoring#Windows_Media_Audio_AudioStateMonitor_CreateForCaptureMonitoring). Получите экземпляр для наблюдения за потоками обработки звуковых данных, вызвав метод [**CreateForRenderMonitoring**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor.createforrendermonitoring). Зарегистрируйте обработчик для события [**SoundLevelChanged**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor.soundlevelchanged) каждого средства наблюдения, чтобы получать уведомления про изменение системой громкости звука для потоков соответствующей категории.

[!code-cs[AudioStateMonitorUsing](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetAudioStateMonitorUsing)]

[!code-cs[AudioStateVars](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetAudioStateVars)]

[!code-cs[RegisterAudioStateMonitor](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetRegisterAudioStateMonitor)]

В обработчике **SoundLevelChanged** для потока записи вы можете проверить свойство [**SoundLevel**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor.soundlevel) отправителя **AudioStateMonitor**, и определить новую уровень громкости. Обратите внимание, что система никогда не должна уменьшать уровень громкости потока записи. Он должен либо отключаться совсем либо включаться обратно с полной громкостью. Если звуковой поток выключен, вы можете остановить процесс идущей записи. Если громкость звукового потока восстановлена до полного уровня, вы можете возобновить запись. В следующем примере в нескольких переменных класса Boolean отслеживается процесс записи звука приложением и была ли запись остановлена из-за изменения громкости звука. С помощью этих переменных можно определить, когда стоит программно запустить или остановить аудиозапись.

[!code-cs[CaptureSoundLevelChanged](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetCaptureSoundLevelChanged)]

В следующем примере кода показана реализация обработчика **SoundLevelChanged** для вывода звука. В зависимости от вашего сценария приложения и типа воспроизводимого содержимого, возможно, потребуется приостановить воспроизведение при выключении звука. Дополнительные сведения о том, как реагировать на изменение громкости звука при воспроизведении мультимедиа см. в разделе [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md).

[!code-cs[RenderSoundLevelChanged](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetRenderSoundLevelChanged)]


* [Запись фотографий и видео с помощью встроенного пользовательского интерфейса Windows для веб-камер](capture-photos-and-video-with-cameracaptureui.md)
* [Обработка ориентации устройства с помощью MediaCapture](handle-device-orientation-with-mediacapture.md)
* [Создание, редактирование и сохраните точечных рисунков](imaging.md)
* [Файлы, папки и библиотеки](https://msdn.microsoft.com/windows/uwp/files/index)

