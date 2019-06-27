---
Description: Ресурсы темы XAML представляют собой набор ресурсов, применяющих различные значения в зависимости от того, какая тема системы активна.
MS-HAID: dev\_ctrl\_layout\_txt.xaml\_theme\_resources
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: Ресурсы темы XAML
ms.assetid: 41B87DBF-E7A2-44E9-BEBA-AF6EEBABB81B
label: XAML theme resources
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9544988837d44f42d963b268a2ce3d37cce83952
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66364111"
---
# <a name="xaml-theme-resources"></a>Ресурсы темы XAML

Ресурсы темы XAML представляют собой набор ресурсов, применяющих различные значения в зависимости от того, какая тема системы активна. Платформа XAML поддерживает три темы: Light, Dark и HighContrast.

**Предварительные условия**: Предполагается, что вы уже прочитали раздел [Справочники по ResourceDictionary и ресурсу XAML](resourcedictionary-and-xaml-resource-references.md).

## <a name="theme-resources-v-static-resources"></a>Ресурсы темы и статические ресурсы

Существует два расширения разметки XAML, которые могут ссылаться на ресурс XAML из существующего словаря ресурсов XAML: [расширение разметки {StaticResource}](../../xaml-platform/staticresource-markup-extension.md) и [расширение разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md).

Анализ [расширения разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md) происходит при загрузке приложения и затем при каждом изменении темы во время выполнения. Это, как правило, является результатом изменения пользователем параметров устройства или программных изменений текущей темы в приложении.

А анализ [расширения разметки {StaticResource}](../../xaml-platform/staticresource-markup-extension.md) происходит только при первой загрузке XAML приложением. Обновление при этом не выполняется. Этот процесс похож на функцию поиска и замены в XAML с фактическим значением выполнения при запуске приложения.

## <a name="theme-resources-in-the-resource-dictionary-structure"></a>Ресурсы темы в структуре словаря ресурсов

Каждый ресурс темы является частью XAML-файла themeresources.xaml. Для целей проектирования файл themeresources.xaml находится в папке \\(Program Files)\\Windows Kits\\10\\DesignTime\\CommonConfiguration\\Neutral\\UAP\\&lt;SDK version&gt;\\Generic, созданной при установке пакета средств разработки программного обеспечения для Windows (SDK). Словари ресурсов в themeresources.xaml также воспроизведены в generic.xaml в том же каталоге.

Среда выполнения Windows не использует эти физические файлы для поиска во время выполнения. Поэтому они специально находятся в папке DesignTime и не копируются в приложения по умолчанию. Вместо этого такие словари ресурсов существуют в памяти как часть самой среды выполнения Windows, и ссылки на ресурсы XAML приложения — на ресурсы темы (или системные ресурсы) — разрешаются там во время выполнения.

## <a name="guidelines-for-custom-theme-resources"></a>Рекомендации по использованию пользовательских ресурсов темы

Следуйте данным рекомендациям, чтобы определить и использовать собственные настраиваемые ресурсы темы:

- Определить словари для тем Light и Dark в дополнение к словарю HighContrast. Хотя можно создать словарь [ResourceDictionary](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary) с основным значением Default, предпочтительнее использовать явные параметры Light, Dark и HighContrast.

