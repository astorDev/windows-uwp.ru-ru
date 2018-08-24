---
author: Jwmsft
description: Можно создать представлением расширяемого дерева путем привязки ItemsSource иерархического источника данных, или можно создавать и управлять объектами TreeViewNode самостоятельно.
title: Представление в виде дерева
label: Tree view
template: detail.hbs
ms.author: jimwalk
ms.localizationpriority: medium
pm-contact: predavid
design-contact: ksulliv
dev-contact: joyate
doc-status: Published
dev_langs:
- csharp
- vb
ms.openlocfilehash: 20de58d13c4ace6b71ec952dc88cd59d1ab6114f
ms.sourcegitcommit: c6d6f8b54253e79354f8db14e5cf3b113a3e5014
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "2839006"
---
# <a name="treeview"></a>Иерархическое представление (представление в виде дерева)

> [!IMPORTANT]
> В этой статье описана еще не выпущенная функция, которая может быть существенно изменена до коммерческого выпуска. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации. Функции предварительной версии или их версии требуется [последние построения предварительного просмотра внутренних 10 Windows и пакет SDK для](https://insider.windows.com/for-developers/) [Библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Элемент управления XAML TreeView создает иерархический список с разворачиваемыми и сворачиваемыми узлами, которые содержат вложенные элементы. Его можно использовать для демонстрации структуры папок или вложенных отношений в пользовательском интерфейсе.

API TreeView поддерживают следующие компоненты:

- Многоуровневые вложения
- Выбор одного или нескольких узлов
- (Предварительная версия) Привязка данных к свойству ItemsSource на TreeView и TreeViewItem
- (Предварительная версия) TreeViewItem в корневом шаблон элемента TreeView
- (Предварительная версия) Произвольный типов контента в TreeViewItem
- (Предварительная версия) Перетаскивание между представлениями в виде дерева

| **Получение библиотека интерфейса пользователя Windows** |
| - |
| Этот элемент управления включается в состав библиотека интерфейса пользователя Windows, NuGet пакет, содержащий новые элементы управления и возможности пользовательского интерфейса приложений UWP. Дополнительные сведения, включая инструкции по установке в разделе [Общие сведения о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

| **Интерфейсы API платформы** | **Windows API-интерфейсы библиотеки пользовательского интерфейса** |
| - | - |
| [Класс TreeView](/uwp/api/windows.ui.xaml.controls.treeview), [класс TreeViewNode](/uwp/api/windows.ui.xaml.controls.treeviewnode), [свойство TreeView.ItemsSource](/uwp/api/windows.ui.xaml.controls.treeview.itemssource) | [Класс TreeView](/uwp/api/microsoft.ui.xaml.controls.treeview), [класс TreeViewNode](/uwp/api/microsoft.ui.xaml.controls.treeviewnode), [свойство TreeView.ItemsSource](/uwp/api/microsoft.ui.xaml.controls.treeview.itemssource) |

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

- Используйте TreeView, когда ваши объекты имеют вложенные списочные элементы, и если важно показать иерархическую связь элементов с другими элементами на одном и вышестоящих уровнях.

- Не рекомендуется использовать TreeView, если отображение иерархических связей элементов не имеет высокого значения. Для большинства стандартных ситуаций подходит обычное представление списка

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>При наличии установленного приложения <strong style="font-weight: semi-bold">Коллекция элементов управления XAML</strong> , щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/TreeView">открыть приложение и посмотрите, TreeView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="treeview-ui"></a>Пользовательский интерфейс TreeView

В представлении в виде дерева используется сочетание отступа и значков можно использовать для представления иерархических связей между папкой или родительским узлом и дочерними узлами и объектами, не являющимися папками. Для единообразия на свернутых узлах следует использовать указывающий вправо шеврон, а на развернутых — шеврон, указывающий вниз.

![Значок шеврона в иерархическом представлении](images/treeview-simple.png)

Для представления узлов в шаблон данных элементов иерархического представления можно добавить значок. Если это сделать, значок папки следует использовать только для узлов, представляющих буквальные папки, например, структуры папок на диске.

![Значки шеврона и папки вместе в иерархическом представлении](images/treeview-icons.png)

## <a name="create-a-tree-view"></a>Создание иерархического представления

Можно создать представление в виде дерева путем привязки [ItemsSource](/uwp/api/windows.ui.xaml.controls.treeview.itemssource) иерархического источника данных, или можно создавать и управлять объектами TreeViewNode самостоятельно.

Чтобы создать иерархическое представление, используйте элемент управления [TreeView](/uwp/api/windows.ui.xaml.controls.treeview) и иерархию объектов [TreeViewNode](/uwp/api/windows.ui.xaml.controls.treeviewnode). Создание иерархии узлов, добавив один или несколько узлов в коллекции [RootNodes](/uwp/api/windows.ui.xaml.controls.treeview.rootnodes) элемента управления TreeView. Затем каждый элемент TreeViewNode может иметь больше узлов, добавленных в его дочернюю (Children) коллекцию. Узлы иерархического представления можно вложить на любую необходимую глубину.

Начиная в предварительной версии изнутри Windows, можно привязать иерархического источника данных для свойства [ItemsSource](/uwp/api/windows.ui.xaml.controls.treeview.itemssource) содержимое представления дерева, так же, как с помощью элемента списка автофильтра ItemsSource. Аналогично используйте [ItemTemplate](/uwp/api/windows.ui.xaml.controls.treeview.itemtemplate) (и необязательные [ItemTemplateSelector](/uwp/api/windows.ui.xaml.controls.treeview.itemtemplate)) для предоставления DataTemplate, отображающий элемент.

> [!IMPORTANT]
> ItemsSource — это альтернативный механизм TreeView.RootNodes для занесения содержимого в элементе управления TreeView. В то же время нельзя задать ItemsSource и RootNodes. При использовании ItemsSource узлы созданы для вас и доступа к ним из свойства TreeView.RootNodes.

Вот пример простого иерархического представления, объявленного в XAML. Как правило, узлы добавляются в код, но здесь мы продемонстрируем иерархию XAML, так как это может быть полезно для обеспечения наглядности процесса создания иерархии узлов.

```xaml
<TreeView>
    <TreeView.RootNodes>
        <TreeViewNode Content="Flavors" IsExpanded="True">
            <TreeViewNode.Children>
                <TreeViewNode Content="Vanilla"/>
                <TreeViewNode Content="Strawberry"/>
                <TreeViewNode Content="Chocolate"/>
            </TreeViewNode.Children>
        </TreeViewNode>
    </TreeView.RootNodes>
</TreeView>
```

В большинстве случаев древовидном представлении отображаются данные из источника данных, обычно объявляется корневой элемент управления TreeView в XAML, но добавьте объекты TreeViewNode программно или с помощью привязки данных.

### <a name="bind-to-a-hierarchical-data-source"></a>Привязка к источнику иерархических данных

Для создания представления дерева, с помощью привязки данных, значение свойства TreeView.ItemsSource иерархическую структуру. Затем в ItemTemplate, установите дочерний элемент коллекции элементов в свойство TreeViewItem.ItemsSource.

```xaml
<TreeView ItemsSource="{x:Bind DataSource}">
    <TreeView.ItemTemplate>
        <DataTemplate x:DataType="local:Item">
            <TreeViewItem ItemsSource="{x:Bind Children}"
                          Content="{x:Bind Name}"/>
        </DataTemplate>
    </TreeView.ItemTemplate>
</TreeView>
```

В разделе _представления дерева, с помощью привязки данных_ в разделе примеры полный код.

#### <a name="items-and-item-containers"></a>Элементы и контейнеры элементов

Если вы используете TreeView.ItemsSource, эти интерфейсы API доступны для получения элемента узла или данных из контейнера и наоборот.

| **[TreeViewItem](/uwp/api/windows.ui.xaml.controls.treeviewitem)** | |
| - | - |
| [TreeView.ItemFromContainer](/uwp/api/windows.ui.xaml.controls.treeview.itemfromcontainer) | Возвращает элемент данных для указанного контейнера TreeViewItem. |
| [TreeView.ContainerFromItem](/uwp/api/windows.ui.xaml.controls.treeview.containerfromitem) | Получает контейнер TreeViewItem для заданного элемента данных. |

| **[TreeViewNode (узел иерархического представления)](/uwp/api/windows.ui.xaml.controls.treeviewnode)** | |
| - | - |
| [TreeView.NodeFromContainer](/uwp/api/windows.ui.xaml.controls.treeview.nodefromcontainer) | Получает указанный контейнер TreeViewItem TreeViewNode. |
| [TreeView.ContainerFromNode](/uwp/api/windows.ui.xaml.controls.treeview.containerfromnode) | Получает контейнер TreeViewItem для указанного TreeViewNode. |

### <a name="manage-tree-view-nodes"></a>Управление узлов дерева

Это иерархическое представление аналогично созданному ранее в XAML, однако в этом примере узлы созданы в коде.

```xaml
<TreeView x:Name="sampleTreeView"/>
```

```csharp
private void InitializeTreeView()
{
    TreeViewNode rootNode = new TreeViewNode() { Content = "Flavors" };
    rootNode.IsExpanded = true;
    rootNode.Children.Add(new TreeViewNode() { Content = "Vanilla" });
    rootNode.Children.Add(new TreeViewNode() { Content = "Strawberry" });
    rootNode.Children.Add(new TreeViewNode() { Content = "Chocolate" });

    sampleTreeView.RootNodes.Add(rootNode);
}
```

```vb
Private Sub InitializeTreeView()
    Dim rootNode As New TreeViewNode With {.Content = "Flavors", .IsExpanded = True}
    With rootNode.Children
        .Add(New TreeViewNode With {.Content = "Vanilla"})
        .Add(New TreeViewNode With {.Content = "Strawberry"})
        .Add(New TreeViewNode With {.Content = "Chocolate"})
    End With
    sampleTreeView.RootNodes.Add(rootNode)
End Sub
```

Эти API-интерфейсы можно использовать для управления иерархией данных представления в виде дерева.

| **[TreeView (иерархическое представление)](/uwp/api/windows.ui.xaml.controls.treeview)** | |
| - | - |
| [RootNodes (корневые узлы)](/uwp/api/windows.ui.xaml.controls.treeview.rootnodes) | Иерархическое представление может содержать один или несколько корневых узлов. Чтобы создать корневой узел, добавьте объект TreeViewNode в коллекцию RootNodes. Значением свойства **Parent** корневого узла всегда является **null**. Значение параметра **Depth** корневого узла равно 0. |

| **[TreeViewNode (узел иерархического представления)](/uwp/api/windows.ui.xaml.controls.treeviewnode)** | |
| - | - |
| [Children (дочерние элементы)](/uwp/api/windows.ui.xaml.controls.treeviewnode.children) | Добавьте объекты TreeViewNode в коллекцию Children родительского узла для создания иерархии узла. Узел представляет собой **Parent** всех узлов в его коллекции **Children**. |
| [HasChildren (дочерние элементы присутствуют)](/uwp/api/windows.ui.xaml.controls.treeviewnode.haschildren) | Имеет значение **true**, если узел реализовал дочерние элементы. Значение **false** указывает на пустую папку или элемент. |
| [HasUnrealizedChildren (присутствуют нереализованные дочерние элементы)](/uwp/api/windows.ui.xaml.controls.treeviewnode.hasunrealizedchildren) | Используйте это свойство при заполнении узлов, когда они развернуты. См. раздел _Заполнение узла при его развертывании_ далее в этой статье. |
| [Depth (глубина)](/uwp/api/windows.ui.xaml.controls.treeviewnode.depth) | Указывает, насколько далеко от корневого узла находится дочерний узел. |
| [Parent (родительский элемент)](/uwp/api/windows.ui.xaml.controls.treeviewnode.parent) | Получает TreeViewNode, которому принадлежит коллекция **Children**, частью которой является этот узел. |

Иерархическое представление использует свойства **HasChildren** и **HasUnrealizedChildren**, чтобы определить, отображается ли значок "развернуть/свернуть". Если любое из свойств имеет значение **true**, значок отображается; в противном случае он не отображается.

## <a name="tree-view-node-content"></a>Содержимое узла иерархического представления

Элемент данных, который представляет узел иерархического представления, можно хранить в свойстве [Content](/uwp/api/windows.ui.xaml.controls.treeviewnode.content).

В предыдущих примерах содержимое являлось простым строковом значением. Здесь узел иерархического представления представляет пользовательскую папку "Изображения", поэтому библиотека изображений [StorageFolder](/uwp/api/windows.storage.storagefolder) назначается свойству узла Content.

```csharp
StorageFolder picturesFolder = KnownFolders.PicturesLibrary;
TreeViewNode pictureNode = new TreeViewNode();
pictureNode.Content = picturesFolder;
```

```vb
Dim picturesFolder As StorageFolder = KnownFolders.PicturesLibrary
Dim pictureNode As New TreeViewNode With {.Content = picturesFolder}
```

Можно предоставить [DataTemplate](/uwp/api/windows.ui.xaml.datatemplate), чтобы указать, как элемент данных должен отображаться в иерархическом представлении.

> [!NOTE]
> В Windows 10 версии 1803 необходимо изменить шаблон элемента управления TreeView и указать пользовательский ItemTemplate, если ваше содержимое не является строкой. Дополнительные сведения см. в полном примере в конце этой статьи. В более поздние версии присвойте свойству [TreeView.ItemTemplate](/uwp/api/windows.ui.xaml.controls.treeview.itemtemplate) .

### <a name="item-container-style"></a>Контейнер стиля элемента

Использование ItemsSource или RootNodes, фактический элементов, используемых для отображения каждого узла – называется «контейнер» – — это объект [TreeViewItem](/uwp/api/windows.ui.xaml.controls.treeviewitem) . Задать стиль контейнера, с помощью TreeView ItemContainerStyle используется или ItemContainerStyleSelector свойств.

### <a name="item-template-selectors"></a>Селекторы шаблона элемента

Можно установить разные DataTemplate для элементов представления дерева, на основе типа элемента. Например в приложении explorer файла можно использовать один шаблон данных для папок, а другой для файлов.

![Папки и файлы, используя различные шаблоны данных](images/treeview-icons.png)

Ниже приведен пример того, как создавать и использовать элемент выбора шаблона.

```xaml
<Page.Resources>
    <DataTemplate x:Key="FolderTemplate" x:DataType="local:ExplorerItem">
        <TreeViewItem ItemsSource="{x:Bind Children}">
            <StackPanel Orientation="Horizontal">
                <Image Width="20" Source="Assets/folder.png"/>
                <TextBlock Text="{x:Bind Name}" />
            </StackPanel>
        </TreeViewItem>
    </DataTemplate>

    <DataTemplate x:Key="FileTemplate" x:DataType="local:ExplorerItem">
        <TreeViewItem>
            <StackPanel Orientation="Horizontal">
                <Image Width="20" Source="Assets/file.png"/>
                <TextBlock Text="{Binding Name}"/>
            </StackPanel>
        </TreeViewItem>
    </DataTemplate>

    <local:ExplorerItemTemplateSelector
            x:Key="ExplorerItemTemplateSelector"
            FolderTemplate="{StaticResource FolderTemplate}"
            FileTemplate="{StaticResource FileTemplate}" />
</Page.Resources>

<Grid>
    <TreeView ItemsSource="{x:Bind DataSource}"
              ItemTemplateSelector="{StaticResource ExplorerItemTemplateSelector}"/>
</Grid>
```

```csharp
public class ExplorerItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate FolderTemplate { get; set; }
    public DataTemplate FileTemplate { get; set; }

    protected override DataTemplate SelectTemplateCore(object item)
    {
        var explorerItem = (ExplorerItem)item;
        if (explorerItem.Type == ExplorerItem.ExplorerItemType.Folder) return FolderTemplate;

        return FileTemplate;
    }
}
```

## <a name="interacting-with-a-tree-view"></a>Взаимодействие с иерархическим представлением

Вы можете настроить иерархическое представление, чтобы предоставить пользователю возможность взаимодействовать с ним разными способами:

- Развертывание или свертывание узлов
- Выбор одного или нескольких элементов
- Щелчок для вызова элемента

### <a name="expandcollapse"></a>Разворачивание/сворачивание

Любой узел иерархического представления, у которого есть дочерние элементы, можно развернуть или свернуть, щелкнув глиф разворачивания/сворачивания. Можно также развернуть или свернуть узел программными средствами и отреагировать при изменении состояния узла.

#### <a name="expandcollapse-a-node-programmatically"></a>Разворачивание/сворачивание узла программным способом

Существует два способа разворачивания или сворачивания узла иерархического представления в коде.

- У класса [TreeView](/uwp/api/windows.ui.xaml.controls.treeview) есть методы [Collapse](/uwp/api/windows.ui.xaml.controls.treeview.collapse) и [Expand](/uwp/api/windows.ui.xaml.controls.treeview.expand). При вызове этих методов выполняется передача TreeViewNode, который требуется развернуть или свернуть.

- Каждый элемент [TreeViewNode](/uwp/api/windows.ui.xaml.controls.treeviewnode) имеет свойство [IsExpanded](/uwp/api/windows.ui.xaml.controls.treeviewnode.isexpanded). Это свойство можно использовать для проверки состояния узла или настроить его для изменения состояния. Вы также можете задать это свойство в XAML, чтобы настроить начальное состояние узла.

### <a name="fill-a-node-when-its-expanding"></a>Заполнение узла при его развертывании

Возможно, вам потребуется отобразить большое число узлов в вашем иерархическом представлении. Также вам может быть неизвестно заранее, сколько узлов оно будет содержать. Элемент управления TreeView не виртуализирован, поэтому вы можете управлять ресурсами, заполняя каждый узел, когда он развернут, и удаляя дочерние узлы, когда они свернуты.

Выполните обработку события [Expanding](/uwp/api/windows.ui.xaml.controls.treeview.expand) и используйте свойство [HasUnrealizedChildren](/uwp/api/windows.ui.xaml.controls.treeviewnode.hasunrealizedchildren) для добавления дочерних элементов в узел, если он развернут. Свойство HasUnrealizedChildren указывает на то, требуется ли заполнить узел, либо была ли уже заполнена его коллекция Children. Важно помнить, что элемент TreeViewNode не задает это значение; им нужно необходимо управлять в коде приложения.

Ниже приведен пример использования этих API. См. полный пример кода в конце этой статьи, чтобы ознакомиться с контекстом, включая реализацию 'FillTreeNode'.

```csharp
private void SampleTreeView_Expanding(TreeView sender, TreeViewExpandingEventArgs args)
{
    if (args.Node.HasUnrealizedChildren)
    {
        FillTreeNode(args.Node);
    }
}
```

```vb
Private Sub SampleTreeView_Expanding(sender As TreeView, args As TreeViewExpandingEventArgs)
    If args.Node.HasUnrealizedChildren Then
        FillTreeNode(args.Node)
    End If
End Sub
```

Это не обязательно, однако можно также обработать событие [Collapsed](/uwp/api/windows.ui.xaml.controls.treeview.collapsed) и удалить дочерние узлы, когда родительский узел закрыт. Это может быть важно, если в иерархическом представлении много узлов, или в том случае, если данные узла используют много ресурсов. Следует учитывать влияние на производительность при заполнении узла при его каждом открытии по сравнению с тем, когда дочерние элементы остаются в закрытом узле. Оптимальный вариант будет зависеть от вашего приложения.

Вот пример обработчика для события Collapsed.

```csharp
private void SampleTreeView_Collapsed(TreeView sender, TreeViewCollapsedEventArgs args)
{
    args.Node.Children.Clear();
    args.Node.HasUnrealizedChildren = true;
}
```

```vb
Private Sub SampleTreeView_Collapsed(sender As TreeView, args As TreeViewCollapsedEventArgs)
    args.Node.Children.Clear()
    args.Node.HasUnrealizedChildren = True
End Sub
```

### <a name="invoking-an-item"></a>Вызов элемента

Пользователь может вызывать действие (используя элемент как кнопку), а не выбирать элемент. Обработка события [ItemInvoked](/uwp/api/windows.ui.xaml.controls.treeview.iteminvoked) выполняется для обеспечения отклика на это действие пользователя.

> [!NOTE]
> В отличие от элемента ListView, у которого есть свойство [IsItemClickEnabled](/uwp/api/windows.ui.xaml.controls.listviewbase.isitemclickenabled), вызов элемента всегда включен в иерархическом представлении. Вы по-прежнему можете выбрать, следует ли обрабатывать событие или нет.

**Класс [TreeViewItemInvokedEventArgs](/uwp/api/windows.ui.xaml.controls.treeviewiteminvokedeventargs)**

Аргументы события ItemInvoked предоставляют доступ к вызываемому элементу. У свойства [InvokedItem](/uwp/api/windows.ui.xaml.controls.treeviewiteminvokedeventargs.invokeditem) есть вызванный узел. Можно передать его элементу TreeViewNode и получить элемент данных из свойства TreeViewNode.Content.

Ниже приведен пример обработчика событий ItemInvoked. Элементом данных является [IStorageItem](/uwp/api/windows.storage.istorageitem), и в этом примере представлены только некоторые сведения о файле и дереве. Кроме того, если узел находится в узле папки, наряду с этим он раскрывает или сворачивает узел. В противном случае узел разворачивается или сворачивается, только если щелкнуть значок шеврона.

```csharp
private void SampleTreeView_ItemInvoked(TreeView sender, TreeViewItemInvokedEventArgs args)
{
    var node = args.InvokedItem as TreeViewNode;
    if (node.Content is IStorageItem item)
    {
        FileNameTextBlock.Text = item.Name;
        FilePathTextBlock.Text = item.Path;
        TreeDepthTextBlock.Text = node.Depth.ToString();

        if (node.Content is StorageFolder)
        {
            node.IsExpanded = !node.IsExpanded;
        }
    }
}
```

```vb
Private Sub SampleTreeView_ItemInvoked(sender As TreeView, args As TreeViewItemInvokedEventArgs)
    Dim node = TryCast(args.InvokedItem, TreeViewNode)
    Dim item = TryCast(node.Content, IStorageItem)
    If item IsNot Nothing Then
        FileNameTextBlock.Text = item.Name
        FilePathTextBlock.Text = item.Path
        TreeDepthTextBlock.Text = node.Depth.ToString()
        If TypeOf node.Content Is StorageFolder Then
            node.IsExpanded = Not node.IsExpanded
        End If
    End If
End Sub
```

### <a name="item-selection"></a>Выбор элементов

Элемент управления TreeView поддерживает выбор как одного, так и нескольких элементов. По умолчанию выбор узлов отключен, однако можно задать свойство [TreeView.SelectionMode](/uwp/api/windows.ui.xaml.controls.treeview.selectionmode), чтобы разрешить выбор узлов. Значениями [TreeViewSelectionMode](/uwp/api/windows.ui.xaml.controls.treeviewselectionmode) являются **None**, **Single** и **Multiple**.

Когда возможность выбора включена, рядом с каждым узлом иерархического представления отображается флажок, а выбранные элементы выделены. Пользователь может выбрать элемент или отменить его выбор, используя этот флажок; щелчок по элементу приводит к его вызову.

Выбор или Отмена выбора родительского узла будет установите или снимите флажок все дочерние элементы в этом узле. Если некоторые, но не всех дочерних элементов в родительский узел выбраны, флажок для родительского узла отображается как неопределенное (заполняются черного прямоугольника).

![Несколько фрагментов в виде дерева](images/treeview-selection.png)

Выбранные узлы добавляются в коллекцию [SelectedNodes](/uwp/api/windows.ui.xaml.controls.treeview.selectednodes) иерархического представления. Вы можете вызвать метод [SelectAll](/uwp/api/windows.ui.xaml.controls.treeview.selectall), чтобы выбрать все узлы в иерархическом представлении.

> [!NOTE]
> При вызове **SelectAll** выбираются все реализованные узлы, независимо от значения SelectionMode. Чтобы обеспечить согласованное взаимодействие пользователя с интерфейсом, следует вызывать только SelectAll, если значением SelectionMode является **Multiple**.

#### <a name="selection-and-realizedunrealized-nodes"></a>Выбор и реализованные/нереализованные узлы

Если в иерархическом представлении есть нереализованные узлы, они не учитываются при выборе. Вот что нужно помнить о выборе элементов при наличии нереализованных узлов.

- Если пользователь выбирает родительский узел, также выбираются все реализованные дочерние элементы, принадлежащие этому родительскому узлу. Аналогичным образом, при выборе всех дочерних узлов также выбирается родительский узел.
- Метод SelectAll только добавляет реализованные узлы в коллекцию SelectedNodes.
- При выборе родительского узла с нереализованными дочерними элементами также будут выбраны дочерние элементы, если их реализовать.

## <a name="code-examples"></a>Примеры кода

### <a name="tree-view-using-xaml"></a>Представления дерева, с помощью XAML

Этот пример демонстрирует, как создать простую иерархическую структуру в XAML. Иерархическое представление содержит вкусы мороженого и поливки для него, которые пользователь может выбирать. Они распределены по категориям. Включена возможность выбора нескольких элементов, и когда пользователь нажимает кнопку, в пользовательском интерфейсе основного приложения отображаются элементы SelectedItems.

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" Padding="100">
    <SplitView IsPaneOpen="True"
               DisplayMode="Inline"
               OpenPaneLength="296">
        <SplitView.Pane>
            <TreeView x:Name="DessertTree" SelectionMode="Multiple">
                <TreeView.RootNodes>
                    <TreeViewNode Content="Flavors" IsExpanded="True">
                        <TreeViewNode.Children>
                            <TreeViewNode Content="Vanilla"/>
                            <TreeViewNode Content="Strawberry"/>
                            <TreeViewNode Content="Chocolate"/>
                        </TreeViewNode.Children>
                    </TreeViewNode>

                    <TreeViewNode Content="Toppings">
                        <TreeViewNode.Children>
                            <TreeViewNode Content="Candy">
                                <TreeViewNode.Children>
                                    <TreeViewNode Content="Chocolate"/>
                                    <TreeViewNode Content="Mint"/>
                                    <TreeViewNode Content="Sprinkles"/>
                                </TreeViewNode.Children>
                            </TreeViewNode>
                            <TreeViewNode Content="Fruits">
                                <TreeViewNode.Children>
                                    <TreeViewNode Content="Mango"/>
                                    <TreeViewNode Content="Peach"/>
                                    <TreeViewNode Content="Kiwi"/>
                                </TreeViewNode.Children>
                            </TreeViewNode>
                            <TreeViewNode Content="Berries">
                                <TreeViewNode.Children>
                                    <TreeViewNode Content="Strawberry"/>
                                    <TreeViewNode Content="Blueberry"/>
                                    <TreeViewNode Content="Blackberry"/>
                                </TreeViewNode.Children>
                            </TreeViewNode>
                        </TreeViewNode.Children>
                    </TreeViewNode>
                </TreeView.RootNodes>
            </TreeView>
        </SplitView.Pane>

        <StackPanel Grid.Column="1" Margin="12,0">
            <Button Content="Select all" Click="SelectAllButton_Click"/>
            <Button Content="Create order" Click="OrderButton_Click" Margin="0,12"/>
            <TextBlock Text="Your flavor selections:" Style="{StaticResource CaptionTextBlockStyle}"/>
            <TextBlock x:Name="FlavorList" Margin="0,0,0,12"/>
            <TextBlock Text="Your topping selections:" Style="{StaticResource CaptionTextBlockStyle}"/>
            <TextBlock x:Name="ToppingList"/>
        </StackPanel>
    </SplitView>
</Grid>
```

```csharp
private void OrderButton_Click(object sender, RoutedEventArgs e)
{
    FlavorList.Text = string.Empty;
    ToppingList.Text = string.Empty;

    foreach (TreeViewNode node in DessertTree.SelectedNodes)
    {
        if (node.Parent.Content?.ToString() == "Flavors")
        {
            FlavorList.Text += node.Content + "; ";
        }
        else if (node.HasChildren == false)
        {
            ToppingList.Text += node.Content + "; ";
        }
    }
}

private void SelectAllButton_Click(object sender, RoutedEventArgs e)
{
    if (DessertTree.SelectionMode == TreeViewSelectionMode.Multiple)
    {
        DessertTree.SelectAll();
    }
}
```

```vb
Private Sub OrderButton_Click(sender As Object, e As RoutedEventArgs)
    FlavorList.Text = String.Empty
    ToppingList.Text = String.Empty
    For Each node As TreeViewNode In DessertTree.SelectedNodes
        If node.Parent.Content?.ToString() = "Flavors" Then
            FlavorList.Text += node.Content & "; "
        ElseIf node.HasChildren = False Then
            ToppingList.Text += node.Content & "; "
        End If
    Next
End Sub

Private Sub SelectAllButton_Click(sender As Object, e As RoutedEventArgs)
    If DessertTree.SelectionMode = TreeViewSelectionMode.Multiple Then
        DessertTree.SelectAll()
    End If
End Sub
```

### <a name="tree-view-using-data-binding"></a>Представления дерева, с помощью привязки данных

В этом примере показано, как создать дерево же, что и в предыдущем примере. Тем не менее вместо создания иерархии данных в XAML, данных создается в коде и привязать свойство ItemsSource представлении дерева. (Кнопка обработчиков событий, приведенные в предыдущем примере относится к в этом примере также).

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" Padding="100">
    <SplitView IsPaneOpen="True"
               DisplayMode="Inline"
               OpenPaneLength="296">
        <SplitView.Pane>
            <TreeView Name="DessertTree"
                      SelectionMode="Multiple"
                      ItemsSource="{x:Bind DataSource}">
                <TreeView.ItemTemplate>
                    <DataTemplate x:DataType="local:Item">
                        <TreeViewItem ItemsSource="{x:Bind Children}"
                                      Content="{x:Bind Name}"/>
                    </DataTemplate>
                </TreeView.ItemTemplate>
            </TreeView>
        </SplitView.Pane>

        <StackPanel Grid.Column="1" Margin="12,0">
            <Button Content="Select all" Click="SelectAllButton_Click"/>
            <Button Content="Create order" Click="OrderButton_Click" Margin="0,12"/>
            <TextBlock Text="Your flavor selections:" Style="{StaticResource CaptionTextBlockStyle}"/>
            <TextBlock x:Name="FlavorList" Margin="0,0,0,12"/>
            <TextBlock Text="Your topping selections:" Style="{StaticResource CaptionTextBlockStyle}"/>
            <TextBlock x:Name="ToppingList"/>
        </StackPanel>
    </SplitView>
</Grid>
```

```csharp
public sealed partial class MainPage : Page
{
    private ObservableCollection<Item> DataSource = new ObservableCollection<Item>();

    public MainPage()
    {
        this.InitializeComponent();
        DataSource = GetDessertData();
    }

    private ObservableCollection<Item> GetDessertData()
    {
        var list = new ObservableCollection<Item>();
        Item flavorsCategory = new Item()
        {
            Name = "Flavors",
            Children =
            {
                new Item() { Name = "Vanilla" },
                new Item() { Name = "Strawberry" },
                new Item() { Name = "Chocolate" }
            }
        };
        Item toppingsCategory = new Item()
        {
            Name = "Toppings",
            Children =
            {
                new Item()
                {
                    Name = "Candy",
                    Children =
                    {
                        new Item() { Name = "Chocolate" },
                        new Item() { Name = "Mint" },
                        new Item() { Name = "Sprinkles" }
                    }
                },
                new Item()
                {
                    Name = "Fruits",
                    Children =
                    {
                        new Item() { Name = "Mango" },
                        new Item() { Name = "Peach" },
                        new Item() { Name = "Kiwi" }
                    }
                },
                new Item()
                {
                    Name = "Berries",
                    Children =
                    {
                        new Item() { Name = "Strawberry" },
                        new Item() { Name = "Blueberry" },
                        new Item() { Name = "Blackberry" }
                    }
                }
            }
        };

        list.Add(flavorsCategory);
        list.Add(toppingsCategory);
        return list;
    }

    // Button event handlers...
}

public class Item
{
    public string Name { get; set; }
    public ObservableCollection<Item> Children { get; set; } = new ObservableCollection<Item>();

    public override string ToString()
    {
        return Name;
    }
}
```

### <a name="pictures-and-music-library-tree-view"></a>Иерархическое представление библиотеки изображений и музыки

В этом примере показано, как создать иерархическое представление, отображающее содержимое и структуру библиотек пользователя "Изображения" и "Музыка". Число элементов не может быть известно заранее, поэтому каждый узел заполняется при его развертывании и очищается при его сворачивании.

Пользовательский шаблон элементов используется для отображения элементов данных, которые относятся к типу [IStorageItem](/uwp/api/windows.storage.istorageitem).

> [!IMPORTANT]
> В этом примере кода необходимо использовать возможности picturesLibrary и musicLibrary. Дополнительные сведения о доступе к файлам см. в разделах [Разрешения на доступ к файлам](../../files/file-access-permissions.md), [Перечисление файлов и папок и адресация им запросов](../../files/quickstart-listing-files-and-folders.md) и [Файлы и папки в библиотеках музыки, изображений и видео](../../files/quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md).

```xaml
<Page
    x:Class="TreeViewApp1.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:TreeViewApp1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">
    <Page.Resources>
        <DataTemplate x:Key="TreeViewItemDataTemplate">
            <Grid Height="44">
                <TextBlock
                    Text="{Binding Content.DisplayName}"
                    HorizontalAlignment="Left"
                    VerticalAlignment="Center"
                    Style="{ThemeResource BodyTextBlockStyle}"/>
            </Grid>
        </DataTemplate>

        <Style TargetType="TreeView">
            <Setter Property="IsTabStop" Value="False" />
            <Setter Property="Template">
                <Setter.Value>
                    <ControlTemplate TargetType="TreeView">
                        <TreeViewList x:Name="ListControl"
                                      ItemTemplate="{StaticResource TreeViewItemDataTemplate}"
                                      ItemContainerStyle="{StaticResource TreeViewItemStyle}"
                                      CanDragItems="True"
                                      AllowDrop="True"
                                      CanReorderItems="True">
                            <TreeViewList.ItemContainerTransitions>
                                <TransitionCollection>
                                    <ContentThemeTransition />
                                    <ReorderThemeTransition />
                                    <EntranceThemeTransition IsStaggeringEnabled="False" />
                                </TransitionCollection>
                            </TreeViewList.ItemContainerTransitions>
                        </TreeViewList>
                    </ControlTemplate>
                </Setter.Value>
            </Setter>
        </Style>
    </Page.Resources>

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <SplitView IsPaneOpen="True"
                   DisplayMode="Inline"
                   OpenPaneLength="296">
            <SplitView.Pane>
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition/>
                    </Grid.RowDefinitions>
                    <Button Content="Refresh tree" Click="RefreshButton_Click" Margin="24,12"/>
                    <TreeView x:Name="sampleTreeView" Grid.Row="1" SelectionMode="Single"
                              Expanding="SampleTreeView_Expanding"
                              Collapsed="SampleTreeView_Collapsed"
                              ItemInvoked="SampleTreeView_ItemInvoked"/>
                </Grid>
            </SplitView.Pane>

            <StackPanel Grid.Column="1" Margin="12,72">
                <TextBlock Text="File name:" Style="{StaticResource CaptionTextBlockStyle}"/>
                <TextBlock x:Name="FileNameTextBlock" Margin="0,0,0,12"/>

                <TextBlock Text="File path:" Style="{StaticResource CaptionTextBlockStyle}"/>
                <TextBlock x:Name="FilePathTextBlock" Margin="0,0,0,12"/>

                <TextBlock Text="Tree depth:" Style="{StaticResource CaptionTextBlockStyle}"/>
                <TextBlock x:Name="TreeDepthTextBlock" Margin="0,0,0,12"/>
            </StackPanel>
        </SplitView>
    </Grid>
</Page>
```

```csharp
public MainPage()
{
    this.InitializeComponent();
    InitializeTreeView();
}

private void InitializeTreeView()
{
    // A TreeView can have more than 1 root node. The Pictures library
    // and the Music library will each be a root node in the tree.
    // Get Pictures library.
    StorageFolder picturesFolder = KnownFolders.PicturesLibrary;
    TreeViewNode pictureNode = new TreeViewNode();
    pictureNode.Content = picturesFolder;
    pictureNode.IsExpanded = true;
    pictureNode.HasUnrealizedChildren = true;
    sampleTreeView.RootNodes.Add(pictureNode);
    FillTreeNode(pictureNode);

    // Get Music library.
    StorageFolder musicFolder = KnownFolders.MusicLibrary;
    TreeViewNode musicNode = new TreeViewNode();
    musicNode.Content = musicFolder;
    musicNode.IsExpanded = true;
    musicNode.HasUnrealizedChildren = true;
    sampleTreeView.RootNodes.Add(musicNode);
    FillTreeNode(musicNode);
}

private async void FillTreeNode(TreeViewNode node)
{
    // Get the contents of the folder represented by the current tree node.
    // Add each item as a new child node of the node that's being expanded.

    // Only process the node if it's a folder and has unrealized children.
    StorageFolder folder = null;
    if (node.Content is StorageFolder && node.HasUnrealizedChildren == true)
    {
        folder = node.Content as StorageFolder;
    }
    else
    {
        // The node isn't a folder, or it's already been filled.
        return;
    }

    IReadOnlyList<IStorageItem> itemsList = await folder.GetItemsAsync();

    if (itemsList.Count == 0)
    {
        // The item is a folder, but it's empty. Leave HasUnrealizedChildren = true so
        // that the chevron appears, but don't try to process children that aren't there.
        return;
    }

    foreach (var item in itemsList)
    {
        var newNode = new TreeViewNode();
        newNode.Content = item;

        if (item is StorageFolder)
        {
            // If the item is a folder, set HasUnrealizedChildren to true. 
            // This makes the collapsed chevron show up.
            newNode.HasUnrealizedChildren = true;
        }
        else
        {
            // Item is StorageFile. No processing needed for this scenario.
        }
        node.Children.Add(newNode);
    }
    // Children were just added to this node, so set HasUnrealizedChildren to false.
    node.HasUnrealizedChildren = false;
}

private void SampleTreeView_Expanding(TreeView sender, TreeViewExpandingEventArgs args)
{
    if (args.Node.HasUnrealizedChildren)
    {
        FillTreeNode(args.Node);
    }
}

private void SampleTreeView_Collapsed(TreeView sender, TreeViewCollapsedEventArgs args)
{
    args.Node.Children.Clear();
    args.Node.HasUnrealizedChildren = true;
}

private void SampleTreeView_ItemInvoked(TreeView sender, TreeViewItemInvokedEventArgs args)
{
    var node = args.InvokedItem as TreeViewNode;
    if (node.Content is IStorageItem item)
    {
        FileNameTextBlock.Text = item.Name;
        FilePathTextBlock.Text = item.Path;
        TreeDepthTextBlock.Text = node.Depth.ToString();

        if (node.Content is StorageFolder)
        {
            node.IsExpanded = !node.IsExpanded;
        }
    }
}

private void RefreshButton_Click(object sender, RoutedEventArgs e)
{
    sampleTreeView.RootNodes.Clear();
    InitializeTreeView();
}
```

```vb
Public Sub New()
    InitializeComponent()
    InitializeTreeView()
End Sub

Private Sub InitializeTreeView()
    ' A TreeView can have more than 1 root node. The Pictures library
    ' and the Music library will each be a root node in the tree.
    ' Get Pictures library.
    Dim picturesFolder As StorageFolder = KnownFolders.PicturesLibrary
    Dim pictureNode As New TreeViewNode With {
        .Content = picturesFolder,
        .IsExpanded = True,
        .HasUnrealizedChildren = True
    }
    sampleTreeView.RootNodes.Add(pictureNode)
    FillTreeNode(pictureNode)

    ' Get Music library.
    Dim musicFolder As StorageFolder = KnownFolders.MusicLibrary
    Dim musicNode As New TreeViewNode With {
        .Content = musicFolder,
        .IsExpanded = True,
        .HasUnrealizedChildren = True
    }
    sampleTreeView.RootNodes.Add(musicNode)
    FillTreeNode(musicNode)
End Sub

Private Async Sub FillTreeNode(node As TreeViewNode)
    ' Get the contents of the folder represented by the current tree node.
    ' Add each item as a new child node of the node that's being expanded.

    ' Only process the node if it's a folder and has unrealized children.
    Dim folder As StorageFolder = Nothing
    If TypeOf node.Content Is StorageFolder AndAlso node.HasUnrealizedChildren Then
        folder = TryCast(node.Content, StorageFolder)
    Else
        ' The node isn't a folder, or it's already been filled.
        Return
    End If

    Dim itemsList As IReadOnlyList(Of IStorageItem) = Await folder.GetItemsAsync()
    If itemsList.Count = 0 Then
        ' The item is a folder, but it's empty. Leave HasUnrealizedChildren = true so
        ' that the chevron appears, but don't try to process children that aren't there.
        Return
    End If

    For Each item In itemsList
        Dim newNode As New TreeViewNode With {
            .Content = item
        }
        If TypeOf item Is StorageFolder Then
            ' If the item is a folder, set HasUnrealizedChildren to True.
            ' This makes the collapsed chevron show up.
            newNode.HasUnrealizedChildren = True
        Else
            ' Item is StorageFile. No processing needed for this scenario.
        End If
        node.Children.Add(newNode)
    Next

    ' Children were just added to this node, so set HasUnrealizedChildren to False.
    node.HasUnrealizedChildren = False
End Sub

Private Sub SampleTreeView_Expanding(sender As TreeView, args As TreeViewExpandingEventArgs)
    If args.Node.HasUnrealizedChildren Then
        FillTreeNode(args.Node)
    End If
End Sub

Private Sub SampleTreeView_Collapsed(sender As TreeView, args As TreeViewCollapsedEventArgs)
    args.Node.Children.Clear()
    args.Node.HasUnrealizedChildren = True
End Sub

Private Sub SampleTreeView_ItemInvoked(sender As TreeView, args As TreeViewItemInvokedEventArgs)
    Dim node = TryCast(args.InvokedItem, TreeViewNode)
    Dim item = TryCast(node.Content, IStorageItem)
    If item IsNot Nothing Then
        FileNameTextBlock.Text = item.Name
        FilePathTextBlock.Text = item.Path
        TreeDepthTextBlock.Text = node.Depth.ToString()
        If TypeOf node.Content Is StorageFolder Then
            node.IsExpanded = Not node.IsExpanded
        End If
    End If
End Sub

Private Sub RefreshButton_Click(sender As Object, e As RoutedEventArgs)
    sampleTreeView.RootNodes.Clear()
    InitializeTreeView()
End Sub
```

## <a name="related-articles"></a>Связанные разделы

- [Класс TreeView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.treeview)
- [Класс ListView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listview.aspx)
- [ListView и GridView](listview-and-gridview.md)
