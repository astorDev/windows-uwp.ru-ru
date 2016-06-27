---
author: Jwmsft
Description: "Отображает изображения в виде коллекции, например фотографий в альбоме или элементов на странице описания продукта. Каждый раз отображается одно изображение."
title: "Руководство по элементам пролистывания"
ms.assetid: A4E05D92-1A0E-4CDD-84B9-92199FF8A8A3
label: Flip view
template: detail.hbs
ms.sourcegitcommit: a4e9a90edd2aae9d2fd5d7bead948422d43dad59
ms.openlocfilehash: 7da18bd897248ecef7e1caaebff5b8bbab02e3fe

---
# Представление пролистывания

Используйте представление пролистывания для просмотра изображений или других элементов в коллекции, например фотографий в альбоме или элементов на странице описания продукта. Каждый раз отображается один элемент. В случае сенсорных устройств для прокрутки коллекции необходимо коснуться элемента и провести пальцем. Если используется мышь, кнопки навигации отображаются при наведении ее указателя. При использовании клавиатуры для перемещения по коллекции используются клавиши со стрелками.


<span class="sidebar_heading" style="font-weight: bold;">Важные API</span>

-   [**Класс FlipView**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.flipview.aspx)
-   [**Свойство ItemsSource**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemssource.aspx)
-   [**Свойство ItemTemplate**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx)

## Выбор правильного элемента управления

Представление пролистывания лучше всего подходит для просмотра содержимого маленьких и средних коллекций (примерно до 25 элементов). К таким коллекциям относятся элементы страниц, содержащих сведения о продукте, или фотографии в фотоальбоме. Хотя мы не рекомендуем использовать элемент пролистывания для больших коллекций, он используется для просмотра отдельных изображений в фотоальбоме.

## Примеры.

Горизонтальный просмотр, начиная с крайнего левого элемента, и переключение справа является стандартным макетом представления отражения. Этот макет хорошо работает как в книжной, так и в альбомной ориентации на всех устройствах:

![Пример горизонтального макета для элемента пролистывания](images/controls_flipview_horizonal.jpg)

Представление пролистывания можно также просматривать по вертикали:

![Пример вертикального представления пролистывания](images/controls_flipview_vertical.jpg)

## Создание представления пролистывания

FlipView представляет собой [ItemsControl](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.aspx), поэтому может содержать коллекцию элементов любого типа. Для добавления элементов в представление заполните коллекцию [**Items**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.items.aspx) или свяжите свойство [**ItemsSource**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemssource.aspx) с источником данных.

По умолчанию элемент данных отображается в представлении пролистывания в виде строки, представляющей объект данных, к которому он привязан. Чтобы точно определить, как должны отображаться элементы в представлении пролистывания, следует создать [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.datatemplate.aspx), чтобы задать макет элементов управления, используемых для отображения отдельного элемента. Элементы управления в макете могут быть привязаны к свойствам объекта данных или иметь содержимое, задаваемое внутри кода. Вы назначаете DataTemplate свойству [**ItemTemplate**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx) представления FlipView.

### Добавление элементов в коллекцию Items

Добавить элементы в коллекцию [**Items**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.items.aspx) можно с помощью XAML или кода программы. Обычно так добавляют небольшое количество неизменяемых элементов, которые легко задать с помощью XAML, либо в случае, если элементы создаются на этапе выполнения кода. Здесь приведено представление пролистывания с внутренне определяемыми элементами.

```xaml
<FlipView x:Name="flipView1">
    <Image Source="Assets/Logo.png" />
    <Image Source="Assets/SplashScreen.png" />
    <Image Source="Assets/SmallLogo.png" />
</FlipView>
```

```csharp
// Create a new flip view, add content, 
// and add a SelectionChanged event handler.
FlipView flipView1 = new FlipView();
flipView1.Items.Add("Item 1");
flipView1.Items.Add("Item 2");

// Add the flip view to a parent container in the visual tree.
stackPanel1.Children.Add(flipView1);
```

