---
Description: Использование шаблонов для изменения внешнего вида элементов в элементах ListView или GridView.
title: Контейнеры и шаблоны элементов
label: Item containers and templates
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: d8eb818d-b62e-4314-a612-f29142dbd93f
pm-contact: predavid
design-contact: kimsea
dev-contact: ranjeshj
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 2445f1b718532fd0862c276e8fadf12e88fe36ac
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79209079"
---
# <a name="item-containers-and-templates"></a>Контейнеры и шаблоны элементов

 

Элементы управления **ListView** и **GridView** управляют расположением своих элементов (горизонтальное и вертикальное выравнивание, обтекание и т. д.) и взаимодействием пользователя с этими элементами, но не тем, как отдельные элементы отображаются на экране. Визуализацией элементов управляют контейнеры элементов. При добавлении элементов в представление списка они автоматически помещаются в контейнер. По умолчанию для ListView используется контейнер элементов [ListViewItem](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListViewItem), а для GridView — [GridViewItem](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridViewItem).

> **Важные API**: [класс ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview), [класс GridView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridview), [класс ListViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewitem), [класс GridViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridviewitem), [свойство ItemTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate), [свойство ItemContainerStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle).


> [!NOTE]
> Элементы управления ListView и GridView являются производными от класса [ListViewBase](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase), поэтому они имеют аналогичные функции, но отображают данные по-другому. В этой статье вся информация о представлении списка актуальна для обоих элементов управления (ListView и GridView), если не указано иное. Когда упоминаются классы ListView или ListViewItem, нужно помнить, что префикс *List* может быть заменен префиксом *Grid* для соответствующего эквивалента сетки (GridView или GridViewItem). 

## <a name="listview-items-and-gridview-items"></a>Элементы ListView и GridView
Как упоминалось выше, элементы ListView автоматически помещаются в контейнер ListViewItem, а элементы GridView — в контейнер GridViewItem. Эти контейнеры являются элементами управления, которые обладают собственными встроенными стилями и возможностями взаимодействия, но их также можно легко настроить. Однако перед настройкой обязательно ознакомьтесь с рекомендованными стилями и указаниями для ListViewItem и GridViewItem.

- Элементы **ListViewItem** имеют вытянутую форму и в основном предназначены для текста. Слева от текста могут отображаться значки или изображения.
- Элементы **GridViewItem** обычно имеют квадратную форму или по крайней мере форму вытянутого прямоугольника. Элементы предназначены для изображений и могут содержать текст, отображаемый вокруг или поверх изображения. 

## <a name="introduction-to-customization"></a>Введение в настройку
Контейнерные элементы управления (такие как ListViewItem и GridViewItem) состоят из двух важных частей, сочетание которых формирует окончательное визуальное оформление элемента. Это *шаблон данных* и *шаблон элемента управления*.

- **Шаблон данных.** Класс [DataTemplate](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate) назначается свойству [ItemTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate) представления списка, чтобы указать способ отображения отдельных элементов данных.
- **Шаблон элемента управления**. Шаблон элемента управления предоставляет часть визуализации элемента, за которую отвечает платформа, например визуальные состояния. Вы можете изменять шаблон элемента управления с помощью свойства [ItemContainerStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle). Как правило, таким образом изменяются цвета представления списка в соответствии с фирменной символикой или способ отображения выбранных элементов.

На этом изображении показана комбинация шаблонов элементов управления и данных для формирования окончательного визуального оформления элемента.

![Шаблоны элемента управления и данных представления списка](images/listview-visual-parts.png)

Вот XAML-код для создания этого элемента. Мы опишем эти шаблоны позже.

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
                           FontSize="14" Grid.Column="1"
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

> [!IMPORTANT]
> Шаблоны данных и шаблоны элементов управления используются для настройки стиля ListView, GridView и многих других элементов управления. К ним относятся элементы управления с собственными встроенными стилями, такие как FlipView, и пользовательские элементы управления, такие как ItemsRepeater. Хотя приведенный ниже пример относится только к ListView и GridView, продемонстрированные в нем принципы могут применяться и ко многим другим элементам управления. 
 
