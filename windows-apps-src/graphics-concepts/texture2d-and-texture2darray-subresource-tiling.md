---
title: "Размещение на плитках вложенных ресурсов Texture2D и Texture2DArray"
description: "В таблицах ниже показано, как размещаются на плитках вложенные ресурсы Texture2D и Texture2DArray."
ms.assetid: 2DC14DFC-5299-44D9-895F-5A223D3FD530
keywords: "Размещение на плитках вложенных ресурсов Texture2D и Texture2DArray"
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: 292adb2f06022fbb8fc063c49442cd69ccf64534
ms.sourcegitcommit: c80b9e6589a1ee29c5032a0b942e6a024c224ea7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="texture2d-and-texture2darray-subresource-tiling"></a>Размещение на плитках вложенных ресурсов Texture2D и Texture2DArray


В таблицах ниже показано, как размещаются на плитках вложенные ресурсы [**Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff471525) и [**Texture2DArray**](https://msdn.microsoft.com/library/windows/desktop/ff471526). Значения в этих таблицах не учитывают упаковку хвостовых MIP-карт.

## <a name="span-idsubresources-with-multisample-counts-of-1spanspan-idsubresources-with-multisample-counts-of-1spanspan-idsubresources-with-multisample-counts-of-1spansubresources-with-multisample-counts-of-1"></a><span id="Subresources-with-multisample-counts-of-1"></span><span id="subresources-with-multisample-counts-of-1"></span><span id="SUBRESOURCES-WITH-MULTISAMPLE-COUNTS-OF-1"></span>Вложенные ресурсы с множественной дискретизацией и одной выборкой


В этой таблице показано, как размещаются на плитках вложенные ресурсы [**Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff471525) и [**Texture2DArray**](https://msdn.microsoft.com/library/windows/desktop/ff471526) с множественной дискретизацией и одной выборкой.

| Бит/пкс (1 семпл/пкс) | Размеры плиток (пиксели, ШxВ) |
|-----------------------------|-------------------------------|
| 8                           | 256x256                       |
| 16                          | 256x128                       |
| 32                          | 128x128                       |
| 64                          | 128x64                        |
| 128                         | 64x64                         |
| BC1,4                       | 512x256                       |
| BC2,3,5,6,7                 | 256x256                       |

 

Для потоковых ресурсов не поддерживаются форматы 96 бит/пкс, видеоформаты, DXGI\_FORMAT\_R1\_UNORM, DXGI\_FORMAT\_R8G8\_B8G8\_UNORM и DXGI\_FORMAT\_R8R8\_G8B8\_UNORM.

## <a name="span-idsubresources-with-various-multisample-countsspanspan-idsubresources-with-various-multisample-countsspanspan-idsubresources-with-various-multisample-countsspansubresources-with-various-multisample-counts"></a><span id="Subresources-with-various-multisample-counts"></span><span id="subresources-with-various-multisample-counts"></span><span id="SUBRESOURCES-WITH-VARIOUS-MULTISAMPLE-COUNTS"></span>Вложенные ресурсы с множественной дискретизацией и различным числом выборок


В этой таблице показано, как размещаются на плитках вложенные ресурсы [**Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff471525) и [**Texture2DArray**](https://msdn.microsoft.com/library/windows/desktop/ff471526) с множественной дискретизацией и различным числом выборок.

| Бит/пкс (1 семпл/пкс) | Размеры плиток (пиксели, ШxВ) |
|-----------------------------|-------------------------------|
| 1                           | 1x1                           |
| 2                           | 2x1                           |
| 4                           | 2x2                           |
| 8                           | 4x2                           |
| 16                          | 4x4                           |

 

Требуется и разрешена поддержка только 1 и 4 выборок для потоковых ресурсов. Потоковые ресурсы в настоящее время не поддерживают 2, 8 и 16 выборок, несмотря на то, что они отображаются.

При реализации можно выбрать поддержку режима сглаживания с множественной дискретизацией (MSAA) с 2, 8 и 16 выборками для ресурсов, не являющихся потоковыми, несмотря на то, что потоковые ресурсы это не поддерживают.

Потоковые ресурсы с количеством выборок больше одной не могут использовать форматы 128 бит/пкс.

Ограничения на количество поддерживаемых выборок и форматы связаны с несогласованностью оборудования и спецификации.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Размещение плиток в области потокового ресурса](how-a-streaming-resource-s-area-is-tiled.md)

 

 




