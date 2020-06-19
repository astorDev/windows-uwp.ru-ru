---
ms.assetid: CC0D6E9B-128D-488B-912F-318F5EE2B8D3
description: В этой статье описывается, как использовать класс [**камеракаптуреуи**](/uwp/api/windows.media.capture.cameracaptureui) для записи фотографий или видео с помощью пользовательского интерфейса камеры, встроенного в Windows.
title: Запись фотографий и видео с помощью встроенного пользовательского интерфейса камеры Windows
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
ms.openlocfilehash: a512f72c01f2082dd067fc867f7434c92d2aa0c8
ms.sourcegitcommit: 79e4b3a9c53060b64513e2e240f0a4f073cc5dab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84978939"
---
# <a name="capture-photos-and-video-with-the-windows-built-in-camera-ui"></a>Запись фотографий и видео с помощью встроенного пользовательского интерфейса камеры Windows

В этой статье описывается, как использовать класс [**камеракаптуреуи**](/uwp/api/windows.media.capture.cameracaptureui) для записи фотографий или видео с помощью пользовательского интерфейса камеры, встроенного в Windows. Эта функция проста в использовании. Это позволяет приложению получить фотографию или видео, захваченные пользователем, с помощью всего нескольких строк кода.

Если вы хотите предоставить собственный пользовательский интерфейс камеры или ваш сценарий требует более надежного, низкоуровневого управления операцией записи, следует использовать класс [**медиакаптуре**](/uwp/api/Windows.Media.Capture.MediaCapture) и реализовать собственный интерфейс записи. Подробнее см. в статье [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md).

> [!NOTE]
> Не следует указывать возможности **веб-камеры** и **микрофона** в файле манифеста приложения, если приложение использует только **камеракаптуреуи**. В этом случае приложение будет отображаться в настройках конфиденциальности камеры устройства, но даже если пользователь отклоняет доступ к камере к приложению, это не помешает **камеракаптуреуи** записывать носитель. <p>Это обусловлено тем, что встроенное приложение камеры Windows является доверенным основным приложением, которое требует, чтобы пользователь инициировал захват фото, звука и видео нажатием кнопки. Приложение может завершиться с ошибкой сертификации комплекта сертификации приложений Windows при отправке в Microsoft Store, если вы указали возможности веб-камеры или микрофона при использовании **камеракаптуреуи** в качестве единственного механизма записи фотографий.<p>
Если вы используете **медиакаптуре** для записи звука, фотографий или видео программными средствами, необходимо указать возможности **веб-камеры** или **микрофона** в файле манифеста приложения.

## <a name="capture-a-photo-with-cameracaptureui"></a>Захват фотографий с помощью CameraCaptureUI

Чтобы применять пользовательский интерфейс захвата с камеры, добавьте в свой проект пространство имен [**Windows.Media.Capture**](/uwp/api/Windows.Media.Capture). Чтобы выполнять файловые операции с возвращенными файлами изображений, включите в проект пространство имен [**Windows.Storage**](/uwp/api/Windows.Storage).

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetUsingCaptureUI":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.h" id="SnippetUsingCaptureUI":::

Чтобы захватить фотографию, создайте новый объект [**CameraCaptureUI**](/uwp/api/Windows.Media.Capture.CameraCaptureUI). С помощью свойства « [**Фотонастройка**](/uwp/api/windows.media.capture.cameracaptureui.photosettings) » объекта можно указать свойства для возвращаемой фотографии, например формат изображения фотографии. По умолчанию пользовательский интерфейс записи камеры поддерживает Кадрирование фотографии перед возвратом. Это можно отключить с помощью свойства [**алловкроппинг**](/uwp/api/windows.media.capture.cameracaptureuiphotocapturesettings.allowcropping) . В этом примере свойство [**CroppedSizeInPixels**](/uwp/api/windows.media.capture.cameracaptureuiphotocapturesettings.croppedsizeinpixels) используется для возвращения изображений с разрешением 200 x 200 пикселей.

> [!NOTE]
> Обрезка изображения в **камеракаптуреуи** не поддерживается для устройств в семействе мобильных устройств. Значение свойства [**AllowCropping**](/uwp/api/windows.media.capture.cameracaptureuiphotocapturesettings.allowcropping) игнорируется, если приложение выполняется на этих устройствах.

Вызовите метод [**CaptureFileAsync**](/uwp/api/windows.media.capture.cameracaptureui.capturefileasync) и задайте [**CameraCaptureUIMode.Photo**](/uwp/api/Windows.Media.Capture.CameraCaptureUIMode), чтобы захватить фотографию. Если захват выполнен успешно, этот метод возвращает экземпляр [**StorageFile**](/uwp/api/Windows.Storage.StorageFile), содержащий изображение. Если пользователь отменил захват, возвращается объект null.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetCapturePhoto":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.cpp" id="SnippetCapturePhoto":::

Файл **StorageFile**, содержащий фотографию, получает динамически генерируемое имя и сохраняется в локальной папке приложения. Чтобы лучше организовать захваченные фотографии, можно переместить файл в другую папку.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetCopyAndDeletePhoto":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.cpp" id="SnippetCopyAndDeletePhoto":::

