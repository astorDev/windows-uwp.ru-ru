---
title: Размещение на плитках вложенного ресурса Texture3D
description: В этой таблице показано, как размещаются на плитках вложенные ресурсы Texture3D.
ms.assetid: 210D03E4-CF12-47E0-BA2F-C8D059B17D3E
keywords:
- Размещение на плитках вложенного ресурса Texture3D
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: c9c232bc60bbbb3cccc16618d82ec23452c58ee8
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57645959"
---
# <a name="texture3d-subresource-tiling"></a>Размещение на плитках вложенного ресурса Texture3D


В этой таблице показано, как размещаются на плитках вложенные ресурсы [**Texture3D**](https://msdn.microsoft.com/library/windows/desktop/ff471562). Значения в этой таблице не учитывают упаковку хвостовых MIP-карт.

В этой таблице берется размещение на плитках [**Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff471525), размеры по осям X и Y делятся на 4 каждое, и добавляется 16 уровней глубины. Все плитки для первой плоскости (двухмерной плоскости плиток, определяющих первые 16 слоев глубины) отображаются перед последующими плоскостями.

**Примечание.**  Поддержка   [**Texture3D**](https://msdn.microsoft.com/library/windows/desktop/ff471562) в потоковых ресурсах не предоставляется в начальной реализации потоковых ресурсов, но необходимые формы плиток перечислены здесь ввиду возможного появления поддержки в будущих выпусках.

 

| Бит/пкс (1 семпл/пкс) | Размеры плиток (пиксели, ШxВxГ) |
|-----------------------------|---------------------------------|
| 8                           | 64x32x32                        |
| 16                          | 32x32x32                        |
| 32                          | 32x32x16                        |
| 64                          | 32x16x16                        |
| 128                         | 16x16x16                        |
| BC1,4                       | 128x64x16                       |
| BC2,3,5,6,7                 | 64x64x16                        |

 

Количество бит формат не поддерживается с помощью потоковой передачи ресурсов форматы 96 bpp, форматы видео DXGI\_ФОРМАТ\_R1\_UNORM, DXGI\_ФОРМАТ\_R8G8\_B8G8\_UNORM, и DXGI\_ФОРМАТ\_R8R8\_G8B8\_UNORM.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Как выполняется мозаичное заполнение области потоковой передачи ресурсов](how-a-streaming-resource-s-area-is-tiled.md)

 

 




