---
author: laurenhughes
description: "В этой статье объясняется, как использовать класс LowLightFusion для обработки точечных рисунков."
title: "Обработка точечных рисунков с помощью API Low Light Fusion"
ms.author: lahugh
ms.date: 11/02/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, low light fusion, точечные рисунки, обработка изображений"
localizationpriority: medium
ms.openlocfilehash: 989571063603a7133f39961b4b32bdc60fc373dc
ms.sourcegitcommit: d0c93d734639bd31f264424ae5b6fead903a951d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="process-bitmaps-with-the-lowlightfusion-api"></a>Обработка точечных рисунков с помощью API LowLightFusion

Изображения в условиях низкой освещенности сложно зафиксировать с хорошим качеством изображения, особенно если на мобильном устройстве фиксированные диафрагма и размер датчика. Чтобы компенсировать условия низкой освещенности, время экспозиции или усиление датчика увеличивается, что может стать причиной размытости движений или повышенному шуму на изображениях. 

[Класс LowLightFusion](https://docs.microsoft.com/uwp/api/windows.media.core.lowlightfusion) повышает качество изображений в условиях низкой освещенности, делая выборку пиксельных данных из нескольких кадров, снятых в течение короткого промежутка времени, например из короткой серии кадров, чтобы уменьшить шум и размытость движений. Это полезная функция, которую, например, можно добавить в фоторедактор.

Эта функция становится доступной благодаря [классу AdvancedPhotoCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture), который при необходимости применяет алгоритм Low Light Fusion к последовательности изображений сразу после съемки. См. сведения о реализации этой функции в разделе о [создании фото в условиях низкой освещенности](https://docs.microsoft.com/windows/uwp/audio-video-camera/high-dynamic-range-hdr-photo-capture#low-light-photo-capture).

## <a name="prepare-the-images-for-processing"></a>Подготовка изображений для обработки

В этом примере рассматривается, как использовать [класс LowLightFusion](https://docs.microsoft.com/uwp/api/windows.media.core.lowlightfusion) и [FileOpenPicker](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker), чтобы предоставить пользователю возможность выбрать несколько изображений для применения функции Low Light Fusion.

Сначала необходимо определить, сколько изображений ("кадров") принимает алгоритм, и создать список для размещения этих кадров.

[!code-cs[SnippetGetMaxLLFFrames](./code/LowLightFusionSample/cs/MainPage.xaml.cs#SnippetGetMaxLLFFrames)]

Определив, сколько кадров принимает алгоритм Low Light Fusion, можно воспользоваться инструментов [FileOpenPicker](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker), чтобы предоставить пользователю возможность выбрать, какие изображения следует использовать в алгоритме.

[!code-cs[SnippetGetFrames](./code/LowLightFusionSample/cs/MainPage.xaml.cs#SnippetGetFrames)]

Выбрав подходящее число кадров, необходимо декодировать кадры в объекты [SoftwareBitmap](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) и убедиться, что эти объекты имеют подходящий формат для LowLightFusion.

[!code-cs[SnippetDecodeFrames](./code/LowLightFusionSample/cs/MainPage.xaml.cs#SnippetDecodeFrames)]


## <a name="fuse-the-bitmaps-into-a-single-bitmap"></a>Объединение нескольких точечных рисунков в один

Выбрав подходящее число кадров и преобразовав их в приемлемый формат, можно с помощью метода **[FuseAsync](https://docs.microsoft.com/uwp/api/windows.media.core.lowlightfusion#Windows_Media_Core_LowLightFusion_FuseAsync_Windows_Foundation_Collections_IIterable_Windows_Graphics_Imaging_SoftwareBitmap__)** применить алгоритм Low Light Fusion. В результате мы получим более четкое обработанное изображение в виде объекта SoftwareBitmap. 

[!code-cs[SnippetFuseFrames](./code/LowLightFusionSample/cs/MainPage.xaml.cs#SnippetFuseFrames)]

Наконец, выполним очистку полученного изображения SoftwareBitmap: для этого зашифруем его и сохраним в виде "обычного", доступного для просмотра пользователем, изображения, похожего на то, с которого мы начинали.

[!code-cs[SnippetEncodeFrame](./code/LowLightFusionSample/cs/MainPage.xaml.cs#SnippetEncodeFrame)]


## <a name="before-and-after"></a>До и после

Здесь приводится пример двух изображений на входе и полученного после применения алгоритма Low Light Fusion изображения на выходе.

> [!div class="mx-tableFixed"] 
| Кадр 1 | Кадр 2 | Результат использования функции Low Light Fusion | 
|---------|---------|-------------------------|
| ![Первый входной кадр для алгоритма Low Light Fusion](./images/LLF-Input1.png) | ![Второй входной кадр для алгоритма Low Light Fusion](./images/LLF-Input2.png) | ![Полученный после применения алгоритма Low Light Fusion кадр](./images/LLF-Output.png) |

Посмотрите на кадры на входе и на выходе: четкость и освещенность чашки улучшились, окружающее изображение, например молдинг рядом с ковром, тоже стало четче.

## <a name="related-topics"></a>Связанные статьи 
[Класс LowLightFusion](https://docs.microsoft.com/uwp/api/windows.media.core.lowlightfusion)  
[Класс LowLightFusionResult](https://docs.microsoft.com/uwp/api/windows.media.core.lowlightfusionresult)