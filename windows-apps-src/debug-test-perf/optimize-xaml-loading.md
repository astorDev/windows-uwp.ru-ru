---
author: jwmsft
ms.assetid: 569E8C27-FA01-41D8-80B9-1E3E637D5B99
title: Оптимизация разметки XAML
description: В случае использования сложных пользовательских интерфейсов анализ разметки XAML с целью создания объектов в памяти отнимает много времени. Вот что можно сделать, чтобы улучшить анализ разметки XAML, сократить время загрузки приложения и повысить эффективность его работы с памятью.
ms.author: jimwalk
ms.date: 08/10/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 884825f2e9639f620d8db4e6110791fddf2d7e77
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "5760121"
---
# <a name="optimize-your-xaml-markup"></a>Оптимизация разметки XAML


В случае использования сложных пользовательских интерфейсов анализ разметки XAML с целью создания объектов в памяти отнимает много времени. Вот что можно сделать, чтобы улучшить анализ и время загрузки разметки XAML, а также повысить эффективность работы приложения с памятью.

При запуске приложения загружайте только те элементы разметки XAML, которые входят в первоначальный пользовательский интерфейс. Проверьте разметку на начальной странице (включая ресурсы страницы) и убедитесь, что вы не загружаете лишние элементы, которые сейчас не нужны. Эти элементы могут поступать из различных источников, таких как словари ресурсов, элементы, которые изначально свернуты, и элементы, нарисованные поверх других элементов.

Оптимизация кода XAML для повышения эффективности требует определенных компромиссов; не для всех ситуаций существуют универсальные решения. Здесь мы рассмотрим некоторые распространенные проблемы и дадим рекомендации по выбору правильных компромиссов для вашего приложения.

## <a name="minimize-element-count"></a>Сокращение числа элементов

Хотя платформа XAML может отображать большое количество элементов, загрузку макета приложения и его обработку можно ускорить, используя меньшее количество элементов для достижения требуемого внешнего вида.

Решения, которые вы принимаете о расположении элементов управления пользовательского интерфейса, повлияют на количество создаваемых при запуске приложения элементов пользовательского интерфейса. Более подробные сведения об оптимизации макета см. в разделе [Оптимизация макета XAML](optimize-your-xaml-layout.md).

Число элементов очень важно в шаблонах данных, так как каждый элемент создается заново для каждого элемента данных. Сведения об уменьшении числа элементов в списке или сетке см. в разделе *Уменьшение элементов для каждого элемента* статьи [Оптимизация пользовательского интерфейса ListView и GridView](optimize-gridview-and-listview.md).

Здесь мы рассмотрим некоторые другие способы уменьшения количества загружаемых при запуске приложения элементов.

### <a name="defer-item-creation"></a>Отложенное создание элементов

Если разметка XAML содержит элементы, которые не отображаются сразу, можно отложить загрузку этих элементов до тех пор, пока не возникнет необходимость их отобразить. Например, можно отсрочить создание невидимого содержимого (например, дополнительной вкладки в пользовательском интерфейсе). Или можно отображать элементы в представлении сетки по умолчанию, однако предоставить пользователю возможность просмотра данных списком. Можно отложить загрузку списка до тех пор, пока он не понадобится.

Используйте атрибут [x:Load](../xaml-platform/x-load-attribute.md) вместо свойства [Visibility](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.Visibility), чтобы определить, когда будет отображаться элемент. Если для видимости элемента задано свойство **Collapsed**, он будет пропущен во время прохода отрисовки, но экземпляр объекта все равно будет потреблять ресурсы памяти. Когда используется x:Load, экземпляр объекта не создается платформой до тех пор, пока он не потребуется, поэтому потребляется еще меньше ресурсов памяти. Недостаток этот метода заключается в небольшой перегрузке памяти (около 600байт), когда пользовательский интерфейс не загружается.

