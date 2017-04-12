---
title: "Представление буфера вершин (VBV) и представление буфера индексов (IBV)"
description: "Буфер вершин содержит данные для списка вершин."
ms.assetid: 695115D2-9DA0-41F2-9416-33BFAB698129
keywords: "Представление буфера вершин (VBV)"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.openlocfilehash: 502b0e4816e31ebace93d3250f7da335d2540272
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="vertex-buffer-view-vbv-and-index-buffer-view-ibv"></a>Представление буфера вершин (VBV) и представление буфера индексов (IBV)


Буфер вершин содержит данные для списка вершин. Данные по каждой вершине могут включать в себя положение, цвет, вектор нормали, координаты текстуры и т.д. Буфер индексов содержит целочисленные индексы (смещения) в буфере вершин и используется для определения и отрисовки объекта, состоящего из подмножества полного списка вершин.

Определение одиночной вершины часто зависит от приложения, например:

``` syntax
struct CUSTOMVERTEX { 
        FLOAT x, y, z;      // The position
        FLOAT nx, ny, nz;   // The normal
        DWORD color;        // RGBA color
        FLOAT tu, tv;       // The texture coordinates. 
}; 
```

Определение CUSTOMVERTEX затем может быть передано графическому драйверу при создании буферов вершин.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Представления](views.md)

 

 




