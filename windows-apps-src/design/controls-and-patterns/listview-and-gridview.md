---
Description: Используйте элементы управления ListView и GridView, чтобы отображать наборы данных, например галерею изображений или набор сообщений электронной почты, и выполнять с ними различные действия.
title: Представления списка и сетки
label: List view and grid view
template: detail.hbs
ms.date: 11/04/2019
ms.topic: article
keywords: windows 10, uwp
ms.assetid: f8532ba0-5510-4686-9fcf-87fd7c643e7b
pm-contact: predavid
design-contact: kimsea
dev-contact: ranjeshj
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: c130505ec79ca83698fd79df26464969afe79c36
ms.sourcegitcommit: 1b06c27e7fa4726fd950cbeaf05206c0a070e3c7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80893474"
---
# <a name="list-view-and-grid-view"></a>Представления списка и сетки

Большая часть приложений отображает наборы данных, такие как коллекции изображений или набор сообщений электронной почты, и управляет ими. Платформа пользовательского интерфейса XAML предоставляет элементы управления ListView и GridView, упрощающие отображение данных и управление ими в приложении.  

> **Важные API**: [класс ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview), [класс GridView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.gridview), [свойство ItemsSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource), [свойство Items](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.items)

> [!NOTE]
> Элементы управления ListView и GridView являются производными от класса [ListViewBase](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase), поэтому они имеют аналогичные функции, но отображают данные по-другому. В этой статье вся информация о представлении списка актуальна для обоих элементов управления (ListView и GridView), если не указано иное. Когда упоминаются классы ListView или ListViewItem, нужно помнить, что префикс *List* может быть заменен префиксом *Grid* для соответствующего эквивалента сетки (GridView или GridViewItem). 

ListView и GridView предоставляют множество преимуществ для работы с коллекциями. Они просты в реализации и предоставляют базовый пользовательский интерфейс, а также возможности взаимодействия и прокрутки. Кроме того, их легко настраивать. ListView и GridView могут быть привязаны к существующим динамическим источникам данных или жестко запрограммированным данным, предоставленным в XAML или в коде программной части. 

Эти два элемента управления являются гибкими и подходят для многих вариантов использования. Однако в целом они лучше всего работают с коллекциями, в которых все элементы имеют одинаковую базовую структуру и внешний вид, а также единые возможности взаимодействия, т. е. все они выполняют одно и то же действие при щелчке (открытие ссылки, переход и т. д.).


## <a name="differences-between-listview-and-gridview"></a>Различия между ListView и GridView

