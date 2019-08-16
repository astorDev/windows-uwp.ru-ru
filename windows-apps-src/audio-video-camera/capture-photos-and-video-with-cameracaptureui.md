---
ms.assetid: CC0D6E9B-128D-488B-912F-318F5EE2B8D3
description: В этой статье описывается, как использовать класс Камеракаптуреуи для записи фотографий или видео с помощью пользовательского интерфейса камеры, встроенного в Windows.
title: Запись фотографий и видео с помощью встроенного пользовательского интерфейса камеры Windows
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d582d4815b4fb2168b187a1efff3795cc98aca02
ms.sourcegitcommit: 99595e4938213aafdb49635d684d8ba8eb3f697a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487806"
---
# <a name="capture-photos-and-video-with-the-windows-built-in-camera-ui"></a>Запись фотографий и видео с помощью встроенного пользовательского интерфейса камеры Windows



В этой статье описывается, как использовать класс Камеракаптуреуи для записи фотографий или видео с помощью пользовательского интерфейса камеры, встроенного в Windows. Эта функция проста в использовании. Это позволяет приложению получить фотографию или видео, захваченные пользователем, с помощью всего нескольких строк кода.

Если вы хотите предоставить собственный пользовательский интерфейс камеры или вам требуется более надежное низкоуровневое управление операцией захвата, необходимо использовать объект [**MediaCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) и внедрить собственный способ захвата. Подробнее см. в статье [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md).

> [!NOTE]
> Не следует указывать возможности **веб-камеры** или микрофона в файле манифеста приложения, если приложение использует только камеракаптуреуи. В этом случае приложение будет отображаться в настройках конфиденциальности камеры устройства, но даже если пользователь отклоняет доступ к камере к приложению, это не помешает Камеракаптуреуи записывать носитель. <p>Это обусловлено тем, что встроенное приложение камеры Windows является доверенным основным приложением, которое требует, чтобы пользователь инициировал захват фото, звука и видео нажатием кнопки. Приложение может завершиться с ошибкой сертификации комплекта сертификации приложений Windows при отправке в Microsoft Store, если вы указали возможности веб-камеры или микрофона при использовании Камеракаптуреуи в качестве единственного механизма записи фотографий.<p>
Если вы используете Медиакаптуре для записи звука, фотографий или видео программными средствами, необходимо указать возможности веб-камеры или микрофона в файле манифеста приложения.

## <a name="capture-a-photo-with-cameracaptureui"></a>Захват фотографий с помощью CameraCaptureUI

