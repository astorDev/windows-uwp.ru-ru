---
ms.assetid: CC0D6E9B-128D-488B-912F-318F5EE2B8D3
description: В этой статье описано, как использовать класс CameraCaptureUI, чтобы фотографировать и снимать видео с помощью пользовательского интерфейса камеры, встроенного в Windows.
title: Фото- и видеосъемка с использованием встроенного пользовательского интерфейса камеры в Windows
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f23d80b4d2796b4d9c86648c09d6bece5e82d482
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67317834"
---
# <a name="capture-photos-and-video-with-windows-built-in-camera-ui"></a>Фото- и видеосъемка с использованием встроенного пользовательского интерфейса камеры в Windows



В этой статье описано, как использовать класс CameraCaptureUI, чтобы фотографировать и снимать видео с помощью пользовательского интерфейса камеры, встроенного в Windows. Эта функция проста в использовании и позволяет приложению получать захваченные пользователем фотографии и видео с помощью всего нескольких строк кода.

Если вы хотите предоставить собственный пользовательский интерфейс камеры или вам требуется более надежное низкоуровневое управление операцией захвата, необходимо использовать объект [**MediaCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) и внедрить собственный способ захвата. Подробнее см. в статье [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md).

> [!NOTE]
> Не следует указывать **веб-камера** или **"микрофон"** возможности в приложении файл манифеста, если приложение использует только CameraCaptureUI. Если вы это сделаете, ваше приложение будет отображаться в параметрах конфиденциальности камеры устройства, но даже если пользователь запретит приложению доступ к камере, это не помешает CameraCaptureUI захватывать мультимедиа. Это обусловлено тем, что встроенное приложение камеры Windows является доверенным основным приложением, которое требует, чтобы пользователь инициировал захват фото, звука и видео нажатием кнопки. Приложение может завершиться ошибкой сертификации комплект сертификации приложений Windows, при отправке в Store, при указании возможности веб-камеры или "микрофон", при использовании CameraCaptureUI в качестве механизма отслеживания только фотографии.
> Вы должны указать в файле манифеста вашего приложения возможность использования веб-камеры или микрофона, если для программного захвата звука, фото или видео используется MediaCapture.

## <a name="capture-a-photo-with-cameracaptureui"></a>Захват фотографий с помощью CameraCaptureUI

