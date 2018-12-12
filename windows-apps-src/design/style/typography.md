---
description: Узнайте, как использовать шрифтовое оформление текста в приложении, чтобы помочь пользователям понимать содержимое.
title: Шрифтовое оформление текста в приложениях UWP
ms.date: 04/06/2018
ms.topic: article
keywords: Windows10, UWP
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 0943273dab239669be75b30070222d698246aa41
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8944726"
---
# <a name="typography"></a>Шрифтовое оформление

![главное изображение](images/header-typography.svg)

Основная цель шрифтового оформления как визуального представления языка — передача информации. Стиль шрифтов никогда не должен затмевать эту цель. В этой статье мы обсудим, как использовать шрифтовое оформление в вашем приложение UWP, чтобы помочь пользователям легко понимать содержимое.

## <a name="font"></a>Шрифт

Вам следует использовать один шрифт для всего пользовательского интерфейса приложения, и мы рекомендуем выбрать шрифт по умолчанию для приложений UWP, **Segoe UI**. Он создан для обеспечения оптимальной четкости для размеров любых размеров и любой плотности пикселей, а также отличается четкой, легкой и открытой эстетикой, которая дополняет содержимое системы.

![Пример текста с использованием шрифта Segoe UI](images/type/segoe-sample.svg)

Сведения об отображения текста на языках, отличных от английского, или выборе другого шрифта для приложения см. в разделах [Языки](#Languages) и [Шрифты](#Fonts), в которых описываются шрифты, рекомендованные для приложений UWP.

:::row:::
    :::column:::
        ![do](images/do.svg)
        Pick one font for your UI.
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        Don't mix multiple fonts.
    :::column-end:::
:::row-end:::

## <a name="size-and-scaling"></a>Размер и масштабирование

Размеры шрифтов в приложениях UWP масштабируются автоматически на всех устройствах. Алгоритм масштабирования позволяет добиться того, что шрифт размером 24 пикселя на Surface Hub, который располагается на расстоянии трех метров от пользователя, будет таким же читаемым, как и шрифт аналогичного размера, отображаемый на телефоне с диагональю экрана 5 дюймов, который располагается на расстоянии нескольких сантиметров от пользователя.

![Дистанция просмотра для различных устройств](images/type/scaling-chart.svg)

Из-за того, как работает система масштабирования, вы работаете с эффективными пикселями, а не физическими пикселями, и вам не следует изменять размер шрифта для различных размеров и разрешений экрана.

:::row:::
    :::column:::
        ![do](images/do.svg)
        Follow the UWP [type ramp](#type-ramp) sizing.
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        Use a font size smaller than 12 px.
    :::column-end:::
:::row-end:::

## <a name="hierarchy"></a>Иерархия

:::row:::
    :::column:::
        Users rely on visual hierarchy when scanning a page: headers summarize content, and body text provides more detail. To create a clear visual hierarchy in your app, follow the UWP type ramp.
    :::column-end:::
    :::column:::
        ![text block styles](images/type/type-hierarchy.svg)
    :::column-end:::
:::row-end:::

### <a name="type-ramp"></a>Набор шрифтов

Набор шрифтов UWP определяет важные отношения между начертаниями шрифтов на странице, помогая пользователям легко читать содержимое. Все размеры указываются в эффективных пикселях и оптимизированы для приложений UWP, работающих на всех устройствах.

![Набор шрифтов](images/type/type-ramp.svg)

### <a name="using-the-type-ramp"></a>Использование набора шрифтов

:::row:::
    :::column:::
        You can access levels of the type ramp as XAML [static resources](../controls-and-patterns/xaml-theme-resources.md#the-xaml-type-ramp). The styles follow the `*TextBlockStyle` naming convention.
    :::column-end:::
    :::column:::
        ![text block styles](images/type/text-block-type-ramp.svg)
    :::column-end:::
:::row-end:::

```XAML
<TextBlock Text="Header" Style="{StaticResource HeaderTextBlockStyle}"/>
<TextBlock Text="SubHeader" Style="{StaticResource SubheaderTextBlockStyle}"/>
<TextBlock Text="Title" Style="{StaticResource TitleTextBlockStyle}"/>
<TextBlock Text="SubTitle" Style="{StaticResource SubtitleTextBlockStyle}"/>
<TextBlock Text="Base" Style="{StaticResource BaseTextBlockStyle}"/>
<TextBlock Text="Body" Style="{StaticResource BodyTextBlockStyle}"/>
<TextBlock Text="Caption" Style="{StaticResource CaptionTextBlockStyle}"/>
```

:::row:::
    :::column:::
        ![do](images/do.svg)
        Use "Body" for most text.

        Use "Base" for titles when space is constrained.
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        Use "Caption" for primary action or any long strings.

        Use "Header" or "Subheader" if text needs to wrap.
    :::column-end:::
:::row-end:::

## <a name="alignment"></a>Выравнивание

Значение [TextAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.textalignment) по умолчанию— "Left", и в большинстве случаев такой подход, в котором содержимое сдвинуто влево и не выравнивается по правому краю, обеспечивает согласованную привязку содержимого и универсальный макет. Сведения о языках RTL см. в разделе [Настройка макета и шрифтов для поддержки глобализации](../globalizing/adjust-layout-and-fonts--and-support-rtl.md).

![Показан текст со сдвигом влево.](images/type/alignment.svg)

```xaml
<TextBlock TextAlignment="Left">
```

## <a name="character-count"></a>Количество символов

:::row:::
    :::column:::
        ![do](images/do.svg)
        Keep to 50–60 letters per line for ease of reading.
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        Less than 20 characters or more than 60 characters per line is difficult to read.
    :::column-end:::
:::row-end:::

## <a name="clipping-and-ellipses"></a>Обрезка и многоточия

Если текст выходит за пределы доступного места, мы рекомендуем обрезать его, что происходит по умолчанию для большинства [элементов управления текстом UWP](../controls-and-patterns/text-controls.md).

![Показана граница экрана устройства с обрезкой текста](images/type/clipping.svg)

```xaml
<TextBlock TextWrapping="WrapWholeWords" TextTrimming="Clip"/>
```

:::row:::
    :::column:::
        ![do](images/do.svg)
        Clip text, and wrap if multiple lines are enabled.
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        Use ellipses to avoid visual clutter.
    :::column-end:::
:::row-end:::

**Примечание**. Если контейнеры не определены точно (например, не указан отличающий цвет фона) или существует ссылка для просмотра всего текста, используйте многоточия.

## <a name="languages"></a>Языки 

Segoe UI — это наш шрифт для иврита, английского, греческого, армянского, грузинского, арабского и европейских языков. Для других языков см. следующие рекомендации.

### <a name="globalizinglocalizing-fonts"></a>Шрифты для глобализации и локализации

Используйте [API сопоставления шрифтов LanguageFont](https://docs.microsoft.com/uwp/api/Windows.Globalization.Fonts.LanguageFont) для программного доступа к рекомендуемому семейству, размеру, насыщенности и стилю шрифта определенного языка. Объект LanguageFont предоставляет доступ к нужной информации о шрифте для различных категорий содержимого, включая заголовки, уведомления, текст и шрифты доступных для редактирования текстов пользовательского интерфейса. Дополнительные сведения см. в разделе [Настройка макета и шрифтов для поддержки глобализации](../globalizing/adjust-layout-and-fonts--and-support-rtl.md).

### <a name="fonts-for-non-latin-languages"></a>Шрифты для языков, не использующих латинский алфавит

<table>
<thead>
<tr class="header">
<th align="left">Семейство шрифтов</th>
<th align="left">Стили</th>
<th align="left">Примечания</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="font-family: Embrima;">Ebrima</td>
<td align="left">Обычный, полужирный</td>
<td align="left">Шрифт пользовательского интерфейса для африканского письма (эфиопский, нко, османья, тифинаг, ваи).</td>
</tr>
<tr class="even">
<td style="font-family: Gadugi;">Gadugi</td>
<td align="left">Обычный, полужирный</td>
<td align="left">Шрифт пользовательского интерфейса для североамериканского письма (канадский силлабический, чероки).</td>
</tr>
<tr class="odd">
<td align="left" style="font-family: Leelawadee UI;">Leelawadee UI</td>
<td align="left">Обычный, полусветлый, полужирный</td>
<td align="left">Шрифт пользовательского интерфейса для письма народов Юго-Восточной Азии (бугийский, лаосский, кхмерский, тайский).</td>
</tr>
<tr class="odd">
<td align="left" style="font-family: Malgun Gothic;">Malgun Gothic</td>
<td align="left">Обычный</td>
<td align="left">Шрифт пользовательского интерфейса для корейского письма.</td>
</tr>
<tr class="even">
<td align="left" style="font-family: Microsoft JhengHei UI;">Microsoft JhengHei UI</td>
<td align="left">Обычный, полужирный, светлый</td>
<td align="left">Шрифт пользовательского интерфейса для традиционного китайского письма.</td>
</tr>
<tr class="odd">
<td align="left" style="font-family: Microsoft YaHei UI;">Microsoft YaHei UI</td>
<td align="left">Обычный, полужирный, светлый</td>
<td align="left">Шрифт пользовательского интерфейса для упрощенного китайского письма.</td>
</tr>
<tr class="odd">
<td align="left" style="font-family: Myanmar Text;">Myanmar Text</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для бирманского письма.</td>
</tr>
<tr class="even">
<td align="left" style="font-family: Nirmala UI;">Nirmala UI</td>
<td align="left">Обычный, полусветлый, полужирный</td>
<td align="left">Шрифт пользовательского интерфейса для письма народов Южной Азии (бенгальский, деванагари, гуджарати, гурмукхский, каннада, малаялам, ория, ол-чики, сингальский, сора-сонпенг, тамильский, телугу)</td>
</tr>
<tr class="odd">
<td align="left" style="font-family: SimSun;">SimSun</td>
<td align="left">Обычный</td>
<td align="left">Устаревший шрифт пользовательского интерфейса для китайского языка. </td>
</tr>
<tr class="even">
<td align="left" style="font-family: Yu Gothic UI;">Yu Gothic UI</td>
<td align="left">Светлый, полусветлый, обычный, плотный, полужирный</td>
<td align="left">Шрифт пользовательского интерфейса для японского языка.</td>
</tr>
</tbody>
</table>

## <a name="fonts"></a>Шрифты

### <a name="sans-serif-fonts"></a>Рубленые шрифты (без засечек)

Рубленые шрифты хорошо подходят для заголовков и элементов пользовательского интерфейса. 

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Семейство шрифтов</th>
<th align="left">Стили</th>
<th align="left">Примечания</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left" style="font-family: Arial;">Arial</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив, черный</td>
<td align="left">Поддерживаются наборы знаков для европейских и ближневосточных языков (латинский, греческий, кириллический, арабский, армянский и иврит); стиль «черный» поддерживает только наборы знаков для европейских языков.</td>
</tr>
<tr class="even">
<td align="left" style="font-family: Calibri;">Calibri</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив, светлый, светлый курсив</td>
<td align="left">Поддерживаются наборы знаков для европейских и ближневосточных языков (латинский, греческий, кириллический, арабский и иврит). Арабские символы доступны только в вертикальном написании.</td>
</tr>
<td style="font-family: Consolas;">Consolas</td>
<td>Обычный, курсив, полужирный, полужирный курсив</td>
<td>Шрифт фиксированной ширины, поддерживающий наборы знаков для европейских языков (латинский, греческий и кириллический).</td>
</tr>

<tr>
<td style="font-family: Segoe UI;">Segoe UI</td>
<td>Обычный, курсив, светлый курсив, черный курсив, полужирный, полужирный курсив, светлый, полусветлый, плотный, черный</td>
<td>Шрифт пользовательского интерфейса для набора знаков европейских и ближневосточных языков (арабский, армянский, кириллический, грузинский, греческий, иврит и латинский), а также набора знаков письма Лису.</td>
</tr>

<tr class="even">
<td style="font-family: Selawik;">Selawik</td>
<td align="left">Обычный, полусветлый, светлый, полужирный, плотный</td>
<td align="left">Шрифт с открытым кодом, метрически совместимый с Segoe UI; предназначен для приложений на других платформах, в состав которых не входит шрифт Segoe UI. <a href="https://github.com/Microsoft/Selawik">Шрифт Selawik можно получить на GitHub.</a></td>
</tr>

</tbody>
</table>

### <a name="serif-fonts"></a>Шрифты с засечками

Шрифты с засечками хорошо подходят для представления больших объемов текста. 

<table>
<thead>
<tr class="header">
<th align="left">Семейство шрифтов</th>
<th align="left">Стили</th>
<th align="left">Примечания</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="font-family: Cambria;">Cambria</td>
<td align="left">Обычный</td>
<td align="left">Шрифт с засечками, поддерживающий наборы знаков для европейских языков (латинский, греческий, кириллический).</td>
</tr>
<tr class="even">
<td style="font-family: Courier New;">Courier New</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left">Шрифт фиксированной ширины с засечками, поддерживающий наборы знаков для европейских и ближневосточных языков (латинский, греческий, кириллический, арабский, армянский и иврит).</td>
</tr>
<tr class="odd">
<td style="font-family: Georgia;">Georgia</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left">Поддерживаются наборы знаков европейских языков (латинский, греческий и кириллический).</td>
</tr>

<tr class="even">
<td style="font-family: Times New Roman;">Times New Roman</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left">Устаревший шрифт, поддерживающий наборы знаков для европейских языков (латинский, греческий, кириллический, арабский, армянский, иврит).</td>
</tr>

</tbody>
</table>

### <a name="symbols-and-icons"></a>Символы и значки

<table>
<thead>
<tr class="header">
<th align="left">Семейство шрифтов</th>
<th align="left">Стили</th>
<th align="left">Примечания</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Segoe MDL2 Assets</td>
<td align="left">Обычный</td>
<td align="left">Шрифт пользовательского интерфейса для значков приложений. Дополнительные сведения см. в <a href="segoe-ui-symbol-font.md">статье Segoe MDL2 Assets</a>.</td>
</tr>
<tr class="even">
<td align="left">Segoe UI Emoji</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Segoe UI Symbol</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для символов</td>
</tr>
</tbody>
</table>

## <a name="related-articles"></a>Статьи по теме

* [Элементы управления текстом](../controls-and-patterns/text-controls.md)
* [Ресурсы темы XAML](../controls-and-patterns/xaml-theme-resources.md#the-xaml-type-ramp)
* [Стили XAML](../controls-and-patterns/xaml-styles.md)
* [Шрифтовое оформление Майкрософт](https://docs.microsoft.com/typography/)
