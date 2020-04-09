---
Description: Панели макета используются для группирования и упорядочивания элементов управления пользовательского интерфейса в приложении.
title: Панели макета для приложений универсальной платформы Windows (UWP)
ms.date: 04/02/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9322ba847aeb7eb64c2654e1105582478a0d3b47
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79210210"
---
# <a name="layout-panels"></a>Панели макета

Панели макета представляют собой контейнеры, которые позволяют упорядочивать и группировать элементы управления пользовательского интерфейса в приложении. Встроенные панели макета XAML включают [**RelativePanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RelativePanel), [**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel), [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid), [**VariableSizedWrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.VariableSizedWrapGrid) и [**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas). Здесь будет описана каждая панель, а также будет показано, как ее использовать для создания макета элементов пользовательского интерфейса XAML.

Существует несколько аспектов, которые необходимо учитывать при выборе панели макета.
- Способ расположения панелью своих дочерних элементов.
- Способ определения панелью размеров своих дочерних элементов.
- Характер перекрытия дочерних элементов, наложенных друг на друга (в z-порядке).
- Число и сложность вложенных элементов управления, необходимых для создания нужного макета.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если вы установили приложение <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong>, вы можете испытать элементы <a href="xamlcontrolsgallery:/item/RelativePanel">RelativePanel</a>, <a href="xamlcontrolsgallery:/item/StackPanel">StackPanel</a>, <a href="xamlcontrolsgallery:/item/Grid">Grid</a>, <a href="xamlcontrolsgallery:/item/VariableSizedWrapGrid">VariableSizedWrapGrid</a> и <a href="xamlcontrolsgallery:/item/Canvas">Canvas</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="panel-properties"></a>Свойства панелей

Прежде чем мы обсудим отдельные панели, давайте рассмотрим некоторые общие свойства, характерные для всех панелей. 

### <a name="panel-attached-properties"></a>Подключенные свойства панели

В большинстве панелей макета XAML используются присоединенные свойства, что позволяет их дочерним элементам сообщить родительской панели, как они должны быть расположены в пользовательском интерфейсе. Присоединенные свойства используют синтаксис *AttachedPropertyProvider.PropertyName*. При наличии панелей, вложенных в другие панели, присоединенные свойства элементов пользовательского интерфейса, которые описывают характеристики макета родительскому объекту, будут интерпретированы только ближайшей родительской панелью.

Ниже приведен пример задания присоединенного свойства [**Canvas.Left**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.left) в элементе управления «Кнопка» в XAML. Оно информирует родительский элемент Canvas о том, что кнопка Button должна располагаться в 50 эффективных пикселах от левого края Canvas.

```xaml
<Canvas>
  <Button Canvas.Left="50">Hello</Button>
</Canvas>
```

Подробнее о подключенных свойствах см. в разделе [Общие сведения о подключенных свойствах](../../xaml-platform/attached-properties-overview.md).

### <a name="panel-borders"></a>Границы панели

Панели RelativePanel, StackPanel и Grid определяют свойства границ, позволяющие начертить границу вокруг панели без обтекания в дополнительном элементе Border. Свойства границы: **BorderBrush**, **BorderThickness**, **CornerRadius** и **Padding**.

Вот пример задания свойств границ в Grid.

```xaml
<Grid BorderBrush="Blue" BorderThickness="12" CornerRadius="12" Padding="12">
    <TextBlock Text="Hello World!"/>
</Grid>
```

![Сетка с границами](images/layout-panel-grid-border.png)

Использование встроенных свойств границ уменьшает количество элементов XAML, что может повысить производительность пользовательского интерфейса приложения. Подробнее о панелях макета и производительности пользовательского интерфейса см. в разделе [Оптимизация макета XAML](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-your-xaml-layout).

## <a name="relativepanel"></a>RelativePanel

Элемент [ **RelativePanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RelativePanel) позволяет создать макет элементов пользовательского интерфейса путем указания их расположения относительно других элементов и панели. По умолчанию элемент располагается в левом верхнем углу панели. Можно использовать RelativePanel с [**VisualStateManager**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualStateManager) и [**AdaptiveTrigger**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.AdaptiveTrigger) для изменения макета пользовательского интерфейса для разных размеров окон.

В этой таблице показаны присоединяемые свойства, которые можно использовать для выравнивания элемента относительно панели и других элементов.