Чтобы использовать фотографию в приложении, можно создать объект [**SoftwareBitmap**](/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap), который можно использовать с различными функциями универсального приложения для Windows.

Сначала включите в проект пространство имен [**Windows. Graphics. Imaging**](/uwp/api/Windows.Graphics.Imaging) .

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetUsingSoftwareBitmap":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.h" id="SnippetUsingSoftwareBitmap":::

Вызовите метод [**OpenAsync**](/uwp/api/windows.storage.istoragefile.openasync) для доступа к потоку файла изображения. Вызовите [**BitmapDecoder.CreateAsync**](/uwp/api/windows.graphics.imaging.bitmapdecoder.createasync), чтобы получить доступ к декодеру точечных рисунков для потока. Затем вызовите [**жетсофтваребитмап**](/uwp/api/windows.graphics.imaging.bitmapdecoder.getsoftwarebitmapasync) , чтобы получить **софтваребитмапное** представление изображения.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetSoftwareBitmap":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.cpp" id="SnippetSoftwareBitmap":::

Чтобы показать изображение в пользовательском интерфейсе, объявите элемент управления [**Image**](/uwp/api/Windows.UI.Xaml.Controls.Image) на странице XAML.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetImageControl":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.cpp" id="SnippetImageControl":::

Чтобы использовать точечный рисунок программного обеспечения на странице XAML, добавьте в свой проект пространство имен [**Windows.UI.Xaml.Media.Imaging**](/uwp/api/Windows.UI.Xaml.Media.Imaging) using.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetUsingSoftwareBitmapSource":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.h" id="SnippetUsingSoftwareBitmapSource":::

Элемент управления " **изображение** " требует, чтобы источник изображения был в формате BGRA8 с предварительно умноженным альфа-каналом или без альфа-канала. Вызовите статический метод [**софтваребитмап. Convert**](/uwp/api/windows.graphics.imaging.softwarebitmap.convert) для создания нового точечного рисунка программного обеспечения с нужным форматом. Затем создайте новый объект [**софтваребитмапсаурце**](/uwp/api/Windows.UI.Xaml.Media.Imaging.SoftwareBitmapSource) и вызовите его [**сетбитмапасинк**](/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource.setbitmapasync) , чтобы назначить исходный рисунок программного обеспечения источнику. После этого задайте свойство [**Source**](/uwp/api/windows.ui.xaml.controls.image.source) элемента управления **Image**, чтобы отобразить захваченную фотографию в пользовательском интерфейсе.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetSetImageSource":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.cpp" id="SnippetSetImageSource":::

## <a name="capture-a-video-with-cameracaptureui"></a>Видеозахват с помощью CameraCaptureUI

Чтобы захватить видео, создайте новый объект [**CameraCaptureUI**](/uwp/api/Windows.Media.Capture.CameraCaptureUI). С помощью свойства [**видеосеттингс**](/uwp/api/windows.media.capture.cameracaptureui.videosettings) объекта можно указать свойства для возвращаемого видео, например формат видео.

Вызовите [**каптурефилеасинк**](/uwp/api/windows.media.capture.cameracaptureui.capturefileasync) и укажите [**видео**](/uwp/api/windows.media.capture.cameracaptureui.videosettings) , чтобы записать видео. Если захват выполнен успешно, этот метод возвращает экземпляр [**StorageFile**](/uwp/api/Windows.Storage.StorageFile), содержащий видео. Если отменить захват, возвращаемый объект будет иметь значение null.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetCaptureVideo":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.cpp" id="SnippetCaptureVideo":::

Доступные действия с захваченным видеофайлом зависят от сценария вашего приложения. Далее в этой статье объясняется, как быстро создать композицию мультимедиа из одного или нескольких захваченных видео и отобразить ее в пользовательском интерфейсе.

Сначала добавьте элемент управления [**медиаплайерелемент**](/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement) , в котором композиция видео будет отображаться на странице XAML.

:::code language="xml" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml" id="SnippetMediaElement":::

Когда видеофайл возвращается из пользовательского интерфейса записи камеры, создайте новый [**медиасаурце**](/uwp/api/windows.media.core.mediasource) , вызвав **[креатефромсторажефиле](/uwp/api/windows.media.core.mediasource.createfromstoragefile)**. Вызовите метод **[Play](/uwp/api/windows.media.playback.mediaplayer.Play)** метод для **[MediaPlayer](/uwp/api/windows.media.playback.mediaplayer)** по умолчанию, связанного с **MediaPlayerElement**, для воспроизведения видео.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cs/MainPage.xaml.cs" id="SnippetPlayVideo":::
:::code language="cppwinrt" source="~/../snippets-windows/windows-uwp/audio-video-camera/CameraCaptureUIWin10/cppwinrt/MainPage.cpp" id="SnippetPlayVideo":::

## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [CameraCaptureUI](/uwp/api/Windows.Media.Capture.CameraCaptureUI)
