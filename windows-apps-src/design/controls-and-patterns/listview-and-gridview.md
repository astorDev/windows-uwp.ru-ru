---
author: muhsinking
Description: Use ListView and GridView controls to display and manipulate sets of data, such as a gallery of images or a set of email messages.
title: Представления списка и сетки
label: List view and grid view
template: detail.hbs
ms.author: jimwalk
ms.date: 05/20/2017
ms.topic: article
keywords: Windows10, UWP
ms.assetid: f8532ba0-5510-4686-9fcf-87fd7c643e7b
pm-contact: predavid
design-contact: kimsea
dev-contact: ranjeshj
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 1ee00a9af23be945ad27ab4b39eec127ec397894
ms.sourcegitcommit: e38b334edb82bf2b1474ba686990f4299b8f59c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6835821"
---
# <a name="list-view-and-grid-view"></a>Представления списка и сетки

Большая часть приложений отображает наборы данных, такие как коллекции изображений или набор сообщений электронной почты, и управляет ими. Платформа пользовательского интерфейса XAML предоставляет элементы управления ListView и GridView, упрощающие отображение данных и управление ими в приложении.  

> **Важные API-интерфейсы**: [класс ListView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listview.aspx), [класс GridView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.gridview.aspx), [свойство ItemsSource](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemssource.aspx), [свойство Items](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.items.aspx)

Элементы управления ListView и GridView являются производными от класса ListViewBase, поэтому они имеют аналогичные функции, но отображают данные по-другому. В этой статье вся информация об элементе управления ListView актуальна для обоих элементов управления (ListView и GridView), если не указано иное. Когда упоминаются классы ListView или ListViewItem, нужно помнить, что префикс "List" может быть заменен префиксом "Grid" для соответствующего эквивалента сетки (GridView или GridViewItem). 

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

ListView отображает данные в одном столбце и упорядочивает их по вертикали. Он часто применяется для отображения упорядоченного списка элементов, например списка сообщений электронной почты или результатов поиска. 

![Представление списка со сгруппированными данными](images/simple-list-view-phone.png)

Элемент управления GridView представляет коллекцию элементов, которые расположены в строках и столбцах, с вертикальной прокруткой. Данные упорядочиваются по горизонтали до тех пор, пока не заполнят столбцы, после чего начинается заполнение следующей строки. Он часто используется для отображения расширенного визуального представления элемента, которому требуется больше места. Примером может служить фотоальбом. 

![Пример библиотеки содержимого](images/controls_list_contentlibrary.png)

Более подробное сравнение и инструкции по выбору элемента управления см. в разделе [Списки](lists.md).

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/ListView">ListView</a> или <a href="xamlcontrolsgallery:/item/GridView">GridView</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-a-list-view"></a>Создание представления списка

Представление списка представляет собой [ItemsControl](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.aspx), поэтому может содержать коллекцию элементов любого типа. Прежде чем отобразить что-то на экране, необходимо включить элементы в коллекцию [Items](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.items.aspx). Для добавления элементов в представление заполните коллекцию [Items](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.items.aspx) напрямую или свяжите свойство [ItemsSource](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemssource.aspx) с источником данных. 

**Важно!**&nbsp;&nbsp;Для заполнения списка можно использовать коллекции Items или ItemsSource, однако использовать обе одновременно невозможно. Если задано значение для свойства ItemsSource и при этом добавляется элемент в коде XAML, этот элемент игнорируется. Если задано значение для свойства ItemsSource и при этом в коде программы добавляется элемент в коллекцию Items, возникает исключение.

> **Примечание.**&nbsp;&nbsp;Во многих примерах в этой статье коллекция **Items** заполняется напрямую для упрощения. Однако в реальности элементы часто попадают в список из динамического источника, например списка книг в интернет-базе данных. Для этого используется свойство **ItemsSource**. 

### <a name="add-items-to-the-items-collection"></a>Добавление элементов в коллекцию Items

Добавить элементы в коллекцию [Items](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.items.aspx) можно с помощью XAML или кода программы. Обычно так добавляют небольшое количество неизменяемых элементов, которые легко задать с помощью XAML, либо в случае, если элементы создаются на этапе выполнения кода. 

