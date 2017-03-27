---
title: "Конвейерный доступ к потоковым ресурсам"
description: "Потоковые ресурсы можно использовать в представлениях ресурсов шейдера (SRV), представлениях целевого объекта отрисовки (RTV), представлениях трафарета глубины (DSV) и представлениях неупорядоченного доступа (UAV), а также как точки привязки, где представления не используются, например как привязки буфера вершин."
ms.assetid: 18DA5D61-930D-4466-8EFE-0CED566EA4A6
keywords:
- "Конвейерный доступ к потоковым ресурсам"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 5f4fd92e1519579f15b3c3d452ce80e944f0942b
ms.lasthandoff: 02/07/2017

---

# <a name="pipeline-access-to-streaming-resources"></a>Конвейерный доступ к потоковым ресурсам


Потоковые ресурсы можно использовать в представлениях ресурсов шейдера (SRV), представлениях целевого объекта отрисовки (RTV), представлениях трафарета глубины (DSV) и представлениях неупорядоченного доступа (UAV), а также как точки привязки, где представления не используются, например как привязки буфера вершин. Список поддерживаемых привязок см. в разделе [Параметры создания потоковых ресурсов](streaming-resource-creation-parameters.md). Различные операции копирования D3D также работают с потоковыми ресурсами.

Если несколько координат плиток в одном или нескольких представлениях привязаны к одной области памяти, операции чтения и записи для различных путей в одной области памяти являются недетерминистическими и неповторяемыми.

Если все плитки, связанные с доступом к памяти шейдера, сопоставляются с уникальными плитками, поведение для всех реализаций будет одинаковым, т. е. на поверхности будет одинаковое содержимое памяти без плиток.

## <a name="span-idin-this-sectionspanin-this-section"></a><span id="in-this-section"></span>В этом разделе


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Раздел</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[Поведение SRV с несопоставленными плитками](srv-behavior-with-non-mapped-tiles.md)</p></td>
<td align="left"><p>Поведение операций чтения представления ресурса шейдера (SRV), включающих несопоставленные плитки, зависит от уровня поддержки оборудования.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Поведение UAV с несопоставленными плитками](uav-behavior-with-non-mapped-tiles.md)</p></td>
<td align="left"><p>Поведение операций чтения и записи представлений неупорядоченного доступа (UAV) зависит от уровня поддержки оборудования.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Поведение средства программной прорисовки с несопоставленными плитками](rasterizer-behavior-with-non-mapped-tiles.md)</p></td>
<td align="left"><p>В этом разделе описывается поведение растеризации несопоставленных плиток.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Ограничения доступа к плитке с повторяющимися сопоставлениями](tile-access-limitations-with-duplicate-mappings.md)</p></td>
<td align="left"><p>Существуют ограничения на доступ к плиткам с повторяющимися сопоставлениями, например при копировании потоковых ресурсов с перекрывающимися источником и назначением или при отрисовке общих плиток для области отрисовки.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Функции создания образцов текстур для потоковых ресурсов](streaming-resources-texture-sampling-features.md)</p></td>
<td align="left"><p>Дискретизация текстур потоковых ресурсов включает в себя обратную связь от шейдера о состоянии сопоставленных областей. При этом проверяется, были ли все данные, к которым запрашивается доступ, сопоставлены в ресурсе, выполняется сжатие, чтобы помочь шейдерам избежать областей в потоковых ресурсах, которые не были сопоставлены, и определяется минимальный уровень детализации, полностью сопоставленной для всего фильтра текстуры.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Экспозиция потоковых ресурсов HLSL](hlsl-streaming-resources-exposure.md)</p></td>
<td align="left"><p>Для поддержки потоковых ресурсов в [модели шейдера 5](https://msdn.microsoft.com/library/windows/desktop/ff471356) требуется определенный синтаксис HLSL.</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Потоковые ресурсы](streaming-resources.md)

 

 




