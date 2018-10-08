---
author: QuinnRadich
description: Узнайте, как использовать цвета элементов и темы в приложениях UWP.
title: Цвет в приложениях UWP
ms.author: quradic
ms.date: 4/7/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
design-contact: karenmui
ms.localizationpriority: medium
ms.openlocfilehash: ca59855456abe366ec681404b3bf6253bc182f79
ms.sourcegitcommit: fbdc9372dea898a01c7686be54bea47125bab6c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "4421653"
---
# <a name="color"></a>Цвет

![главное изображение](images/header-color.svg)

Цвет— это интуитивно понятный способом передачи информации пользователям в приложении. Его можно применять для обозначения интерактивных возможностей, обратной связи на действия пользователя, а также создания ощущения визуальной непрерывности интерфейса. 

В приложениях UWP цвета в первую очередь определяются цветом элементов и темой. В этой статье мы обсудим, как использовать цвет в приложении и как использовать цвет элементов и ресурсы темы, чтобы ваше приложение UWP работало в контексте любой темы. 

## <a name="color-principles"></a>Цветовые принципы

:::row:::
    :::column:::
        **Use color meaningfully.**
        When color is used sparingly to highlight important elements, it can help create a user interface that is fluid and intuitive.
    :::column-end:::
    :::column:::
        **Use color to indicate interactivity.**
        It's a good idea to choose one color to indicate elements of your application that are interactive. For example, many web pages use blue text to denote a hyperlink.
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        **Color is personal.**
        In Windows, users can choose an accent color and a light or dark theme, which are reflected throughout their experience. You can choose how to incorporate the user's accent color and theme into your application, personalizing their experience.
    :::column-end:::
    :::column:::
        **Color is cultural.**
        Consider how the colors you use will be interpreted by people from different cultures. For example, in some cultures the color blue is associated with virtue and protection, while in others it represents mourning.
    :::column-end:::
:::row-end:::

## <a name="themes"></a>Темы

Приложения UWP могут использовать светлую или темную тему. Тема определяет цвета фона, текста, значков и [общих элементов управления](../controls-and-patterns/index.md) приложения.

### <a name="light-theme"></a>Светлая тема

![светлая тема](images/color/light-theme.svg)

### <a name="dark-theme"></a>Темная тема

![темная тема](images/color/dark-theme.svg)

По умолчанию тема приложения UWP— это тема, выбранная пользователем в параметрах Windows, или тема устройства по умолчанию (например, темная тема на консоли Xbox). Однако вы можете настроить тему для вашего приложения UWP. 

### <a name="changing-the-theme"></a>Изменение темы

Вы можете изменитьтему, указав соответствующее значение свойства **RequestedTheme** в файле `App.xaml`.

```XAML
<Application
    x:Class="App9.App"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App9"
    RequestedTheme="Dark">
</Application>
```

Удаление свойства **RequestedTheme** означает, что приложение будет применять параметры системы пользователя.

Пользователи также могут выбрать тему с высокой контрастностью, в которой применяется небольшая палитра контрастных цветов, благодаря чему интерфейс хорошо видно. В этом случае система переопределит свойство RequestedTheme.

### <a name="testing-themes"></a>Тестирование тем

Если вы не запрашиваете тему для приложения, обязательно протестируйте приложение в светлой и темной теме, чтобы убедиться, что оно будет читаться в любых обстоятельствах.

**Примечание**. В Visual Studio по умолчанию в свойстве RequestedTheme указана светлая тема, так что вам потребуется изменить RequestedTheme для тестирования обеих тем.

## <a name="theme-brushes"></a>Кисти темы