### <a name="listview"></a>ListView
ListView отображает данные в одном столбце и упорядочивает их по вертикали. ListView лучше подходит для элементов, использующим текст в качестве точки фокуса, и коллекций, которые предназначены для чтения сверху вниз (т. е. упорядочены в алфавитном порядке). Несколько распространенных вариантов использования ListView включают в себя списки сообщений и результатов поиска. Коллекции, которые нужно отобразить в нескольких столбцах или в формате таблицы, _не_ должны использовать ListView. Вместо этого рекомендуем применить [DataGrid](https://docs.microsoft.com/windows/communitytoolkit/controls/datagrid).

![Представление списка со сгруппированными данными](images/listview-grouped-example-resized-final.png)

### <a name="gridview"></a>GridView
Элемент управления GridView представляет коллекцию элементов, которые расположены в строках и столбцах, с вертикальной прокруткой. Данные упорядочиваются по горизонтали до тех пор, пока не заполнят столбцы, после чего начинается заполнение следующей строки. GridView лучше подходит для элементов, использующих изображения в качестве точки фокуса, и коллекций, которые могут считываться из стороны в сторону или не сортируются в каком-либо определенном порядке. Распространенным вариантом использования GridView является коллекция фотографий или продуктов.

![Пример библиотеки содержимого](images/gridview-simple-example-final.png)

## <a name="which-collection-control-should-you-use-a-comparison-with-itemsrepeater"></a>Какой элемент управления коллекциями следует использовать? Сравнение с ItemsRepeater

ListView и GridView — это элементы управления, которые готовы к отображению любой коллекции с помощью собственного встроенного пользовательского интерфейса и возможностей взаимодействия. Элемент управления [ItemsRepeater](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/items-repeater) также используется для отображения коллекций, но он был разработан как стандартный блок для создания пользовательского элемента управления, удовлетворяющего конкретным требованиям к пользовательскому интерфейсу. Ниже перечислены наиболее важные отличия, которые помогут вам выбрать элемент управления.

-   ListView и GridView — это элементы управления с широкими возможностями, которые почти не нуждаются в настройке, но предоставляют множество преимуществ. ItemsRepeater — это стандартный блок, предназначенный для создания собственного элемента управления макетом и не обладающий такими же встроенными возможностями и функциями. Все необходимые функции или возможности взаимодействия требуется реализовать самостоятельно.
-   ItemsRepeater следует использовать, если у вас есть очень специализированный пользовательский интерфейс, который невозможно реализовать с помощью ListView или GridView, или если у вас есть источник данных, требующий реализации сильно отличающегося поведения для каждого элемента.


Дополнительные сведения о ItemsRepeater можно получить, прочитав [руководство](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/items-repeater) по этому элементу управления и [документацию по API](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.itemsrepeater?view=winui-2.2).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/ListView">ListView</a> или <a href="xamlcontrolsgallery:/item/GridView">GridView</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-a-listview-or-gridview"></a>Создание ListView или GridView

Элементы управления ListView и GridView относятся к типу [ItemsControl](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol), поэтому они могут содержать коллекцию элементов любого типа. Прежде чем отобразить что-то на экране, необходимо включить элементы в коллекцию [Items](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.items) элемента управления ListView или GridView. Для добавления элементов в представление заполните коллекцию [Items](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.items) напрямую или свяжите свойство [ItemsSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) с источником данных. 

> [!IMPORTANT]
> Для заполнения списка можно использовать коллекции Items или ItemsSource, однако использовать обе одновременно невозможно. Если задано значение для свойства ItemsSource и при этом добавляется элемент в коде XAML, этот элемент игнорируется. Если задано значение для свойства ItemsSource и при этом в коде программы добавляется элемент в коллекцию Items, возникает исключение.

Во многих примерах в этой статье коллекция `Items` заполняется напрямую для упрощения. Однако в реальности элементы часто попадают в список из динамического источника, например списка книг в интернет-базе данных. Для этого используется свойство `ItemsSource`. 

### <a name="add-items-to-a-listview-or-gridview"></a>Добавление элементов в ListView или GridView

Вы можете добавить элементы в коллекцию [Items](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.items) элемента управления ListView или GridView с помощью XAML или кода, обеспечивающего тот же результат. С помощью XAML обычно добавляют небольшое количество неизменяемых элементов, которые легко задать, либо в случае, если элементы создаются на этапе выполнения кода. 

<u>Метод 1. Добавление элементов в коллекцию Items</u>
#### <a name="option-1-add-items-through-xaml"></a>Вариант 1. Добавление элементов с помощью XAML
```xml
<!-- No corresponding C# code is needed for this example. -->

<ListView x:Name="Fruits"> 
   <x:String>Apricot</x:String> 
   <x:String>Banana</x:String> 
   <x:String>Cherry</x:String> 
   <x:String>Orange</x:String> 
   <x:String>Strawberry</x:String> 
</ListView>  
```


#### <a name="option-2-add-items-through-c"></a>Вариант 2. Добавление элементов с помощью C#

##### <a name="c-code"></a>Код C#:
```csharp
// Create a new ListView and add content. 
ListView Fruits = new ListView(); 
Fruits.Items.Add("Apricot"); 
Fruits.Items.Add("Banana"); 
Fruits.Items.Add("Cherry"); 
Fruits.Items.Add("Orange"); 
Fruits.Items.Add("Strawberry");
 
// Add the ListView to a parent container in the visual tree (that you created in the corresponding XAML file).
FruitsPanel.Children.Add(Fruits); 
```

##### <a name="corresponding-xaml-code"></a>Соответствующий код XAML:
```xml
<StackPanel Name="FruitsPanel"></StackPanel>
```
Оба приведенных выше варианта позволяют получить один и тот же элемент управления ListView, как показано ниже.

![Простое представление списка](images/listview-basic-code-example2.png)
<br/>
<u>Метод 2. Добавление элементов с помощью свойства ItemsSource</u>

Обычно ListView или GridView используется для отображения данных из таких источников, как база данных или Интернет. Чтобы заполнить  ListView или GridView элементами из источника данных, задайте для его свойства [ItemsSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) коллекцию элементов данных. Этот метод работает лучше, если элемент управления ListView или GridView будет хранить объекты пользовательского класса, как показано в примерах ниже.

#### <a name="option-1-set-itemssource-in-c"></a>Вариант 1. Задание свойства ItemsSource на C#
В этом коде ItemsSource для представления списка задается непосредственно экземпляру коллекции. 

##### <a name="c-code"></a>Код C#:
```csharp 
// Class defintion should be provided within the namespace being used, outside of any other classes.

this.InitializeComponent();

// Instead of adding hard coded items to an ObservableCollection as shown below, 
//the data could be pulled asynchronously from a database or the internet.
ObservableCollection<Contact> Contacts = new ObservableCollection<Contact>();

// Contact objects are created by providing a first name, last name, and company for the Contact constructor.
// They are then added to the ObservableCollection Contacts.
Contacts.Add(new Contact("John", "Doe", "ABC Printers"));
Contacts.Add(new Contact("Jane", "Doe", "XYZ Refridgerators"));
Contacts.Add(new Contact("Santa", "Claus", "North Pole Toy Factory Inc."));

// Create a new ListView (or GridView) for the UI, add content by setting ItemsSource
ListView ContactsLV = new ListView();
ContactsLV.ItemsSource = Contacts;

// Add the ListView to a parent container in the visual tree (that you created in the corresponding XAML file)
ContactPanel.Children.Add(ContactsLV);
```

##### <a name="xaml-code"></a>Код XAML:
```xml
<StackPanel x:Name="ContactPanel"></StackPanel>
```

#### <a name="option-2-set-itemssource-in-xaml"></a>Вариант 2. Задание свойства ItemsSource в XAML
Свойство ItemsSource можно также привязать к коллекции в XAML. Здесь ItemsSource привязывается к именованному открытому свойству `Contacts`, которое предоставляет частную коллекцию данных Пейдж, `_contacts`.

**XAML**
```xml
<ListView x:Name="ContactsLV" ItemsSource="{x:Bind Contacts}"/>
```

**C#**
```csharp
// Class defintion should be provided within the namespace being used, outside of any other classes.
// These two declarations belong outside of the main page class.
private ObservableCollection<Contact> _contacts = new ObservableCollection<Contact>();

public ObservableCollection<Contact> Contacts
{
    get { return this._contacts; }
}

// This method should be defined within your main page class.
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    base.OnNavigatedTo(e);

    // Instead of hard coded items, the data could be pulled 
    // asynchronously from a database or the internet.
    Contacts.Add(new Contact("John", "Doe", "ABC Printers"));
    Contacts.Add(new Contact("Jane", "Doe", "XYZ Refridgerators"));
    Contacts.Add(new Contact("Santa", "Claus", "North Pole Toy Factory Inc."));
}
```

Оба приведенных выше варианта позволяют получить один и тот же элемент управления ListView, как показано ниже. В ListView отображается только строковое представление каждого элемента, так как мы не указали шаблон данных.

![Простое представление списка с заданным свойством ItemsSource](images/listview-basic-code-example-final.png)

> [!IMPORTANT]
> Если шаблон данных не определен, то объекты пользовательского класса будут отображаться в ListView со своим строковым значением, если для них определен метод [ToString()](https://docs.microsoft.com/uwp/api/windows.foundation.istringable.tostring).

 В следующем разделе подробно описано, как визуально представить элементы простого и пользовательского класса в ListView или GridView.

См.сведения о [привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-quickstart).

> [!NOTE]
> Если необходимо отобразить сгруппированные данные в ListView, то нужно выполнить привязку к [CollectionViewSource](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource). Элемент CollectionViewSource работает в качестве прокси для класса коллекции в XAML, обеспечивая поддержку группировки и валюты. Дополнительные сведения см. в разделе [CollectionViewSource](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource).

## <a name="customizing-the-look-of-items-with-a-datatemplate"></a>Настройка внешнего вида элементов с помощью DataTemplate

Шаблон данных в ListView или GridView определяет, как выглядят элементы или данные. По умолчанию элемент данных отображается в ListView в виде строки, представляющей объект данных, к которому он привязан. Вы можете отображать строковое представление конкретного свойства элемента данных, установив [DisplayMemberPath](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.displaymemberpath) для этого свойства.

Как правило, хочется показать более сложно оформленное представление данных. Чтобы точно определить, как должны отображаться элементы в ListView или GridView, следует создать [DataTemplate](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate). В DataTemplate с помощью XAML определяется макет и внешний вид элементов управления, используемых для отображения отдельного элемента. Элементы управления в макете могут быть привязаны к свойствам объекта данных или иметь статическое содержимое, задаваемое в коде. 

> [!NOTE]
> При использовании [расширения разметки x:Bind](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) в DataTemplate необходимо задать DataType (`x:DataType`) в DataTemplate.

#### <a name="simple-listview-data-template"></a>Простой шаблон данных ListView
В этом примере элемент данных является простой строкой. Шаблон DataTemplate определен в определении ListView, чтобы добавить изображение слева от строки и отобразить строку в сине-зеленом цвете. Это элемент управления ListView, который был создан с помощью метода 1 и варианта 1, описанных выше.

**XAML**
```XML
<!--No corresponding C# code is needed for this example.-->
<ListView x:Name="FruitsList">
                <ListView.ItemTemplate>
                    <DataTemplate x:DataType="x:String">
                        <Grid>
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="47"/>
                                <ColumnDefinition/>
                            </Grid.ColumnDefinitions>
                            <Image Source="Assets/placeholder.png" Width="32" Height="32"
                                HorizontalAlignment="Left" VerticalAlignment="Center"/>
                            <TextBlock Text="{x:Bind}" Foreground="Teal" FontSize="14" 
                                Grid.Column="1" VerticalAlignment="Center"/>
                        </Grid>
                    </DataTemplate>
                </ListView.ItemTemplate>
                <x:String>Apricot</x:String>
                <x:String>Banana</x:String>
                <x:String>Cherry</x:String>
                <x:String>Orange</x:String>
                <x:String>Strawberry</x:String>
            </ListView>

```

Вот как выглядят элементы данных в ListView при отображении с помощью этого шаблона данных.

![Элементы ListView, отображаемые с помощью шаблона данных](images/listview-w-datatemplate1-final.png)

#### <a name="listview-data-template-for-custom-class-objects"></a>Шаблон данных ListView для объектов пользовательского класса
В этом примере элемент данных является объектом Contact. Шаблон DataTemplate определен в определении ListView, чтобы добавить изображение контакта слева от его имени и организации. Этот элемент управления ListView был создан с помощью метода 2 и варианта 2, описанных выше.
```xml
<ListView x:Name="ContactsLV" ItemsSource="{x:Bind Contacts}">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="local:Contact">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="*"/>
                    <RowDefinition Height="*"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto"/>
                    <ColumnDefinition Width="*"/>
                </Grid.ColumnDefinitions>
                <Image Grid.Column="0" Grid.RowSpan="2" Source="Assets/grey-placeholder.png" Width="32"
                    Height="32" HorizontalAlignment="Center" VerticalAlignment="Center"></Image>
                <TextBlock Grid.Column="1" Text="{x:Bind Name}" Margin="12,6,0,0" 
                    Style="{ThemeResource BaseTextBlockStyle}"/>
                <TextBlock  Grid.Column="1" Grid.Row="1" Text="{x:Bind Company}" Margin="12,0,0,6" 
                    Style="{ThemeResource BodyTextBlockStyle}"/>
            </Grid>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Вот как выглядят элементы данных в ListView при отображении с помощью этого шаблона данных.

![Элементы пользовательского класса ListView, отображаемые с помощью шаблона данных](images/listview-customclass-datatemplate-final.png)

Шаблоны данных — это основной способ определения внешнего вида ListView. Они могут значительно влиять на производительность, если список содержит много элементов.  

Шаблон данных можно определить в определении ListView или GridView (как показано выше) или отдельно в разделе Resources. Если DataTemplate определен вне элемента управления ListView или GridView, то для него необходимо указать атрибут [x:Key](https://docs.microsoft.com/windows/uwp/xaml-platform/x-key-attribute) и назначить его свойству [ItemTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate) элемента управления ListView или GridView с помощью этого ключа.

Дополнительные сведения и примеры использования шаблонов данных и контейнеров элементов для определения внешнего вида элементов в списке или сетке см. в разделе [Контейнеры элементов и шаблоны](item-containers-templates.md). 

## <a name="change-the-layout-of-items"></a>Изменение макета элементов

При добавлении элементов в ListView или GridView этот элемент управления автоматически переносит на следующую строку каждый элемент в контейнере, а затем располагает все контейнеры элементов. Расположение этих контейнеров зависит от свойства [ItemsPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemspanel) элемента управления.  
- По умолчанию в **ListView** используется [ItemsStackPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel), создавая вертикальный список, наподобие показанного ниже.

![Простое представление списка](images/listview-simple.png)

- В **GridView** используется [ItemsWrapGrid](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemswrapgrid), при этом элементы добавляются по горизонтали, переносятся на следующую строку и прокручиваются по вертикали следующим образом.

![Простое представление сетки](images/gridview-simple.png)

Можно изменить расположение элементов, скорректировав свойства на панели элементов, или заменить панель по умолчанию другой панелью.

> [!NOTE]
> Будьте внимательны, чтобы не отключить виртуализацию при изменении ItemsPanel. **ItemsStackPanel** и **ItemsWrapGrid** поддерживают виртуализацию, поэтому ими можно пользоваться безопасно. При использовании любой другой панели может отключиться виртуализация, что снизит производительность представления списка. Дополнительные сведения см. в статьях о представлении списка в разделе [Производительность](https://docs.microsoft.com/windows/uwp/debug-test-perf/performance-and-xaml-ui). 

Этот пример показывает, как с помощью **ListView** расположить контейнеры элементов горизонтальным списком, изменив свойство [Orientation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel.orientation) коллекции **ItemsStackPanel**.
Поскольку по умолчанию представление списка прокручивается по вертикали,потребуется также скорректировать определенные свойства внутреннего элемента [ScrollViewer](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer) представления списка, чтобы настроить горизонтальную прокрутку.
- [ScrollViewer.HorizontalScrollMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollmode), чтобы задать значение **Включено** или **Авто**
- [ScrollViewer.HorizontalScrollBarVisibility](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollbarvisibility), чтобы задать значение **Авто** 
- [ScrollViewer.VerticalScrollMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollmode), чтобы задать значение **Отключено** 
- [ScrollViewer.VerticalScrollBarVisibility](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollbarvisibility), чтобы задать значение **Скрыто** 

> [!IMPORTANT]
> Эти примеры показаны с неограниченной шириной представления списка, поэтому горизонтальные полосы прокрутки не отображаются. Если запустить этот код, можно настроить `Width="180"` для отображения полос прокрутки.

**XAML**
```xml
<ListView Height="60" 
          ScrollViewer.HorizontalScrollMode="Enabled" 
          ScrollViewer.HorizontalScrollBarVisibility="Auto"
          ScrollViewer.VerticalScrollMode="Disabled"
          ScrollViewer.VerticalScrollBarVisibility="Hidden">
    <ListView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsStackPanel Orientation="Horizontal"/>
        </ItemsPanelTemplate>
    </ListView.ItemsPanel>
    <x:String>Apricot</x:String>
    <x:String>Banana</x:String>
    <x:String>Cherry</x:String>
    <x:String>Orange</x:String>
    <x:String>Strawberry</x:String>
</ListView>
```

Созданный список выглядит следующим образом.

![Горизонтальное представление списка](images/listview-horizontal2-final.png)

 В следующем примере **ListView** размещает элементы в вертикальном списке с переносом, используя элемент **ItemsWrapGrid** вместо **ItemsStackPanel**. 
 
> [!IMPORTANT]
> Высоту представления списка необходимо ограничить, чтобы элемент управления принудительно переносил контейнеры на следующую строку.

**XAML**
```xml
<ListView Height="100"
          ScrollViewer.HorizontalScrollMode="Enabled" 
          ScrollViewer.HorizontalScrollBarVisibility="Auto"
          ScrollViewer.VerticalScrollMode="Disabled"
          ScrollViewer.VerticalScrollBarVisibility="Hidden">
    <ListView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsWrapGrid/>
        </ItemsPanelTemplate>
    </ListView.ItemsPanel>
    <x:String>Apricot</x:String>
    <x:String>Banana</x:String>
    <x:String>Cherry</x:String>
    <x:String>Orange</x:String>
    <x:String>Strawberry</x:String>
</ListView>
```

Созданный список выглядит следующим образом.

![Представление списка с макетом сетки](images/listview-itemswrapgrid2-final.png)

Если в представлении списка показаны группированные данные, элемент ItemsPanel определяет расположение групп элементов, а не отдельных элементов. Например, если показанная ранее горизонтальная коллекция ItemsStackPanel используется для отображения группированных данных, группы организуются по горизонтали, но элементы в каждой из них по-прежнему упорядочены по вертикали, как показано здесь.

![Группированное горизонтальное представление списка](images/listview-horizontal-groups.png)

## <a name="item-selection-and-interaction"></a>Выбор элементов и взаимодействие

Можно выбрать и сделать доступными различные способы взаимодействия пользователя с представлением списка. По умолчанию пользователь может выбрать один элемент. Можно изменить свойство [SelectionMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selectionmode), чтобы разрешить выбор нескольких элементов или отключить их выбор. Можно задать свойство [IsItemClickEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.isitemclickenabled) так, чтобы пользователь нажатием элемента вызывал действие (наподобие кнопки), а не выбирал элемент.

> **Примечание.** &nbsp;&nbsp;Элементы ListView и GridView используют перечисление [ListViewSelectionMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewselectionmode) для свойств SelectionMode. IsItemClickEnabled по умолчанию имеет значение **False**, поэтому его нужно настроить, чтобы включить режим щелчка.

В таблице показаны способы взаимодействия пользователя с представлением списка и способы реагирования на это взаимодействие.

Чтобы включить взаимодействие, выполните следующие действия. | Используйте следующие параметры: | Обработайте следующее событие: | Используйте это свойство, чтобы получить выбранный элемент:
----------------------------|---------------------|--------------------|--------------------------------------------
Без взаимодействия | [SelectionMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selectionmode) = **None**, [IsItemClickEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.isitemclickenabled) = **False** | Н/Д | Н/Д 
Выбор одного элемента | SelectionMode = **Single**, IsItemClickEnabled = **False** | [SelectionChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) | [SelectedItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selecteditem), [SelectedIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedindex)  
Выбор нескольких элементов | SelectionMode = **Multiple**, IsItemClickEnabled = **False** | [SelectionChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) | [SelectedItems](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selecteditems)  
Расширенный выбор | SelectionMode = **Extended**, IsItemClickEnabled = **False** | [SelectionChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) | [SelectedItems](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selecteditems)  
Щелкните | SelectionMode = **None**, IsItemClickEnabled = **True** | [ItemClick](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.itemclick) | Н/Д 

> **Примечание.** &nbsp;&nbsp;Начиная с Windows 10, можно сделать так, чтобы свойство IsItemClickEnabled вызывало событие ItemClick, если для SelectionMode выбрано значение Single, Multiple или Extended. В этом случае сначала вызывается событие ItemClick, а затем — SelectionChanged. В некоторых случаях, например при переходе на другую страницу в обработчике событий ItemClick, событие SelectionChanged не вызывается, элемент не выделяется.

Можно задать эти свойства в XAML или в коде. как показано здесь.

**XAML**
```xaml
<ListView x:Name="myListView" SelectionMode="Multiple"/>

<GridView x:Name="myGridView" SelectionMode="None" IsItemClickEnabled="True"/> 
```

**C#**
```csharp
myListView.SelectionMode = ListViewSelectionMode.Multiple; 

myGridView.SelectionMode = ListViewSelectionMode.None;
myGridView.IsItemClickEnabled = true;
```

### <a name="read-only"></a>Только для чтения

Чтобы отключить выбор элементов, можно задать для свойства SelectionMode значение **ListViewSelectionMode.None**. Это переведет режим управления в режим "только чтение", его можно будет использовать для отображения данных, но не для взаимодействия с ними. Сам элемент управления не отключается, отключается только выделение элементов.

### <a name="single-selection"></a>Выбор одного элемента

В этой таблице описаны взаимодействия с использованием клавиатуры, мыши и сенсорных элементов, если SectionMode имеет значение **Single**.

Клавиша-модификатор | Тип взаимодействия
-------------|------------
Нет | <li>Пользователь может выбрать один элемент с помощью клавиши пробела, щелчка мыши или касания.</li>
Ctrl | <li>Пользователь может отменить выбор одного элемента с помощью клавиши пробела, щелчка мыши или касания.</li><li>С помощью клавиш со стрелками пользователь может перемещать фокус независимо от выделения.</li>

Если SelectionMode имеет значение **Single**, выбранный элемент данных можно получить из свойства [SelectedItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selecteditem). Можно получить индекс в коллекции выбранного элемента с помощью свойства [SelectedIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedindex). Если элементы не выбраны, SelectedItem имеет значение **null**, и SelectedIndex — -1. 
 
Если попытаться задать элемент вне коллекции **Items**, например **SelectedItem**, операция будет проигнорирована, а SelectedItem будет равен **null**. Однако если попытаться задать для **SelectedIndex** индекс вне диапазона **Items** в списке, произойдет исключение **System.ArgumentException**. 

### <a name="multiple-selection"></a>Выбор нескольких элементов

В этой таблице описаны взаимодействия с использованием клавиатуры, мыши и сенсорных элементов, если SectionMode имеет значение **Multiple**.

Клавиша-модификатор | Тип взаимодействия
-------------|------------
Нет | <li>Пользователь может выбрать несколько элементов с помощью клавиши пробела, щелчка мыши или касания сенсорной панели, чтобы выбрать элемент в фокусе или отменить его выбор.</li><li>С помощью клавиш со стрелками пользователь может перемещать фокус независимо от выделения.</li>
Shift | <li>Пользователь может выбрать несколько смежных элементов, щелкнув первый элемент выделения (или коснувшись его), а затем — последний.</li><li>С помощью клавиш со стрелками пользователь может создать непрерывное выделение, начиная с выбранного с нажатой клавишей Shift элемента.</li>

### <a name="extended-selection"></a>Расширенный выбор

В этой таблице описаны взаимодействия с использованием клавиатуры, мыши и сенсорных элементов, если SectionMode имеет значение **Extended**.

Клавиша-модификатор | Тип взаимодействия
-------------|------------
Нет | <li>Поведение схоже с выделением **Single**.</li>
Ctrl | <li>Пользователь может выбрать несколько элементов с помощью клавиши пробела, щелчка мыши или касания сенсорной панели, чтобы выбрать элемент в фокусе или отменить его выбор.</li><li>С помощью клавиш со стрелками пользователь может перемещать фокус независимо от выделения.</li>
Shift | <li>Пользователь может выбрать несколько смежных элементов, щелкнув первый элемент выделения (или коснувшись его), а затем — последний.</li><li>С помощью клавиш со стрелками пользователь может создать непрерывное выделение, начиная с выбранного с нажатой клавишей Shift элемента.</li>

Если SelectionMode имеет значение **Multiple** или **Extended**, выбранные элементы данных можно получить из свойства [SelectedItems](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selecteditems). 

Свойства **SelectedIndex**, **SelectedItem** и **SelectedItems** синхронизируются. Например, если для SelectedIndex задано значение -1, SelectedItem равно **null**, а коллекция SelectedItems пуста; если задать для SelectedItem значение **null**, SelectedIndex равно -1, а SelectedItems пуст.

В режиме с множественным выбором **SelectedItem** содержит выбранный первым элемент, а **Selectedindex** — индекс выбранного первым элемента. 

### <a name="respond-to-selection-changes"></a>Реагирование на изменения выбора

Для того, чтобы реагировать на изменения в выборе элементов представления списка, используйте событие [SelectionChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged). В коде обработчика этого события вы можете получить список выбранных элементов, используя свойство [SelectionChangedEventArgs.AddedItems](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.selectionchangedeventargs.addeditems). Можно получить любые элементы, выбор которых был отменен, в свойстве [SelectionChangedEventArgs.RemovedItems](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.selectionchangedeventargs.removeditems). Коллекции AddedItems и RemovedItems содержат не более 1 элемента за исключением случаев, когда пользователь выбирает диапазон элементов удержанием клавиши Shift.

В этом примере показана обработка события **SelectionChanged** и доступ к разным коллекциям элементов.

**XAML**
```xml
<StackPanel HorizontalAlignment="Right">
    <ListView x:Name="listView1" SelectionMode="Multiple" 
              SelectionChanged="ListView1_SelectionChanged">
        <x:String>Apricot</x:String>
        <x:String>Banana</x:String>
        <x:String>Cherry</x:String>
        <x:String>Orange</x:String>
        <x:String>Strawberry</x:String>
    </ListView>
    <TextBlock x:Name="selectedItem"/>
    <TextBlock x:Name="selectedIndex"/>
    <TextBlock x:Name="selectedItemCount"/>
    <TextBlock x:Name="addedItems"/>
    <TextBlock x:Name="removedItems"/>
</StackPanel> 
```

**C#**
```csharp
private void ListView1_SelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (listView1.SelectedItem != null)
    {
        selectedItem.Text = 
            "Selected item: " + listView1.SelectedItem.ToString();
    }
    else
    {
        selectedItem.Text = 
            "Selected item: null";
    }
    selectedIndex.Text = 
        "Selected index: " + listView1.SelectedIndex.ToString();
    selectedItemCount.Text = 
        "Items selected: " + listView1.SelectedItems.Count.ToString();
    addedItems.Text = 
        "Added: " + e.AddedItems.Count.ToString();
    removedItems.Text = 
        "Removed: " + e.RemovedItems.Count.ToString();
}
```

### <a name="click-mode"></a>Режим щелчка

Кроме того, вы можете изменить представление списка, чтобы пользователь нажимал элементы (например, кнопки), а не выбирал их. Этот вариант подходит, например если ваше приложение переходит на новую страницу при нажатии пользователем элемента из списка или сетки. Включить такое поведение:
- Задайте для **SelectionMode** значение **None**.
- Задайте для **IsItemClickEnabled** значение **true**.
- Обработайте событие **ItemClick**, чтобы что-то происходило, когда пользователь щелкает элемент.

Вот пример представления списка с нажимаемыми элементами. Код в обработчике события ItemClick переходит на новую страницу.

**XAML**
```xml
<ListView SelectionMode="None"
          IsItemClickEnabled="True" 
          ItemClick="ListView1_ItemClick">
    <x:String>Page 1</x:String>
    <x:String>Page 2</x:String>
    <x:String>Page 3</x:String>
    <x:String>Page 4</x:String>
    <x:String>Page 5</x:String>
</ListView>
```

**C#**
```csharp
private void ListView1_ItemClick(object sender, ItemClickEventArgs e)
{
    switch (e.ClickedItem.ToString())
    {
        case "Page 1":
            this.Frame.Navigate(typeof(Page1));
            break;

        case "Page 2":
            this.Frame.Navigate(typeof(Page2));
            break;

        case "Page 3":
            this.Frame.Navigate(typeof(Page3));
            break;

        case "Page 4":
            this.Frame.Navigate(typeof(Page4));
            break;

        case "Page 5":
            this.Frame.Navigate(typeof(Page5));
            break;

        default:
            break;
    }
}
```

### <a name="select-a-range-of-items-programmatically"></a>Программный выбор диапазона элементов

Иногда необходимо наладить программное управление выбором элементов в представлении списка. Например, возможно, вы используете кнопку **Выбрать все**, чтобы пользователь мог выбрать все элементы в списке. В этом случае добавлять и удалять элементы из коллекции SelectedItems по одному не очень целесообразно. Каждое изменение элемента вызывает событие SelectionChanged, а если работать с элементами напрямую, а не со значениями индекса, элемент девиртуализуется.

Методы [SelectAll](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selectall), [SelectRange](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selectrange) и [DeselectRange](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.deselectrange) предоставляют более эффективный способ изменения выделения, чем использование свойства SelectedItems. Эти методы осуществляют выделение или отмену выделения с помощью диапазонов индексов элементов. Виртуализованные элементы остаются виртуализованными, поскольку используется только индекс. Все элементы в определенном диапазоне выделены (или их выделение снято) вне зависимости от начального состояния выбора. Событие SelectionChanged происходит однократно для каждого вызова этих методов.

> [!IMPORTANT]
> Необходимо вызывать эти методы, только когда для свойства SelectionMode задано значение Multiple или Extended. Если вызвать SelectRange, когда SelectionMode имеет значение Single или None, создается исключение.

При выборе элементов с использованием диапазонов индексов используйте свойство [SelectedRanges](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selectedranges), чтобы получить все выбранные диапазоны в списке.

Если ItemsSource реализует [IItemsRangeInfo](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.iitemsrangeinfo) и вы используете эти методы, чтобы изменить выделение, свойства **AddedItems** и **RemovedItems** не задаются в элементе SelectionChangedEventArgs. Настройка этих свойств требует отмены виртуализации объекта элемента. Для получения элементов используйте свойство **SelectedRanges**.

Для выделения всех элементов коллекции вызовите метод SelectAll. Однако метод для отмены выделения всех элементов отсутствует. Можно отменить выбор всех элементов, вызвав DeselectRange и передав [ItemIndexRange](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.itemindexrange) со свойством [FirstIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.itemindexrange.firstindex), значение которого равно 0, и значением [Length](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.itemindexrange.length), равным числу элементов в коллекции. Это показано в приведенном ниже примере, включая возможность выбора всех элементов.

**XAML**
```xml
<StackPanel Width="160">
    <Button Content="Select all" Click="SelectAllButton_Click"/>
    <Button Content="Deselect all" Click="DeselectAllButton_Click"/>
    <ListView x:Name="listView1" SelectionMode="Multiple">
        <x:String>Apricot</x:String>
        <x:String>Banana</x:String>
        <x:String>Cherry</x:String>
        <x:String>Orange</x:String>
        <x:String>Strawberry</x:String>
    </ListView>
</StackPanel>
```

**C#**
```csharp
private void SelectAllButton_Click(object sender, RoutedEventArgs e)
{
    if (listView1.SelectionMode == ListViewSelectionMode.Multiple ||
        listView1.SelectionMode == ListViewSelectionMode.Extended)
    {
        listView1.SelectAll();
    }
}

private void DeselectAllButton_Click(object sender, RoutedEventArgs e)
{
    if (listView1.SelectionMode == ListViewSelectionMode.Multiple ||
        listView1.SelectionMode == ListViewSelectionMode.Extended)
    {
        listView1.DeselectRange(new ItemIndexRange(0, (uint)listView1.Items.Count));
    }
}
```

Сведения о порядке изменения внешнего вида выбранных элементов см. в разделе [Контейнеры и шаблоны элементов](item-containers-templates.md).

### <a name="drag-and-drop"></a>Перетаскивание

Элементы управления ListView и GridView поддерживают перетаскивание элементов внутри данного элемента управления, а также между данным и другими элементами управления ListView и GridView. Подробнее о реализации шаблона перетаскивания см. в разделе [Перетаскивание](../input/drag-and-drop.md).

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример ListView и GridView в XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlListView) — демонстрация элементов управления ListView и GridView.
- [Пример перетаскивания в XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlDragAndDrop) — демонстрация перетаскивания с помощью элемента управления ListView.
- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи

- [Списки](lists.md)
- [Контейнеры и шаблоны элементов](item-containers-templates.md)
- [Перетаскивание](../input/drag-and-drop.md)
