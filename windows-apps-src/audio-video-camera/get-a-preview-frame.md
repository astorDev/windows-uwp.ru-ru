---
author: drewbatgit
ms.assetid: 05E418B4-5A62-42BD-BF66-A0762216D033
description: В этом разделе объясняется, как получить один кадр предварительного просмотра из потока предварительного просмотра захвата мультимедийного содержимого.
title: Получение кадра предварительного просмотра
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 211bd4ce660726030f8b90d29c4ea4d8a14564de
ms.sourcegitcommit: 086001cffaf436e6e4324761d59bcc5e598c15ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5685527"
---
# <a name="get-a-preview-frame"></a>Получение кадра предварительного просмотра


В этом разделе объясняется, как получить один кадр предварительного просмотра из потока предварительного просмотра захвата мультимедийного содержимого.

> [!NOTE] 
> В этой статье используются понятия и код из статьи [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье рассчитан на то, что ваше приложение уже содержит экземпляр MediaCapture, который был инициализирован надлежащим образом, и что у вас есть объект [**CaptureElement**](https://msdn.microsoft.com/library/windows/apps/br209278) с активным видеопотоком предварительного просмотра.

Помимо пространства имен, необходимого для основного захвата мультимедиа, для захвата кадра предварительного просмотра требуется следующее пространство имен.

[!code-cs[PreviewFrameUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPreviewFrameUsing)]

При запросе кадра предварительного просмотра вы можете указать формат, в котором требуется получить кадр. Для этого создайте объект [**VideoFrame**](https://msdn.microsoft.com/library/windows/apps/dn930917) в соответствующем формате. В этом примере будет создан видеокадр с тем же разрешением, что и у потока предварительного просмотра. В ходе него вызывается метод [**VideoDeviceController.GetMediaStreamProperties**](https://msdn.microsoft.com/library/windows/apps/br211995) и указывается тип [**MediaStreamType.VideoPreview**](https://msdn.microsoft.com/library/windows/apps/br226640) для запроса свойств для потока предварительного просмотра. Ширина и высота потока предварительного просмотра используются для создания видеокадра.

[!code-cs[CreateFormatFrame](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateFormatFrame)]

Если ваш объект [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/br241124) инициализирован и имеется активный поток предварительного просмотра, вызовите метод [**GetPreviewFrameAsync**](https://msdn.microsoft.com/library/windows/apps/dn926711), чтобы получить доступ к потоку предварительного просмотра. В качестве параметров задайте видеокадр, созданный на последнем шаге, чтобы указать формат возвращенного кадра.

[!code-cs[GetPreviewFrameAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetPreviewFrameAsync)]

Получите кадр предварительного просмотра в представлении [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887358), обратившись к свойству [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn930926) объекта [**VideoFrame**](https://msdn.microsoft.com/library/windows/apps/dn930917). Сведения о сохранении, загрузке и изменении точечных рисунков программного обеспечения см. в разделе [Обработка изображений](imaging.md).

[!code-cs[GetPreviewBitmap](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetPreviewBitmap)]

Вы также можете получить доступ к кадру предварительного просмотра в представлении [**IDirect3DSurface**](https://msdn.microsoft.com/library/windows/apps/dn965505), чтобы использовать изображение с API Direct3D.

[!code-cs[GetPreviewSurface](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetPreviewSurface)]

> [!IMPORTANT]
> Свойство [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn930926) или свойство [**Direct3DSurface**](https://msdn.microsoft.com/library/windows/apps/dn930920) возвращенного объекта **VideoFrame** может иметь нулевое значение в зависимости от того, как был вызван метод **GetPreviewFrameAsync**, а также в зависимости от устройства, на котором выполняется приложение.

> - Если вызвать перегрузку метода [**GetPreviewFrameAsync**](https://msdn.microsoft.com/library/windows/apps/dn926713), которая принимает аргумент **VideoFrame**, то возвращенный объект **VideoFrame** будет иметь значение **SoftwareBitmap**, отличное от нулевого, а свойство **Direct3DSurface** будет иметь нулевое значение.
> - Если вызвать перегрузку [**GetPreviewFrameAsync**](https://msdn.microsoft.com/library/windows/apps/dn926712), которое не располагает аргументами на устройстве, использующем поверхность Direct3D для представления кадра внутри себя, свойство **Direct3DSurface** будет иметь значение, отличное от нулевого, а свойство **SoftwareBitmap** будет иметь нулевое значение.
> - Если вызвать перегрузку [**GetPreviewFrameAsync**](https://msdn.microsoft.com/library/windows/apps/dn926712), которое не располагает аргументами на устройстве, не использующем поверхность Direct3D для представления кадра внутри себя, свойство **SoftwareBitmap** будет иметь значение, отличное от нулевого, а свойство **Direct3DSurface** будет иметь нулевое значение.

Ваше приложение должно всегда проверять нулевое значение перед попыткой работы с объектами, возвращенными свойством **SoftwareBitmap** или **Direct3DSurface**.

Поработав с кадром предварительного просмотра, не забудьте вызвать его метод [**Close**](https://msdn.microsoft.com/library/windows/apps/dn930918) (сопоставляемый с Dispose в C#), чтобы освободить ресурсы, используемые кадром. Можно также использовать шаблон **using**, который автоматически ликвидирует объект.

[!code-cs[CleanUpPreviewFrame](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpPreviewFrame)]

## <a name="related-topics"></a>Статьи по теме

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
 

 




