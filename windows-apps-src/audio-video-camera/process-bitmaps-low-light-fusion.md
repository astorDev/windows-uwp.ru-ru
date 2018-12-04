---
description: В этой статье объясняется, как использовать класс LowLightFusion для обработки точечных рисунков.
title: Обработка точечных рисунков с помощью API Low Light Fusion
ms.date: 03/22/2018
ms.topic: article
keywords: windows 10, uwp, low light fusion, точечные рисунки, обработка изображений
ms.localizationpriority: medium
ms.openlocfilehash: e0677d2e4ce5e412c158b8a00da3a6338bae6c46
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8467402"
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

Выбрав подходящее число кадров и преобразовав их в приемлемый формат, можно с помощью метода **[FuseAsync](https://docs.microsoft.com/uwp/api/windows.media.core.lowlightfusion.fuseasync)** применить алгоритм Low Light Fusion. В результате мы получим более четкое обработанное изображение в виде объекта SoftwareBitmap. 

[!code-cs[SnippetFuseFrames](./code/LowLightFusionSample/cs/MainPage.xaml.cs#SnippetFuseFrames)]

Наконец, выполним очистку полученного изображения SoftwareBitmap: для этого зашифруем его и сохраним в виде "обычного", доступного для просмотра пользователем, изображения, похожего на то, с которого мы начинали.

[!code-cs[SnippetEncodeFrame](./code/LowLightFusionSample/cs/MainPage.xaml.cs#SnippetEncodeFrame)]


## <a name="before-and-after"></a>До и после

Здесь приводится пример изображения на входе и полученного после применения алгоритма Low Light Fusion изображения на выходе.

> [!div class="mx-tableFixed"] 
| Входной кадр | Результат использования функции Low Light Fusion | 
|-------------|-------------------------|
| ![Входной кадр для алгоритма Low Light Fusion](./images/LLF-Input.png) | ![Полученный после применения алгоритма Low Light Fusion кадр](./images/LLF-Output.png) |

Как можно видеть на входном кадре, яркость и четкость теней, окружающих баннер, были улучшены.

## <a name="related-topics"></a>Статьи по теме 
[Класс LowLightFusion](https://docs.microsoft.com/uwp/api/windows.media.core.lowlightfusion)  
[Класс LowLightFusionResult](https://docs.microsoft.com/uwp/api/windows.media.core.lowlightfusionresult)