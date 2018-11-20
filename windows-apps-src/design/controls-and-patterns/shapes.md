---
author: Jwmsft
ms.assetid: 54CC0BD4-1961-44D7-AB40-6E8B58E42D65
title: Рисование фигур
description: Узнайте, как рисовать фигуры— эллипсы, прямоугольники, многоугольники и пути. При помощи класса Path в пользовательском интерфейсе XAML можно применять довольно сложный язык для рисования на основе векторов, например рисовать кривые Безье.
ms.author: jimwalk
ms.date: 11/16/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 984653ad20fc40035528ab7e32b904e64d6ff8c5
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7304986"
---
# <a name="draw-shapes"></a>Рисование фигур

Узнайте, как рисовать фигуры— эллипсы, прямоугольники, многоугольники и пути. При помощи класса [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path) в пользовательском интерфейсе XAML можно применять довольно сложный язык для рисования на основе векторов, например рисовать кривые Безье.

> **Важные API-интерфейсы**: [класс Path](/uwp/api/Windows.UI.Xaml.Shapes.Path), [пространство имен Windows.UI.Xaml.Shapes](/uwp/api/Windows.UI.Xaml.Shapes), [пространство имен Windows.UI.Xaml.Media](/uwp/api/Windows.UI.Xaml.Media)


