---
title: "Вводные сведения о текстурах"
description: "Текстурный ресурс — это структура данных для хранения текселей, представляющих собой наименьшие элементы текстуры, которые можно использовать для чтения или записи. При чтении текстуры шейдером ее можно фильтровать по дискретизаторам текстур."
ms.assetid: 6F3C76A8-F762-4296-AE02-BFBD6476A5A8
keywords:
- "Вводные сведения о текстурах"
author: mtoepke
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: ccdca1396673b0eed9ef74c5f0ce3a6f97e02b6d
ms.lasthandoff: 02/07/2017

---

# <a name="introduction-to-textures"></a>Вводные сведения о текстурах


Текстурный ресурс — это структура данных для хранения текселей, представляющих собой наименьшие элементы текстуры, которые можно использовать для чтения или записи. При чтении текстуры шейдером ее можно фильтровать по дискретизаторам текстур.

Текстурный ресурс — это структурированный набор данных, предназначенный для хранения текселей. Тексель представляет собой наименьший элемент текстуры, который может использоваться конвейером для чтения или записи. В отличие от буферов текстуры можно фильтровать по дискретизаторам текстур во время их считывания блоками шейдера. Тип текстуры влияет на способ ее фильтрации. Каждый тексель содержит от 1 до 4 компонентов, расположенных в одном из форматов DXGI, определенных перечислением DXGI\_FORMAT.

Текстуры создаются как структурированные ресурсы с известным размером. Однако каждая текстура может быть типизированной или не иметь типа при создании ресурса, если тип полностью задается с помощью представления при привязке текстуры к конвейеру.

## <a name="span-idtexturetypesspanspan-idtexturetypesspanspan-idtexturetypesspantexture-types"></a><span id="Texture_Types"></span><span id="texture_types"></span><span id="TEXTURE_TYPES"></span>Типы текстур


Direct3D поддерживает несколько представлений с плавающей запятой. Все вычисления с плавающей запятой выполняются согласно определенному набору правил для формата 32-разрядных чисел одинарной точности с плавающей запятой по стандарту IEEE 754.

Существует несколько типов текстур: одно-, дву-, и трехмерный; каждый из них может быть создан с использованием MIP-карт и без них. Direct3D также поддерживает массивы текстур и текстуры с множественной дискретизацией.

