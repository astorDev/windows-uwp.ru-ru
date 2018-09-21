---
author: QuinnRadich
Description: Use layout panels to arrange and group UI elements in your app.
title: Панели макета для приложений универсальной платформы Windows (UWP)
ms.author: quradic
ms.date: 04/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a839379150ecd38fc1925c81d4e11d588018011f
ms.sourcegitcommit: a160b91a554f8352de963d9fa37f7df89f8a0e23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2018
ms.locfileid: "4122109"
---
# <a name="layout-panels"></a>Панели макета

Панели макета представляют собой контейнеры, которые позволяют упорядочивать и группировать элементы управления пользовательского интерфейса в приложении. Встроенные панели макета XAML включают [**RelativePanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.aspx), [**StackPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.stackpanel.aspx), [**Grid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.aspx), [**VariableSizedWrapGrid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.aspx) и [**Canvas**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.aspx). Здесь будет описана каждая панель, а также будет показано, как ее использовать для создания макета элементов пользовательского интерфейса XAML.

Существует несколько аспектов, которые необходимо учитывать при выборе панели макета.
- Способ расположения панелью своих дочерних элементов.
- Способ определения панелью размеров своих дочерних элементов.
- Характер перекрытия дочерних элементов, наложенных друг на друга (в z-порядке).
- Число и сложность вложенных элементов управления, необходимых для создания нужного макета.

## <a name="panel-properties"></a>Свойства панели

Прежде чем мы обсудим отдельные панели, давайте рассмотрим некоторые общие свойства, которые имеют все панели. 

### <a name="panel-attached-properties"></a>Подключенные свойства панели

В большинстве панелей макета XAML используются присоединенные свойства, что позволяет их дочерним элементам сообщить родительской панели, как они должны быть расположены в пользовательском интерфейсе. Присоединенные свойства используют синтаксис *AttachedPropertyProvider.PropertyName*. При наличии панелей, вложенных в другие панели, присоединенные свойства элементов пользовательского интерфейса, которые описывают характеристики макета родительскому объекту, будут интерпретированы только ближайшей родительской панелью.

Ниже приведен пример задания присоединенного свойства [**Canvas.Left**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.left.aspx) в элементе управления «Кнопка» в XAML. Оно информирует родительский элемент Canvas о том, что кнопка Button должна располагаться в 50эффективных пикселах от левого края Canvas.

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

