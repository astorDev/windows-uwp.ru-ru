---
title: Списки точек
description: Список точек — это коллекция вершин, которые отображаются в виде изолированных точек. Ваше приложение может использовать списки точки в трехмерных сценах для получения звездных полей или пунктирных линий на поверхности многоугольника.
ms.assetid: 332954AE-019F-4A91-B773-E3A7C92F3297
keywords:
- Списки точек
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 84a08d480070e4a23147679dd9b5dda1f8c9cca1
ms.sourcegitcommit: b11f305dbf7649c4b68550b666487c77ea30d98f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "7832792"
---
# <a name="point-lists"></a>Списки точек


Список точек — это коллекция вершин, которые отображаются в виде изолированных точек. Ваше приложение может использовать списки точки в трехмерных сценах для получения звездных полей или пунктирных линий на поверхности многоугольника.

## <a name="span-idexamplespanspan-idexamplespanspan-idexamplespanexample"></a><span id="Example"></span><span id="example"></span><span id="EXAMPLE"></span>Пример


На следующем рисунке показан отрисованный список точек.

![иллюстрация списка точек](images/pointlst.png)

Приложение может применять к списку точек материалы и текстуры. Цвета материала или текстуры отображаются только в нарисованных точках, но не между ними.

Следующий код показывает, как создать вершины для такого списка точек.

```
struct CUSTOMVERTEX
{
    float x,y,z;
};

CUSTOMVERTEX Vertices[] = 
{
    {-5.0, -5.0, 0.0},
    { 0.0,  5.0, 0.0},
    { 5.0, -5.0, 0.0},
    {10.0,  5.0, 0.0},
    {15.0, -5.0, 0.0},
    {20.0,  5.0, 0.0}
};
```

В примере кода ниже показано, как отрисовать этот список точек в Direct3D.

```
//
// It is assumed that d3dDevice is a valid
// pointer to an IDirect3DDevice interface.
//
d3dDevice->DrawPrimitive( D3DPT_POINTLIST, 0, 6 );
```

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Примитивы](primitives.md)

 

 




