---
description: Вы можете создать развертываемое представление в виде дерева, привязав ItemsSource к иерархическому источнику данных, или создавать объекты TreeViewNode и управлять ими самостоятельно.
title: Представление в виде дерева
label: Tree view
template: detail.hbs
ms.date: 06/14/2019
ms.topic: article
ms.localizationpriority: medium
pm-contact: predavid
design-contact: ksulliv
dev-contact: joyate
doc-status: Published
dev_langs:
- csharp
- vb
ms.custom: RS5, 19H1
ms.openlocfilehash: b4333d7d1b1b1561a88e92221e846471d7205ea5
ms.sourcegitcommit: 139717a79af648a9231821bdfcaf69d8a1e6e894
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67714040"
---
# <a name="treeview"></a>Представление в виде дерева

Элемент управления [XAML TreeView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.treeview) создает иерархический список с развертываемыми и свертываемыми узлами, которые содержат вложенные элементы. Его можно использовать для демонстрации структуры папок или вложенных отношений в пользовательском интерфейсе.

API **TreeView** поддерживают следующие функции:

- Многоуровневые вложения
- Выбор одного или нескольких узлов.
- Привязка данных к свойству **ItemsSource** элементов управления **TreeView** и [TreeViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.treeviewitem).
- **TreeViewItem** как корень шаблона элемента **TreeView**.
- Содержимое произвольного типа в элементе **TreeViewItem**.
- Операции перетаскивания между представлениями в виде дерева.

