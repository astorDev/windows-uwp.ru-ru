---
Description: ItemsRepeater является элементом управления нетребовательный к ресурсам для создания и представления коллекции элементов.
title: ItemsRepeater
label: ItemsRepeater
template: detail.hbs
ms.date: 02/01/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 93a81501b524826484111419899675fbb99b86fa
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66364759"
---
# <a name="itemsrepeater"></a>ItemsRepeater

Используйте [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) пользовательской коллекции интерфейсы с помощью системы гибкий макет, пользовательские представления и виртуализации.

В отличие от [ListView](/uwp/api/windows.ui.xaml.controls.listview), [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) не предоставляет комплексное взаимодействии — она нет значения по умолчанию пользовательского интерфейса и предоставляет политика не взаимодействию фокус, объектов или пользователя. Вместо этого это стандартного блока, который можно использовать для создания собственных уникальных приложений на базе веб-коллекции и пользовательских элементов управления. Хотя у него есть нет встроенной политики, позволяет присоединить создайте свое собственное видение, требуемую политику. Например можно определить макет для использования, keyboarding политики, политики выбора и т. д.

Можно представить себе [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) концептуально как панель управляемых данными, а не как полный контроль, таких как ListView. Укажите коллекцию отображаемых элементов данных, шаблон элемента, который создает элемент пользовательского интерфейса для каждого элемента данных и макет, который определяет, как размер и положение элементов. Затем ItemsRepeater создает дочерние элементы, на основе источника данных и отображает их в соответствии с шаблоном элемента и макет. Отображаемых элементов не обязательно должны быть однородной, так как ItemsRepeater можно загрузить содержимое для представления элементов данных, на основе критериев, указываемых в элемент выбора шаблона данных.