Использование встроенных свойств границ уменьшает количество элементов XAML, что может повысить производительность пользовательского интерфейса приложения. Подробнее о панелях макета и производительности пользовательского интерфейса см. в разделе [Оптимизация макета XAML](https://msdn.microsoft.com/en-us/library/windows/apps/mt404609.aspx).

## <a name="relativepanel"></a>RelativePanel

[**RelativePanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.aspx) позволяет создать макет элементов пользовательского интерфейса путем указания их расположения относительно других элементов и панели. По умолчанию элемент располагается в левом верхнем углу панели. Можно использовать RelativePanel с [**VisualStateManager**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.visualstatemanager.aspx) и [**AdaptiveTrigger**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.adaptivetrigger.aspx) для переупорядочивания пользовательского интерфейса для разных размеров окон.

В этой таблице показаны присоединенные свойства, которые можно использовать для выравнивания элемента относительно панели и других элементов.

Согласование панели | Согласование с элементами одного уровня | Согласование с положением одного уровня
----------------|-------------------|-----------------
[**AlignTopWithPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.aligntopwithpanel.aspx) | [**AlignTopWith**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.aligntopwith.aspx) | [**Выше**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.above.aspx)  
[**AlignBottomWithPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignbottomwithpanel.aspx) | [**AlignBottomWith**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignbottomwith.aspx) | [**Ниже**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.below.aspx)  
[**AlignLeftWithPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignleftwithpanel.aspx) | [**AlignLeftWith**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignleftwith.aspx) | [**LeftOf**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.leftof.aspx)  
[**AlignRightWithPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignrightwithpanel.aspx) | [**AlignRightWith**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignrightwith.aspx) | [**RightOf**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.rightof.aspx)  
[**AlignHorizontalCenterWithPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignhorizontalcenterwithpanel.aspx) | [**AlignHorizontalCenterWith**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignhorizontalcenterwith.aspx) | &nbsp;   
[**AlignVerticalCenterWithPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignverticalcenterwithpanel.aspx) | [**AlignVerticalCenterWith**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignverticalcenterwith.aspx) | &nbsp;   

 
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

[**StackPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.stackpanel.aspx) упорядочивает дочерние элементы в одной строке, которую можно расположить по горизонтали или вертикали. StackPanel обычно используется для упорядочивания небольших подразделов пользовательского интерфейса на странице.

Для указания направления дочерних элементов можно использовать свойство [**Orientation**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.stackpanel.orientation.aspx). Ориентация по умолчанию — [**Vertical**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.orientation.aspx).

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

В StackPanel, если размер дочернего элемента явным образом не задан, он растягивается на всю доступную ширину (или высоту, если ориентация— **Horizontal**). В этом примере ширина прямоугольников не задана. Прямоугольники растягиваются, заполняя всю ширину StackPanel.

## <a name="grid"></a>Grid

Панель [**Grid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.aspx) поддерживает гибкие макеты и позволяет размещать элементы управления в несколько строк и столбцов. Вы можете определить строки и столбцы панели Grid с помощью свойств [**RowDefinitions**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.rowdefinitions.aspx) и [**ColumnDefinitions**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.columndefinitions.aspx).

Объекты располагаются в определенных ячейках макета Grid с помощью присоединенных свойств [**Grid.Column**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.column.aspx) и [**Grid.Row**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.row.aspx).

Присоединенные свойства [**Grid.RowSpan**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.rowspan.aspx) и [**Grid.ColumnSpan**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.columnspan.aspx) позволяют распространить содержимое на несколько строк или столбцов.

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

Пропорциональное изменение размеров, также именуемое *задание размера с помощью звезды*, используется для распределения доступного пространства среди строк и столбцов сетки с помощью взвешенных пропорций. В языке XAML значения звезд выражены как \* (или *n*\* для пропорционального задания размера с помощью звезды). Например, чтобы указать, что один столбец двухстолбцового макета в пять раз шире, чем другой, используйте значения «5\*» и «\*» для свойств [**Width**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.columndefinition.width.aspx) в элементах [**ColumnDefinition**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.columndefinition.aspx).

В этом примере сочетаются фиксированный, автоматический и пропорциональный размер в элементе [**Grid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.aspx) с четырьмя столбцами.

&nbsp;|&nbsp;|&nbsp;
------|------|------
Column_1 | **Auto (Автоматически)** | Размер столбца автоматически подстраивается под содержимое.
Column_2 | * | После вычисления значений ширины для автоматических столбцов этот столбец получает часть оставшейся ширины. Ширина столбца Column_2 будет занимать половину ширины столбца Column_4.
Column_3 | **44** | Столбец будет иметь ширину 44пикселя.
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

[**Элемент VariableSizedWrapGrid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.aspx) — это панель макета в виде сетки, где строки и столбцы обеспечивают автоматический перенос на новую строку или в новый столбец при достижении значения [**MaximumRowsOrColumns**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.maximumrowsorcolumns.aspx). 

Свойство [**Orientation**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.orientation.aspx) указывает, добавляет ли сетка свои элементы или столбцы перед переносом. Ориентация по умолчанию — **Vertical**. Это означает, что сетка добавляет элементы сверху вниз до заполнения столбца, а затем переносит данные в новый столбец. Если выбрано значение **Horizontal**, сетка добавляет элементы слева направо, а затем переносит данные в новую строку.

Размеры ячейки задаются параметрами [**ItemHeight**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.itemheight.aspx) и [**ItemWidth**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.itemwidth.aspx). Все ячейки имеют одинаковый размер. Если параметр ItemHeight или ItemWidth не указан, первая ячейка подбирает размер согласно своему содержимому, и размер каждой последующей ячейки полностью соответствует размеру первой ячейки.

Можно использовать вложенные свойства [**VariableSizedWrapGrid.ColumnSpan**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.columnspan.aspx) и [**VariableSizedWrapGrid.RowSpan**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.rowspan.aspx), чтобы задать количество смежных ячеек, которое должен заполнять дочерний элемент.

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

## <a name="canvas"></a>Холст

Панель [**Canvas**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.aspx) располагает свои дочерние элементы с помощью фиксированных точек координат и не поддерживает гибкие макеты. Эти точки указываются в индивидуальных дочерних элементах заданием присоединенных свойств [**Canvas.Left**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.left.aspx) и [**Canvas.Top**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.top.aspx) для каждого элемента. Родительский объект Canvas считывает значения этих присоединенных свойств со своих дочерних элементов во время этапа упорядочивания макета [Arrange](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.arrange.aspx).

Объекты в Canvas могут перекрываться, когда один объект рисуется поверх другого. По умолчанию Canvas отображает дочерние объекты в порядке, в котором они объявляются. Таким образом, последний дочерний объект отображается сверху (каждый элемент имеет по умолчанию значение z-index, равное0). То же самое справедливо и для других встроенных панелей. Однако Canvas также поддерживает присоединенное свойство [**Canvas.ZIndex**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.zindex.aspx), которое можно задать на каждом из дочерних элементов. Вы можете задать это свойство в коде, чтобы изменять последовательность перекрытия элементов во время выполнения. Элемент с наиболее высоким значением Canvas.ZIndex соответствует последнему элементу, загораживающему все прочие элементы, которые используют то же пространство, или перекрывающему их. Обратите внимание, что учитывается альфа-фактор (прозрачность), поэтому даже если элементы перекрываются, содержимое в зонах перекрытия может смешиваться, если у верхнего элемента альфа-фактор не является максимальной величиной.

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

![Холст](images/layout-panel-canvas.png)

Используйте панель Canvas с осторожностью. Хотя точное определение положения элементов пользовательского интерфейса в некоторых случаях весьма удобно, фиксированное размещение панели макета приводит к тому, что область пользовательского интерфейса хуже приспосабливается к изменениям размера окна приложения в целом. Изменение размера окна приложения может быть вызвано изменением ориентации устройства, разделением окон приложения, сменой мониторов и множеством иных пользовательских сценариев.

## <a name="panels-for-itemscontrol"></a>Панели для ItemsControl

Существует несколько специальных панелей, которые можно использовать только в качестве [**ItemsPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemspanel.aspx) для отображения элементов в [**ItemsControl**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.aspx). Это типы [**ItemsStackPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemsstackpanel.aspx), [**ItemsWrapGrid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemswrapgrid.aspx), [**VirtualizingStackPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.virtualizingstackpanel.aspx) и [**WrapGrid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.wrapgrid.aspx). Эти панели невозможно использовать для общего макета пользовательского интерфейса.