> [!NOTE]
> Загрузку элементов можно отложить с помощью атрибута [x:Load](../xaml-platform/x-load-attribute.md) или [x:DeferLoadStrategy](../xaml-platform/x-deferloadstrategy-attribute.md). Атрибут x:Load доступен, начиная с Windows 10 Creators Update (версия 1703, сборка SDK 15063). Для использования атрибута x:Load минимальная версия, указанная в вашем проекте Visual Studio, должна равняться *Windows 10 Creators Update (10.0, сборка 15063)*. Для более ранних версий используйте x: DeferLoadStrategy.

В следующих примерах показано различие в количестве элементов и использовании памяти при использовании разных техник скрытия элементов пользовательского интерфейса. ListView и GridView, содержащие идентичные элементы, помещаются в корневую сетку страницы. ListView не отображается, а GridView отображается. XAML-код в каждом из этих примеров создает один и тот же интерфейс на экране. Мы используем [инструменты профилирования и контроля производительности](tools-for-profiling-and-performance.md) Visual Studio, чтобы проверить количество элементов и использование памяти.

#### <a name="option-1---inefficient"></a>Вариант 1 — неэффективный

Здесь ListView загружается, но не отображается, так как его ширина равна 0. ListView и все его дочерние элементы создаются в визуальном дереве и загружаются в память.

```xaml
<!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <ListView x:Name="List1" Width="0">
        <ListViewItem>Item 1</ListViewItem>
        <ListViewItem>Item 2</ListViewItem>
        <ListViewItem>Item 3</ListViewItem>
        <ListViewItem>Item 4</ListViewItem>
        <ListViewItem>Item 5</ListViewItem>
        <ListViewItem>Item 6</ListViewItem>
        <ListViewItem>Item 7</ListViewItem>
        <ListViewItem>Item 8</ListViewItem>
        <ListViewItem>Item 9</ListViewItem>
        <ListViewItem>Item 10</ListViewItem>
    </ListView>

    <GridView x:Name="Grid1">
        <GridViewItem>Item 1</GridViewItem>
        <GridViewItem>Item 2</GridViewItem>
        <GridViewItem>Item 3</GridViewItem>
        <GridViewItem>Item 4</GridViewItem>
        <GridViewItem>Item 5</GridViewItem>
        <GridViewItem>Item 6</GridViewItem>
        <GridViewItem>Item 7</GridViewItem>
        <GridViewItem>Item 8</GridViewItem>
        <GridViewItem>Item 9</GridViewItem>
        <GridViewItem>Item 10</GridViewItem>
    </GridView>
</Grid>
```

Интерактивное визуальное дерево с загруженным элементом управления ListView. Общее количество элементов на странице— 89.

![Визуальное дерево с представлением списка](images/visual-tree-1.png)

ListView и его дочерние элементы загружаются в память.

![Визуальное дерево с представлением списка](images/memory-use-1.png)

#### <a name="option-2---better"></a>Вариант 2— лучше

Здесь видимость ListView имеет значение "свернуто" (другой XAML-код идентичен оригиналу). ListView создается в визуальном дереве, но его дочерние элементы— нет. Тем не менее они загружаются в память, поэтому использование памяти идентично предыдущему примеру.

```xaml
    <ListView x:Name="List1" Visibility="Collapsed">
```

Интерактивное визуальное дерево со свернутым элементом управления ListView. Общее количество элементов на странице— 46.

![Визуальное дерево со свернутым представлением списка](images/visual-tree-2.png)

ListView и его дочерние элементы загружаются в память.

![Визуальное дерево с представлением списка](images/memory-use-1.png)

#### <a name="option-3---most-efficient"></a>Вариант 3— наиболее эффективен

Здесь ListView имеет значение атрибута x:Load **False** (другой XAML-код совпадает с исходным). ListView не создается в визуальном дереве и не загружается в память при запуске.

```xaml
    <ListView x:Name="List1" Visibility="Collapsed" x:Load="False">
```

Интерактивное визуальное дерево с незагруженным элементом управления ListView. Общее количество элементов на странице— 45.

![Визуальное дерево с незагруженным представлением списка](images/visual-tree-3.png)

ListView и его дочерние элементы не загружаются в память.

![Визуальное дерево с представлением списка](images/memory-use-3.png)

