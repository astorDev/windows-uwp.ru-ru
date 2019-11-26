---
description: Узнайте, как использовать цвета элементов и темы в приложениях UWP.
title: Цвет в приложениях UWP
ms.date: 04/07/2019
ms.topic: article
keywords: windows 10, uwp
design-contact: karenmui
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: efe67707edc5f556301ded466f3f2919ec04873e
ms.sourcegitcommit: 49a34e957433966ac8d4822b5822f21087aa61c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74153725"
---
# <a name="color"></a>Цвет

![Главное изображение](images/header-color.svg)

Цвет — это интуитивно понятный способ передачи информации пользователям в приложении. Его можно применять для обозначения интерактивных возможностей, обратной связи на действия пользователя, а также создания ощущения визуальной непрерывности интерфейса.

В приложениях UWP цвета в первую очередь определяются цветом элементов и темой. В этой статье мы обсудим, как использовать цвет в приложении и как применять цвет элементов и ресурсы темы, чтобы ваше приложение UWP работало в контексте любой темы.

## <a name="color-principles"></a>Цветовые принципы

:::row:::
    :::column:::
**Используйте цвет осмысленно.**
Использование цвета для выделения важных элементов поможет создать гибкий и интуитивно понятный интерфейс.
    :::column-end:::
    :::column:::
**Используйте цвет для обозначения интерактивных возможностей.**
Рекомендуется выбрать один цвет для обозначения интерактивных элементов приложения. Например, на многих веб-страницах синий текст обозначает гиперссылки.
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
**Цвет отражает личные предпочтения.**
В Windows пользователи могут выбирать цвет элементов, а также светлую или темную тему, чтобы применить эти настройки системно. Вы можете выбрать способ применения выбираемых пользователем цветов и тем в приложении для персонализации взаимодействия.
    :::column-end:::
    :::column:::
**Цвет отражает культурные особенности.**
Подумайте о том, как применяемые цвета будут интерпретироваться пользователями из разных культур. Например, в некоторых странах синий цвет ассоциируется с достоинством и защитой, а в других странах он символизирует печаль.
    :::column-end:::
:::row-end:::

## <a name="themes"></a>Темы

В приложениях UWP может использоваться светлая или темная тема. Тема определяет цвета фона, текста, значков и [общих элементов управления](../controls-and-patterns/index.md) приложения.

### <a name="light-theme"></a>Светлая тема

![Светлая тема](images/color/light-theme.svg)

### <a name="dark-theme"></a>Темная тема

![Темная тема](images/color/dark-theme.svg)

По умолчанию тема приложения UWP — это тема, выбранная пользователем в параметрах Windows, или стандартная тема устройства (например, темная тема на консоли Xbox). Однако вы можете настроить тему для вашего приложения UWP.

### <a name="changing-the-theme"></a>Изменение темы

Вы можете изменить тему, указав соответствующее значение свойства **RequestedTheme** в файле `App.xaml`.

```XAML
<Application
    x:Class="App9.App"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App9"
    RequestedTheme="Dark">
</Application>
```

Удаление свойства **RequestedTheme** означает, что приложение будет применять системные параметры пользователя.

Пользователи также могут выбрать тему с высокой контрастностью, в которой применяется небольшая палитра контрастных цветов, благодаря чему интерфейс хорошо видно. В этом случае система переопределит свойство RequestedTheme.

### <a name="testing-themes"></a>Тестирование тем

Если вы не запрашиваете тему для приложения, обязательно протестируйте приложение в светлой и темной теме, чтобы убедиться, что оно читабельно в любом из этих состояний.

**Примечание**. В Visual Studio по умолчанию в свойстве RequestedTheme указана светлая тема, так что вам потребуется изменить RequestedTheme для тестирования обеих тем.

## <a name="theme-brushes"></a>Кисти темы