Здесь приведено представление списка с внутренне определяемыми элементами в XAML. Если элементы определяются в коде XAML, они также автоматически добавляются в коллекцию Items.

**XAML**
```xaml
<ListView x:Name="listView1"> 
   <x:String>Item 1</x:String> 
   <x:String>Item 2</x:String> 
   <x:String>Item 3</x:String> 
   <x:String>Item 4</x:String> 
   <x:String>Item 5</x:String> 
</ListView>  
```

Вот представление списка, созданное в коде. Полученный список совпадает с созданным ранее в XAML.

**C#**
```csharp
// Create a new ListView and add content. 
ListView listView1 = new ListView(); 
listView1.Items.Add("Item 1"); 
listView1.Items.Add("Item 2"); 
listView1.Items.Add("Item 3"); 
listView1.Items.Add("Item 4"); 
listView1.Items.Add("Item 5");
 
// Add the ListView to a parent container in the visual tree. 
stackPanel1.Children.Add(listView1); 
```

ListView выглядит следующим образом.

![Простое представление списка](images/listview-simple.png)

### <a name="set-the-items-source"></a>Установка источника элементов

Обычно представление списка используется для отображения данных из таких источников, как база данных или Интернет. Чтобы заполнить представление списка из источника данных, задайте его свойству [ItemsSource](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemssource.aspx) коллекцию элементов данных.

В этом коде ItemsSource для представления списка задается непосредственно экземпляру коллекции.

**C#**
```csharp 
// Instead of hard coded items, the data could be pulled 
// asynchronously from a database or the internet.
ObservableCollection<string> listItems = new ObservableCollection<string>();
listItems.Add("Item 1");
listItems.Add("Item 2");
listItems.Add("Item 3");
listItems.Add("Item 4");
listItems.Add("Item 5");

// Create a new list view, add content, 
ListView itemListView = new ListView();
itemListView.ItemsSource = listItems;

// Add the list view to a parent container in the visual tree.
stackPanel1.Children.Add(itemListView);
```

