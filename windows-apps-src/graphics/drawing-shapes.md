---
ms.assetid: 54CC0BD4-1961-44D7-AB40-6E8B58E42D65
title: Рисование фигур
description: Узнайте, как рисовать фигуры — эллипсы, прямоугольники, многоугольники и пути. При помощи класса Path в пользовательском интерфейсе XAML можно применять довольно сложный язык для рисования на основе векторов, например рисовать кривые Безье.
---
# Рисование фигур

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


** Важные API **

-   [**Путь**](https://msdn.microsoft.com/library/windows/apps/BR243355)
-   [**Пространство имен Windows.UI.Xaml.Shapes**](https://msdn.microsoft.com/library/windows/apps/BR243401)
-   [**Пространство имен Windows.UI.Xaml.Media**](https://msdn.microsoft.com/library/windows/apps/BR243045)

Узнайте, как рисовать фигуры — эллипсы, прямоугольники, многоугольники и пути. При помощи класса [**Path**](https://msdn.microsoft.com/library/windows/apps/BR243355) в пользовательском интерфейсе XAML можно применять довольно сложный язык для рисования на основе векторов, например рисовать кривые Безье.

## Введение

Область пространства в пользовательском интерфейсе XAML определяется двумя наборами классов: [**Shape**](https://msdn.microsoft.com/library/windows/apps/BR243377) и [**Geometry**](https://msdn.microsoft.com/library/windows/apps/BR210041). Главное различие между этими классами заключается в том, что у класса **Shape** имеется связанная с ним кисть, и он может быть отрисован на экране, а класс **Geometry** просто определяет область и не отрисовывается на экране, если не содержит сведений для другого свойства пользовательского интерфейса. Объект **Shape** можно представить как элемент [**UIElement**](https://msdn.microsoft.com/library/windows/apps/BR208911), граница которого определена объектом **Geometry**. В этом разделе содержатся в основном сведения о классах **Shape**.

Классы [**Shape**](https://msdn.microsoft.com/library/windows/apps/BR243377): [**Line**](https://msdn.microsoft.com/library/windows/apps/BR243345), [**Ellipse**](https://msdn.microsoft.com/library/windows/apps/BR243343), [**Rectangle**](https://msdn.microsoft.com/library/windows/apps/BR243371), [**Polygon**](https://msdn.microsoft.com/library/windows/apps/BR243359), [**Polyline**](https://msdn.microsoft.com/library/windows/apps/BR243365) и [**Path**](https://msdn.microsoft.com/library/windows/apps/BR243355). Класс **Path** интересен тем, что с его помощью можно описать произвольное геометрическое тело, а класс [**Geometry**](https://msdn.microsoft.com/library/windows/apps/BR210041) используется для того, чтобы определить части класса **Path**.

## Функции Fill (заливка) и Stroke (росчерк) для фигур

Чтобы объект [**Shape**](https://msdn.microsoft.com/library/windows/apps/BR243377) можно было отрисовать на холсте приложения, необходимо сопоставить с ним класс [**Brush**](https://msdn.microsoft.com/library/windows/apps/BR228076). Установите для свойства [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill) объекта **Shape** нужное значение **Brush**. Подробнее о кистях см. в статье [Использование кистей](using-brushes.md).

Кроме того, объект [**Shape**](https://msdn.microsoft.com/library/windows/apps/BR243377) также может иметь свойство [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke), представляющее собой линию, нарисованную по периметру фигуры. Для свойства **Stroke** также необходим класс [**Brush**](https://msdn.microsoft.com/library/windows/apps/BR228076), определяющий его внешний вид, а свойству [**StrokeThickness**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.strokethickness) необходимо присвоить ненулевое значение. **StrokeThickness** — это свойство, которое определяет толщину периметра, ограничивающего фигуру. Если значение **Brush** для свойства **Stroke** не задано или задано нулевое значение **StrokeThickness**, граница вокруг фигуры не будет нарисована.

## Эллипс

[
            **Ellipse**](https://msdn.microsoft.com/library/windows/apps/BR243343) (эллипс) — это фигура с закругленным периметром. Чтобы создать обычную фигуру **Ellipse**, укажите значения [**Width**](https://msdn.microsoft.com/library/windows/apps/BR208751), [**Height**](https://msdn.microsoft.com/library/windows/apps/BR208718) и [**Brush**](https://msdn.microsoft.com/library/windows/apps/BR228076) для [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill).

В следующем примере создается фигура [**Ellipse**](https://msdn.microsoft.com/library/windows/apps/BR243343) со значением [**Width**](https://msdn.microsoft.com/library/windows/apps/BR208751), равным 200, и [**Height**](https://msdn.microsoft.com/library/windows/apps/BR208718), также равным 200, а также используются значения [**SteelBlue**](https://msdn.microsoft.com/library/windows/apps/Hh748056) и [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/BR242962) для заливки ([**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill)).

```xml
<Ellipse Fill="SteelBlue" Height="200" Width="200" />
```

Ниже показан отрисованный объект [**Ellipse**](https://msdn.microsoft.com/library/windows/apps/BR243343).

![Обработанный эллипс.](images/shapes-ellipse.jpg)

В данном случае большинство людей сочтут фигуру [**Ellipse**](https://msdn.microsoft.com/library/windows/apps/BR243343) кругом, но именно так определяется круг в языке XAML: используется фигура **Ellipse**, где значения [**Width**](https://msdn.microsoft.com/library/windows/apps/BR208751) и [**Height**](https://msdn.microsoft.com/library/windows/apps/BR208718) равны.

Когда фигура [**Ellipse**](https://msdn.microsoft.com/library/windows/apps/BR243343) размещается в макете интерфейса, ее размер принимается равным размеру прямоугольника со сторонами [**Width**](https://msdn.microsoft.com/library/windows/apps/BR208751) и [**Height**](https://msdn.microsoft.com/library/windows/apps/BR208718). Область за пределами периметра не отрисовывается, но все равно считается частью пространства макета.

Набор из 6 элементов [**Ellipse**](https://msdn.microsoft.com/library/windows/apps/BR243343) входит в состав шаблона элемента управления [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/BR227538), а 2 концентрических элемента **Ellipse** являются частью [**RadioButton**](https://msdn.microsoft.com/library/windows/apps/BR227544).

## <span id="Rectangle"></span><span id="rectangle"></span><span id="RECTANGLE"></span>Прямоугольник

[
            **Rectangle**](https://msdn.microsoft.com/library/windows/apps/BR243371) (прямоугольник) — это фигура с четырьмя сторонами, противоположные стороны которой равны. Чтобы создать обычную фигуру **Rectangle**, укажите значения [**Width**](https://msdn.microsoft.com/library/windows/apps/BR208751), [**Height**](https://msdn.microsoft.com/library/windows/apps/BR208718) и [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill).

Углы [**Rectangle**](https://msdn.microsoft.com/library/windows/apps/BR243371) можно скруглить. Чтобы создать скругленные углы, задайте значения для свойств [**RadiusX**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.rectangle.radiusx.aspx) и [**RadiusY**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.rectangle.radiusy). Эти свойства указывают оси x и y эллипса, определяющего скругление углов. Максимально допустимое значение свойства **RadiusX** — это значение [**Width**](https://msdn.microsoft.com/library/windows/apps/BR208751), разделенное на два, а максимально возможное значение **RadiusY** — это значение [**Height**](https://msdn.microsoft.com/library/windows/apps/BR208718), разделенное на два.

В следующем примере создается фигура [**Rectangle**](https://msdn.microsoft.com/library/windows/apps/BR243371), у которой значение [**Width**](https://msdn.microsoft.com/library/windows/apps/BR208751) равно 200, а [**Height**](https://msdn.microsoft.com/library/windows/apps/BR208718) — 100. Она использует значение [**Blue**](https://msdn.microsoft.com/library/windows/apps/Hh747837) свойства [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/BR242962) для своего свойства [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill) и значение [**Black**](https://msdn.microsoft.com/library/windows/apps/Hh747833) свойства **SolidColorBrush** для своего свойства [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke). Мы задаем для свойства [**StrokeThickness**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.strokethickness) значение 3. Задаем для свойства [**RadiusX**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.rectangle.radiusx.aspx) значение 50, а для свойства [**RadiusY**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.rectangle.radiusy) — значение 10, что определяет для фигуры **Rectangle** скругленные углы.

```xml
<Rectangle Fill="Blue"
           Width="200"
           Height="100"
           Stroke="Black"
           StrokeThickness="3"
           RadiusX="50"
           RadiusY="10" />
           ```

Here's the rendered [**Rectangle**](https://msdn.microsoft.com/library/windows/apps/BR243371).

![A rendered Rectangle.](images/shapes-rectangle.jpg)

**Tip**  There are some scenarios for UI definitions where instead of using a [**Rectangle**](https://msdn.microsoft.com/library/windows/apps/BR243371), a [**Border**](https://msdn.microsoft.com/library/windows/apps/BR209250) might be more appropriate. If your intention is to create a rectangle shape around other content, it might be better to use **Border** because it can have child content and will automatically size around that content, rather than using the fixed dimensions for height and width like **Rectangle** does. A **Border** also has the option of having rounded corners if you set the [**CornerRadius**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.border.cornerradius) property.

 

On the other hand, a [**Rectangle**](https://msdn.microsoft.com/library/windows/apps/BR243371) is probably a better choice for control composition. A **Rectangle** shape is seen in many control templates because it's used as a "FocusVisual" part for focusable controls. Whenever the control is in a "Focused" visual state, this rectangle is made visible, in other states it's hidden.

## Polygon

A [**Polygon**](https://msdn.microsoft.com/library/windows/apps/BR243359) is a shape with a boundary defined by an arbitrary number of points. The boundary is created by connecting a line from one point to the next, with the last point connected to the first point. The [**Points**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.polygon.points.aspx) property defines the collection of points that make up the boundary. In XAML, you define the points with a comma-separated list. In code-behind you use a [**PointCollection**](https://msdn.microsoft.com/library/windows/apps/BR210220) to define the points and you add each individual point as a [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870) value to the collection.

You don't need to explicitly declare the points such that the start point and end point are both specified as the same [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870) value. The rendering logic for a [**Polygon**](https://msdn.microsoft.com/library/windows/apps/BR243359) assumes that you are defining a closed shape and will connect the end point to the start point implicitly.

The next example creates a [**Polygon**](https://msdn.microsoft.com/library/windows/apps/BR243359) with 4 points set to `(10,200)`, `(60,140)`, `(130,140)`, and `(180,200)`. It uses a [**LightBlue**](https://msdn.microsoft.com/library/windows/apps/Hh747960) value of [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/BR242962) for its [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill), and has no value for [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke) so it has no perimeter outline.

```xml
<Polygon Fill="LightBlue"
         Points="10,200,60,140,130,140,180,200" />
```

Ниже показан отрисованный объект [**Polygon**](https://msdn.microsoft.com/library/windows/apps/BR243359).

![Обработанный многоугольник.](images/shapes-polygon.jpg)

**Совет.** Значение [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870) часто используется в качестве типа в XAML для сценариев, в которых не объявляются вершины фигур. Например, **Point** входит в состав данных событий сенсорного ввода, что позволяет точно определить, где в системе координат произошло действие касания. Подробнее о параметре **Point** и его использовании в XAML или коде см. в справочных статьях по API для [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870).

 

## Линия

[
            **Line**](https://msdn.microsoft.com/library/windows/apps/BR243345) (линия) — это линия, проведенная между двумя точками в координатном пространстве. Объект **Line** игнорирует значение параметра [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill), так как не имеет внутреннего пространства. Для объекта **Line** нужно задать значения свойств [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke) и [**StrokeThickness**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.strokethickness), так как в противном случае объект **Line** не будет отрисован.

Не следует использовать значения [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870) для определения фигуры [**Line**](https://msdn.microsoft.com/library/windows/apps/BR243345). Вместо этого задайте отдельные значения [**Double**](https://msdn.microsoft.com/library/windows/apps/xaml/system.double.aspx) для параметров [**X1**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.line.x1.aspx), [**Y1**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.line.y1.aspx), [**X2**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.line.x2.aspx) и [**Y2**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.line.y2.aspx). Это позволяет сократить разметку для горизонтальных и вертикальных линий. Например, код `<Line Stroke="Red" X2="400"/>` определяет горизонтальную линию длиной 400 пикселей. Другие значения X,Y равны 0 по умолчанию, поэтому в отношении точек этот код XAML определяет линию от точки `(0,0)` до точки `(400,0)`. Затем можно использовать [**TranslateTransform**](https://msdn.microsoft.com/library/windows/apps/BR243027) для перемещения **Line** в целом, если необходимо начать с другой точки, отличной от (0,0).

## <span id="_Polyline"></span><span id="_polyline"></span><span id="_POLYLINE"></span> Ломаная линия

Фигура [**Polyline**](https://msdn.microsoft.com/library/windows/apps/BR243365) похожа на фигуру [**Polygon**](https://msdn.microsoft.com/library/windows/apps/BR243359) тем, что ее граница также определяется набором точек, но последняя точка в **Polyline** не соединяется с первой.

**Примечание.** Вы можете задать в явном виде одну и ту же точку в качестве начальной и конечной точки в наборе [**Points**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.polyline.points.aspx) для получения фигуры [**Polyline**](https://msdn.microsoft.com/library/windows/apps/BR243365), но лучше для этого использовать объект [**Polygon**](https://msdn.microsoft.com/library/windows/apps/BR243359).

 

Если вы задаете параметр [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill) для фигуры [**Polyline**](https://msdn.microsoft.com/library/windows/apps/BR243365), свойство **Fill** закрасит внутреннее пространство фигуры даже в том случае, если начальная и конечная точки в наборе [**Points**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.polyline.points.aspx) для фигуры **Polyline** не совпадают. Если вы не зададите параметр **Fill**, фигура **Polyline** будет напоминать комбинацию из нескольких отдельных элементов [**Line**](https://msdn.microsoft.com/library/windows/apps/BR243345), у которых начальные и конечные точки соседних отрезков линии совпадают.

Как и в случае фигуры [**Polygon**](https://msdn.microsoft.com/library/windows/apps/BR243359), свойство [**Points**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.shapes.polyline.points.aspx) определяет коллекцию точек, образующих границу. В языке XAML точки определяются с помощью списка с разделителями-запятыми. В коде программной части мы используем [**PointCollection**](https://msdn.microsoft.com/library/windows/apps/BR210220) для определения точек, а затем добавляем каждую отдельную точку в коллекцию как структуру [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870).

В этом примере создается фигура [**Polyline**](https://msdn.microsoft.com/library/windows/apps/BR243365) по четырем точкам, заданным координатами `(10,200)`, `(60,140)`, `(130,140)` и `(180,200)`. Свойство [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke) определено, а [**Fill**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.fill) — нет.

```xml
<Polyline Stroke="Black"
        StrokeThickness="4"
        Points="10,200,60,140,130,140,180,200" />
```

Ниже показан отрисованный объект [**Polyline**](https://msdn.microsoft.com/library/windows/apps/BR243365). Обратите внимание, что первая и последняя точки не соединены контуром [**Stroke**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.shape.stroke), как это сделано в фигуре [**Polygon**](https://msdn.microsoft.com/library/windows/apps/BR243359).

![Обработанная ломаная линия.](images/shapes-polyline.jpg)

## Путь

Фигура [**Path**](https://msdn.microsoft.com/library/windows/apps/BR243355) (путь) является наиболее универсальной из всех фигур [**Shape**](https://msdn.microsoft.com/library/windows/apps/BR243377). С ее помощью можно создать произвольную геометрию. Но эта универсальность порождает сложность. Давайте посмотрим, как создать простую фигуру **Path** на языке XAML.

Геометрия пути определяется с помощью свойства [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data). Есть два способа определения **Data**.

-   Для свойства [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data) можно задать строковое значение на языке XAML. В этой форме значение **Path.Data** использует формат сериализации для графики. Это значение обычно не редактируется как текст после того, как будет первый раз определено. Вместо этого используются средства конструирования, позволяющие работать в режиме конструирования или рисования на поверхности. Затем следует сохранить или экспортировать полученные данные, в результате чего будет создан файл или фрагмент строки XAML с информацией о **Path.Data**.
-   Можно назначить свойство [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data) отдельному объекту [**Geometry**](https://msdn.microsoft.com/library/windows/apps/BR210041). Это можно сделать с помощью кода или XAML. Этот отдельный объект **Geometry** является, как правило, объектом [**GeometryGroup**](https://msdn.microsoft.com/library/windows/apps/BR210041group), который выполняет роль контейнера, объединяющего множество геометрических определений в один объект для целей объектной модели. Самая распространенная причина таких действий заключается в том, что нужно использовать одну или несколько кривых и сложных фигур, которые можно определить как значения [**Segments**](https://msdn.microsoft.com/library/windows/apps/BR210164) для объекта [**PathFigure**](https://msdn.microsoft.com/library/windows/apps/BR210143), например [**BezierSegment**](https://msdn.microsoft.com/library/windows/apps/BR228068).

В этом примере показан объект [**Path**](https://msdn.microsoft.com/library/windows/apps/BR243355), который можно получить с помощью Blend для Visual Studio путем создания нескольких векторных фигур с последующим сохранением результата в XAML. Общий объект **Path** содержит сегмент кривой Безье и сегмент прямой. Этот пример служит иллюстрацией того, какие элементы существуют в формате сериализации [**Path.Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data) и что представляют числа.

Это свойство [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data) начинается с команды перемещения, отмеченной буквой M, которая определяет абсолютную начальную точку пути.

Первый сегмент — кривая Безье третьего порядка, которая начинается в точке `(100,200)` и заканчивается в точке `(400,175)`. Чтобы начертить ее, используются две контрольные точки `(100,25)` и `(400,350)`. Этот сегмент отмечен командой C в строке атрибута [**Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data).

Второй сегмент начинается с команды абсолютно горизонтальной линии H, которая определяет линию, нарисованную от конечной точки предыдущего отрезка пути `(400,175)` до новой конечной точки `(280,175)`. Так как это команда горизонтальной линии, указанным значением является координата по оси Х.

```xml
<Path Stroke="DarkGoldenRod" 
      StrokeThickness="3"
      Data="M 100,200 C 100,25 400,350 400,175 H 280" />
      ```

Here's the rendered [**Path**](https://msdn.microsoft.com/library/windows/apps/BR243355).

![A rendered Path.](images/shapes-path.jpg)

The next example shows a usage of the other technique we discussed: a [**GeometryGroup**](https://msdn.microsoft.com/library/windows/apps/BR210041group) with a [**PathGeometry**](https://msdn.microsoft.com/library/windows/apps/BR210168). This example exercises some of the contributing geometry types that can be used as part of a **PathGeometry**: [**PathFigure**](https://msdn.microsoft.com/library/windows/apps/BR210143) and the various elements that can be a segment in [**PathFigure.Segments**](https://msdn.microsoft.com/library/windows/apps/BR210164).

```xml
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

Причиной, по которой, возможно, используется [**PathGeometry**](https://msdn.microsoft.com/library/windows/apps/BR210168) с различными частями, является то, что каждая часть содержит свойства **Double** и **Point**, которые в перспективе могут использоваться для анимации пользовательского интерфейса. Это невозможно сделать с помощью формы сериализации [**Path.Data**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.path.data). Подробнее: [Раскадрованные анимации](storyboarded-animations.md).

 

 






<!--HONumber=Mar16_HO1-->