| **Получение библиотеки пользовательского интерфейса Windows** |
| - |
| Этот элемент управления входит в состав библиотеки пользовательского интерфейса Windows, пакет NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений универсальной платформы Windows. Дополнительные сведения, включая инструкции по установке, см. в разделе [Общие сведения о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **Важные API**: [Класс ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), [класс ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) для создания пользовательских представлений коллекций данных. Хотя его можно использовать для предоставления базового набора элементов, его часто можно использовать в качестве отображаемого элемента в шаблоне элемента управления.

Если требуется, чтобы элемент управления out of box для отображения данных в списке или сетке с минимальной настройкой, рассмотрите возможность использования [ListView](/uwp/api/windows.ui.xaml.controls.listview) или [GridView](/uwp/api/windows.ui.xaml.controls.gridview).

ItemsRepeater не имеет встроенной коллекции элементов. Если вам нужно предоставить набор Items напрямую, вместо привязки к источнику данных, то скорее всего, требующие возможности более высокой policy и следует использовать [ListView](/uwp/api/windows.ui.xaml.controls.listview) или [GridView](/uwp/api/windows.ui.xaml.controls.gridview).

[Элемент управления ItemsControl](/uwp/api/windows.ui.xaml.controls.itemscontrol) ItemsRepeater обоих доступ к возможностям настраиваемые коллекции, но ItemsRepeater поддерживает виртуализации пользовательского интерфейса, а элемент управления ItemsControl — нет. Мы рекомендуем использовать ItemsRepeater вместо ItemsControl, является ли его просто представления несколько элементов на основе данных или создание настраиваемой коллекции элемента управления.

> [!NOTE]
> Если у вас есть ситуации, где вы считаете, что элемент управления ItemsControl соответствует вашим потребностям и не ItemsRepeater, оставьте свои отзывы на [проекта GitHub библиотека пользовательского интерфейса Windows](https://github.com/Microsoft/microsoft-ui-xaml/issues) и дайте нам знать.

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы открыть приложение и просмотреть <a href="xamlcontrolsgallery:/item/ItemsRepeater">ItemsRepeater</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="scrolling-with-itemsrepeater"></a>Прокрутка при помощи ItemsRepeater

[**ItemsRepeater** ](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) является производным от [ **управления**](/uwp/api/windows.ui.xaml.controls.control), поэтому он не имеет шаблона элемента управления. Таким образом он не содержит любые встроенные прокрутка как ListView, или у других элементов управления в коллекции.

При использовании **ItemsRepeater**, необходимо предоставить функции прокрутки, если поместить его в [ **ScrollViewer** ](/uwp/api/windows.ui.xaml.controls.scrollviewer) элемента управления.

> [!NOTE]
> Если приложение работает в более ранних версиях Windows - тех освобожден *перед* Windows 10, версии 1809 - то вам также нужно разместить **ScrollViewer** внутри [  **ItemsRepeaterScrollHost**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeaterscrollhost). 
> ```xaml
> <muxc:ItemsRepeaterScrollHost>
>     <ScrollViewer>
>         <muxc:ItemsRepeater ... />
>     </ScrollViewer>
> </muxc:ItemsRepeaterScrollHost>
> ```
> Если приложение будет выполняться только на последних версиях Windows 10, версия 1809 и более поздние версии — то нет необходимости использовать [ **ItemsRepeaterScrollHost**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeaterscrollhost).
>
> До Windows 10, версия 1809, **ScrollViewer** не реализовал [ **IScrollAnchorProvider** ](/uwp/api/windows.ui.xaml.controls.iscrollanchorprovider) интерфейс, который **ItemsRepeater**требуется.  **ItemsRepeaterScrollHost** позволяет **ItemsRepeater** для координации с **ScrollViewer** в более ранних выпусках, чтобы правильно сохранить отображается расположение элементов При просмотре.  В противном случае элементы могут отображаться для перемещения или исчезновения после изменения элементов в списке или изменении размера приложения.

## <a name="create-an-itemsrepeater"></a>Создание ItemsRepeater

Чтобы использовать [ **ItemsRepeater**](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), необходимо предоставить ей данные для отображения, задав **ItemsSource** свойство. Затем, разъясняющий порядок отображения элементов, задав [ **ItemTemplate** ](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemtemplate) свойство.

### <a name="itemssource"></a>ItemsSource

Чтобы заполнить представление, задайте [ **ItemsSource** ](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemssource) свойство в коллекцию элементов данных. Здесь **ItemsSource** задания в коде непосредственно к экземпляру коллекции.

```csharp
ObservableCollection<string> Items = new ObservableCollection<string>();

ItemsRepeater itemsRepeater1 = new ItemsRepeater();
itemsRepeater1.ItemsSource = Items;
```

Можно также привязать **ItemsSource** свойство в коллекцию в XAML. Подробнее о концепциях привязки данных см. в разделе [Общие сведения о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-quickstart).


```xaml
<ItemsRepeater ItemsSource="{x:Bind Items}"/>
```

### <a name="itemtemplate"></a>ItemTemplate
Чтобы указать, как визуализируются элемент данных, задайте [ **ItemTemplate** ](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemtemplate) свойства [ **DataTemplate** ](/uwp/api/windows.ui.xaml.datatemplate) или [  **DataTemplateSelector** ](/uwp/api/windows.ui.xaml.controls.datatemplateselector) вы определили. Шаблон данных определяет, каким образом данные визуализируются. По умолчанию, элемент отображается в представлении с **TextBlock** использует строковое представление объекта данных.

Тем не менее обычно требуется отобразить более широкие возможности представления данных с помощью шаблона, который определяет макет и внешний вид один или несколько элементов управления, которые будут использоваться для отображения отдельного элемента. Элементы управления, которые можно использовать в шаблоне можно привязать к свойствам объекта данных, либо имеет статического содержимого определен как встроенный.

#### <a name="datatemplate"></a>DataTemplate
В этом примере объект данных является простой строкой. **DataTemplate** включает изображения слева от текста и стили **TextBlock** для отображения строки в сине-зеленым цветом.

> [!NOTE]
> При использовании [расширение разметки x: Bind](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) в **DataTemplate**, вам нужно будет указать тип данных (`x:DataType`) на DataTemplate.

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

Вот как будет выглядеть элементы при отображении с этим **DataTemplate**.

![Элементы, отображаемые с помощью шаблона данных](images/listview-itemstemplate.png)

Число элементов, используемых в **DataTemplate** для элемента может иметь значительное влияние на производительность, если представление отображает большое число элементов. Дополнительные сведения и примеры использования **DataTemplate**s, чтобы определить внешний вид элементов в списке, см. в разделе [контейнеры и шаблоны](item-containers-templates.md).

> [!TIP]
> Для удобства при объявлении встроенного шаблона, а не ссылаться как на статический ресурс, можно указать **DataTemplate** или **DataTemplateSelector** как непосредственным дочерним элементом **ItemsRepeater**.  Ему будет назначено для параметра **ItemTemplate** свойство. Например это является допустимым:
> ```xaml
> <ItemsRepeater ItemsSource="{x:Bind Items}">
>     <DataTemplate>
>         <!-- ... -->
>     </DataTemplate>
> </ItemsRepeater>
> ```

> [!TIP]
> В отличие от **ListView** и другие элементы управления коллекции, **ItemsRepeater** без переноса элементов из **DataTemplate** с контейнером еще один элемент, который включает в себя Политика по умолчанию, например поля, заполнения, Выбор визуальных элементов или указатель на визуальное состояние. Вместо этого **ItemsRepeater** показан только что определена в **DataTemplate**. Если требуется, чтобы элементы выглядел как элемент представления списка, можно явно включить контейнер, как **ListViewItem**, в шаблоне данных. **ItemsRepeater** покажет **ListViewItem** визуальных элементов, но не становится автоматически использовать для других функций, таких как выбор или отображение множественного выбора флажок.
>
> Аналогично, если сбора данных является коллекцией фактических элементов управления, например **кнопку** (`List<Button>`), можно поместить **ContentPresenter** в вашей **DataTemplate** для отображения элемента управления.

#### <a name="datatemplateselector"></a>DataTemplateSelector

Элементы, которые можно отобразить в представлении не обязательно должны быть одного типа. Вы можете предоставить [ **ItemTemplate** ](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemtemplate) свойство с [ **DataTemplateSelector** ](/uwp/api/windows.ui.xaml.controls.datatemplateselector) для выбора различных  **DataTemplate**, основываясь на указанным критериям.

В этом примере предполагается **DataTemplateSelector** был определен, решает между двумя разными **DataTemplate**s для представления больших и малых элемента.

```xaml
<ItemsRepeater ...>
    <ItemsRepeater.ItemTemplate>
        <local:VariableSizeTemplateSelector Large="{StaticResource LargeItemTemplate}" 
                                            Small="{StaticResource SmallItemTemplate}"/>
    </ItemsRepeater.ItemTemplate>
</ItemsRepeater>
```

При определении **DataTemplateSelector** для использования с **ItemsRepeater** необходимо реализовать переопределение для [ **SelectTemplateCore(Object)** ](/uwp/api/windows.ui.xaml.controls.datatemplateselector.selecttemplatecore#Windows_UI_Xaml_Controls_DataTemplateSelector_SelectTemplateCore_System_Object_) метод. Дополнительные сведения и примеры см. в разделе [ **DataTemplateSelector**](/uwp/api/windows.ui.xaml.controls.datatemplateselector).

> [!NOTE]
> Альтернативой **DataTemplate**s для управления как элементы создаются в более сложных сценариях является реализация собственных [ **Windows.UI.Xaml.Controls.IElementFactory** ](/uwp/api/windows.ui.xaml.controls.ielementfactory)для использования в качестве **ItemTemplate**.  Он будет отвечать за генерирование содержимого по запросу.

## <a name="configure-the-data-source"></a>Настройка источника данных

Используйте [ItemsSource](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemssource) свойство, чтобы указать коллекцию для использования для создания содержимого элементов. Можно задать любой тип, реализующий ItemsSource **IEnumerable**. Дополнительную коллекцию интерфейсов, реализованных в источнике данных определить, какая функция доступна для ItemsRepeater для взаимодействия с данными.

Этот список содержит доступные интерфейсы и когда следует использовать каждый из них.

- [IEnumerable](/dotnet/api/system.collections.generic.ienumerable-1)(.NET) / [IIterable](/uwp/api/windows.foundation.collections.iiterable_t_)

  - Можно использовать для небольшой и статичный наборов данных.

    Как минимум, источник данных должен реализовывать IEnumerable / интерфейса IIterable. Если это все, что поддерживается затем элемент управления будет использовать перебор все один раз, чтобы создать копию, ее можно использовать для доступа к элементам с помощью значения индекса.

- [IReadonlyList](/dotnet/api/system.collections.generic.ireadonlylist-1)(.NET) / [IVectorView](/uwp/api/windows.foundation.collections.ivectorview_t_)

  - Можно использовать для наборов данных статические, только для чтения.

    Включает элемент управления для доступа к элементам по индексу и позволяет избежать избыточных внутренней копии.

- [IList](/dotnet/api/system.collections.generic.ilist-1)(.NET) / [IVector](/uwp/api/windows.foundation.collections.ivector_t_)

  - Можно использовать для статических наборов данных.

    Включает элемент управления для доступа к элементам по индексу и позволяет избежать избыточных внутренней копии.

    **Предупреждение**: Изменения в списке/вектор без реализации [INotifyCollectionChanged](/dotnet/api/system.collections.specialized.inotifycollectionchanged) не будут отражены в пользовательском Интерфейсе.

- [INotifyCollectionChanged](/dotnet/api/system.collections.specialized.inotifycollectionchanged)(.NET)

  - Рекомендуется для поддержки уведомления об изменениях.

    Позволяет элементу управления, наблюдать и реагировать на изменения в источнике данных и отразить эти изменения в пользовательском Интерфейсе.

- [IObservableVector](/uwp/api/windows.foundation.collections.iobservablevector_t_)

  - Поддерживает уведомления об изменениях

    Как и **INotifyCollectionChanged** интерфейса, это позволяет элементу управления, наблюдать и реагировать на изменения в источнике данных.

    **Предупреждение**: Windows.Foundation.IObservableVector\<T > не поддерживает действие «Перемещение». Это может вызвать пользовательский Интерфейс для элемента потерять его визуальное состояние.  Например элемент, который выбран в данный момент и/или имеет фокус, в котором перемещения достигается за счет «Удалить», а затем «Добавить» теряет фокус и нельзя будет выбрать.

    Platform.Collections.Vector\<T > использует IObservableVector\<T > и это же ограничение. Если поддержка для действия «Перемещение» требуется использовать **INotifyCollectionChanged** интерфейс.  Коллекция ObservableCollection .NET\<T > класс использует **INotifyCollectionChanged**.

- [IKeyIndexMapping](/uwp/api/microsoft.ui.xaml.controls.ikeyindexmapping)

  - Если уникальный идентификатор, можно связать с каждым элементом.  Рекомендуется при использовании «Сброс» в качестве действие изменения коллекции.

    Позволяет элементу управления, очень эффективно восстановление существующего пользовательского интерфейса после получения жестких действие «Сбросить» как часть **INotifyCollectionChanged** или **IObservableVector** событий. После получения Сброс элемент управления будет использовать указанный уникальный идентификатор должен быть сопоставлен текущие данные и элементы, которые уже создали. Без ключа для сопоставления индекса элемента управления бы предположить, что ему следует начать с нуля при создании пользовательского интерфейса для данных.

Интерфейсы, перечисленные выше, отличный от IKeyIndexMapping, предоставляют та же проблема при ItemsRepeater, как в ListView и GridView.


Следующие интерфейсы ItemsSource включить специальные функции в элементах управления ListView и GridView, но в настоящее время не оказывают влияния на ItemsRepeater:

- [ISupportIncrementalLoading](/uwp/api/windows.ui.xaml.data.isupportincrementalloading)
- [IItemsRangeInfo](/uwp/api/windows.ui.xaml.data.iitemsrangeinfo)
- [ISelectionInfo](/uwp/api/windows.ui.xaml.data.iselectioninfo)

> [!TIP]
> Ждем ваших отзывов! Сообщите нам свое мнение на [проекта GitHub библиотека пользовательского интерфейса Windows](https://github.com/Microsoft/microsoft-ui-xaml/issues). Рассмотрите возможность добавления своими мыслями на существующие предложения таких как [#374](https://github.com/Microsoft/microsoft-ui-xaml/issues/374): Добавьте поддержку постепенной загрузкой ItemsRepeater.

Альтернативой для добавочной загрузки данных, как пользователь прокручивает вверх или вниз будет наблюдать положение ScrollViewer окна просмотра и загрузки данных по мере просмотра области памяти.

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

Элементы отображаются с [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) упорядочены по [макета](/uwp/api/microsoft.ui.xaml.controls.layout) объект, который управляет определения размеров и положения его дочерних элементов. При использовании с ItemsRepeater, объект макета обеспечивающее виртуализацию пользовательского интерфейса. Макеты являются [StackLayout](/uwp/api/microsoft.ui.xaml.controls.stacklayout) и [UniformGridLayout](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout). По умолчанию ItemsRepeater использует StackLayout с вертикальной ориентацией.

### <a name="stacklayout"></a>StackLayout

[StackLayout](/uwp/api/microsoft.ui.xaml.controls.stacklayout) выравнивает элементы в одну линию, вы можете ориентировать горизонтально или вертикально.

Можно задать [интервал](/en-us/uwp/api/microsoft.ui.xaml.controls.stacklayout.spacing) свойство, чтобы отрегулировать величину пространства между элементами. Интервал применяется в направлении макета [ориентации](/uwp/api/microsoft.ui.xaml.controls.stacklayout.orientation).

![Стек макета интервал](images/stack-layout.png)

В этом примере показано, как присвоить свойство ItemsRepeater.Layout StackLayout с горизонтальной ориентации и интервалы 8 пикселей.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<muxc:ItemsRepeater ItemsSource="{x:Bind Items}" ItemTemplate="{StaticResource MyTemplate}">
    <muxc:ItemsRepeater.Layout>
        <muxc:StackLayout Orientation="Horizontal" Spacing="8"/>
    </muxc:ItemsRepeater.Layout>
</muxc:ItemsRepeater>
```

### <a name="uniformgridlayout"></a>UniformGridLayout

[UniformGridLayout](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout) размещает элементы последовательно в макете упаковки. Элементы расположены в порядке слева направо при [ориентации](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.orientation) — **горизонтальной**и разместить сверху вниз при Ориентация **вертикальной**. Каждый элемент одинаковыми.

![Одинаковые интервалы макета сетки](images/uniform-grid-layout.png)

Число элементов в каждой строке горизонтальном расположении зависит от ширины минимальный элемент. Число элементов в каждом столбце вертикальный макет влияют высота минимальный элемент.

- Можно явно указать минимальный размер с помощью параметра [MinItemHeight](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.minitemheight) и [MinItemWidth](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.minitemwidth) свойства.
- Если не указать минимальный размер, полученный размер первого элемента считается минимальный размер каждого элемента.

Можно также задать минимальный интервал для макета для включения между строками и столбцами, задав [MinColumnSpacing](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.mincolumnspacing) и [MinRowSpacing](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.minrowspacing) свойства.

![Изменение размера универсальный сетки и интервалов](images/uniform-grid-sizing-spacing.png)

После число, если элементы в строке или столбце было определено в зависимости от минимальный размер элемента и интервалы, может возникнуть неиспользуемого места после последнего элемента в строке или столбце (как показано на предыдущем рисунке). Можно указать ли все лишнее пространство учитывается, используемого для увеличения размера каждого элемента или создать дополнительное пространство между элементами. Это поведение управляется [ItemsStretch](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.itemsstretch) и [ItemsJustification](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.itemsjustification) свойства.

Можно задать [ItemsStretch](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.itemsstretch) свойство, чтобы указать, как увеличивается размер элемента до заполнения всего неиспользуемого пространства.

Список доступных значений. Определения предполагается значение по умолчанию **ориентации** из **горизонтальной**.

- **Нет**. Дополнительного места неиспользуемых в конце строки. Это значение используется по умолчанию.
- **Заливка**: Элементы получают дополнительную ширину израсходует все доступное пространство (высота, если по вертикали).
- **Универсальный код**: Элементы являются заданной дополнительную ширину израсходует все доступное пространство и дополнительных высота чтобы сохранить пропорции (высоты и ширины меняются местами, если по вертикали).

На этом изображении показаны последствия **ItemsStretch** значения в горизонтальном расположении.

![Универсальный код сетки элемента stretch](images/uniform-grid-item-stretch.png)

Когда **ItemsStretch** — **None**, можно задать [ItemsJustification](/uwp/api/microsoft.ui.xaml.controls.uniformgridlayout.itemsjustification) свойство, чтобы указать, как дополнительное пространство используется для выравнивания элементов.

Список доступных значений. Определения предполагается значение по умолчанию **ориентации** из **горизонтальной**.

- **Запуск**: Элементы выравниваются с начала строки. Дополнительного места неиспользуемых в конце строки. Это значение используется по умолчанию.
- **Center**: Элементы выравниваются по центру строки. Дополнительное пространство делится поровну, в начале и конце строки.
- **Конец**: Элементы выравниваются с концом строки. Дополнительного места неиспользуемых в начале строки.
- **SpaceAround**: Элементы распределяются равномерно. Равное количество места добавляется до и после каждого элемента.
- **SpaceBetween**: Элементы распределяются равномерно. Равное количество места добавляется между каждым элементом. Пространство не добавляется в начале и конце строки.
- **SpaceEvenly**: Элементы равномерно распределены с равное количество пространства между каждым элементом и в начале и конце строки.

На этом изображении показаны последствия **ItemsStretch** значения в макете по вертикали (применяется к столбцам, а не строк).

![Универсальный код сетки элемента обоснование](images/uniform-grid-item-justification.png)

> [!TIP]
> **ItemsStretch** свойство влияет на _мер_ передачи макета. **ItemsJustification** свойство влияет на _упорядочить_ передачи макета.

В этом примере демонстрируется задание **ItemsRepeater.Layout** свойства **UniformGridLayout**.

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

При размещении элементов в [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), может потребоваться выполнить определенное действие, когда элемент отображается или останавливает показывается, таких как запуск асинхронной операции загрузки содержания, свяжите элемент с механизмом для отслеживания выделения, или Остановите некоторые фоновой задачи.

В элементе управления виртуализации нельзя полагаться на события Loaded/Unloaded потому, что элемент не может быть удален из динамическом визуальном дереве при его после перезапуска. Вместо этого другие события, позволяющих управлять жизненным циклом элементов. На этой схеме показан жизненный цикл элемента в ItemsRepeater, а также при создании связанных событий.

![Схема событий жизненного цикла](images/items-repeater-lifecycle.png)

- [**ElementPrepared** ](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.elementprepared) происходит каждый раз, элемент становится готовой к использованию. Эта операция выполняется для только что созданный элемент как элемент, который уже существует и используется повторно из очереди повторный запуск.
- [**ElementClearing** ](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.elementclearing) возникает непосредственно поняли, каждый раз элемент был отправлен повторный запуск очереди, например когда она находится за пределами диапазона элементов.
- [**ElementIndexChanged** ](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.elementindexchanged
) возникает для каждого выполненных UIElement с измененной индекс элемента, он представляет. Например когда другой элемент добавляется или удаляется в источнике данных, индекс для элементов, идущих после порядковым получать это событие.

В этом примере показано, как использовать эти события для присоединения настаиваемую службы для отслеживания Выбор элементов в пользовательский элемент управления, который использует ItemsRepeater для отображения элементов.

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

## <a name="sorting-filtering-and-resetting-the-data"></a>Сортировка, фильтрация и повторная инициализация данных

При выполнении действия, такие как фильтрацию или сортировку набора данных, обычно может по сравнению с предыдущим набором данных к новым данным, а вы выданный детализированные изменение уведомлений через [INotifyCollectionChanged](/uwp/api/windows.ui.xaml.interop.inotifycollectionchanged). Тем не менее, часто бывает проще полностью заменить старые данные новыми данными и активировать уведомление изменения коллекции с помощью [Сброс](/uwp/api/windows.ui.xaml.interop.notifycollectionchangedaction) действия вместо этого.

Как правило Сброс вызывает в элементе управления, для освобождения существующих дочерних элементов и начать заново, создание пользовательского интерфейса с самого начала в позиции прокрутки 0, поскольку в нем не знают точно как данные, которые были изменены во время сброса.

Тем не менее, если назначен коллекции ItemsSource поддерживает уникальные идентификаторы, реализовав [IKeyIndexMapping](/uwp/api/microsoft.ui.xaml.controls.ikeyindexmapping) интерфейс, можно быстро определить ItemsRepeater:

- многократно используемые элементы интерфейса пользователя UIElement для данных, которые существовали до и после сброса
- ранее видимые элементы, которые были удалены
- добавлены новые элементы, которые будут видны

Это позволяет ItemsRepeater избежать начинать все с позиции прокрутки 0. Он также позволяет он быстро восстановить UIElements для данных, которые не изменяются в сброса, что приводит к повышению производительности.

В этом примере показано, как отобразить список элементов в вертикальном столбце где _MyItemsSource_ является пользовательский источник данных, служащий оболочкой базовый список элементов. Он предоставляет _данных_ свойство, которое может использоваться повторно назначить новый список для использования в качестве источника элементов, который затем активирует Сброс.

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

## <a name="create-a-custom-collection-control"></a>Создание пользовательской коллекции элемента управления

Можно использовать [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) Создание пользовательской коллекции элемента управления с его собственного типа элемента управления для представления каждого элемента.

> [!NOTE]
> Это похоже на использование **ItemsControl**, но вместо наследования от **ItemsControl** и разместив **ItemsPresenter** в шаблоне элемента управления, производного от  **Элемент управления** и вставить **ItemsRepeater** в шаблоне элемента управления. Элемент управления пользовательскую коллекцию «имеет» **ItemsRepeater** и «—» **ItemsControl**. Это означает, что необходимо явным образом выбрать какие свойства следует предоставлять вместо которой наследуемые свойства не поддерживает.

В этом примере показано, как разместить [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) в шаблоне пользовательского элемента управления с именем _MediaCollectionView_ и предоставляют его свойства.

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

## <a name="display-grouped-items"></a>Отображения сгруппированных элементов

Можно вложить [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) в [ItemTemplate](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.itemtemplate) из другой ItemsRepeater Создание вложенной виртуализации макетов. Платформа будет позволяют эффективно использовать ресурсы, сводя к минимуму ненужные реализации элементов, которые не отображаются или рядом с текущей области просмотра.

В этом примере показано, как можно отобразить список сгруппированные элементы в вертикальном столбце. Внешнее ItemsRepeater приводит к возникновению ошибки каждой группы. В шаблоне для каждой группы другой ItemsRepeater создает элементы.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<ScrollViewer>
  <muxc:ItemsRepeater ItemsSource="{x:Bind AppNotifications}"
                      Layout="{StaticResource MyGroupLayout}">
    <muxc:ItemsRepeater.ItemTemplate>
      <DataTemplate x:DataType="ExampleApp:AppNotifications">
        <!-- Group -->
        <StackPanel>
          <!-- Header -->
          TextBlock Text="{x:Bind AppTitle}"/>
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

В этом примере показан макет для приложения, которое имеет различные категории, которые можно изменить с помощью предпочтений пользователя и представлены в виде горизонтальной прокрутки списков, как показано ниже.

![Вложенные макета с помощью элемента управления repeater элементы](images/items-repeater-nested-layout.png)

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

## <a name="bringing-an-element-into-view"></a>Перенос элемент в представлении

Framework XAML уже обрабатывает перенос FrameworkElement в представление, либо при 1) получает фокус клавиатуры, либо 2) фокуса экранного диктора. Возможны другие ситуации, где необходимо явно сделать элемент видимым. Например в ответ на действия пользователя или для восстановления состояния пользовательского интерфейса после перехода между страницами.

Перенос виртуализированной элемент в представлении включает следующее:
1. Учтите, для элемента UIElement
2. Выполнять макет, чтобы убедиться, что элемент имеет допустимую позицию
3. Создайте запрос для отображения реализованного элемента в представлении

В приведенном ниже примере показано, эти действия в процессе восстановления позицию прокрутки элемента в списке плоский, по вертикали после перехода между страницами. В случае с иерархическими данными, использующими вложенных ItemsRepeaters подход, по существу, прежняя, но при этом должны выполняться на каждом уровне иерархии.

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
        var anchorBounds = anchor.TransformToVisual(this.scrollviewer).TransformBounds(new Rect(0, 0, anchor.ActualWidth, anchor.ActualHeight));
        relativeVerticalOffset = this.sv.VerticalOffset – anchorBounds.Top;
    }
}

```

## <a name="enable-accessibility"></a>Включить специальные возможности

[ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater) не поддерживает специальные возможности взаимодействия по умолчанию. В документации по [удобство использования для приложений UWP](/windows/uwp/design/usability) обеспечивает широкий набор сведений, чтобы обеспечить приложение обеспечивает взаимодействие с пользователем включительно. Если вы используете ItemsRepeater создать настраиваемый элемент управления не забудьте см. в документации на [одноранговые классы автоматизации пользовательских](/windows/uwp/design/accessibility/custom-automation-peers).

### <a name="keyboarding"></a>Работа с клавиатурой
Минимальная поддержка keyboarding для перемещения фокуса, который предоставляет ItemsRepeater основан на XAML [2D направленное перемещение между элементами для Keyboarding](/windows/uwp/design/input/focus-navigation#2d-directional-navigation-for-keyboard).

![Направление навигации](/windows/uwp/design/input/images/keyboard/directional-navigation.png)

ItemsRepeater [режим XYFocusKeyboardNavigation](/uwp/api/windows.ui.xaml.input.xyfocuskeyboardnavigationmode) — _включено_ по умолчанию. В зависимости от того, взаимодействие, рассмотрите возможность добавления поддержки для часто используемых [взаимодействия пользователей с клавиатурой](/windows/uwp/design/input/keyboard-interactions) Home, End, PageUp и PageDown.

ItemsRepeater автоматически убедитесь, что последовательность перехода по умолчанию для своих элементов (ли виртуализировать или нет) соответствует элементам присваивается в данных совпадает с порядком. По умолчанию имеет ItemsRepeater его [TabFocusNavigation](/uwp/api/windows.ui.xaml.uielement.tabfocusnavigation) свойство значение [один раз](/uwp/api/windows.ui.xaml.input.keyboardnavigationmode) вместо распространенных стандартных _локального_.

> [!NOTE]
> ItemsRepeater не запоминает автоматически последнего элемента, имеющего фокус.  Это означает, что при использовании клавиши Shift + Tab, они могут быть использованы до последнего понял элемента.

### <a name="announcing-item-x-of-y-in-screen-readers"></a>Объявление о выпуске «элемент _X_ из _Y_» в средства чтения с экрана

Вам нужно управлять, установки свойств автоматизации, таких как значения для **PositionInSet** и **SizeOfSet**и убедитесь, что они входят актуальном состоянии при добавлении элементов, переместить, удалить, и т.д.

В некоторых пользовательских макетов может отсутствовать является очевидным последовательность, чтобы порядок.  Как минимум, пользователи ожидают, что значения свойств PositionInSet и SizeOfSet, используемых средствами чтения с экрана будут соответствовать порядке следования элементов в данных (начиная с 1 для соответствия естественным, подсчет и, отсчитываемый от нуля).

Для этого рекомендуется, если одноранговый элемент автоматизации для реализации элемента управления [GetPositionInSetCore](/uwp/api/windows.ui.xaml.automation.peers.automationpeer.getpositioninsetcore) и [GetSizeOfSetCore](/uwp/api/windows.ui.xaml.automation.peers.automationpeer.getsizeofsetcore) методы и положение элемента в наборе данных отчета представленный элемент управления. Значение вычисляется только во время выполнения при обращении вспомогательные технологии и ее постоянное обновление становится не стало проблемой. Значение совпадает с порядком данных.

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

## <a name="related-articles"></a>Связанные статьи

- [Списки](lists.md)
- [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)
- [ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer)
