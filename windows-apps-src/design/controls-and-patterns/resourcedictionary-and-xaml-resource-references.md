---
Description: Explains how to define a ResourceDictionary element and keyed resources, and how XAML resources relate to other resources that you define as part of your app or app package.
MS-HAID: dev\_ctrl\_layout\_txt.resourcedictionary\_and\_xaml\_resource\_references
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: Ссылки на ресурсы ResourceDictionary и XAML
ms.assetid: E3CBFA3D-6AF5-44E1-B9F9-C3D3EA8A25CE
label: ResourceDictionary and XAML resource references
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 51461df47fe92c296fee198a6f2ed1c34e833cd7
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7971527"
---
# <a name="resourcedictionary-and-xaml-resource-references"></a>Ссылки на ресурсы ResourceDictionary и XAML

 

Вы можете определить пользовательский интерфейс или ресурсы для вашего приложения с помощью XAML. Обычно ресурсы представляют собой определения некоторого объекта, который предполагается использовать несколько раз. Чтобы к ресурсу XAML можно было обратиться позже, вам необходимо указать ключ, который выступает в качестве его имени. На ресурс можно ссылаться из любого места приложения или с любой используемой им страницы XAML. Вы можете определить свои ресурсы с помощью элемента [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) из среды выполнения Windows XAML. Затем вы можете ссылаться на ресурсы с помощью расширения разметки [StaticResource](../../xaml-platform/staticresource-markup-extension.md) или [ThemeResource](../../xaml-platform/themeresource-markup-extension.md).

