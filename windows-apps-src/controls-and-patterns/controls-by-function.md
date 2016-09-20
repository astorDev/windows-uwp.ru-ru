---
author: Jwmsft
Description: "Перечисляет — согласно их функциям — некоторые элементы управления, которые можно использовать в приложении."
title: "Элементы управления по функциям"
ms.assetid: 8DB4347B-91D6-4659-91F2-80ECF7BBB596
label: Controls by function
template: detail.hbs
ms.sourcegitcommit: c183f7390c5b4f99cf0f31426c1431066e1bc96d
ms.openlocfilehash: 5d6548a4b72144e3a9bf5d759809720c79472afb

---
# Элементы управления по функциям

Инфраструктура пользовательского интерфейса XAML для Windows предоставляет обширную библиотеку элементов управления, поддерживающих разработку интерфейса. Часть элементов управления имеет визуальное представление, а остальные используются в качестве контейнеров для других элементов управления или содержимого — например, для изображений и мультимедиа. 

Чтобы просмотреть элементы управления интерфейса Windows в действии, скачайте [**пример основных элементов пользовательского интерфейса XAML**](http://go.microsoft.com/fwlink/p/?LinkId=619992). 

Ниже перечисленысгруппированные по функциям типовые элементы управления XAML, которые можно использовать в приложении. 

## Панели приложения и команды

### Панель приложения
Панель инструментов для отображения команд конкретного приложения. См. Панель команд.

Справка: [AppBar](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbar.aspx) 

### Кнопка панели приложения
Кнопка для отображения команд с использованием стиля панели приложения.

![Значки кнопок панели приложения](images/controls/app-bar-buttons.png) 

Справка: [AppBarButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarbutton.aspx), [SymbolIcon](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.symbolicon.aspx), [BitmapIcon](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.bitmapicon.aspx), [FontIcon](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.fonticon.aspx), [PathIcon](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pathicon.aspx) 

Проектирование и инструкции: [Руководство по элементам управления панели приложения и панели команд](app-bars.md) 

Пример кода: [Пример команд XAML](http://go.microsoft.com/fwlink/p/?LinkId=620019)

### Разделитель панели приложения
Визуально разделяет группы команд на соответствующей панели.

Справка: [AppBarSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarseparator.aspx) 

Пример кода: [Пример команд XAML](http://go.microsoft.com/fwlink/p/?LinkId=620019)

### Переключатель панели приложения
Кнопка для переключения команд на соответствующей панели.

Справка: [AppBarToggleButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbartogglebutton.aspx) 

Пример кода: [Пример команд XAML](http://go.microsoft.com/fwlink/p/?LinkId=620019)

### Панель команд
Специализированная панель приложения, которая обрабатывает изменение размера кнопок панели приложения.

![Элемент управления "Панель команд"](images/command-bar-compact.png)

```xaml
<CommandBar>
    <AppBarButton Icon="Back" Label="Back" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Stop" Label="Stop" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Play" Label="Play" Click="AppBarButton_Click"/>
</CommandBar>
```
Справка: [CommandBar](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.commandbar.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Панель приложения" и "Панель команд"](app-bars.md)

Пример кода: [Пример команд XAML](http://go.microsoft.com/fwlink/p/?LinkId=620019)

## Кнопки

### Button
Элемент управления, который реагирует на действия пользователя и вызывает событие **Click**.

![Обычная кнопка](images/controls/button.png)

```xaml
<Button x:Name="button1" Content="Button" 
        Click="Button_Click" />
```

Справка: [Button](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.button.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Кнопка"](buttons.md) 

### Гиперссылка
См. "Кнопка с гиперссылкой".

### Кнопка с гиперссылкой
Кнопка, которая отображается в виде размеченного текста и открывает указанный универсальный код ресурса (URI) в браузере.

![Кнопка с гиперссылкой](images/controls/hyperlink-button.png)

```xaml
<HyperlinkButton Content="www.microsoft.com" 
                 NavigateUri="http://www.microsoft.com"/>
```

Справка: [HyperlinkButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.hyperlinkbutton.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Гиперссылка"](hyperlinks.md)

### Кнопка "Повтор"
Кнопка, при нажатии которой событие **Click** повторяется до тех пор, пока она не будет отпущена. 

![Элемент управления "Кнопка "Повтор""](images/controls/repeat-button.png) 

```xaml
<RepeatButton x:Name="repeatButton1" Content="Repeat Button" 
              Click="RepeatButton_Click" />
```

Справка: [RepeatButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.primitives.repeatbutton.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Кнопка"](buttons.md) 

## Элементы управления для коллекций и данных

### Представление пролистывания
Элемент управления, отображающий коллекцию элементов, которые пользователь может пролистать по одному.

```xaml
<FlipView x:Name="flipView1" SelectionChanged="FlipView_SelectionChanged">
    <Image Source="Assets/Logo.png" />
    <Image Source="Assets/SplashScreen.png" />
    <Image Source="Assets/SmallLogo.png" />
</FlipView>
```

Справка: [FlipView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.flipview.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Представление пролистывания"](flipview.md) 

### Представление в виде сетки
Элемент управления, являющийся коллекцией элементов, которые расположены в строках и столбцах, с горизонтальной прокруткой.

```xaml
<GridView x:Name="gridView1" SelectionChanged="GridView_SelectionChanged">
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
</GridView>
```

Справка: [GridView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.gridview.aspx) 

Проектирование и инструкции: [Списки](lists.md) 

Пример кода: [Пример ListView](http://go.microsoft.com/fwlink/p/?LinkId=619900)

### Элемент управления "Элементы"
Элемент управления, представляющий коллекцию элементов, которые расположены в пользовательском интерфейсе, указанном в шаблоне данных. 

```xaml
<ItemsControl/>
```

Справка: [ItemsControl](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.aspx) 

### Представление в виде списка
Элемент управления, представляющий коллекцию элементов в виде списка с вертикальной прокруткой.

```xaml
<ListView x:Name="listView1" SelectionChanged="ListView_SelectionChanged">
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
</ListView>
```

Справка: [ListView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.listview.aspx) 

Проектирование и инструкции: [Списки](lists.md) 

Пример кода: [Пример ListView](http://go.microsoft.com/fwlink/p/?LinkId=619900)

## Элементы управления датой и временем

### Выбор даты в календаре
Элемент управления, позволяющий пользователю выбрать дату из раскрывающегося календаря.

![Выбор даты с представлением открытого календаря](images/controls/calendar-date-picker-open.png)

```xaml
<CalendarDatePicker/>
```

Справка: [CalendarDatePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.aspx) 

Проектирование и инструкции: [Элементы управления календарем, датой и временем](date-and-time.md)
 
### Представление календаря
Настраиваемое отображение календаря, позволяющее пользователю выбрать одну или несколько дат.

```xaml
<CalendarView/>
```

Справка: [CalendarView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendarview.aspx) 

Проектирование и инструкции: [Элементы управления календарем, датой и временем](date-and-time.md) 

### Выбор даты
Элемент управления, позволяющий пользователю выбрать дату.

![Элемент управления "Выбор даты"](images/controls/date-picker.png)

```xaml
<DatePicker Header="Arrival Date"/>
```

Справка: [DatePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.datepicker.aspx) 

Проектирование и инструкции: [Элементы управления календарем, датой и временем](date-and-time.md)
 
### Выбор времени
Элемент управления, позволяющий пользователю установить значение времени.

![Элемент управления TimePicker](images/controls/time-picker.png) 

```xaml
<TimePicker Header="Arrival Time"/>
```

Справка: [TimePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.aspx) 

Проектирование и инструкции: [Элементы управления календарем, датой и временем](date-and-time.md)

## Всплывающие элементы

### Контекстное меню
См. "Всплывающее меню" и "Контекстное меню".

### Всплывающий элемент
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

Справка: [Flyout](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.flyout.aspx) 

Проектирование и инструкции: [Контекстное меню и диалоги](dialogs-popups-menus.md) 

### Всплывающее меню
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

Справка: [MenuFlyout](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyout.aspx), [MenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutitem.aspx), [MenuFlyoutSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.menuflyoutseparator.aspx), [ToggleMenuFlyoutItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.togglemenuflyoutitem.aspx) 

Проектирование и инструкции: [Контекстное меню и диалоги](dialogs-popups-menus.md) 

Пример кода: [Пример контекстного меню XAML](http://go.microsoft.com/fwlink/p/?LinkId=620021)

### Контекстное меню
Настраиваемое меню с выбранными вами командами.

Справка: [PopupMenu](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.popups.popupmenu.aspx) 

Проектирование и инструкции: [Контекстное меню и диалоги](dialogs-popups-menus.md) 

### Всплывающая подсказка
Всплывающее окно, которое содержит сведения об элементе. 
 
![Элемент управления "Подсказка"](images/controls/tool-tip.png)

```xaml
<Button Content="Button" Click="Button_Click" 
        ToolTipService.ToolTip="Click to perform action" />
```

Справка: [ToolTip](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.tooltip.aspx), [ToolTipService](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.tooltipservice.aspx) 

Проектирование и инструкции: руководство по всплывающим подсказкам 

## Изображения

### Изображение
Элемент управления, представляющий изображение.

```xaml
<Image Source="Assets/Logo.png" />
```

Справка: [Image](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.image.aspx) 

Проектирование и инструкции: [Image и ImageBrush](images-imagebrushes.md) 

Пример кода: [Пример изображений в XAML](http://go.microsoft.com/fwlink/p/?linkid=226867)

## Графика и рукописный ввод

### InkCanvas
Элемент управления, который получает и отображает росчерки пера.

```xaml
<InkCanvas/>
```

Справка: [InkCanvas](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.inkcanvas.aspx) 

### Фигуры
Различные графические объекты абстрактного режима отображения, которые можно представить в виде эллипсов, прямоугольников, линий, кривых Безье и т. д.

![Многоугольник](images/controls/shapes-polygon.png) 
![Контур](images/controls/shapes-path.png) 

```xaml
<Ellipse/>
<Path/>
<Rectangle/>
```

Справка: [Shapes](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.shapes.shape.aspx) 

Инструкции: [Рисование фигур](../graphics/drawing-shapes.md) 

Пример кода: [Пример рисования на основе векторов в языке XAML](http://go.microsoft.com/fwlink/p/?linkid=226866)

## Элементы управления для макета

### Рамка
Контейнерный элемент управления, рисующий границу и/или фон по периметру другого объекта.

![Рамка вокруг двух прямоугольников](images/controls/border.png) 

```xaml
<Border BorderBrush="Blue" BorderThickness="4" 
        Height="108" Width="64" 
        Padding="8" CornerRadius="4">
    <Canvas>
        <Rectangle Fill="Yellow"/>
        <Rectangle Fill="Green" Margin="0,44"/>
    </Canvas>
</Border>
```

Справка: [Border](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.border.aspx)

### Холст
Панель макета, поддерживающая размещение в абсолютной системе дочерних элементов относительно левого верхнего угла холста.
 
![Панель макета "Холст"](images/controls/canvas.png) 

```xaml
<Canvas Width="120" Height="120">
    <Rectangle Fill="Red"/>
    <Rectangle Fill="Blue" Canvas.Left="20" Canvas.Top="20"/>
    <Rectangle Fill="Green" Canvas.Left="40" Canvas.Top="40"/>
    <Rectangle Fill="Yellow" Canvas.Left="60" Canvas.Top="60"/>
</Canvas>
```

Справка: [Canvas](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.aspx)
 
### Сетка
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
    <Rectangle Fill="Yellow" Grid.Row="1" Grid.Column="1"/>
</Grid>
```

Справка [Grid](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.aspx)
 
### Сдвиг средства прокрутки
См. "Средство прокрутки".

### RelativePanel
Панель, которая позволяет располагать и выравнивать дочерние объекты относительно друг друга или родительской панели.

![Относительная панель панели макета](images/controls/relative-panel.png) 

```xaml
<RelativePanel>
    <TextBox x:Name="textBox1" RelativePanel.AlignLeftWithPanel="True"/>
    <Button Content="Submit" RelativePanel.Below="textBox1"/>
</RelativePanel>
```

Справка: [RelativePanel](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.aspx)

### Полоса прокрутки
См. "Средство прокрутки". (ScrollBar— это элемент ScrollViewer. Он обычно не используется в качестве изолированного элемента управления.)

Справка: [ScrollBar](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.primitives.scrollbar.aspx)
 
### Средство прокрутки
Контейнерный элемент управления, позволяющий пользователю сдвигать и масштабировать свое содержимое.

```xaml
<ScrollViewer ZoomMode="Enabled" MaxZoomFactor="10" 
              HorizontalScrollMode="Enabled" 
              HorizontalScrollBarVisibility="Visible"
              Height="200" Width="200">
    <Image Source="Assets/Logo.png" Height="400" Width="400"/>
</ScrollViewer>
```

Справка: [ScrollViewer](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.aspx)

Проектирование и инструкции: [Руководство по элементам управления прокруткой и сдвигом](scroll-controls.md) 

Пример кода: [Пример прокрутки, сдвига и масштабирования на XAML](http://go.microsoft.com/fwlink/p/?linkid=238577)

### Панель стека
Панель макета, упорядочивающая дочерние элементы в одной строке, которую можно расположить по горизонтали или вертикали.

![Элемент управления макетом панели стека](images/controls/stack-panel.png) 

```xaml
<StackPanel>
    <Rectangle Fill="Red"/>
    <Rectangle Fill="Blue"/>
    <Rectangle Fill="Green"/>
    <Rectangle Fill="Yellow"/>
</StackPanel>
```

Справка: [StackPanel](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.stackpanel.aspx)
 
### VariableSizedWrapGrid
Панель макета, упорядочивающая дочерние элементы по горизонтали и вертикали. Каждый дочерний элемент может занимать несколько строк или столбцов.

![Панель макета сетки с переносом переменного размера](images/controls/variable-sized-wrap-grid.png) 

```xaml
<VariableSizedWrapGrid MaximumRowsOrColumns="3" ItemHeight="44" ItemWidth="44">
    <Rectangle Fill="Red"/>
    <Rectangle Fill="Blue" Height="80" 
               VariableSizedWrapGrid.RowSpan="2"/>
    <Rectangle Fill="Green" Width="80" 
               VariableSizedWrapGrid.ColumnSpan="2"/>
    <Rectangle Fill="Yellow" Height="80" Width="80" 
               VariableSizedWrapGrid.RowSpan="2" 
               VariableSizedWrapGrid.ColumnSpan="2"/>
</VariableSizedWrapGrid>
```

Справка: [VariableSizedWrapGrid](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.aspx)

### Viewbox
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

Справка: [Viewbox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.viewbox.aspx)
 
### Масштабирующее средство прокрутки
См. "Средство прокрутки".

## Элементы управления мультимедиа

### Звук
См. «Элемент мультимедиа».

### Элемент мультимедиа
Элемент управления для воспроизведения аудио- и видеоматериалов.

```xaml
<MediaElement x:Name="myMediaElement"/>
```

Справка: [MediaElement](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediaelement.aspx) 

Проектирование и инструкции: [Руководство по элементам управления мультимедиа](media-playback.md)

### MediaTransportControls
Элемент управления, предоставляющий элементы управления воспроизведением для MediaElement.

![Элемент мультимедиа с элементами управления транспортировкой](images/controls/media-transport-controls.png) 

```xaml
<MediaTransportControls MediaElement="myMediaElement"/>
```

Справка: [MediaTransportControls](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.mediatransportcontrols.aspx) 

Проектирование и инструкции: [Руководство по элементам управления мультимедиа](media-playback.md) 

Пример кода: [Пример элементов управления транспортировкой мультимедиа](http://go.microsoft.com/fwlink/p/?LinkId=620023)

### Видео
См. "Элемент мультимедиа".

## Навигация

### Концентратор
Контейнерный элемент управления, который позволяет пользователю просматривать различные разделы содержимого и перемещаться между ними.

```xaml
<Hub>
    <HubSection>
        <!--- hub section content -->
    </HubSection>
    <HubSection>
        <!--- hub section content -->
    </HubSection>
</Hub>
```

Справка: [Hub](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.hub.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Главный раздел"](hub.md) 

Пример кода:[Пример элемента управления "Главный раздел" для XAML](http://go.microsoft.com/fwlink/p/?LinkID=309828)

### Сводка
Полноэкранный контейнер и модель навигации, которые также позволяют быстро перемещаться между различными сводками (представлениями или фильтрами), обычно в пределах одного и того же набора данных.

Элементу управления сводкой можно задать стиль макета вкладки.

Справка: [Pivot](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.aspx) 

Проектирование и инструкции: [Руководство по элементам управления вкладками и сводками](tabs-pivot.md) 

Пример кода: [Пример сводки](http://go.microsoft.com/fwlink/p/?LinkId=619903&amp;clcid=0x409)

### Контекстное масштабирование
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

Справка: [SemanticZoom](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.semanticzoom.aspx) 

Проектирование и инструкции: [Руководство по элементам управления контекстным масштабированием](semantic-zoom.md) 

Пример кода: [Пример группировки GridView и элемент управления SemanticZoom на языке XAML](http://go.microsoft.com/fwlink/p/?linkid=226564)

### SplitView
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

Справка: [SplitView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.splitview.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Комбинированный режим"](split-view.md)

### Представление веб-страницы
Контейнерный элемент управления, содержащий веб-страницу.

```xaml
<WebView x:Name="webView1" Source="http://dev.windows.com" 
         Height="400" Width="800"/>
```

Справка: [WebView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.aspx) 

Проектирование и инструкции: Руководство по представлениям веб-страниц 

Пример кода: [Пример элемента управления WebView на языке XAML](http://go.microsoft.com/fwlink/p/?linkid=238582)

## Элементы управления "Ход выполнения"

### Индикатор выполнения
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

Справка: [ProgressBar](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.progressbar.aspx) 

Проектирование и инструкции: [Руководство по элементам управления ходом выполнения](progress-controls.md) 

### Кольцевой индикатор выполнения
Элемент управления, отображающий неопределенный ход выполнения задачи в виде кольца. 

![Элемент управления "Кольцевой индикатор выполнения"](images/controls/progress-ring.png) 

```xaml
<ProgressRing x:Name="progressRing1" IsActive="True"/>
```

Справка: [ProgressRing](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.progressring.aspx) 

Проектирование и инструкции: [Руководство по элементам управления ходом выполнения](progress-controls.md) 

## Элементы управления текстом

### Поле автозаполнения
Поле текстового ввода, где отображается предлагаемый текст по мере его набора пользователем.

![Поле автозаполнения для поиска](images/controls/auto-suggest-box.png) 

Справка: [AutoSuggestBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.aspx)

Проектирование и инструкции: [Элементы управления текстом](text-controls.md), [Руководство по элементу управления полем автозаполнения](auto-suggest-box.md)

Пример кода: [Пример переноса AutoSuggestBox](http://go.microsoft.com/fwlink/p/?LinkId=619996)

### Поле многострочного текста
См. "Текстовое поле".

### Поле ввода пароля
Элемент управления для ввода паролей.

 ![Поле ввода пароля](images/controls/password-box.png)

```xaml
<PasswordBox x:Name="passwordBox1" 
             PasswordChanged="PasswordBox_PasswordChanged" />
```

Справка: [PasswordBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.passwordbox.aspx) 

Проектирование и инструкции: [Элементы управления текстом](text-controls.md), [Руководство по элементу управления "Поле ввода пароля"](password-box.md) 

Пример кода: [Пример отображения текста на языке XAML](http://go.microsoft.com/fwlink/p/?linkid=238579), [Пример редактирования текста на языке XAML](http://go.microsoft.com/fwlink/p/?linkid=251417)

### Поле с форматом
Элемент управления, при помощи которого пользователь может редактировать документы в формате RTF, содержащие, например, форматированный текст, гиперссылки и изображения.

```xaml
<RichEditBox />
```

Справка: [RichEditBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.aspx) 

Проектирование и инструкции: [Элементы управления текстом](text-controls.md), [Руководство по элементу управления "Поле с форматированием"](rich-edit-box.md)

Пример кода: [Пример текста на языке XAML](http://go.microsoft.com/fwlink/p/?linkid=238578)

### Поле поиска
См. "Поле автозаполнения".

### Однострочное текстовое поле
См. "Текстовое поле".

### Статический текст или абзац
См. "Блок текста".

### Блок текста
Элемент управления, отображающий текст.

![Элемент управления "Блок текста"](images/controls/text-block.png) 

```xaml
<TextBlock x:Name="textBlock1" Text="I am a TextBlock"/>
```

Справка: [TextBlock](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.aspx), [RichTextBlock](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richtextblock.aspx) 

Проектирование и инструкции: [Элементы управления текстом](text-controls.md), [Руководство по элементу управления "Блок текста"](text-block.md), [Руководство по элементу управления "Блок текста с форматированием"](rich-text-block.md)

Пример кода: [Пример текста на языке XAML](http://go.microsoft.com/fwlink/p/?linkid=238578)

### Текстовое поле
Поле для однострочного или многострочного ввода обычного текста.

![Элемент управления "Текстовое поле"](images/controls/text-box.png) 

```xaml
<TextBox x:Name="textBox1" Text="I am a TextBox" 
         TextChanged="TextBox_TextChanged"/>
```

Справка: [TextBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.aspx) 

Проектирование и инструкции: [Элементы управления текстом](text-controls.md), [Руководство по элементу управления "Текстовое поле"](text-box.md) 

Пример кода: [Пример текста на языке XAML](http://go.microsoft.com/fwlink/p/?linkid=238578)

## Элементы управления выделением

### Флажок
Элемент управления, который пользователь может установить или снять.

![Три состояния флажка](images/templates-checkbox-states-default.png)

```xaml
<CheckBox x:Name="checkbox1" Content="CheckBox" 
          Checked="CheckBox_Checked"/>
```

Справка: [CheckBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.checkbox.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Флажок"](checkbox.md) 

### Поле со списком
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

Справка: [ComboBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.combobox.aspx) 

Проектирование и инструкции: [Списки](lists.md) 

### Список
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

Справка: [ListBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.listbox.aspx) 

Проектирование и инструкции: [Списки](lists.md) 

### Переключатель
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

Справка: [RadioButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.radiobutton.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Переключатель"](radio-button.md)
 
### Ползунок
Элемент управления, позволяющий пользователю выбирать значение из диапазона путем перемещения элемента управления Thumb по дорожке.

![Элемент управления "Ползунок"](images/controls/slider.png)

```xaml
<Slider x:Name="slider1" Width="100" ValueChanged="Slider_ValueChanged" />
```

Справка: [Slider](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.slider.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Ползунок"](slider.md) 

### Выключатель
Кнопка, которая может находиться в одном из двух состояний.

```xaml
<ToggleButton x:Name="toggleButton1" Content="Button" 
              Checked="ToggleButton_Checked"/>
```

Справка: [ToggleButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.primitives.togglebutton.aspx)

Проектирование и инструкции: [Руководство по элементам управления "Переключатель"](toggles.md) 

### Тумблер
Переключатель, который может находиться в одном из двух состояний.

![Элемент управления "Тумблер"](images/controls/toggle-switch.png) 

```xaml
<ToggleSwitch x:Name="toggleSwitch1" Header="ToggleSwitch" 
              OnContent="On" OffContent="Off" 
              Toggled="ToggleSwitch_Toggled"/>
```

Справка: [ToggleSwitch](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.toggleswitch.aspx) 

Проектирование и инструкции: [Руководство по элементам управления "Переключатель"](toggles.md) 



<!--HONumber=Jun16_HO4-->


