---
title: Размещение на плитках вложенных ресурсов Texture3D
description: В этой таблице показано, как размещаются на плитках вложенные ресурсы Texture3D.
ms.assetid: 210D03E4-CF12-47E0-BA2F-C8D059B17D3E
keywords:
- Размещение на плитках вложенных ресурсов Texture3D
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: d51d20ddaeca5aa0689104b3dd71e36b1a5d4132
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1043993"
---
# <a name="texture3d-subresource-tiling"></a>Размещение на плитках вложенных ресурсов Texture3D


В этой таблице показано, как размещаются на плитках вложенные ресурсы [**Texture3D**](https://msdn.microsoft.com/library/windows/desktop/ff471562). Значения в этой таблице не учитывают упаковку хвостовых MIP-карт.

В этой таблице берется размещение на плитках [**Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff471525), размеры по осям X и Y делятся на 4 каждое, и добавляется 16 уровней глубины. Все плитки для первой плоскости (двухмерной плоскости плиток, определяющих первые 16 слоев глубины) отображаются перед последующими плоскостями.

**Примечание.**  Поддержка [**Texture3D**](https://msdn.microsoft.com/library/windows/desktop/ff471562) в потоковых ресурсах не предоставляется в начальной реализации потоковых ресурсов, но необходимые формы плиток перечислены здесь ввиду возможного появления поддержки в будущих выпусках.

 

| Бит/пкс (1 семпл/пкс) | Размеры плиток (пиксели, ШxВxГ) |
|-----------------------------|---------------------------------|
| 8                           | 64x32x32                        |
| 16                          | 32x32x32                        |
| 32                          | 32x32x16                        |
| 64                          | 32x16x16                        |
| 128                         | 16x16x16                        |
| BC1,4                       | 128x64x16                       |
| BC2,3,5,6,7                 | 64x64x16                        |

 

Для потоковых ресурсов не поддерживаются форматы 96 бит/пкс, видеоформаты, DXGI\_FORMAT\_R1\_UNORM, DXGI\_FORMAT\_R8G8\_B8G8\_UNORM и DXGI\_FORMAT\_R8R8\_G8B8\_UNORM.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Размещение плиток в области потокового ресурса](how-a-streaming-resource-s-area-is-tiled.md)

 

 