Согласование панели | Согласование с элементами одного уровня | Согласование с положением одного уровня
----------------|-------------------|-----------------
[**AlignTopWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.aligntopwithpanelproperty) | [**AlignTopWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.aligntopwithproperty) | [**Above**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel)  
[**AlignBottomWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignbottomwithpanelproperty) | [**AlignBottomWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignbottomwithproperty) | [**Below**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.belowproperty)  
[**AlignLeftWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel) | [**AlignLeftWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.getalignleftwith) | [**LeftOf**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.leftofproperty)  
[**AlignRightWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignrightwithpanelproperty) | [**AlignRightWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignrightwithproperty) | [**RightOf**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.setrightof)  
[**AlignHorizontalCenterWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignhorizontalcenterwithpanelproperty) | [**AlignHorizontalCenterWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignhorizontalcenterwithproperty) | &nbsp;   
[**AlignVerticalCenterWithPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignverticalcenterwithpanelproperty) | [**AlignVerticalCenterWith**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.relativepanel.alignverticalcenterwithproperty) | &nbsp;   

 
Этот фрагмент XAML-кода демонстрирует расположение элементов в RelativePanel.

```xaml
<RelativePanel BorderBrush="Gray" BorderThickness="1">
    <Rectangle x:Name="RedRect" Fill="Red" Height="44" Width="44"/>
    <Rectangle x:Name="BlueRect" Fill="Blue"
               Height="44" Width="88"
               RelativePanel.RightOf="RedRect" />

    <Rectangle x:Name="GreenRect" Fill="Green" 
               Height="44"
               RelativePanel.Below="RedRect" 
               RelativePanel.AlignLeftWith="RedRect" 
               RelativePanel.AlignRightWith="BlueRect"/>
    <Rectangle Fill="Orange"
               RelativePanel.Below="GreenRect" 
               RelativePanel.AlignLeftWith="BlueRect" 
               RelativePanel.AlignRightWithPanel="True"
               RelativePanel.AlignBottomWithPanel="True"/>
</RelativePanel>
```

Результат выглядит так: 

![Относительная панель](images/layout-panel-relative-panel.png)

Вот несколько аспектов, которые нужно учитывать при определении размеров прямоугольников.
- Для красного прямоугольника устанавливается точный размер 44x44. Он помещается в верхнем левом углу панели, что является положением по умолчанию.
- Для зеленого прямоугольника устанавливается точная высота 44. Его левая сторона выравнивается с красным прямоугольником, а его правая сторона — с синим прямоугольником, который определяет его ширину.
- Для оранжевого прямоугольника не устанавливается точный размер. Его левая сторона выравнивается с синим прямоугольником. Его правый и нижний края выравниваются с краем панели. Его размер определяется такими выравниваниями, и его размеры изменятся при изменении размеров панели.

## <a name="stackpanel"></a>StackPanel

Панель [**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) упорядочивает дочерние элементы в одной строке, которую можно расположить по горизонтали или вертикали. StackPanel обычно используется для упорядочивания небольших подразделов пользовательского интерфейса на странице.

Для указания направления дочерних элементов можно использовать свойство [**Orientation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.stackpanel.orientation). Ориентация по умолчанию — [**Vertical**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Orientation).

Следующий фрагмент кода XAML демонстрирует размещение элементов в вертикальном макете StackPanel.

```xaml
<StackPanel>
    <Rectangle Fill="Red" Height="44"/>
    <Rectangle Fill="Blue" Height="44"/>
    <Rectangle Fill="Green" Height="44"/>
    <Rectangle Fill="Orange" Height="44"/>
</StackPanel>
```


Результат выглядит так:

![Панель стека](images/layout-panel-stack-panel.png)

В StackPanel, если размер дочернего элемента явным образом не задан, он растягивается на всю доступную ширину (или высоту, если ориентация — **Horizontal**). В этом примере ширина прямоугольников не задана. Прямоугольники растягиваются, заполняя всю ширину StackPanel.

## <a name="grid"></a>Макет Grid

Панель [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) поддерживает гибкие макеты и позволяет размещать элементы управления в несколько строк и столбцов. Вы можете определить строки и столбцы панели Grid с помощью свойств [**RowDefinitions**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid.rowdefinitions) и [**ColumnDefinitions**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid.columndefinitions).

