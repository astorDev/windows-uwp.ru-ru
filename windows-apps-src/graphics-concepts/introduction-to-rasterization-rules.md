---
title: Общие сведения о правилах растеризации
description: Довольно часто точки, заданные для вершин, не имеют точного соответствия с пикселями на экране. В этом случае Direct3D применяет правила растеризации по треугольнику, чтобы определить, какие пиксели относятся к определенному треугольнику.
ms.assetid: 4232CDBA-F669-4417-9378-F9013E83462C
keywords:
- Общие сведения о правилах растеризации
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: c635d0efad627734e0305c687e015ad183764717
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371055"
---
# <a name="introduction-to-rasterization-rules"></a>Общие сведения о правилах растеризации


Довольно часто точки, заданные для вершин, не имеют точного соответствия с пикселями на экране. В этом случае Direct3D применяет правила растеризации по треугольнику, чтобы определить, какие пиксели относятся к определенному треугольнику.

Это упрощенное введение в правила растеризации. Дополнительные сведения см. в разделе [Правила растеризации](rasterization-rules.md). См. также [Этап средства программной прорисовки (RS)](rasterizer-stage--rs-.md).

## <a name="span-idtrianglerasterizationrulesspanspan-idtrianglerasterizationrulesspanspan-idtrianglerasterizationrulesspantriangle-rasterization-rules"></a><span id="Triangle_Rasterization_Rules"></span><span id="triangle_rasterization_rules"></span><span id="TRIANGLE_RASTERIZATION_RULES"></span>Правила растеризации треугольник


Direct3D использует общепринятый подход с началом отсчета в левом верхнему углу для размещения геометрических элементов. Этот же подход используется для прямоугольников в GDI и OpenGL. В Direct3D центр пикселя является решающей точкой. Если центр находится внутри треугольника, этот пиксель является частью такого треугольника. Центрам пикселей соответствуют целочисленные координаты.

Это описание правил растеризации по треугольнику, используемых Direct3D, не обязательно распространяется на все доступное оборудование. Во время тестирования вы можете обнаружить небольшие расхождения в реализации этих правил.

На следующем рисунке показан прямоугольник, левый верхний угол которого находится в точке (0, 0), а правый нижний угол — в точке (5, 5). Этот прямоугольник заполняет 25 пикселей, как и следовало ожидать. Ширина прямоугольника определяется как значение правой координаты минус значение левой координаты. Высота определяется как значение нижней координаты минус значение верхней координаты.

![пронумерованный квадрат, разделенный на шесть строк и столбцов](images/pixmap.png)

В подходе с заполнением от верхнего левого угла термин *верхний* указывает на расположение горизонтальных отрезков по вертикали, а термин *левый* указывает на расположение пикселей в отрезке по горизонтали. Сторона фигуры не может быть верхней, если она не является горизонтальной. Чаще всего у большинства треугольников есть только левая и правая стороны. На следующем рисунке показаны верхняя и правая стороны.

![пронумерованный квадрат, содержащий два треугольника](images/triedge.png)

Подход с заполнением от левого верхнего угла определяет действие, предпринимаемое Direct3D, когда треугольник проходит через центр пикселя. На следующем рисунке показаны два треугольника, один с координатами вершин (0, 0), (5, 0) и (5, 5) и второй с координатами вершин (0, 5) (0, 0) и (5, 5). К первому треугольнику в этом случае относятся 15 пикселей (показаны черным цветом), тогда как ко второму только 10 пикселей (отображаются серым цветом), поскольку их общая сторона — левая сторона первого треугольника.

![пронумерованный квадрат с двумя треугольниками](images/twotris.png)

Если определить прямоугольник с верхним левым углом в точке (0,5, 0,5) и нижним правым углом в точке (2,5, 4,5), центральной точкой этого прямоугольника будет (1,5, 2,5). При растеризации Direct3D раскладывает этот прямоугольник, центр каждого пикселя однозначно находится внутри каждого из четырех треугольников и подход с заполнением от левого верхнего угла не требуется. На следующем рисунке показана такая ситуация. Пиксели в прямоугольнике помечены согласно треугольнику, в который Direct3D их включает.

![пронумерованный квадрат, содержащий прямоугольник, поделенный на четыре треугольника](images/noambig.png)

При перемещении прямоугольника с предыдущего рисунка таким образом, чтобы его верхний левый угол оказался в точке (1,0, 1,0), его правый нижний угол — в точке (3,0, 5,0), а центр — в точке (2,0, 3,0), Direct3D применяет подход с заполнением от левого верхнего угла. Большинство пикселей в этом прямоугольнике попадают на границу между двумя или более треугольниками, как показано на рисунке ниже.

![пронумерованный квадрат, содержащий прямоугольник, поделенный на четыре треугольника](images/fillrule.png)

Для обоих прямоугольников задействуются те же пиксели, как показано на следующем рисунке.

![пиксели, задействованные в двух предыдущих пронумерованных квадратах](images/samepix.png)

## <a name="span-idpointandlinerulesspanspan-idpointandlinerulesspanspan-idpointandlinerulesspanpoint-and-line-rules"></a><span id="Point_and_Line_Rules"></span><span id="point_and_line_rules"></span><span id="POINT_AND_LINE_RULES"></span>Точка и правилах линии


Точки отрисовываются так же, как и точечные спрайты, то есть оба эти вида элементов отрисовываются как выровненные по экрану четырехгранники, и, соответственно, на них распространяются те же правила, что и на отрисовку многоугольников.

Правила отрисовки линий без сглаживания являются точно такими же, что и для [линий GDI](https://docs.microsoft.com/windows/desktop/gdi/lines).

## <a name="span-idpointspriterulesspanspan-idpointspriterulesspanspan-idpointspriterulesspanpoint-sprite-rules"></a><span id="Point_Sprite_Rules"></span><span id="point_sprite_rules"></span><span id="POINT_SPRITE_RULES"></span>Правила спрайт точки


Точечные спрайты и фрагментарные примитивы подвергаются растеризации таким образом, как если бы эти примитивы сначала рассекались на треугольники и затем проводилась бы растеризация полученных треугольников.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Устройства](devices.md)

[Этап растеризации (RS)](rasterizer-stage--rs-.md)

[Правила растеризации](rasterization-rules.md)

 

 




