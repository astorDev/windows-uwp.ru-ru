---
title: Прямоугольники
description: В программировании для Direct3D и Windows объекты на экране называются терминами ограничивающих прямоугольников.
ms.assetid: 3B78AE66-2C1A-4191-BDCA-D737E33460BA
keywords:
- Прямоугольники
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 9d6a3b1eb64c89f231d0334a3cbe8e58d11c58ae
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370956"
---
# <a name="rectangles"></a>Прямоугольники

В программировании для Direct3D и Windows объекты на экране называются терминами ограничивающих прямоугольников. Стороны ограничивающего прямоугольника всегда параллельны сторонам экрана, поэтому такой прямоугольник можно описать двумя точками: левый верхний угол и правый нижний угол.

## <a name="span-idboundingrectanglesspanspan-idboundingrectanglesspanspan-idboundingrectanglesspanbounding-rectangles"></a><span id="Bounding_rectangles"></span><span id="bounding_rectangles"></span><span id="BOUNDING_RECTANGLES"></span>Ограничивающих прямоугольников


Большинство приложений используют структуру [**RECT**](https://docs.microsoft.com/previous-versions//dd162897(v=vs.85)) (или преобразованный с помощью typedef псевдоним) для передачи сведений об ограничивающем прямоугольнике, который необходимо использовать при копировании битового блока на экран или при обнаружении попаданий. В C++ структура **RECT** содержит следующее определение.

```cpp
typedef struct tagRECT { 
    LONG    left;    // This is the upper-left corner x-coordinate.
    LONG    top;     // The upper-left corner y-coordinate.
    LONG    right;   // The lower-right corner x-coordinate.
    LONG    bottom;  // The lower-right corner y-coordinate.
} RECT, *PRECT, NEAR *NPRECT, FAR *LPRECT; 
```

В предыдущем примере верхний и левый элементы — это координаты x и y верхнего левого угла ограничивающего прямоугольника. Аналогичным образом правый и нижний элементы представляют координаты правого нижнего угла. На следующем рисунке показано, как можно отобразить эти значения.

![иллюстрации прямоугольника, ограниченного значениями левой, верхней, правой и нижней сторон](images/rect.png)

Столбец пикселей справа и строка пикселей внизу не включаются в RECT. Например, блокировка RECT с использованием элементов {10, 10, 138, 138} в результате даст объект шириной и высотой 128 пикселей.

Для эффективности, согласования и удобства все функции представления Direct3D работают с прямоугольниками.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Системы координат и геометрия](coordinate-systems-and-geometry.md)

 

 