-   [Одномерные текстуры](#texture1d-resource)
-   [Массивы одномерных текстур](#texture1d-array-resource)
-   [Двумерные текстуры и массивы двумерных текстур](#texture2d-resource)
-   [Трехмерные текстуры](#texture3d-resource)

### <a name="span-idtexture1dresourcespanspan-idtexture1dresourcespanspan-idtexture1dresourcespanspan-idtexture1d-resourcespan1d-textures"></a><span id="Texture1D_Resource"></span><span id="texture1d_resource"></span><span id="TEXTURE1D_RESOURCE"></span><span id="texture1d-resource"></span>Одномерные текстуры

Одномерная текстура в самом простом своем виде содержит данные текстуры, к которым можно обращаться по одной координате текстуры; ее можно отобразить как массив текселей, как показано на следующем рисунке.

На следующем рисунке показана одномерная текстура.

![одномерная текстура](images/d3d10-1d-texture.png)

Каждый тексель содержит несколько цветовых компонентов в зависимости от формата хранимых данных. Повышая сложность, можно создать одномерную текстуру с уровнями MIP, как показано на следующем рисунке.

![одномерная текстура с уровнями MIP](images/d3d10-resource-texture1d.png)

Уровень MIP — это текстура, которая меньше вышестоящего уровня на число, соответствующее степени двух. Самый верхний уровень наиболее детализирован, а каждый последующий уровень содержит меньше деталей. В одномерной MIP-карте самый нижний уровень содержит один тексель. Кроме того, уровни MIP всегда уменьшаются до 1:1.

При создании MIP-карт для текстуры с нечетным значением размера более низкий уровень всегда имеет четное значение размера (за исключением низшего уровня, достигающего 1). Например, на схеме показана текстура 5 x 1, следующий низкий уровень которой является текстурой 2 x 1, а следующим (и последним) уровнем MIP является текстура размером 1 x 1. Уровни идентифицируются с помощью индекса, называемого LOD (уровень детализации), который используется для доступа к более мелким текстурам при отрисовке геометрии, находящейся не так близко к камере.

### <a name="span-idtexture1darrayresourcespanspan-idtexture1darrayresourcespanspan-idtexture1darrayresourcespanspan-idtexture1d-array-resourcespan1d-texture-arrays"></a><span id="Texture1D_Array_Resource"></span><span id="texture1d_array_resource"></span><span id="TEXTURE1D_ARRAY_RESOURCE"></span><span id="texture1d-array-resource"></span>Массивы одномерных текстур

Direct3D также поддерживает массивы текстур. Массив одномерных текстур концептуально выглядит, как на следующем рисунке.

![массив одномерных текстур](images/d3d10-resource-texture1darray.png)

Этот массив текстур содержит три текстуры. Каждая из трех текстур имеет ширину текстуры, равную 5 (это число элементов на первом уровне). Каждая текстура также содержит трехслойную MIP-карту.

Все массивы текстур в Direct3D являются однородным массивом текстур; это означает, что каждая текстура в массиве текстур должна иметь тот же формат данных и размер (в том числе ширину текстуры и количество уровней MIP). Вы можете создавать массивы текстур различных размеров при условии, что все текстуры в отдельном массиве соответствуют друг другу по размеру.

### <a name="span-idtexture2dresourcespanspan-idtexture2dresourcespanspan-idtexture2dresourcespanspan-idtexture2d-resourcespan2d-textures-and-2d-texture-arrays"></a><span id="Texture2D_Resource"></span><span id="texture2d_resource"></span><span id="TEXTURE2D_RESOURCE"></span><span id="texture2d-resource"></span>Двумерные текстуры и массивы двумерных текстур

Ресурс Texture2D содержит двумерную сетку текселей. На каждый тексель указывает вектор u, v. Поскольку это текстурный ресурс, он может содержать уровни MIP и вложенные ресурсы. Полностью заполненный ресурс двумерных текстур соответствует изображению на следующем рисунке.

![ресурс двумерных текстур](images/d3d10-resource-texture2d.png)

Этот текстурный ресурс содержит одну текстуру 3 x 5 с тремя уровнями MIP.

Ресурс массива двумерных текстур — это однородный массив двумерных текстур; то есть все текстуры имеют одинаковый формат данных и размеры (в том числе уровни MIP). Он имеет аналогичный массиву одномерных текстур макет за исключением того, что теперь текстуры содержат двумерные данных, как показано на следующем рисунке.

![массив двумерных текстур](images/d3d10-resource-texture2darray.png)

Этот массив текстур содержит три текстуры; каждая текстура имеет размер 3 x 5 и два уровня MIP.

### <a name="span-idtexture2darrayresourceasatexturecubespanspan-idtexture2darrayresourceasatexturecubespanspan-idtexture2darrayresourceasatexturecubespanusing-a-2d-texture-array-as-a-texture-cube"></a><span id="Texture2DArray_Resource_as_a_Texture_Cube"></span><span id="texture2darray_resource_as_a_texture_cube"></span><span id="TEXTURE2DARRAY_RESOURCE_AS_A_TEXTURE_CUBE"></span>Использование массива двумерных текстур как куба текстур

Куб текстур — это массив двумерных текстур, содержащий 6 текстур, по одной на каждую грань куба. Полностью заполненный куб текстур выглядит, как изображение на следующем рисунке.

![массив двумерных текстур, представляющих куб текстур](images/d3d10-resource-texturecube.png)

Чтение массива двумерных текстур, содержащего 6 текстур, может производиться из шейдеров со встроенными функциями карт куба, после их привязки к конвейеру с представлением кубической текстуры. Обращение к кубам текстур производится из шейдера с трехмерным вектором, направленным от центра куба текстур.

### <a name="span-idtexture3dresourcespanspan-idtexture3dresourcespanspan-idtexture3dresourcespanspan-idtexture3d-resourcespan3d-textures"></a><span id="Texture3D_Resource"></span><span id="texture3d_resource"></span><span id="TEXTURE3D_RESOURCE"></span><span id="texture3d-resource"></span>Трехмерные текстуры

Ресурс трехмерной текстуры (также известной как объемная текстура) содержит трехмерный объем текселей. Поскольку это текстурный ресурс, он может содержать уровни MIP. Полностью заполненная трехмерная текстура выглядит, как изображение на следующем рисунке.

![ресурс трехмерной текстуры](images/d3d10-resource-texture3d.png)

При привязке фрагмента MIP-карты трехмерной текстуры в качестве целевых выходных данных отрисовки (с использованием представления целевого объекта отрисовки) трехмерная текстура ведет себя аналогично массиву двумерных текстур с n фрагментами. Конкретный фрагмент отрисовки выбирается из этапа шейдера геометрии.

Понятие массива трехмерных текстур не существует, соответственно, вложенный ресурс трехмерных текстур является единичным уровнем карты MIP.

Системы координат для Direct3D определены для точек и текселей.

## <a name="span-idpixelspanspan-idpixelspanspan-idpixelspanpixel-coordinate-system"></a><span id="Pixel"></span><span id="pixel"></span><span id="PIXEL"></span>Система координат для пикселей


Система координат для пикселей в Direct3D определяет начало координат целевого объекта отрисовки в левом верхнем углу, как показано на следующем рисунке. Центры пикселей смещены на (0,5f, 0,5f) от целочисленных расположений.

![схема системы координат для пикселей в direct3d 10](images/d3d10-coordspix10.png)

## <a name="span-idtexelspanspan-idtexelspanspan-idtexelspantexel-coordinate-system"></a><span id="Texel"></span><span id="texel"></span><span id="TEXEL"></span>Система координат для текселей


Начало системы координат для текселей расположено в верхнем левом углу текстуры, как показано на следующем рисунке. Это упрощает отрисовку выровненных по экрану текстур, поскольку система координат для пикселей совпадает с системой координат для текселей.

![схема системы координат для текселей](images/d3d10-coordstex10.png)

Координаты текстуры представлены в виде нормализованного или скалярного числа; каждая координата текстуры сопоставляется определенному текселю следующим образом.

Для нормализованной координаты:

-   выборка точек: тексель \# = низ(U \* ширина)
-   линейная выборка: левый тексель \# = низ(U \* ширина), правый тексель \# = левый тексель \# + 1

Для скалярной координаты:

-   выборка точек: тексель \# = низ(U)
-   линейная выборка: левый тексель \# = низ(U - 0,5), правый тексель \# = левый тексель \# + 1

Где ширина — это ширина текстуры (в текселях).

Упаковка адреса текстуры происходит после расчета расположения текселя.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Текстуры](textures.md)