При добавлении элементов в представление пролистывания они автоматически помещаются в контейнер [**FlipViewItem**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.flipviewitem.aspx). Чтобы изменить отображение элемента, можно применить стиль к контейнеру элементов, задав свойство [**ItemContainerStyle**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle.aspx). 

Если элементы определяются в коде XAML, они также автоматически добавляются в коллекцию Items.

### Установка источника элементов

Обычно представление пролистывания используется для отображения данных из таких источников, как база данных или Интернет. Чтобы заполнить представление пролистывания из источника данных, задайте его свойству [**ItemsSource**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemssource.aspx) коллекцию элементов данных.

В этом коде ItemsSource для представления пролистывания задается непосредственно экземпляру коллекции.

```csharp
// Data source.
List<String> itemsList = new List<string>();
itemsList.Add("Item 1");
itemsList.Add("Item 2");

// Create a new flip view, add content, 
// and add a SelectionChanged event handler.
FlipView flipView1 = new FlipView();
flipView1.ItemsSource = itemsList;
flipView1.SelectionChanged += FlipView_SelectionChanged;

// Add the flip view to a parent container in the visual tree.
stackPanel1.Children.Add(flipView1);
```

Свойство ItemsSource можно также связать с коллекцией в среде XAML. Подробнее см. в разделе [Привязка данных с помощью XAML](../data-binding/data-binding-quickstart.md).

Здесь свойство ItemsSource привязано к объекту класса [**CollectionViewSource**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.data.collectionviewsource.aspx) с именем `itemsViewSource`. 

```xaml
<Page.Resources>
    <!-- Collection of items displayed by this page -->
    <CollectionViewSource x:Name="itemsViewSource" Source="{Binding Items}"/>
</Page.Resources>

...

<FlipView x:Name="itemFlipView" 
          ItemsSource="{Binding Source={StaticResource itemsViewSource}}"/>
```

>**Примечание.**
            &nbsp;&nbsp;Заполнить представление пролистывания можно либо путем добавления элементов в его коллекцию Items, либо установкой свойства ItemsSource, но нельзя использовать оба эти способа одновременно. Если задано значение для свойства ItemsSource и при этом добавляется элемент в коде XAML, этот элемент игнорируется. Если задано значение для свойства ItemsSource и при этом в коде программы добавляется элемент в коллекцию Items, возникает исключение.

### Указание вида элементов

По умолчанию элемент данных отображается в представлении пролистывания в виде строки, представляющей объект данных, к которому он привязан. Как правило, хочется показать более сложно оформленное представление данных. Чтобы точно определить, как должны отображаться элементы в представлении пролистывания, вы создаете [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.datatemplate.aspx). В DataTemplate с помощью языка XAML задается макет и внешний вид элементов управления, используемых для отображения отдельного элемента. Элементы управления в макете могут быть привязаны к свойствам объекта данных или иметь содержимое, задаваемое внутри кода. DataTemplate назначается свойству [**ItemTemplate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx) элемента управления FlipView.

В данном примере свойство ItemTemplate представления FlipView является внутренне определяемым. Для отображения имени изображения добавляется перекрытие. 

```XAML
<FlipView x:Name="flipView1" Width="480" Height="270" 
          BorderBrush="Black" BorderThickness="1">
    <FlipView.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Image Width="480" Height="270" Stretch="UniformToFill"
                       Source="{Binding Image}"/>
                <Border Background="#A5000000" Height="80" VerticalAlignment="Bottom">
                    <TextBlock Text="{Binding Name}" 
                               FontFamily="Segoe UI" FontSize="26.667" 
                               Foreground="#CCFFFFFF" Padding="15,20"/>
                </Border>
            </Grid>
        </DataTemplate>
    </FlipView.ItemTemplate>
</FlipView>
```

Вот как выглядит макет, заданный с помощью шаблона данных.

Шаблон представлении пролистывания данных.

### Задание ориентации представления пролистывания

По умолчанию пролистывание в представлении пролистывания происходит в горизонтальном направлении. Для вертикального пролистывания используйте панель стека с вертикальной ориентацией в качестве [**ItemsPanel**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemspanel.aspx) представления пролистывания.

