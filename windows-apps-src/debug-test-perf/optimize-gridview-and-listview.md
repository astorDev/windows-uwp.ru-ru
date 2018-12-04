---
ms.assetid: 26DF15E8-2C05-4174-A714-7DF2E8273D32
title: Оптимизация пользовательского интерфейса ListView и GridView
description: Повысить производительность и время запуска ListView и GridView можно с помощью виртуализации пользовательского интерфейса, сокращения элементов и прогрессивного обновления элементов.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 5f8ddbdd1e8079e4b5bf945455bfa2efe7094203
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8476899"
---
# <a name="listview-and-gridview-ui-optimization"></a>Оптимизация пользовательского интерфейса ListView и GridView


**Примечание**  Дополнительные сведения см. в статье сеансов //build/ [Резкое повышение производительности при взаимодействии пользователей с большим объемом данных, в GridView и ListView](https://channel9.msdn.com/events/build/2013/3-158).

Повысить производительность и время запуска [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) и [**GridView**](https://msdn.microsoft.com/library/windows/apps/BR242705) можно с помощью виртуализации пользовательского интерфейса, сокращения элементов и прогрессивного обновления элементов. Подробнее о методах виртуализации данных см. [Виртуализация данных ListView и GridView](listview-and-gridview-data-optimization.md).

## <a name="two-key-factors-in-collection-performance"></a>Два ключевых фактора, влияющих на производительность коллекций

Управление коллекциями — распространенный сценарий. Средство просмотра фотографий содержит коллекции фотографий, средство чтения содержит коллекции статей, книг или историй, а приложение для покупок содержит коллекции продуктов. В этом разделе показано, что нужно сделать для эффективного управления коллекциями в приложении.

Существует два ключевых фактора производительности с точки зрения коллекций: время, которое поток пользовательского интерфейса тратит на создание элементов; и память, которая используется как набором необработанных данных, так и элементами пользовательского интерфейса, которые применяются для обработки этих данных.

Для плавного сдвига и прокрутки очень важно, чтобы поток пользовательского интерфейса эффективно и продуманно выполнял такие задачи, как создание экземпляра, привязка данных и расположение элементов.

## <a name="ui-virtualization"></a>Виртуализация пользовательского интерфейса

Виртуализация пользовательского интерфейса — это самое важное улучшение, которое можно сделать. Это означает, что элементы пользовательского интерфейса, представляющие элементы, создаются по запросу. Для элемента управления элементами, привязанного к коллекции из 1000 элементов, создание пользовательского интерфейса одновременно для всех элементов было бы напрасной тратой ресурсов, поскольку они не могут отображаться одновременно. [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) и [**GridView**](https://msdn.microsoft.com/library/windows/apps/BR242705) (и другие стандартные элементы управления, производные от [**ItemsControl**](https://msdn.microsoft.com/library/windows/apps/BR242803)) выполняют виртуализацию пользовательского интерфейса за вас. Если элементы близки к появлению на экране (за несколько страниц), платформа создает пользовательский интерфейс для таких элементов и помещает их в кэш. Если повторное появление элементов маловероятно, платформа освобождает память.

Если вы задали пользовательский шаблон панели элементов (см. [**ItemsPanel**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemspanel.aspx)), следует использовать панель виртуализации, например [**ItemsWrapGrid**](https://msdn.microsoft.com/library/windows/apps/Dn298849) или [**ItemsStackPanel**](https://msdn.microsoft.com/library/windows/apps/Dn298795). Если вы используете [**VariableSizedWrapGrid**](https://msdn.microsoft.com/library/windows/apps/BR227651), [**WrapGrid**](https://msdn.microsoft.com/library/windows/apps/BR227717) или [**StackPanel**](https://msdn.microsoft.com/library/windows/apps/BR209635), то виртуализация не будет выполнена. Кроме того, следующие события [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) вызываются только при использовании элементов [**ItemsWrapGrid**](https://msdn.microsoft.com/library/windows/apps/Dn298849) или [**ItemsStackPanel**](https://msdn.microsoft.com/library/windows/apps/Dn298795): [**ChoosingGroupHeaderContainer**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.choosinggroupheadercontainer), [**ChoosingItemContainer**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.choosingitemcontainer) и [**ContainerContentChanging**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.containercontentchanging).

Концепция окна просмотра очень важна для виртуализации пользовательского интерфейса, так как среда разработки должна создавать элементы с высокой вероятностью отображения. Как правило, окно просмотра [**ItemsControl**](https://msdn.microsoft.com/library/windows/apps/BR242803) того же размера, что и логический элемент управления. Например, окно просмотра элемента управления [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) имеет те же ширину и высоту, что и элемент **ListView**. Некоторые панели предоставляют дочерним элементам неограниченное пространство (например, [**ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/BR209527) и [**Grid**](https://msdn.microsoft.com/library/windows/apps/BR242704)) с автоматически устанавливаемым размером строк и столбцов. Когда виртуализированный элемент управления **ItemsControl** помещается на такую панель, нужно достаточно места для отображения всех его элементов, что препятствует виртуализации. Восстановите виртуализацию, задав ширину и высоту в **ItemsControl**.

## <a name="element-reduction-per-item"></a>Уменьшение элементов для каждого элемента

Сведите к минимуму число элементов пользовательского интерфейса, используемых для обработки элементов.

При первом появлении элемента управления элементами создаются все элементы, необходимые для обработки окна просмотра с множеством элементов. Кроме того, по мере поступления элементов в окно просмотра платформа обновляет элементы пользовательского интерфейса в помещенных в кэш шаблонах элементов с привязанными объектами данных. Упрощение разметки внутри шаблонов оправдывается памятью и временем, потраченным на поток пользовательского интерфейса, что улучшает отклик, особенно во время сдвига и прокрутки. Рассматриваемые шаблоны — это шаблон элемента (см. [**ItemTemplate**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx)) и шаблон элемента управления [**ListViewItem**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewitem.aspx) или [**GridViewItem**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.gridviewitem.aspx) (шаблон элемента управления элементами или [**ItemContainerStyle**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle)). Преимущество даже небольшого сокращения числа элементов умножается на число отображаемых элементов.

Примеры сокращения элементов см. в разделе [Оптимизация разметки XAML](optimize-xaml-loading.md).

Шаблоны элементов управления по умолчанию для [**ListViewItem**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewitem.aspx) и [**GridViewItem**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.gridviewitem.aspx) содержат элемент [**ListViewItemPresenter**](https://msdn.microsoft.com/library/windows/apps/Dn298500). Этот обработчик — это отдельный оптимизированный элемент, который отображает сложные визуальные элементы для фокуса, выбора и других визуальных состояний. Если у вас уже есть пользовательские шаблоны элемента управления элементами ([**ItemContainerStyle**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.itemscontrol.itemcontainerstyle)) или если в будущем вы измените копию шаблона элемента управления элементами, рекомендуется использовать **ListViewItemPresenter** или GridViewItemPresenter, поскольку эти элементы обеспечивают оптимальный баланс производительности и возможности настройки в большинстве случаев. Этот обработчик можно настроить, задав соответствующие свойства. Например, возьмем фрагмент разметки, снимающий флажок, который появляется по умолчанию при выборе элемента и меняет фоновый цвет выбранного элемента на оранжевый.

```xml
...
<ListView>
 ...
 <ListView.ItemContainerStyle>
 <Style TargetType="ListViewItem">
 <Setter Property="Template">
 <Setter.Value>
 <ControlTemplate TargetType="ListViewItem">
 <ListViewItemPresenter SelectionCheckMarkVisualEnabled="False" SelectedBackground="Orange"/>
 </ControlTemplate>
 </Setter.Value>
 </Setter>
 </Style>
 </ListView.ItemContainerStyle>
</ListView>
<!-- ... -->
```

Существует около 25 свойств с описательными именами, такими как [**SelectionCheckMarkVisualEnabled**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.listviewitempresenter.selectioncheckmarkvisualenabled.aspx) и [**SelectedBackground**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.listviewitempresenter.selectedbackground.aspx). Если типы обработчика недостаточно поддаются настройке для вашего варианта использования, вы можете изменить копию шаблона элемента управления `ListViewItemExpanded` или `GridViewItemExpanded`. Их можно найти в `\Program Files (x86)\Windows Kits\10\DesignTime\CommonConfiguration\Neutral\UAP\<version>\Generic\generic.xaml`. Помните, что использовать эти шаблоны — значит в некоторой степени пожертвовать производительностью ради повышения возможностей настройки.

<span id="update-items-incrementally"/>

## <a name="update-listview-and-gridview-items-progressively"></a>Прогрессивное обновление элементов ListView и GridView

Если вы используете виртуализацию данных, то можете сохранить быстрый отклик [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) и [**GridView**](https://msdn.microsoft.com/library/windows/apps/BR242705), настроив элемент управления для обработки временных элементов пользовательского интерфейса, чтобы продолжить их загрузку и скачивание. Временные элементы затем сообразно заменяются фактическим пользовательским интерфейсом по мере загрузки данных.

Кроме того, откуда бы вы ни загружали данные — с локального диска, из сети или облака, — пользователь может сдвигать или прокручивать [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) или [**GridView**](https://msdn.microsoft.com/library/windows/apps/BR242705) настолько быстро, что невозможно будет обработать каждый элемент с максимальным качеством, сохранив плавный сдвиг или прокрутку. Чтобы сохранить плавный сдвиг или прокрутку, можно выбрать обработку элемента в несколько этапов, используя еще и заполнители.

Пример таких методов часто встречается в приложениях для просмотра фотографий: хотя не все изображения загружены и отображены, пользователь может сдвигать или прокручивать объекты, а также взаимодействовать с коллекцией. Или, в случае элемента "видео", можно отобразить заголовок на первом этапе, оценку — на втором этапе, а изображение афиши — на третьем. Пользователь сможет увидеть наиболее важные данные о каждом элементе как можно раньше, а это означает, что он сможет сразу же предпринять действия. Затем заполняется менее важная информация, насколько позволяет время. Ниже приведены компоненты платформы, которые можно использовать для реализации этих методов.

### <a name="placeholders"></a>Заполнители

Функция визуальных элементов временных заполнителей включена по умолчанию и управляется свойством [**ShowsScrollingPlaceholders**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.showsscrollingplaceholders). Во время быстрого сдвига или прокрутки эта функция дает пользователю визуальную подсказку о том, что можно полностью отобразить большее число элементов, сохранив при этом плавность. При использовании одного из следующих методов можно установить для параметра **ShowsScrollingPlaceholders** значение «false», если вы не хотите, чтобы система обрабатывала заполнители.

**Прогрессивные обновления шаблонов данных с использованием x:Phase**

Вот как использовать [атрибут x:Phase](https://msdn.microsoft.com/library/windows/apps/Mt204790) с привязками [{x:Bind}](https://msdn.microsoft.com/library/windows/apps/Mt204783) для реализации прогрессивных обновлений шаблонов данных.

1.  Вот как выглядит источник привязки (это источник данных, к которому осуществляется привязка).

    ```csharp
    namespace LotsOfItems
    {
        public class ExampleItem
        {
            public string Title { get; set; }
            public string Subtitle { get; set; }
            public string Description { get; set; }
        }

        public class ExampleItemViewModel
        {
            private ObservableCollection<ExampleItem> exampleItems = new ObservableCollection<ExampleItem>();
            public ObservableCollection<ExampleItem> ExampleItems { get { return this.exampleItems; } }

            public ExampleItemViewModel()
            {
                for (int i = 1; i < 150000; i++)
                {
                    this.exampleItems.Add(new ExampleItem(){
                        Title = "Title: " + i.ToString(),
                        Subtitle = "Sub: " + i.ToString(),
                        Description = "Desc: " + i.ToString()
                    });
                }
            }
        }
    }
    ```
2.  Вот разметка, которая содержит `DeferMainPage.xaml`. Представление сетки содержит шаблон элемента с элементами, связанными со свойствами **Title**, **Subtitle** и **Description** класса **MyItem**. Обратите внимание, что по умолчанию для **x:Phase** задано значение 0. Здесь элементы будут изначально обрабатываться с отображением только заголовка. Затем элемент подзаголовка будет связан с данными и будет видимым для всех элементов и т. д., пока не будут пройдены все этапы.
    ```xml
    <Page
        x:Class="LotsOfItems.DeferMainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:lotsOfItems="using:LotsOfItems"
        mc:Ignorable="d">

        <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
            <GridView ItemsSource="{x:Bind ViewModel.ExampleItems}">
                <GridView.ItemTemplate>
                    <DataTemplate x:DataType="lotsOfItems:ExampleItem">
                        <StackPanel Height="100" Width="100" Background="OrangeRed">
                            <TextBlock Text="{x:Bind Title}"/>
                            <TextBlock Text="{x:Bind Subtitle}" x:Phase="1"/>
                            <TextBlock Text="{x:Bind Description}" x:Phase="2"/>
                        </StackPanel>
                    </DataTemplate>
                </GridView.ItemTemplate>
            </GridView>
        </Grid>
    </Page>
    ```

3.  Если теперь вы запустите приложение и выполните быстрый сдвиг или прокрутку в представлении сетки, то вы заметите, что когда на экране появляется новый элемент, сначала он отображается в виде темно-серого прямоугольника (благодаря свойству [**ShowsScrollingPlaceholders**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.showsscrollingplaceholders) с заданным по умолчанию значением **true**), затем отображается заголовок, а после — подзаголовок и описание.

**Прогрессивные обновления шаблонов данных с использованием ContainerContentChanging**

Общая стратегия для события [**ContainerContentChanging**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.containercontentchanging) состоит в использовании **Opacity** для скрытия элементов, которые не должны быть видимыми сразу. При повторном использовании элементов сохраняются старые значения, поэтому следует скрыть эти элементы, пока значения не будут обновлены из нового элемента данных. Мы используем свойство **Phase** в аргументах события, чтобы определить, какие элементы нужно обновить и отобразить. Если требуются дополнительные этапы, регистрируем обратный вызов.

1.  Мы будем использовать тот же источник привязки, что и для **x:Phase**.

2.  Вот разметка, которую содержит `MainPage.xaml`. Представление сетки объявляет обработчик событию [**ContainerContentChanging**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.containercontentchanging) и содержит шаблон элементов с элементами, используемыми для отображения свойств **Title**, **Subtitle** и **Description** класса **MyItem**. Чтобы получить максимальную производительность при использовании **ContainerContentChanging**, мы не используем привязки в разметке, а вместо этого назначаем значения программно. Исключением является элемент, отображающий заголовок, который предполагался на этапе 0.
    ```xml
    <Page
        x:Class="LotsOfItems.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:lotsOfItems="using:LotsOfItems"
        mc:Ignorable="d">

        <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
            <GridView ItemsSource="{x:Bind ViewModel.ExampleItems}" ContainerContentChanging="GridView_ContainerContentChanging">
                <GridView.ItemTemplate>
                    <DataTemplate x:DataType="lotsOfItems:ExampleItem">
                        <StackPanel Height="100" Width="100" Background="OrangeRed">
                            <TextBlock Text="{x:Bind Title}"/>
                            <TextBlock Opacity="0"/>
                            <TextBlock Opacity="0"/>
                        </StackPanel>
                    </DataTemplate>
                </GridView.ItemTemplate>
            </GridView>
        </Grid>
    </Page>
    ```
3.  И наконец, вот реализация обработчика событий [**ContainerContentChanging**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.containercontentchanging). Этот код также показывает добавление свойства типа **RecordingViewModel** на **MainPage** для предоставления класса источника привязки из класса, представляющего нашу страницу разметки. Если у вас нет привязок [{Binding}](https://msdn.microsoft.com/library/windows/apps/Mt204782) в шаблоне данных, пометьте объект аргументов события как обработанный на первом этапе обработчика, чтобы дать элементу подсказку о том, что не нужно устанавливать контекст данных.
    ```csharp
    namespace LotsOfItems
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            public MainPage()
            {
                this.InitializeComponent();
                this.ViewModel = new ExampleItemViewModel();
            }

            public ExampleItemViewModel ViewModel { get; set; }

            // Display each item incrementally to improve performance.
            private void GridView_ContainerContentChanging(ListViewBase sender, ContainerContentChangingEventArgs args)
            {
                if (args.Phase != 0)
                {
                    throw new System.Exception("We should be in phase 0, but we are not.");
                }

                // It's phase 0, so this item's title will already be bound and displayed.

                args.RegisterUpdateCallback(this.ShowSubtitle);

                args.Handled = true;
            }

            private void ShowSubtitle(ListViewBase sender, ContainerContentChangingEventArgs args)
            {
                if (args.Phase != 1)
                {
                    throw new System.Exception("We should be in phase 1, but we are not.");
                }

                // It's phase 1, so show this item's subtitle.
                var templateRoot = args.ItemContainer.ContentTemplateRoot as StackPanel;
                var textBlock = templateRoot.Children[1] as TextBlock;
                textBlock.Text = (args.Item as ExampleItem).Subtitle;
                textBlock.Opacity = 1;

                args.RegisterUpdateCallback(this.ShowDescription);
            }

            private void ShowDescription(ListViewBase sender, ContainerContentChangingEventArgs args)
            {
                if (args.Phase != 2)
                {
                    throw new System.Exception("We should be in phase 2, but we are not.");
                }

                // It's phase 2, so show this item's description.
                var templateRoot = args.ItemContainer.ContentTemplateRoot as StackPanel;
                var textBlock = templateRoot.Children[2] as TextBlock;
                textBlock.Text = (args.Item as ExampleItem).Description;
                textBlock.Opacity = 1;
            }
        }
    }
    ```

4.  Если вы теперь запустите приложение и выполните быстрый сдвиг или прокрутку в представлении сетки, то увидите такое же поведение, как и для **x:Phase**.

## <a name="container-recycling-with-heterogeneous-collections"></a>Повторное использование контейнеров с неоднородными коллекциями

В некоторых приложениях необходим отдельный пользовательский интерфейс для разных типов элементов в коллекции. Это может создать ситуацию, в которой невозможно виртуализировать панели для повторного использования визуальных элементов, используемых для отображения элементов. Повторное создание визуальных элементов для элемента при сдвиге во многом сводит на нет выигрыш производительности, полученный за счет виртуализации. Однако надлежащее планирование может разрешить панелям виртуализации повторное использование элементов. У разработчиков есть два варианта действий в зависимости от сценария: событие [**ChoosingItemContainer**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.choosingitemcontainer) или селектор шаблонов элементов. Подход с использованием контейнера **ChoosingItemContainer** обеспечивает более высокую производительность.

**Событие ChoosingItemContainer**

[**ChoosingItemContainer**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.choosingitemcontainer)— это событие, которое позволит предоставить элемент (**ListViewItem**/**GridViewItem**) для [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878)/[**GridView**](https://msdn.microsoft.com/library/windows/apps/BR242705) при необходимости создания элемента в ходе запуска или повторного использования. Вы можете создать контейнер в зависимости от типа элемента данных, который этот контейнер будет отображать (как в примере ниже). **ChoosingItemContainer** — это более производительный способ использования разных шаблонов данных для разных элементов. Кэширование контейнера можно обеспечить при помощи **ChoosingItemContainer**. Например, если вы используете пять разных шаблонов и один из них — на порядок чаще других, ChoosingItemContainer позволяет не только создавать элементы в нужных соотношениях, но и сохранять нужное количество помещенных в кэш и доступных для повторного использования элементов. [**ChoosingGroupHeaderContainer**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.choosinggroupheadercontainer) обеспечивает такую же функциональность для заголовков групп.

```csharp
// Example shows how to use ChoosingItemContainer to return the correct
// DataTemplate when one is available. This example shows how to return different 
// data templates based on the type of FileItem. Available ListViewItems are kept
// in two separate lists based on the type of DataTemplate needed.
private void ListView_ChoosingItemContainer
    (ListViewBase sender, ChoosingItemContainerEventArgs args)
{
    // Determines type of FileItem from the item passed in.
    bool special = args.Item is DifferentFileItem;

    // Uses the Tag property to keep track of whether a particular ListViewItem's 
    // datatemplate should be a simple or a special one.
    string tag = special ? "specialFiles" : "simpleFiles";

    // Based on the type of datatemplate needed return the correct list of 
    // ListViewItems, this could have also been handled with a hash table. These 
    // two lists are being used to keep track of ItemContainers that can be reused.
    List<UIElement> relevantStorage = special ? specialFileItemTrees : simpleFileItemTrees;

    // args.ItemContainer is used to indicate whether the ListView is proposing an 
    // ItemContainer (ListViewItem) to use. If args.Itemcontainer, then there was a 
    // recycled ItemContainer available to be reused.
    if (args.ItemContainer != null)
    {
        // The Tag is being used to determine whether this is a special file or 
        // a simple file.
        if (args.ItemContainer.Tag.Equals(tag))
        {
            // Great: the system suggested a container that is actually going to 
            // work well.
        }
        else
        {
            // the ItemContainer's datatemplate does not match the needed 
            // datatemplate.
            args.ItemContainer = null;
        }
    }

    if (args.ItemContainer == null)
    {
        // see if we can fetch from the correct list.
        if (relevantStorage.Count > 0)
        {
            args.ItemContainer = relevantStorage[0] as SelectorItem;
        }
        else
        {
            // there aren't any (recycled) ItemContainers available. So a new one 
            // needs to be created.
            ListViewItem item = new ListViewItem();
            item.ContentTemplate = this.Resources[tag] as DataTemplate;
            item.Tag = tag;
            args.ItemContainer = item;
        }
    }
}
```

**Селектор шаблонов элементов**

Селектор шаблонов элементов ([**DataTemplateSelector**](https://msdn.microsoft.com/library/windows/apps/BR209469)) позволяет приложению вернуть другой шаблон элемента во время выполнения в зависимости от типа элемента данных, который будет отображен. Это повышает эффективность разработки, но усложняет виртуализацию пользовательского интерфейса, поскольку не каждый шаблон элемента можно использовать повторно для любого элемента данных.

При повторном использовании элемента (**** ListViewItem/** или **GridViewItem) платформа должна определить, имеют ли элементы, доступные для использования в "очереди повторного запуска" (очередь повторного запуска — это кэш элементов, которые в настоящее время не используются для отображения данных), шаблон элемента, который соответствует нужному для текущего элемента данных. Если в очереди повторного запуска нет элементов с подходящим шаблоном элемента, создается новый элемент, и для него создается экземпляр соответствующего шаблона элемента. Если, напротив, очередь повторного запуска содержит элемент с подходящим шаблоном элемента, этот элемент изымается из очереди повторного запуска и используется для текущего элемента данных. Селектор шаблонов элементов работает в ситуациях, когда используются всего несколько шаблонов элементов и распределение в коллекции элементов, использующих различные шаблоны элементов, плоское.

При неравномерном распределении элементов, использующих различные шаблоны элементов, вероятно, потребуется создать новые шаблоны элементов при сдвиге, что во многом сводит на нет выигрыш, полученный за счет виртуализации. Кроме того, селектор шаблонов элементов учитывает только пять вариантов при оценки возможности повторного использования конкретного контейнера для текущего элемента данных. Поэтому перед использованием селектора шаблонов элементов в приложении необходимо тщательно обдумать, подходят ли ваши данные для использования с таковым. Если ваша коллекция в основном однородна, то селектор возвращает один и тот же тип в большинстве случаев (возможно, во всех). Просто не забывайте о цене, которую вы платите за редкие исключения в этой однородности, и обдумайте, не лучше ли использовать [**ChoosingItemContainer**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.choosingitemcontainer) (или два элемента управления элементами).

 

