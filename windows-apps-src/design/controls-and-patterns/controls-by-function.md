---
Description: Перечисляет — согласно их функциям — некоторые элементы управления, которые можно использовать в приложении.
title: Элементы управления по функциям
ms.assetid: 8DB4347B-91D6-4659-91F2-80ECF7BBB596
label: Controls by function
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a0a267385668a276fc776c5f5d495b27ae72a2b3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57611519"
---
# <a name="controls-by-function"></a>Элементы управления по функциям

Инфраструктура пользовательского интерфейса XAML для Windows предоставляет обширную библиотеку элементов управления, поддерживающих разработку интерфейса. Часть элементов управления имеет визуальное представление, а остальные используются в качестве контейнеров для других элементов управления или содержимого — например, для изображений и мультимедиа. 

Чтобы просмотреть элементы управления интерфейса Windows в действии, скачайте [Пример основных элементов пользовательского интерфейса XAML](https://go.microsoft.com/fwlink/p/?LinkId=619992).

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/NavigationView">откройте приложение и NavigationView в действии см. в разделе</a> </p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>


Ниже перечислены сгруппированные по функциям типовые элементы управления XAML, которые можно использовать в приложении.

## <a name="appbars-and-commands"></a>Панели приложения и команды

### <a name="app-bar"></a>Панель приложения
Панель инструментов для отображения команд конкретного приложения. См. Панель команд.

Справочные материалы. [Элемент управления AppBar](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbar.aspx) 

### <a name="app-bar-button"></a>Кнопка панели приложения
Кнопка для отображения команд с использованием стиля панели приложения.

![Значки кнопок панели приложения](images/controls/app-bar-buttons.png) 

Справочные материалы. [AppBarButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarbutton.aspx), [SymbolIcon](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.symbolicon.aspx), [BitmapIcon](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.bitmapicon.aspx), [FontIcon](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.fonticon.aspx), [PathIcon](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pathicon.aspx) 

Проектирование и Практическое руководство: [Панель приложения и панели команд управления структурой](app-bars.md) 

Пример кода: [Пример команды XAML](https://go.microsoft.com/fwlink/p/?LinkId=620019)

### <a name="app-bar-separator"></a>Разделитель панели приложения
Визуально разделяет группы команд на соответствующей панели.

Справочные материалы. [AppBarSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarseparator.aspx) 

Пример кода: [Пример команды XAML](https://go.microsoft.com/fwlink/p/?LinkId=620019)

### <a name="app-bar-toggle-button"></a>Переключатель панели приложения
Кнопка для переключения команд на соответствующей панели.

Справочные материалы. [AppBarToggleButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbartogglebutton.aspx) 

Пример кода: [Пример команды XAML](https://go.microsoft.com/fwlink/p/?LinkId=620019)

### <a name="command-bar"></a>Панель команд
Специализированная панель приложения, которая обрабатывает изменение размера кнопок панели приложения.

![Элемент управления "Панель команд"](images/command-bar-compact.png)

```xaml
<CommandBar>
    <AppBarButton Icon="Back" Label="Back" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Stop" Label="Stop" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Play" Label="Play" Click="AppBarButton_Click"/>
</CommandBar>
```
Справочные материалы. [Панели команд](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.commandbar.aspx) 

Проектирование и Практическое руководство: [Панель приложения и панели команд управления структурой](app-bars.md)

Пример кода: [Пример команды XAML](https://go.microsoft.com/fwlink/p/?LinkId=620019)

## <a name="buttons"></a>Кнопки

### <a name="button"></a>Button
Элемент управления, который реагирует на действия пользователя и вызывает событие **Click**.

![Обычная кнопка](images/controls/button.png)

```xaml
<Button x:Name="button1" Content="Button" 
        Click="Button_Click" />
```

Справочные материалы. [Button](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.button.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю кнопок](buttons.md) 

### <a name="hyperlink"></a>Гиперссылка
См. "Кнопка с гиперссылкой".

### <a name="hyperlink-button"></a>Кнопка с гиперссылкой
Кнопка, которая отображается в виде размеченного текста и открывает указанный универсальный код ресурса (URI) в браузере.

![Кнопка с гиперссылкой](images/controls/hyperlink-button.png)

```xaml
<HyperlinkButton Content="www.microsoft.com" 
                 NavigateUri="https://www.microsoft.com"/>
```

Справочные материалы. [HyperlinkButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.hyperlinkbutton.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю гиперссылки](hyperlinks.md)

### <a name="repeat-button"></a>Кнопка "Повтор"
Кнопка, при нажатии которой событие **Click** повторяется до тех пор, пока она не будет отпущена. 

![Элемент управления "Кнопка "Повтор""](images/controls/repeat-button.png) 

```xaml
<RepeatButton x:Name="repeatButton1" Content="Repeat Button" 
              Click="RepeatButton_Click" />
```

Справочные материалы. [RepeatButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.primitives.repeatbutton.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю кнопок](buttons.md) 

## <a name="collectiondata-controls"></a>Элементы управления для коллекций и данных

### <a name="flip-view"></a>Представление пролистывания
Элемент управления, отображающий коллекцию элементов, которые пользователь может пролистать по одному.

```xaml
<FlipView x:Name="flipView1" SelectionChanged="FlipView_SelectionChanged">
    <Image Source="Assets/Logo.png" />
    <Image Source="Assets/SplashScreen.png" />
    <Image Source="Assets/SmallLogo.png" />
</FlipView>
```

Справочные материалы. [FlipView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.flipview.aspx) 

Проектирование и Практическое руководство: [Перевернуть руководство по контролю представления](flipview.md) 

### <a name="grid-view"></a>Представление в виде сетки
Элемент управления, являющийся коллекцией элементов, которые расположены в строках и столбцах, с вертикальной прокруткой.

```xaml
<GridView x:Name="gridView1" SelectionChanged="GridView_SelectionChanged">
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
</GridView>
```

Справочные материалы. [GridView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.gridview.aspx) 

Проектирование и Практическое руководство: [Списки](lists.md) 

Пример кода: [Пример ListView](https://go.microsoft.com/fwlink/p/?LinkId=619900)

### <a name="items-control"></a>Элемент управления "Элементы"
Элемент управления, представляющий коллекцию элементов, которые расположены в пользовательском интерфейсе, указанном в шаблоне данных. 

```xaml
<ItemsControl/>
```

Справочные материалы. [Элемент управления ItemsControl](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.aspx) 

### <a name="list-view"></a>Представление списка
Элемент управления, представляющий коллекцию элементов в виде списка с вертикальной прокруткой.

```xaml
<ListView x:Name="listView1" SelectionChanged="ListView_SelectionChanged">
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
</ListView>
```

Справочные материалы. [ListView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.listview.aspx) 

Проектирование и Практическое руководство: [Списки](lists.md) 

Пример кода: [Пример ListView](https://go.microsoft.com/fwlink/p/?LinkId=619900)

## <a name="date-and-time-controls"></a>Элементы управления датой и временем

### <a name="calendar-date-picker"></a>Управляющий элемент выбора даты в календаре
Элемент управления, позволяющий пользователю выбрать дату из раскрывающегося календаря.

![Выбор даты с представлением открытого календаря](images/controls/calendar-date-picker-open.png)

```xaml
<CalendarDatePicker/>
```

Справочные материалы. [CalendarDatePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.aspx) 

Проектирование и Практическое руководство: [Календарь, Дата и время элементов управления](date-and-time.md)
 
### <a name="calendar-view"></a>Представление календаря
Настраиваемое отображение календаря, позволяющее пользователю выбрать одну или несколько дат.

```xaml
<CalendarView/>
```

Справочные материалы. [Представления календаря](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendarview.aspx) 

Проектирование и Практическое руководство: [Календарь, Дата и время элементов управления](date-and-time.md) 

### <a name="date-picker"></a>Управляющий элемент выбора даты
Элемент управления, позволяющий пользователю выбрать дату.

![Элемент управления "Выбор даты"](images/controls/date-picker.png)

```xaml
<DatePicker Header="Arrival Date"/>
```

Справочные материалы. [Выбор даты](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.datepicker.aspx) 

Проектирование и Практическое руководство: [Календарь, Дата и время элементов управления](date-and-time.md)
 
### <a name="time-picker"></a>Управляющий элемент выбора времени
Элемент управления, позволяющий пользователю установить значение времени.

![Элемент управления TimePicker](images/controls/time-picker.png) 

```xaml
<TimePicker Header="Arrival Time"/>
```

Справочные материалы. [TimePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.aspx) 

Проектирование и Практическое руководство: [Календарь, Дата и время элементов управления](date-and-time.md)

## <a name="flyouts"></a>Всплывающие элементы

### <a name="context-menu"></a>Контекстное меню
См. "Всплывающее меню" и "Контекстное меню".

### <a name="flyout"></a>Всплывающий элемент
Отображает сообщение, требующее взаимодействия с пользователем. (В отличие от диалогового окна всплывающий элемент не создает отдельное окно и не блокирует другие действия пользователя.)

![Элемент управления "Всплывающий элемент"](images/controls/flyout.png)

```xaml
<Flyout>
    <StackPanel>
        <TextBlock Text="All items will be removed. Do you want to continue?"/>
        <Button Click="DeleteConfirmation_Click" Content="Yes, empty my cart"/>
    </StackPanel>
</Flyout>
```

Справочные материалы. [Всплывающий элемент](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.flyout.aspx) 

Проектирование и Практическое руководство: [Всплывающие окна](dialogs-and-flyouts/flyouts.md) 

### <a name="menu-flyout"></a>Всплывающее меню
Временно отображает список команд или параметров, относящихся к текущим действиям пользователя.

![Элемент управления "Всплывающее меню"](images/controls/menu-flyout.png) 

```xaml
<MenuFlyout>
    <MenuFlyoutItem Text="Reset" Click="Reset_Click"/>
    <MenuFlyoutSeparator/>
    <ToggleMenuFlyoutItem Text="Shuffle" 
                          IsChecked="{Binding IsShuffleEnabled, Mode=TwoWay}"/>
    <ToggleMenuFlyoutItem Text="Repeat" 
                          IsChecked="{Binding IsRepeatEnabled, Mode=TwoWay}"/>
</MenuFlyout>
```

Справочные материалы. [MenuFlyout](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyout.aspx), [MenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx), [MenuFlyoutSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx), [ToggleMenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx) 

Проектирование и Практическое руководство: [Меню и контекстные меню](menus.md) 

Пример кода: [Пример XAML контекстного меню](https://go.microsoft.com/fwlink/p/?LinkId=620021)

### <a name="popup-menu"></a>Контекстное меню
Настраиваемое меню с выбранными вами командами.

Справочные материалы. [PopupMenu](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.popups.popupmenu.aspx) 

Проектирование и Практическое руководство: [Диалоговые окна](dialogs-and-flyouts/dialogs.md) 

### <a name="tooltip"></a>Всплывающая подсказка
Всплывающее окно, которое содержит сведения об элементе. 
 
![Элемент управления "Подсказка"](images/controls/tool-tip.png)

```xaml
<Button Content="Button" Click="Button_Click" 
        ToolTipService.ToolTip="Click to perform action" />
```

Справочные материалы. [Подсказка](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.tooltip.aspx), [ToolTipService](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.tooltipservice.aspx) 

Проектирование и Практическое руководство: Руководство по всплывающим подсказкам 

## <a name="images"></a>образы,

### <a name="image"></a>Изображение
Элемент управления, представляющий изображение.

```xaml
<Image Source="Assets/Logo.png" />
```

Справочные материалы. [Image](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.image.aspx) 

Проектирование и Практическое руководство: [Образ и ImageBrush](images-imagebrushes.md) 

Пример кода: [Пример использования изображений XAML](https://go.microsoft.com/fwlink/p/?linkid=226867)

## <a name="graphics-and-ink"></a>Графика и рукописный ввод

### <a name="inkcanvas"></a>InkCanvas
Элемент управления, который получает и отображает росчерки пера.

```xaml
<InkCanvas/>
```

Справочные материалы. [InkCanvas](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.inkcanvas.aspx) 

### <a name="shapes"></a>Фигуры
Различные графические объекты абстрактного режима отображения, которые можно представить в виде эллипсов, прямоугольников, линий, кривых Безье и т. д.

![Многоугольник](images/controls/shapes-polygon.png) 
![Контур](images/controls/shapes-path.png) 

```xaml
<Ellipse/>
<Path/>
<Rectangle/>
```

Справочные материалы. [Фигуры](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.shapes.shape.aspx) 

Как... [Рисование фигур](../../graphics/drawing-shapes.md) 

Пример кода: [Пример рисования векторной XAML](https://go.microsoft.com/fwlink/p/?linkid=226866)

## <a name="layout-controls"></a>Элементы управления для макета

### <a name="border"></a>Рамка
Контейнерный элемент управления, рисующий границу и/или фон по периметру другого объекта.

![Рамка вокруг двух прямоугольников](images/controls/border.png) 

```xaml
<Border BorderBrush="Blue" BorderThickness="4" 
        Height="108" Width="64" 
        Padding="8" CornerRadius="4">
    <Canvas>
        <Rectangle Fill="Orange"/>
        <Rectangle Fill="Green" Margin="0,44"/>
    </Canvas>
</Border>
```

Справочные материалы. [Границы](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.border.aspx)

### <a name="canvas"></a>Элемент Canvas
Панель макета, поддерживающая размещение в абсолютной системе дочерних элементов относительно левого верхнего угла холста.
 
![Панель макета "Холст"](images/controls/canvas.png) 

```xaml
<Canvas Width="120" Height="120">
    <Rectangle Fill="Red"/>
    <Rectangle Fill="Blue" Canvas.Left="20" Canvas.Top="20"/>
    <Rectangle Fill="Green" Canvas.Left="40" Canvas.Top="40"/>
    <Rectangle Fill="Orange" Canvas.Left="60" Canvas.Top="60"/>
</Canvas>
```

Справочные материалы. [Холст](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.aspx)
 
### <a name="grid"></a>Макет Grid
Панель макета, упорядочивающая дочерние элементы по горизонтали и вертикали.

![Панель макета "Сетка"](images/controls/grid.png) 

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="50"/>
        <RowDefinition Height="50"/>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="50"/>
        <ColumnDefinition Width="50"/>
    </Grid.ColumnDefinitions>
    <Rectangle Fill="Red"/>
    <Rectangle Fill="Blue" Grid.Row="1"/>
    <Rectangle Fill="Green" Grid.Column="1"/>
    <Rectangle Fill="Orange" Grid.Row="1" Grid.Column="1"/>
</Grid>
```

Справочные материалы. [Сетка](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.aspx)
 
### <a name="panning-scroll-viewer"></a>Сдвиг средства прокрутки
См. "Средство прокрутки".

### <a name="relativepanel"></a>RelativePanel
Панель, которая позволяет располагать и выравнивать дочерние объекты относительно друг друга или родительской панели.

![Относительная панель панели макета](images/controls/relative-panel.png) 

```xaml
<RelativePanel>
    <TextBox x:Name="textBox1" RelativePanel.AlignLeftWithPanel="True"/>
    <Button Content="Submit" RelativePanel.Below="textBox1"/>
</RelativePanel>
```

Справочные материалы. [RelativePanel](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.aspx)

### <a name="scroll-bar"></a>полоса прокрутки;
См. "Средство прокрутки". (ScrollBar — это элемент ScrollViewer. Он обычно не используется в качестве изолированного элемента управления.)

Справочные материалы. [Полосы прокрутки](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.primitives.scrollbar.aspx)
 
### <a name="scroll-viewer"></a>Средство прокрутки
Контейнерный элемент управления, позволяющий пользователю сдвигать и масштабировать свое содержимое.

```xaml
<ScrollViewer ZoomMode="Enabled" MaxZoomFactor="10" 
              HorizontalScrollMode="Enabled" 
              HorizontalScrollBarVisibility="Visible"
              Height="200" Width="200">
    <Image Source="Assets/Logo.png" Height="400" Width="400"/>
</ScrollViewer>
```

Справочные материалы. [ScrollViewer](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.aspx)

Проектирование и Практическое руководство: [Прокрутка и панорамирования руководство по элементам управления](scroll-controls.md) 

Пример кода: [XAML прокрутки, сдвига и масштабирования примера](https://go.microsoft.com/fwlink/p/?linkid=238577)

### <a name="stack-panel"></a>Панель стека
Панель макета, упорядочивающая дочерние элементы в одной строке, которую можно расположить по горизонтали или вертикали.

![Элемент управления макетом панели стека](images/controls/stack-panel.png) 

```xaml
<StackPanel>
    <Rectangle Fill="Red"/>
    <Rectangle Fill="Blue"/>
    <Rectangle Fill="Green"/>
    <Rectangle Fill="Orange"/>
</StackPanel>
```

Справочные материалы. [StackPanel](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.stackpanel.aspx)
 
### <a name="variablesizedwrapgrid"></a>VariableSizedWrapGrid
Панель макета, упорядочивающая дочерние элементы по горизонтали и вертикали. Каждый дочерний элемент может занимать несколько строк или столбцов.

![Панель макета сетки с переносом переменного размера](images/controls/variable-sized-wrap-grid.png) 

```xaml
<VariableSizedWrapGrid MaximumRowsOrColumns="3" ItemHeight="44" ItemWidth="44">
    <Rectangle Fill="Red"/>
    <Rectangle Fill="Blue" Height="80" 
               VariableSizedWrapGrid.RowSpan="2"/>
    <Rectangle Fill="Green" Width="80" 
               VariableSizedWrapGrid.ColumnSpan="2"/>
    <Rectangle Fill="Orange" Height="80" Width="80" 
               VariableSizedWrapGrid.RowSpan="2" 
               VariableSizedWrapGrid.ColumnSpan="2"/>
</VariableSizedWrapGrid>
```

Справочные материалы. [VariableSizedWrapGrid](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.aspx)

### <a name="viewbox"></a>Viewbox
Контейнерный элемент управления, который масштабирует свое содержимое до определенного размера.

![Элемент управления Viewbox](images/controls/view-box.png) 

```xaml
<Viewbox MaxWidth="25" MaxHeight="25">
    <Image Source="Assets/Logo.png"/>
</Viewbox>
<Viewbox MaxWidth="75" MaxHeight="75">
    <Image Source="Assets/Logo.png"/>
</Viewbox>
<Viewbox MaxWidth="150" MaxHeight="150">
    <Image Source="Assets/Logo.png"/>
</Viewbox>
```

Справочные материалы. [Viewbox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.viewbox.aspx)
 
### <a name="zooming-scroll-viewer"></a>Масштабирующее средство прокрутки
См. "Средство прокрутки".

## <a name="media-controls"></a>Элементы управления мультимедиа

### <a name="audio"></a>Звук
См. "Элемент мультимедиа".

### <a name="media-element"></a>Элемент мультимедиа
Элемент управления для воспроизведения аудио- и видеоматериалов.

```xaml
<MediaElement x:Name="myMediaElement"/>
```

Справочные материалы. [MediaElement](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediaelement.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю элемент мультимедиа](media-playback.md)

### <a name="mediatransportcontrols"></a>MediaTransportControls
Элемент управления, предоставляющий элементы управления воспроизведением для MediaElement.

![Элемент мультимедиа с элементами управления транспортировкой](images/controls/media-transport-controls.png) 

```xaml
<MediaTransportControls MediaElement="myMediaElement"/>
```

Справочные материалы. [MediaTransportControls](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediatransportcontrols.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю элемент мультимедиа](media-playback.md) 

Пример кода: [Пример элементов управления мультимедиа транспорта](https://go.microsoft.com/fwlink/p/?LinkId=620023)

### <a name="video"></a>Видео
См. "Элемент мультимедиа".

## <a name="navigation"></a>Навигация

### <a name="navigationview"></a>NavigationView

Адаптируемые контейнера и гибкие навигации модель, которая реализует левой панели навигации, верхней панели навигации и вкладок шаблон.

Справочные материалы. [NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview)

Проектирование и Практическое руководство: [Руководство по контролю NavigationView](navigationview.md)

### <a name="splitview"></a>SplitView

Контейнерный элемент управления с двумя представлениями: одно представление предназначено для основного содержимого, второе обычно используется для меню навигации.

![Элемент управления "Комбинированный режим"](images/controls/split-view.png) 

```xaml
<SplitView>
    <SplitView.Pane>
        <!-- Menu content -->
    </SplitView.Pane>
    <SplitView.Content>
        <!-- Main content -->
    </SplitView.Content>
</SplitView>
```

Справочные материалы. [SplitView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.splitview.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю разделение представления](split-view.md)

### <a name="web-view"></a>Представление веб-страницы

Контейнерный элемент управления, содержащий веб-страницу.

```xaml
<WebView x:Name="webView1" Source="https://developer.microsoft.com" 
         Height="400" Width="800"/>
```

Справочные материалы. [WebView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.aspx) 

Проектирование и Практическое руководство: Рекомендации по веб-представления 

Пример кода: [Пример элемента управления WebView в XAML](https://go.microsoft.com/fwlink/p/?linkid=238582)

### <a name="semantic-zoom"></a>Контекстное масштабирование

Контейнерный элемент управления, позволяющий пользователю выполнять масштабирование между двумя представлениями коллекции элементов.

```xaml
<SemanticZoom>
    <ZoomedInView>
        <GridView></GridView>
    </ZoomedInView>
    <ZoomedOutView>
        <GridView></GridView>
    </ZoomedOutView>
</SemanticZoom>
```

Справочные материалы. [Элемент управления SemanticZoom](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.semanticzoom.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю семантического масштабирования.](semantic-zoom.md)

Пример кода: [Группирование XAML GridView и элемент управления SemanticZoom пример](https://go.microsoft.com/fwlink/p/?linkid=226564)

## <a name="progress-controls"></a>Элементы управления «Ход выполнения»

### <a name="progress-bar"></a>Индикатор выполнения
Элемент управления, отображающий ход выполнения задачи с помощью индикатора.

![Элемент управления “Индикатор выполнения”](images/controls/progress-bar-determinate.png)

Индикатор выполнения, который показывает конкретное значение.

```xaml
<ProgressBar x:Name="progressBar1" Value="50" Width="100"/>
```

![Элемент управления “Неопределенный индикатор выполнения”](images/controls/progress-bar-indeterminate.png)

Индикатор выполнения, который показывает ход выполнения неопределенного процесса.

```xaml
<ProgressBar x:Name="indeterminateProgressBar1" IsIndeterminate="True" Width="100"/>
```

Справочные материалы. [ProgressBar](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.progressbar.aspx) 

Проектирование и Практическое руководство: [Руководство по элементам управления хода выполнения](progress-controls.md) 

### <a name="progress-ring"></a>Кольцевой индикатор выполнения
Элемент управления, отображающий неопределенный ход выполнения задачи в виде кольца. 

![Элемент управления "Кольцевой индикатор выполнения"](images/controls/progress-ring.png) 

```xaml
<ProgressRing x:Name="progressRing1" IsActive="True"/>
```

Справочные материалы. [ProgressRing](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.progressring.aspx) 

Проектирование и Практическое руководство: [Руководство по элементам управления хода выполнения](progress-controls.md) 

## <a name="text-controls"></a>Текстовые элементы управления

### <a name="auto-suggest-box"></a>Поле автозаполнения
Поле текстового ввода, где отображается предлагаемый текст по мере его набора пользователем.

![Поле автозаполнения для поиска](images/controls/auto-suggest-box.png) 

Справочные материалы. [AutoSuggestBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.aspx)

Проектирование и Практическое руководство: [Текстовые элементы управления](text-controls.md), [автозаполнения руководство по контролю поле](auto-suggest-box.md)

Пример кода: [Пример AutoSuggestBox миграции](https://go.microsoft.com/fwlink/p/?LinkId=619996)

### <a name="multi-line-text-box"></a>Поле многострочного текста
См. "Текстовое поле".

### <a name="password-box"></a>Поле ввода пароля
Элемент управления для ввода паролей.

 ![Поле ввода пароля](images/controls/password-box.png)

```xaml
<PasswordBox x:Name="passwordBox1" 
             PasswordChanged="PasswordBox_PasswordChanged" />
```

Справочные материалы. [PasswordBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.passwordbox.aspx) 

Проектирование и Практическое руководство: [Текстовые элементы управления](text-controls.md), [руководство по контролю поле пароля](password-box.md) 

Пример кода: [Пример отображения текста XAML](https://go.microsoft.com/fwlink/p/?linkid=238579), [редактирования образец текста XAML](https://go.microsoft.com/fwlink/p/?linkid=251417)

### <a name="rich-edit-box"></a>Блок форматируемого текста
Элемент управления, при помощи которого пользователь может редактировать документы в формате RTF, содержащие, например, форматированный текст, гиперссылки и изображения.

```xaml
<RichEditBox />
```

Справочные материалы. [RichEditBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.aspx) 

Проектирование и Практическое руководство: [Текстовые элементы управления](text-controls.md), [руководство поле элемента управления "Rich edit"](rich-edit-box.md)

Пример кода: [Пример текста XAML](https://go.microsoft.com/fwlink/p/?linkid=238578)

### <a name="search-box"></a>Поле поиска
См. "Поле автозаполнения".

### <a name="single-line-text-box"></a>Однострочное текстовое поле
См. "Текстовое поле".

### <a name="static-textparagraph"></a>Статический текст или абзац
См. "Блок текста".

### <a name="text-block"></a>Блок текста
Элемент управления, отображающий текст.

![Элемент управления "Блок текста"](images/controls/text-block.png) 

```xaml
<TextBlock x:Name="textBlock1" Text="I am a TextBlock"/>
```

Справочные материалы. [TextBlock](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.aspx), [RichTextBlock](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richtextblock.aspx) 

Проектирование и Практическое руководство: [Текстовые элементы управления](text-controls.md), [руководство по контролю блок текста](text-block.md), [руководство по контролю блок форматированного текста](rich-text-block.md)

Пример кода: [Пример текста XAML](https://go.microsoft.com/fwlink/p/?linkid=238578)

### <a name="text-box"></a>Текстовое поле
Поле для однострочного или многострочного ввода обычного текста.

![Элемент управления "Текстовое поле"](images/controls/text-box.png) 

```xaml
<TextBox x:Name="textBox1" Text="I am a TextBox" 
         TextChanged="TextBox_TextChanged"/>
```

Справочные материалы. [TextBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.aspx) 

Проектирование и Практическое руководство: [Текстовые элементы управления](text-controls.md), [руководство по контролю текстовое поле](text-box.md) 

Пример кода: [Пример текста XAML](https://go.microsoft.com/fwlink/p/?linkid=238578)

## <a name="selection-controls"></a>Элементы управления выделением

### <a name="check-box"></a>Флажок
Элемент управления, который пользователь может установить или снять.

![Три состояния флажка](images/templates-checkbox-states-default.png)

```xaml
<CheckBox x:Name="checkbox1" Content="CheckBox" 
          Checked="CheckBox_Checked"/>
```

Справочные материалы. [Флажок](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.checkbox.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю "флажок"](checkbox.md) 

### <a name="combo-box"></a>поле со списком;
Раскрывающийся список, в котором пользователь может выбрать нужный элемент.

![Поле с раскрытым списком](images/controls/combo-box-open.png) 

```xaml
<ComboBox x:Name="comboBox1" Width="100"
          SelectionChanged="ComboBox_SelectionChanged">
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
    <x:String>Item 3</x:String>
</ComboBox>
```

Справочные материалы. [ComboBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.combobox.aspx) 

Проектирование и Практическое руководство: [Списки](lists.md) 

### <a name="list-box"></a>Список
Элемент управления, представляющий встроенный список, в котором пользователь может выбрать нужный элемент. 

![Элемент управления "Список"](images/controls/list-box.png)

```xaml
<ListBox x:Name="listBox1" Width="100"
         SelectionChanged="ListBox_SelectionChanged">
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
    <x:String>Item 3</x:String>
</ListBox>
```

Справочные материалы. [ListBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.listbox.aspx) 

Проектирование и Практическое руководство: [Списки](lists.md) 

### <a name="radio-button"></a>Переключатель
Элемент управления, который позволяет пользователю выбирать нужный параметр из группы. Если переключатели сгруппированы, они являются взаимоисключающими.

![Элементы управления "Переключатель"](images/controls/radio-button.png)

```xaml
<RadioButton x:Name="radioButton1" Content="RadioButton 1" GroupName="Group1" 
             Checked="RadioButton_Checked"/>
<RadioButton x:Name="radioButton2" Content="RadioButton 2" GroupName="Group1" 
             Checked="RadioButton_Checked" IsChecked="True"/>
<RadioButton x:Name="radioButton3" Content="RadioButton 3" GroupName="Group1" 
             Checked="RadioButton_Checked"/>
```

Справочные материалы. [Переключатель "](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.radiobutton.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю Radio button](radio-button.md)
 
### <a name="slider"></a>Ползунок
Элемент управления, позволяющий пользователю выбирать значение из диапазона путем перемещения элемента управления Thumb по дорожке.

![Элемент управления "Ползунок"](images/controls/slider.png)

```xaml
<Slider x:Name="slider1" Width="100" ValueChanged="Slider_ValueChanged" />
```

Справочные материалы. [Slider](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.slider.aspx) 

Проектирование и Практическое руководство: [Руководство по элемента управления "ползунок"](slider.md) 

### <a name="toggle-button"></a>Выключатель
Кнопка, которая может находиться в одном из двух состояний.

```xaml
<ToggleButton x:Name="toggleButton1" Content="Button" 
              Checked="ToggleButton_Checked"/>
```

Справочные материалы. [Выключатель](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.primitives.togglebutton.aspx)

Проектирование и Практическое руководство: [Руководство по контролю переключателя](toggles.md) 

### <a name="toggle-switch"></a>Тумблер
Переключатель, который может находиться в одном из двух состояний.

![Элемент управления "Тумблер"](images/controls/toggle-switch.png) 

```xaml
<ToggleSwitch x:Name="toggleSwitch1" Header="ToggleSwitch" 
              OnContent="On" OffContent="Off" 
              Toggled="ToggleSwitch_Toggled"/>
```

Справочные материалы. [ToggleSwitch](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.toggleswitch.aspx) 

Проектирование и Практическое руководство: [Руководство по контролю переключателя](toggles.md) 
