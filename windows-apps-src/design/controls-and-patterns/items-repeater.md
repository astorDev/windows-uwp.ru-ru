---
Description: ItemsRepeater — это небольшой элемент управления для создания и представления коллекции элементов.
title: ItemsRepeater
label: ItemsRepeater
template: detail.hbs
ms.date: 02/01/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 5782c6e9ba42fed07c2b1382f2d17b1d311d0a13
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80893464"
---
# <a name="itemsrepeater"></a>ItemsRepeater

Используйте [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) для создания взаимодействия настраиваемых коллекций с помощью системы гибкого макета, пользовательских представлений и виртуализации.

В отличие от [ListView](/uwp/api/windows.ui.xaml.controls.listview), [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) не обеспечивает комплексное взаимодействие с конечным пользователем — он не содержит стандартного пользовательского интерфейса и не предоставляет политику в отношении фокуса, выбора или взаимодействия с пользователем. Вместо этого он является стандартным блоком, который можно использовать для создания собственных уникальных интерфейсов, основанных на коллекции, и пользовательских элементов управления. Поскольку он не содержит встроенной политики, то предоставляет возможность подключения политики для создания необходимого взаимодействия. Вы можете определить, например, макет для использования, политику поддержки клавиатуры, политику выбора и т. д.

Концептуально [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) можно представить скорее как управляемую данными панель, чем как полное управление, подобное ListView. Вы указываете коллекцию отображаемых элементов данных, шаблон элемента, который создает элемент пользовательского интерфейса для каждого элемента данных, и макет, который определяет размер и положение элементов. Затем ItemsRepeater, на основе источника данных, создает дочерние элементы и отображает их в соответствии с шаблоном элемента и макетом. Поскольку ItemsRepeater может загрузить содержимое для представления элементов данных на основе критериев, указанных в селекторе шаблонов данных, отображаемые элементы не обязательно должны быть однородными.

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Элемент управления **ItemsRepeater** является частью библиотеки пользовательского интерфейса Windows, пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **API-интерфейсы библиотеки пользовательского интерфейса Windows:** [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)
>
> **API платформы:** [Класс ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), чтобы создать пользовательские представления коллекций данных. Хотя его можно использовать для предоставления базового набора элементов, Вы часто можете использовать его в качестве отображаемого элемента в шаблоне пользовательского элемента управления.

Если требуется, чтобы стандартный элемент управления отображал данные в списке или сетке с минимальной настройкой, рассмотрите возможность использования [ListView](/uwp/api/windows.ui.xaml.controls.listview) или [GridView](/uwp/api/windows.ui.xaml.controls.gridview).

ItemsRepeater не содержит встроенную коллекцию элементов. Если Вам нужно напрямую предоставить коллекцию элементов, вместо привязки к отдельному источнику данных, то скорее всего, Вам необходимо взаимодействие с более высоким уровнем политики. Поэтому используйте [ListView](/uwp/api/windows.ui.xaml.controls.listview) или [GridView](/uwp/api/windows.ui.xaml.controls.gridview).

[ItemsControl](/uwp/api/windows.ui.xaml.controls.itemscontrol) и ItemsRepeater оба позволяют создать настраиваемые взаимодействия с коллекцией, но ItemsRepeater, в отличии от ItemsControl поддерживает виртуализацию макетов пользовательского интерфейса. Мы рекомендуем использовать ItemsRepeater вместо ItemsControl, независимо от того, представляет он несколько элементов данных или создает настраиваемые элементы управления коллекции.