Свойство ItemsSource можно также связать с коллекцией в среде XAML. Подробнее о концепциях привязки данных см. в разделе [Общие сведения о привязке данных](https://msdn.microsoft.com/windows/uwp/data-binding/data-binding-quickstart).

Здесь ItemsSource привязывается к именованному открытому свойству `Items`, которое предоставляет коллекцию конфиденциальных данных страницы.

**XAML**
```xaml
<ListView x:Name="itemListView" ItemsSource="{x:Bind Items}"/>
```

**C#**
```csharp
private ObservableCollection<string> _items = new ObservableCollection<string>();

public ObservableCollection<string> Items
{
    get { return this._items; }
}

protected override void OnNavigatedTo(NavigationEventArgs e)
{
    base.OnNavigatedTo(e);

    // Instead of hard coded items, the data could be pulled 
    // asynchronously from a database or the internet.
    Items.Add("Item 1");
    Items.Add("Item 2");
    Items.Add("Item 3");
    Items.Add("Item 4");
    Items.Add("Item 5");
}
```

Если необходимо отобразить группированные данные в представлении списка, необходимо выполнить привязку к [CollectionViewSource](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.data.collectionviewsource.aspx). Элемент CollectionViewSource работает в качестве прокси для класса коллекции в XAML, обеспечивая поддержку группировки и валюты. Дополнительные сведения см. в разделе [CollectionViewSource](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.data.collectionviewsource.aspx).

## <a name="data-template"></a>Шаблон данных

Шаблон данных элемента определяет способ визуализации данных. По умолчанию элемент данных отображается в представлении списка в виде строки, представляющей объект данных, к которому он привязан. Вы можете отображать строковое представление конкретного свойства элемента данных, установив [DisplayMemberPath](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.displaymemberpath.aspx) для этого свойства.

Как правило, хочется показать более сложно оформленное представление данных. Чтобы точно определить, как должны отображаться элементы в представлении списка, вы создаете [DataTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.datatemplate.aspx). В DataTemplate с помощью языка XAML задается макет и внешний вид элементов управления, используемых для отображения отдельного элемента. Элементы управления в макете могут быть привязаны к свойствам объекта данных или иметь статическое содержимое, задаваемое внутри кода. Вы назначаете DataTemplate свойству [ItemTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx) элемента управления списком.

В этом примере элемент данных является простой строкой. DataTemplate используется для добавления изображения слева от строки и отображения строки сине-зеленым.

> **Примечание.**&nbsp;&nbsp;При использовании [расширения разметки x:Bind](https://msdn.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) в DataTemplate необходимо задать DataType (`x:DataType`) в DataTemplate.

**XAML**
```XAML
<ListView x:Name="listView1">
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="x:String">
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="47"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Image Source="Assets/placeholder.png" Width="32" Height="32" 
                       HorizontalAlignment="Left"/>
                <TextBlock Text="{x:Bind}" Foreground="Teal" 
                           FontSize="15" Grid.Column="1"/>
            </Grid> 
        </DataTemplate>
    </ListView.ItemTemplate>
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
    <x:String>Item 3</x:String>
    <x:String>Item 4</x:String>
    <x:String>Item 5</x:String>
</ListView>
```

Вот как выглядят элементы данных при отображении с помощью этого шаблона данных.

![Элементы представления списка, отображаемые с помощью шаблона данных](images/listview-itemstemplate.png)

Шаблоны данных— это основной способ определения внешнего вида представления списка. Они могут значительно влиять на производительность, если список содержит много элементов. В этой статье используются простые строковые данные для большинства примеров, а шаблон данных не задается. Дополнительные сведения и примеры использования шаблонов данных и контейнеров элементов для определения внешнего вида элементов в списке или сетке см. в разделе [Контейнеры элементов и шаблоны](item-containers-templates.md). 

## <a name="change-the-layout-of-items"></a>Изменение макета элементов

При добавлении элементов в представление списка или представление сетки элемент управления автоматически переносит на следующую строку каждый элемент в контейнере, а затем располагает все контейнеры элементов. Расположение этих контейнеров зависит от свойства [ItemsPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemspanel.aspx) элемента управления.  
- По умолчанию в **ListView** используется [ItemsStackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemsstackpanel.aspx), создавая вертикальный список, наподобие показанного ниже.

![Простое представление списка](images/listview-simple.png)

- В **GridView** используется [ItemsWrapGrid](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemswrapgrid.aspx), при этом элементы добавляются по горизонтали, переносятся на следующую строку и прокручиваются по вертикали следующим образом.

![Простое представление сетки](images/gridview-simple.png)

Можно изменить расположение элементов, скорректировав свойства на панели элементов, или заменить панель по умолчанию другой панелью.

> Примечание.&nbsp;&nbsp;Будьте внимательны, чтобы не отключить виртуализацию при изменении ItemsPanel. **ItemsStackPanel** и **ItemsWrapGrid** поддерживают виртуализацию, поэтому ими можно пользоваться безопасно. При использовании любой другой панели может отключиться виртуализация, что снизит производительность представления списка. Дополнительные сведения см. в статьях о представлении списка в разделе [Производительность](https://msdn.microsoft.com/windows/uwp/debug-test-perf/performance-and-xaml-ui). 

Этот пример показывает, как с помощью **ListView** расположить контейнеры элементов горизонтальным списком, изменив свойство [Orientation](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemsstackpanel.orientation.aspx) коллекции **ItemsStackPanel**.
Поскольку по умолчанию представление списка прокручивается по вертикали,потребуется также скорректировать определенные свойства внутреннего элемента [ScrollViewer](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.aspx) представления списка, чтобы настроить горизонтальную прокрутку.
- [ScrollViewer.HorizontalScrollMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.horizontalscrollmode.aspx), чтобы задать значение **Включено** или **Авто**
- [ScrollViewer.HorizontalScrollBarVisibility](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.horizontalscrollbarvisibility.aspx), чтобы задать значение **Авто** 
- [ScrollViewer.VerticalScrollMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.verticalscrollmode.aspx), чтобы задать значение **Отключено** 
- [ScrollViewer.VerticalScrollBarVisibility](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.scrollviewer.verticalscrollbarvisibility.aspx), чтобы задать значение **Скрыто** 

> **Примечание.**&nbsp;&nbsp;Эти примеры показаны с неограниченной шириной представления списка, поэтому горизонтальные полосы прокрутки не показаны. Если запустить этот код, можно настроить `Width="180"` для отображения полос прокрутки.

**XAML**
```xaml
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
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
    <x:String>Item 3</x:String>
    <x:String>Item 4</x:String>
    <x:String>Item 5</x:String>
</ListView>
```

Созданный список выглядит следующим образом.

![Горизонтальное представление списка](images/listview-horizontal.png)

 В следующем примере **ListView** размещает элементы в вертикальном списке с переносом, используя элемент **ItemsWrapGrid** вместо **ItemsStackPanel**. 
 
> **Примечание.**&nbsp;&nbsp;Высоту представления списка необходимо ограничить, чтобы элемент управления принудительно переносил контейнеры на следующую строку.

**XAML**
```xaml
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
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
    <x:String>Item 3</x:String>
    <x:String>Item 4</x:String>
    <x:String>Item 5</x:String>
</ListView>
```

Созданный список выглядит следующим образом.

![Представление списка с макетом сетки](images/listview-itemswrapgrid.png)

Если в представлении списка показаны группированные данные, элемент ItemsPanel определяет расположение групп элементов, а не отдельных элементов. Например, если показанная ранее горизонтальная коллекция ItemsStackPanel используется для отображения группированных данных, группы организуются по горизонтали, но элементы в каждой из них по-прежнему упорядочены по вертикали, как показано здесь.

![Группированное горизонтальное представление списка](images/listview-horizontal-groups.png)

## <a name="item-selection-and-interaction"></a>Выбор элементов и взаимодействие

Можно выбрать и сделать доступными различные способы взаимодействия пользователя с представлением списка. По умолчанию пользователь может выбрать один элемент. Можно изменить свойство [SelectionMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.selectionmode.aspx), чтобы разрешить выбор нескольких элементов или отключить их выбор. Можно задать свойство [IsItemClickEnabled](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.isitemclickenabled.aspx) так, чтобы пользователь нажатием элемента вызывал действие (наподобие кнопки), а не выбирал элемент.

> **Примечание**.&nbsp;&nbsp;Элементы ListView и GridView используют перечисление [ListViewSelectionMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewselectionmode.aspx) для свойств SelectionMode. IsItemClickEnabled по умолчанию имеет значение **False**, поэтому его нужно настроить, чтобы включить режим щелчка.

В таблице показаны способы взаимодействия пользователя с представлением списка и способы реагирования на это взаимодействие.

Чтобы включить взаимодействие, выполните следующие действия. | Используйте следующие параметры: | Обработайте следующее событие: | Используйте это свойство, чтобы получить выбранный элемент:
----------------------------|---------------------|--------------------|--------------------------------------------
Без взаимодействия | [SelectionMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.selectionmode.aspx) = **None**, [IsItemClickEnabled](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.isitemclickenabled.aspx) = **False** | Н/д | Н/д 
Выбор одного элемента | SelectionMode = **Single**, IsItemClickEnabled = **False** | [SelectionChanged](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.selector.selectionchanged.aspx) | [SelectedItem](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.selector.selecteditem.aspx), [SelectedIndex](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.selector.selectedindex.aspx)  
Выбор нескольких элементов | SelectionMode = **Multiple**, IsItemClickEnabled = **False** | [SelectionChanged](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.selector.selectionchanged.aspx) | [SelectedItems](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.selecteditems.aspx)  
Расширенный выбор | SelectionMode = **Extended**, IsItemClickEnabled = **False** | [SelectionChanged](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.selector.selectionchanged.aspx) | [SelectedItems](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.selecteditems.aspx)  
Щелчок | SelectionMode = **None**, IsItemClickEnabled = **True** | [ItemClick](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.itemclick.aspx) | Н/д 

> **Примечание.**&nbsp;&nbsp;Начиная с Windows 10, можно сделать так, чтобы свойство IsItemClickEnabled вызывало событие ItemClick, если для SelectionMode выбрано значение Single, Multiple или Extended. В этом случае сначала вызывается событие ItemClick, а затем — SelectionChanged. В некоторых случаях, например при переходе на другую страницу в обработчике событий ItemClick, событие SelectionChanged не вызывается, элемент не выделяется.

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

### <a name="read-only"></a>Только чтение

Чтобы отключить выбор элементов, можно задать для свойства SelectionMode значение **ListViewSelectionMode.None**. Это переведет режим управления в режим "только чтение", его можно будет использовать для отображения данных, но не для взаимодействия с ними. Сам элемент управления не отключается, отключается только выделение элементов.

### <a name="single-selection"></a>Выбор одного элемента

В этой таблице описаны взаимодействия с использованием клавиатуры, мыши и сенсорных элементов, если SectionMode имеет значение **Single**.

Клавиша-модификатор | Тип взаимодействия
-------------|------------
Нет | <li>Пользователь может выбрать один элемент с помощью клавиши пробела, щелчка мыши или касания.</li>
Ctrl | <li>Пользователь может отменить выбор одного элемента с помощью клавиши пробела, щелчка мыши или касания.</li><li>С помощью клавиш со стрелками пользователь может перемещать фокус независимо от выделения.</li>

Если SelectionMode имеет значение **Single**, выбранный элемент данных можно получить из свойства [SelectedItem](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.selector.selecteditem.aspx). Можно получить индекс в коллекции выбранного элемента с помощью свойства [SelectedIndex](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.selector.selectedindex.aspx). Если элементы не выбраны, SelectedItem имеет значение **null**, и SelectedIndex— -1. 
 
Если попытаться задать элемент вне коллекции **Items**, например **SelectedItem**, операция будет проигнорирована, а SelectedItem будет равен **null**. Однако если попытаться задать для **SelectedIndex** индекс вне диапазона **Items** в списке, произойдет исключение **System.ArgumentException**. 

### <a name="multiple-selection"></a>Выбор нескольких элементов

В этой таблице описаны взаимодействия с использованием клавиатуры, мыши и сенсорных элементов, если SectionMode имеет значение **Multiple**.

Клавиша-модификатор | Тип взаимодействия
-------------|------------
Нет | <li>Пользователь может выбрать несколько элементов с помощью клавиши пробела, щелчка мыши или касания сенсорной панели, чтобы выбрать элемент в фокусе или отменить его выбор.</li><li>С помощью клавиш со стрелками пользователь может перемещать фокус независимо от выделения.</li>
Shift | <li>Пользователь может выбрать несколько смежных элементов, щелкнув первый элемент выделения (или коснувшись его), а затем— последний.</li><li>С помощью клавиш со стрелками пользователь может создать непрерывное выделение, начиная с выбранного с нажатой клавишей Shift элемента.</li>

### <a name="extended-selection"></a>Расширенный выбор

В этой таблице описаны взаимодействия с использованием клавиатуры, мыши и сенсорных элементов, если SectionMode имеет значение **Extended**.

Клавиша-модификатор | Тип взаимодействия
-------------|------------
Нет | <li>Поведение схоже с выделением **Single**.</li>
Ctrl | <li>Пользователь может выбрать несколько элементов с помощью клавиши пробела, щелчка мыши или касания сенсорной панели, чтобы выбрать элемент в фокусе или отменить его выбор.</li><li>С помощью клавиш со стрелками пользователь может перемещать фокус независимо от выделения.</li>
Shift | <li>Пользователь может выбрать несколько смежных элементов, щелкнув первый элемент выделения (или коснувшись его), а затем— последний.</li><li>С помощью клавиш со стрелками пользователь может создать непрерывное выделение, начиная с выбранного с нажатой клавишей Shift элемента.</li>

Если SelectionMode имеет значение **Multiple** или **Extended**, выбранные элементы данных можно получить из свойства [SelectedItems](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.selecteditems.aspx). 

Свойства **SelectedIndex**, **SelectedItem** и **SelectedItems** синхронизируются. Например, если для SelectedIndex задано значение -1, SelectedItem равно **null**, а коллекция SelectedItems пуста; если задать для SelectedItem значение **null**, SelectedIndex равно -1, а SelectedItems пуст.

В режиме с множественным выбором **SelectedItem** содержит выбранный первым элемент, а **Selectedindex** — индекс выбранного первым элемента. 

### <a name="respond-to-selection-changes"></a>Реагирование на изменения выбора

Для того, чтобы реагировать на изменения в выборе элементов представления списка, используйте событие [SelectionChanged](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.selector.selectionchanged.aspx). В коде обработчика этого события вы можете получить список выбранных элементов, используя свойство [SelectionChangedEventArgs.AddedItems](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.selectionchangedeventargs.addeditems.aspx). Можно получить любые элементы, выбор которых был отменен, в свойстве [SelectionChangedEventArgs.RemovedItems](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.selectionchangedeventargs.removeditems.aspx). Коллекции AddedItems и RemovedItems содержат не более 1 элемента за исключением случаев, когда пользователь выбирает диапазон элементов удержанием клавиши Shift.

В этом примере показана обработка события **SelectionChanged** и доступ к разным коллекциям элементов.

**XAML**
```xaml
<StackPanel HorizontalAlignment="Right">
    <ListView x:Name="listView1" SelectionMode="Multiple" 
              SelectionChanged="ListView1_SelectionChanged">
        <x:String>Item 1</x:String>
        <x:String>Item 2</x:String>
        <x:String>Item 3</x:String>
        <x:String>Item 4</x:String>
        <x:String>Item 5</x:String>
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
```xaml
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

Методы [SelectAll](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.selectall.aspx), [SelectRange](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.selectrange.aspx) и [DeselectRange](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.deselectrange.aspx) предоставляют более эффективный способ изменения выделения, чем использование свойства SelectedItems. Эти методы осуществляют выделение или отмену выделения с помощью диапазонов индексов элементов. Виртуализованные элементы остаются виртуализованными, поскольку используется только индекс. Все элементы в определенном диапазоне выделены (или их выделение снято) вне зависимости от начального состояния выбора. Событие SelectionChanged происходит однократно для каждого вызова этих методов.

> **Важно!**&nbsp;&nbsp;Необходимо вызывать эти методы, только когда для свойства SelectionMode задано значение Multiple или Extended. Если вызвать SelectRange, когда SelectionMode имеет значение Single или None, создается исключение.

При выборе элементов с использованием диапазонов индексов используйте свойство [SelectedRanges](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.selectedranges.aspx), чтобы получить все выбранные диапазоны в списке.

Если ItemsSource реализует [IItemsRangeInfo](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.iitemsrangeinfo.aspx) и вы используете эти методы, чтобы изменить выделение, свойства **AddedItems** и **RemovedItems** не задаются в элементе SelectionChangedEventArgs. Настройка этих свойств требует отмены виртуализации объекта элемента. Для получения элементов используйте свойство **SelectedRanges**.

Для выделения всех элементов коллекции вызовите метод SelectAll. Однако метод для отмены выделения всех элементов отсутствует. Можно отменить выбор всех элементов, вызвав DeselectRange и передав [ItemIndexRange](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.itemindexrange.aspx) со свойством [FirstIndex](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.itemindexrange.firstindex.aspx), значение которого равно 0, и значением [Length](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.data.itemindexrange.length.aspx), равным числу элементов в коллекции. 

**XAML**
```xaml
<StackPanel Width="160">
    <Button Content="Select all" Click="SelectAllButton_Click"/>
    <Button Content="Deselect all" Click="DeselectAllButton_Click"/>
    <ListView x:Name="listView1" SelectionMode="Multiple">
        <x:String>Item 1</x:String>
        <x:String>Item 2</x:String>
        <x:String>Item 3</x:String>
        <x:String>Item 4</x:String>
        <x:String>Item 5</x:String>
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

Элементы управления ListView и GridView поддерживают перетаскивание элементов внутри данного элемента управления, а также между данным и другими элементами управления ListView и GridView. Подробнее о реализации шаблона перетаскивания см. в разделе [Перетаскивание](https://msdn.microsoft.com/windows/uwp/design/input/drag-and-drop). 

## <a name="get-the-sample-code"></a>Получить пример кода

- [Пример ListView и GridView в XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlListView) — демонстрация элементов управления ListView и GridView.
- [Пример перетаскивания в XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlDragAndDrop) — демонстрация перетаскивания с помощью элемента управления ListView.
- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

- [Списки](lists.md)
- [Контейнеры и шаблоны элементов](item-containers-templates.md)
- [Перетаскивание](https://msdn.microsoft.com/windows/uwp/app-to-app/drag-and-drop)