| **Получение библиотеки пользовательского интерфейса Windows** |
| - |
| Этот элемент управления является частью библиотеки пользовательского интерфейса Windows, пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в [обзорной статье о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

| **API платформы** | **API библиотеки пользовательского интерфейса Windows** |
| - | - |
| [Класс TreeView](/uwp/api/windows.ui.xaml.controls.treeview), [класс TreeViewNode](/uwp/api/windows.ui.xaml.controls.treeviewnode), [свойство TreeView.ItemsSource](/uwp/api/windows.ui.xaml.controls.treeview.itemssource) | [Класс TreeView](/uwp/api/microsoft.ui.xaml.controls.treeview), [класс TreeViewNode](/uwp/api/microsoft.ui.xaml.controls.treeviewnode), [свойство TreeView.ItemsSource](/uwp/api/microsoft.ui.xaml.controls.treeview.itemssource) |

В этом документе мы будем использовать в XAML псевдоним **muxc** для всех API библиотеки пользовательского интерфейса Windows, которую мы добавили в проект. Мы добавили его для нашего элемента [Page](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page):

```xaml
xmlns:muxc="using:Microsoft.UI.Xaml.Controls"
```

В коде C# мы также будем использовать псевдоним **muxc** для всех API библиотеки пользовательского интерфейса Windows, которую мы добавили в проект. Мы добавили оператор **using** в верхней части файла:

```cs
using muxc = Microsoft.UI.Xaml.Controls;
```

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

- Используйте **TreeView**, если ваши объекты имеют вложенные элементы списка и если важно показать иерархическую связь элементов с другими элементами и узлами.

- Не рекомендуется использовать **TreeView**, если отображение иерархических связей элементов не имеет большого значения. Для большинства сценариев просмотра подходит обычное представление списка.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/TreeView">открыть приложение и увидеть TreeView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="treeview-ui"></a>Пользовательский интерфейс TreeView

В представлении в виде дерева используется сочетание отступа и значков для представления вложенной взаимосвязи между родительскими и дочерними узлами. На свернутых узлах используется указывающий вправо шеврон, а на развернутых — шеврон, указывающий вниз.

![Значок шеврона в TreeView](images/treeview-simple.png)

Для представления узлов в шаблон данных элементов в представлении в виде дерева можно добавить значок. Например, если показывается иерархия файловой системы, можно использовать значки папок для родительских узлов и значки файлов для листовых узлов.

![Значки шеврона и папки вместе в представлении в виде дерева](images/treeview-icons.png)

## <a name="create-a-tree-view"></a>Создание представления в виде дерева

Вы можете создать представление в виде дерева, привязав [ItemsSource](/uwp/api/windows.ui.xaml.controls.treeview.itemssource) к иерархическому источнику данных, или создать объекты **TreeViewNode** и управлять ими самостоятельно.

Для создания представления в виде дерева используйте элемент управления [TreeView](/uwp/api/windows.ui.xaml.controls.treeview) и иерархию объектов [TreeViewNode](/uwp/api/windows.ui.xaml.controls.treeviewnode). Иерархия узла создается путем добавления одного или нескольких корневых узлов в коллекцию [RootNodes](/uwp/api/windows.ui.xaml.controls.treeview.rootnodes) элемента управления **TreeView**. В каждом элементе **TreeViewNode** может быть больше узлов, добавленных в его коллекцию [Children](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.treeviewnode.children). Узлы представления в виде дерева можно вложить на любую необходимую глубину.

Вы можете привязать иерархический источник данных к свойству [ItemsSource](/uwp/api/windows.ui.xaml.controls.treeview.itemssource) для предоставления содержимого представления в виде дерева так же, как в случае с элементом **ItemsSource** представления [ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview). Аналогичным образом можно использовать свойство [ItemTemplate](/uwp/api/windows.ui.xaml.controls.treeview.itemtemplate) (и необязательное свойство [ItemTemplateSelector](/uwp/api/windows.ui.xaml.controls.treeview.itemtemplate)) для предоставления [DataTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.datatemplate) и выполнения визуализации элемента.

> [!IMPORTANT]
> Для работы с **ItemsSource** и связанными с ним API требуется Windows 10, версия 1809 ([пакет SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или выше либо [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).
>
> **ItemsSource** — это альтернативный механизм **TreeView.RootNodes** для размещения содержимого в элементе управления **TreeView**. Нельзя одновременно задать **ItemsSource** и **RootNodes**. При использовании **ItemsSource** узлы создаются автоматически. Они доступны через свойство **TreeView.RootNodes**.

Здесь продемонстрирован пример простого представления в виде дерева, объявленного в XAML. Как правило, узлы добавляются в код, но здесь мы продемонстрируем иерархическую структуру данных XAML, так как это может обеспечить наглядность процесса создания иерархии узлов.

```xaml
<muxc:TreeView>
    <muxc:TreeView.RootNodes>
        <muxc:TreeViewNode Content="Flavors"
                               IsExpanded="True">
            <muxc:TreeViewNode.Children>
                <muxc:TreeViewNode Content="Vanilla"/>
                <muxc:TreeViewNode Content="Strawberry"/>
                <muxc:TreeViewNode Content="Chocolate"/>
            </muxc:TreeViewNode.Children>
        </muxc:TreeViewNode>
    </muxc:TreeView.RootNodes>
</muxc:TreeView>
```

В большинстве случаев представление в виде дерева отображает данные из источника данных, поэтому обычно следует объявлять корневой элемент управления **TreeView** в XAML, но добавлять объекты **TreeViewNode** нужно в коде или с использованием привязки данных.

### <a name="bind-to-a-hierarchical-data-source"></a>Привязка к иерархическому источнику данных

Чтобы создать представление в виде дерева с использованием привязки данных, необходимо задать иерархическую коллекцию для свойства **TreeView.ItemsSource**. Затем в **ItemTemplate** задайте коллекцию дочерних элементов для свойства **TreeViewItem.ItemsSource**.

```xaml
<muxc:TreeView ItemsSource="{x:Bind DataSource}">
    <muxc:TreeView.ItemTemplate>
        <DataTemplate x:DataType="local:Item">
            <muxc:TreeViewItem ItemsSource="{x:Bind Children}"
                                   Content="{x:Bind Name}"/>
        </DataTemplate>
    </muxc:TreeView.ItemTemplate>
</muxc:TreeView>
```

Весь код показан в разделе [Представление в виде дерева с использованием привязки данных](#tree-view-using-data-binding).

#### <a name="items-and-item-containers"></a>Элементы и контейнеры элементов

Если вы используете **TreeView.ItemsSource**, эти API доступны для получения узлов или элементов данных из контейнера и наоборот.

| **[Элемент представления в виде дерева](/uwp/api/windows.ui.xaml.controls.treeviewitem)** | |
| - | - |
| [TreeView.ItemFromContainer](/uwp/api/windows.ui.xaml.controls.treeview.itemfromcontainer) | Получает элемент данных для указанного контейнера **TreeViewItem**. |
| [TreeView.ContainerFromItem](/uwp/api/windows.ui.xaml.controls.treeview.containerfromitem) | Получает контейнер **TreeViewItem** для указанного элемента данных. |

| **[Узел представления в виде дерева](/uwp/api/windows.ui.xaml.controls.treeviewnode)** | |
| - | - |
| [TreeView.NodeFromContainer](/uwp/api/windows.ui.xaml.controls.treeview.nodefromcontainer) | Получает **TreeViewNode** для указанного контейнера **TreeViewItem**. |
| [TreeView.ContainerFromNode](/uwp/api/windows.ui.xaml.controls.treeview.containerfromnode) | Получает контейнер **TreeViewItem** для указанного объекта **TreeViewNode**. |

### <a name="manage-tree-view-nodes"></a>Управление узлами представления в виде дерева

Это представление в виде дерева аналогично созданному ранее в XAML, однако в этом примере узлы созданы в коде.

```xaml
<muxc:TreeView x:Name="sampleTreeView"/>
```

```csharp
private void InitializeTreeView()
{
    muxc.TreeViewNode rootNode = new muxc.TreeViewNode() { Content = "Flavors" };
    rootNode.IsExpanded = true;
    rootNode.Children.Add(new muxc.TreeViewNode() { Content = "Vanilla" });
    rootNode.Children.Add(new muxc.TreeViewNode() { Content = "Strawberry" });
    rootNode.Children.Add(new muxc.TreeViewNode() { Content = "Chocolate" });

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

| **[Представление в виде дерева](/uwp/api/windows.ui.xaml.controls.treeview)** | |
| - | - |
| [RootNodes](/uwp/api/windows.ui.xaml.controls.treeview.rootnodes) | Представление в виде дерева может содержать один или несколько корневых узлов. Для создания корневого узла добавьте объект **TreeViewNode** в коллекцию **RootNodes**. Значением свойства **Parent** корневого узла всегда является **null**. Значение параметра **Depth** корневого узла равно 0. |

| **[Узел представления в виде дерева](/uwp/api/windows.ui.xaml.controls.treeviewnode)** | |
| - | - |
| [Children](/uwp/api/windows.ui.xaml.controls.treeviewnode.children) | Добавьте объекты **TreeViewNode** в коллекцию **Children** родительского узла для создания иерархии узла. Узел является **родительским** для всех узлов в своей коллекции **Children**. |
| [HasChildren](/uwp/api/windows.ui.xaml.controls.treeviewnode.haschildren) | Имеет значение **true**, если узел реализовал дочерние элементы. Значение **false** указывает на пустую папку или элемент. |
| [HasUnrealizedChildren](/uwp/api/windows.ui.xaml.controls.treeviewnode.hasunrealizedchildren) | Используйте это свойство, если вы заполняете узлы по мере их развертывания. См. сведения в разделе [Заполнение узла при его развертывании](#fill-a-node-when-its-expanding) далее в этой статье. |
| [Depth](/uwp/api/windows.ui.xaml.controls.treeviewnode.depth) | Указывает, насколько далеко от корневого узла находится дочерний узел. |
| [Parent](/uwp/api/windows.ui.xaml.controls.treeviewnode.parent) | Получает объект **TreeViewNode**, которому принадлежит коллекция **Children**, частью которой является этот узел. |

Представление в виде дерева использует свойства **HasChildren** и **HasUnrealizedChildren**, чтобы определить, отображается ли значок "развернуть/свернуть". Если любое из свойств имеет значение **true**, значок отображается. В противном случае он не отображается.

## <a name="tree-view-node-content"></a>Содержимое узла представления в виде дерева

Элемент данных, который представляет узел представления в виде дерева, можно хранить в свойстве [Content](/uwp/api/windows.ui.xaml.controls.treeviewnode.content).

В предыдущих примерах содержимое являлось простым строковым значением. Здесь узел представления в виде дерева представляет пользовательскую папку **Pictures**, поэтому библиотека изображений [StorageFolder](/uwp/api/windows.storage.storagefolder) назначается свойству узла **Content**.

```csharp
StorageFolder picturesFolder = KnownFolders.PicturesLibrary;
muxc.TreeViewNode pictureNode = new muxc.TreeViewNode();
pictureNode.Content = picturesFolder;
```

```vb
Dim picturesFolder As StorageFolder = KnownFolders.PicturesLibrary
Dim pictureNode As New TreeViewNode With {.Content = picturesFolder}
```

> [!NOTE]
> Чтобы получить доступ к папке **Pictures**, необходимо указать возможность **Pictures Library** в манифесте приложения. Дополнительные сведения см. в статье [Объявление возможностей приложения](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).

Можно предоставить [DataTemplate](/uwp/api/windows.ui.xaml.datatemplate), чтобы указать, как элемент данных должен отображаться в представлении в виде дерева.

> [!NOTE]
> В Windows 10, версия 1803 необходимо изменить шаблон элемента управления **TreeView** и указать пользовательский шаблон **ItemTemplate**, если ваше содержимое не является строкой. Дополнительные сведения см. в полном примере в конце этой статьи. В более поздних версиях задайте свойство [TreeView.ItemTemplate](/uwp/api/windows.ui.xaml.controls.treeview.itemtemplate).

### <a name="item-container-style"></a>Стиль контейнера элементов

При использовании **ItemsSource** или **RootNodes** фактические элементы, участвующие в отображении каждого узла (и называемые контейнерами), являются объектами [TreeViewItem](/uwp/api/windows.ui.xaml.controls.treeviewitem). Стиль контейнера можно задать с помощью свойств [ItemContainerStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.treeview.itemcontainerstyle) или [ItemContainerStyleSelector](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.treeview.itemcontainerstyleselector) представления **TreeView**.

### <a name="item-template-selectors"></a>Селектор шаблонов элементов

Вы можете задать разные шаблоны данных **DataTemplate** для элементов представления в виде дерева на основе типа элемента. Например, в приложении проводника можно использовать один шаблон данных для папок, а другой для файлов.

![Папки и файлы, использующие различные шаблоны данных](images/treeview-icons.png)

Здесь продемонстрирован пример создания и использования селектора шаблонов элементов.

```xaml
<Page.Resources>
    <DataTemplate x:Key="FolderTemplate" x:DataType="local:ExplorerItem">
        <muxc:TreeViewItem ItemsSource="{x:Bind Children}">
            <StackPanel Orientation="Horizontal">
                <Image Width="20" Source="Assets/folder.png"/>
                <TextBlock Text="{x:Bind Name}" />
            </StackPanel>
        </muxc:TreeViewItem>
    </DataTemplate>

    <DataTemplate x:Key="FileTemplate" x:DataType="local:ExplorerItem">
        <muxc:TreeViewItem>
            <StackPanel Orientation="Horizontal">
                <Image Width="20" Source="Assets/file.png"/>
                <TextBlock Text="{Binding Name}"/>
            </StackPanel>
        </muxc:TreeViewItem>
    </DataTemplate>

    <local:ExplorerItemTemplateSelector
            x:Key="ExplorerItemTemplateSelector"
            FolderTemplate="{StaticResource FolderTemplate}"
            FileTemplate="{StaticResource FileTemplate}" />
</Page.Resources>

<Grid>
    <muxc:TreeView
        ItemsSource="{x:Bind DataSource}"
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

> [!NOTE]
> Эти фрагменты кода являются частью большего примера и не работают по отдельности. Весь пример доступен в [репозитории Xaml-Controls-Gallery](https://github.com/microsoft/Xaml-Controls-Gallery) на сайте GitHub. Файлы [TreeViewPage.xaml](https://github.com/microsoft/Xaml-Controls-Gallery/blob/1ecd85c908a8a1cb9a8201e548f58db379801e69/XamlControlsGallery/ControlPages/TreeViewPage.xaml) и [TreeViewPage.xaml.cs](https://github.com/Microsoft/Xaml-Controls-Gallery/blob/1ecd85c908a8a1cb9a8201e548f58db379801e69/XamlControlsGallery/ControlPages/TreeViewPage.xaml.cs) содержат соответствующий код.

## <a name="interacting-with-a-tree-view"></a>Взаимодействие с представлением в виде дерева

Вы можете настроить представление в виде дерева, чтобы предоставить пользователю возможность взаимодействовать с ним разными способами:

- развертывание и свертывание узлов;
- выбор одного или нескольких элементов;
- выполнение щелчка для вызова элемента;

### <a name="expandcollapse"></a>развертывание или свертывание.

Любой узел представления в виде дерева, у которого есть дочерние элементы, можно развернуть или свернуть, щелкнув глиф развертывания/свертывания. Можно также развернуть или свернуть узел программными средствами и отреагировать при изменении состояния узла.

#### <a name="expandcollapse-a-node-programmatically"></a>Развертывание/свертывание узла программным способом

Существует два способа развертывания или свертывания узла представления в виде дерева в коде.

- У класса [TreeView](/uwp/api/windows.ui.xaml.controls.treeview) есть методы [Collapse](/uwp/api/windows.ui.xaml.controls.treeview.collapse) и [Expand](/uwp/api/windows.ui.xaml.controls.treeview.expand). При вызове этих методов выполняется передача в **TreeViewNode**, который требуется развернуть или свернуть.

- Каждый элемент [TreeViewNode](/uwp/api/windows.ui.xaml.controls.treeviewnode) имеет свойство [IsExpanded](/uwp/api/windows.ui.xaml.controls.treeviewnode.isexpanded). Это свойство можно использовать для проверки состояния узла или настроить его для изменения состояния. Вы также можете задать это свойство в XAML для настройки начального состояния узла.

### <a name="fill-a-node-when-its-expanding"></a>Заполнение узла при его развертывании

Возможно, вам потребуется отобразить большое число узлов в вашем представлении в виде дерева. Кроме того, вам может быть неизвестно заранее, сколько узлов будет туда входить. Элемент управления **TreeView** не виртуализирован, поэтому вы можете управлять ресурсами, заполняя каждый узел, когда он развернут, и удаляя дочерние узлы, когда они свернуты.

Выполните обработку события [Expanding](/uwp/api/windows.ui.xaml.controls.treeview.expand) и используйте свойство [HasUnrealizedChildren](/uwp/api/windows.ui.xaml.controls.treeviewnode.hasunrealizedchildren) для добавления дочерних элементов в узел, когда он развернут. Свойство **HasUnrealizedChildren** указывает на то, требуется ли заполнить узел или была ли уже заполнена его коллекция **Children**. Важно помнить, что элемент **TreeViewNode** не задает это значение. Им необходимо управлять в коде приложения.

Ниже приведен пример использования этих API. Чтобы ознакомиться с контекстом, включая реализацию **FillTreeNode**, см. полный пример кода в конце этой статьи.

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

Это необязательно, однако можно также обработать событие [Collapsed](/uwp/api/windows.ui.xaml.controls.treeview.collapsed) и удалить дочерние узлы, когда родительский узел закрыт. Это может быть важно, если представление в виде дерева содержит много узлов, или в том случае, если данные узла используют много ресурсов. Следует учитывать влияние на производительность при заполнении узла при каждом его открытии по сравнению с тем, когда дочерние элементы остаются в закрытом узле. Оптимальный вариант будет зависеть от вашего приложения.

Здесь продемонстрирован пример обработчика событий **Collapsed**.

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

Пользователь может вызывать действие (используя элемент в качестве кнопки) вместо выбора элемента. Обработка события [ItemInvoked](/uwp/api/windows.ui.xaml.controls.treeview.iteminvoked) выполняется для обеспечения отклика на это действие пользователя.

> [!NOTE]
> В отличие от элемента **ListView**, у которого есть свойство [IsItemClickEnabled](/uwp/api/windows.ui.xaml.controls.listviewbase.isitemclickenabled), вызов элемента всегда включен в представлении в виде дерева. Вы по-прежнему можете выбрать, следует ли обрабатывать событие.

**Класс [TreeViewItemInvokedEventArgs](/uwp/api/windows.ui.xaml.controls.treeviewiteminvokedeventargs)**

Аргументы события **ItemInvoked** предоставляют доступ к вызываемому элементу. У свойства [InvokedItem](/uwp/api/windows.ui.xaml.controls.treeviewiteminvokedeventargs.invokeditem) есть вызванный узел. Можно передать его элементу **TreeViewNode** и получить элемент данных из свойства **TreeViewNode.Content**.

Ниже приведен пример обработчика событий **ItemInvoked**. Элементом данных является [IStorageItem](/uwp/api/windows.storage.istorageitem), и в этом примере представлены только некоторые сведения о файле и дереве. Кроме того, если узел является узлом папки, наряду с этим он развертывает или свертывает узел. В противном случае узел развертывается или свертывается, только если щелкнуть значок шеврона.

```csharp
private void SampleTreeView_ItemInvoked(muxc.TreeView sender, muxc.TreeViewItemInvokedEventArgs args)
{
    var node = args.InvokedItem as muxc.TreeViewNode;
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

Элемент управления **TreeView** поддерживает выбор как одного, так и нескольких элементов. По умолчанию выбор узлов отключен, но его можно разрешить, задав свойство [TreeView.SelectionMode](/uwp/api/windows.ui.xaml.controls.treeview.selectionmode). Значениями [TreeViewSelectionMode](/uwp/api/windows.ui.xaml.controls.treeviewselectionmode) являются **None**, **Single** и **Multiple**.

#### <a name="multiple-selection"></a>Выбор нескольких элементов

Когда возможность выбора нескольких элементов включена, рядом с каждым узлом представления в виде дерева отображается флажок, а выбранные элементы выделены. Пользователь может выбрать элемент или отменить его выбор, используя данный флажок. Щелчок элемента будет приводить к его вызову.

Выбор или отмена выбора родительского узла приводит к выбору или отмене выбора всех дочерних элементов этого узла. Если выбраны не все дочерние элементы под родительским узлом, флажок для родительского узла отображается как неопределенный (в виде черного квадрата).

![Выбор нескольких элементов в представлении в виде дерева](images/treeview-selection.png)

Выбранные узлы добавляются в коллекцию [SelectedNodes](/uwp/api/windows.ui.xaml.controls.treeview.selectednodes) представления в виде дерева. Вы можете вызвать метод [SelectAll](/uwp/api/windows.ui.xaml.controls.treeview.selectall), чтобы выбрать все узлы в представлении в виде дерева.

> [!NOTE]
> При вызове метода **SelectAll** выбираются все реализованные узлы, независимо от значения **SelectionMode**. Чтобы обеспечить согласованное взаимодействие пользователя с интерфейсом, следует вызывать метод **SelectAll**, только если для **SelectionMode** выбрано значение **Multiple**.

#### <a name="selection-and-realizedunrealized-nodes"></a>Выбор и реализованные/нереализованные узлы

Если в представлении в виде дерева есть нереализованные узлы, они не учитываются при выборе. Вот что нужно помнить о выборе элементов при наличии нереализованных узлов.

- Если пользователь выбирает родительский узел, выбираются также все принадлежащие ему реализованные дочерние элементы. Аналогичным образом, при выборе всех дочерних узлов также выбирается родительский узел.
- Метод **SelectAll** добавляет только реализованные узлы в коллекцию **SelectedNodes**.
- При выборе родительского узла с нереализованными дочерними элементами они будут выбраны после их реализации.

## <a name="code-examples"></a>Примеры кода

В следующих примерах кода демонстрируют различные элементы управления представления в виде дерева.

### <a name="tree-view-using-xaml"></a>Представление в виде дерева с использованием XAML

В этом примере показано, как создать простое представление в виде дерева в XAML. Представление в виде дерева содержит вкусы мороженого и соответствующие поливки для него, среди которых пользователь может выбирать. Они распределены по категориям. Включена возможность выбора нескольких элементов, и когда пользователь нажимает кнопку, в пользовательском интерфейсе основного приложения отображаются выбранные элементы.

```xaml
<Page
    x:Class="TreeViewTest.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    xmlns:muxc="using:Microsoft.UI.Xaml.Controls"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"
          Padding="100">
        <SplitView IsPaneOpen="True"
               DisplayMode="Inline"
               OpenPaneLength="296">
            <SplitView.Pane>
                <muxc:TreeView x:Name="DessertTree" SelectionMode="Multiple">
                    <muxc:TreeView.RootNodes>
                        <muxc:TreeViewNode Content="Flavors" IsExpanded="True">
                            <muxc:TreeViewNode.Children>
                                <muxc:TreeViewNode Content="Vanilla"/>
                                <muxc:TreeViewNode Content="Strawberry"/>
                                <muxc:TreeViewNode Content="Chocolate"/>
                            </muxc:TreeViewNode.Children>
                        </muxc:TreeViewNode>

                        <muxc:TreeViewNode Content="Toppings">
                            <muxc:TreeViewNode.Children>
                                <muxc:TreeViewNode Content="Candy">
                                    <muxc:TreeViewNode.Children>
                                        <muxc:TreeViewNode Content="Chocolate"/>
                                        <muxc:TreeViewNode Content="Mint"/>
                                        <muxc:TreeViewNode Content="Sprinkles"/>
                                    </muxc:TreeViewNode.Children>
                                </muxc:TreeViewNode>
                                <muxc:TreeViewNode Content="Fruits">
                                    <muxc:TreeViewNode.Children>
                                        <muxc:TreeViewNode Content="Mango"/>
                                        <muxc:TreeViewNode Content="Peach"/>
                                        <muxc:TreeViewNode Content="Kiwi"/>
                                    </muxc:TreeViewNode.Children>
                                </muxc:TreeViewNode>
                                <muxc:TreeViewNode Content="Berries">
                                    <muxc:TreeViewNode.Children>
                                        <muxc:TreeViewNode Content="Strawberry"/>
                                        <muxc:TreeViewNode Content="Blueberry"/>
                                        <muxc:TreeViewNode Content="Blackberry"/>
                                    </muxc:TreeViewNode.Children>
                                </muxc:TreeViewNode>
                            </muxc:TreeViewNode.Children>
                        </muxc:TreeViewNode>
                    </muxc:TreeView.RootNodes>
                </muxc:TreeView>
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
</Page>
```

```csharp
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using muxc = Microsoft.UI.Xaml.Controls;

namespace TreeViewTest
{
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
        }

        private void OrderButton_Click(object sender, RoutedEventArgs e)
        {
            FlavorList.Text = string.Empty;
            ToppingList.Text = string.Empty;

            foreach (muxc.TreeViewNode node in DessertTree.SelectedNodes)
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
            if (DessertTree.SelectionMode == muxc.TreeViewSelectionMode.Multiple)
            {
                DessertTree.SelectAll();
            }
        }
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

### <a name="tree-view-using-data-binding"></a>Представление в виде дерева с использованием привязки данных

Этот пример демонстрирует, как создать такое же представление в виде дерева, как и в прошлом примере. Но, вместо создания иерархии данных в XAML, данные создаются в коде и привязываются к свойству **ItemsSource** представления в виде дерева. (Продемонстрированные в предыдущем примере обработчики событий кнопки также применяются в данном примере.)

```xaml
<Page
    x:Class="TreeViewTest.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    xmlns:muxc="using:Microsoft.UI.Xaml.Controls"
    xmlns:local="using:TreeViewTest"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"
          Padding="100">
        <SplitView IsPaneOpen="True"
                   DisplayMode="Inline"
                   OpenPaneLength="296">
            <SplitView.Pane>
                <muxc:TreeView Name="DessertTree"
                                      SelectionMode="Multiple"
                                      ItemsSource="{x:Bind DataSource}">
                    <muxc:TreeView.ItemTemplate>
                        <DataTemplate x:DataType="local:Item">
                            <muxc:TreeViewItem
                                ItemsSource="{x:Bind Children}"
                                Content="{x:Bind Name}"/>
                        </DataTemplate>
                    </muxc:TreeView.ItemTemplate>
                </muxc:TreeView>
            </SplitView.Pane>

            <StackPanel Grid.Column="1" Margin="12,0">
                <Button Content="Select all"
                        Click="SelectAllButton_Click"/>
                <Button Content="Create order"
                        Click="OrderButton_Click"
                        Margin="0,12"/>
                <TextBlock Text="Your flavor selections:"
                           Style="{StaticResource CaptionTextBlockStyle}"/>
                <TextBlock x:Name="FlavorList" Margin="0,0,0,12"/>
                <TextBlock Text="Your topping selections:"
                           Style="{StaticResource CaptionTextBlockStyle}"/>
                <TextBlock x:Name="ToppingList"/>
            </StackPanel>
        </SplitView>
    </Grid>

</Page>
```

```csharp
using System.Collections.ObjectModel;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using muxc = Microsoft.UI.Xaml.Controls;

namespace TreeViewTest
{
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

        private void OrderButton_Click(object sender, RoutedEventArgs e)
        {
            FlavorList.Text = string.Empty;
            ToppingList.Text = string.Empty;

            foreach (muxc.TreeViewNode node in DessertTree.SelectedNodes)
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
            if (DessertTree.SelectionMode == muxc.TreeViewSelectionMode.Multiple)
            {
                DessertTree.SelectAll();
            }
        }
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
}

```

### <a name="pictures-and-music-library-tree-view"></a>Представление в виде дерева библиотеки изображений и музыкальных файлов

В этом примере показано, как создать представление в виде дерева, отображающее содержимое и структуру библиотек пользователя **Pictures** и **Music**. Число элементов не может быть известно заранее, поэтому каждый узел заполняется при его развертывании и очищается при его свертывании.

Пользовательский шаблон элементов используется для отображения элементов данных, которые относятся к типу [IStorageItem](/uwp/api/windows.storage.istorageitem).

> [!IMPORTANT]
> В этом примере кода требуются возможности **picturesLibrary** и **musicLibrary**. Дополнительные сведения о доступе к файлам см. в статьях [Разрешения на доступ к файлам](../../files/file-access-permissions.md), [Перечисление и запрос папок и файлов](../../files/quickstart-listing-files-and-folders.md) и [Файлы и папки в библиотеках музыки, изображений и видео](../../files/quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md).

```xaml
<Page
    x:Class="TreeViewTest.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:TreeViewTest"
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
using System;
using System.Collections.Generic;
using Windows.Storage;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

namespace TreeViewTest
{
    public sealed partial class MainPage : Page
    {
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
    }
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

### <a name="drag-and-drop-items-between-tree-views"></a>Операции перетаскивания между представлениями в виде дерева.

В следующем примере демонстрируется, как создать два представления дерева, элементы которых можно перетаскивать из дерева в дерево. Перетаскиваемый в другое представление дерева элемент добавляется в конец списка. При этом элементы можно упорядочить, используя представление в виде дерева. В этом примере показаны представления в виде дерева с одним корневым узлом.

```xaml
<Page
    x:Class="TreeViewTest.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>

        <TreeView x:Name="treeView1"
                  AllowDrop="True"
                  CanDragItems="True"
                  CanReorderItems="True"
                  DragOver="TreeView_DragOver"
                  Drop="TreeView_Drop"
                  DragItemsStarting="TreeView_DragItemsStarting"
                  DragItemsCompleted="TreeView_DragItemsCompleted"/>
        <TreeView x:Name="treeView2"
                  AllowDrop="True"
                  Grid.Column="1"
                  CanDragItems="True"
                  CanReorderItems="True"
                  DragOver="TreeView_DragOver"
                  Drop="TreeView_Drop"
                  DragItemsStarting="TreeView_DragItemsStarting"
                  DragItemsCompleted="TreeView_DragItemsCompleted"/>

    </Grid>

</Page>
```

```cs
using System;
using Windows.ApplicationModel.DataTransfer;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

namespace TreeViewTest
{
    public sealed partial class MainPage : Page
    {
        private TreeViewNode deletedItem;
        private TreeView sourceTreeView;

        public MainPage()
        {
            this.InitializeComponent();
            InitializeTreeView();
        }

        private void InitializeTreeView()
        {
            TreeViewNode parentNode1 = new TreeViewNode() { Content = "tv1" };
            TreeViewNode parentNode2 = new TreeViewNode() { Content = "tv2" };

            parentNode1.Children.Add(new TreeViewNode() { Content = "tv1FirstChild" });
            parentNode1.Children.Add(new TreeViewNode() { Content = "tv1SecondChild" });
            parentNode1.Children.Add(new TreeViewNode() { Content = "tv1ThirdChild" });
            parentNode1.Children.Add(new TreeViewNode() { Content = "tv1FourthChild" });
            parentNode1.IsExpanded = true;
            treeView1.RootNodes.Add(parentNode1);

            parentNode2.Children.Add(new TreeViewNode() { Content = "tv2FirstChild" });
            parentNode2.Children.Add(new TreeViewNode() { Content = "tv2SecondChild" });
            parentNode2.IsExpanded = true;
            treeView2.RootNodes.Add(parentNode2);
        }

        private void TreeView_DragOver(object sender, DragEventArgs e)
        {
            if (e.DataView.Contains(StandardDataFormats.Text))
            {
                e.AcceptedOperation = DataPackageOperation.Move;
            }
        }

        private async void TreeView_Drop(object sender, DragEventArgs e)
        {
            if (e.DataView.Contains(StandardDataFormats.Text))
            {
                string text = await e.DataView.GetTextAsync();
                TreeView destinationTreeView = sender as TreeView;

                if (destinationTreeView.RootNodes != null)
                {
                    TreeViewNode newNode = new TreeViewNode() { Content = text };
                    destinationTreeView.RootNodes[0].Children.Add(newNode);
                    deletedItem = newNode;
                }
            }
        }

        private void TreeView_DragItemsStarting(TreeView sender, TreeViewDragItemsStartingEventArgs args)
        {
            if (args.Items.Count == 1)
            {
                args.Data.RequestedOperation = DataPackageOperation.Move;
                sourceTreeView = sender;

                foreach (var item in args.Items)
                {
                    args.Data.SetText(item.ToString());
                }
            }
        }

        private void TreeView_DragItemsCompleted(TreeView sender, TreeViewDragItemsCompletedEventArgs args)
        {
            var children = sourceTreeView.RootNodes[0].Children;

            if (deletedItem != null)
            {
                for (int i = 0; i < children.Count; i++)
                {
                    if (children[i].Content.ToString() == deletedItem.Content.ToString())
                    {
                        children.RemoveAt(i);
                        break;
                    }
                }
            }

            sourceTreeView = null;
            deletedItem = null;
        }
    }
}
```

## <a name="related-articles"></a>Связанные статьи

- [Класс TreeView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.treeview)
- [Класс ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview)
- [Представления списка и сетки](listview-and-gridview.md)
