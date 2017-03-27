---
title: "Окна просмотра и кадрирование"
description: "Окно просмотра — это двумерный (2D) прямоугольник, в который проецируется 3D-сцена."
ms.assetid: D0DD646E-13AE-452A-AD22-8C35000D0BA9
keywords:
- "Окна просмотра и кадрирование"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 7bbd0c2f2254cb5c65f7ae211124c4b3b1e523ed
ms.lasthandoff: 02/07/2017

---

# <a name="viewports-and-clipping"></a>Окна просмотра и кадрирование


*Окно просмотра* — это двумерный (2D) прямоугольник, в который проецируется 3D-сцена. В Direct3D прямоугольник существует в виде координат в пределах поверхности Direct3D, которые система использует в качестве целевого объекта отрисовки. Преобразование проекции преобразует вершины в систему координат, используемую для окна просмотра. Окно просмотра также используется для задания диапазона значений глубины на поверхности целевого объекта отрисовки, в котором будет отрисовываться сцена (обычно от 0,0 до 1,0).

## <a name="span-idtheviewingfrustumspanspan-idtheviewingfrustumspanspan-idtheviewingfrustumspanthe-viewing-frustum"></a><span id="The_Viewing_Frustum"></span><span id="the_viewing_frustum"></span><span id="THE_VIEWING_FRUSTUM"></span>Усеченная пирамида видимости


Усеченная пирамида видимости — это 3D-объем в сцене, позиционируемый относительно камеры окна просмотра. Форма этого объема влияет на то, как модели проецируются из пространства камеры на экран. При использовании наиболее распространенного типа проекции — перспективной проекции — объекты, которые находятся ближе к камере, кажутся больше, чем объекты дальше от нее. В случае перспективной проекции усеченную пирамиду видимости можно визуализировать как пирамиду, на вершине которой находится камера, как показано на следующем рисунке. Эта пирамида пересекается передней и задней плоскостью кадрирования. Объем пирамиды между передней и задней плоскостями кадрирования — это и есть усеченная пирамида видимости. Объекты отображаются, только когда они находятся в пределах этого объема.

![Иллюстрация усеченной пирамиды видимости с передней и задней плоскостью кадрирования](images/frustum.png)

Если представить себе, что вы стоите в темной комнате и смотрите наружу через квадратное окно, то, что вы видите — это усеченная пирамида видимости. В этой аналогии ближняя плоскость кадрирования — это окно, а задняя плоскость кадрирования — это то, что в итоге перегораживает открывающийся перед вами вид: высотный дом через дорогу, горы вдалеке или вообще ничего. Вы видите все, что находится внутри усеченной пирамиды, которая начинается окном и заканчивается предметом, который перегораживает вид; больше вы не видите ничего.

Усеченная пирамида видимости определяется полем зрения и расстояниями до передней и задней плоскостей кадрирования, заданными в виде Z-координат, как показано на следующей схеме.

![Схема усеченной пирамиды видимости](images/fovdiag.png)

На этой схеме переменная D — это расстояние от камеры до начала координат пространства, которое было определено на последнем участке геометрического конвейера — при преобразовании для просмотра. Это пространство, вокруг которого располагаются пределы вашей усеченной пирамиды видимости. О том, как использовать эту переменную D для построения матрицы проекции, см. в разделе [Преобразование проекции](projection-transform.md)

## <a name="span-idviewportrectanglespanspan-idviewportrectanglespanspan-idviewportrectanglespanviewport-rectangle"></a><span id="Viewport_Rectangle"></span><span id="viewport_rectangle"></span><span id="VIEWPORT_RECTANGLE"></span>Прямоугольник окна просмотра


Структура окна просмотра содержит четыре члена (X, Y, Width, Height), которые определяют область поверхности целевого объекта отрисовки, где будет отрисовываться сцена. Эти значения соответствуют целевому прямоугольнику, или прямоугольнику окна просмотра, как показано на следующей схеме.

![Схема прямоугольника окна просмотра](images/destrect.png)

Значения, задаваемые в качестве членов X, Y, Width, Height, — это экранные координаты относительно левого верхнего угла поверхности целевого объекта отрисовки. Структура определяет два дополнительных члена (MinZ и MaxZ); они указывают диапазоны глубин, в которых будет отрисовываться сцена.