## <a name="prerequisites"></a>Предварительные условия

- Мы предполагаем, что вы умеете пользоваться элементами управления представления списка. См. сведения о [ListView и GridView](listview-and-gridview.md).
- Мы также предполагаем, что вы знакомы со стилями и шаблонами элементов управления и, среди прочего, умеете использовать стили в качестве встроенных элементов или ресурсов. См. сведения о [настройке стиля элементов управления](xaml-styles.md) и [шаблонах элементов управления](control-templates.md).

## <a name="the-data"></a>Данные

Прежде чем углубляться в особенности отображения элементов данных в представлении списка, необходимо разобраться в отображаемых данных. В этом примере мы создаем тип данных с именем `NamedColor`. Он сочетает название и значение цвета, а также класс **SolidColorBrush** для цвета, которые предоставляются в виде трех свойств: `Name`, `Color` и `Brush`.
 
Затем мы заполняем класс **List** объектом `NamedColor` для каждого именованного цвета в классе [Colors](https://docs.microsoft.com/uwp/api/windows.ui.colors). Этот список устанавливается как свойство [ItemsSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) для представления списка

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

Шаблон данных определяется, чтобы сообщить представлению списка о способе отображения элемента данных. 

По умолчанию элемент данных отображается в представлении списка в виде строки, представляющей объект данных, к которому он привязан. Если вы отображаете данные NamedColors в представлении списка, не сообщая способ отображения, отображается лишь значение, возвращаемое методом **ToString**. Вот как это выглядит.

**XAML**
```xaml
<ListView x:Name="colorsListView"/>
```

![Представление списка, отображающее строковое представление элементов](images/listview-no-template.png)

Вы можете отображать строковое представление конкретного свойства элемента данных, установив [DisplayMemberPath](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.displaymemberpath) для этого свойства. Здесь свойству `Name` элемента `NamedColor` назначается свойство DisplayMemberPath.

**XAML**
```xaml
<ListView x:Name="colorsListView" DisplayMemberPath="Name" />
```

Теперь элементы в представлении списка отсортированы по имени, как показано в примере. Это удобнее, но малоинтересно. Кроме того, отображается далеко не вся информация.

![Представление списка с отображением строкового представления свойства элемента](images/listview-display-member-path.png)

Более интересным является сложно оформленное представление данных. Чтобы точно определить, как должны отображаться элементы в представлении списка, вы создаете [DataTemplate](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate). В DataTemplate с помощью XAML определяется макет и внешний вид элементов управления, используемых для отображения отдельного элемента. Элементы управления в макете могут быть привязаны к свойствам объекта данных или иметь статическое содержимое, задаваемое в коде. Вы назначаете DataTemplate свойству [ItemTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate) элемента управления списком.

> [!IMPORTANT]
> Свойства **ItemTemplate** и **DisplayMemberPath** нельзя использовать одновременно. Если настроены оба свойства, возникает исключение.

Здесь определяется свойство DataTemplate, отображающее класс [Rectangle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.rectangle) в цвете элемента, а также название цвета и значения RGB. 

> [!NOTE]
> При использовании [расширения разметки x:Bind](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) в DataTemplate необходимо задать DataType (`x:DataType`) в DataTemplate.

**XAML**
```xaml
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

> [!IMPORTANT]
> По умолчанию содержимое в элементах ListViewItem выравнивается по левому краю, т. е. их свойство [HorizontalContentAlignmentProperty](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.horizontalcontentalignment#Windows_UI_Xaml_Controls_Control_HorizontalContentAlignment) имеет значение Left. Если в ListViewItem имеется несколько элементов, расположенных рядом по горизонтали, например столбцы элементов или элементы, размещенные в одной строке сетки, то все они будут выровнены по левому краю и разделены только заданными для них полями. 
<br/><br/> Чтобы заполнить элементами все текстовое поле ListItem, необходимо задать для свойства HorizontalContentAlignmentProperty значение [Stretch](https://docs.microsoft.com/uwp/api/windows.ui.xaml.horizontalalignment), воспользовавшись классом [Setter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.setter) в ListView.

```xaml
<ListView.ItemContainerStyle>
    <Style TargetType="ListViewItem">
        <Setter Property="HorizontalContentAlignment" Value="Stretch"/>
    </Style>
</ListView.ItemContainerStyle>
```


Вам может потребоваться отображать данные в GridView. Вот еще один шаблон данных для отображения данных способом, который подходит для макета сетки. На этот раз шаблон данных определяется как ресурс, а не встроенный элемент в XAML для GridView.


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

### <a name="performance-considerations"></a>Рекомендации по производительности

Шаблоны данных — это основной способ определения внешнего вида для представления списка. Они могут значительно влиять на производительность, если список содержит много элементов. 

Для каждого элемента в представлении списка создается экземпляр каждого элемента XAML в шаблоне данных. Например, шаблон сетки в предыдущем примере содержит 10 элементов XAML (1 элемент Grid, 1 элемент Rectangle, 3 элемента Border, 5 элементов TextBlock). Представление GridView, в котором отображается 20 элементов с использованием этого шаблона данных, создает не менее 200 элементов (20*10=200). Сокращение количества элементов в шаблоне данных может значительно сократить общее количество элементов, созданных для представления списка. Подробнее см. в разделе [Оптимизация пользовательского интерфейса ListView и GridView. Уменьшение элементов для каждого элемента](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-gridview-and-listview).

 Обратите внимание на этот раздел шаблона данных сетки. Давайте рассмотрим, как можно сократить число элементов.

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

 - Во-первых, в макете используется 1 элемент Grid. Можно использовать элемент Grid с одним столбцом и разместить эти 3 элемента TextBlock в элементе StackPanel, но при работе с многократно создаваемым шаблоном данных следует избегать встраивания одних панелей макета в другие.
 - Во-вторых, вы можете использовать элемент управления Border для отрисовки фона, не размещая элементы внутри этого элемента Border. У элемента Border может быть только 1 дочерний элемент, поэтому для размещения 3 элементов TextBlock внутри элемента Border в XAML вам потребуется добавить дополнительную панель макета. Не делая элементы TextBlock дочерними элементами Border, вы исключаете необходимость добавления панели для размещения элементов TextBlock.
 - Наконец, вы можете разместить элементы TextBlock внутри элемента StackPanel и установить свойства рамок элемента, вместо того чтобы использовать отдельный элемент Border. Но элемент управления Border проще, чем StackPanel, поэтому при многократной отрисовке он меньше влияет на производительность.

### <a name="using-different-layouts-for-different-items"></a>Использование различных макетов для разных элементов


## <a name="control-template"></a>Шаблон элемента управления
Шаблон элемента управления содержит визуальные элементы, отображающие состояние, например при выделении, наведении указателя и фокусировке. Эти визуальные элементы отрисовываются над шаблоном данных или под ним. Здесь показаны некоторые распространенные визуальные элементы, отображаемые шаблоном элемента управления ListView.

- Наведение. Под шаблоном данных отображается светло-серый прямоугольник.  
- Выделение. Под шаблоном данных отображается голубой прямоугольник. 
- Клавиатурная фокусировка. Над шаблоном элемента отображается черно-белая пунктирная рамка. 

![Визуальные элементы состояний представления списка](images/listview-state-visuals.png)

Представление списка совмещает элементы шаблонов данных и элемента управления для формирования окончательных визуальных элементов, отображаемых на экране. Здесь визуальные элементы состояний отображаются в контексте представления списка.

![Представление списка с элементами в разных состояниях](images/listview-states.png)

### <a name="listviewitempresenter"></a>ListViewItemPresenter

Выше мы говорили о шаблонах данных в контексте того, что количество элементов XAML, созданных для каждого элемента, может сильно влиять на производительность представления списка. Так как шаблоны данных и элемента управления совмещаются при отображении каждого элемента, фактическое количество элементов, требуемых для отображения элемента, включает количество элементов в обоих шаблонах.

Элементы управления ListView и GridView позволяют сократить количество элементов XAML, создаваемых для каждого элемента. Визуальные элементы **ListViewItem** создаются классом [ListViewItemPresenter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter). Это особый элемент XAML для отображения сложных визуальных элементов фокуса, выделения и других визуальных состояний без излишней нагрузки многочисленных элементов пользовательского интерфейса.
 
> [!NOTE]
> В приложениях UWP для Windows 10 в **ListViewItem** и **GridViewItem** используется **ListViewItemPresenter**. Класс GridViewItemPresenter использовать не рекомендуется. Классы ListViewItem и GridViewItem устанавливают разные значения свойств в ListViewItemPresenter для получения разного визуального оформления по умолчанию.

Для изменения оформления контейнера элемента используйте свойство [ItemContainerStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle) и предоставьте класс [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style), при этом его свойству [TargetType](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.targettype) следует задать значение **ListViewItem** или **GridViewItem**.

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

В стиле по умолчанию ListViewItem свойство **ContentMargin** класса имеет привязку [TemplateBinding](https://docs.microsoft.com/windows/uwp/xaml-platform/templatebinding-markup-extension) к свойству **Padding** класса ListViewItem (`<ListViewItemPresenter ContentMargin="{TemplateBinding Padding}"/>`). При установке свойства Padding его значение фактически передается свойству ContentMargin класса ListViewItemPresenter.

Для изменения других свойств ListViewItemPresenter, не привязанных шаблоном к свойствам ListViewItem, вам потребуется изменить шаблон ListViewItem с помощью нового класса ListViewItemPresenter, в котором можно изменять свойства. 

> [!NOTE]
> Стили по умолчанию ListViewItem и GridViewItem задают большое количество свойств в ListViewItemPresenter. Следует всегда начинать с копии стиля по умолчанию и изменять только необходимые свойства. В противном случае визуальные элементы могут отображаться не так, как ожидается, так как некоторые свойства будут настроены неправильно.

**Создание копии шаблона по умолчанию в Visual Studio**
 
1. Откройте панель "Структура документа" (**Представление > Windows (другие) > Структура документа**).
2. Выберите элемент списка или сетки для изменения. В этом примере изменяется элемент `colorsGridView`.
3. Щелкните правой кнопкой мыши и выберите **Изменить дополнительные шаблоны > Изменить созданный контейнер элемента (ItemContainerStyle) > Изменить копию**.
    ![Редактор Visual Studio](images/listview-itemcontainerstyle-vs.png)
4. В диалоговом окне "Создание ресурса стиля" введите имя стиля. В этом примере используется `colorsGridViewItemStyle`.
    ![Диалоговое окно "Создание ресурса стиля" в Visual Studio(images/listview-style-resource-vs.png)

В приложение добавляется копия стиля по умолчанию в качестве ресурса, а свойство **GridView.ItemContainerStyle** настраивается для этого ресурса, как показано в этом коде XAML. 

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

Классы ListView и GridView указывают выделенные элементы по-разному в зависимости от элемента управления и [SelectionMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selectionmode). См. сведения о выделении в представлении списка в описании [ListView и GridView](listview-and-gridview.md). 

Если для **SelectionMode** установлено значение **Multiple**, в составе шаблона элемента управления элемента отображается флажок выделения. Вы можете использовать свойство [SelectionCheckMarkVisualEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectioncheckmarkvisualenabled) для отключения флажка выделения в режиме выбора нескольких элементов. Но это свойство игнорируется в других режимах выделения. Так, флажок нельзя включить в режиме расширенного выделения или выделения одного элемента.

Вы можете установить свойство [CheckMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.checkmode), чтобы указать, следует ли отображать флажок в стиле наложения или встроенном стиле.

- **Inline**. Этот стиль отображает флажок слева от содержимого, при этом фон контейнера элемента закрашивается для обозначения выделения. Это стиль по умолчанию для ListView.
- **Overlay**. Этот стиль отображает флажок над содержимым, при этом закрашивается только рамка контейнера элемента для обозначения выделения. Это стиль по умолчанию для GridView.

В этой таблице показаны визуальные элементы по умолчанию, используемые для обозначения выделения.

Режим выделения:&nbsp;&nbsp; | Один или расширенный | Несколько
---------------|-----------------|---------
Встроенный | ![Выделение одного элемента или расширенное выделение во встроенном режиме](images/listview-single-selection.png) | ![Выделение нескольких элементов во встроенном режиме](images/listview-multi-selection.png)
Наложение | ![Выделение одного элемента или расширенное выделение в режиме наложения](images/gridview-single-selection.png) | ![Выделение нескольких элементов в режиме наложения](images/gridview-multi-selection.png)

> [!NOTE]
> В этом и последующих примерах простые строковые элементы данных отображаются без шаблонов данных, чтобы выделить визуальные элементы шаблона элемента управления.

Также существует несколько свойств кисти для изменения цвета флажка. Далее мы рассмотрим эти и другие свойства кисти.

#### <a name="brushes"></a>Кисти 

Во многих свойствах указываются кисти, используемые для разных визуальных состояний. Вам может потребоваться изменить их в соответствии с цветом фирменной символики. 

В этой таблице показаны визуальные состояния "Общее" и "Выделение" для ListViewItem, а также кисти, используемые для отрисовки визуальных элементов каждого состояния. На изображениях показан результат применения кистей к визуальным стилям выделения в режиме наложения и встроенном режиме.

> [!NOTE]
> В этой таблице измененные значения цветов кистей — это встроенные именованные цвета, причем они выделены, чтобы было видно, где они применяются в шаблоне. Это не цвета по умолчанию для визуальных состояний. При изменении цветов по умолчанию в приложении следует использовать ресурсы кистей для изменения значений цветов, как сделано в шаблоне по умолчанию.

Состоянии и название кисти | Встроенный стиль | Стиль наложения
------------|--------------|--------------
<b>Normal</b><ul><li><b>CheckBoxBrush="Red"</b></li></ul> | ![Встроенный режим, обычное выделение](images/listview-item-normal.png) | ![Режим наложения, обычное выделение](images/gridview-item-normal.png)
<b>PointerOver</b><ul><li><b>PointerOverForeground="DarkOrange"</b></li><li><b>PointerOverBackground="MistyRose"</b></li><li>CheckBoxBrush="Red"</li></ul> | ![Встроенный режим, выделение с наведением указателя](images/listview-item-pointerover.png) | ![Режим наложения, выделение с наведением указателя](images/gridview-item-pointerover.png)
<b>Pressed</b><ul><li><b>PressedBackground="LightCyan"</b></li><li>PointerOverForeground="DarkOrange"</li><li>CheckBoxBrush="Red"</li></ul> | ![Встроенный режим, выделение с нажатием](images/listview-item-pressed.png) | ![Режим наложения, выделение с нажатием](images/gridview-item-pressed.png)
<b>Selected</b><ul><li><b>SelectedForeground="Navy"</b></li><li><b>SelectedBackground="Khaki"</b></li><li><b>CheckBrush="Green"</b></li><li>CheckBoxBrush="Red" (только встроенный режим)</li></ul> | ![Встроенный режим, выделение](images/listview-item-selected.png) | ![Режим наложения, выделение](images/gridview-item-selected.png)
<b>PointerOverSelected</b><ul><li><b>SelectedPointerOverBackground="Lavender"</b></li><li>SelectedForeground="Navy"</li><li>SelectedBackground="Khaki" (только режим наложения)</li><li>CheckBrush="Green"</li><li>CheckBoxBrush="Red" (только встроенный режим)</li></ul> | ![Встроенный режим, наведение указателя поверх выделения](images/listview-item-pointeroverselected.png) | ![Режим наложения, наведение указателя поверх выделения](images/gridview-item-pointeroverselected.png)
<b>PressedSelected</b><ul><li><b>SelectedPressedBackground="MediumTurquoise"</b></li></li><li>SelectedForeground="Navy"</li><li>SelectedBackground="Khaki" (только режим наложения)</li><li>CheckBrush="Green"</li><li>CheckBoxBrush="Red" (только встроенный режим)</li></ul> | ![Встроенный режим, нажатие выделенного элемента](images/listview-item-pressedselected.png) | ![Режим наложения, нажатие выделенного элемента](images/gridview-item-pressedselected.png)
<b>Focused</b><ul><li><b>FocusBorderBrush="Crimson"</b></li><li><b>FocusSecondaryBorderBrush="Gold"</b></li><li>CheckBoxBrush="Red"</li></ul> | ![Встроенный режим, фокус на выделенном элементе](images/listview-item-focused.png) | ![Режим наложения, фокус на выделенном элементе](images/gridview-item-focused.png)

ListViewItemPresenter имеет другие свойства кисти для заполнителей данных и состояний перетаскивания. При использовании добавочной загрузки или перетаскивания в представлении списка следует решить, нужно ли изменять и эти дополнительные свойства кисти. См. полный список изменяемых свойств в разделе класса ListViewItemPresenter. 

### <a name="expanded-xaml-item-templates"></a>Расширенные шаблоны элементов XAML

Если вам требуется внести больше изменений, чем можно с помощью свойств **ListViewItemPresenter** (например, если нужно изменить расположение флажка), используйте шаблоны *ListViewItemExpanded* или *GridViewItemExpanded*. Эти шаблоны доступны со стилями по умолчанию в generic.xaml. Они соответствуют стандартному подходу XAML, когда все визуальные элементы формируются из отдельных элементов пользовательского интерфейса.

Как упоминалось ранее, большое количество элементов пользовательского интерфейса в шаблоне элемента заметно влияет на производительность представления списка Замена ListViewItemPresenter расширенными шаблонами XAML приводит к существенному увеличению количества элементов, поэтому ее не рекомендуется выполнять, если в представлении списка должно отображаться большое количество элементов или когда производительность должна оставаться высокой.

> [!NOTE]
> **ListViewItemPresenter** поддерживается, только если свойству [ItemsPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemspanel) представления списка задано значение [ItemsWrapGrid](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemswrapgrid) или [ItemsStackPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel). Если вы измените значение свойства ItemsPanel на [VariableSizedWrapGrid](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.variablesizedwrapgrid), [WrapGrid](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.wrapgrid) или [StackPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.stackpanel), шаблон элемента автоматически переключится на расширенный шаблон XAML. См. сведения о том, как [оптимизировать пользовательский интерфейс ListView и GridView](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-gridview-and-listview).

Для настройки расширенного шаблона XAML вам потребуется создать его копию в приложении и привязать свойство **ItemContainerStyle** к копии.

**Копирование расширенного шаблона**
1. Задайте свойству ItemContainerStyle значение, как показано здесь для ListView или GridView.
    ```xaml
    <ListView ItemContainerStyle="{StaticResource ListViewItemExpanded}"/>
    <GridView ItemContainerStyle="{StaticResource GridViewItemExpanded}"/>
    ```
2. На панели свойств Visual Studio раскройте раздел "Прочее" и найдите свойство ItemContainerStyle. (Убедитесь, что выбран класс ListView или GridView.)
3. Нажмите метку свойства ItemContainerStyle. (Это небольшое окно рядом с TextBox. Оно зеленого цвета, то есть настроено на StaticResource.) Откроется меню свойства. Откроется меню свойств.
4. В меню свойств щелкните **Преобразовать в новый ресурс**. 
    
    ![Меню свойств Visual Studio](images/listview-convert-resource-vs.png)
5. В диалоговом окне "Создать ресурс стиля" введите название для ресурса и щелкните "ОК".

В приложении будет создана копия расширенного шаблона из generic.xaml, которую можно изменять при необходимости.


## <a name="related-articles"></a>Похожие статьи

- [Списки](lists.md)
- [ListView и GridView](listview-and-gridview.md)