Стандартные элементы управления автоматически используют [кисти темы](../controls-and-patterns/xaml-theme-resources.md#the-xaml-color-ramp-and-theme-dependent-brushes) для адаптации к светлой и темной теме.

Например, ниже приведена иллюстрация того, как [AutoSuggestBox](../controls-and-patterns/auto-suggest-box.md) использует кисти темы.

![пример элемента управления кистями темы](images/color/theme-brushes.svg)

Кисти темы используются в следующих целях.

- **Base** используется для текста.
- **Alt** — это инверсированная кисть Base.
- **Chrome** используется для элементов верхнего уровня, таких как области навигации и панели команд.
- **List** используется для элементов управления списком.

**Low**/**Medium**/**High** обозначают интенсивность цвета.

### <a name="using-theme-brushes"></a>Использование кистей темы

:::row:::
    :::column:::
При создании шаблонов для пользовательских элементов управления применяйте кисти темы, а не закодированные значения цветов. Так приложение сможет легко адаптироваться к любой теме.

Например, [эти шаблоны элементов для ListView](../controls-and-patterns/item-templates-listview.md) демонстрируют использование кистей темы в пользовательском шаблоне.
    :::column-end:::
    :::column:::
 ![Пример двухстрочного элемента списка со значком](images/color/list-view.svg)
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

Дополнительные сведения об использовании кистей тем в приложении см. в статье [Ресурсы темы XAML](../controls-and-patterns/xaml-theme-resources.md).

## <a name="accent-color"></a>Цвет элементов

Стандартные элементы управления используют цвет элементов для передачи сведений о состоянии. По умолчанию цветом элементов выступает `SystemAccentColor`, выбранный пользователями в параметрах. Однако вы можете настроить цвет элементов приложения в соответствии с вашим брендом.

![элементы управления Windows](images/color/windows-controls.svg)

:::row:::
    :::column:::
![Выбранный пользователем заголовок](images/color/user-accent.svg)
![Выбранный пользователем цвет элементов](images/color/user-selected-accent.svg)
    :::column-end:::
    :::column:::
![Настраиваемый заголовок](images/color/custom-accent.svg)
![Настраиваемый корпоративный цвет элементов](images/color/brand-color.svg)
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

Если вы выбрали пользовательский цвет элементов для приложения, убедитесь, что текст и фон, которые его используют, достаточно контрастные для обеспечения оптимальной удобочитаемости. Для тестирования контрастности можно использовать средство выбора цвета в параметрах Windows или эти [веб-средства проверки контрастности](https://www.w3.org/TR/WCAG20-TECHS/G18.html#G18-resources).

![Средство выбора настраиваемого цвета элементов в параметрах Windows](images/color/color-picker.svg)

## <a name="accent-color-palette"></a>Палитра цветов элементов

Алгоритм цвета элементов в оболочке Windows создает светлые и темные оттенки цвета элементов.

![Палитра цветов элементов](images/color/accent-color-palette.svg)

Эти оттенки можно использовать как [ресурсы темы](../controls-and-patterns/xaml-theme-resources.md):

- `SystemAccentColorLight3`
- `SystemAccentColorLight2`
- `SystemAccentColorLight1`
- `SystemAccentColorDark1`
- `SystemAccentColorDark2`
- `SystemAccentColorDark3`

<!-- check this is true -->
Вы также можете получить доступ к палитре цветов элементов программными средствами с помощью метода [**UISettings.GetColorValue**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.UISettings#Windows_UI_ViewManagement_UISettings_GetColorValue_Windows_UI_ViewManagement_UIColorType_) и перечисления [**UIColorType**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.UIColorType).

С помощью палитры цветов элементов можно определить цветовую тему в приложении. Ниже приведен пример того, как можно использовать палитру цветов элементов на кнопке.

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

При использовании цветного текста на цветном фоне убедитесь, что между текстом и фоном достаточно контраста. По умолчанию цвет элементов используется для гиперссылок и гипертекста. Если вы применяете варианты цвета элементов для фона, вам следует использовать вариант исходного цвета элементов для оптимизации контрастности цветного текста на цветном фоне.

На приведенной ниже схеме показаны примеры различных светлых и темных оттенков цвета элементов и применения цветных типов на цветной поверхности.

![цвет на цвете](images/color/color-on-color.png)

Дополнительные сведения об использовании стилей для элементов управления см. в статье [Стили XAML](../controls-and-patterns/xaml-styles.md).

## <a name="color-api"></a>API цветов

Существует несколько API-интерфейсов, позволяющих добавлять цвет в приложение. Во-первых, это класс [**Colors**](https://docs.microsoft.com/en-us/uwp/api/windows.ui.colors), который реализует обширный список предопределенных цветов. Доступ к ним можно получать автоматически с помощью свойств XAML. В приведенном ниже примере мы создаем кнопку и задаем свойства цвета фона и цвета переднего плана для элементов класса **Colors**.

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

Буквы "Argb" означают "альфа" (непрозрачность), "красный", "зеленый" и "синий" — четыре компонента цвета. Каждый аргумент может принимать значение от 0 до 255. Можно пропустить первое значение, чтобы использовать непрозрачность по умолчанию (255 или 100 %).

> [!Note]
> Если вы используете C++, вам необходимо создать цвета с помощью класса [**ColorHelper**](https://docs.microsoft.com/en-us/uwp/api/windows.ui.colorhelper).

Чаще всего **Color** используется в качестве аргумента для метода [**SolidColorBrush**](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.media.solidcolorbrush), который можно применять для залива элементов пользовательского интерфейса одним сплошным цветом. Эти кисти обычно определяются в [**ResourceDictionary**](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.ResourceDictionary), поэтому их можно повторно использовать для нескольких элементов.

```xaml
<ResourceDictionary>
    <SolidColorBrush x:Key="ButtonBackgroundBrush" Color="#FFFF4F67"/>
    <SolidColorBrush x:Key="ButtonForegroundBrush" Color="White"/>
</ResourceDictionary>
```

Дополнительные сведения об использовании кистей см. в статье [Кисти XAML](brushes.md).

## <a name="scoping-system-colors"></a>Определение области системных цветов

В дополнение к определению собственных цветов в приложении, вы также можете использовать наши систематизированные цвета для желаемых областей в своем приложении с помощью тега **ColorPaletteResources**. Этот API-интерфейс позволяет не только одновременно выделять цветом большие группы элементов управления и задавать темы для них с помощью настройки нескольких свойств, но также обеспечивает другие системные преимущества, которые будут недоступны при определении своих собственных цветов вручную:

- Любой набор цветов с использованием **ColorPaletteResources** не будет влиять на высокую контрастность.
  * Это означает, что ваше приложение будет доступно большему количеству пользователей без каких-либо дополнительных затрат на проектирование или разработку.
- Можно легко установить светлые или темные цвета или применить их к обеим темам, установив одно свойство в API.
- Цвета, установленные в **ColorPaletteResources**, будут применяться сверху вниз ко всем аналогичным элементам управления, которые также используют этот системный цвет.
  * Это гарантирует, что у вас будет единообразная цветовая гамма в приложении и позволит сохранить корпоративные цвета вашего бренда.
- Влияет на все визуальные состояния, анимацию и вариации непрозрачности без необходимости изменения шаблона.

### <a name="how-to-use-colorpaletteresources"></a>Использование ColorPaletteResources

ColorPaletteResources — это API, который сообщает системе, какие ресурсы находятся в определенной области. ColorPaletteResources должен принимать значение [x:Key](https://docs.microsoft.com/windows/uwp/xaml-platform/x-key-attribute), которое может быть в одном из трех вариантов:
- По умолчанию
  * Изменения цвета будут отображаться в [светлой](https://docs.microsoft.com/windows/uwp/design/style/color#light-theme) и [темной](https://docs.microsoft.com/windows/uwp/design/style/color#dark-theme) темах.
- Светлая
  * Изменения цвета будут отображаться только в [светлой теме](https://docs.microsoft.com/windows/uwp/design/style/color#light-theme).
- Темная
  * Изменения цвета будут отображаться в [темной теме](https://docs.microsoft.com/windows/uwp/design/style/color#dark-theme).

Если вы захотите изменить внешний вид любой из тем, настройка значения x:Key обеспечит правильное изменение цветов в соответствии с темой системы или приложения.

### <a name="how-to-apply-scoped-colors"></a>Применение цветов с заданной областью

Применение области к ресурсам с помощью API **ColorPaletteResources** в XAML позволяет выбрать любой системный цвет или кисть, которые есть в нашей [библиотеке ресурсов тем](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/xaml-theme-resources), и переопределить их в рамках страницы или контейнера.

Например, если вы определили два системных цвета — **BaseLow** и **BaseMediumLow** внутри сетки, а затем разместили две кнопки на своей странице — одну внутри этой сетки, а другую снаружи:

```xaml
<Grid x:Name="Grid_A">
    <Grid.Resources>
        <ColorPaletteResources x:Key="Default"
        BaseLow="LightGreen"
        BaseMediumLow="DarkCyan"/>
    </Grid.Resources>

    <Buton Content="Button_A"/>
</Grid>
<Buton Content="Button_B"/>
```

к кнопке **Button_A** будут применены новые цвета, а кнопка **Button_B** останется похожей на нашу системную кнопку по умолчанию:

![цвета с заданной областью на кнопке](images/color/scopedcolors_cyan_button.png)

Но так как все наши системные цвета распространяются на другие элементы управления, настройка **BaseLow** и **BaseMediumLow** повлияет не только на кнопки. В этом случае такие элементы управления, как **ToggleButton**, **RadioButton** и **Slider**, также будут зависеть от этих системных изменений цвета, если эти элементы управления будут помещены в область сетки в приведенном выше примере.
Если вы хотите изменить системный цвет только для *одного элемента управления*, определите **ColorPaletteResources** в ресурсах этого элемента управления:

```xaml
<Grid x:Name="Grid_A">
    <Button Content="Button_A">
        <Button.Resources>
            <ColorPaletteResources x:Key="Default"
                BaseLow="LightGreen"
                BaseMediumLow="DarkCyan"/>
        </Button.Resources>
    </Button>
</Grid>
<Button Content="Button_B"/>
```
По сути, все останется по-прежнему, но теперь любые другие элементы управления, добавленные в сетку, не будут восприимчивы к изменениям цвета. Это связано с тем, что эти системные цвета применяются только к кнопке **Button_A**.

### <a name="nesting-scoped-resources"></a>Вложение ресурсов с заданной областью

Вы также можете вложить системные цвета путем размещения **ColorPaletteResources** в ресурсах вложенных элементов в разметке макета вашего приложения:

```xaml
<Grid x:Name="Grid_A">
    <Grid.Resources>
        <ColorPaletteResources x:Key="Default"
            BaseLow="LightGreen"
            BaseMediumLow="DarkCyan"/>
    </Grid.Resources>

    <Button Content="Button_A"/>
    <Grid x:Name="Grid_B">
        <Grid.Resources>
            <ColorPaletteResources x:Key="Default"
                BaseLow="Goldenrod"
                BaseMediumLow="DarkGoldenrod"/>
        </Grid.Resources>

        <Button Content="Nested Button"/>
    </Grid>
</Grid>
```

В этом примере **Button_A** наследует цвета, определенные в ресурсах **Grid_A**, а **вложенная кнопка** наследует цвета из ресурсов **Grid_B**. Это означает, что любые другие элементы управления, помещенные в **Grid_B**, сначала проверят или применят ресурсы **Grid_B**, потом проверят или применят ресурсы **Grid_A**, а затем применят наши цвета по умолчанию, если на уровне страницы или приложения ничего не определено.

Это работает для любого количества вложенных элементов с ресурсами, для которых определены цвета.

### <a name="scoping-with-a-resourcedictionary"></a>Определение области с помощью ResourceDictionary

Вы не ограничены ресурсами контейнера или страницы, а также можете определить эти системные цвета в ResourceDictionary, которые затем можно объединить в любой области, как обычно объединяют словарь.

#### <a name="mycustomthemexaml"></a>MyCustomTheme.xaml

Во-первых, создайте словарь ResourceDictionary. Затем поместите класс **ColorPaletteResources** в раздел ThemeDictionaries и переопределите нужные системные цвета:

```xaml
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:TestApp">

    <ResourceDictionary.ThemeDictionaries>
        <ResourceDictionary x:Key="Default">
            <ResourceDictionary.MergedDictionaries>

                <ColorPaletteResources x:Key="Default"
                    Accent="#FF0073CF"
                    AltHigh="#FF000000"
                    AltLow="#FF000000"/>

            </ResourceDictionary>
        </ResourceDictionary.MergedDictionaries>        
    </ResourceDictionary.ThemeDictionaries>
</ResourceDictionary>
```

#### <a name="mainpagexaml"></a>MainPage.xaml

На странице, содержащей ваш макет, объедините этот словарь с нужной областью:

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

Теперь все ресурсы, темы и пользовательские цвета можно поместить в один словарь ресурсов **MyCustomTheme** и при необходимости указать область применения, не беспокоясь о беспорядке в разметке макета.

### <a name="other-ways-to-define-color-resources"></a>Другие способы определения цветовых ресурсов

Класс ColorPaletteResources также позволяет размещать системные цвета и определять их непосредственно внутри ресурса как в оболочке, а не как в строке:

``` xaml
<ColorPaletteResources x:Key="Dark">
    <Color x:Key="SystemBaseLowColor">Goldenrod</Color>
</ColorPaletteResources>
```

## <a name="usability"></a>Удобство использования

:::row:::
    :::column:::
![Иллюстрация контрастности](images/color/illo-contrast.svg)
    :::column-end:::
    :::column span="2":::
**Контрастность**

Убедитесь, что элементы и изображения достаточно контрастные, чтобы их можно было различать независимо от темы или цвета элементов.

В ходе принятия решения о применении цветов в приложении следует ориентироваться на доступность. Используйте приведенные ниже инструкции, чтобы убедиться, что приложение доступно для использования максимальному числу пользователей.
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
![Иллюстрация контрастности](images/color/illo-lighting.svg)
    :::column-end:::
    :::column span="2":::
**Освещение**

Имейте в виду, что особенности освещения могут влиять на удобство использования приложения. Например, страница с черным фоном может быть нечитаемой на улице из-за бликов, а страница с белым фоном может вызывать дискомфорт при просмотре в темной комнате.
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
![Иллюстрация контрастности](images/color/illo-colorblindness.svg)
    :::column-end:::
    :::column span="2":::
**Цветовая слепота**

Учитывайте то, как особенности восприятия цвета у некоторых пользователей могут влиять на удобство использования приложения. Например, некоторые пользователи испытывают трудности при различении красных и зеленых элементов. Около **8 % мужчин** и **0,5 % женщин** не различают красный и зеленый цвета, поэтому не используйте их сочетание в качестве единственного отличия между элементами приложения.
    :::column-end:::
:::row-end:::

## <a name="related-articles"></a>Связанные статьи

- [Стили XAML](../controls-and-patterns/xaml-styles.md)
- [Ресурсы темы XAML](../controls-and-patterns/xaml-theme-resources.md)