Direct3D предполагает, что объем кадрирования окна просмотра находится в диапазоне от -1,0 до 1,0 по оси X и от 1,0 до -1,0 по оси Y. Эти значения в прошлом использовались большинством приложений. Пропорции окна просмотра можно откорректировать перед кадрированием с использованием [преобразования проекции](projection-transform.md).

**Примечание**   Члены MinZ и MaxZ указывают диапазоны глубин, в которых будет отрисовываться сцена, и для кадрирования не используются. Большинство приложений будут задавать эти значения равными 0,0 и 1,0, чтобы разрешить системе отрисовывать сцену во всем диапазоне значений глубины в буфере глубины. В некоторых случаях можно добиться особых эффектов, используя другие диапазоны глубин. Например, для отрисовки экранных элементов в игре вы можно задать оба значения равными 0,0, чтобы заставить систему отрисовывать объекты в сцене на переднем плане, или задать их оба равными 1,0 для отрисовки объекта, который всегда должен находиться на заднем плане.

 

Размеры, используемые в членах X, Y, Width, Height структуры окна просмотра, определяют местоположение и размеры окна просмотра на поверхности целевого объекта отрисовки. Эти значения задаются в экранных координатах относительно левого верхнего угла поверхности.

Direct3D использует местоположение и размеры окна просмотра для масштабирования вершин так, чтобы отрисованная сцена поместилась в соответствующее место на поверхности целевого объекта. Внутренне Direct3D вставляет эти значения в следующую матрицу, которая применяется к каждой вершине.

![уравнение матрицы, применяемое к каждой вершине](images/vpscale.png)

Эта матрица масштабирует вершины в соответствии с размерами окна просмотра и желаемым диапазоном глубин, а также переносит их в соответствующее местоположение на поверхности целевого объекта отрисовки. Матрица также обращает Y-координату, чтобы отразить начало координат экрана в левом верхнем углу с осью Y, направленной вниз. После применения этой матрицы вершины по-прежнему однородны, т. е. они по-прежнему существуют как вершины \[x,y,z,w\], и их необходимо преобразовать в неоднородные координаты, прежде чем отправлять в средство программной прорисовки.

**Примечание** Приложения обычно задают члены MinZ и MaxZ равными 0,0 и 1,0 соответственно, чтобы заставить систему отрисовывать сцену во всем диапазоне глубин. Однако можно использовать другие значения для достижения определенных эффектов. Например, можно задать оба значения равными 0,0, чтобы все объекты отрисовывались на переднем плане, или равными 1,0, чтобы все объекты отрисовывались на заднем плане.

 

## <a name="span-idclearingaviewportspanspan-idclearingaviewportspanspan-idclearingaviewportspanclearing-a-viewport"></a><span id="Clearing_a_Viewport"></span><span id="clearing_a_viewport"></span><span id="CLEARING_A_VIEWPORT"></span>Очистка окна просмотра


При очистке окна просмотра содержимое прямоугольника окна просмотра на поверхности целевого объекта отрисовки сбрасывается. Также при этом может очищаться прямоугольник в буфере глубины и буфере трафарета.

## <a name="span-idsetuptheviewportforclippingspanspan-idsetuptheviewportforclippingspanspan-idsetuptheviewportforclippingspanset-up-the-viewport-for-clipping"></a><span id="Set_Up_the_Viewport_for_Clipping"></span><span id="set_up_the_viewport_for_clipping"></span><span id="SET_UP_THE_VIEWPORT_FOR_CLIPPING"></span>Настройка окна просмотра для кадрирования


Результаты матрицы проекции определяют объем кадрирования в пространстве проекции следующим образом:

-w<sub>c</sub>&lt;= x<sub>c</sub>&lt;= w<sub>c</sub>

-w<sub>c</sub>&lt;= y<sub>c</sub>&lt;= w<sub>c</sub>

0 &lt;= z<sub>c</sub>&lt;= w<sub>c</sub>

где x, y, z и w представляют координаты вершин после применения преобразования проекции. Все вершины, у которых x-, y- или z-компонент находится вне этих диапазонов, отсекаются, если кадрирование включено (поведение по умолчанию).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Системы координат и геометрия](coordinate-systems-and-geometry.md)

 

 