Стандартные элементы управления автоматически используют [кисти темы](../controls-and-patterns/xaml-theme-resources.md#the-xaml-color-ramp-and-theme-dependent-brushes) для адаптации к светлой и темной теме.

Например, ниже приведена иллюстрация того, как [AutoSuggestBox](../controls-and-patterns/auto-suggest-box.md) использует кисти темы:

![пример элемента управления кистями темы](images/color/theme-brushes.svg)

Кисти темы используются в следующих целях.

- **Base** используется для текста.
- **Alt**— это инверсированная кисть Base.
- **Chrome** используется для элементов верхнего уровня, таких как области навигации и панели команд.
- **List** используется для элементов управления списком.

**Low**/**Medium**/**High** обозначают интенсивность цвета.

### <a name="using-theme-brushes"></a>Использование кистей темы

:::row:::
    :::column:::
        When creating templates for custom controls, use theme brushes rather than hard code color values. This way, your app can easily adapt to any theme.

        For example, these [item templates for ListView](../controls-and-patterns/item-templates-listview.md) demonstrate how to use theme brushes in a custom template.
    :::column-end:::
    :::column:::
         ![double line list item with icon example](images/color/list-view.svg)
    :::column-end:::
:::row-end:::

```xaml
<ListView ItemsSource="{x:Bind ViewModel.Recordings}">
    <ListView.ItemTemplate>
        <DataTemplate x:Name="DoubleLineDataTemplate" x:DataType="local:Recording">
            <StackPanel Orientation="Horizontal" Height="64" AutomationProperties.Name="{x:Bind CompositionName}">
                <Ellipse Height="48" Width="48" VerticalAlignment="Center">
                    <Ellipse.Fill>
                        <ImageBrush ImageSource="Placeholder.png"/>
                    </Ellipse.Fill>
                </Ellipse>
                <StackPanel Orientation="Vertical" VerticalAlignment="Center" Margin="12,0,0,0">
                    <TextBlock Text="{x:Bind CompositionName}"  Style="{ThemeResource BaseTextBlockStyle}" Foreground="{ThemeResource SystemControlPageTextBaseHighBrush}" />
                    <TextBlock Text="{x:Bind ArtistName}" Style="{ThemeResource BodyTextBlockStyle}" Foreground="{ThemeResource SystemControlPageTextBaseMediumBrush}"/>
                </StackPanel>
            </StackPanel>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Дополнительные сведения об использовании кистей тем в приложении см. в разделе [Ресурсы темы](../controls-and-patterns/xaml-theme-resources.md).

## <a name="accent-color"></a>Цвет элементов

Стандартные элементы управления используют цвет элементов для передачи сведений о состоянии. По умолчанию цвет элементов— это `SystemAccentColor`, выбранный пользователями в параметрах. Однако вы можете настроить цвет элементов приложения в соответствии с вашим брендом.

![элементы управления windows](images/color/windows-controls.svg)

:::row:::
    :::column:::
        ![user-selected accent header](images/color/user-accent.svg)
        ![user-selected accent color](images/color/user-selected-accent.svg)
    :::column-end:::
    :::column:::
        ![custom accent header](images/color/custom-accent.svg)
        ![custom brand accent color](images/color/brand-color.svg)
    :::column-end:::
:::row-end:::

### <a name="overriding-the-accent-color"></a>Переопределение цвета элементов

Чтобы изменить цвет элементов, вставьте следующий код в `app.xaml`.

```xaml
<Application.Resources>
    <ResourceDictionary>
        <Color x:Key="SystemAccentColor">#107C10</Color>
    </ResourceDictionary>
</Application.Resources>
```

### <a name="choosing-an-accent-color"></a>Выбор цвета элементов

Если выбрать пользовательский цвет элементов для приложения, убедитесь, что текст и фон, которые его используют, достаточно контрастные для обеспечения оптимальной удобочитаемости. Для тестирования контрастности можно использовать средство выбора цвета в параметрах Windows или эти [веб-средства проверки контрастности](https://www.w3.org/TR/WCAG20-TECHS/G18.html#G18-resources).

![Средство выбора настраиваемого цвета элементов в параметрах Windows](images/color/color-picker.svg)

## <a name="accent-color-palette"></a>Палитра цветов элементов

Алгоритм цвета элементов в оболочке Windows создает светлые и темные оттенки цвета элементов.

![палитра цветов элементов](images/color/accent-color-palette.svg)

Эти оттенки можно использовать как [ресурсы темы](../controls-and-patterns/xaml-theme-resources.md):

- `SystemAccentColorLight3`
- `SystemAccentColorLight2`
- `SystemAccentColorLight1`
- `SystemAccentColorDark1`
- `SystemAccentColorDark2`
- `SystemAccentColorDark3`

<!-- check this is true -->
Вы также можете получить доступ к палитре цветов элементов программными средствами с помощью метода [**UISettings.GetColorValue**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.UISettings#Windows_UI_ViewManagement_UISettings_GetColorValue_Windows_UI_ViewManagement_UIColorType_) и перечисления [**UIColorType**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.UIColorType).

Вы можете палитру цветов элементов для определения цветовой темы в приложении. Ниже приведен пример того, как можно использовать палитру цветов элементов на кнопке.

![Палитра цветов элементов, примененная к кнопке](images/color/color-theme-button.svg)

```xaml
<Page.Resources>
    <ResourceDictionary>
        <ResourceDictionary.ThemeDictionaries>
            <ResourceDictionary x:Key="Light">
                <SolidColorBrush x:Key="ButtonBackground" Color="{ThemeResource SystemAccentColor}"/>
                <SolidColorBrush x:Key="ButtonBackgroundPointerOver" Color="{ThemeResource SystemAccentColorLight1}"/>
                <SolidColorBrush x:Key="ButtonBackgroundPressed" Color="{ThemeResource SystemAccentColorDark1}"/>
            </ResourceDictionary>
        </ResourceDictionary.ThemeDictionaries>
    </ResourceDictionary>
</Page.Resources>

<Button Content="Button"></Button>
```

При использовании цветного текста на цветном фоне убедитесь, что между текстом и фоном достаточно контраста. По умолчанию для гиперссылок и гипертекста используется цвет элементов. Если вы применяете варианты цвета элементов для фона, вам следует использовать вариант исходного цвета элементов для оптимизации контрастности цветного текста на цветном фоне.

На приведенной ниже схеме показан пример различных светлых и темных оттенков цвета элементов и применения цветного типа на цветной поверхности.

![цвет на цвете](images/color/color-on-color.svg)

Подробнее об использовании стилей для элементов управления см. в разделе [Стили XAML](../controls-and-patterns/xaml-styles.md).

## <a name="color-api"></a>API цветов

Существует несколько API-интерфейсов, которые можно использовать для добавления цвета в приложение. Во-первых, это класс [**Colors**](https://docs.microsoft.com/en-us/uwp/api/windows.ui.colors), который реализует обширный список предопределенных цветов. Доступ к ним можно получать автоматически с помощью свойств XAML. В приведенном ниже примере мы создаем кнопку и задаем свойства цвета фона и цвета переднего плана для членов класса **Colors**.

```xaml
<Button Background="MediumSlateBlue" Foreground="White">Button text</Button>
```

Вы можете создать собственные цвета из RGB- или шестнадцатеричных значений с помощью структуры [**Color**](https://docs.microsoft.com/en-us/uwp/api/windows.ui.color) в XAML.

```xaml
<Color x:Key="LightBlue">#FF36C0FF</Color>
```

Вы также можете создать такой цвет в коде с помощью метода **FromArgb**.

```csharp
Color LightBlue = Color.FromArgb(255,54,192,255);
```

Буквы "Argb" означают "альфа" (непрозрачность), красный, зеленый и синий— четырех компонента цвета. Каждый аргумент может принимать значение от 0 до 255. Можно пропустить первое значение, чтобы использовать непрозрачность по умолчанию (255 или 100%).

> [!Note]
> Если вы используете C++, вам необходимо создать цвета с помощью класса [**ColorHelper**](https://docs.microsoft.com/en-us/uwp/api/windows.ui.colorhelper).

Чаще всего **Color** используется в качестве аргумента для метода [**SolidColorBrush**](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.media.solidcolorbrush), который можно применять для рисования элементов пользовательского интерфейса одним сплошным цветом. Эти кисти обычно определяются в [**ResourceDictionary**](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.ResourceDictionary), поэтому их можно повторно использовать для нескольких элементов.

```xaml
<ResourceDictionary>
    <SolidColorBrush x:Key="ButtonBackgroundBrush" Color="#FFFF4F67"/>
    <SolidColorBrush x:Key="ButtonForegroundBrush" Color="White"/>
</ResourceDictionary>
```

Дополнительные сведения об использовании кистей см. в разделе [Кисти XAML](brushes.md).

## <a name="scoping-system-colors"></a>Определение системных цветов

Помимо определения собственные цвета в приложении, можно также определить область наших systematized цвета для нужного регионов во всем приложении с помощью тега **ColorSchemeResources** . Этот API позволяет вам не только колоризации и темы больших групп элементов управления за один раз, задав несколько свойств, но также предоставляет вам множество других систем преимущества, которые обычно вряд ли с определением собственные цвета вручную:

- Не повлияет на любой цвет, настраиваются с помощью **ColorSchemeResources** высокой контрастности
  * То есть приложение, будут доступны другим пользователям без дополнительных проектирования и разработки затраты
- Можно легко установить цвета светлой, темной или распространено для обеих тем, задав для одного свойства по API
- Цвета на **ColorSchemeResources** каскадом до все похожие элементы управления, которые также используют этот цвет системы
  * Это гарантирует, что у вас будет новости единообразной цветовой вашего приложения при сохранении его внешний вид вашего фирменного стиля
- Влияет на все визуальные состояния, анимации и варианты непрозрачности без необходимости изменить шаблон

### <a name="how-to-use-colorschemeresources"></a>Как использовать ColorSchemeResources

ColorSchemeResources — это API-Интерфейс, который сообщает системе, какие ресурсы область, где. ColorSchemeResources необходимо выполнить с [x: Key](https://docs.microsoft.com/windows/uwp/xaml-platform/x-key-attribute), может быть один из трех вариантов:
- По умолчанию
  * Будет отображать изменения цвета в [света](https://docs.microsoft.com/windows/uwp/design/style/color#light-theme) и [темной](https://docs.microsoft.com/windows/uwp/design/style/color#dark-theme) теме
- Light
  * Изменения цвета будет отображаться только в [светлой теме](https://docs.microsoft.com/windows/uwp/design/style/color#light-theme) 
- Dark
  * Изменения цвета будет отображаться только в [темной теме](https://docs.microsoft.com/windows/uwp/design/style/color#dark-theme)

Параметр x: Key гарантирует, что цвета соответствующим образом изменить тему системы или приложения, вы хотите различных пользовательского внешнего вида при в двух цветовых.

### <a name="how-to-apply-scoped-colors"></a>Способ применения цвета с областью действия

Определение области действия ресурсы с помощью **ColorSchemeResources** API-интерфейса в XAML позволяет воспользоваться любой системный цвет или кисти, которая находится в нашей библиотеке [ресурсов темы](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/xaml-theme-resources) и переопределить их в пределах области страницы или контейнера.

Например, если определяется два системных цветов - **SystemBaseLowColor** и **SystemBaseMediumLowColor** в виде сетки и затем помещается на странице две кнопки: одна внутри этой сетки и один за пределами:

```xaml
<Grid x:Name="Grid_A">
    <Grid.Resources>
        <ColorSchemeResources x:Key="Default" 
        SystemBaseLowColor="LightGreen" 
        SystemBaseMediumLowColor="DarkCyan"/>
    </Grid.Resources>

    <Buton Content="Button_A"/>
</Grid>
<Buton Content="Button_B"/>
```

Вы получите **Button_A** , с помощью применен новые цвета и **Button_B** останется привлекательный как кнопка по умолчанию нашей системы:

![с областью системных цветов на кнопке](images/color/scopedcolors_cyan_button.png)

Тем не менее так как все наши системных цветов каскадом слишком до других элементов управления, задав **SystemBaseLowColor** и **SystemBaseMediumLowColor** повлияет на несколько кнопок. В этом случае элементы управления, как **ToggleButton**, **RadioButton** и **ползунок** будет также быть влияет эти изменения цвета системы, следует этих элементов управления можно поместить в выше области exampl сетки.
Если вы хотите определить область системы цвет изменение *только один элементы управления* можно сделать путем определения **ColorSchemeResources** внутри этого элемента управления ресурсами:

```xaml
<Grid x:Name="Grid_A">
    <Button Content="Button_A">
        <Button.Resources>
            <ColorSchemeResources x:Key="Default" 
                SystemBaseLowColor="LightGreen" 
                SystemBaseMediumLowColor="DarkCyan"/>
        </Button.Resources>
    </Button>
</Grid>
<Button Content="Button_B"/>
```
По сути имеют то же самое как перед, но теперь любых других элементов управления, добавляемый в сетку не получают изменения цвета. Это связано с эти системные цвета относятся к **Button_A** только.

### <a name="nesting-scoped-resources"></a>Ресурсы уровня вложения

Системные цвета, вложенное можно также и — это сделано, переключив **ColorSchemeResources** в ресурсах вложенных элементов внутри разметки макет вашего приложения.

```xaml
<Grid x:Name="Grid_A">
    <Grid.Resources>
        <ColorSchemeResources x:Key="Default"
            SystemBaseLowColor="LightGreen"
            SystemBaseMediumLowColor="DarkCyan"/>
    </Grid.Resources>

    <Button Content="Button_A"/>
    <Grid x:Name="Grid_B">
        <Grid.Resources>
            <ColorSchemeResources x:Key="Default"
                SystemBaseLowColor="Goldenrod"
                SystemBaseMediumLowColor="DarkGoldenrod"/>
        </Grid.Resources>

        <Button Content="Nested Button"/>
    </Grid>
</Grid>
```

В этом примере наследования **Button_A** цвета определить в ресурсах **Grid_A**пользователя и **Вложенные кнопка** наследует цвета из ресурсов **Grid_B**элемента. С помощью расширения, это означает, что любые другие элементы управления размещаются внутри **Grid_B** будет проверить или применить **Grid_B**элемента ресурсов во-первых, перед проверкой или применения ресурсов **Grid_A**пользователя, и наконец применения наших цвета по умолчанию, если ничего не определены на уровне страницы или приложения.

Это работает для любого числа вложенных элементов, ресурсы которого имеют определения цвета.

### <a name="scoping-with-a-resourcedictionary"></a>Определение области действия с ResourceDictionary

Не ограничиваются контейнера или ресурсы страницы, а также можно определить эти системные цвета в ResourceDictionary, могут быть объединены в любой области так, как вы обычно бы объединить словарь.

#### <a name="mycustomthemexaml"></a>MyCustomTheme.xaml

Во-первых создается ResourceDictionary. Затем поместите **ColorSchemeResources** в ThemeDictionaries и переопределить нужные системные цвета:

```xaml
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" 
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:TestApp">

    <ResourceDictionary.ThemeDictionaries>

        <ColorSchemeResources x:Key="Default"
            SystemBaseLowColor="LightGreen"
            SystemBaseMediumLowColor="DarkCyan"/>
        
    </ResourceDictionary.ThemeDictionaries>
</ResourceDictionary>
```

#### <a name="mainpagexaml"></a>MainPage.xaml

На странице, содержащий макет просто объедините этого словаря в в область, которую вы хотите, чтобы:

```xaml
<Grid x:Name="Grid_A">
    <Grid.Resources>
            <ResourceDictionary>
                <ResourceDictionary.MergedDictionaries>
                    <ResourceDictionary Source="MyCustomTheme.xaml"/>
                </ResourceDictionary.MergedDictionaries>
            </ResourceDictionary>
    </Grid.Resources>
             
    <Button Content="Button_A"/>
</Grid>
```

Теперь все ресурсы темы и цвет можно помещаются в один словарь ресурсов **MyCustomTheme** и предусматривать там, где без необходимости беспокоиться о дополнительных помех в разметке макета.

### <a name="other-ways-to-define-color-resources"></a>Другие способы определения ресурсы цвета

ColorSchemeResources также позволяет системных цветов, чтобы разместить и определение непосредственно в качестве оболочки, а не в строке:

``` xaml
<ColorSchemeResources x:Key="Dark">
    <Color x:Key="SystemBaseLowColor">Goldenrod</Color>
</ColorSchemeResources>
```

## <a name="usability"></a>Удобство использования

:::row:::
    :::column:::
        ![contrast illustration](images/color/illo-contrast.svg)
    :::column-end:::
    :::column span="2":::
        **Contrast**

        Make sure that elements and images have sufficient contrast to differentiate between them, regardless of the accent color or theme.

        When considering what colors to use in your application, accessiblity should be a primary concern. Use the guidance below to make sure your application is accessible to as many users as possible.
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![contrast illustration](images/color/illo-lighting.svg)
    :::column-end:::
    :::column span="2":::
        **Lighting**

        Be aware that variation in ambient lighting can affect the useability of your app. For example, a page with a black background might unreadable outside due to screen glare, while a page with a white background might be painful to look at in a dark room.
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![contrast illustration](images/color/illo-colorblindness.svg)
    :::column-end:::
    :::column span="2":::
        **Colorblindness**

        Be aware of how colorblindness could affect the useability of your application. For example, a user with red-green colorblindness will have difficulty distinguishing red and green elements from each other. About **8 percent of men** and **0.5 percent of women** are red-green colorblind, so avoid using these color combinations as the sole differentiator between application elements.
    :::column-end:::
:::row-end:::

## <a name="related-articles"></a>Еще по теме

- [Стили XAML](../controls-and-patterns/xaml-styles.md)
- [Ресурсы темы XAML](../controls-and-patterns/xaml-theme-resources.md)