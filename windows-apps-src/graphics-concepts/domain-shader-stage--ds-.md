---
title: Этап шейдера доменов (DS)
description: На этапе шейдера доменов вычисляется положение вершины составной точки в выходном участке. Вычисляется положение вершины, соответствующее каждой выборке домена.
ms.assetid: 673CC04A-A74F-495F-AFB7-49157538749C
keywords:
- Этап шейдера доменов (DS)
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 3bcad4a5e22249d4d7faed08fe9cc9af4c3fb338
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5561550"
---
# <a name="domain-shader-ds-stage"></a>Этап шейдера доменов (DS)


На этапе шейдера доменов вычисляется положение вершины составной точки в выходном участке. Вычисляется положение вершины, соответствующее каждой выборке домена. Шейдер доменов запускается единожды на каждую выходную точку этапа тесселяции и имеет доступ только для чтения к выходному участку шейдера поверхности и константам выходного участка, а также к координатам UV выходных данных этапа тесселяции.

## <a name="span-idpurposeandusesspanspan-idpurposeandusesspanspan-idpurposeandusesspanpurpose-and-uses"></a><span id="Purpose_and_uses"></span><span id="purpose_and_uses"></span><span id="PURPOSE_AND_USES"></span>Назначение и способы использования


Этап шейдера домена выводит положение вершины составной точки в выходном участке на основе входных данных [этапа шейдера поверхности (HS)](hull-shader-stage--hs-.md) и [этапа тесселяции (TS)](tessellator-stage--ts-.md).

![схема этапа шейдера доменов](images/d3d11-domain-shader.png)

## <a name="span-idinputspanspan-idinputspanspan-idinputspaninput"></a><span id="Input"></span><span id="input"></span><span id="INPUT"></span>Входные данные


-   Шейдер доменов использует выходные контрольные точки [этапа шейдера поверхности (HS)](hull-shader-stage--hs-.md). Выходные данные шейдера поверхности включают следующее:
    -   контрольные точки;
    -   данные констант участков;
    -   факторы тесселяции. Факторы тесселяции могут включать значения, используемые тесселятором с фиксированными функциями, а также необработанные значения (например, перед округлением с помощью целочисленной тесселяции). Это, к примеру, упрощает геоморфизм.
-   Шейдер доменов вызывается единожды на каждую выходную координату [этапа тесселяции (TS)](tessellator-stage--ts-.md).

## <a name="span-idoutputspanspan-idoutputspanspan-idoutputspanoutput"></a><span id="Output"></span><span id="output"></span><span id="OUTPUT"></span>Выходные данные


-   Этап шейдера доменов (DS) выводит положение вершины составной точки в выходном участке.

После завершения шейдера доменов тесселяция закончена и конвейерные данные переходят на следующий этап конвейера, например [этап шейдера геометрии (GS)](geometry-shader-stage--gs-.md) и [этап построителя текстуры (PS)](pixel-shader-stage--ps-.md). Шейдер геометрии, ожидающий примитивы со смежностью (например, 6 вершин на треугольник), недействителен при активной тесселяции (это приводит к неопределенному поведению, о чем будет сообщать уровень отладки).

## <a name="span-idexamplespanspan-idexamplespanspan-idexamplespanexample"></a><span id="Example"></span><span id="example"></span><span id="EXAMPLE"></span>Пример


```
void main( out    MyDSOutput result, 
           float2 myInputUV : SV_DomainPoint, 
           MyDSInput DSInputs,
           OutputPatch<MyOutPoint, 12> ControlPts, 
           MyTessFactors tessFactors)
{
     ...

     result.Position = EvaluateSurfaceUV(ControlPoints, myInputUV);
}
```

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Графический конвейер](graphics-pipeline.md)

 

 