Область пространства в пользовательском интерфейсе XAML определяется двумя наборами классов: [**Shape**](/uwp/api/Windows.UI.Xaml.Shapes.Shape) и [**Geometry**](/uwp/api/Windows.UI.Xaml.Media.Geometry). Главное различие между этими классами заключается в том, что у класса **Shape** имеется связанная с ним кисть, и он может быть отрисован на экране, а класс **Geometry** просто определяет область и не отрисовывается на экране, если не содержит сведений для другого свойства пользовательского интерфейса. Объект **Shape** можно представить как элемент [**UIElement**](https://msdn.microsoft.com/library/windows/apps/BR208911), граница которого определена объектом **Geometry**. В этом разделе содержатся в основном сведения о классах **Shape**.

Классы [**Shape**](/uwp/api/Windows.UI.Xaml.Shapes.Shape): [**Line**](/uwp/api/Windows.UI.Xaml.Shapes.Line), [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse), [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle), [**Polygon**](/uwp/api/Windows.UI.Xaml.Shapes.Polygon), [**Polyline**](/uwp/api/Windows.UI.Xaml.Shapes.Polyline) и [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path). Класс **Path** интересен тем, что с его помощью можно описать произвольное геометрическое тело, а класс [**Geometry**](/uwp/api/Windows.UI.Xaml.Media.Geometry) используется для того, чтобы определить части класса **Path**.

## <a name="fill-and-stroke-for-shapes"></a>Функции Fill (заливка) и Stroke (росчерк) для фигур

Чтобы объект [**Shape**](/uwp/api/Windows.UI.Xaml.Shapes.Shape) можно было отрисовать на холсте приложения, необходимо сопоставить с ним класс [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush). Установите для свойства [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill) объекта **Shape** нужное значение **Brush**. Подробнее о кистях см. в статье [Использование кистей](../style/brushes.md).

Кроме того, объект [**Shape**](/uwp/api/Windows.UI.Xaml.Shapes.Shape) также может иметь свойство [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke), представляющее собой линию, нарисованную по периметру фигуры. Для свойства **Stroke** также необходим класс [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush), определяющий его внешний вид, а свойству [**StrokeThickness**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.strokethickness) необходимо присвоить ненулевое значение. **StrokeThickness** — это свойство, которое определяет толщину периметра, ограничивающего фигуру. Если значение **Brush** для свойства **Stroke** не задано или задано нулевое значение **StrokeThickness**, граница вокруг фигуры не будет нарисована.

## <a name="ellipse"></a>Эллипс

[**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) (эллипс)— это фигура с закругленным периметром. Чтобы создать обычную фигуру **Ellipse**, укажите значения [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width), [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) для [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill).

В следующем примере создается фигура [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) со значением [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width), равным 200, и [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height), также равным 200, а также используются значения [**SteelBlue**](https://msdn.microsoft.com/library/windows/apps/Hh748056) и [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/BR242962) для заливки ([**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill)).

```xaml
<Ellipse Fill="SteelBlue" Height="200" Width="200" />
```

```csharp
var ellipse1 = new Ellipse();
ellipse1.Fill = new SolidColorBrush(Windows.UI.Colors.SteelBlue);
ellipse1.Width = 200;
ellipse1.Height = 200;

// When you create a XAML element in code, you have to add
// it to the XAML visual tree. This example assumes you have
// a panel named 'layoutRoot' in your XAML file, like this:
// <Grid x:Name="layoutRoot>
layoutRoot.Children.Add(ellipse1);
```

Ниже показан отрисованный объект [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse).

![Обработанный эллипс.](images/shapes-ellipse.jpg)

В данном случае большинство людей сочтут фигуру [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) кругом, но именно так определяется круг в языке XAML: используется фигура **Ellipse**, где значения [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) и [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) равны.

Когда фигура [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) размещается в макете интерфейса, ее размер принимается равным размеру прямоугольника со сторонами [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) и [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height). Область за пределами периметра не отрисовывается, но все равно считается частью пространства макета.

Набор из 6 элементов [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) входит в состав шаблона элемента управления [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/BR227538), а 2 концентрических элемента **Ellipse** являются частью [**RadioButton**](https://msdn.microsoft.com/library/windows/apps/BR227544).

## <a name="span-idrectanglespanspan-idrectanglespanspan-idrectanglespanrectangle"></a><span id="Rectangle"></span><span id="rectangle"></span><span id="RECTANGLE"></span>Rectangle

[**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) (прямоугольник)— это фигура с четырьмя сторонами, противоположные стороны которой равны. Чтобы создать обычную фигуру **Rectangle**, укажите значения [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width), [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill).

Углы [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) можно скруглить. Чтобы создать скругленные углы, задайте значения для свойств [**RadiusX**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.rectangle.radiusx.aspx) и [**RadiusY**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.rectangle.radiusy). Эти свойства указывают оси x и y эллипса, определяющего скругление углов. Максимально допустимое значение свойства **RadiusX** — это значение [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width), разделенное на два, а максимально возможное значение **RadiusY** — это значение [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height), разделенное на два.

В следующем примере создается фигура [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle), у которой значение [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) равно 200, а [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) — 100. Она использует значение [**Blue**](https://msdn.microsoft.com/library/windows/apps/Hh747837) свойства [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/BR242962) для своего свойства [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill) и значение [**Black**](https://msdn.microsoft.com/library/windows/apps/Hh747833) свойства **SolidColorBrush** для своего свойства [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke). Мы задаем для свойства [**StrokeThickness**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.strokethickness) значение3. Задаем для свойства [**RadiusX**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.rectangle.radiusx.aspx) значение 50, а для свойства [**RadiusY**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.rectangle.radiusy)— значение10, что определяет для фигуры **Rectangle** скругленные углы.

```xaml
<Rectangle Fill="Blue"
           Width="200"
           Height="100"
           Stroke="Black"
           StrokeThickness="3"
           RadiusX="50"
           RadiusY="10" />
```

```csharp
var rectangle1 = new Rectangle();
rectangle1.Fill = new SolidColorBrush(Windows.UI.Colors.Blue);
rectangle1.Width = 200;
rectangle1.Height = 100;
rectangle1.Stroke = new SolidColorBrush(Windows.UI.Colors.Black);
rectangle1.StrokeThickness = 3;
rectangle1.RadiusX = 50;
rectangle1.RadiusY = 10;

// When you create a XAML element in code, you have to add
// it to the XAML visual tree. This example assumes you have
// a panel named 'layoutRoot' in your XAML file, like this:
// <Grid x:Name="layoutRoot>
layoutRoot.Children.Add(rectangle1);
```

Далее показана отрисованная фигура [**Прямоугольник**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle).

![Обработанный прямоугольник.](images/shapes-rectangle.jpg)

**Совет**существуют определенные сценарии для определения пользовательского интерфейса, где вместо [**прямоугольника**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle), [**границы**](https://msdn.microsoft.com/library/windows/apps/BR209250) более подходящим будет. Если вы хотите создать прямоугольную фигуру вокруг некоторого содержимого, удобнее использовать объект **Border**, так как он может иметь дочернее содержимое и его размеры автоматически настраиваются по размеру содержимого, благодаря чему отпадает необходимость задавать конкретные ширину и высоту прямоугольника, как в случае с **Rectangle**. Объект **Border** можно также создавать с закругленными углами, задав значение свойства [**CornerRadius**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.border.cornerradius).

С другой стороны, объект [**Прямоугольник**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) , вероятно, представляет собой лучший вариант для компоновки элементов управления. Фигура **Rectangle** отображается во множестве шаблонов элементов управления, так как она используется как часть "FocusVisual" для фокусируемых элементов управления. Каждый раз, когда элемент управления находится в визуальном состоянии с фокусом ввода, этот прямоугольник становится видимым, в других состояниях он скрыт.

## <a name="polygon"></a>Многоугольник

[**Polygon**](/uwp/api/Windows.UI.Xaml.Shapes.Polygon) — это фигура, имеющая границу, определяемую произвольным числом точек. Граница создается путем проведения линии от одной точки к другой и соединения последней точки с первой. Свойство [**Points**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.polygon.points.aspx) определяет коллекцию точек, образующих границу. В языке XAML точки определяются с помощью списка с разделителями-запятыми. В коде программной части мы используем для определения точек объект [**PointCollection**](https://msdn.microsoft.com/library/windows/apps/BR210220), где каждая точка добавляется в коллекцию как значение [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870).

Вам не нужно в явном виде объявлять начальную и конечную точки, так как обе они определяются одним значением [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870). В логике отрисовки [**Многоугольник**](/uwp/api/Windows.UI.Xaml.Shapes.Polygon) предполагается, что задана замкнутая фигура, в которой начальная точка объединяется с конечной точкой.

В следующем примере создается фигура [**Многоугольник**](/uwp/api/Windows.UI.Xaml.Shapes.Polygon) по 4 точкам, заданным координатами `(10,200)`, `(60,140)`, `(130,140)`и `(180,200)`. Здесь используется значение [**LightBlue**](https://msdn.microsoft.com/library/windows/apps/Hh747960) для параметра [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/BR242962) в свойстве [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill) и не задано значение [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke), поэтому фигура рисуется без контура по периметру.

```xaml
<Polygon Fill="LightBlue"
         Points="10,200,60,140,130,140,180,200" />
```

```csharp
var polygon1 = new Polygon();
polygon1.Fill = new SolidColorBrush(Windows.UI.Colors.LightBlue);

var points = new PointCollection();
points.Add(new Windows.Foundation.Point(10, 200));
points.Add(new Windows.Foundation.Point(60, 140));
points.Add(new Windows.Foundation.Point(130, 140));
points.Add(new Windows.Foundation.Point(180, 200));
polygon1.Points = points;

// When you create a XAML element in code, you have to add
// it to the XAML visual tree. This example assumes you have
// a panel named 'layoutRoot' in your XAML file, like this:
// <Grid x:Name="layoutRoot>
layoutRoot.Children.Add(polygon1);
```

Ниже показан отрисованный объект [**Polygon**](/uwp/api/Windows.UI.Xaml.Shapes.Polygon).

![Обработанный многоугольник.](images/shapes-polygon.jpg)

**Совет**значением [**точки**](https://msdn.microsoft.com/library/windows/apps/BR225870) часто используется в качестве типа в XAML для сценариев не объявляются вершины фигур. Например, **Point** входит в состав данных событий сенсорного ввода, что позволяет точно определить, где в системе координат произошло действие касания. Подробнее о параметре **Point** и его использовании в XAML или коде см. в справочных статьях по API для [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870).

## <a name="line"></a>Линия

[**Line**](/uwp/api/Windows.UI.Xaml.Shapes.Line) (линия) — это линия, проведенная между двумя точками в координатном пространстве. Объект **Line** игнорирует значение параметра [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill), так как не имеет внутреннего пространства. Для объекта **Line** нужно задать значения свойств [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke) и [**StrokeThickness**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.strokethickness), так как в противном случае объект **Line** не будет отрисован.

Не следует использовать значения [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870) для определения фигуры [**Line**](/uwp/api/Windows.UI.Xaml.Shapes.Line). Вместо этого задайте отдельные значения [**Double**](https://msdn.microsoft.com/library/windows/apps/xaml/system.double.aspx) для параметров [**X1**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.line.x1.aspx), [**Y1**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.line.y1.aspx), [**X2**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.line.x2.aspx) и [**Y2**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.line.y2.aspx). Это позволяет сократить разметку для горизонтальных и вертикальных линий. Например, код `<Line Stroke="Red" X2="400"/>` определяет горизонтальную линию длиной 400 пикселей. Другие значения X,Y равны 0 по умолчанию, поэтому в отношении точек этот код XAML определяет линию от точки `(0,0)` до точки `(400,0)`. Затем можно использовать [**TranslateTransform**](https://msdn.microsoft.com/library/windows/apps/BR243027) для перемещения **Line** в целом, если необходимо начать с другой точки, отличной от (0,0).

```xaml
<Line Stroke="Red" X2="400"/>
```

```csharp
var line1 = new Line();
line1.Stroke = new SolidColorBrush(Windows.UI.Colors.Red);
line1.X2 = 400;

// When you create a XAML element in code, you have to add
// it to the XAML visual tree. This example assumes you have
// a panel named 'layoutRoot' in your XAML file, like this:
// <Grid x:Name="layoutRoot>
layoutRoot.Children.Add(line1);
```

## <a name="span-idpolylinespanspan-idpolylinespanspan-idpolylinespan-polyline"></a><span id="_Polyline"></span><span id="_polyline"></span><span id="_POLYLINE"></span> Ломаная линия

Фигура [**Polyline**](/uwp/api/Windows.UI.Xaml.Shapes.Polyline) похожа на фигуру [**Polygon**](/uwp/api/Windows.UI.Xaml.Shapes.Polygon) тем, что ее граница также определяется набором точек, но последняя точка в **Polyline** не соединяется с первой.

**Примечание**  явным образом может быть точку так же, как меню "Пуск" и настроить конечную точку в [**точки**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.polyline.points.aspx) для [**ломаной линии**](/uwp/api/Windows.UI.Xaml.Shapes.Polyline), но в этом случае можно использовать [**многоугольник**](/uwp/api/Windows.UI.Xaml.Shapes.Polygon) вместо.

Если вы задаете параметр [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill) для фигуры [**Polyline**](/uwp/api/Windows.UI.Xaml.Shapes.Polyline), свойство **Fill** закрасит внутреннее пространство фигуры даже в том случае, если начальная и конечная точки в наборе [**Points**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.polyline.points.aspx) для фигуры **Polyline** не совпадают. Если вы не зададите параметр **Fill**, фигура **Polyline** будет напоминать комбинацию из нескольких отдельных элементов [**Line**](/uwp/api/Windows.UI.Xaml.Shapes.Line), у которых начальные и конечные точки соседних отрезков линии совпадают.

Как и в случае фигуры [**Polygon**](/uwp/api/Windows.UI.Xaml.Shapes.Polygon), свойство [**Points**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.polyline.points.aspx) определяет коллекцию точек, образующих границу. В языке XAML точки определяются с помощью списка с разделителями-запятыми. В коде программной части мы используем [**PointCollection**](https://msdn.microsoft.com/library/windows/apps/BR210220) для определения точек, а затем добавляем каждую отдельную точку в коллекцию как структуру [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870).

В этом примере создается фигура [**Polyline**](/uwp/api/Windows.UI.Xaml.Shapes.Polyline) по четырем точкам, заданным координатами `(10,200)`, `(60,140)`, `(130,140)` и `(180,200)`. Свойство [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke) определено, а [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill)— нет.

```xaml
<Polyline Stroke="Black"
        StrokeThickness="4"
        Points="10,200,60,140,130,140,180,200" />
```

```csharp
var polyline1 = new Polyline();
polyline1.Stroke = new SolidColorBrush(Windows.UI.Colors.Black);
polyline1.StrokeThickness = 4;

var points = new PointCollection();
points.Add(new Windows.Foundation.Point(10, 200));
points.Add(new Windows.Foundation.Point(60, 140));
points.Add(new Windows.Foundation.Point(130, 140));
points.Add(new Windows.Foundation.Point(180, 200));
polyline1.Points = points;

// When you create a XAML element in code, you have to add
// it to the XAML visual tree. This example assumes you have
// a panel named 'layoutRoot' in your XAML file, like this:
// <Grid x:Name="layoutRoot>
layoutRoot.Children.Add(polyline1);
```

Ниже показан отрисованный объект [**Polyline**](/uwp/api/Windows.UI.Xaml.Shapes.Polyline). Обратите внимание, что первая и последняя точки не соединены контуром [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke), как это сделано в фигуре [**Polygon**](/uwp/api/Windows.UI.Xaml.Shapes.Polygon).

![Обработанная ломаная линия.](images/shapes-polyline.jpg)

## <a name="path"></a>Путь

Фигура [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path) (путь) является наиболее универсальной из всех фигур [**Shape**](/uwp/api/Windows.UI.Xaml.Shapes.Shape). С ее помощью можно создать произвольную геометрию. Но эта универсальность порождает сложность. Давайте посмотрим, как создать простую фигуру **Path** на языке XAML.

Геометрия пути определяется с помощью свойства [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data). Есть два способа определения **Data**.

- Для свойства [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data) можно задать строковое значение на языке XAML. В этой форме значение **Path.Data** использует формат сериализации для графики. Это значение обычно не редактируется как текст после того, как будет первый раз определено. Вместо этого используются средства конструирования, позволяющие работать в режиме конструирования или рисования на поверхности. Затем следует сохранить или экспортировать полученные данные, в результате чего будет создан файл или фрагмент строки XAML с информацией о **Path.Data**.
- Можно назначить свойство [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data) отдельному объекту [**Geometry**](/uwp/api/Windows.UI.Xaml.Media.Geometry). Это можно сделать с помощью кода или XAML. Этот отдельный объект **Geometry** является, как правило, объектом [**GeometryGroup**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.geometrygroup), который выполняет роль контейнера, объединяющего множество геометрических определений в один объект для целей объектной модели. Самая распространенная причина таких действий заключается в том, что нужно использовать одну или несколько кривых и сложных фигур, которые можно определить как значения [**Segments**](https://msdn.microsoft.com/library/windows/apps/BR210164) для объекта [**PathFigure**](https://msdn.microsoft.com/library/windows/apps/BR210143), например [**BezierSegment**](https://msdn.microsoft.com/library/windows/apps/BR228068).

В этом примере показан объект [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path), который можно получить с помощью Blend для Visual Studio путем создания нескольких векторных фигур с последующим сохранением результата в XAML. Общий объект **Path** содержит сегмент кривой Безье и сегмент прямой. Этот пример служит иллюстрацией того, какие элементы существуют в формате сериализации [**Path.Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data) и что представляют числа.

Это свойство [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data) начинается с команды перемещения, отмеченной буквой M, которая определяет абсолютную начальную точку пути.

Первый сегмент — кривая Безье третьего порядка, которая начинается в точке `(100,200)` и заканчивается в точке `(400,175)`. Чтобы начертить ее, используются две контрольные точки `(100,25)` и `(400,350)`. Этот сегмент отмечен командой C в строке атрибута [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data).

Второй сегмент начинается с команды абсолютно горизонтальной линии H, которая определяет линию, нарисованную от конечной точки предыдущего отрезка пути `(400,175)` до новой конечной точки `(280,175)`. Так как это команда горизонтальной линии, указанным значением является координата по оси Х.

```xaml
<Path Stroke="DarkGoldenRod" 
      StrokeThickness="3"
      Data="M 100,200 C 100,25 400,350 400,175 H 280" />
```

Далее показана отрисованная фигура [**Путь**](/uwp/api/Windows.UI.Xaml.Shapes.Path).

![Обработанный путь.](images/shapes-path.jpg)

В следующем примере показано применение другой вышеупомянутой технологии: [**GeometryGroup**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.geometrygroup) с [**PathGeometry**](https://msdn.microsoft.com/library/windows/apps/BR210168). В нем применяются некоторые геометрические типы в составе **PathGeometry**: [**PathFigure**](https://msdn.microsoft.com/library/windows/apps/BR210143) и различные элементы, которые могут быть сегментом в [**PathFigure.Segments**](https://msdn.microsoft.com/library/windows/apps/BR210164).

```xaml
<Path Stroke="Black" StrokeThickness="1" Fill="#CCCCFF">
    <Path.Data>
        <GeometryGroup>
            <RectangleGeometry Rect="50,5 100,10" />
            <RectangleGeometry Rect="5,5 95,180" />
            <EllipseGeometry Center="100, 100" RadiusX="20" RadiusY="30"/>
            <RectangleGeometry Rect="50,175 100,10" />
            <PathGeometry>
                <PathGeometry.Figures>
                    <PathFigureCollection>
                        <PathFigure IsClosed="true" StartPoint="50,50">
                            <PathFigure.Segments>
                                <PathSegmentCollection>
                                    <BezierSegment Point1="75,300" Point2="125,100" Point3="150,50"/>
                                    <BezierSegment Point1="125,300" Point2="75,100"  Point3="50,50"/>
                                </PathSegmentCollection>
                            </PathFigure.Segments>
                        </PathFigure>
                    </PathFigureCollection>
                </PathGeometry.Figures>
            </PathGeometry>
        </GeometryGroup>
    </Path.Data>
</Path>
```

```csharp
var path1 = new Windows.UI.Xaml.Shapes.Path();
path1.Fill = new SolidColorBrush(Windows.UI.Color.FromArgb(255, 204, 204, 255));
path1.Stroke = new SolidColorBrush(Windows.UI.Colors.Black);
path1.StrokeThickness = 1;

var geometryGroup1 = new GeometryGroup();
var rectangleGeometry1 = new RectangleGeometry();
rectangleGeometry1.Rect = new Rect(50, 5, 100, 10);
var rectangleGeometry2 = new RectangleGeometry();
rectangleGeometry2.Rect = new Rect(5, 5, 95, 180);
geometryGroup1.Children.Add(rectangleGeometry1);
geometryGroup1.Children.Add(rectangleGeometry2);

var ellipseGeometry1 = new EllipseGeometry();
ellipseGeometry1.Center = new Point(100, 100);
ellipseGeometry1.RadiusX = 20;
ellipseGeometry1.RadiusY = 30;
geometryGroup1.Children.Add(ellipseGeometry1);

var pathGeometry1 = new PathGeometry();
var pathFigureCollection1 = new PathFigureCollection();
var pathFigure1 = new PathFigure();
pathFigure1.IsClosed = true;
pathFigure1.StartPoint = new Windows.Foundation.Point(50, 50);
pathFigureCollection1.Add(pathFigure1);
pathGeometry1.Figures = pathFigureCollection1;

var pathSegmentCollection1 = new PathSegmentCollection();
var pathSegment1 = new BezierSegment();
pathSegment1.Point1 = new Point(75, 300);
pathSegment1.Point2 = new Point(125, 100);
pathSegment1.Point3 = new Point(150, 50);
pathSegmentCollection1.Add(pathSegment1);

var pathSegment2 = new BezierSegment();
pathSegment2.Point1 = new Point(125, 300);
pathSegment2.Point2 = new Point(75, 100);
pathSegment2.Point3 = new Point(50, 50);
pathSegmentCollection1.Add(pathSegment2);
pathFigure1.Segments = pathSegmentCollection1;

geometryGroup1.Children.Add(pathGeometry1);
path1.Data = geometryGroup1;

// When you create a XAML element in code, you have to add
// it to the XAML visual tree. This example assumes you have
// a panel named 'layoutRoot' in your XAML file, like this:
// <Grid x:Name="layoutRoot>
layoutRoot.Children.Add(path1);
```

Далее показана отрисованная фигура [**Путь**](/uwp/api/Windows.UI.Xaml.Shapes.Path).

![Обработанный путь.](images/shapes-path-2.png)

Использование [**PathGeometry**](https://msdn.microsoft.com/library/windows/apps/BR210168) может повысить читаемость по сравнению с заполнением строковой переменной [**Path.Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data). С другой стороны, [**Path.Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data) использует синтаксис, совместимый с определениями пути к изображению в формате масштабируемого векторного рисунка (SVG), поэтому он может быть полезен для переноса графики из SVG или в виде результата использования такого средства, как Blend.
