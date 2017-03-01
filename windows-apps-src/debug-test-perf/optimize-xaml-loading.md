---
author: mcleblanc
ms.assetid: 569E8C27-FA01-41D8-80B9-1E3E637D5B99
title: "Оптимизация разметки XAML"
description: "В случае использования сложных пользовательских интерфейсов анализ разметки XAML с целью создания объектов в памяти отнимает много времени. Вот что можно сделать, чтобы улучшить анализ разметки XAML, сократить время загрузки приложения и повысить эффективность его работы с памятью."
ms.author: markl
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 24a5696a6e835a40b9b4e800677596514b56d53b
ms.lasthandoff: 02/07/2017

---
# <a name="optimize-your-xaml-markup"></a>Оптимизация разметки XAML

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В случае использования сложных пользовательских интерфейсов анализ разметки XAML с целью создания объектов в памяти отнимает много времени. Вот что можно сделать, чтобы улучшить анализ разметки XAML, сократить время загрузки приложения и повысить эффективность его работы с памятью.

При запуске приложения загружайте только те элементы разметки XAML, которые входят в первоначальный пользовательский интерфейс. Проверьте разметку начальной страницы и убедитесь, что она не содержит ничего лишнего. Если страница ссылается на пользовательский элемент управления или ресурс, определенный в другом файле, платформа также анализирует и этот файл.

Так как в этом примере InitialPage.xaml использует один ресурс из ExampleResourceDictionary.xaml, последний необходимо проанализировать при запуске.

**InitialPage.xaml.**

```xml
<Page x:Class="ExampleNamespace.InitialPage" ...> 
    <UserControl.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="ExampleResourceDictionary.xaml"/>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </UserControl.Resources>

    <Grid>
        <TextBox Foreground="{StaticResource TextBrush}"/>
    </Grid>
</Page>
```

**ExampleResourceDictionary.xaml.**

```xml
<ResourceDictionary>
    <SolidColorBrush x:Key="TextBrush" Color="#FF3F42CC"/>

    <!--This ResourceDictionary contains many other resources that
    used in the app, but not during startup.-->
</ResourceDictionary>
```

Если ресурс используется на нескольких страницах приложения, мы рекомендуем сохранить его в файл App.xaml, чтобы избежать дублирования. Но файл App.xaml анализируется при запуске приложения, поэтому любой ресурс, используемый только на одной странице (кроме начальной), необходимо включить в локальные ресурсы такой страницы. В этом примере неправильного использования файл App.xaml содержит ресурсы, используемые только одной страницей, которая не является начальной. Это снижает скорость загрузки приложения.

**InitialPage.xaml.**

```xml
<Page ...>  <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->   
    <StackPanel>  
        <TextBox Foreground="{StaticResource InitialPageTextBrush}"/> 
    </StackPanel> 
</Page> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
```

**SecondPage.xaml.**

```xml
<Page ...>  <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
    <StackPanel> 
        <Button Content="Submit" Foreground="{StaticResource SecondPageTextBrush}" /> 
    </StackPanel> 
</Page> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
```

**App.xaml**

```xml
<Application ...> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
     <Application.Resources>  
        <SolidColorBrush x:Key="DefaultAppTextBrush" Color="#FF3F42CC"/> 
        <SolidColorBrush x:Key="InitialPageTextBrush" Color="#FF3F42CC"/> 
        <SolidColorBrush x:Key="SecondPageTextBrush" Color="#FF3F42CC"/> 
        <SolidColorBrush x:Key="ThirdPageTextBrush" Color="#FF3F42CC"/> 
    </Application.Resources> 
</Application> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
```

Чтобы исправить пример неэффективного использования выше нужно переместить файл `SecondPageTextBrush` в SecondPage.xaml, а `ThirdPageTextBrush` — в ThirdPage.xaml. `InitialPageTextBrush`  можно оставить в файле App.xaml, потому что ресурсы приложения необходимо проанализировать при запуске в любом случае.

## <a name="minimize-element-count"></a>Сокращение числа элементов

Хотя платформа XAML может отображать большое количество элементов, загрузку макета приложения и его обработку можно ускорить, используя меньшее количество элементов для достижения требуемого внешнего вида.