Чтобы применять пользовательский интерфейс захвата с камеры, добавьте в свой проект пространство имен [**Windows.Media.Capture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture). Чтобы выполнять файловые операции с возвращенными файлами изображений, включите в проект пространство имен [**Windows.Storage**](https://docs.microsoft.com/uwp/api/Windows.Storage).

[!code-cs[UsingCaptureUI](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingCaptureUI)]

Чтобы захватить фотографию, создайте новый объект [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI). С помощью свойства [**PhotoSettings**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.photosettings) объекта можно указать параметры возвращенной фотографии, например ее формат. По умолчанию пользовательский интерфейс захвата с камеры позволяет обрезать фотографии перед их возвращением. Эту функцию можно отключить с помощью свойства [**AllowCropping**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureuiphotocapturesettings.allowcropping). В этом примере свойство [**CroppedSizeInPixels**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureuiphotocapturesettings.croppedsizeinpixels) используется для возвращения изображений с разрешением 200 x 200 пикселей.

> [!NOTE]
> Обрезка изображения в **CameraCaptureUI** не поддерживается для устройств из семейства мобильных устройств. Значение свойства [**AllowCropping**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureuiphotocapturesettings.allowcropping) игнорируется, если приложение выполняется на этих устройствах.

Вызовите метод [**CaptureFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.capturefileasync) и задайте [**CameraCaptureUIMode.Photo**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUIMode), чтобы захватить фотографию. Если захват выполнен успешно, этот метод возвращает экземпляр [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), содержащий изображение. Если пользователь отменил захват, возвращается объект null.

[!code-cs[CapturePhoto](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetCapturePhoto)]

Файл **StorageFile**, содержащий фотографию, получает динамически генерируемое имя и сохраняется в локальной папке приложения. Чтобы упорядочить снятые фотографии, их можно переместить файл в другой папке.

[!code-cs[CopyAndDeletePhoto](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetCopyAndDeletePhoto)]

Чтобы использовать фотографию в приложении, можно создать объект [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap), который можно использовать с различными функциями универсального приложения для Windows.

Сначала необходимо добавить в проект пространство имен [**Windows.Graphics.Imaging**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging).

[!code-cs[UsingSoftwareBitmap](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingSoftwareBitmap)]

Вызовите метод [**OpenAsync**](https://docs.microsoft.com/uwp/api/windows.storage.istoragefile.openasync) для доступа к потоку файла изображения. Вызовите [**BitmapDecoder.CreateAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapdecoder.createasync), чтобы получить доступ к декодеру точечных рисунков для потока. Затем вызовите метод [**GetSoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapdecoder.getsoftwarebitmapasync), чтобы получить доступ к изображению в виде **SoftwareBitmap**.

[!code-cs[SoftwareBitmap](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetSoftwareBitmap)]

Чтобы показать изображение в пользовательском интерфейсе, объявите элемент управления [**Image**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image) на странице XAML.

[!code-xml[ImageControl](./code/CameraCaptureUIWin10/cs/MainPage.xaml#SnippetImageControl)]

Чтобы использовать точечный рисунок программного обеспечения на странице XAML, добавьте в свой проект пространство имен [**Windows.UI.Xaml.Media.Imaging**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging) using.

[!code-cs[UsingSoftwareBitmapSource](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingSoftwareBitmapSource)]

Элементу управления **Image** требуется, чтобы источник изображения был в формате BGRA8 с предварительным умножением альфа-канала или без него, поэтому вызовите статистический метод [**SoftwareBitmap.Convert**](/uwp/api/windows.graphics.imaging.softwarebitmap.convert) для создания нового точечного рисунка программного обеспечения в требуемом формате. Затем создайте новый объект [**SoftwareBitmapSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.SoftwareBitmapSource) и вызовите метод [**SetBitmapAsync**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource.setbitmapasync), чтобы присвоить источнику точечный рисунок программного обеспечения. После этого задайте свойство [**Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image.source) элемента управления **Image**, чтобы отобразить захваченную фотографию в пользовательском интерфейсе.

[!code-cs[SetImageSource](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetSetImageSource)]

## <a name="capture-a-video-with-cameracaptureui"></a>Видеозахват с помощью CameraCaptureUI

Чтобы захватить видео, создайте новый объект [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI). С помощью свойства [**VideoSettings**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.videosettings) объекта можно указать параметры возвращенного видео, например его формат.

Вызовите метод [**CaptureFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.capturefileasync) и задайте [**Video**](https://docs.microsoft.com/uwp/api/windows.media.capture.cameracaptureui.videosettings), чтобы осуществить видеозахват. Если захват выполнен успешно, этот метод возвращает экземпляр [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), содержащий видео. Если пользователь отменил захват, возвращается объект null.

[!code-cs[CaptureVideo](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetCaptureVideo)]

Доступные действия с захваченным видеофайлом зависят от сценария вашего приложения. Далее в этой статье объясняется, как быстро создать композицию мультимедиа из одного или нескольких захваченных видео и отобразить ее в пользовательском интерфейсе.

Сначала добавьте элемент управления [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement), в котором композиция видеороликов будет отображаться на вашей странице XAML.

[!code-xml[MediaElement](./code/CameraCaptureUIWin10/cs/MainPage.xaml#SnippetMediaElement)]


Создайте объект [**MediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource) с помощью видеофайла, возвращенного пользовательским интерфейсом захвата с камеры, вызвав метод **[CreateFromStorageFile](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.createfromstoragefile)** . Вызовите метод **[Play](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.Play)** метод для **[MediaPlayer](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer)** по умолчанию, связанного с **MediaPlayerElement**, для воспроизведения видео.

[!code-cs[PlayVideo](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetPlayVideo)]
 

## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Основные фото, видео и аудио захвата с MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [**CameraCaptureUI**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CameraCaptureUI) 
 

 