> [!NOTE]
> Количество элементов и использование памяти в этих примерах очень малы и показаны только для того, чтобы продемонстрировать концепцию. В этих примерах расходы на использование x:Load выше, чем экономия памяти, поэтому приложение ничего не выигрывает. Следует использовать со своим приложением инструменты профилирования, чтобы установить, будет ли приложению польза от отложенной загрузки.

### <a name="use-layout-panel-properties"></a>Использование свойств панели макета

У панелей макета есть свойство [Background](https://msdn.microsoft.com/library/windows/apps/BR227512), поэтому перед ними не нужно использовать класс [Rectangle](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle), только чтобы изменить цвет панели.

**Неэффективно**

```xaml
<!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE. -->
<Grid>
    <Rectangle Fill="Black"/>
</Grid>
```

**Эффективно**

```xaml
<Grid Background="Black"/>
```

Панели макета также имеют встроенные свойства границ, поэтому вокруг панели макета не нужно помещать элемент [Граница](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.border). См. подробные сведения и примеры в разделе [Оптимизация макета XAML](optimize-your-xaml-layout.md).

### <a name="use-images-in-place-of-vector-based-elements"></a>Использование изображений вместо векторных элементов

Если один и тот же элемент на основе векторов используется несколько раз, эффективней будет использовать вместо него элемент [Image](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image). Элементы на основе векторов могут быть более затратными, потому что ЦП приходится создавать каждый элемент по отдельности. Файл изображения требуется декодировать всего один раз.

## <a name="optimize-resources-and-resource-dictionaries"></a>Оптимизация ресурсов и словарей ресурсов

Как правило, [словари ресурсов](../design/controls-and-patterns/resourcedictionary-and-xaml-resource-references.md) используются для сохранения на определенном глобальном уровне ресурсов, на которые вы хотите сослаться в нескольких местах вашего приложения. Например, стили, кисти, шаблоны и т. д.

В целом мы оптимизировали [ResourceDictionaries](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary) так, чтобы экземпляр ресурса не создавался без соответствующего запроса. Однако есть ситуации, которых следует избегать, чтобы не создавать экземпляры ресурсов без лишней необходимости.

### <a name="resources-with-xname"></a>Ресурсы с x:Name

Используйте [атрибут x:Key](../xaml-platform/x-key-attribute.md) для создания ссылки на ресурсы. Ресурсы с атрибутом [x:Name](../xaml-platform/x-name-attribute.md) не получают преимуществ при оптимизации платформы. Вместо этого экземпляр ресурса создается сразу же после создания ResourceDictionary. Это происходит потому, что атрибут x:Name показывает платформе, что приложению требуется местный доступ к этому ресурсу. Поэтому платформе необходимо создать объект, на который можно создать ссылку.

### <a name="resourcedictionary-in-a-usercontrol"></a>ResourceDictionary в UserControl

Определение ResourceDictionary в [UserControl](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.usercontrol) ведет к снижению производительности. Платформа создает копию такого ResourceDictionary для каждого экземпляра UserControl. Если UserControl используется интенсивно, извлеките ResourceDictionary из UserControl и разместите его на уровне страницы.

### <a name="resource-and-resourcedictionary-scope"></a>Ресурс и область ResourceDictionary

Если страница ссылается на пользовательский элемент управления или ресурс, определенный в другом файле, платформа анализирует и этот файл.

Поскольку в этом примере _InitialPage.xaml_ использует один ресурс из _ExampleResourceDictionary.xaml_, последний _необходимо_ проанализировать при запуске.

**InitialPage.xaml.**

```xaml
<Page x:Class="ExampleNamespace.InitialPage" ...>
    <Page.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="ExampleResourceDictionary.xaml"/>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Page.Resources>

    <Grid>
        <TextBox Foreground="{StaticResource TextBrush}"/>
    </Grid>
</Page>
```

**ExampleResourceDictionary.xaml.**

```xaml
<ResourceDictionary>
    <SolidColorBrush x:Key="TextBrush" Color="#FF3F42CC"/>

    <!--This ResourceDictionary contains many other resources that
        are used in the app, but are not needed during startup.-->
</ResourceDictionary>
```

Если ресурс используется на нескольких страницах приложения, мы рекомендуем сохранить его в файл _App.xaml_, чтобы избежать дублирования. Но файл _App.xaml_ анализируется при запуске приложения, поэтому любой ресурс, используемый только на одной странице (кроме начальной), необходимо включить в локальные ресурсы такой страницы. В этом примере неправильного использования файл _App.xaml_ содержит ресурсы, используемые только одной страницей, которая не является начальной. Это снижает скорость загрузки приложения.

**App.xaml**

```xaml
<!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE. -->
<Application ...>
     <Application.Resources>
        <SolidColorBrush x:Key="DefaultAppTextBrush" Color="#FF3F42CC"/>
        <SolidColorBrush x:Key="InitialPageTextBrush" Color="#FF3F42CC"/>
        <SolidColorBrush x:Key="SecondPageTextBrush" Color="#FF3F42CC"/>
        <SolidColorBrush x:Key="ThirdPageTextBrush" Color="#FF3F42CC"/>
    </Application.Resources>
</Application>
```

**InitialPage.xaml.**

```xaml
<!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE. -->
<Page x:Class="ExampleNamespace.InitialPage" ...>
    <StackPanel>
        <TextBox Foreground="{StaticResource InitialPageTextBrush}"/>
    </StackPanel>
</Page>
```

**SecondPage.xaml.**

```xaml
<!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE. -->
<Page x:Class="ExampleNamespace.SecondPage" ...>
    <StackPanel>
        <Button Content="Submit" Foreground="{StaticResource SecondPageTextBrush}" />
    </StackPanel>
</Page>
```

Чтобы сделать сценарий в этом примере более эффективным, нужно переместить файл `SecondPageTextBrush` в _SecondPage.xaml_, а `ThirdPageTextBrush`— в _ThirdPage.xaml_. `InitialPageTextBrush` можно оставить в файле _App.xaml_, потому что ресурсы приложения необходимо проанализировать при запуске в любом случае.

### <a name="consolidate-multiple-brushes-that-look-the-same-into-one-resource"></a>Объедините несколько кистей, которые выглядят одинаково, в один ресурс

Платформа XAML пытается кэшировать часто используемые объекты, чтобы их можно было применять повторно как можно чаще. Но XAML не может определить, одинаковы ли две кисти, объявленные в разных частях разметки. В этом примере класс [SolidColorBrush](https://msdn.microsoft.com/library/windows/apps/BR242962) использован в демонстрационных целях, но гораздо важнее применять этот способ к классу [GradientBrush](https://msdn.microsoft.com/library/windows/apps/BR210068). Также проверьте кисти, использующие предопределенные цвета, например `"Orange"` и `"#FFFFA500"`— это один и тот же цвет.

**Неэффективно.**

```xaml
<!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE. -->
<Page ... >
    <StackPanel>
        <TextBlock>
            <TextBlock.Foreground>
                <SolidColorBrush Color="#FFFFA500"/>
            </TextBlock.Foreground>
        </TextBox>
        <Button Content="Submit">
            <Button.Foreground>
                <SolidColorBrush Color="#FFFFA500"/>
            </Button.Foreground>
        </Button>
    </StackPanel>
</Page>
```

Чтобы устранить дублирование, определите кисть как ресурс. Если элементы управления на других страницах используют эту же кисть, переместите ее в файл _App.xaml_.

**Эффективно.**

```xaml
<Page ... >
    <Page.Resources>
        <SolidColorBrush x:Key="BrandBrush" Color="#FFFFA500"/>
    </Page.Resources>

    <StackPanel>
        <TextBlock Foreground="{StaticResource BrandBrush}" />
        <Button Content="Submit" Foreground="{StaticResource BrandBrush}" />
    </StackPanel>
</Page>
```

## <a name="minimize-overdrawing"></a>Сокращение перерисовки

Перерисовка происходит, когда одни и те же пиксели экрана используются для отрисовки нескольких объектов. Обратите внимание, что иногда приходится искать компромисс между выполнением этого руководства и сокращением количества элементов.

Для визуальной диагностики используйте свойство [**DebugSettings.IsOverdrawHeatMapEnabled**](https://msdn.microsoft.com/library/windows/apps/Hh701823). Так вы можете обнаружить в сцене отрисованные объекты, о наличии которых не знали.

### <a name="transparent-or-hidden-elements"></a>Прозрачные или скрытые элементы

Если элемент не видно из-за того, что он прозрачный или скрыт за другими элементами, и при этом он не является частью макета, удалите его. Если элемент не отображается в первоначальном визуальном состоянии, но отображается в других визуальных состояниях, отрегулируйте его состояние с помощью x:Load или присвойте параметру [Visibility](https://msdn.microsoft.com/library/windows/apps/BR208992) для элемента значение **Collapsed** и измените значение параметра на **Visible** для соответствующих состояний. У этой эвристической процедуры могут быть исключения: значение свойства в большинстве визуальных состояний лучше присваивать элементу локально.

### <a name="composite-elements"></a>Составные элементы

Для создания эффекта используйте составной элемент, а не несколько отдельных элементов. Результат выполнения этого примера— форма, закрашенная двумя цветами. Верхняя часть черного цвета (полученного присвоением соответствующего фона объекту класса [Grid](https://msdn.microsoft.com/library/windows/apps/BR242704)), а нижняя серого (полученного наложением объекта [Rectangle](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) полупрозрачного белого цвета в альфа-канале на объект **Grid** с черным фоном). Здесь для достижения результата заполняется 150 % пикселей.

**Неэффективно.**

```xaml
<!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE. -->
<Grid Background="Black">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <Rectangle Grid.Row="1" Fill="White" Opacity=".5"/>
</Grid>
```

**Эффективно.**

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <Rectangle Fill="Black"/>
    <Rectangle Grid.Row="1" Fill="#FF7F7F7F"/>
</Grid>
```

### <a name="layout-panels"></a>Панели макета

У панели макета есть два варианта использования: для закрашивания области и для размещения дочерних элементов. Если элемент, который расположен ниже в z-порядке, уже закрашивает область, для этого не нужно использовать панель макета вверху. Вместо этого на ней можно просто размещать дочерние элементы. Пример:

**Неэффективно.**

```xaml
<!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE. -->
<GridView Background="Blue">
    <GridView.ItemTemplate>
        <DataTemplate>
            <Grid Background="Blue"/>
        </DataTemplate>
    </GridView.ItemTemplate>
</GridView>
```

**Эффективно.**

```xaml
<GridView Background="Blue">
    <GridView.ItemTemplate>
        <DataTemplate>
            <Grid/>
        </DataTemplate>
    </GridView.ItemTemplate>
</GridView>
```

Если следует выполнить проверку нажатия объекта класса [Grid](https://msdn.microsoft.com/library/windows/apps/BR242704), присвойте ему прозрачный фон.

### <a name="borders"></a>Границы

Используйте элемент [Border](https://msdn.microsoft.com/library/windows/apps/BR209253) для рисования границы вокруг объекта. В этом примере объект класса [Grid](https://msdn.microsoft.com/library/windows/apps/BR242704) используется в качестве временной границы вокруг объекта класса [TextBox](https://msdn.microsoft.com/library/windows/apps/BR209683). Но все пиксели в центральной ячейке перерисованы.

**Неэффективно.**

```xaml
<!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE. -->
<Grid Background="Blue" Width="300" Height="45">
    <Grid.RowDefinitions>
        <RowDefinition Height="5"/>
        <RowDefinition/>
        <RowDefinition Height="5"/>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="5"/>
        <ColumnDefinition/>
        <ColumnDefinition Width="5"/>
    </Grid.ColumnDefinitions>
    <TextBox Grid.Row="1" Grid.Column="1"></TextBox>
</Grid>
```

**Эффективно.**

```xaml
 <Border BorderBrush="Blue" BorderThickness="5" Width="300" Height="45">
     <TextBox/>
</Border>
```

### <a name="margins"></a>Поля

Учитывайте поля. Два соседних элемента могут перекрываться (вероятно, случайно), если отрицательные поля одного из них переходят границы соседнего отрисованного объекта и вызывают их перерисовку.

### <a name="cache-static-content"></a>Кэширование статического содержимого

Другая причина перерисовки — использование формы, созданной из большого количества перекрывающихся элементов. Если вы присвоили [CacheMode](https://msdn.microsoft.com/library/windows/apps/BR228084) значение **BitmapCache** в классе [UIElement](https://msdn.microsoft.com/library/windows/apps/BR208911), который содержит составную форму, платформа обработает элемент как растровое изображение, а затем будет использовать это изображение в каждом кадре вместо того, чтобы перерисовывать объекты.

**Неэффективно.**

```xaml
<Canvas Background="White">
    <Ellipse Height="40" Width="40" Fill="Blue"/>
    <Ellipse Canvas.Left="21" Height="40" Width="40" Fill="Blue"/>
    <Ellipse Canvas.Top="13" Canvas.Left="10" Height="40" Width="40" Fill="Blue"/>
</Canvas>
```

![Диаграмма Венна с тремя сплошными кругами](images/solidvenn.png)

Вверху вы видите результирующее изображение, а вот карта перерисовки областей: Более темному красному цвету соответствуют области с большим объемом перерисовки.

![Диаграмма Венна с демонстрацией перекрывающихся областей](images/translucentvenn.png)

**Эффективно.**

```xaml
<Canvas Background="White" CacheMode="BitmapCache">
    <Ellipse Height="40" Width="40" Fill="Blue"/>
    <Ellipse Canvas.Left="21" Height="40" Width="40" Fill="Blue"/>
    <Ellipse Canvas.Top="13" Canvas.Left="10" Height="40" Width="40" Fill="Blue"/>
</Canvas>
```

Обратите внимание на использование класса [CacheMode](https://msdn.microsoft.com/library/windows/apps/BR228084). Не используйте этот прием, если какая-либо из подформ является анимированной, так как, вероятно, потребуется повторная генерация кэша в каждом кадре, что неэффективно.

## <a name="use-xbf2"></a>Использование XBF2

XBF2 — это двоичное представление разметки XAML, которое позволяет избежать синтаксического разбора текста во время выполнения. Оно также оптимизирует двоичный объект для создания дерева и загрузки и позволяет использовать «быстрый путь» для типов XAML, чтобы оптимизировать создание кучи и объектов, например VSM, ResourceDictionary, стили и т. д. Оно полностью сопоставлено в памяти, поэтому для загрузки и чтения страницы XAML куча не используется. Кроме того, оно уменьшает место на диске, которое занимают хранящиеся в пакете приложения (.appx) страницы XAML. XBF2 — это более компактное представление. По сравнению с файлами XAML/XBF1, оно уменьшает занимаемое место на диске на 50%. Например, после преобразования в представление XBF2 объем ресурсов встроенного приложения «Фотографии» уменьшился на 60% — с примерно 1 МБ (XBF1) до 400 КБ (XBF2). Также такие приложения сокращают нагрузку на ЦП на 15–20% и на 10–15% уменьшают размер кучи в Win32.

Встроенные элементы управления и словари XAML, предоставляемые платформой, уже полностью поддерживают XBF2. Файл проекта вашего приложения должен поддерживать TargetPlatformVersion 8.2 или выше.

Чтобы проверить наличие XBF2, откройте приложение в двоичном редакторе. При наличии XBF2 12-й и 13-й байты имеют значение «00 02».

## <a name="related-articles"></a>Статьи по теме

- [Рекомендации по повышению производительности запуска приложения](best-practices-for-your-app-s-startup-performance.md)
- [Оптимизация макета XAML](optimize-your-xaml-layout.md)
- [Оптимизация пользовательского интерфейса ListView и GridView](optimize-gridview-and-listview.md)
- [Инструменты для профилирования и производительности](tools-for-profiling-and-performance.md)