Объекты располагаются в определенных ячейках макета Grid с использованием присоединенных свойств [**Grid.Column**](https://docs.microsoft.com/dotnet/api/system.windows.controls.grid.column) и [**Grid.Row**](https://docs.microsoft.com/dotnet/api/system.windows.controls.grid.row).

Присоединенные свойства [**Grid.RowSpan**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/ms605035(v=vs.95)) и [**Grid.ColumnSpan**](https://docs.microsoft.com/dotnet/api/system.windows.controls.grid.columnspan) позволяют распространить содержимое на несколько строк или столбцов.

В этом примере кода на XAML показано, как создать макет Grid с двумя строками и двумя столбцами.

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition/>
        <RowDefinition Height="44"/>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto"/>
        <ColumnDefinition/>
    </Grid.ColumnDefinitions>
    <Rectangle Fill="Red" Width="44"/>
    <Rectangle Fill="Blue" Grid.Row="1"/>
    <Rectangle Fill="Green" Grid.Column="1"/>
    <Rectangle Fill="Orange" Grid.Row="1" Grid.Column="1"/>
</Grid>
```


Результат выглядит так:

![Макет Grid](images/layout-panel-grid.png)

В этом примере показан принцип работы изменения размера. 
- Вторая строка имеет точную высоту 44 эффективных пикселя. По умолчанию высота первой строки занимает оставшееся пространство.
- Для ширины первого столбца устанавливается значение **Auto**, чтобы он имел требуемую ширину для своих дочерних элементов. В данном случае ширина составляет 44 эффективных пикселя в соответствии с шириной красного прямоугольника.
- Больше для размеров прямоугольников не действуют никакие другие ограничения, поэтому каждый прямоугольник растягивается для заполнения ячейки сетки, в которой он находится.

Функции назначения размера **Auto** или с помощью звезд позволяют распределить пространство столбца и строки. Функция автоматического выбора размера используется, чтобы разрешить элементам пользовательского интерфейса изменять размер по размеру своего содержимого или родительского контейнера. Можно также использовать функцию автоматического выбора размера со строками и столбцами сетки. Для использования функции автоматического выбора размеров установите для параметров Height и/или Width элементов пользовательского интерфейса значение **Auto**.

Пропорциональное изменение размеров, также именуемое *задание размера с помощью звезды*, используется для распределения доступного пространства среди строк и столбцов сетки с помощью взвешенных пропорций. В языке XAML значения звезд выражены как \* (или *n*\* для пропорционального задания размера с помощью звезды). Например, чтобы указать, что один столбец двухстолбцового макета в пять раз шире, чем другой, используйте значения «5\*» и «\*» для свойств [**Width**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.columndefinition.width) в элементах [**ColumnDefinition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ColumnDefinition).

В этом примере сочетаются фиксированный, автоматический и пропорциональный размер в элементе [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) с четырьмя столбцами.

&nbsp;|&nbsp;|&nbsp;
------|------|------
Column_1 | **Автоматически** | Размер столбца автоматически подстраивается под содержимое.
Column_2 | * | После вычисления значений ширины для столбцов с автоматическим подбором размера этот столбец получает часть оставшейся ширины. Ширина столбца Column_2 будет занимать половину ширины столбца Column_4.
Column_3 | **44** | Столбец будет иметь ширину 44 пикселя.
Column_4 | **2**\* | После вычисления значений ширины для столбцов с автоматическим подбором размера этот столбец получает часть оставшейся ширины. Ширина столбца Column_4 будет в два раза больше ширины столбца Column_2.

Ширина столбцов по умолчанию составляет «*», поэтому не нужно явным образом задавать это значение для второго столбца.

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto"/>
        <ColumnDefinition/>
        <ColumnDefinition Width="44"/>
        <ColumnDefinition Width="2*"/>
    </Grid.ColumnDefinitions>
    <TextBlock Text="Column 1 sizes to its conent." FontSize="24"/>
</Grid>
```

В конструкторе XAML в Visual Studio результат выглядит следующим образом.

![Сетка 4 столбцов в конструкторе Visual Studio](images/xaml-layout-grid-in-designer.png)

## <a name="variablesizedwrapgrid"></a>VariableSizedWrapGrid

Элемент [**VariableSizedWrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.VariableSizedWrapGrid) — это панель макета в виде сетки, где строки и столбцы обеспечивают автоматический перенос на новую строку или в новый столбец при достижении значения [**MaximumRowsOrColumns**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.maximumrowsorcolumns). 

Свойство [**Orientation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.orientation) указывает, добавляет ли сетка свои элементы или столбцы перед переносом. Ориентация по умолчанию — **Vertical**. Это означает, что сетка добавляет элементы сверху вниз до заполнения столбца, а затем переносит данные в новый столбец. Если выбрано значение **Horizontal**, сетка добавляет элементы слева направо, а затем переносит данные в новую строку.

Размеры ячейки задаются параметрами [**ItemHeight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.itemheight) и [**ItemWidth**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.itemwidth). Все ячейки имеют одинаковый размер. Если параметр ItemHeight или ItemWidth не указан, первая ячейка подбирает размер согласно своему содержимому, и размер каждой последующей ячейки полностью соответствует размеру первой ячейки.

Можно использовать вложенные свойства [**VariableSizedWrapGrid.ColumnSpan**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid) и [**VariableSizedWrapGrid.RowSpan**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid.getrowspan), чтобы задать количество смежных ячеек, которое должен заполнять дочерний элемент.

Пример использования VariableSizedWrapGrid в XAML.

```xaml
<VariableSizedWrapGrid MaximumRowsOrColumns="3" ItemHeight="44" ItemWidth="44">
    <Rectangle Fill="Red"/>
    <Rectangle Fill="Blue" 
               VariableSizedWrapGrid.RowSpan="2"/>
    <Rectangle Fill="Green" 
               VariableSizedWrapGrid.ColumnSpan="2"/>
    <Rectangle Fill="Orange" 
               VariableSizedWrapGrid.RowSpan="2" 
               VariableSizedWrapGrid.ColumnSpan="2"/>
</VariableSizedWrapGrid>
```


Результат выглядит так:

![Сетка с переносом с переменным размером](images/layout-panel-variable-size-wrap-grid.png)

В этом примере максимальное число строк в каждом столбце составляет 3. Первый столбец содержит только 2 элемента (красный и синий прямоугольники), поскольку размер синего прямоугольника составляет 2 строки. Затем зеленый прямоугольник переносит данные наверх следующего столбца.

## <a name="canvas"></a>Элемент Canvas

Панель [**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas) располагает свои дочерние элементы с помощью фиксированных точек координат и не поддерживает гибкие макеты. Эти точки указываются в индивидуальных дочерних элементах заданием присоединенных свойств [**Canvas.Left**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.left) и [**Canvas.Top**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.top) для каждого элемента. Родительский объект Canvas считывает значения этих присоединенных свойств со своих дочерних элементов во время упорядочивания макета [Arrange](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange).

Объекты в Canvas могут перекрываться, когда один объект рисуется поверх другого. По умолчанию Canvas отображает дочерние объекты в порядке, в котором они объявляются. Таким образом, последний дочерний объект отображается сверху (каждый элемент имеет по умолчанию значение z-index, равное 0). То же самое справедливо и для других встроенных панелей. Однако Canvas также поддерживает присоединенное свойство [**Canvas.ZIndex**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc190397(v=vs.95)), которое можно задать на каждом из дочерних элементов. Вы можете задать это свойство в коде, чтобы изменять последовательность перекрытия элементов во время выполнения. Элемент с наиболее высоким значением Canvas.ZIndex соответствует последнему элементу, загораживающему все прочие элементы, которые используют то же пространство, или перекрывающему их. Обратите внимание, что учитывается альфа-фактор (прозрачность), поэтому даже если элементы перекрываются, содержимое в зонах перекрытия может смешиваться, если у верхнего элемента альфа-фактор не является максимальной величиной.

Объект Canvas никак не изменяет размер своих дочерних элементов. Каждый элемент должен указывать свой размер.

Пример Canvas в XAML.

```xaml
<Canvas Width="120" Height="120">
    <Rectangle Fill="Red" Height="44" Width="44"/>
    <Rectangle Fill="Blue" Height="44" Width="44" Canvas.Left="20" Canvas.Top="20"/>
    <Rectangle Fill="Green" Height="44" Width="44" Canvas.Left="40" Canvas.Top="40"/>
    <Rectangle Fill="Orange" Height="44" Width="44" Canvas.Left="60" Canvas.Top="60"/>
</Canvas>
```

Результат выглядит так:

![Элемент Canvas](images/layout-panel-canvas.png)

Используйте панель Canvas с осторожностью. Хотя точное определение положения элементов пользовательского интерфейса в некоторых случаях весьма удобно, фиксированное размещение панели макета приводит к тому, что область пользовательского интерфейса хуже приспосабливается к изменениям размера окна приложения в целом. Изменение размера окна приложения может быть вызвано изменением ориентации устройства, разделением окон приложения, сменой мониторов и множеством иных пользовательских сценариев.

## <a name="panels-for-itemscontrol"></a>Панели для ItemsControl

Существует несколько специальных панелей, которые можно использовать только в качестве [**ItemsPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemspanel) для отображения элементов в [**ItemsControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsControl). Это типы [**ItemsStackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsStackPanel), [**ItemsWrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsWrapGrid), [**VirtualizingStackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.VirtualizingStackPanel) и [**WrapGrid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WrapGrid). Эти панели невозможно использовать для общего макета пользовательского интерфейса.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.
