---
author: Jwmsft
Description: "Следуйте данному руководству при выборе шрифтов и указании их размеров и цветов для приложений UWP."
title: "Шрифты для приложений UWP"
ms.assetid: 1B8B90AD-CDC4-4997-ACDE-871C1E94A929
label: Fonts
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: a3924fef520d7ba70873d6838f8e194e5fc96c62
ms.openlocfilehash: 0b25dc91a5ec82a83ae24a41854e9eeab8990128

---


# <a name="fonts-for-uwp-apps"></a>Шрифты для приложений UWP

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

В этой статье перечисляются шрифты, рекомендованные для приложений UWP. Эти шрифты будут гарантировано доступны в выпусках Windows 10, поддерживающих приложения UWP.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Свойство FontFamily**](https://msdn.microsoft.com/library/windows/apps/br209655)</li>
</ul>
</div>

В [руководстве по шрифтовому оформлению UWP](typography.md) рекомендуется использовать в приложениях шрифт Segoe UI; но хотя шрифт Segoe UI хорошо подходит для большинства приложений, не обязательно ограничиваться только им. Можно использовать другие шрифты в таких сценариях, как чтение или отображение текста на некоторых языках, отличных от английского. 
 
## <a name="sans-serif-fonts"></a>Рубленые шрифты (без засечек)

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

<tr class="odd">
<td>Segoe UI Historic</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для исторического письма</td>
</tr>

<tr class="even">
<td style="font-family: Selawik;">Selawik</td>
<td align="left">Обычный, полусветлый, светлый, полужирный, плотный</td>
<td align="left">Шрифт с открытым кодом, метрически совместимый с Segoe UI; предназначен для приложений на других платформах, в состав которых не входит шрифт Segoe UI. [Шрифт Selawik можно получить на GitHub.](https://github.com/Microsoft/Selawik)</td>
</tr>

<tr class="even">
<td style="font-family: Verdana;">Verdana</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left">Поддерживаются наборы знаков европейских языков (латинский, греческий, кириллический и армянский).</td>
</tr>

</tbody>
</table>


## <a name="serif-fonts"></a>Шрифты с засечками

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

## <a name="symbols-and-icons"></a>Символы и значки


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
<td align="left">Шрифт пользовательского интерфейса для значков приложений. Дополнительные сведения см. в [статье Segoe MDL2 Assets](segoe-ui-symbol-font.md).</td>
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



## <a name="fonts-for-non-latin-languages"></a>Шрифты для языков, не использующих латинский алфавит

Однако многие из этих шрифтов содержат символы латиницы.

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
<tr class="even">
<td align="left">Javanese Text Regular Fallback font for Javanese script</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для яванского письма</td>
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
<td align="left" style="font-family: Microsoft Himalaya;">Microsoft Himalaya</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для тибетского письма.</td>
</tr>
<!--
<tr class="odd">
<td align="left" style="font-family: Microsoft JhengHei;">Microsoft JhengHei</td>
<td align="left">Regular</td>
<td align="left"></td>
</tr>
-->
<tr class="even">
<td align="left" style="font-family: Microsoft JhengHei UI;">Microsoft JhengHei UI</td>
<td align="left">Обычный, полужирный, светлый</td>
<td align="left">Шрифт пользовательского интерфейса для традиционного китайского письма.</td>
</tr>
<tr class="odd">
<td align="left" style="font-family: Microsoft New Tai Lue;">Microsoft New Tai Lue</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для нового письма Тай-лю.</td>
</tr>
<tr class="even">
<td align="left" style="font-family: Microsoft PhagsPa;">Microsoft PhagsPa</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для монгольского квадратного письма.</td>
</tr>
<tr class="odd">
<td align="left" style="font-family: Microsoft Tai Le;">Microsoft Tai Le</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для письма Тай-лы.</td>
</tr>
<!--
<tr class="even">
<td align="left" style="font-family: Microsoft YaHei;">Microsoft YaHei</td>
<td align="left">Regular</td>
<td align="left"></td>
</tr>
-->
<tr class="odd">
<td align="left" style="font-family: Microsoft YaHei UI;">Microsoft YaHei UI</td>
<td align="left">Обычный, полужирный, светлый</td>
<td align="left">Шрифт пользовательского интерфейса для упрощенного китайского письма.</td>
</tr>
<tr class="even">
<td align="left" style="font-family: Microsoft Yi Baiti;">Microsoft Yi Baiti</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для письма «И».</td>
</tr>
<tr class="odd">
<td align="left" style="font-family: Mongolian Baiti;">Mongolian Baiti</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для монгольского письма.</td>
</tr>
<tr class="even">
<td align="left" style="font-family: MV Boli;">MV Boli</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для письма Тана.</td>
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
<tr class="odd">
<td align="left" style="font-family: Yu Gothic;">Yu Gothic</td>
<td align="left">Средний</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left" style="font-family: Yu Gothic UI;">Yu Gothic UI</td>
<td align="left">Обычный</td>
<td align="left">Шрифт пользовательского интерфейса для японского языка.</td>
</tr>
</tbody>
</table>


## <a name="globalizinglocalizing-fonts"></a>Шрифты для глобализации и локализации
Используйте [API сопоставления шрифтов LanguageFont](https://msdn.microsoft.com/library/windows/apps/br206864) для программного доступа к рекомендуемому семейству, размеру, насыщенности и стилю шрифта определенного языка. Объект LanguageFont предоставляет доступ к нужной информации о шрифте для различных категорий содержимого, включая заголовки, уведомления, текст и шрифты доступных для редактирования текстов пользовательского интерфейса. Дополнительные сведения см. в разделе [Настройка макета и шрифтов для поддержки глобализации](https://msdn.microsoft.com/windows/uwp/globalizing/adjust-layout-and-fonts--and-support-rtl).


## <a name="get-the-samples"></a>Получение примеров

* [Пример шрифтов, доступных для скачивания](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/XamlCloudFontIntegration)
* [Пример базовых характеристик пользовательского интерфейса](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/XamlUIBasics)
* [Пример междустрочного интервала с DirectWrite](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/DWriteLineSpacingModes) 

## <a name="related-articles"></a>Связанные разделы

* [Настройка макета и шрифтов для поддержки глобализации](https://msdn.microsoft.com/windows/uwp/globalizing/adjust-layout-and-fonts--and-support-rtl)
* [Segoe MDL2](segoe-ui-symbol-font.md)
* [Текстовые элементы управления)](../controls-and-patterns/text-controls.md)
* [Ресурсы темы XAML](https://msdn.microsoft.com/library/windows/apps/mt187274)

 

 







<!--HONumber=Dec16_HO2-->


