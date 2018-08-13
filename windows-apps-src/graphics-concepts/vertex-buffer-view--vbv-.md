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
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: da0b8e8841e7c9df88ea22ba637d7236090e7ee5
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1044283"
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

 

 