-   У панелей макета есть свойство [**Background**](https://msdn.microsoft.com/library/windows/apps/BR227512), поэтому нет необходимости использовать перед ними класс [**Rectangle**](https://msdn.microsoft.com/library/windows/apps/BR243371), только чтобы изменить цвет панели.

**Неэффективно.**

```xml
<Grid> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
        <Rectangle Fill="Black"/> 
    </Grid> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
```

**Эффективно.**

```xml
<Grid Background="Black"/>
```

-   Если один и тот же элемент на основе векторов используется несколько раз, эффективней будет использовать вместо него элемент [**Image**](https://msdn.microsoft.com/library/windows/apps/BR242752). Элементы на основе векторов могут быть более затратными, потому что ЦП приходится создавать каждый элемент по отдельности. Файл изображения требуется декодировать всего один раз.

## <a name="consolidate-multiple-brushes-that-look-the-same-into-one-resource"></a>Объедините несколько кистей, которые выглядят одинаково, в один ресурс

Платформа XAML пытается кэшировать часто используемые объекты, чтобы их можно было применять повторно как можно чаще. Но XAML не может определить, одинаковы ли две кисти, объявленные в разных частях разметки. В этом примере класс [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/BR242962) использован в демонстрационных целях, но гораздо важнее применять этот способ к классу [**GradientBrush**](https://msdn.microsoft.com/library/windows/apps/BR210068).

**Неэффективно.**

```xml
<Page ... > <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
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
</Page> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
```

Также проверьте кисти, использующие предопределенные цвета: `"Orange"` и `"#FFFFA500"` — это один и тот же цвет. Чтобы устранить дублирование, определите кисть как ресурс. Если элементы управления на других страницах используют эту же кисть, переместите ее в файл App.xaml.

**Эффективно.**

```xml
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

-   Если элемент не видно из-за того, что он прозрачный или скрыт за другими элементами, и при этом он не является частью макета, удалите его. Если первоначально элемент не отображается, но будет отображаться в последующих визуальных состояниях, присвойте параметру [**Visibility**](https://msdn.microsoft.com/library/windows/apps/BR208992) элемента значение **Collapsed** и измените значение параметра на **Visible** для соответствующих состояний. У этой эвристической процедуры могут быть исключения: значение свойства в большинстве визуальных состояний лучше присваивать элементу локально.
-   Для создания эффекта используйте составной элемент, а не несколько отдельных элементов. Результат выполнения этого примера — форма, закрашенная двумя цветами. Верхняя часть черного цвета (полученного присвоением соответствующего фона объекту класса [**Grid**](https://msdn.microsoft.com/library/windows/apps/BR242704)), а нижняя серого (полученного наложением объекта [**Rectangle**](https://msdn.microsoft.com/library/windows/apps/BR243371) полупрозрачного белого цвета в альфа-канале на объект **Grid** с черным фоном). Здесь для достижения результата заполняется 150 % пикселей.

**Неэффективно.**
    
```xml
    <Grid Background="Black"> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
        <Grid.RowDefinitions> 
            <RowDefinition Height="*"/> 
            <RowDefinition Height="*"/> 
        </Grid.RowDefinitions> 
        <Rectangle Grid.Row="1" Fill="White" Opacity=".5"/> 
    </Grid> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
```

**Эффективно.**

```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Rectangle Fill="Black"/>
        <Rectangle Grid.Row="1" Fill="#FF7F7F7F"/>
    </Grid>
```

-   У панели макета есть два варианта использования: для закрашивания области и для размещения дочерних элементов. Если элемент, который расположен ниже в z-порядке, уже закрашивает область, для этого не нужно использовать панель макета вверху. Вместо этого на ней можно просто размещать дочерние элементы. Пример:

**Неэффективно.**

```xml
    <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
    <GridView Background="Blue">  
        <GridView.ItemTemplate> 
            <DataTemplate> 
                <Grid Background="Blue"/> 
            </DataTemplate> 
        </GridView.ItemTemplate> 
    </GridView> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
```

**Эффективно.**

```xml
    <GridView Background="Blue">  
        <GridView.ItemTemplate> 
            <DataTemplate> 
                <Grid/> 
            </DataTemplate>
        </GridView.ItemTemplate>
    </GridView> 
```

Если следует выполнить проверку нажатия объекта класса [**Grid**](https://msdn.microsoft.com/library/windows/apps/BR242704), присвойте ему прозрачный фон.

-   Используйте элемент [**Border**](https://msdn.microsoft.com/library/windows/apps/BR209253) для рисования границы вокруг объекта. В этом примере объект класса [**Grid**](https://msdn.microsoft.com/library/windows/apps/BR242704) используется в качестве временной границы вокруг объекта класса [**TextBox**](https://msdn.microsoft.com/library/windows/apps/BR209683). Но все пиксели в центральной ячейке перерисованы.

**Неэффективно.**

```xml
    <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
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
    </Grid> <!-- NOTE: EXAMPLE OF INEFFICIENT CODE; DO NOT COPY-PASTE.-->
```

**Эффективно.**

```xml
    <Border BorderBrush="Blue" BorderThickness="5" Width="300" Height="45">
        <TextBox/>
    </Border>
```

-   Учитывайте поля. Два соседних элемента могут перекрываться (вероятно, случайно), если отрицательные поля одного из них переходят границы соседнего отрисованного объекта и вызывают их перерисовку.

Для визуальной диагностики используйте свойство [**DebugSettings.IsOverdrawHeatMapEnabled**](https://msdn.microsoft.com/library/windows/apps/Hh701823). Так вы можете обнаружить в сцене отрисованные объекты, о наличии которых не знали.

## <a name="cache-static-content"></a>Кэширование статического содержимого

Другая причина перерисовки — использование формы, созданной из большого количества перекрывающихся элементов. Если вы присвоили [**CacheMode**](https://msdn.microsoft.com/library/windows/apps/BR228084) значение **BitmapCache** в классе [**UIElement**](https://msdn.microsoft.com/library/windows/apps/BR208911), который содержит составную форму, платформа обработает элемент как растровое изображение, а затем будет использовать это изображение в каждом кадре вместо того, чтобы перерисовывать объекты.

**Неэффективно.**

```xml
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

```xml
<Canvas Background="White" CacheMode="BitmapCache">
    <Ellipse Height="40" Width="40" Fill="Blue"/>
    <Ellipse Canvas.Left="21" Height="40" Width="40" Fill="Blue"/>
    <Ellipse Canvas.Top="13" Canvas.Left="10" Height="40" Width="40" Fill="Blue"/>
</Canvas>
```

Обратите внимание на использование класса [**CacheMode**](https://msdn.microsoft.com/library/windows/apps/BR228084). Не используйте этот прием, если какая-либо из подформ является анимированной, так как, вероятно, потребуется повторная генерация кэша в каждом кадре, что неэффективно.

## <a name="resourcedictionaries"></a>ResourceDictionaries

ResourceDictionaries (Словари ресурсов) обычно используются для хранения ресурсов на глобальном уровне. Ресурсы, на которые приложение ссылается в нескольких местах. Например, стили, кисти, шаблоны и т. д. В целом мы оптимизировали ResourceDictionaries так, чтобы экземпляр ресурса не создавался без соответствующего запроса. Однако в некоторых случаях необходимо проявлять осторожность.

**Ресурс с атрибутом x:Name**. Ресурсы с атрибутом x:Name не получают преимуществ при оптимизации платформы. Вместо этого экземпляр ресурса создается сразу же после создания ResourceDictionary. Это происходит потому, что атрибут x:Name показывает платформе, что приложению требуется местный доступ к этому ресурсу. Поэтому платформе необходимо создать объект, на который можно создать ссылку.

**ResourceDictionaries в UserControl**. Определение ResourceDictionaries в UserControl ведет к снижению производительности. Платформа создает копию такого ResourceDictionary для каждого экземпляра UserControl. Если UserControl используется интенсивно, извлеките ResourceDictionary из UserControl и разместите его на уровне страницы.

## <a name="use-xbf2"></a>Использование XBF2

XBF2 — это двоичное представление разметки XAML, которое позволяет избежать синтаксического разбора текста во время выполнения. Оно также оптимизирует двоичный объект для создания дерева и загрузки и позволяет использовать «быстрый путь» для типов XAML, чтобы оптимизировать создание кучи и объектов, например VSM, ResourceDictionary, стили и т. д. Оно полностью сопоставлено в памяти, поэтому для загрузки и чтения страницы XAML куча не используется. Кроме того, оно уменьшает место на диске, которое занимают хранящиеся в пакете приложения (.appx) страницы XAML. XBF2 — это более компактное представление. По сравнению с файлами XAML/XBF1, оно уменьшает занимаемое место на диске на 50 %. Например, после преобразования в представление XBF2 объем ресурсов встроенного приложения «Фотографии» уменьшился на 60 % — с примерно 1 МБ (XBF1) до 400 КБ (XBF2). Также такие приложения сокращают нагрузку на ЦП на 15–20 % и на 10–15 % уменьшают размер кучи в Win32.

Встроенные элементы управления и словари XAML, предоставляемые платформой, уже полностью поддерживают XBF2. Файл проекта вашего приложения должен поддерживать TargetPlatformVersion 8.2 или выше.

Чтобы проверить наличие XBF2, откройте приложение в двоичном редакторе. При наличии XBF2 12-й и 13-й байты имеют значение «00 02».


