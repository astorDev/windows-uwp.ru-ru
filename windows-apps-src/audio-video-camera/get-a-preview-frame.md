---
ms.assetid: 05E418B4-5A62-42BD-BF66-A0762216D033
description: В этом разделе объясняется, как получить один кадр предварительного просмотра из потока предварительного просмотра захвата мультимедийного содержимого.
title: Получение кадра предварительного просмотра
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9963955649b98f226fbb81871b2ac391035ba41a
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66360886"
---
# <a name="get-a-preview-frame"></a>Получение кадра предварительного просмотра


В этом разделе объясняется, как получить один кадр предварительного просмотра из потока предварительного просмотра захвата мультимедийного содержимого.

> [!NOTE] 
> В этой статье используются понятия и код из статьи [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье рассчитан на то, что ваше приложение уже содержит экземпляр MediaCapture, который был инициализирован надлежащим образом, и что у вас есть объект [**CaptureElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CaptureElement) с активным видеопотоком предварительного просмотра.

Помимо пространства имен, необходимого для основного захвата мультимедиа, для захвата кадра предварительного просмотра требуется следующее пространство имен.

[!code-cs[PreviewFrameUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPreviewFrameUsing)]

При запросе кадра предварительного просмотра вы можете указать формат, в котором требуется получить кадр. Для этого создайте объект [**VideoFrame**](https://docs.microsoft.com/uwp/api/Windows.Media.VideoFrame) в соответствующем формате. В этом примере будет создан видеокадр с тем же разрешением, что и у потока предварительного просмотра. В ходе него вызывается метод [**VideoDeviceController.GetMediaStreamProperties**](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.getmediastreamproperties) и указывается тип [**MediaStreamType.VideoPreview**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaStreamType) для запроса свойств для потока предварительного просмотра. Ширина и высота потока предварительного просмотра используются для создания видеокадра.

[!code-cs[CreateFormatFrame](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateFormatFrame)]

Если ваш объект [**MediaCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) инициализирован и имеется активный поток предварительного просмотра, вызовите метод [**GetPreviewFrameAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.getpreviewframeasync), чтобы получить доступ к потоку предварительного просмотра. В качестве параметров задайте видеокадр, созданный на последнем шаге, чтобы указать формат возвращенного кадра.

[!code-cs[GetPreviewFrameAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetPreviewFrameAsync)]

Получите кадр предварительного просмотра в представлении [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap), обратившись к свойству [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.media.videoframe.softwarebitmap) объекта [**VideoFrame**](https://docs.microsoft.com/uwp/api/Windows.Media.VideoFrame). Сведения о сохранении, загрузке и изменении точечных рисунков программного обеспечения см. в разделе [Обработка изображений](imaging.md).

[!code-cs[GetPreviewBitmap](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetPreviewBitmap)]

Вы также можете получить доступ к кадру предварительного просмотра в представлении [**IDirect3DSurface**](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11.IDirect3DSurface), чтобы использовать изображение с API Direct3D.

[!code-cs[GetPreviewSurface](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetPreviewSurface)]

> [!IMPORTANT]
> Свойство [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.media.videoframe.softwarebitmap) или свойство [**Direct3DSurface**](https://docs.microsoft.com/uwp/api/windows.media.videoframe.direct3dsurface) возвращенного объекта **VideoFrame** может иметь нулевое значение в зависимости от того, как был вызван метод **GetPreviewFrameAsync**, а также в зависимости от устройства, на котором выполняется приложение.

> - Если вызвать перегрузку метода [**GetPreviewFrameAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.getpreviewframeasync), которая принимает аргумент **VideoFrame**, то возвращенный объект **VideoFrame** будет иметь значение **SoftwareBitmap**, отличное от нулевого, а свойство **Direct3DSurface** будет иметь нулевое значение.
> - Если вызвать перегрузку [**GetPreviewFrameAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.getpreviewframeasync), которое не располагает аргументами на устройстве, использующем поверхность Direct3D для представления кадра внутри себя, свойство **Direct3DSurface** будет иметь значение, отличное от нулевого, а свойство **SoftwareBitmap** будет иметь нулевое значение.
> - Если вызвать перегрузку [**GetPreviewFrameAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.getpreviewframeasync), которое не располагает аргументами на устройстве, не использующем поверхность Direct3D для представления кадра внутри себя, свойство **SoftwareBitmap** будет иметь значение, отличное от нулевого, а свойство **Direct3DSurface** будет иметь нулевое значение.

Ваше приложение должно всегда проверять нулевое значение перед попыткой работы с объектами, возвращенными свойством **SoftwareBitmap** или **Direct3DSurface**.

Поработав с кадром предварительного просмотра, не забудьте вызвать его метод [**Close**](https://docs.microsoft.com/uwp/api/windows.media.videoframe.close) (сопоставляемый с Dispose в C#), чтобы освободить ресурсы, используемые кадром. Можно также использовать шаблон **using**, который автоматически ликвидирует объект.

[!code-cs[CleanUpPreviewFrame](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpPreviewFrame)]

## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Основные фото, видео и аудио захвата с MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
 

 




