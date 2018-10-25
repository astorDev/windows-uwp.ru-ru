---
title: Представление буфера вершин (VBV) и представление буфера индексов (IBV)
description: Буфер вершин содержит данные для списка вершин.
ms.assetid: 695115D2-9DA0-41F2-9416-33BFAB698129
keywords:
- Представление буфера вершин (VBV)
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 7956c7a03256da04e98a5b8f9a33951d7e0216d3
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "5572433"
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

 

 




