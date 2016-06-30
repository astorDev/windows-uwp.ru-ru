---
author: Jwmsft
Description: "Следуйте данному руководству при выборе шрифтов и указании их размеров и цветов."
title: "Шрифты"
ms.assetid: 1B8B90AD-CDC4-4997-ACDE-871C1E94A929
label: Fonts
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: a4e9a90edd2aae9d2fd5d7bead948422d43dad59
ms.openlocfilehash: 268884c011caa18f3afb6d8b0d9cfda1ec27f51e

---

# Рекомендации по шрифтам

**Важные API**

-   [**Свойство FontFamily**](https://msdn.microsoft.com/library/windows/apps/br209655)

Надлежащее применение размеров, насыщенности, цветов, уплотнения и разрядки шрифтов поможет вам придать приложению универсальной платформы Windows (UWP) чистый и лаконичный вид, что сделает его более удобным для использования. Следуйте данному руководству при выборе шрифтов и указании их размеров и цветов.

Список значков Segoe UI Symbol можно найти в [**Рекомендациях по значкам Segoe UI Symbol**](segoe-ui-symbol-font.md).

## <span id="The_Windows_10_type_ramp"></span><span id="the_windows_10_type_ramp"></span><span id="THE_WINDOWS_10_TYPE_RAMP"></span>Набор шрифтов Windows 10


С помощью набора шрифтов устанавливается важное с точки зрения дизайна соотношение между заголовками и текстом, а также четкая и понятная иерархия между разными уровнями. Пользователи сразу понимают, где искать информацию и в каком порядке читать страницу.

Ниже приведен набор шрифтов, который мы рекомендуем для приложений UWP:

| Стиль текста | Шрифт | Насыщенность    | Размер (эфф. пиксели) | Междустрочный интервал (эфф. пиксели) | Интервал между словами | Межбуквенная разрядка (1/1000 em) | Ключ стилей XAML          |
|------------|----------|-----------|------------|--------------------|--------------|----------------------|-------------------------|
| Заголовок     | Segoe UI | Светлый     | 46         | 56                 | 100 %         | 0                    | HeaderTextBlockStyle    |
| Подзаголовок  | Segoe UI | Светлый     | 34         | 40                 | 100 %         | 0                    | SubheaderTextBlockStyle |
| Заголовок      | Segoe UI | Полусветлый | 24         | 28                 | 100 %         | 0                    | TitleTextBlockStyle     |
| Подзаголовок   | Segoe UI | Обычный   | 20         | 24                 | 100 %         | 0                    | SubtitleTextBlockStyle  |
| Base       | Segoe UI | Плотный  | 15         | 20                 | 100 %         | 0                    | BaseTextBlockStyle      |
| Тело       | Segoe UI | Обычный   | 15         | 20                 | 100 %         | 0                    | BodyTextBlockStyle      |
| Титр    | Segoe UI | Обычный   | 12         | 14                 | 100 %         | 0                    | CaptionTextBlockStyle   |

 

## <span id="Recommended_fonts"></span><span id="recommended_fonts"></span><span id="RECOMMENDED_FONTS"></span>Рекомендуемые шрифты


Нет необходимости использовать шрифт Segoe UI для всех элементов. Можно использовать другие шрифты в таких сценариях, как чтение или отображение текста на языках, отличных от английского.

Ниже приведен список шрифтов, которые точно будут доступны в выпусках Windows 10, поддерживающих приложения UWP.

**Примечание.** Если вы используете шрифт, отсутствующий в этом списке, ваше приложение может вызвать автоматическое скачивание данных шрифта из службы Майкрософт. Это может сказаться на производительности и других факторах, вызвав тем самым обеспокоенность, особенно у пользователей мобильных устройств. В частности, обратите внимание, что это может привести к расходу средств по тарифу мобильной связи пользователя. Приложения UWP, которые будут доступны на мобильных устройствах, не должны использовать для элементов пользовательского интерфейса какие-либо шрифты, не включенные в этот список.

 

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
<th align="left">Примечание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Arial</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив, черный</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Calibri</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив, светлый, светлый курсив</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Cambria</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Cambria Math</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Comic Sans MS</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Courier New</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Ebrima</td>
<td align="left">Обычный, полужирный</td>
<td align="left">Шрифт пользовательского интерфейса для африканского письма (эфиопский, нко, османья, тифинаг, ваи)</td>
</tr>
<tr class="even">
<td align="left">Gadugi</td>
<td align="left">Обычный</td>
<td align="left">Шрифт пользовательского интерфейса для североамериканского письма (канадский силлабический, чероки)</td>
</tr>
<tr class="odd">
<td align="left">Georgia</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Javanese Text Regular Fallback font for Javanese script</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для яванского письма</td>
</tr>
<tr class="odd">
<td align="left">Leelawadee UI</td>
<td align="left">Обычный, полусветлый, полужирный</td>
<td align="left">Шрифт пользовательского интерфейса для письма народов Юго-Восточной Азии (бугийский, лаосский, кхмерский, тайский)</td>
</tr>
<tr class="even">
<td align="left">Lucida Console</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Malgun Gothic</td>
<td align="left">Обычный</td>
<td align="left">Шрифт пользовательского интерфейса для корейского языка</td>
</tr>
<tr class="even">
<td align="left">Microsoft Himalaya</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для тибетского письма</td>
</tr>
<tr class="odd">
<td align="left">Microsoft JhengHei</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Microsoft JhengHei UI</td>
<td align="left">Обычный</td>
<td align="left">Шрифт пользовательского интерфейса для традиционного китайского письма</td>
</tr>
<tr class="odd">
<td align="left">Microsoft New Tai Lue</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для нового письма Тай-лю</td>
</tr>
<tr class="even">
<td align="left">Microsoft PhagsPa</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для монгольского квадратного письма</td>
</tr>
<tr class="odd">
<td align="left">Microsoft Tai Le</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для письма Тай-лы</td>
</tr>
<tr class="even">
<td align="left">Microsoft YaHei</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Microsoft YaHei UI</td>
<td align="left">Обычный</td>
<td align="left">Шрифт пользовательского интерфейса для упрощенного китайского письма</td>
</tr>
<tr class="even">
<td align="left">Microsoft Yi Baiti</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для письма Йи</td>
</tr>
<tr class="odd">
<td align="left">Mongolian Baiti</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для монгольского письма</td>
</tr>
<tr class="even">
<td align="left">MV Boli</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для письма Тана</td>
</tr>
<tr class="odd">
<td align="left">Myanmar Text</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для бирманского письма</td>
</tr>
<tr class="even">
<td align="left">Nirmala UI</td>
<td align="left">Обычный, полусветлый, полужирный</td>
<td align="left">Шрифт пользовательского интерфейса для письма народов Южной Азии (бенгальский, деванагари, гуджарати, гурмукхский, каннада, малаялам, ория, ол-чики, сингальский, сора-сонпенг, тамильский, телугу)</td>
</tr>
<tr class="odd">
<td align="left">Segoe MDL2 Assets</td>
<td align="left">Обычный</td>
<td align="left">Шрифт пользовательского интерфейса для значков приложений</td>
</tr>
<tr class="even">
<td align="left">Segoe Print</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Segoe UI</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив, светлый, полусветлый, плотный, черный</td>
<td align="left">Шрифт пользовательского интерфейса для письма европейских и ближневосточных народов (арабский, армянский, кириллический, грузинский, греческий, еврейский, латинский), а также письмо Лису</td>
</tr>
<tr class="even">
<td align="left">Segoe UI Emoji</td>
<td align="left">Обычный</td>
<td align="left">В версии, поставляемой в Windows Phone, вокруг каждого смайлика есть белый контур, благодаря которому смайлик хорошо виден на фоне любого цвета. Эта версия метрически совместима с версией, которая поставляется в Windows.</td>
</tr>
<tr class="odd">
<td align="left">Segoe UI Historic</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для исторического письма</td>
</tr>
<tr class="even">
<td align="left">Segoe UI Symbol</td>
<td align="left">Обычный</td>
<td align="left">Резервный шрифт для символов</td>
</tr>
<tr class="odd">
<td align="left">SimSun</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Times New Roman</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Trebuchet MS</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Verdana</td>
<td align="left">Обычный, курсив, полужирный, полужирный курсив</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Webdings</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Wingdings</td>
<td align="left">Обычный</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Yu Gothic</td>
<td align="left">Средний</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Yu Gothic UI</td>
<td align="left">Обычный</td>
<td align="left">Шрифт пользовательского интерфейса для японского языка</td>
</tr>
</tbody>
</table>

 

## <span id="related_topics"></span>Ссылки по теме

**Проектировщикам**
* [Метка (или блок текста)](labels.md)
* [Значки символов Segoe UI](segoe-ui-symbol-font.md)
            
          
            **Для разработчиков (XAML)**
* [Ресурсы темы XAML](https://msdn.microsoft.com/library/windows/apps/mt187274)
* [Создание макета приложения](https://msdn.microsoft.com/library/windows/apps/hh872191)
* [Руководство по значкам Segoe UI Symbol](segoe-ui-symbol-font.md)
* [**Свойство TextBlock.FontFamily**](https://msdn.microsoft.com/library/windows/apps/br209655)

**Примеры**
* [Пример отображения текста XAML](http://go.microsoft.com/fwlink/p/?linkid=238578)
* [Пример использования стилей CSS: создание фирменного стиля приложения](http://go.microsoft.com/fwlink/p/?linkid=231641)
* [Пример Language font mapping](http://go.microsoft.com/fwlink/p/?linkid=231603)
 

 







<!--HONumber=Jun16_HO4-->