К элементам XAML, которые в первую очередь имеет смысл объявлять как ресурсы XAML, относятся [Style](https://msdn.microsoft.com/library/windows/apps/br208849), [ControlTemplate](https://msdn.microsoft.com/library/windows/apps/br209391), компоненты анимации и подклассы [Brush](/uwp/api/Windows.UI.Xaml.Media.Brush). В данной статье приводятся сведения о том, как определить [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) и соответствующие ресурсы и как ресурсы XAML соотносятся с другими ресурсами, которые вы определяете как часть своего приложения или пакета приложения. Кроме того, здесь описываются такие дополнительные функции словаря, как [MergedDictionaries](https://msdn.microsoft.com/library/windows/apps/br208801) и [ThemeDictionaries](https://msdn.microsoft.com/library/windows/apps/br208807).

**Необходимые условия**

Предполагается, что вы знакомы с разметкой XAML и прочитали [обзор XAML](https://msdn.microsoft.com/library/windows/apps/mt185595).

## <a name="define-and-use-xaml-resources"></a>Определение и использование ресурсов XAML

Ресурсы XAML — это объекты, ссылка на которые из разметки осуществляется несколько раз. Ресурсы определены в [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794), обычно в отдельном файле или в верхней части страницы разметки, как показано ниже.

```XAML
<Page
    x:Class="MSDNSample.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">

    <Page.Resources>
        <x:String x:Key="greeting">Hello world</x:String>
        <x:String x:Key="goodbye">Goodbye world</x:String>
    </Page.Resources>

    <TextBlock Text="{StaticResource greeting}" Foreground="Gray" VerticalAlignment="Center"/>
</Page>
```

В этом примере:

-   `<Page.Resources>…</Page.Resources>` — определяет словарь ресурсов;
-   `<x:String>` — определяет ресурс с ключом greeting;
-   `{StaticResource greeting}` — ищет ресурс с ключом greeting, который назначен свойству [Text](https://msdn.microsoft.com/library/windows/apps/br209676) свойства [TextBlock](https://msdn.microsoft.com/library/windows/apps/br209652).

> **Примечание.**&nbsp;&nbsp;Не путайте принципы, связанные с [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794), с действием при сборке **Resource**, файлами ресурсов (.resw) и другими ресурсами, обсуждаемыми в контексте структурирования проекта кода, создающего пакет приложения.

Ресурсы необязательно должны быть строками, они могут иметь вид любого объекта, который можно предоставить для общего доступа, например стилей, шаблонов, кистей и цветов. Однако элементы управления, фигуры и другие элементы [FrameworkElement](https://msdn.microsoft.com/library/windows/apps/br208706) нельзя предоставить для общего доступа, поэтому их невозможно объявлять как повторно используемые ресурсы. Подробнее об общем доступе читайте в разделе [Ресурсы XAML должны находиться в общем доступе](#xaml-resources-must-be-shareable) далее в этой статье.

Здесь кисть и строка объявляются как ресурсы и используются элементами управления на странице.

```XAML
<Page
    x:Class="SpiderMSDN.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">

    <Page.Resources>
        <SolidColorBrush x:Key="myFavoriteColor" Color="green"/>
        <x:String x:Key="greeting">Hello world</x:String>
    </Page.Resources>

    <TextBlock Foreground="{StaticResource myFavoriteColor}" Text="{StaticResource greeting}" VerticalAlignment="Top"/>
    <Button Foreground="{StaticResource myFavoriteColor}" Content="{StaticResource greeting}" VerticalAlignment="Center"/>
</Page>
```

Всем ресурсам понадобится ключ. Обычно этот ключ представляет собой строку, определенную с помощью `x:Key=”myString”`. Тем не менее существует ряд других способов задать ключ:

-   [Style](https://msdn.microsoft.com/library/windows/apps/br208849) и [ControlTemplate](https://msdn.microsoft.com/library/windows/apps/br209391) требуют **TargetType** и будут использовать **TargetType** в качестве ключа, если [x:Key](https://msdn.microsoft.com/library/windows/apps/mt204787) не задан. В этом случае ключ фактически является объектом Type, а не строкой. (См. приведенные ниже примеры.)
-   Ресурсы [DataTemplate](https://msdn.microsoft.com/library/windows/apps/br242348) с **TargetType** будут использовать **TargetType** в качестве ключа, если [x:Key](https://msdn.microsoft.com/library/windows/apps/mt204787) не задан. В этом случае ключ фактически является объектом Type, а не строкой.
-   Вместо [x:Key](https://msdn.microsoft.com/library/windows/apps/mt204787) можно использовать [x:Name](https://msdn.microsoft.com/library/windows/apps/mt204788). Однако x:Name также создает выделенный код поля для ресурса. В результате x:Name менее эффективен по сравнению с x:Key, так как это поле необходимо инициализировать при загрузке страницы.

[Расширение разметки StaticResource](../../xaml-platform/staticresource-markup-extension.md) может получить ресурсы только с именем строки ([x:Key](https://msdn.microsoft.com/library/windows/apps/mt204787) или [x:Name](https://msdn.microsoft.com/library/windows/apps/mt204788)). Однако платформа XAML также ищет неявные ресурсы стиля (которые используют **TargetType**, а не x:Key или x:Name), когда принимает решение об использовании стиля и шаблона для элемента управления, который не установил свойства [Style](https://msdn.microsoft.com/library/windows/apps/br208743) и [ContentTemplate](https://msdn.microsoft.com/library/windows/apps/br209369) или [ItemTemplate](https://msdn.microsoft.com/library/windows/apps/br242830).

Здесь [Style](https://msdn.microsoft.com/library/windows/apps/br208849) имеет неявный ключ **typeof(Button)**, а поскольку [Button](https://msdn.microsoft.com/library/windows/apps/br209265) внизу страницы не определяет свойство [Style](https://msdn.microsoft.com/library/windows/apps/br208743), выполняется поиск стиля с ключом **typeof(Button)**:

```XAML
<Page
    x:Class="MSDNSample.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">

    <Page.Resources>
        <Style TargetType="Button">
            <Setter Property="Background" Value="Red"/>
        </Style>
    </Page.Resources>
    <Grid>
       <!-- This button will have a red background. -->
       <Button Content="Button" Height="100" VerticalAlignment="Center" Width="100"/>
    </Grid>
</Page>
```

Подробнее о неявных стилях и их использовании см. в статьях [Настройка стиля элементов управления](xaml-styles.md) и [Шаблоны элементов управления](control-templates.md).

## <a name="look-up-resources-in-code"></a>Поиск ресурсов в коде

Вы получаете доступ к участникам словаря ресурсов, как к любому другому словарю.

> [!WARNING]
> При выполнении поиска ресурсов в коде, только ресурсы в `Page.Resources` рассматриваются словарь. В отличие от [расширения разметки StaticResource](../../xaml-platform/staticresource-markup-extension.md), данный код не возвращается в словарь `Application.Resources`, если ресурсы не найдены в первом словаре.

 

В этом примере показано, как извлечь ресурс `redButtonStyle` из словаря ресурсов страницы:

```XAML
<Page
    x:Class="MSDNSample.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">

    <Page.Resources>
        <Style TargetType="Button" x:Key="redButtonStyle">
            <Setter Property="Background" Value="red"/>
        </Style>
    </Page.Resources>
</Page>
```

```CSharp
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            Style redButtonStyle = (Style)this.Resources["redButtonStyle"];
        }
    }
```

Чтобы посмотреть ресурсы всего приложения из кода, используйте **Application.Current.Resources** для получения словаря ресурсов приложения, как показано здесь.

```XAML
<Application
    x:Class="MSDNSample.App"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:SpiderMSDN">
    <Application.Resources>
        <Style TargetType="Button" x:Key="appButtonStyle">
            <Setter Property="Background" Value="red"/>
        </Style>
    </Application.Resources>

</Application>
```

```CSharp
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            Style appButtonStyle = (Style)Application.Current.Resources["appButtonStyle"];
        }
    }
```

Вы также можете добавить ресурс приложения в коде.

Существует два аспекта, которые при этом следует иметь в виду.

-   Во-первых, вам необходимо добавить ресурсы, прежде чем какая-либо страница попытается использовать ресурс.
-   Во-вторых, вы не можете добавлять ресурсы в конструкторе приложений.

Обеих проблем можно избежать, если добавить ресурс в методе [Application.OnLaunched](https://msdn.microsoft.com/library/windows/apps/br242335) следующим образом.

```CSharp
// App.xaml.cs
    
sealed partial class App : Application
{
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        Frame rootFrame = Window.Current.Content as Frame;
        if (rootFrame == null)
        {
            SolidColorBrush brush = new SolidColorBrush(Windows.UI.Color.FromArgb(255, 0, 255, 0)); // green
            this.Resources["brush"] = brush;
            // … Other code that VS generates for you …
        }
    }
}
```

## <a name="every-frameworkelement-can-have-a-resourcedictionary"></a>Каждый FrameworkElement может иметь ResourceDictionary

[FrameworkElement](https://msdn.microsoft.com/library/windows/apps/br208706) — это базовый класс, от которого наследуют элементы управления, имеющий свойство [Resources](https://msdn.microsoft.com/library/windows/apps/br208740). Таким образом, вы можете добавить локальный словарь ресурсов в любой **FrameworkElement**.

Здесь [Page](https://msdn.microsoft.com/library/windows/apps/br227503) и [Border](https://msdn.microsoft.com/library/windows/apps/br209250) имеют словари ресурсов, а также содержат ресурс с именем greeting. [TextBlock](https://msdn.microsoft.com/library/windows/apps/br209652) с именем «textBlock2» является внутри **границ**, поэтому его поиск ресурсов сначала **Border**ресурсы **страницы**ресурсов и ресурсы [приложения](https://msdn.microsoft.com/library/windows/apps/br242324) . В **TextBlock** будет отображаться значение Hola mundo.

Чтобы обратиться к ресурсам этого элемента из кода, используйте свойство [Resources](https://msdn.microsoft.com/library/windows/apps/br208740) этого элемента. Доступ к ресурсам [FrameworkElement](https://msdn.microsoft.com/library/windows/apps/br208706) в коде, а не XAML, обеспечит поиск только в этом словаре, но не в словарях родительского элемента.

```XAML
<Page
    x:Class="MSDNSample.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Page.Resources>
        <x:String x:Key="greeting">Hello world</x:String>
    </Page.Resources>
    
    <StackPanel>
        <!-- Displays "Hello world" -->
        <TextBlock x:Name="textBlock1" Text="{StaticResource greeting}"/>

        <Border x:Name="border">
            <Border.Resources>
                <x:String x:Key="greeting">Hola mundo</x:String>
            </Border.Resources>
            <!-- Displays "Hola mundo" -->
            <TextBlock x:Name="textBlock2" Text="{StaticResource greeting}"/>
        </Border>

        <!-- Displays "Hola mundo", set in code. -->
        <TextBlock x:Name="textBlock3"/>
    </StackPanel>
</Page>

```

```CSharp
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            textBlock3.Text = (string)border.Resources["greeting"];
        }
    }
```

## <a name="merged-resource-dictionaries"></a>Объединенные словари ресурсов

*Объединенный словарь ресурсов* включает один словарь ресурсов в другой, обычно в другом файле.

> **Подсказка.**&nbsp;&nbsp;Вы можете создать файл словаря ресурсов в Microsoft Visual Studio, используя функцию **Добавить &gt; Новый элемент &gt; Словарь ресурсов** в меню **Проект**.

Здесь вы определяете словарь ресурсов в отдельном XAML-файле с именем Dictionary1.xaml.

```XAML
<!-- Dictionary1.xaml -->
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:MSDNSample">

    <SolidColorBrush x:Key="brush" Color="Red"/>

</ResourceDictionary>

```

Чтобы использовать этот словарь, объедините его с словарем страницы:

```XAML
<Page
    x:Class="MSDNSample.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Page.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="Dictionary1.xaml"/>
            </ResourceDictionary.MergedDictionaries>

            <x:String x:Key="greeting">Hello world</x:String>

        </ResourceDictionary>
    </Page.Resources>

    <TextBlock Foreground="{StaticResource brush}" Text="{StaticResource greeting}" VerticalAlignment="Center"/>
</Page>
```

Дадим пояснения к этому примеру. В `<Page.Resources>` вы объявляете `<ResourceDictionary>`. Платформа XAML неявно создает словарь ресурсов, когда вы добавляете ресурсы в `<Page.Resources>`, однако в этом случае вам нужен не любой словарь ресурсов, а тот, который содержит объединенные словари.

Поэтому вы объявляете `<ResourceDictionary>`, а затем добавляете объекты в его коллекцию `<ResourceDictionary.MergedDictionaries>`. Каждая из этих записей принимает форму `<ResourceDictionary Source="Dictionary1.xaml"/>`. Чтобы добавить несколько словарей, просто добавьте запись `<ResourceDictionary Source="Dictionary2.xaml"/>` после первой записи.

После `<ResourceDictionary.MergedDictionaries>…</ResourceDictionary.MergedDictionaries>` можно также поместить в главный словарь дополнительные ресурсы. Использование ресурсов из объединенного словаря осуществляется аналогично обычному словарю. В приведенном выше примере `{StaticResource brush}` находит ресурс в дочернем/объединенном словаре (Dictionary1.xaml), а `{StaticResource greeting}` находит его ресурс в словаре главной страницы.

В последовательности поиска ресурса словарь [MergedDictionaries](https://msdn.microsoft.com/library/windows/apps/br208801) проверяется только после проверки всех ключевых ресурсов этого [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794). После поиска на этом уровне поиск достигает объединенных словарей, и каждый элемент в **MergedDictionaries** проверяется. Если существуют несколько объединенных словарей, они проверяются в порядке, обратном тому, в котором они объявлены в свойстве **MergedDictionaries**. В следующем примере, если Dictionary2.xaml и Dictionary1.xaml объявляют один и тот же ключ, первым используется ключ из Dictionary2.xaml, поскольку он является последним в наборе **MergedDictionaries**.

```XAML
<Page
    x:Class="MSDNSample.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Page.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="Dictionary1.xaml"/>
                <ResourceDictionary Source="Dictionary2.xaml"/>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Page.Resources>

    <TextBlock Foreground="{StaticResource brush}" Text="greetings!" VerticalAlignment="Center"/>
</Page>
```

В рамках области любого отдельного [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) словарь проверяется на уникальность ключа. Тем не менее эта область не расширяется на другие элементы в других файлах [MergedDictionaries](https://msdn.microsoft.com/library/windows/apps/br208801).

Чтобы создать последовательность резервных значений для ресурсов [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794), можно использовать сочетание последовательности поиска и отсутствия требования уникальности ключа в областях объединенных словарей. Например, вы можете сохранить настройки пользователя для определенного цвета кисти в последнем объединенном словаре ресурсов последовательности, используя словарь ресурсов, синхронизирующийся с состоянием приложения и данными о настройках пользователя. Однако если настройки пользователя отсутствуют, можно определить ту же строку ключа для ресурса **ResourceDictionary** в исходном файле [MergedDictionaries](https://msdn.microsoft.com/library/windows/apps/br208801), и она может послужить резервным значением. Помните, что перед проверкой объединенных словарей будет выполняться поиск любого значения, предоставленного в основном словаре ресурсов, поэтому не определяйте ресурс в основном словаре ресурсов, если вы хотите использовать метод резервных значений.

## <a name="theme-resources-and-theme-dictionaries"></a>Ресурсы темы и словари тем

[ThemeResource](../../xaml-platform/themeresource-markup-extension.md) аналогичен [StaticResource](../../xaml-platform/staticresource-markup-extension.md), однако поиск ресурсов вычисляется повторно при смене темы.

В этом примере для переднего плана [TextBlock](https://msdn.microsoft.com/library/windows/apps/br209652) устанавливается значение из текущей темы.

```XAML
<TextBlock Text="hello world" Foreground="{ThemeResource FocusVisualWhiteStrokeThemeBrush}" VerticalAlignment="Center"/>
```

Словарь тем — это особый тип объединенного словаря для ресурсов, изменяющихся в зависимости от той темы, которую пользователь использует в настоящий момент на своем устройстве. Например, в светлой теме может использоваться белая кисть, а в темной теме — темная. Кисть меняет ресурс, в котором она разрешается, но в остальном композиция элемента управления, использующего кисть в качестве ресурса, может оставаться прежней. Чтобы воспроизвести поведение смены тем в своих шаблонах и стилях, вместо использования [MergedDictionaries](https://msdn.microsoft.com/library/windows/apps/br208801) как свойства для объединения элементов в основные словари используйте свойство [ThemeDictionaries](https://msdn.microsoft.com/library/windows/apps/br208807).

Каждый элемент [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) в [ThemeDictionaries](https://msdn.microsoft.com/library/windows/apps/br208807) должен иметь значение [x:Key](https://msdn.microsoft.com/library/windows/apps/mt204787). Это значение представляет собой строку, которая называет соответствующую тему, например Default, Dark, Light или HighContrast. В общем случае `Dictionary1` и `Dictionary2` определяют ресурсы с теми же именами, но другими значениями.

Здесь используется красный текст для светлой темы и синий текст для темной темы.

```XAML
<!-- Dictionary1.xaml -->
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:MSDNSample">

    <SolidColorBrush x:Key="brush" Color="Red"/>

</ResourceDictionary>

<!-- Dictionary2.xaml -->
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:MSDNSample">

    <SolidColorBrush x:Key="brush" Color="blue"/>

</ResourceDictionary>
```

В этом примере для переднего плана [TextBlock](https://msdn.microsoft.com/library/windows/apps/br209652) устанавливается значение из текущей темы.

```XAML
<Page
    x:Class="MSDNSample.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Page.Resources>
        <ResourceDictionary>
            <ResourceDictionary.ThemeDictionaries>
                <ResourceDictionary Source="Dictionary1.xaml" x:Key="Light"/>
                <ResourceDictionary Source="Dictionary2.xaml" x:Key="Dark"/>
            </ResourceDictionary.ThemeDictionaries>
        </ResourceDictionary>
    </Page.Resources>
    <TextBlock Foreground="{StaticResource brush}" Text="hello world" VerticalAlignment="Center"/>
</Page>
```

В случае тематических словарей активный словарь, используемый для поиска ресурсов, меняется динамически всякий раз, когда для ссылки используется [расширение разметки ThemeResource](../../xaml-platform/themeresource-markup-extension.md) и система обнаруживает смену темы. Поведение поиска в системе основано на сопоставлении активной темы и [x:Key](https://msdn.microsoft.com/library/windows/apps/mt204787) из определенного тематического словаря.

Может быть полезно рассмотреть, как словари тем структурированы в стандартных ресурсах макета XAML, которые соответствуют шаблонам, используемым по умолчанию средой выполнения Windows для своих элементов управления. Откройте файлы XAML в папке \\(Program Files)\\Windows Kits\\10\\DesignTime\\CommonConfiguration\\Neutral\\UAP\\&lt;версия SDK&gt;\\Generic с помощью текстового редактора или интегрированной среды разработки. Обратите внимание на то, как словари тем сначала определяются в generic.xaml, а также на то, как каждый словарь определяет одинаковые ключи. На каждый такой ключ затем ссылаются элементы композиции в различных ключевых элементах за пределами словарей тем, которые позже определяются в XAML. Имеется также отдельный файл themeresources.xaml для макета, содержащий только ресурсы темы и дополнительные шаблоны, а не стандартные шаблоны элементов управления. Области темы дублируют области, которые видны в файле generic.xaml.

При использовании инструментов дизайна XAML для редактирования копий стилей и шаблонов инструменты дизайна извлекают разделы из словарей ресурсов дизайна XAML и размещают их как локальные копии элементов словарей дизайна XAML, являющихся частью приложения и проекта.

Дополнительную информацию, а также список ресурсов темы и системных ресурсов, которые доступны приложению, см. в статье [Ресурсы темы XAML](xaml-theme-resources.md).

## <a name="lookup-behavior-for-xaml-resource-references"></a>Логика поиска по ссылкам на ресурсы XAML

*Поведение при поиске* описывает то, как система ресурсов XAML пытается выполнить поиск ресурса XAML. Поиск выполняется при ссылке на ключ как ресурс XAML из какого-либо места XAML-кода приложения. Во-первых, поведение системы ресурсов предсказуемо во время проверки на наличие ресурса на основе области. Если ресурс не обнаружен в исходной области, она расширяется. Поведение поиска продолжается в расположениях и областях, в которых ресурс XAML может быть определен приложением или системой. Если ресурс не найден ни в одном из возможных расположений, часто выдается ошибка. Обычно в процессе разработки этих ошибок можно избежать.

Поиск по ссылкам на ресурсы XAML начинается с объекта, в котором происходит фактическое использование, и его собственного свойства [Resources](https://msdn.microsoft.com/library/windows/apps/br208740). Если в нем существует объект [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794), то выполняется проверка, нет ли в этом объекте **ResourceDictionary** элемента, который содержит запрашиваемый ключ. Этот первый уровень поиска редко бывает полезен, так как обычно ссылка на ресурс создается не в том объекте, в котором этот ресурс был определен. Собственно, зачастую свойство **Resources** не существует в этом месте. Вы можете создать ссылки на ресурсы XAML почти в любом месте XAML-кода, не ограничиваясь свойствами подклассов [FrameworkElement](https://msdn.microsoft.com/library/windows/apps/br208706).

Затем последовательность поиска проверяет следующий родительский объект в дереве объектов среды выполнения приложения. Если [FrameworkElement.Resources](https://msdn.microsoft.com/library/windows/apps/br208740) существует и содержит [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794), запрашивается элемент словаря с указанной строкой ключа. Если ресурс найден, последовательность поиска останавливается, и объект предоставляется в то расположение, в котором была создана ссылка. В ином случае поведение нахождения перемещается к следующему родительскому уровню по направлению к корню дерева объектов. Поиск продолжается рекурсивно вверх, включая поиск по всем возможным расположениям непосредственного ресурса, пока не будет достигнут корневой элемент XAML.

> **Подсказка.**&nbsp;&nbsp;Обычно все непосредственные ресурсы определяют на корневом уровне страницы для извлечения преимущества из этого поведения при поиске ресурсов и для соблюдения стиля разметки XAML.

 

Если запрошенный ресурс не найден среди непосредственных ресурсов, следующим шагом поиска будет проверка свойства [Application.Resources](https://msdn.microsoft.com/library/windows/apps/br242338). **Application.Resources** — лучшее место для расположения специфических для приложения ресурсов, ссылки на которые присутствуют на нескольких страницах в структуре навигации вашего приложения.

При поиске по ссылкам шаблоны элементов управления могут обнаруживаться еще в одном месте — в словарях тем. Словарь тем представляет собой единый XAML-файл, корнем которого является элемент [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794). Словарь тем может быть объединенным словарем из [Application.Resources](https://msdn.microsoft.com/library/windows/apps/br242338). но также может быть отдельным словарем тем шаблонного пользовательского элемента управления.

Наконец, существует поиск в ресурсах платформы. Ресурсы платформы включают в себя шаблоны элементов управления, определенные для каждой темы пользовательского интерфейса системы, которые задают внешний вид по умолчанию всех элементов управления, используемых в пользовательском интерфейсе приложения среды выполнения Windows. Кроме того, они включают набор именованных ресурсов, относящихся к системным представлениям и темам. С технической точки зрения данные ресурсы образуют элемент [MergedDictionaries](https://msdn.microsoft.com/library/windows/apps/br208801) и поэтому доступны для поиска из XAML или кода после загрузки приложения. Например, в состав ресурсов темы системы входит ресурс с именем SystemColorWindowTextColor, предоставляющий определение [Color](https://msdn.microsoft.com/library/windows/apps/hh673723), которое устанавливает соответствие между цветом текста приложения и цветом текста системных окон, задаваемым параметрами операционной системы и настройками пользователя. Другие стили XAML вашего приложения могут ссылаться на этот стиль, или же ваш код может получать поисковое значение ресурса (и преобразовывать его тип в **Color**, как в приведенном примере).

Дополнительную информацию, а также список ресурсов темы и системных ресурсов, которые доступны приложению UWP на XAML, см. в статье [Ресурсы темы XAML](xaml-theme-resources.md).

Если запрошенный ключ не найден ни в одном из этих расположений, возникает исключение (ошибка) анализа XAML. В определенных условиях исключение анализа XAML может быть исключением среды выполнения, не выявленным действием компилирования разметки XAML или средой проектирования XAML.

Исходя из многоуровневой логики поиска по словарям ресурсов, можно намеренно определить несколько элементов ресурсов с одним и тем же строковым значением в качестве ключа, если все ресурсы определены на разных уровнях. Иными словами, хотя ключи должны быть уникальными в рамках любого элемента [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794), требование уникальности не распространяется на всю последовательность поведения поиска. Во время поиска только первый успешно извлеченный объект используется для ссылки на ресурс XAML, после чего поиск останавливается. Используйте это поведение, чтобы запросить тот же ресурс XAML по ключу в различных местах XAML вашего приложения и получить при этом разные ресурсы в зависимости от области, из которой была отправлена ссылка на ресурс XAML, и поведения данного поиска.

##  <a name="forward-references-within-a-resourcedictionary"></a>Переадресация ссылки в ResourceDictionary


Ссылки на ресурсы XAML в рамках конкретного словаря ресурсов должны указывать на ресурс, уже определенный ключом. Лексически этот ресурс должен находиться перед ссылкой на ресурс. Упреждающие ссылки не могут разрешаться ссылками на ресурсы XAML. Поэтому, если вы используете ссылки на ресурс XAML из другого ресурса, вам необходимо структурировать словарь ресурсов так, чтобы ресурсы, используемые другими ресурсами, были определены первыми в словаре ресурсов.

Ресурсы, определенные на уровне приложения, не могут ссылаться на непосредственные ресурсы. Это похоже на попытку переадресации ссылки, так как ресурсы приложения фактически обрабатываются первыми (когда приложение запускается в первый раз и до загрузки содержимого любой навигационной страницы). Однако любой непосредственный ресурс может ссылаться на ресурс приложения, и это может стать полезным способом, помогающим избежать переадресации ссылок.

## <a name="xaml-resources-must-be-shareable"></a>Ресурсы XAML должны находиться в общем доступе


Чтобы объект мог существовать в [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794), этот объект должен находиться *в общем доступе*.

Общий доступ необходим, так как при создании дерева объектов приложения и его использования во время выполнения объекты не могут существовать в нескольких расположениях в дереве. Внутренне система ресурсов создает копии значений ресурсов для использования в графе объекта вашего приложения при запросе каждого из ресурсов XAML.

[ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) и XAML среды выполнения Windows в целом поддерживают следующие объекты для общего использования.

-   Стили и шаблоны ([Style](https://msdn.microsoft.com/library/windows/apps/br208849) и классы, производные от [FrameworkTemplate](https://msdn.microsoft.com/library/windows/apps/br208753))
-   Кисти и цвета (классы, производные от [Brush](/uwp/api/Windows.UI.Xaml.Media.Brush), и значения [Color](https://msdn.microsoft.com/library/windows/apps/hh673723))
-   Типы анимации, включая [Storyboard](https://msdn.microsoft.com/library/windows/apps/br210490)
-   Преобразования (классы, производные от [GeneralTransform](https://msdn.microsoft.com/library/windows/apps/br210034))
-   [Matrix](https://msdn.microsoft.com/library/windows/apps/br210127) и [Matrix3D](https://msdn.microsoft.com/library/windows/apps/br243266)
-   Значения [Point](https://msdn.microsoft.com/library/windows/apps/br225870)
-   Некоторые другие структуры, связанные с пользовательским интерфейсом, например [Thickness](https://msdn.microsoft.com/library/windows/apps/br208864) и [CornerRadius](https://msdn.microsoft.com/library/windows/apps/br242343)
-   [Встроенные типы данных в языке XAML](https://msdn.microsoft.com/library/windows/apps/mt186448)

Вы также можете использовать пользовательские типы как общедоступный ресурс, следуя необходимым шаблонам реализации. Такие классы определяются в резервном коде (или во включаемых компонентах среды выполнения), после чего экземпляры этих классов создаются в XAML как ресурс. В качестве примеров можно назвать источники данных объекта и реализации [IValueConverter](https://msdn.microsoft.com/library/windows/apps/br209903) для привязки данных.

Пользовательские типы должны иметь конструктор по умолчанию, поскольку анализатор XAML использует его для создания экземпляра класса. Пользовательские типы, используемые как ресурсы, не могут включать класс [UIElement](https://msdn.microsoft.com/library/windows/apps/br208911) в свое наследование, поскольку **UIElement** не может быть общедоступным (он всегда представляет единственный элемент пользовательского интерфейса, который существует на одной позиции в графе объекта вашего приложения среды выполнения).

## <a name="usercontrol-usage-scope"></a>Область использования UserControl


Элемент [UserControl](https://msdn.microsoft.com/library/windows/apps/br227647) представляет собой особый случай с точки зрения логики поиска ресурсов, так как для него существуют понятия области определения и области использования. Элемент **UserControl**, создающий ссылку на ресурс XAML из своей области определения, должен поддерживать поиск этого ресурса в рамках собственной последовательности поиска в области определения. Иными словами, он не имеет доступа к ресурсам приложения. Из области использования **UserControl** ссылка на ресурс рассматривается как находящаяся в последовательности поиска по направлению к корню страницы использования (как и любая другая ссылка на ресурс из объекта в загруженном дереве объектов), и ей доступны ресурсы приложения.

## <a name="resourcedictionary-and-xamlreaderload"></a>ResourceDictionary и XamlReader.Load

Вы можете использовать [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) как корень или как часть входных данных XAML для метода [XamlReader.Load](https://msdn.microsoft.com/library/windows/apps/br228048). Вы также можете включить ссылки на ресурсы XAML в этот код XAML, если все они полностью самодостаточны в XAML, отправленном для загрузки. **XamlReader.Load** анализирует XAML в контексте, в котором отсутствуют сведения о каких-либо других объектах **ResourceDictionary**, даже об [Application.Resources](https://msdn.microsoft.com/library/windows/apps/br242338). Кроме того, не используйте `{ThemeResource}` из XAML, отправленного в **XamlReader.Load**.

## <a name="using-a-resourcedictionary-from-code"></a>Использование ResourceDictionary из кода

Большинство сценариев для [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) обрабатывается исключительно в XAML. Контейнер **ResourceDictionary** и ресурсы объявляются как XAML-файл или набор узлов XAML в файле определения пользовательского интерфейса. Затем с помощью ссылок на ресурсы XAML отправляется запрос на эти ресурсы из других частей XAML. По-прежнему существуют некоторые сценарии, где приложению может потребоваться изменить содержимое **ResourceDictionary** с помощью кода, выполняемого во время работы приложения, или по крайней мере запросить содержимое **ResourceDictionary**, чтобы выяснить, определен ли ресурс. Вызовы этого кода осуществляются в экземпляре **ResourceDictionary**, поэтому сначала необходимо получить либо один непосредственный **ResourceDictionary** где-либо в дереве объектов, получив [FrameworkElement.Resources](https://msdn.microsoft.com/library/windows/apps/br208740) либо `Application.Current.Resources`.

В коде на C\# или Microsoft Visual Basic можно сослаться на ресурс в данном [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) с помощью индексатора ([Item](https://msdn.microsoft.com/library/windows/apps/jj603134)). Элемент **ResourceDictionary** представляет собой словарь со строковыми ключами, поэтому индексатор использует такие ключи вместо целочисленного индекса. В расширениях компонентов VisualC ++ (C + +/ CX) кода, используйте [поиска](https://msdn.microsoft.com/library/windows/apps/br208800).

Во время использования кода для проверки или изменения [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) такие API, как [Lookup](https://msdn.microsoft.com/library/windows/apps/br208800) или [Item](https://msdn.microsoft.com/library/windows/apps/jj603134), не переходят от непосредственных ресурсов к ресурсам приложения. Это поведение анализатора XAML, которое вызывается только при загрузке страниц XAML. Во время выполнения область ключей самодостаточна в экземпляре **ResourceDictionary**, используемом в это время. Однако эта область не распространяется на [MergedDictionaries](https://msdn.microsoft.com/library/windows/apps/br208801).

Кроме того, если вы запросите ключ, который не существует в [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794), ошибка может не возникнуть, и возвращаемое значение может быть просто равно **null**. Однако ошибка может возникнуть, если вы попытаетесь использовать возвращенный **null** в качестве значения. Эта ошибка возникает из установщика свойства, а не вашего вызова **ResourceDictionary**. Ошибки можно избежать только в том случае, если свойство принимает значение **null** как допустимое. Обратите внимание, как это поведение отличается от поведения поиска XAML во время анализа XAML. Из-за невозможности разрешить ключ, полученный из XAML во время анализа, ошибка анализа XAML будет возникать даже в тех случаях, когда свойство могло бы принять значение **null**.

Объединенные словари ресурсов включены в область индекса основного словаря ресурсов, который ссылается на объединенный словарь во время выполнения. Другими словами, можно использовать **Item** или [Lookup](https://msdn.microsoft.com/library/windows/apps/br208800) основного словаря для поиска любых объектов, которые были фактически определены в объединенном словаре. В этом случае поведение поиска напоминает поведение поиска XAML во время анализа: если в объединенных словарях есть несколько объектов с одним и тем же ключом, возвращается объект из словаря, который был добавлен последним.

Можно добавлять элементы к существующему элементу [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794), вызывая метод **Add** (C\# или Visual Basic) или [Insert](https://msdn.microsoft.com/library/windows/apps/br208799) (C++/CX). Добавлять элементы можно к непосредственным ресурсам или ресурсам приложения. При любом из этих вызовов API необходимо указывать ключ, за счет чего соблюдается требование наличия ключа для каждого элемента в **ResourceDictionary**. Но элементы, которые вы добавляете к **ResourceDictionary** во время выполнения, не относятся к ссылкам на ресурсы XAML. Необходимый поиск ссылок на ресурсы XAML выполняется, когда этот XAML сначала анализируется при загрузке приложения (или при обнаружении изменения темы). Ресурсы, добавленные в коллекции во время выполнения, были недоступны, и изменение **ResourceDictionary** не отменяет уже полученный из него ресурс, даже если вы изменяете значение этого ресурса.

Вы также можете удалять элементы из [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) во время выполнения, копировать некоторые или все элементы, а также выполнять другие действия. Список членов **ResourceDictionary** показывает, какие API доступны. Обратите на это внимание, поскольку **ResourceDictionary** имеет предполагаемый API для поддержки базовых интерфейсов коллекции. Варианты API будут различаться в зависимости от того, какие языки вы используете— C\# или Visual Basic либо C++/CX.

## <a name="resourcedictionary-and-localization"></a>ResourceDictionary и локализация


Элемент XAML [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794) может изначально содержать строки, которые нужно локализовать. В этом случае сохраните такие строки в качестве ресурсов проекта, а не в **ResourceDictionary**. Извлеките строки из XAML, а вместо них задайте для элемента-владельца значение [x:Uid directive](https://msdn.microsoft.com/library/windows/apps/mt204791). Затем определите ресурс в файле ресурсов. Укажите имя ресурса в форме *XUIDValue*.*PropertyName* и значение ресурса для строки, которую нужно локализовать.

## <a name="custom-resource-lookup"></a>Настраиваемый поиск ресурсов

В сложных сценариях можно реализовать класс, поведение которого будет отличаться от поведения поиска ссылок на ресурсы XAML, описанного в этой теме. Для этого реализуется класс [CustomXamlResourceLoader](https://msdn.microsoft.com/library/windows/apps/br243327), после чего можно пользоваться этим поведением, применяя для ссылок на ресурсы [расширение разметки CustomResource](https://msdn.microsoft.com/library/windows/apps/mt185580) вместо [StaticResource](../../xaml-platform/staticresource-markup-extension.md) или [ThemeResource](../../xaml-platform/themeresource-markup-extension.md). В большинстве приложений не будет сценариев, которым требуется такое поведение. Более подробную информацию см. в статье [CustomXamlResourceLoader](https://msdn.microsoft.com/library/windows/apps/br243327).

 
## <a name="related-topics"></a>Еще по теме

* [ResourceDictionary](https://msdn.microsoft.com/library/windows/apps/br208794)
* [Обзор языка XAML](https://msdn.microsoft.com/library/windows/apps/mt185595)
* [Расширение разметки StaticResource](../../xaml-platform/staticresource-markup-extension.md)
* [Расширение разметки ThemeResource](../../xaml-platform/themeresource-markup-extension.md)
* [Ресурсы темы XAML](xaml-theme-resources.md)
* [Настройка стиля элементов управления](xaml-styles.md)
* [Атрибут x:Key](https://msdn.microsoft.com/library/windows/apps/mt204787)

 

 



