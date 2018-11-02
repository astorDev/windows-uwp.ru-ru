---
author: muhsinking
Description: Use templates to modify the look of items in ListView or GridView controls.
title: Контейнеры и шаблоны элементов
label: Item containers and templates
template: detail.hbs
ms.author: mukin
ms.date: 05/19/2017
ms.topic: article
keywords: Windows10, UWP
ms.assetid: d8eb818d-b62e-4314-a612-f29142dbd93f
pm-contact: predavid
design-contact: kimsea
dev-contact: ranjeshj
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: f5a9756a8afc267c9ec8763af49ba02714c2b4f0
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5946085"
---
# <a name="item-containers-and-templates"></a>Контейнеры и шаблоны элементов

 

Элементы управления **ListView** и **GridView** управляют расположением своих элементов (горизонтально, вертикально, обтекание и так далее) и взаимодействием пользователя с этими элементами, но не тем, как отдельные элементы отображаются на экране. Визуализацией элементов управляют контейнеры элементов. При добавлении элементов в представление списка они автоматически помещаются в контейнер. По умолчанию для ListView используется контейнер элементов [ListViewItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.listviewitem.aspx); а для GridView — [GridViewItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.gridviewitem.aspx).

> **Важные API-интерфейсы**: [класс ListView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listview.aspx), [класс GridView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.gridview.aspx), [свойство ItemTemplate](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx), [свойство ItemContainerStyle](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle.aspx)


> [!NOTE]
> Элементы управления ListView и GridView являются производными от класса [ListViewBase](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.aspx), поэтому они имеют аналогичные функции, но отображают данные по-другому. В этой статье вся информация о представлении списка актуальна для обоих элементов управления (ListView и GridView), если не указано иное. Когда упоминаются классы ListView или ListViewItem, нужно помнить, что префикс *List* может быть заменен префиксом *Grid* для соответствующего эквивалента сетки (GridView или GridViewItem). 

Эти контейнерные элементы управления состоят из двух важных частей, сочетание которых формирует окончательное визуальное оформление элемента: *шаблон данных* и *шаблон элемента управления*.

- **Шаблон данных** Класс [DataTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.datatemplate.aspx) назначается свойству [ItemTemplate](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx) представления списка, чтобы указать способ отображения отдельных элементов данных.
- **Шаблон элемента управления**. Шаблон элемента управления предоставляет часть визуализации элемента, за которую отвечает платформа, например визуальные состояния. Вы можете изменять шаблон элемента управления с помощью свойства [ItemContainerStyle](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle.aspx). Как правило, таким образом изменяются цвета представления списка в соответствии с фирменной символикой или способ отображения выбранных элементов.

На этом изображении показано сочетание шаблонов элементов управления и данных для формирования окончательного визуального оформления элемента.

![Шаблоны элемента управления и данных представления списка](images/listview-visual-parts.png)

Вот XAML-код для создания этого элемента. Мы опишем шаблоны позже.

```xaml
<ListView Width="220" SelectionMode="Multiple">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="x:String">
            <Grid Background="Yellow">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="54"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Image Source="Assets/placeholder.png" Width="44" Height="44"
                       HorizontalAlignment="Left"/>
                <TextBlock Text="{x:Bind}" Foreground="Black"
                           FontSize="15" Grid.Column="1"
                           VerticalAlignment="Center"
                           Padding="0,0,54,0"/>
            </Grid>
        </DataTemplate>
    </ListView.ItemTemplate>
    <ListView.ItemContainerStyle>
        <Style TargetType="ListViewItem">
            <Setter Property="Background" Value="LightGreen"/>
        </Style>
    </ListView.ItemContainerStyle>
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
    <x:String>Item 3</x:String>
    <x:String>Item 4</x:String>
    <x:String>Item 5</x:String>
</ListView>
```
 
## <a name="prerequisites"></a>Необходимые условия

