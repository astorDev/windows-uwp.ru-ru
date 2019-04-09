---
title: Списки линий
description: Список линий — это список изолированных прямых отрезков. Списки линий полезны для таких задач, как добавление дождя или ливня в трехмерную сцену. Приложения создают списки линий, заполняя массив вершин.
ms.assetid: 42BF32A1-3535-42A3-82C5-3945CB309F2C
keywords:
- Списки линий
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: ac66066a4140ace5905ff6bc52a7b1290341beea
ms.sourcegitcommit: 82edc63a5b3623abce1d5e70d8e200a58dec673c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58291592"
---
# <a name="line-lists"></a>Списки линий

Список линий — это список изолированных прямых отрезков. Списки линий полезны для таких задач, как добавление дождя или ливня в трехмерную сцену. Приложения создают списки линий, заполняя массив вершин. Обратите внимание, что число вершин в списке линий должно быть четным числом больше двух или равным двум.

-   [Пример](#example)
-   [Связанные разделы](#related-topics)

## <a name="span-idexamplespanspan-idexamplespanspan-idexamplespanexample"></a><span id="Example"></span><span id="example"></span><span id="EXAMPLE"></span>Пример


На следующем рисунке показан список отображаемых линий.

![иллюстрация списка линий](images/linelst.png)

К списку линий можно применять материалы и текстуры. Цвета в материале или текстуре отображаются только на прорисованных линиях и отсутствуют во всех других точках, не относящихся к этим линиям.

Следующий код показывает, как создать вершины для такого списка линий.

```cpp
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

В примере кода ниже показано, как отрисовать список линий в Direct3D.

```cpp
//
// It is assumed that d3dDevice is a valid
// pointer to an IDirect3DDevice interface.
//
d3dDevice->DrawPrimitive( D3DPT_LINELIST, 0, 3 );
```

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Примитивы](primitives.md)

 

 