Чтобы применять пользовательский интерфейс захвата с камеры, добавьте в свой проект пространство имен [**Windows.Media.Capture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture). Чтобы выполнять файловые операции с возвращенными файлами изображений, включите в проект пространство имен [**Windows.Storage**](https://docs.microsoft.com/uwp/api/Windows.Storage).

[!code-cs[UsingCaptureUI](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingCaptureUI)]

Чтобы захватить фотографию, создайте новый объект [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI). С помощью свойства « [**Фотонастройка**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.photosettings) » объекта можно указать свойства для возвращаемой фотографии, например формат изображения фотографии. По умолчанию пользовательский интерфейс записи камеры поддерживает Кадрирование фотографии перед возвратом. Это можно отключить с помощью свойства [**алловкроппинг**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureuiphotocapturesettings.allowcropping) . В этом примере свойство [**CroppedSizeInPixels**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureuiphotocapturesettings.croppedsizeinpixels) используется для возвращения изображений с разрешением 200 x 200 пикселей.

> [!NOTE]
> Обрезка изображения в **камеракаптуреуи** не поддерживается для устройств в семействе мобильных устройств. Значение свойства [**AllowCropping**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureuiphotocapturesettings.allowcropping) игнорируется, если приложение выполняется на этих устройствах.

Вызовите метод [**CaptureFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.capturefileasync) и задайте [**CameraCaptureUIMode.Photo**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUIMode), чтобы захватить фотографию. Если захват выполнен успешно, этот метод возвращает экземпляр [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), содержащий изображение. Если пользователь отменил захват, возвращается объект null.

[!code-cs[CapturePhoto](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetCapturePhoto)]

Файл **StorageFile**, содержащий фотографию, получает динамически генерируемое имя и сохраняется в локальной папке приложения. Чтобы лучше организовать захваченные фотографии, можно переместить файл в другую папку.

[!code-cs[CopyAndDeletePhoto](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetCopyAndDeletePhoto)]

Чтобы использовать фотографию в приложении, можно создать объект [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap), который можно использовать с различными функциями универсального приложения для Windows.

Сначала включите в проект пространство имен [**Windows. Graphics. Imaging**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging) .

[!code-cs[UsingSoftwareBitmap](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingSoftwareBitmap)]

Вызовите метод [**OpenAsync**](https://docs.microsoft.com/uwp/api/windows.storage.istoragefile.openasync) для доступа к потоку файла изображения. Вызовите [**BitmapDecoder.CreateAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapdecoder.createasync), чтобы получить доступ к декодеру точечных рисунков для потока. Затем вызовите [**жетсофтваребитмап**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapdecoder.getsoftwarebitmapasync) , чтобы получить **софтваребитмапное** представление изображения.

[!code-cs[SoftwareBitmap](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetSoftwareBitmap)]

Чтобы показать изображение в пользовательском интерфейсе, объявите элемент управления [**Image**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image) на странице XAML.

[!code-xml[ImageControl](./code/CameraCaptureUIWin10/cs/MainPage.xaml#SnippetImageControl)]

Чтобы использовать точечный рисунок программного обеспечения на странице XAML, добавьте в свой проект пространство имен [**Windows.UI.Xaml.Media.Imaging**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging) using.

[!code-cs[UsingSoftwareBitmapSource](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingSoftwareBitmapSource)]

Элемент управления " **изображение** " требует, чтобы источник изображения был в формате BGRA8 с предварительно умноженным альфа-каналом или без альфа-канала. Вызовите статический метод [**софтваребитмап. Convert**](/uwp/api/windows.graphics.imaging.softwarebitmap.convert) для создания нового точечного рисунка программного обеспечения с нужным форматом. Затем создайте новый объект [**софтваребитмапсаурце**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.SoftwareBitmapSource) и вызовите его [**сетбитмапасинк**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource.setbitmapasync) , чтобы назначить исходный рисунок программного обеспечения источнику. После этого задайте свойство [**Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image.source) элемента управления **Image**, чтобы отобразить захваченную фотографию в пользовательском интерфейсе.

[!code-cs[SetImageSource](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetSetImageSource)]

## <a name="capture-a-video-with-cameracaptureui"></a>Видеозахват с помощью CameraCaptureUI

Чтобы захватить видео, создайте новый объект [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI). С помощью свойства [**видеосеттингс**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.videosettings) объекта можно указать свойства для возвращаемого видео, например формат видео.

Вызовите [**каптурефилеасинк**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.capturefileasync) и укажите [**видео**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.videosettings) , чтобы записать видео. Если захват выполнен успешно, этот метод возвращает экземпляр [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), содержащий видео. Если отменить захват, возвращаемый объект будет иметь значение null.

[!code-cs[CaptureVideo](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetCaptureVideo)]

Доступные действия с захваченным видеофайлом зависят от сценария вашего приложения. Далее в этой статье объясняется, как быстро создать композицию мультимедиа из одного или нескольких захваченных видео и отобразить ее в пользовательском интерфейсе.

Сначала добавьте элемент управления [**медиаплайерелемент**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement) , в котором композиция видео будет отображаться на странице XAML.

[!code-xml[MediaElement](./code/CameraCaptureUIWin10/cs/MainPage.xaml#SnippetMediaElement)]


Когда видеофайл возвращается из пользовательского интерфейса записи камеры, создайте новый [**медиасаурце**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource) , вызвав **[креатефромсторажефиле](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.createfromstoragefile)** . Вызовите метод **[Play](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.Play)** метод для **[MediaPlayer](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer)** по умолчанию, связанного с **MediaPlayerElement**, для воспроизведения видео.

[!code-cs[PlayVideo](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetPlayVideo)]
 

## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Базовая фотография, видео и запись звука с помощью Медиакаптуре](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [**камеракаптуреуи**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI) 
 

 