> [!NOTE]
> При возникновении ситуации, когда вы считаете, что вашим задачам соответствует ItemsControl, а не ItemsRepeater, оставьте свои отзывы на странице [Проект GitHub библиотека пользовательского интерфейса Windows](https://github.com/Microsoft/microsoft-ui-xaml/issues) и сообщите нам.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/ItemsRepeater">ItemsRepeater</a>в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="scrolling-with-itemsrepeater"></a>Прокрутка при помощи ItemsRepeater

[**ItemsRepeater**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) не является разработанным на основе [**элемента управления**](/uwp/api/windows.ui.xaml.controls.control), поэтому он не имеет шаблон элемента управления. Таким образом он не содержит любые встроенные режимы прокрутки, подобно ListView или другим элементам управления в коллекции.

При использовании **ItemsRepeater**, необходимо предоставить функциональные возможности прокрутки, переместив ее в элемент управления [**ScrollViewer**](/uwp/api/windows.ui.xaml.controls.scrollviewer).

> [!NOTE]
> Если приложение работает в более ранних версиях Windows, выпущенных *перед* Windows 10, версии 1809, вам нужно разместить **ScrollViewer** также внутри [**ItemsRepeaterScrollHost**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeaterscrollhost). 
> ```xaml
> <muxc:ItemsRepeaterScrollHost>
>     <ScrollViewer>
>         <muxc:ItemsRepeater ... />
>     </ScrollViewer>
> </muxc:ItemsRepeaterScrollHost>
> ```
> Если приложение работает только в последних версиях Windows 10 — версии 1809 и более поздней — нет необходимости в использовании [**ItemsRepeaterScrollHost**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeaterscrollhost).
>
> **ScrollViewer** для Windows 10, версия 1809, и предыдущих версий не внедрил интерфейс [**IScrollAnchorProvider**](/uwp/api/windows.ui.xaml.controls.iscrollanchorprovider), требуемый для **ItemsRepeater**.  **ItemsRepeaterScrollHost** позволяет **ItemsRepeater** координировать с **ScrollViewer** более ранние версии, чтобы правильно сохранить отображаемое расположение элементов при просмотре пользователем.  В противном случае элементы могут отображаться при перемещении, или внезапно исчезать после изменения элементов в списке или размера приложения.

## <a name="create-an-itemsrepeater"></a>Создание ItemsRepeater

Чтобы использовать [**ItemsRepeater**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), необходимо предоставить ему данные для отображения, задав свойство **ItemsSource**. Затем, определите порядок отображения элементов, задав свойство [**ItemTemplate**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemtemplate).

### <a name="itemssource"></a>ItemsSource

Чтобы заполнить представление, задайте свойство [**ItemsSource**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemssource) в коллекцию элементов данных. В этом коде **ItemsSource** задается непосредственно экземпляру коллекции.

```csharp
ObservableCollection<string> Items = new ObservableCollection<string>();

ItemsRepeater itemsRepeater1 = new ItemsRepeater();
itemsRepeater1.ItemsSource = Items;
```

Свойство **ItemsSource** можно также связать с коллекцией в среде XAML. См.сведения о [привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-quickstart).


```xaml
<ItemsRepeater ItemsSource="{x:Bind Items}"/>
```

### <a name="itemtemplate"></a>ItemTemplate
Чтобы указать, как визуализируются элемент данных, задайте свойство [**ItemTemplate**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemtemplate) в [**DataTemplate**](/uwp/api/windows.ui.xaml.datatemplate) или [**DataTemplateSelector**](/uwp/api/windows.ui.xaml.controls.datatemplateselector), определенный вами. Шаблон данных определяет способ визуализации данных. По умолчанию элемент данных отображается в представлении с **TextBlock**, который представляет объект данных в виде строки.

Тем не менее обычно требуется отобразить более широкое представление данных с помощью шаблона, который определяет макет и внешний вид одного или нескольких элементов управления, используемых для отображения отдельного элемента. Элементы управления, используемые в шаблоне, могут быть привязаны к свойствам объекта данных или иметь статическое содержимое, задаваемое внутри кода.

#### <a name="datatemplate"></a>DataTemplate
В этом примере объект данных является простой строкой. **DataTemplate** включает изображения слева от текста и стили **TextBlock** для отображения строки в сине-зеленом цвете.

> [!NOTE]
> При использовании [расширения разметки x:Bind](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) в **DataTemplate** необходимо задать DataType (`x:DataType`) в DataTemplate.

```xaml
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
```

Вот как будут выглядеть элементы при отображении с помощью **DataTemplate**.

![Элементы, отображаемые с помощью шаблона данных](images/listview-itemstemplate.png)

Число компонентов для элемента, используемое в **DataTemplate**, может иметь значительное влияние на производительность, если представление отображает большое количество элементов. Дополнительные сведения и примеры использования **DataTemplate**для определения внешнего вида элементов в списке см. в разделе [Контейнеры элементов и шаблоны](item-containers-templates.md).

> [!TIP]
> Для удобства при объявлении встроенного шаблона, вместо того, чтобы ссылаться на статический ресурс, **DataTemplate** или **DataTemplateSelector**можно указать как непосредственный дочерний элемент **ItemsRepeater**.  Он будет назначен в качестве значения свойства **ItemTemplate**. Например это действительно для:
> ```xaml
> <ItemsRepeater ItemsSource="{x:Bind Items}">
>     <DataTemplate>
>         <!-- ... -->
>     </DataTemplate>
> </ItemsRepeater>
> ```

> [!TIP]
> В отличие от **ListView** и других элементов управления коллекции, **ItemsRepeater** не переносит элементы из **DataTemplate** с помощью дополнительного контейнера элемента, который содержит стандартную политику, как например поля, заполнение, выбор визуальных элементов или указатель на визуальное состояние. Вместо этого **ItemsRepeater** отображает только данные, определенные в **DataTemplate**. Если требуется, чтобы элементы выглядели как элемент представления списка, можно явно включить контейнер в шаблон данных, как **ListViewItem**. **ItemsRepeater** отображает визуальные элементы **ListViewItem**, но не использует автоматически другие функции, такие как выбор или отображение нескольких флажков.
>
> Аналогично, если коллекция данных является коллекцией фактических элементов управления, на подобие **кнопки** (`List<Button>`),**ContentPresenter** можно поместить в ваш **DataTemplate**, чтобы отобразить элемент управления.

#### <a name="datatemplateselector"></a>DataTemplateSelector

Отображаемые в представлении элементы не обязательно должны быть одного типа. Свойство [**ItemTemplate**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemtemplate) можно предоставить с [**DataTemplateSelector**](/uwp/api/windows.ui.xaml.controls.datatemplateselector), чтобы выбрать разные **DataTemplate**, основываясь на указанной критерии.

В этом примере предполагается, что **DataTemplateSelector** определен для выбора между двумя разными **DataTemplate**, чтобы представлять большой и малый элемент.

```xaml
<ItemsRepeater ...>
    <ItemsRepeater.ItemTemplate>
        <local:VariableSizeTemplateSelector Large="{StaticResource LargeItemTemplate}" 
                                            Small="{StaticResource SmallItemTemplate}"/>
    </ItemsRepeater.ItemTemplate>
</ItemsRepeater>
```

При определении **DataTemplateSelector** для использования с **ItemsRepeater** необходимо только реализовать переопределение для метода [**SelectTemplateCore(Object)** ](/uwp/api/windows.ui.xaml.controls.datatemplateselector.selecttemplatecore#Windows_UI_Xaml_Controls_DataTemplateSelector_SelectTemplateCore_System_Object_). Дополнительные сведения и примеры см. в разделе[**DataTemplateSelector**](/uwp/api/windows.ui.xaml.controls.datatemplateselector).

> [!NOTE]
> Альтернативой **DataTemplate** для управления созданием элементов в более сложных сценариях является реализация собственного [**Windows.UI.Xaml.Controls.IElementFactory**](/uwp/api/windows.ui.xaml.controls.ielementfactory)для использования в качестве **ItemTemplate**.  Он будет отвечать за создание содержимого по запросу.

## <a name="configure-the-data-source"></a>Настройка источника данных

Используйте свойство [ItemsSource](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemssource), чтобы указать коллекцию, используемую для создания содержимого элементов. ItemsSource можно задать любому типу, реализующему **IEnumerable**. Дополнительные интерфейсы коллекции, реализованные источником данных, определяют доступную функцию для ItemsRepeater, чтобы взаимодействовать с данными.

Этот список содержит доступные интерфейсы, а также информацию о том, когда следует использовать каждый из них.

- [IEnumerable](/dotnet/api/system.collections.generic.ienumerable-1)(.NET) / [IIterable](/uwp/api/windows.foundation.collections.iiterable_t_)

  - Можно использовать для небольших, статических наборов данных.

    Источник данных должен, как минимум, реализовывать интерфейс IEnumerable/IIterable. Если это все поддерживаемые интерфейсы, то элемент управления будет перебирать все один раз, чтобы создать копию, которую он может использовать для доступа к элементам через значение индекса.

- [IReadonlyList](/dotnet/api/system.collections.generic.ireadonlylist-1)(.NET) / [IVectorView](/uwp/api/windows.foundation.collections.ivectorview_t_)

  - Можно использовать для статических, нередактируемых наборов данных.

    Включает элемент управления для доступа к элементам с помощью индекса и позволяет избежать избыточных внутренних копий.

- [IList](/dotnet/api/system.collections.generic.ilist-1)(.NET) / [IVector](/uwp/api/windows.foundation.collections.ivector_t_)

  - Можно использовать для статических наборов данных.

    Включает элемент управления для доступа к элементам с помощью индекса и позволяет избежать избыточных внутренних копий.

    **Предупреждение.** Изменения в списке/векторе без реализации [INotifyCollectionChanged](/dotnet/api/system.collections.specialized.inotifycollectionchanged) не будут отражены в пользовательском интерфейсе.

- [INotifyCollectionChanged](/dotnet/api/system.collections.specialized.inotifycollectionchanged)(.NET)

  - Рекомендуется для поддержки уведомления об изменениях.

    Позволяет элементу управления наблюдать за изменениями в источнике данных и реагировать на них, а также отображать эти изменения в пользовательском интерфейсе.

- [IObservableVector](/uwp/api/windows.foundation.collections.iobservablevector_t_)

  - Поддерживает уведомление об изменениях

    Как и интерфейс **INotifyCollectionChanged**, он позволяет элементу управления наблюдать за изменениями в источнике данных и реагировать на них.

    **Предупреждение.** Windows.Foundation.IObservableVector\<T> не поддерживает действие "Перемещение". Это может вызвать потерю визуального состояния пользовательского интерфейса для элемента.  Например, элемент, выбранный в данный момент и/или который фокусируется на месте, куда выполнено перемещение, при использовании команды "Удалить", а затем "Добавить" теряет фокус и не поддается выбору.

    The Platform.Collections.Vector\<T> использует IObservableVector\<T> и имеет то же ограничение. Если необходима поддержка для действия "Перемещение", используйте интерфейс **INotifyCollectionChanged**.  Класс ObservableCollection .NET\<T> использует **INotifyCollectionChanged**.

- [IKeyIndexMapping](/uwp/api/microsoft.ui.xaml.controls.ikeyindexmapping)

  - Уникальный идентификатор можно связать с каждым элементом.  Рекомендуется при использовании действия "Сброс" в качестве действия изменения коллекции.

    Позволяет элементу управления очень эффективно восстановить существующий пользовательский интерфейс после сложного действия "Сброс", как части события **INotifyCollectionChanged** или **IObservableVector**. После сброса элемент управления будет использовать указанный уникальный идентификатор, чтобы сопоставить текущие данные с уже созданными элементами. Без ключа для сопоставления индекса элементу управления будет необходимо начать с нуля создание пользовательского интерфейса для данных.

Интерфейсы, перечисленные выше, отличные от IKeyIndexMapping, ведут себя в ItemsRepeater также, как в ListView и GridView.


Следующие интерфейсы ItemsSource позволяют использовать специальные функции в элементах управления ListView и GridView, но в настоящее время они не оказывают влияния на ItemsRepeater:

- [ISupportIncrementalLoading](/uwp/api/windows.ui.xaml.data.isupportincrementalloading)
- [IItemsRangeInfo](/uwp/api/windows.ui.xaml.data.iitemsrangeinfo)
- [ISelectionInfo](/uwp/api/windows.ui.xaml.data.iselectioninfo)

> [!TIP]
> Ждем ваших отзывов! Сообщите нам свое мнение на странице [Проект GitHub библиотека пользовательского интерфейса Windows](https://github.com/Microsoft/microsoft-ui-xaml/issues). Поделитесь своими мыслями по поводу существующих предложений, таких как [#374](https://github.com/Microsoft/microsoft-ui-xaml/issues/374): Добавление поддержки инкрементной загрузки для ItemsRepeater.

Альтернативный метод для инкрементной загрузки данных при прокручивании пользователем вверх или вниз заключается в наблюдении за положением окна просмотра ScrollViewer и загрузке большего количества данных по мере приближения области просмотра к экстенту.

```xaml
<ScrollViewer ViewChanged="ScrollViewer_ViewChanged">
    <ItemsRepeater ItemsSource="{x:Bind MyItemsSource}" .../>
</ScrollViewer>
```

```csharp
private async void ScrollViewer_ViewChanged(object sender, ScrollViewerViewChangedEventArgs e)
{
    if (!e.IsIntermediate)
    {
        var scroller = (ScrollViewer)sender;
        var distanceToEnd = scroller.ExtentHeight - (scroller.VerticalOffset + scroller.ViewportHeight);

        // trigger if within 2 viewports of the end
        if (distanceToEnd <= 2.0 * scroller.ViewportHeight
                && MyItemsSource.HasMore && !itemsSource.Busy)
        {
            // show an indeterminate progress UI
            myLoadingIndicator.Visibility = Visibility.Visible;

            await MyItemsSource.LoadMoreItemsAsync(/*DataFetchSize*/);

            loadingIndicator.Visibility = Visibility.Collapsed;
        }
    }
}
```

## <a name="change-the-layout-of-items"></a>Изменение макета элементов

Отображаемые с помощью [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) элементы, упорядочены по объекту[макета](/uwp/api/microsoft.ui.xaml.controls.layout), который управляет определением размеров и положения его дочерних элементов. При использовании с ItemsRepeater объект макета позволяет выполнять виртуализацию пользовательского интерфейса. Предоставленными макетами являются [StackLayout](/uwp/api/microsoft.ui.xaml.controls.stacklayout) и [UniformGridLayout](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout). По умолчанию ItemsRepeater использует StackLayout с вертикальной ориентацией.

### <a name="stacklayout"></a>StackLayout

[StackLayout](/uwp/api/microsoft.ui.xaml.controls.stacklayout) выравнивает элементы в одну линию, которую можно размещать по горизонтали или вертикали.

Вы можете задать свойство [интервала](/uwp/api/microsoft.ui.xaml.controls.stacklayout.spacing), чтобы отрегулировать величину пространства между элементами. Интервал применяется в направлении [ориентации](/uwp/api/microsoft.ui.xaml.controls.stacklayout.orientation) макета.

![Интервал макета стека](images/stack-layout.png)

В этом примере показано, как присвоить свойство ItemsRepeater.Layout для StackLayout с горизонтальной ориентацией и интервалом 8 пикселей.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<muxc:ItemsRepeater ItemsSource="{x:Bind Items}" ItemTemplate="{StaticResource MyTemplate}">
    <muxc:ItemsRepeater.Layout>
        <muxc:StackLayout Orientation="Horizontal" Spacing="8"/>
    </muxc:ItemsRepeater.Layout>
</muxc:ItemsRepeater>
```

### <a name="uniformgridlayout"></a>UniformGridLayout

[UniformGridLayout](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout) размещает элементы последовательно в макете переноса. Элементы располагаются в порядке слева направо, если значение параметра [Orientation](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.orientation) — равно **Horizontal** (по горизонтали), и сверху вниз, если значение равно **Vertical** (по вертикали). Все элементы имеют одинаковый размер.

![Универсальный интервал макета сетки](images/uniform-grid-layout.png)

Число элементов в каждой строке горизонтального макета зависит от минимальной ширины элемента. Число элементов в каждом столбце вертикального макета зависит от минимальной высоты элемента.

- Минимальный размер можно явно указать с помощью свойств [MinItemHeight](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.minitemheight) и [MinItemWidth](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.minitemwidth).
- Если не указать минимальный размер, размер первого элемента станет минимальным размером каждого элемента.

Вы можете также задать минимальный интервал для макета, чтобы включить его между строками и столбцами, задав свойства [MinColumnSpacing](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.mincolumnspacing) и [MinRowSpacing](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.minrowspacing).

![Универсальный интервал и размер сетки](images/uniform-grid-sizing-spacing.png)

После определения количества элементов в строке или столбце в зависимости от минимального размера и интервалов, может возникнуть неиспользуемое место после последнего элемента в строке или столбце (как показано на предыдущем рисунке). Вы можете указать,что нужно сделать с оставшимся пространством — игнорировать его, использовать для увеличения размера каждого элемента или создать дополнительное пространство между элементами. Этим управляют свойства [ItemsStretch](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.itemsstretch) и [ItemsJustification](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.itemsjustification).

Чтобы указать, как увеличивается размер элемента для заполнения всего неиспользуемого пространства, можно задать свойство [ItemsStretch](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.itemsstretch).

В списке содержатся доступные значения. Определения предполагают, что для параметра **Orientation** задано по умолчанию **Horizontal** (по горизонтали).

- **Нет.** Неиспользованное дополнительное пространство содержится в конце строки. Это значение по умолчанию.
- **Заполнение**: Ширина элементов увеличивается, чтобы израсходовать все доступное пространство (высота, если по вертикали).
- **Однородный элемент**: Увеличивается ширина элементов, чтобы израсходовать все доступное пространство, а также высота — чтобы сохранить пропорции (высота и ширина меняются местами, если по вертикали).

На этом изображении показано результат влияние значения **ItemsStretch** в горизонтальном макете.

![Универсальное растяжение элемента сетки](images/uniform-grid-item-stretch.png)

Если для параметра **ItemsStretch** задано значение **None** (Нет), можно задать свойство [ItemsJustification](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.itemsjustification), чтобы указать, как используется дополнительное пространство для выравнивания элементов.

В списке содержатся доступные значения. Определения предполагают, что для параметра **Orientation** задано по умолчанию **Horizontal** (по горизонтали).

- **Start** (С начала). Элементы выравниваются с начала строки. Неиспользованное дополнительное пространство содержится в конце строки. Это значение по умолчанию.
- **Center** (По центру). Элементы выравниваются по центру строки. Дополнительное пространство делится поровну в начало и конец строки.
- **End** (К концу). Элементы выравниваются с конца строки. Неиспользованное дополнительное пространство содержится в начале строки.
- **SpaceAround** (Равномерно дополнить). Элементы распределяются равномерно. Равное количество места добавляется до и после каждого элемента.
- **SpaceBetween** (Равномерно добавить): Элементы распределяются равномерно. Равное количество места добавляется между каждым элементом. Пространство не добавляется в начале и конце строки.
- **SpaceEvenly** (Равномерно по краям). Элементы распределяются равномерно, с равным количеством пространства между каждым элементом и в начале, и конце строки.

На этом изображении показано результат влияния значения **ItemsStretch**, в макете по вертикали (применяется к столбцам, а не строкам).

![Универсальное обоснование элемента сетки](images/uniform-grid-item-justification.png)

> [!TIP]
> Свойство **ItemsStretch** влияет на этап_измерения_ макета. Свойство **ItemsJustification** влияет на этап_упорядочивания_ макета.

В этом показано, как задать свойство **ItemsRepeater.Layout** для **UniformGridLayout**.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<muxc:ItemsRepeater ItemsSource="{x:Bind Items}"
                    ItemTemplate="{StaticResource MyTemplate}">
    <muxc:ItemsRepeater.Layout>
        <muxc:UniformGridLayout MinItemWidth="200"
                                MinColumnSpacing="28"
                                ItemsJustification="SpaceAround"/>
    </muxc:ItemsRepeater.Layout>
</muxc:ItemsRepeater>
```

## <a name="lifecycle-events"></a>События жизненного цикла

При размещении элементов в [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), может потребоваться выполнение определенного действия при отображении элемента или остановке его отображения, например, запустить асинхронную загрузку содержания, провести связку элемента с механизмом для отслеживания трека или остановить некоторые фоновые задачи.

В элементе управления виртуализации нельзя полагаться на события Loaded/Unloaded, поскольку элемент может быть не удален из динамического визуального дерева после его повторного запуска. Чтобы управлять жизненным циклом элементов, вместо них предоставляются другие события. На этой схеме показан жизненный цикл элемента в ItemsRepeater, а также при создании связанных событий.

![Схема событий жизненного цикла](images/items-repeater-lifecycle.png)

- [**ElementPrepared**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.elementprepared) возникает каждый раз, когда элемент становится готов к использованию. Эта операция выполняется для только что созданного элемента , а также элемента, который уже существует, и используется повторно из очереди повторного применения.
- [**ElementClearing**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.elementclearing) возникает каждый раз непосредственно когда элемент был отправлен в очередь для повторного запуска, например когда она находится за пределами диапазона элементов.
- [**ElementIndexChanged**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.elementindexchanged
) возникает для каждого выполненного элемента пользовательского интерфейса с измененным индексом представляемого элемента. Например при добавлении другого элемента в источник данных или его удалении, индекс элементов, порядком ниже, получает это событие.

В этом примере показано, как можно использовать эти события, чтобы присоединить пользовательскую службу выбора для отслеживания выбора элементов в пользовательском элементе управления, который использует ItemsRepeater для отображения элементов.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<UserControl ...>
    ...
    <ScrollViewer>
        <muxc:ItemsRepeater ItemsSource="{x:Bind Items}"
                            ItemTemplate="{StaticResource MyTemplate}"
                            ElementPrepared="OnElementPrepared"
                            ElementIndexChanged="OnElementIndexChanged"
                            ElementClearing="OnElementClearing">
        </muxc:ItemsRepeater>
    </ScrollViewer>
    ...
</UserControl>
```

```csharp
interface ISelectable
{
    int SelectionIndex { get; set; }
    void UnregisterSelectionModel(SelectionModel selectionModel);
    void RegisterSelectionModel(SelectionModel selectionModel);
}

private void OnElementPrepared(ItemsRepeater sender, ElementPreparedEventArgs args)
{
    var selectable = args.Element as ISelectable;
    if (selectable != null)
    {
        // Wire up this item to recognize a 'select' and listen for programmatic
        // changes to the selection model to know when to update its visual state.
        selectable.SelectionIndex = args.Index;
        selectable.RegisterSelectionModel(this.SelectionModel);
    }
}

private void OnElementIndexChanged(ItemsRepeater sender, ElementIndexChangedEventArgs args)
{
    var selectable = args.Element as ISelectable;
    if (selectable != null)
    {
        // Sync the ID we use to notify the selection model when the item
        // we represent has changed location in the data source.
        selectable.SelectionIndex = args.NewIndex;
    }
}

private void OnElementClearing(ItemsRepeater sender, ElementClearingEventArgs args)
{
    var selectable = args.Element as ISelectable;
    if (selectable != null)
    {
        // Disconnect handlers to recognize a 'select' and stop
        // listening for programmatic changes to the selection model.
        selectable.UnregisterSelectionModel(this.SelectionModel);
        selectable.SelectionIndex = -1;
    }
}
```

## <a name="sorting-filtering-and-resetting-the-data"></a>Сортировка, фильтрация и сброс данных

Выполняя фильтрацию или сортировку набора данных, традиционно существовала возможность сравнивать предыдущий набор данных с новыми данными, а затем выдавать детальные уведомления об изменениях с помощью [INotifyCollectionChanged](/uwp/api/windows.ui.xaml.interop.inotifycollectionchanged). Тем не менее, вместо этого зачастую проще полностью заменить старые данные новыми данными и запустить уведомление об изменении коллекции с помощью действия [Сброс](/uwp/api/windows.ui.xaml.interop.notifycollectionchangedaction).

Как правило, сброс приводит к тому, что элемент управления освобождает существующие дочерние элементы и выполняет все повторно, создавая пользовательский интерфейс заново с нулевой позицией прокрутки, поскольку он не имеет точной информации об изменении данных во время сброса.

Тем не менее, если коллекция, назначенная в качестве ItemsSource, поддерживает уникальные идентификаторы путем реализации интерфейса [IKeyIndexMapping](/uwp/api/microsoft.ui.xaml.controls.ikeyindexmapping), ItemsRepeater может быстро определить:

- многократно используемые элементы интерфейса пользователя для данных, которые существовали до и после сброса;
- ранее видимые элементы, которые были удалены;
- новые видимые элементы, которые были добавлены.

Это позволяет ItemsRepeater избежать повторного запуска с нулевой позиции прокрутки. Он также позволяет быстро восстановить элементы пользовательского интерфейса для неизменяемых во время сброса данных, что приводит к повышению производительности.

В этом примере показано, как отобразить список элементов в вертикальном столбце, в котором _MyItemsSource_ является пользовательским источником данных, служащим в качестве оболочки базового списка элементов. Он предоставляет свойство _Data_, которое можно использовать , чтобы повторно назначить новый список для использования в качестве источника элементов, который затем активирует сброс.

```xaml
<ScrollViewer x:Name="sv">
    <ItemsRepeater x:Name="repeater"
                ItemsSource="{x:Bind MyItemsSource}"
                ItemTemplate="{StaticResource MyTemplate}">
       <ItemsRepeater.Layout>
           <StackLayout ItemSpacing="8"/>
       </ItemsRepeater.Layout>
   </ItemsRepeater>
</ScrollViewer>
```

```csharp
public MainPage()
{
    this.InitializeComponent();

    // Similar to an ItemsControl, a developer sets the ItemsRepeater's ItemsSource.
    // Here we provide our custom source that supports unique IDs which enables
    // ItemsRepeater to be smart about handling resets from the data.
    // Unique IDs also make it easy to do things apply sorting/filtering
    // without impacting any state (i.e. selection).
    MyItemsSource myItemsSource = new MyItemsSource(data);

    repeater.ItemsSource = myItemsSource;

    // ...

    // We can sort/filter the data using whatever mechanism makes the
    // most sense (LINQ, database query, etc.) and then reassign
    // it, which in our implementation triggers a reset.
    myItemsSource.Data = someNewData;
}

// ...


public class MyItemsSource : IReadOnlyList<ItemBase>, IKeyIndexMapping, INotifyCollectionChanged
{
    private IList<ItemBase> _data;

    public MyItemsSource(IEnumerable<ItemBase> data)
    {
        if (data == null) throw new ArgumentNullException();

        this._data = data.ToList();
    }

    public IList<ItemBase> Data
    {
        get { return _data; }
        set
        {
            _data = value;

            // Instead of tossing out existing elements and re-creating them,
            // ItemsRepeater will reuse the existing elements and match them up
            // with the data again.
            this.CollectionChanged?.Invoke(
                this,
                new NotifyCollectionChangedEventArgs(NotifyCollectionChangedAction.Reset));
        }
    }

    #region IReadOnlyList<T>

    public ItemBase this[int index] => this.Data != null
        ? this.Data[index]
        : throw new IndexOutOfRangeException();

    public int Count => this.Data != null ? this.Data.Count : 0;
    public IEnumerator<ItemBase> GetEnumerator() => this.Data.GetEnumerator();
    IEnumerator IEnumerable.GetEnumerator() => this.GetEnumerator();

    #endregion

    #region INotifyCollectionChanged

    public event NotifyCollectionChangedEventHandler CollectionChanged;

    #endregion

    #region IKeyIndexMapping

    private int lastRequestedIndex = IndexNotFound;
    private const int IndexNotFound = -1;

    // When UniqueIDs are supported, the ItemsRepeater caches the unique ID for each item
    // with the matching UIElement that represents the item.  When a reset occurs the
    // ItemsRepeater pairs up the already generated UIElements with items in the data
    // source.
    // ItemsRepeater uses IndexForUniqueId after a reset to probe the data and identify
    // the new index of an item to use as the anchor.  If that item no
    // longer exists in the data source it may try using another cached unique ID until
    // either a match is found or it determines that all the previously visible items
    // no longer exist.
    public int IndexForUniqueId(string uniqueId)
    {
        // We'll try to increase our odds of finding a match sooner by starting from the
        // position that we know was last requested and search forward.
        var start = lastRequestedIndex;
        for (int i = start; i < this.Count; i++)
        {
            if (this[i].PrimaryKey.Equals(uniqueId))
                return i;
        }

        // Then try searching backward.
        start = Math.Min(this.Count - 1, lastRequestedIndex);
        for (int i = start; i >= 0; i--)
        {
            if (this[i].PrimaryKey.Equals(uniqueId))
                return i;
        }

        return IndexNotFound;
    }

    public string UniqueIdForIndex(int index)
    {
        var key = this[index].PrimaryKey;
        lastRequestedIndex = index;
        return key;
    }

    #endregion
}

```

## <a name="create-a-custom-collection-control"></a>Создание настраиваемого элемента управления коллекцией

Вы можете использовать [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), чтобы создать настраиваемый элемент управления коллекцией с собственным типом элемента управления для представления каждого элемента.

> [!NOTE]
> Он используется аналогично **ItemsControl**, но вместо вывода из **ItemsControl** и помещения **ItemsPresenter** в шаблон элемента управления, производить нужно из **Control** и помещать **ItemsRepeater** в шаблон элемента управления. Пользовательский элемент управления коллекцией содержит **ItemsRepeater**, а не является **ItemsControl**. Это подразумевает, что необходимо также явно выбрать представляемые свойства, вместо того, чтобы выбирать, какие унаследованные свойства не поддерживать.

В этом примере показано, как разместить [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) в шаблоне пользовательского элемента управления с именем _MediaCollectionView_ и как предоставить его свойства.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<Style TargetType="local:MediaCollectionView">
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="local:MediaCollectionView">
                <Border
                    Background="{TemplateBinding Background}"
                    BorderBrush="{TemplateBinding BorderBrush}"
                    BorderThickness="{TemplateBinding BorderThickness}">
                    <ScrollViewer x:Name="ScrollViewer">
                        <muxc:ItemsRepeater x:Name="ItemsRepeater"
                                            ItemsSource="{TemplateBinding ItemsSource}"
                                            ItemTemplate="{TemplateBinding ItemTemplate}"
                                            Layout="{TemplateBinding Layout}"
                                            TabFocusNavigation="{TemplateBinding TabFocusNavigation}"/>
                    </ScrollViewer>
                </Border>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

```csharp
public sealed class MediaCollectionView : Control
{
    public object ItemsSource
    {
        get { return (object)GetValue(ItemsSourceProperty); }
        set { SetValue(ItemsSourceProperty, value); }
    }

    // Using a DependencyProperty as the backing store for ItemsSource.  This enables animation, styling, binding, etc...
    public static readonly DependencyProperty ItemsSourceProperty =
        DependencyProperty.Register(nameof(ItemsSource), typeof(object), typeof(MediaCollectionView), new PropertyMetadata(0));

    public DataTemplate ItemTemplate
    {
        get { return (DataTemplate)GetValue(ItemTemplateProperty); }
        set { SetValue(ItemTemplateProperty, value); }
    }

    // Using a DependencyProperty as the backing store for ItemTemplate.  This enables animation, styling, binding, etc...
    public static readonly DependencyProperty ItemTemplateProperty =
        DependencyProperty.Register(nameof(ItemTemplate), typeof(DataTemplate), typeof(MediaCollectionView), new PropertyMetadata(0));

    public Layout Layout
    {
        get { return (Layout)GetValue(LayoutProperty); }
        set { SetValue(LayoutProperty, value); }
    }

    // Using a DependencyProperty as the backing store for Layout.  This enables animation, styling, binding, etc...
    public static readonly DependencyProperty LayoutProperty =
        DependencyProperty.Register(nameof(Layout), typeof(Layout), typeof(MediaCollectionView), new PropertyMetadata(0));

    public MediaCollectionView()
    {
        this.DefaultStyleKey = typeof(MediaCollectionView);
    }
}
```

## <a name="display-grouped-items"></a>Отображение сгруппированных элементов

[ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) можно вложить в [ItemTemplate](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemtemplate) из другого ItemsRepeater, чтобы создать вложенные макеты виртуализации. Платформа будет эффективно использовать ресурсы, сводя к минимуму ненужную реализацию элементов, которые не отображаются или не находятся рядом с текущим окном просмотра.

В этом примере показано, как можно отобразить список со сгруппированными элементами в вертикальном столбце. Внешний ItemsRepeater создает каждую группу. Другой ItemsRepeater создает элементы в шаблоне каждой группы.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->

<Page.Resources>
    <muxc:StackLayout x:Key="MyGroupLayout"/>
    <muxc:StackLayout x:Key="MyItemLayout" Orientation="Horizontal"/>
</Page.Resources>

<ScrollViewer>
  <muxc:ItemsRepeater ItemsSource="{x:Bind AppNotifications}"
                      Layout="{StaticResource MyGroupLayout}">
    <muxc:ItemsRepeater.ItemTemplate>
      <DataTemplate x:DataType="ExampleApp:AppNotifications">
        <!-- Group -->
        <StackPanel>
          <!-- Header -->
          <TextBlock Text="{x:Bind AppTitle}"/>
          <!-- Items -->
          <muxc:ItemsRepeater ItemsSource="{x:Bind Notifications}"
                              Layout="{StaticResource MyItemLayout}"
                              ItemTemplate="{StaticResource MyTemplate}"/>
          <!-- Footer -->
          <Button Content="{x:Bind FooterText}"/>
        </StackPanel>
      </DataTemplate>
    </muxc:ItemsRepeater.ItemTemplate>
  </muxc:ItemsRepeater>
</ScrollViewer>
```
На следующем рисунке показан базовый макет, созданный с использованием приведенного выше примера в качестве рекомендации.

![Вложенный макет с повторением элементов](images/items-repeater-nested-layout.png)

В следующем примере показан макет для приложения, содержащий разные категории, которые можно изменить с помощью предпочтений пользователя и которые представлены в виде горизонтальных списков с прокруткой. Макет этого примера также представлен на рисунке выше.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<!-- Include the <muxc:ItemsRepeaterScrollHost> if targeting Windows 10 versions earlier than 1809. -->
<ScrollViewer>
  <muxc:ItemsRepeater ItemsSource="{x:Bind Categories}"
                      Background="LightGreen">
    <muxc:ItemsRepeater.ItemTemplate>
      <DataTemplate x:DataType="local:Category">
        <StackPanel Margin="12,0">
          <TextBlock Text="{x:Bind Name}" Style="{ThemeResource TitleTextBlockStyle}"/>
          <!-- Include the <muxc:ItemsRepeaterScrollHost> if targeting Windows 10 versions earlier than 1809. -->
          <ScrollViewer HorizontalScrollMode="Enabled"
                                          VerticalScrollMode="Disabled"
                                          HorizontalScrollBarVisibility="Auto" >
            <muxc:ItemsRepeater ItemsSource="{x:Bind Items}"
                                Background="Orange">
              <muxc:ItemsRepeater.ItemTemplate>
                <DataTemplate x:DataType="local:CategoryItem">
                  <Grid Margin="10"
                        Height="60" Width="120"
                        Background="LightBlue">
                    <TextBlock Text="{x:Bind Name}"
                               Style="{StaticResource SubtitleTextBlockStyle}"
                               Margin="4"/>
                  </Grid>
                </DataTemplate>
              </muxc:ItemsRepeater.ItemTemplate>
              <muxc:ItemsRepeater.Layout>
                <muxc:StackLayout Orientation="Horizontal"/>
              </muxc:ItemsRepeater.Layout>
            </muxc:ItemsRepeater>
          </ScrollViewer>
        </StackPanel>
      </DataTemplate>
    </muxc:ItemsRepeater.ItemTemplate>
  </muxc:ItemsRepeater>
</ScrollViewer>
```

## <a name="bringing-an-element-into-view"></a>Перенос элемента в представление

Framework XAML уже обрабатывает перенос FrameworkElement в представление получении фокуса 1) клавиатуры или 2) экранного диктора. Возможны и другие ситуации, когда элемент необходимо явно сделать отображаемым. Например в ответ на действия пользователя или чтобы восстановить состояние пользовательского интерфейса после перехода между страницами.

Перенос виртуализованного элемента в представление включает следующее:
1. установка элемента управления для элемента;
2. выполнение макета, чтобы убедиться, что элемент имеет допустимую позицию;
3. создание запроса для отображения реализованного элемента в представлении.

В приведенном ниже примере эти действия показаны в процессе восстановления позиции прокрутки элемента в плоском вертикальном списке после перехода между страницами. В случае с иерархическими данными, использующими вложенный ItemsRepeaters, подход, по существу, является прежним, но при этом он должен выполняться на каждом уровне иерархии.

```xaml
<ScrollViewer x:Name="scrollviewer">
  <ItemsRepeater x:Name="repeater" .../>
</ScrollViewer>
```

```csharp
public class MyPage : Page
{
    // ...

     protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        // retrieve saved offset + index(es) of the tracked element and then bring it into view.
        // ... 
        
        var element = repeater.GetOrCreateElement(index);

        // ensure the item is given a valid position
        element.UpdateLayout();

        element.StartBringIntoView(new BringIntoViewOptions()
        {
            VerticalOffset = relativeVerticalOffset
        });
    }

    protected override void OnNavigatingFrom(NavigatingCancelEventArgs e)
    {
        base.OnNavigatingFrom(e);

        // retrieve and save the relative offset and index(es) of the scrollviewer's current anchor element ...
        var anchor = this.scrollviewer.CurrentAnchor;
        var index = this.repeater.GetElementIndex(anchor);
        var anchorBounds = anchor.TransformToVisual(this.scrollviewer).TransformBounds(new Rect(0, 0, anchor.ActualSize.X, anchor.ActualSize.Y));
        relativeVerticalOffset = this.scrollviewer.VerticalOffset - anchorBounds.Top;
    }
}

```

## <a name="enable-accessibility"></a>Включение специальных возможностей

[ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) не поддерживает стандартные специальные возможности взаимодействия. Документация по [удобству использования приложений UWP](/windows/uwp/design/usability) предоставляет широкий набор сведений, чтобы убедиться, что приложение предоставляет инклюзивное взаимодействие с пользователем. Если вы используете ItemsRepeater, чтобы создать настраиваемый элемент управления, ознакомьтесь с документацией по [настраиваемым одноранговым элементам автоматизации](/windows/uwp/design/accessibility/custom-automation-peers).

### <a name="keyboarding"></a>Поддержка клавиатуры
Минимальная поддержка клавиатуры для перемещения фокуса, которую предоставляет ItemsRepeater, основана на [двухмерной направленной навигации для клавиатуры](/windows/uwp/design/input/focus-navigation#2d-directional-navigation-for-keyboard) в XAML.

![Направление навигации](/windows/uwp/design/input/images/keyboard/directional-navigation.png)

Режим [XYFocusKeyboardNavigation](/uwp/api/windows.ui.xaml.input.xyfocuskeyboardnavigationmode) для ItemsRepeater _включен_ по умолчанию. В зависимости от предполагаемого взаимодействия рассмотрите возможность добавления поддержки общих [взаимодействий с клавиатурой](/windows/uwp/design/input/keyboard-interactions), таких как Home, End, PageUp, и PageDown.

ItemsRepeater автоматически гарантирует, что стандартный порядок вкладок для его элементов (виртуализированных или нет) имеет тот же порядок, что и элементы, заданные в данных. По умолчанию ItemsRepeater имеет свое свойство [TabFocusNavigation](/uwp/api/windows.ui.xaml.uielement.tabfocusnavigation), заданное [однократно](/uwp/api/windows.ui.xaml.input.keyboardnavigationmode), вместо общего стандартного значения _локально_.

> [!NOTE]
> ItemsRepeater не запоминает последний выбранный элемент автоматически.  Это означает, что при использовании клавиш Shift + Tab, их можно использовать для последнего реализованного элемента.

### <a name="announcing-item-_x_-of-_y_-in-screen-readers"></a>Объявление "Элемент _X_ из _Y_" в устройствах чтения с экрана

Необходимо управлять настройкой соответствующих свойств автоматизации, таких как значения для **PositionInSet** и **SizeOfSet**, и обеспечивать их актуальность при добавлении, перемещении, удалении и т. д.

В некоторых пользовательских макетах может отсутствовать очевидная последовательность визуального порядка.  Как минимум, пользователи ожидают, что значения свойств PositionInSet и SizeOfSet, используемые устройствами чтения с экрана, будут соответствовать порядку, в котором элементы отображаются в данных (смещение на 1 для соответствия естественному счету, вместо выполнения на основе 0).

Лучший способ добиться этого — использовать одноранговый узел автоматизации для элемента управления, который реализует методы [GetPositionInSetCore](/uwp/api/windows.ui.xaml.automation.peers.automationpeer.getpositioninsetcore) и [GetSizeOfSetCore](/uwp/api/windows.ui.xaml.automation.peers.automationpeer.getsizeofsetcore) и сообщает о положении элемента в наборе данных, представленном элементом управления. Значение вычисляется только во время обращения к нему с помощью специальных возможностей, и его постоянное обновление становится несущественным. Значение совпадает с порядком данных.

В этом примере показано, как это можно сделать при вызове представления пользовательского элемента управления _CardControl_.

```xaml
<ScrollViewer >
    <ItemsRepeater x:Name="repeater" ItemsSource="{x:Bind MyItemsSource}">
       <ItemsRepeater.ItemTemplate>
           <DataTemplate x:DataType="local:CardViewModel">
               <local:CardControl Item="{x:Bind}"/>
           </DataTemplate>
       </ItemsRepeater.ItemTemplate>
   </ItemsRepeater>
</ScrollViewer>
```

```csharp
internal sealed class CardControl : CardControlBase
{
    protected override AutomationPeer OnCreateAutomationPeer() => new CardControlAutomationPeer(this);

    private sealed class CardControlAutomationPeer : FrameworkElementAutomationPeer
    {
        private readonly CardControl owner;

        public CardControlAutomationPeer(CardControl owner) : base(owner) => this.owner = owner;

        protected override int GetPositionInSetCore()
          => ((ItemsRepeater)owner.Parent)?.GetElementIndex(this.owner) + 1 ?? base.GetPositionInSetCore();

        protected override int GetSizeOfSetCore()
          => ((ItemsRepeater)owner.Parent)?.ItemsSourceView?.Count ?? base.GetSizeOfSetCore();
    }
}
```

## <a name="related-articles"></a>Похожие статьи

- [Списки](lists.md)
- [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)
- [ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer)