- Мы предполагаем, что вы умеете пользоваться элементами управления представления списка. Подробнее см. в статье [ListView и GridView](listview-and-gridview.md).
- Мы также предполагаем, что вы знакомы со стилями и шаблонами элементов управления и, среди прочего, умеете использовать стили в качестве встроенных элементов или ресурсов. Подробнее см. в разделах [Настройка стиля элементов управления](xaml-styles.md) и [Шаблоны элементов управления](control-templates.md).

## <a name="the-data"></a>Данные

Прежде чем углубляться в процесс отображения элементов данных в представлении списка, необходимо разобраться в отображаемых данных. В этом примере мы создаем тип данных с именем `NamedColor`. Он сочетает название и значение цвета, а также класс **SolidColorBrush** для цвета, которые предоставляются в виде трех свойств: `Name`, `Color` и `Brush`.
 
Затем мы заполняем класс **List** объектом `NamedColor` для каждого именованного цвета в классе [Colors](https://msdn.microsoft.com/library/windows/apps/windows.ui.colors.aspx). Этот список устанавливается как свойство [ItemsSource](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemssource.aspx) для представления списка.

Вот код для определения класса и заполнения списка `NamedColors`.

**C#**
```csharp
using System.Collections.Generic;
using System.Linq;
using System.Reflection;
using Windows.UI;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

namespace ColorsListApp
{
    public sealed partial class MainPage : Page
    {
        // The list of colors won't change after it's populated, so we use List<T>. 
        // If the data can change, we should use an ObservableCollection<T> intead.
        List<NamedColor> NamedColors = new List<NamedColor>();

        public MainPage()
        {
            this.InitializeComponent();

            // Use reflection to get all the properties of the Colors class.
            IEnumerable<PropertyInfo> propertyInfos = typeof(Colors).GetRuntimeProperties();

            // For each property, create a NamedColor with the property name (color name),
            // and property value (color value). Add it the NamedColors list.
            for (int i = 0; i < propertyInfos.Count(); i++)
            {
                NamedColors.Add(new NamedColor(propertyInfos.ElementAt(i).Name,
                                    (Color)propertyInfos.ElementAt(i).GetValue(null)));
            }

            colorsListView.ItemsSource = NamedColors;
        }
    }

    class NamedColor
    {
        public NamedColor(string colorName, Color colorValue)
        {
            Name = colorName;
            Color = colorValue;
        }

        public string Name { get; set; }

        public Color Color { get; set; }

        public SolidColorBrush Brush
        {
            get { return new SolidColorBrush(Color); }
        }
    }
}
```

## <a name="data-template"></a>Шаблон данных

Шаблон данных указывается, чтобы сообщить представлению списка способ отображения элемента данных. 

По умолчанию элемент данных отображается в представлении списка в виде строки, представляющей объект данных, к которому он привязан. При отображении данных 'NamedColors' в представлении списка, не сообщая способа отображения, отображается лишь значение, возвращенное методом **ToString**. Вот как это выглядит.

**XAML**
```xaml
<ListView x:Name="colorsListView"/>
```

![представление списка, отображающее строковое представление элементов](images/listview-no-template.png)

Вы можете отображать строковое представление конкретного свойства элемента данных, установив [DisplayMemberPath](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.displaymemberpath.aspx) для этого свойства. Здесь свойству `Name` элемента `NamedColor` назначается свойство DisplayMemberPath.

**XAML**
```xaml
<ListView x:Name="colorsListView" DisplayMemberPath="Name" />
```

Теперь элементы в представлении списка отображаются по имени, как показано в примере. Это удобнее, но малоинтересно. Кроме того, отображается далеко не вся информация.

![Представление списка с отображением строкового представления свойства элемента](images/listview-display-member-path.png)

Как правило, хочется показать более сложно оформленное представление данных. Чтобы точно определить, как должны отображаться элементы в представлении списка, вы создаете [DataTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.datatemplate.aspx). В DataTemplate с помощью языка XAML задается макет и внешний вид элементов управления, используемых для отображения отдельного элемента. Элементы управления в макете могут быть привязаны к свойствам объекта данных или иметь статическое содержимое, задаваемое внутри кода. Вы назначаете DataTemplate свойству [ItemTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx) элемента управления списком.

> [!IMPORTANT]
> Свойства **ItemTemplate** и **DisplayMemberPath** нельзя использовать одновременно. Если установлены оба свойства, возникает исключение.

Здесь определяется свойство DataTemplate, отображающее класс [Rectangle](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.rectangle.aspx) в цвете элемента, а также название цвета и значения RGB. 

> [!NOTE]
> При использовании [расширения разметки x:Bind](https://msdn.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) в DataTemplate необходимо задать DataType (`x:DataType`) в DataTemplate.

**XAML**
```XAML
<ListView x:Name="colorsListView">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="local:NamedColor">
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition MinWidth="54"/>
                    <ColumnDefinition Width="32"/>
                    <ColumnDefinition Width="32"/>
                    <ColumnDefinition Width="32"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Grid.RowDefinitions>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                <Rectangle Width="44" Height="44" Fill="{x:Bind Brush}" Grid.RowSpan="2"/>
                <TextBlock Text="{x:Bind Name}" Grid.Column="1" Grid.ColumnSpan="4"/>
                <TextBlock Text="{x:Bind Color.R}" Grid.Column="1" Grid.Row="1" Foreground="Red"/>
                <TextBlock Text="{x:Bind Color.G}" Grid.Column="2" Grid.Row="1" Foreground="Green"/>
                <TextBlock Text="{x:Bind Color.B}" Grid.Column="3" Grid.Row="1" Foreground="Blue"/>
            </Grid>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Вот как выглядят элементы данных при отображении с помощью этого шаблона данных.

![Элементы представления списка, отображаемые с помощью шаблона данных](images/listview-data-template-0.png)

Вам может потребоваться отображать данные в GridView. Вот еще один шаблон данных для отображения данных способом, более подходящим для макета сетки. На этот раз шаблон данных определяется как ресурс, а не встроенный элемент в XAML для GridView.


**XAML**
```xaml
<Page.Resources>
    <DataTemplate x:Key="namedColorItemGridTemplate" x:DataType="local:NamedColor">
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="32"/>
                <ColumnDefinition Width="32"/>
                <ColumnDefinition Width="32"/>
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="96"/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
    
            <Rectangle Width="96" Height="96" Fill="{x:Bind Brush}" Grid.ColumnSpan="3" />
            <!-- Name -->
            <Border Background="#AAFFFFFF" Grid.ColumnSpan="3" Height="40" VerticalAlignment="Top">
                <TextBlock Text="{x:Bind Name}" TextWrapping="Wrap" Margin="4,0,0,0"/>
            </Border>
            <!-- RGB -->
            <Border Background="Gainsboro" Grid.Row="1" Grid.ColumnSpan="3"/>
            <TextBlock Text="{x:Bind Color.R}" Foreground="Red"
                   Grid.Column="0" Grid.Row="1" HorizontalAlignment="Center"/>
            <TextBlock Text="{x:Bind Color.G}" Foreground="Green"
                   Grid.Column="1" Grid.Row="1" HorizontalAlignment="Center"/>
            <TextBlock Text="{x:Bind Color.B}" Foreground="Blue" 
                   Grid.Column="2" Grid.Row="1" HorizontalAlignment="Center"/>
            <!-- HEX -->
            <Border Background="Gray" Grid.Row="2" Grid.ColumnSpan="3">
                <TextBlock Text="{x:Bind Color}" Foreground="White" Margin="4,0,0,0"/>
            </Border>
        </Grid>
    </DataTemplate>
</Page.Resources>

...

<GridView x:Name="colorsGridView" 
          ItemTemplate="{StaticResource namedColorItemGridTemplate}"/>
```

Вот как выглядят данные при отображении в сетке с помощью этого шаблона данных.

![Элементы представления стеки, отображаемые с помощью шаблона данных](images/gridview-data-template.png)

### <a name="performance-considerations"></a>Вопросы производительности

Шаблоны данных— это основной способ определения внешнего вида представления списка. Они могут значительно влиять на производительность, если список содержит много элементов. 

Для каждого элемента в представлении списка создается экземпляр каждого элемента XAML в шаблоне данных. Например, шаблон сетки в предыдущем примере содержит 10 элементов XAML (1 элемент Grid, 1 элемент Rectangle, 3 элемента Border, 5 элементов TextBlock). Представление GridView, в котором отображается 20 элементов с помощью данного шаблона данных, создает не менее 200 элементов (20*10=200). Сокращение количества элементов в шаблоне данных может значительно сократить общее количество элементов, созданных для представления списка. Подробнее см. в разделе [Оптимизация пользовательского интерфейса ListView и GridView: сокращение числа элементов на каждый элемент](https://msdn.microsoft.com/windows/uwp/debug-test-perf/optimize-gridview-and-listview#element-reduction-per-item).

 Обратим внимание на этот раздел шаблона данных сетки. Рассмотрим несколько факторов сокращения числа элементов.

**XAML**
```xaml
<!-- RGB -->
<Border Background="Gainsboro" Grid.Row="1" Grid.ColumnSpan="3"/>
<TextBlock Text="{x:Bind Color.R}" Foreground="Red"
           Grid.Column="0" Grid.Row="1" HorizontalAlignment="Center"/>
<TextBlock Text="{x:Bind Color.G}" Foreground="Green"
           Grid.Column="1" Grid.Row="1" HorizontalAlignment="Center"/>
<TextBlock Text="{x:Bind Color.B}" Foreground="Blue" 
           Grid.Column="2" Grid.Row="1" HorizontalAlignment="Center"/>
```

 - Во-первых, в макете используется один элемент Grid. Можно использовать элемент Grid с одним столбцом и разместить эти три элемента TextBlock в элементе StackPanel, но при работе с многократно создаваемым шаблоном данных следует избегать встраивания одних панелей макета в другие.
 - Во-вторых, вы можете использовать элемент управления Border для отрисовки фона, фактически не размещая элементы внутри элемента Border. У элемента Border может быть только один дочерний элемент, поэтому для размещения трех элементов TextBlock внутри элемента Border в XAML вам потребуется добавить дополнительную панель макета. Не делая элементы TextBlock дочерними элементами Border, вы исключаете необходимость добавления панели для размещения элементов TextBlock.
 - Наконец, вы можете разместить элементы TextBlock внутри элемента StackPanel и установить свойства рамок элемента, вместо того чтобы использовать отдельный элемент Border. Однако элемент управления Border проще, чем StackPanel, поэтому при многократной отрисовке он оказывает меньшее воздействие на производительность.

## <a name="control-template"></a>Шаблон элемента управления
Шаблон элемента управления содержит визуальные элементы, отображающие состояние, например выделение, наведение указателя и фокус. Эти визуальные элементы отрисовываются либо над шаблоном данных, либо под ним. Здесь показаны некоторые распространенные визуальные элементы по умолчанию, отображаемые шаблоном элемента управления ListView.

- Наведение. Под шаблоном данных отображается светло-серый прямоугольник.  
- Выделение. Под шаблоном данных отображается голубой прямоугольник. 
- Фокус клавиатуры. Над шаблоном элемента отображается черно-белая пунктирная рамка. 

![Визуальные элементы состояний представления списка](images/listview-state-visuals.png)

Представление списка совмещает элементы шаблонов данных и элемента управления для формирования окончательных визуальных элементов, отображаемых на экране. Здесь визуальные элементы состояний отображаются в контексте представления списка.

![Представление списка с элементами в различных состояниях](images/listview-states.png)

### <a name="listviewitempresenter"></a>ListViewItemPresenter

Как мы уже говорили о шаблонах данных, количество элементов XAML, созданных для каждого элемента, может сильно сказаться на производительности представления списка. Так как шаблоны данных и элемента управления совмещаются при отображении каждого элемента, фактическое число элементов, требуемых для отображения элемента, включает количество элементов в обоих шаблонах.

Элементы управления ListView и GridView оптимизированы для сокращения числа элементов XAML, создаваемых для каждого элемента. Визуальные элементы **ListViewItem** создаются классом [ListViewItemPresenter](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.listviewitempresenter.aspx). Это особый элемент XAML для отображения сложных визуальных элементов фокуса, выделения и других визуальных состояний без излишней нагрузки многочисленных элементов пользовательского интерфейса.
 
> [!NOTE]
> В приложениях UWP для Windows 10 в **ListViewItem** и **GridViewItem** используется **ListViewItemPresenter**. Класс GridViewItemPresenter использовать не рекомендуется. Классы ListViewItem и GridViewItem устанавливают разные значения свойств в ListViewItemPresenter для получения разного визуального оформления по умолчанию.

Для изменения оформления контейнера элемента используйте свойство [ItemContainerStyle](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle.aspx) и предоставьте класс [Style](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.style.aspx), при этом его свойству [TargetType](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.style.targettype.aspx) следует задать значение **ListViewItem** или **GridViewItem**.

В этом примере к классу ListViewItem добавляется отбивка, чтобы оставить место между элементами списка.

```xaml
<ListView x:Name="colorsListView">
    <ListView.ItemTemplate>
        <!-- DataTemplate XAML shown in previous ListView example -->
    </ListView.ItemTemplate>

    <ListView.ItemContainerStyle>
        <Style TargetType="ListViewItem">
            <Setter Property="Padding" Value="0,4"/>
        </Style>
    </ListView.ItemContainerStyle>
</ListView>
```

Так выглядит представление списка с местом между элементами.

![Элементы представления списка с отбивкой](images/listview-data-template-1.png)

В стиле по умолчанию ListViewItem свойство ListViewItemPresenter **ContentMargin** имеет привязку [TemplateBinding](https://msdn.microsoft.com/windows/uwp/xaml-platform/templatebinding-markup-extension) к свойству ListViewItem **Padding** (`<ListViewItemPresenter ContentMargin="{TemplateBinding Padding}"/>`). При установке свойства Padding его значение фактически передается свойству ContentMargin класса ListViewItemPresenter.

Для изменения других свойств ListViewItemPresenter, не привязанных к свойствам ListViewItem по шаблону, вам потребуется изменить шаблон ListViewItem с помощью нового класса ListViewItemPresenter, в котором можно изменять свойства. 

> [!NOTE]
> Стили по умолчанию ListViewItem и GridViewItem задают большое количество свойств в ListViewItemPresenter. Следует всегда начинать с копии стиля по умолчанию и изменять только необходимые свойства. В противном случае визуальные элементы могут отображаться не так, как ожидается, так как некоторые свойства будут настроены неверно.

**Создание копии шаблона по умолчанию в Visual Studio**
 
1. Откройте панель "Структура документа" (**Представление > Windows (другие) > Структура документа**).
2. Выберите элемент списка или сетки для изменения. В этом примере изменяется элемент `colorsGridView`.
3. Щелкните правой кнопкой мыши и выберите **Изменить дополнительные шаблоны > Изменить созданный контейнер элемента (ItemContainerStyle) > Изменить копию**.
    ![Редактор Visual Studio](images/listview-itemcontainerstyle-vs.png)
4. В диалоговом окне "Создать ресурс стиля" введите имя стиля. В этом примере используется `colorsGridViewItemStyle`.
    ![Диалоговое окно "Создать ресурс стиля" в Visual Studio(images/listview-style-resource-vs.png)

В приложение добавляется копия стиля по умолчанию в качестве ресурсы, а свойство **GridView.ItemContainerStyle** устанавливается на этот ресурс, как показано в этом коде XAML. 

```xaml
<Style x:Key="colorsGridViewItemStyle" TargetType="GridViewItem">
    <Setter Property="FontFamily" Value="{ThemeResource ContentControlThemeFontFamily}"/>
    <Setter Property="FontSize" Value="{ThemeResource ControlContentThemeFontSize}" />
    <Setter Property="Background" Value="Transparent"/>
    <Setter Property="Foreground" Value="{ThemeResource SystemControlForegroundBaseHighBrush}"/>
    <Setter Property="TabNavigation" Value="Local"/>
    <Setter Property="IsHoldingEnabled" Value="True"/>
    <Setter Property="HorizontalContentAlignment" Value="Center"/>
    <Setter Property="VerticalContentAlignment" Value="Center"/>
    <Setter Property="Margin" Value="0,0,4,4"/>
    <Setter Property="MinWidth" Value="{ThemeResource GridViewItemMinWidth}"/>
    <Setter Property="MinHeight" Value="{ThemeResource GridViewItemMinHeight}"/>
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="GridViewItem">
                <ListViewItemPresenter 
                    CheckBrush="{ThemeResource SystemControlForegroundBaseMediumHighBrush}" 
                    ContentMargin="{TemplateBinding Padding}" 
                    CheckMode="Overlay" 
                    ContentTransitions="{TemplateBinding ContentTransitions}" 
                    CheckBoxBrush="{ThemeResource SystemControlBackgroundChromeMediumBrush}" 
                    DragForeground="{ThemeResource ListViewItemDragForegroundThemeBrush}" 
                    DragOpacity="{ThemeResource ListViewItemDragThemeOpacity}" 
                    DragBackground="{ThemeResource ListViewItemDragBackgroundThemeBrush}" 
                    DisabledOpacity="{ThemeResource ListViewItemDisabledThemeOpacity}" 
                    FocusBorderBrush="{ThemeResource SystemControlForegroundAltHighBrush}" 
                    FocusSecondaryBorderBrush="{ThemeResource SystemControlForegroundBaseHighBrush}" 
                    HorizontalContentAlignment="{TemplateBinding HorizontalContentAlignment}" 
                    PointerOverForeground="{ThemeResource SystemControlForegroundBaseHighBrush}" 
                    PressedBackground="{ThemeResource SystemControlHighlightListMediumBrush}" 
                    PlaceholderBackground="{ThemeResource ListViewItemPlaceholderBackgroundThemeBrush}" 
                    PointerOverBackground="{ThemeResource SystemControlHighlightListLowBrush}" 
                    ReorderHintOffset="{ThemeResource GridViewItemReorderHintThemeOffset}" 
                    SelectedPressedBackground="{ThemeResource SystemControlHighlightListAccentHighBrush}" 
                    SelectionCheckMarkVisualEnabled="True" 
                    SelectedForeground="{ThemeResource SystemControlForegroundBaseHighBrush}" 
                    SelectedPointerOverBackground="{ThemeResource SystemControlHighlightListAccentMediumBrush}" 
                    SelectedBackground="{ThemeResource SystemControlHighlightAccentBrush}" 
                    VerticalContentAlignment="{TemplateBinding VerticalContentAlignment}"/>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>

...

<GridView x:Name="colorsGridView" ItemContainerStyle="{StaticResource colorsGridViewItemStyle}"/>
```

Теперь вы можете изменять свойства в ListViewItemPresenter для управления флажком выделения, расположением элементов и цветами кисти для визуальных состояний. 

#### <a name="inline-and-overlay-selection-visuals"></a>Визуальные элементы выделения Inline и Overlay

Классы ListView и GridView указывают выделенные элементы по-разному в зависимости от элемента управления и [SelectionMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.selectionmode.aspx). Подробнее о выделении в представлении списка см. в разделе [ListView and GridView](listview-and-gridview.md). 

Если для **SelectionMode** установлено значение **Multiple**, в составе шаблона элемента управления элемента отображается флажок выделения. Вы можете использовать свойство [SelectionCheckMarkVisualEnabled](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.listviewitempresenter.selectioncheckmarkvisualenabled.aspx) для отключения флажка выделения в режиме выделения нескольких элементов. Но это свойство игнорируется в других режимах выделения. Так, флажок нельзя включить в режиме расширенного выделения или выделения одного элемента.

Вы можете установить свойство [CheckMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.listviewitempresenter.checkmode.aspx), чтобы указать, следует ли отображать флажок в стиле наложения или встроенном стиле.

- **Inline**. Этот стиль отображает флажок слева от содержимого, при этом фон контейнера элемента закрашивается для обозначения выделения. Это стиль по умолчанию для ListView.
- **Overlay**. Этот стиль отображает флажок над содержимым, при этом закрашивается только рамка контейнера элемента для обозначения выделения. Это стиль по умолчанию для GridView.

В этой таблице показаны визуальные элементы по умолчанию, используемые для обозначения выделения.

Режим выделения:&nbsp;&nbsp; | Один элемент/Расширенный | Несколько элементов
---------------|-----------------|---------
Встроенный | ![Выделение одного элемента или расширенное выделение во встроенном режиме](images/listview-single-selection.png) | ![Выделение нескольких элементов во встроенном режиме](images/listview-multi-selection.png)
Наложение | ![Выделение одного элемента или расширенное выделение в режиме наложения](images/gridview-single-selection.png) | ![Выделение нескольких элементов в режиме наложения](images/gridview-multi-selection.png)

> [!NOTE]
> В этом и последующих примерах простые строковые элементы данных отображаются без шаблонов данных с целью подчеркнуть визуальные элементы шаблона элемента управления.

Также существует несколько свойств кисти для изменения цвета флажка. Далее мы рассмотрим эти и другие свойства кисти.

#### <a name="brushes"></a>Кисти 

Во многих свойствах указываются кисти, используемые для различных визуальных состояний. Вам может потребоваться изменить их в соответствии с цветом фирменной символики. 

В этой таблице показаны визуальные состояния "Общее" и "Выделение" для ListViewItem, а также кисти, используемые для отрисовки визуальных элементов каждого состояния. На изображениях показан результат применения кистей к визуальным стилям выделения в режиме наложения и встроенном режиме.

> [!NOTE]
> В этой таблице измененные значения цветов кистей— это встроенные именованные цвета, причем цвета выделены, чтобы подчеркнуть место их применения в шаблоне. Это не цвета по умолчанию для визуальных состояний. При изменении цветов по умолчанию в приложении следует использовать ресурсы кистей для изменения значений цветов, как сделано в шаблоне по умолчанию.

Название состояния/кисти | Встроенный стиль | Стиль наложения
------------|--------------|--------------
<b>Normal</b><ul><li><b>CheckBoxBrush="Red"</b></li></ul> | ![Выделение элемента во встроенном режиме: обычное](images/listview-item-normal.png) | ![Выделение элемента в режиме наложения: обычное](images/gridview-item-normal.png)
<b>PointerOver</b><ul><li><b>PointerOverForeground="DarkOrange"</b></li><li><b>PointerOverBackground="MistyRose"</b></li><li>CheckBoxBrush="Red"</li></ul> | ![Выделение элемента во встроенном режиме: наведение указателя](images/listview-item-pointerover.png) | ![Выделение элемента в режиме наложения: наведение указателя](images/gridview-item-pointerover.png)
<b>Pressed</b><ul><li><b>PressedBackground="LightCyan"</b></li><li>PointerOverForeground="DarkOrange"</li><li>CheckBoxBrush="Red"</li></ul> | ![Выделение элемента во встроенном режиме: нажатие](images/listview-item-pressed.png) | ![Выделение элемента в режиме наложения: нажатие](images/gridview-item-pressed.png)
<b>Selected</b><ul><li><b>SelectedForeground="Navy"</b></li><li><b>SelectedBackground="Khaki"</b></li><li><b>CheckBrush="Green"</b></li><li>CheckBoxBrush="Red" (только встроенный режим)</li></ul> | ![Выделение элемента во встроенном режиме: выделение](images/listview-item-selected.png) | ![Выделение элемента в режиме наложения: выделение](images/gridview-item-selected.png)
<b>PointerOverSelected</b><ul><li><b>SelectedPointerOverBackground="Lavender"</b></li><li>SelectedForeground="Navy"</li><li>SelectedBackground="Khaki" (только режим наложения)</li><li>CheckBrush="Green"</li><li>CheckBoxBrush="Red" (только встроенный режим)</li></ul> | ![Выделение элемента во встроенном режиме: наведение указателя и выделение](images/listview-item-pointeroverselected.png) | ![Выделение элемента в режиме наложения: наведение указателя и выделение](images/gridview-item-pointeroverselected.png)
<b>PressedSelected</b><ul><li><b>SelectedPressedBackground="MediumTurquoise"</b></li></li><li>SelectedForeground="Navy"</li><li>SelectedBackground="Khaki" (только режим наложения)</li><li>CheckBrush="Green"</li><li>CheckBoxBrush="Red" (только встроенный режим)</li></ul> | ![Выделение элемента во встроенном режиме: нажатие и выделение](images/listview-item-pressedselected.png) | ![Выделение элемента в режиме наложения: нажатие и выделение](images/gridview-item-pressedselected.png)
<b>Focused</b><ul><li><b>FocusBorderBrush="Crimson"</b></li><li><b>FocusSecondaryBorderBrush="Gold"</b></li><li>CheckBoxBrush="Red"</li></ul> | ![Выделение элемента во встроенном режиме: в фокусе](images/listview-item-focused.png) | ![Выделение элемента в режиме наложения: в фокусе](images/gridview-item-focused.png)

ListViewItemPresenter имеет другие свойства кисти для заполнителей данных и состояний перетаскивания. При использовании добавочной загрузки или перетаскивания в представлении списка следует решить, нужно ли изменять и эти дополнительные свойства кисти. Полный список изменяемых свойств см. в разделе класса ListViewItemPresenter. 

### <a name="expanded-xaml-item-templates"></a>Расширенные шаблоны элементов XAML

Если вам требуется внести больше изменений, чем возможно с помощью свойств **ListViewItemPresenter** (например, если нужно изменить расположение флажка), вы можете использовать шаблоны *ListViewItemExpanded* или *GridViewItemExpanded*. Эти шаблоны включены вместе со стилями по умолчанию в generic.xaml. Они следуют стандартной схеме XAML при формировании всех визуальных элементов из отдельных элементов пользовательского интерфейса.

Как упоминалось ранее, количество элементов пользовательского интерфейса в шаблоне элемента заметно сказывается на производительности представления списка Замена ListViewItemPresenter расширенными шаблонами XAML приводит к резкому увеличению числа элементов, поэтому ее не рекомендуется выполнять, если в представлении списка должно отображаться большое число элементов или производительности недостаточно.

> [!NOTE]
> **ListViewItemPresenter** поддерживается только, если свойству [ItemsPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemspanel.aspx) представления списка задано значение [ItemsWrapGrid](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemswrapgrid.aspx) или [ItemsStackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemsstackpanel.aspx). Если вы измените значение свойства ItemsPanel на [VariableSizedWrapGrid](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.variablesizedwrapgrid.aspx), [WrapGrid](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.wrapgrid.aspx) или [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx), шаблон элемента автоматически переключится на расширенный шаблон XAML. Подробнее см. в разделе [Оптимизация пользовательского интерфейса ListView и GridView](https://msdn.microsoft.com/windows/uwp/debug-test-perf/optimize-gridview-and-listview).

Для настройки расширенного шаблона XAML вам потребуется создать его копию в приложении и привязать свойство **ItemContainerStyle** к копии.

**Копирование расширенного шаблона**
1. Установите свойству ItemContainerStyle значение, показанное здесь для ListView или GridView.
    ```xaml
    <ListView ItemContainerStyle="{StaticResource ListViewItemExpanded}"/>
    <GridView ItemContainerStyle="{StaticResource GridViewItemExpanded}"/>
    ```
2. На панели свойств Visual Studio раскройте раздел "Прочее" и найдите свойство ItemContainerStyle. (Убедитесь, что выбран класс ListView или GridView.)
3. Нажмите метку свойства ItemContainerStyle. (Это небольшое окно рядом с TextBox. Оно зеленого цвета, то есть настроено на StaticResource.) Откроется меню свойства.
4. В меню свойств нажмите **Преобразовать в новый ресурс**. 
    
    ![Меню свойств Visual Studio](images/listview-convert-resource-vs.png)
5. В диалоговом окне "Создать ресурс стиля" введите название для ресурса и нажмите "ОК".

В приложении создается копия расширенного шаблона из generic.xaml, которую можно изменять при необходимости.


## <a name="related-articles"></a>Связанные статьи

- [Списки](lists.md)
- [ListView и GridView](listview-and-gridview.md)

