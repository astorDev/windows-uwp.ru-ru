---
title: "Примитивы"
description: "Трехмерный примитив — это коллекция вершин, которые формируют одну трехмерную фигуру."
ms.assetid: A1FE6F49-B0D4-4665-90E1-40AD98E668B1
keywords: "Примитивы"
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: fd6995e1d34b736f9f8d2844e1d4358446556c9a
ms.sourcegitcommit: c80b9e6589a1ee29c5032a0b942e6a024c224ea7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="primitives"></a>Примитивы


Трехмерный *примитив* — это коллекция вершин, которые формируют одну трехмерную фигуру.

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
<td align="left"><p>[Списки точек](point-lists.md)</p></td>
<td align="left"><p>Список точек — это коллекция вершин, которые отображаются в виде изолированных точек. Ваше приложение может использовать списки точки в трехмерных сценах для получения звездных полей или пунктирных линий на поверхности многоугольника.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Списки линий](line-lists.md)</p></td>
<td align="left"><p>Список линий — это список изолированных прямых отрезков. Списки линий полезны для таких задач, как добавление дождя или ливня в трехмерную сцену. Приложения создают списки линий, заполняя массив вершин.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Ломаные](line-strips.md)</p></td>
<td align="left"><p>Ломаная — это примитив, состоящий из соединенных отрезков. Приложение может использовать ломаные линии для создания незамкнутых многоугольников. Замкнутый многоугольник — это многоугольник, последняя вершина которого соединяется отрезком с его первой вершиной.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Списки треугольников](triangle-lists.md)</p></td>
<td align="left"><p>Список треугольников — это список изолированных треугольников. Изолированные треугольники могут находиться как рядом друг с другом, так и на удалении друг о друга. Список треугольников должен быт иметь как минимум три вершины, а общее количество вершин должно делиться на три.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Полосы треугольников](triangle-strips.md)</p></td>
<td align="left"><p>Полоса треугольников — это ряд соединенных треугольников. Поскольку треугольники соединены, приложению не нужно постоянно указывать все три вершины для каждого треугольника.</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Системы координат и геометрия](coordinate-systems-and-geometry.md)

 

 