- Используйте [расширение исправления {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md) в стилях, методах установки, шаблонах элементов управления, методах определения свойств и анимациях.

- Не используйте [расширение разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md) в определениях ресурсов в словарях [ThemeDictionaries](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary.themedictionaries). Вместо этого используйте [расширение разметки {StaticResource}](../../xaml-platform/staticresource-markup-extension.md).

    ИСКЛЮЧЕНИЕ. Можно использовать [расширение разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md) для ссылки на ресурсы, которые не зависят от темы приложения в словарях [ThemeDictionaries](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary.themedictionaries). Примерами таких ресурсов являются ресурсы цветов элементов, такие как `SystemAccentColor`, или ресурсы системных цветов, которые обычно имеют префиксы SystemColor, такие как `SystemColorButtonFaceColor`.

> [!CAUTION]
> Если вы не будете следовать этим рекомендациям, вы можете столкнуться с непредвиденным поведением, связанным с темами в приложении. Дополнительные сведения см. в разделе [Устранение неполадок с ресурсами тем](#troubleshooting-theme-resources).

## <a name="the-xaml-color-ramp-and-theme-dependent-brushes"></a>Кисти, зависимые от темы, и линейно изменяющийся цветовой шаблон XAML

Комбинированный набор цветов для тем Light, Dark и HighContrast составляет *Линейно изменяющийся цветовой шаблон Windows* в XAML. Если вы хотите изменить тему системы или применить тему системы к вашим собственным элементам XAML, важно понять структуру ресурсов цвета.

Дополнительные сведения о том, как применять цвета в приложение UWP, см. в разделе [Цвета в приложениях UWP](../style/color.md).

### <a name="light-and-dark-theme-colors"></a>Цвета светлой и темной тем

Платформа XAML предоставляет набор именованных ресурсов [Color](/uwp/api/Windows.UI.Color) со значениями, подобранными для тем Light и Dark. Разделы, используемые для ссылки, должны иметь следующий формат имени: `System[Simple Light/Dark Name]Color`.

В этой таблице перечислены раздел, простое имя и строковое представление цвета (в формате \#aarrggbb) для ресурсов Light и Dark, предоставляемых платформой XAML. Этот раздел используется для ссылки на ресурс в приложении. "Простое имя Light/Dark" используется в рамках соглашения об именовании кистей, о котором будет рассказано позже.

| Раздел                             | Простое имя Light/Dark | Светлая      | Темная       |
|---------------------------------|------------------------|------------|------------|
| SystemAltHighColor              | AltHigh                | \#FFFFFFFF | \#FF000000 |
| SystemAltLowColor               | AltLow                 | \#33FFFFFF | \#33000000 |
| SystemAltMediumColor            | AltMedium              | \#99FFFFFF | \#99000000 |
| SystemAltMediumHighColor        | AltMediumHigh          | \#CCFFFFFF | \#CC000000 |
| SystemAltMediumLowColor         | AltMediumLow           | \#66FFFFFF | \#66000000 |
| SystemBaseHighColor             | BaseHigh               | \#FF000000 | \#FFFFFFFF |
| SystemBaseLowColor              | BaseLow                | \#33000000 | \#33FFFFFF |
| SystemBaseMediumColor           | BaseMedium             | \#99000000 | \#99FFFFFF |
| SystemBaseMediumHighColor       | BaseMediumHigh         | \#CC000000 | \#CCFFFFFF |
| SystemBaseMediumLowColor        | BaseMediumLow          | \#66000000 | \#66FFFFFF |
| SystemChromeAltLowColor         | ChromeAltLow           | \#FF171717 | \#FFF2F2F2 |
| SystemChromeBlackHighColor      | ChromeBlackHigh        | \#FF000000 | \#FF000000 |
| SystemChromeBlackLowColor       | ChromeBlackLow         | \#33000000 | \#33000000 |
| SystemChromeBlackMediumLowColor | ChromeBlackMediumLow   | \#66000000 | \#66000000 |
| SystemChromeBlackMediumColor    | ChromeBlackMedium      | \#CC000000 | \#CC000000 |
| SystemChromeDisabledHighColor   | ChromeDisabledHigh     | \#FFCCCCCC | \#FF333333 |
| SystemChromeDisabledLowColor    | ChromeDisabledLow      | \#FF7A7A7A | \#FF858585 |
| SystemChromeHighColor           | ChromeHigh             | \#FFCCCCCC | \#FF767676 |
| SystemChromeLowColor            | ChromeLow              | \#FFF2F2F2 | \#FF171717 |
| SystemChromeMediumColor         | ChromeMedium           | \#FFE6E6E6 | \#FF1F1F1F |
| SystemChromeMediumLowColor      | ChromeMediumLow        | \#FFF2F2F2 | \#FF2B2B2B |
| SystemChromeWhiteColor          | ChromeWhite            | \#FFFFFFFF | \#FFFFFFFF |
| SystemListLowColor              | ListLow                | \#19000000 | \#19FFFFFF |
| SystemListMediumColor           | ListMedium             | \#33000000 | \#33FFFFFF |

:::row:::
    :::column:::
        #### Light theme
    :::column-end:::
    :::column:::
        #### Dark theme
    :::column-end:::
:::row-end:::

#### <a name="base"></a>Base

:::row:::
    :::column:::
        ![The base light theme](images/themes/light-base.png)
    :::column-end:::
    :::column:::
        ![The base dark theme](images/themes/dark-base.png)
    :::column-end:::
:::row-end:::

#### <a name="alt"></a>Альтернативная

:::row:::
    :::column:::
        ![The alt light theme](images/themes/light-alt.png)
    :::column-end:::
    :::column:::
        ![The alt dark theme](images/themes/dark-alt.png)
    :::column-end:::
:::row-end:::

#### <a name="list"></a>List

:::row:::
    :::column:::
        ![The list light theme](images/themes/light-list.png)
    :::column-end:::
    :::column:::
        ![The list dark theme](images/themes/dark-list.png)
    :::column-end:::
:::row-end:::

#### <a name="chrome"></a>Хром

:::row:::
    :::column:::
        ![The chrome light theme](images/themes/light-chrome.png)
    :::column-end:::
    :::column:::
        ![The chrome dark theme](images/themes/dark-chrome.png)
    :::column-end:::
:::row-end:::

### <a name="windows-system-high-contrast-colors"></a>Высококонтрастные цвета системы Windows

В дополнение к набору ресурсов, предоставляемых платформой XAML, существует набор значений цвета, полученных из палитры Windows. Эти цвета не привязаны к среде выполнения Windows или приложений универсальной платформы Windows (UWP). Однако многие из ресурсов [Brush](/uwp/api/Windows.UI.Xaml.Media.Brush) XAML используют эти цвета, когда система работает (и приложение выполняется) с темой HighContrast. Платформа XAML предоставляет эти системные цвета как ключевые ресурсы. Разделы должны иметь следующий формат имени: `SystemColor[name]Color`.

В этой таблице перечислены системные цвета, предоставляемые XAML в форме объектов ресурса, полученных из палитры Windows. В столбце "Имя специальной возможности" указана метка цвета в пользовательском интерфейсе параметров Windows. В столбце "Простое имя HighContrast" указано слово-описание того, как данный цвет применяется к общим элементам управления XAML. Оно используется в рамках соглашения о наименовании кистей, о котором мы расскажем позже. В столбце "Первоначальное стандартное значение" указаны значения, которые вы получите, если система вообще не работает с высокой контрастностью.

| Раздел                           | Имя специальной возможности            | Простое имя HighContrast | Первоначальное стандартное значение |
|-------------------------------|--------------------------------|--------------------------|-----------------|
| SystemColorButtonFaceColor    | **Текст на кнопке** (фон)   | Фон               | \#FFF0F0F0      |
| SystemColorButtonTextColor    | **Текст на кнопке** (передний план)   | Передний план               | \#FF000000      |
| SystemColorGrayTextColor      | **Отключенный текст**              | Отключено                 | \#FF6D6D6D      |
| SystemColorHighlightColor     | **Выделенный текст** (фон) | Выделить                | \#FF3399FF      |
| SystemColorHighlightTextColor | **Выделенный текст** (передний план) | HighlightAlt             | \#FFFFFFFF      |
| SystemColorHotlightColor      | **Гиперссылки**                 | Гиперссылка                | \#FF0066CC      |
| SystemColorWindowColor        | **Фон**                 | PageBackground           | \#FFFFFFFF      |
| SystemColorWindowTextColor    | **Текст**                       | PageText                 | \#FF000000      |

Windows предоставляет разные тем с высокой контрастностью и позволяет пользователю задавать определенные цвета для параметров высокой контрастности в Центре специальных возможностей, как показано ниже. Поэтому предоставить окончательный список значений высококонтрастных цветов невозможно.

![Пользовательский интерфейс параметров высокой контрастности Windows](images/high-contrast-settings.png)

Дополнительные сведения о поддержке тем с высокой контрастностью см. в разделе [Темы с высокой контрастностью](https://docs.microsoft.com/windows/uwp/accessibility/high-contrast-themes).

### <a name="system-accent-color"></a>Цветовая тема системы

В дополнение к цветам тем с высокой контрастностью цветовая тема системы предоставляется в форме особого ресурса цвета с использованием раздела `SystemAccentColor`. Во время выполнения этот ресурс получает цвет, который пользователь выбрал в качестве цветовой схемы в параметрах персонализации Windows.

> [!NOTE]
> Несмотря н то что можно переопределить ресурсы системных цветов, рекомендуется придерживаться заданных пользователем вариантов использования цвета, особенно для параметров высокой контрастности.

### <a name="theme-dependent-brushes"></a>Кисти, зависимые от темы

Ресурсы цвета, рассмотренные в предыдущих разделах, используются для определения свойства [Color](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush.Color) ресурсов [SolidColorBrush](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) в словарях ресурсов темы системы. Ресурсы кисти используются для применения цвета к элементам XAML. Разделы ресурсов кисти должны иметь следующий формат имени: `SystemControl[Simple HighContrast name][Simple light/dark name]Brush`. Например, `SystemControlBackroundAltHighBrush`.

Рассмотрим, как определяется значение цвета для этой кисти во время выполнения. В словарях ресурсов Light и Dark эта кисть определена следующим образом:

`<SolidColorBrush x:Key="SystemControlBackgroundAltHighBrush" Color="{StaticResource SystemAltHighColor}"/>`

В словаре ресурсов HighContrast эта кисть определена так:

`<SolidColorBrush x:Key="SystemControlBackgroundAltHighBrush" Color="{ThemeResource SystemColorButtonFaceColor}"/>`

Когда кисть применяется к элементу XAML, во время выполнения ее цвет определяется текущей темой, как показано в следующей таблице.

| Тема        | Простое имя цвета | Ресурс цвета             | Значение выполнения                                              |
|--------------|-------------------|----------------------------|------------------------------------------------------------|
| Светлая        | AltHigh           | SystemAltHighColor         | \#FFFFFFFF                                                 |
| Темная         | AltHigh           | SystemAltHighColor         | \#FF000000                                                 |
| HighContrast | Фон        | SystemColorButtonFaceColor | Цвет, указанный в параметрах для фона кнопки. |

Можно использовать схему имен `SystemControl[Simple HighContrast name][Simple light/dark name]Brush`, чтобы определить, какую кисть применить к вашим собственным элементам XAML.

<!--
For many examples of how the brushes are used in the XAML control templates, see the [Default control styles and templates](default-control-styles-and-templates.md).
-->

> [!NOTE]
> Не все сочетания \[*Простого имени HighContrast*\]\[*Простого имени Light/Dark*\] предоставляются как ресурсы кисти.

## <a name="the-xaml-type-ramp"></a>Набор шрифтов XAML

Файл themeresources.xaml определяет несколько ресурсов, определяющих стиль [Style](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style), который можно применить к контейнерам текста в вашем пользовательском интерфейсе, в частности для [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) или [RichTextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock). Эти стили не являются неявными стандартными стилями. Они предоставляются, чтобы упростить создание определений пользовательского интерфейса XAML, соответствующих *набору шрифтов Windows*, указанному в документе [Руководство по шрифтам](../style/typography.md).

Эти стили предназначены для атрибутов текста, которые вы хотите применить ко всему контейнеру текста. Если вам нужно, чтобы стили применялись только к отдельным частям текста, следует задать атрибуты для элементов текста в контейнере, например [Run](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Run) в [TextBlock.Inlines](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.inlines) или [Paragraph](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Paragraph) в [RichTextBlock.Blocks](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richtextblock.blocks).

При применении к [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) стили выглядят таким образом:

![Стили текстовых блоков](../style/images/type/text-block-type-ramp.svg)

```XAML
<TextBlock Text="Header" Style="{StaticResource HeaderTextBlockStyle}"/>
<TextBlock Text="SubHeader" Style="{StaticResource SubheaderTextBlockStyle}"/>
<TextBlock Text="Title" Style="{StaticResource TitleTextBlockStyle}"/>
<TextBlock Text="SubTitle" Style="{StaticResource SubtitleTextBlockStyle}"/>
<TextBlock Text="Base" Style="{StaticResource BaseTextBlockStyle}"/>
<TextBlock Text="Body" Style="{StaticResource BodyTextBlockStyle}"/>
<TextBlock Text="Caption" Style="{StaticResource CaptionTextBlockStyle}"/>
```

Руководство по использованию набора шрифтов UWP в приложении см. в разделе [Шрифтовое оформление приложений UWP](../style/typography.md).

### <a name="basetextblockstyle"></a>BaseTextBlockStyle

**TargetType**: [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock)

Предоставляет общие свойства для всех других стилей контейнеров [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock).

```XAML
<!-- Usage -->
<TextBlock Text="Base" Style="{StaticResource BaseTextBlockStyle}"/>

<!-- Style definition -->
<Style x:Key="BaseTextBlockStyle" TargetType="TextBlock">
    <Setter Property="FontFamily" Value="Segoe UI"/>
    <Setter Property="FontWeight" Value="SemiBold"/>
    <Setter Property="FontSize" Value="14"/>
    <Setter Property="TextTrimming" Value="None"/>
    <Setter Property="TextWrapping" Value="Wrap"/>
    <Setter Property="LineStackingStrategy" Value="MaxHeight"/>
    <Setter Property="TextLineBounds" Value="Full"/>
</Style>
```

### <a name="headertextblockstyle"></a>HeaderTextBlockStyle

```XAML
<!-- Usage -->
<TextBlock Text="Header" Style="{StaticResource HeaderTextBlockStyle}"/>

<!-- Style definition -->
<Style x:Key="HeaderTextBlockStyle" TargetType="TextBlock"
       BasedOn="{StaticResource BaseTextBlockStyle}">
    <Setter Property="FontSize" Value="46"/>
    <Setter Property="FontWeight" Value="Light"/>
    <Setter Property="OpticalMarginAlignment" Value="TrimSideBearings"/>
</Style>
```

### <a name="subheadertextblockstyle"></a>SubheaderTextBlockStyle

```XAML
<!-- Usage -->
<TextBlock Text="SubHeader" Style="{StaticResource SubheaderTextBlockStyle}"/>

<!-- Style definition -->
<Style x:Key="SubheaderTextBlockStyle" TargetType="TextBlock" 
       BasedOn="{StaticResource BaseTextBlockStyle}">
    <Setter Property="FontSize" Value="34"/>
    <Setter Property="FontWeight" Value="Light"/>
    <Setter Property="OpticalMarginAlignment" Value="TrimSideBearings"/>
</Style>
```

### <a name="titletextblockstyle"></a>TitleTextBlockStyle

```XAML
<!-- Usage -->
<TextBlock Text="Title" Style="{StaticResource TitleTextBlockStyle}"/>

<!-- Style definition -->
<Style x:Key="TitleTextBlockStyle" TargetType="TextBlock" 
       BasedOn="{StaticResource BaseTextBlockStyle}">
    <Setter Property="FontWeight" Value="SemiLight"/>
    <Setter Property="FontSize" Value="24"/>
    <Setter Property="OpticalMarginAlignment" Value="TrimSideBearings"/>
</Style>
```

### <a name="subtitletextblockstyle"></a>SubtitleTextBlockStyle

```XAML
<!-- Usage -->
<TextBlock Text="SubTitle" Style="{StaticResource SubtitleTextBlockStyle}"/>

<!-- Style definition -->
<Style x:Key="SubtitleTextBlockStyle" TargetType="TextBlock" 
       BasedOn="{StaticResource BaseTextBlockStyle}">
    <Setter Property="FontWeight" Value="Normal"/>
    <Setter Property="FontSize" Value="20"/>
    <Setter Property="OpticalMarginAlignment" Value="TrimSideBearings"/>
</Style>
```

### <a name="bodytextblockstyle"></a>BodyTextBlockStyle

```XAML
<!-- Usage -->
<TextBlock Text="Body" Style="{StaticResource BodyTextBlockStyle}"/>

<!-- Style definition -->
<Style x:Key="BodyTextBlockStyle" TargetType="TextBlock" 
       BasedOn="{StaticResource BaseTextBlockStyle}">
    <Setter Property="FontWeight" Value="Normal"/>
    <Setter Property="FontSize" Value="14"/>
</Style>
```

### <a name="captiontextblockstyle"></a>CaptionTextBlockStyle

```XAML
<!-- Usage -->
<TextBlock Text="Caption" Style="{StaticResource CaptionTextBlockStyle}"/>

<!-- Style definition -->
<Style x:Key="CaptionTextBlockStyle" TargetType="TextBlock" 
       BasedOn="{StaticResource BaseTextBlockStyle}">
    <Setter Property="FontSize" Value="12"/>
    <Setter Property="FontWeight" Value="Normal"/>
</Style>
```

### <a name="baserichtextblockstyle"></a>BaseRichTextBlockStyle

**TargetType**: [RichTextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock)

Предоставляет общие свойства для всех других стилей контейнеров [RichTextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock).

```XAML
<!-- Usage -->
<RichTextBlock Style="{StaticResource BaseRichTextBlockStyle}">
    <Paragraph>Rich text.</Paragraph>
</RichTextBlock>

<!-- Style definition -->
<Style x:Key="BaseRichTextBlockStyle" TargetType="RichTextBlock">
    <Setter Property="FontFamily" Value="Segoe UI"/>
    <Setter Property="FontWeight" Value="SemiBold"/>
    <Setter Property="FontSize" Value="14"/>
    <Setter Property="TextTrimming" Value="None"/>
    <Setter Property="TextWrapping" Value="Wrap"/>
    <Setter Property="LineStackingStrategy" Value="MaxHeight"/>
    <Setter Property="TextLineBounds" Value="Full"/>
    <Setter Property="OpticalMarginAlignment" Value="TrimSideBearings"/>
</Style>
```

### <a name="bodyrichtextblockstyle"></a>BodyRichTextBlockStyle

```XAML
<!-- Usage -->
<RichTextBlock Style="{StaticResource BodyRichTextBlockStyle}">
    <Paragraph>Rich text.</Paragraph>
</RichTextBlock>

<!-- Style definition -->
<Style x:Key="BodyRichTextBlockStyle" TargetType="RichTextBlock" BasedOn="{StaticResource BaseRichTextBlockStyle}">
    <Setter Property="FontWeight" Value="Normal"/>
</Style>
```

**Примечание**.  Стили [RichTextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock) не содержат всех стилей таблицы шрифтов, которые содержит стиль [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock), главным образом потому, что блочная объектная модель документа для **RichTextBlock** упрощает настройку атрибутов в отдельных элементах текста. Кроме того, параметр [TextBlock.Text](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.text), использующий свойство XAML-содержимого, описывает ситуацию, где отсутствуют элементы текста, к которым нужно применить стиль, поэтому стиль необходимо применить к контейнеру. Это не является проблемой для **RichTextBlock**, так как его текстовое содержимое всегда должно находиться в определенных элементах текста, таких как [Paragraph](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Paragraph). Именно в них вы можете применить стили XAML для заголовка страницы, подзаголовка страницы и аналогичных определений из таблицы шрифтов.

## <a name="miscellaneous-named-styles"></a>Различные именованные стили

Существует дополнительный набор определений [Style](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) с ключами, которые можно применить, чтобы стиль кнопки [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button) отличался от стандартного неявного стиля.

### <a name="textblockbuttonstyle"></a>TextBlockButtonStyle

**TargetType**: [ButtonBase](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.ButtonBase)

Примените этот стиль к [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button), если необходимо отобразить текст, который должен нажать пользователь для выполнения действия. Стиль текста будет соответствовать цветовой схеме и выделять его как интерактивный элемент. Кроме того, для удобной работы текстом будут доступны прямоугольники фокуса. В отличие от неявного стиля [HyperlinkButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.HyperlinkButton), при использовании **TextBlockButtonStyle** текст не подчеркивается.

Кроме того, этот шаблон определяет, что стиль представленного текста должен использовать **SystemControlHyperlinkBaseMediumBrush** (для состояния PointerOver), **SystemControlHighlightBaseMediumLowBrush** (для состояния Pressed) и **SystemControlDisabledBaseLowBrush** (для состояния Disabled).

Вот пример кнопки [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button) с примененным к ней ресурсом **TextBlockButtonStyle**.

```XAML
<Button Content="Clickable text" Style="{StaticResource TextBlockButtonStyle}"
        Click="Button_Click"/>
```

Он выглядит следующим образом:

![Стиль кнопки подобран так, чтобы она выглядела как текст](images/styles-textblock-button-style.png)

### <a name="navigationbackbuttonnormalstyle"></a>NavigationBackButtonNormalStyle

**TargetType**: [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button)

Этот стиль [Style](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) предоставляет полный шаблон для элемента управления [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button), который может использоваться как кнопка навигации "Назад" для приложения навигации. Размер по умолчанию: 40 x 40 пикселей. Для настройки стиля можно либо явным образом задать [Height](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height), [Width](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width), [FontSize](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.fontsize) и другие свойства кнопки **Button**, либо создать производный стиль, используя [BasedOn](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.basedon).

Вот пример кнопки [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button) с примененным к ней ресурсом **NavigationBackButtonNormalStyle**.

```XAML
<Button Style="{StaticResource NavigationBackButtonNormalStyle}" />
```

Он выглядит следующим образом:

![Кнопка, стилизованная как кнопка "Назад"](images/styles-back-button-normal.png)

### <a name="navigationbackbuttonsmallstyle"></a>NavigationBackButtonSmallStyle

**TargetType**: [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button)

Этот стиль [Style](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) предоставляет полный шаблон для элемента управления [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button), который может использоваться как кнопка навигации "Назад" для приложения навигации. Он похож на стиль **NavigationBackButtonNormalStyle**, но его размер составляет 30 x 30 пикселей.

Вот пример кнопки [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button) с примененным к ней ресурсом **NavigationBackButtonSmallStyle**.

```XAML
<Button Style="{StaticResource NavigationBackButtonSmallStyle}" />
```

## <a name="troubleshooting-theme-resources"></a>Устранение неполадок с ресурсами тем

Если вы не будете следовать [рекомендациям по использованию ресурсов темы](#guidelines-for-custom-theme-resources), вы можете столкнуться с непредвиденным поведением, связанным с темами в приложении.

Например, при открытии всплывающего элемента, для которого используется светлая тема, элементы приложения, использующего темную тему, также изменятся, как если бы для них была задана светлая тема. Или если осуществляется переход к странице со светлой темой, а затем назад, оригинальная страница с темной темой (полностью или частично) будет выглядеть так, будто для нее используется светлая тема.

Обычно проблемы такого типа происходят, если для поддержки сценариев с высокой контрастностью задается тема Default и HighContrast, а затем в разных частях приложения используются как тема Light, так и тема Dark.

Например, рассмотрим следующее определение словаря темы.

```XAML
<!-- DO NOT USE. THIS XAML DEMONSTRATES AN ERROR. -->
<ResourceDictionary>
  <ResourceDictionary.ThemeDictionaries>
    <ResourceDictionary x:Key="Default">
      <SolidColorBrush x:Key="myBrush" Color="{ThemeResource SystemBaseHighColor}"/>
    </ResourceDictionary>
    <ResourceDictionary x:Key="HighContrast">
      <SolidColorBrush x:Key="myBrush" Color="{ThemeResource SystemColorButtonFaceColor}"/>
    </ResourceDictionary>
  </ResourceDictionary.ThemeDictionaries>
</ResourceDictionary>
```

Интуитивно всё выглядит правильно. Необходимо изменить цвет, на который указывает `myBrush`, при использовании высокой контрастности, но для случаев, когда высокая контрастность не используется, применяйте [расширение разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md), чтобы убедиться, что точки `myBrush` используют правильный цвет темы. Если в приложении никогда не устанавливается [FrameworkElement.RequestedTheme](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.requestedtheme) для элементов в визуальном дереве темы, все должно работать правильно. Однако в приложении сразу возникнут проблемы, если вы начнете изменять темы различных частей визуального дерева.

Проблема возникает, поскольку кисти являются общими ресурсами, в отличие от большинства других типов XAML. Если у вас есть 2 элемента в поддеревьях XAML с разными темами, но которые ссылаются на один и тот же ресурс кисти, то когда платформа проходит по каждому поддереву, чтобы обновить выражения [расширения разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md), изменения, которые вы внесли в общий ресурс кисти, отображаются и в другом поддереве, хотя это не было запланированным результатом ваших действий.

Чтобы это исправить, замените словарь Default отдельными словарями для тем Light и Dark и используйте также HighContrast:

```XAML
<!-- DO NOT USE. THIS XAML DEMONSTRATES AN ERROR. -->
<ResourceDictionary>
  <ResourceDictionary.ThemeDictionaries>
    <ResourceDictionary x:Key="Light">
      <SolidColorBrush x:Key="myBrush" Color="{ThemeResource SystemBaseHighColor}"/>
    </ResourceDictionary>
    <ResourceDictionary x:Key="Dark">
      <SolidColorBrush x:Key="myBrush" Color="{ThemeResource SystemBaseHighColor}"/>
    </ResourceDictionary>
    <ResourceDictionary x:Key="HighContrast">
      <SolidColorBrush x:Key="myBrush" Color="{ThemeResource SystemColorButtonFaceColor}"/>
    </ResourceDictionary>
  </ResourceDictionary.ThemeDictionaries>
</ResourceDictionary>
```

Однако проблема повторится в случае ссылок на эти ресурсы в унаследованных свойствах, например [Foreground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground). Ваш шаблон пользовательского элемента управления может задать цвет переднего плана элемента, используя [расширение разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md), но когда платформа будет передавать значение унаследованного элемента дочерним элементам, она предоставит прямую ссылку на ресурс, который был разрешен выражением расширения разметки {ThemeResource}. Это приводит к возникновению проблем, когда платформа обрабатывает изменения темы, проходя по визуальному дереву элемента управления. Она заново анализирует выражение расширения разметки {ThemeResource}, чтобы получить новый ресурс кисти, но еще не передает ссылку на него дочерним элементам элемента управления; это происходит позже, например во время следующего измерения.

В результате после прохода визуального дерева элемента управления в ответ на изменение темы платформа проходит дочерние элементы и обновляет все выражения [расширения разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md), установленные для них или для объектов, заданных в их свойствах. Именно в этот момент возникает проблема. Платформа проходит ресурс кисти и, поскольку цвет ее задается с помощью расширения разметки {ThemeResource}, происходит повторный анализ.

На этом этапе кажется, что платформа загрязняет словарь темы, поскольку теперь в нем содержится ресурс из одного словаря, цвет в котором задается из другого словаря.

Чтобы устранить эту проблему, используйте [расширение разметки {StaticResource}](../../xaml-platform/staticresource-markup-extension.md) вместо [расширения разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md). В результате выполнения этих рекомендаций словари темы теперь выглядят следующим образом:

```XAML
<ResourceDictionary>
  <ResourceDictionary.ThemeDictionaries>
    <ResourceDictionary x:Key="Light">
      <SolidColorBrush x:Key="myBrush" Color="{StaticResource SystemBaseHighColor}"/>
    </ResourceDictionary>
    <ResourceDictionary x:Key="Dark">
      <SolidColorBrush x:Key="myBrush" Color="{StaticResource SystemBaseHighColor}"/>
    </ResourceDictionary>
    <ResourceDictionary x:Key="HighContrast">
      <SolidColorBrush x:Key="myBrush" Color="{ThemeResource SystemColorButtonFaceColor}"/>
    </ResourceDictionary>
  </ResourceDictionary.ThemeDictionaries>
</ResourceDictionary>
```

Обратите внимание, что [расширение разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md) по-прежнему используется в словаре HighContrast вместо [расширения разметки {StaticResource}](../../xaml-platform/staticresource-markup-extension.md). Данная ситуация относится к исключению, которое было описано выше в этих рекомендациях. Большинство значений кистей в теме HighContrast используют варианты цветов, которые глобально контролируются системой, но представляются в XAML в виде специально именованных ресурсов (с префиксом SystemColor в имени). Система предоставляет пользователю возможность задать определенные цвета, которые будут использоваться для настройки схем высокой контрастности в Центре специальных возможностей. Эти варианты цвета применяются к специально именованным ресурсам. Платформа XAML использует одно и то же событие изменения темы, чтобы также обновить кисти, если обнаружится, что они были изменены на системном уровне. Поэтому здесь используется расширение разметки {ThemeResource}.