---
title: Топологии примитивов
description: Direct3D поддерживает несколько топологий примитивов, которые определяют, как вершины интерпретируются и рассматриваются конвейером; к ним относятся списки точек, списки линий, и полосы треугольников.
ms.assetid: 7AA5A4A2-0B7C-431D-B597-684D58C02BA5
keywords:
- Топологии примитивов
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 85d1c41fc10f509f3872fb1e4a0af5fa1e1e7c30
ms.sourcegitcommit: d2517e522cacc5240f7dffd5bc1eaa278e3f7768
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8338118"
---
# <a name="primitive-topologies"></a>Топологии примитивов


Direct3D поддерживает несколько топологий примитивов, которые определяют, как вершины интерпретируются и рассматриваются конвейером; к ним относятся списки точек, списки линий, и полосы треугольников.

## <a name="span-idprimitivetypesspanspan-idprimitivetypesspanspan-idprimitivetypesspanbasic-primitive-topologies"></a><span id="Primitive_Types"></span><span id="primitive_types"></span><span id="PRIMITIVE_TYPES"></span>Базовые топологии примитивов


Поддерживаются следующие базовые топологии примитивов (или типы примитивов):

-   [списки точек;](point-lists.md)
-   [Списки линий](line-lists.md)
-   [Ломаные](line-strips.md)
-   [Списки треугольников](triangle-lists.md)
-   [полосы треугольников.](triangle-strips.md)

Сведения о визуализации каждого типа примитива см. на схеме ниже в разделе [Направление поворота и передние позиции вершин](#winding-direction-and-leading-vertex-positions).

На [этапе сборщика входных данных (IA)](input-assembler-stage--ia-.md) данные из буферов вершин и индексов считываются, объединяются в эти примитивы и передаются в оставшиеся стадии конвейера.

## <a name="span-idprimitiveadjacencyspanspan-idprimitiveadjacencyspanspan-idprimitiveadjacencyspanprimitive-adjacency"></a><span id="Primitive_Adjacency"></span><span id="primitive_adjacency"></span><span id="PRIMITIVE_ADJACENCY"></span>Соседство примитивов


Все типы примитивов Direct3D (за исключением списка точек) доступны в двух версиях: один тип примитива с соседством и один тип примитива без соседства. Примитивы с соседством содержат некоторые окружающие вершин, а примитивы без соседство содержат только вершины целевого примитива. Например, у примитива списка линий есть соответствующий примитив списка линий с соседством.

Соседние примитивы предоставляют дополнительные сведения о геометрии и видны только через шейдер геометрии. Соседство полезно для шейдеров геометрии, которые используются обнаружение силуэтов, экструзию теневых объемов и т. д.

Например, предположим, что вы хотите нарисовать список треугольников с соседством. Список треугольников, содержащий 36 вершин (с соседством), даст 6 завершенных примитивов. Примитивы с соседством (за исключением ломаных) содержат ровно два раза больше вершин, чем эквивалентный примитив без соседства, где каждая дополнительная вершина является соседней.

## <a name="span-idwindingdirectionandleadingvertexpositionsspanspan-idwindingdirectionandleadingvertexpositionsspanspan-idwindingdirectionandleadingvertexpositionsspanspan-idwinding-direction-and-leading-vertex-positionsspanwinding-direction-and-leading-vertex-positions"></a><span id="Winding_Direction_and_Leading_Vertex_Positions"></span><span id="winding_direction_and_leading_vertex_positions"></span><span id="WINDING_DIRECTION_AND_LEADING_VERTEX_POSITIONS"></span><span id="winding-direction-and-leading-vertex-positions"></span>Направление поворота и передние позиции вершин


Как показано на следующем рисунке, передняя вершина — это первая несмежная вершина в примитиве. Для типа примитива может быть задано несколько начальных вершин, если каждая из них используется для различных примитивов.

-   Для полосы треугольников с соседством передние вершины — это 0, 2, 4, 6 и т. д.
-   Для ломаной с соседством передние вершины — 1, 2, 3 и т. д.
-   С другой стороны у соседнего примитива нет передних вершин.

На следующем рисунке показан порядок вершин для всех типов примитивов, которые может получить сборщик входных данных.

![схема порядка вершин для типов примитивов](images/d3d10-primitive-topologies.png)

В следующей таблице описаны символы, использованные в предыдущем примере.

| Символ                                                                                   | Имя              | Описание                                                                         |
|------------------------------------------------------------------------------------------|-------------------|-------------------------------------------------------------------------------------|
| ![символ вершины](images/d3d10-primitive-topologies-vertex.png)                     | Вершина            | Точка в трехмерном пространстве.                                                                |
| ![символ направления поворота](images/d3d10-primitive-topologies-winding-direction.png) | Направление поворота | Порядок вершин при сборке примитива. По часовой стрелке или против часовой стрелки. |
| ![символ передней вершины](images/d3d10-primitive-topologies-leading-vertex.png)       | Передняя вершина    | Первая несмежная вершина в примитиве, содержащем данные констант.       |

 

## <a name="span-idgeneratingmultiplestripsspanspan-idgeneratingmultiplestripsspanspan-idgeneratingmultiplestripsspangenerating-multiple-strips"></a><span id="Generating_Multiple_Strips"></span><span id="generating_multiple_strips"></span><span id="GENERATING_MULTIPLE_STRIPS"></span>Создание нескольких полос


Вы можете создать несколько полос с помощью вырезания полос. Вы можете вырезать полосы, явно вызвав HLSL-функцию [RestartStrip](https://msdn.microsoft.com/library/windows/desktop/bb509660) или вставив специальное значение индекса в буфер индексов. Это значение –1, т. е. 0xffffffff для 32-разрядных индексы или 0xffff для 16-разрядные индексов.

Индекс –1 указывает на явное «вырезание» или «перезапуск» текущей полосы. Предыдущий индекс завершает предыдущий примитив или полосу, а следующий индекс начинает новый примитив или полосу.

Дополнительные сведения о создании нескольких полос см. в разделе [Этап шейдера геометрии (GS)](geometry-shader-stage--gs-.md).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Этап сборщика входных данных](input-assembler-stage--ia-.md)

[Графический конвейер](graphics-pipeline.md)

 

 