В этом примере показано, как использовать панель стека с вертикальной ориентацией в качестве ItemsPanel представления FlipView.

```XAML
<FlipView x:Name="flipViewVertical" Width="480" Height="270" 
          BorderBrush="Black" BorderThickness="1">
    
    <!-- Use a vertical stack panel for vertical flipping. -->
    <FlipView.ItemsPanel>
        <ItemsPanelTemplate>
            <VirtualizingStackPanel Orientation="Vertical"/>
        </ItemsPanelTemplate>
    </FlipView.ItemsPanel>
    
    <FlipView.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Image Width="480" Height="270" Stretch="UniformToFill"
                       Source="{Binding Image}"/>
                <Border Background="#A5000000" Height="80" VerticalAlignment="Bottom">
                    <TextBlock Text="{Binding Name}" 
                               FontFamily="Segoe UI" FontSize="26.667" 
                               Foreground="#CCFFFFFF" Padding="15,20"/>
                </Border>
            </Grid>
        </DataTemplate>
    </FlipView.ItemTemplate>
</FlipView>
```

Так выглядит представление пролистывания с вертикальной ориентацией.

![Пример вертикального представления пролистывания](images/controls_flipview_vertical.jpg)

## Добавление индикатора контекста

Индикатор контекста в представлении пролистывания предоставляет собой полезную точку отсчета. Точки в стандартном индикаторе контекста не интерактивны. Как показано в этом примере, оптимальным расположением обычно является расположение по центру и ниже галереи:

![Пример индикатора страницы](images/controls_pageindicator.png)

В случае больших коллекций (10–25 элементов) используйте индикатор, который содержит более подробный контекст, например, ленту эскизов. В отличие от индикатора контекста, в котором используются простые точки, каждый эскиз в ленте показывает небольшую версию соответствующего изображения и должен предоставлять возможность выбора:

![Пример индикатора контекста](images/controls_contextindicator.jpg)

## Возможности и ограничения

-   Представления пролистывания лучше всего подходят для коллекций до 25 элементов.
-   Не используйте элемент пролистывания в более крупных коллекциях, поскольку непрерывное перелистывание элементов может быть утомительным. Исключение можно сделать для фотоальбомов, в которых могут содержаться сотни или тысячи изображений. Фотоальбомы почти всегда переключаются в режим пролистывания, после того как на сетке была выбрана фотография. Для других больших коллекций используйте [Представление списка или представление сетки](lists.md).
-   Для индикаторов контекста:
    -   Порядок точек (или любой выбранный вами визуальный маркер) лучше всего подходит при выравнивании по центру и ниже галереи со сдвигом по горизонтали.
    -   Если вы хотите использовать индикатор контекста в галерее со сдвигом по вертикали, он лучше всего подходит при выравнивании по центру и справа от изображений.
    -   Выделенная точка указывает текущий элемент. Обычно выделенная точка имеет белый цвет, а остальные точки — серый.
    -   Число точек может отличаться, но не следует допускать их большое количество, так как пользователю может быть сложно найти свое место. Обычно максимальное количество точек для отображения — 10.

## Глобализация и локализация: контрольный список

<table>
<tr>
<th>Вопросы, связанные с двунаправленностью</th><td>Используйте стандартное зеркальное отображение для языков с написанием справа налево. Элементы управления для перемещения назад и вперед должны быть основаны на направлении языка, поэтому для языков с написанием справа налево правая кнопка должна служить для перехода назад, а левая – для перехода вперед.</td>
</tr>

</table>


## Статьи по теме

- [Руководство по спискам](https://msdn.microsoft.com/library/windows/apps/mt186889)
- [**Класс FlipView (XAML)**](https://msdn.microsoft.com/library/windows/apps/br242678)
- [**Класс CarouselPanel (XAML)**](https://msdn.microsoft.com/library/windows/apps/hh967950)



<!--HONumber=Jun16_HO3-->


