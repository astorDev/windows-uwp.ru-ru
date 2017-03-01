---
author: DelfCo
Description: "Для отображения даты и времени в желаемом формате можно использовать API Windows.Globalization.DateTimeFormatting с пользовательскими шаблонами."
title: "Использование шаблонов для форматирования даты и времени"
ms.assetid: 012028B3-9DA2-4E72-8C0E-3E06BEC3B3FE
label: Use patterns to format dates and times
template: detail.hbs
ms.author: bobdel
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: c28210a6fca27976a2a4428d8001da87951f1e87
ms.lasthandoff: 02/07/2017

---

# <a name="use-patterns-to-format-dates-and-times"></a>Использование шаблонов для форматирования даты и времени

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Для отображения даты и времени в желаемом формате можно использовать API [**Windows.Globalization.DateTimeFormatting**](https://msdn.microsoft.com/library/windows/apps/br206859) с пользовательскими шаблонами.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Windows.Globalization.DateTimeFormatting**](https://msdn.microsoft.com/library/windows/apps/br206859)</li>
<li>[**DateTimeFormatter**](https://msdn.microsoft.com/library/windows/apps/br206828)</li>
<li>[**DateTime**](https://msdn.microsoft.com/library/windows/apps/br206576)</li>
</ul>
</div>


## <a name="introduction"></a>Введение


[**Windows.Globalization.DateTimeFormatting**](https://msdn.microsoft.com/library/windows/apps/br206859) предоставляет различные способы форматирования даты и времени в соответствии с языковыми и региональными стандартами, применяемыми во всем мире. Вы можете использовать стандартные форматы для года, месяца, дня и т. д. или использовать стандартные шаблоны строки, такие как "longdate" или "month day".

Если же вам требуется больше контроля над порядком и форматом составляющих строки [**DateTime**](https://msdn.microsoft.com/library/windows/apps/br206576), которую вы хотите отобразить, вы можете использовать особенный синтаксис для параметра шаблона строки, называемый "шаблоном". Синтаксис шаблонов позволяет получить отдельные составляющие объекта **DateTime**, например только название месяца или только значение года, чтобы отобразить их в выбранном пользовательском формате. Кроме того, шаблон можно локализовать, чтобы адаптировать его к другим языкам и регионам.

**Примечание.** Это обзор шаблонов формата. Более подробные сведения о стандартных и нестандартных шаблонах формата см. в разделе "Замечания" класса [**DateTimeFormatter**](https://msdn.microsoft.com/library/windows/apps/br206828).

 

## <a name="what-you-need-to-know"></a>Что необходимо знать


Важно отметить, что при использовании шаблонов вы создаете пользовательский формат, который может подходить не для всех культур. Например, рассмотрим шаблон "month day" ("месяц день"):

**C#**
```CSharp
var datefmt = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("month day");
```
**JavaScript**
```JavaScript
var datefmt = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("month day");
```

В результате создается модуль форматирования на основе языка и региона в текущем контексте. Следовательно, месяц и день будут всегда отображаться вместе в подходящем глобальном формате. Например, для английского языка (США) будет показываться "January 1", для французского языка (Франция) — "1 janvier", а для японского — "1月1日". Это достигается благодаря тому, что шаблон строится на основе строки, учитывающей особенности культуры, причем доступ к ней можно получить с помощью свойства шаблона:

**C#**
```CSharp
var monthdaypattern = datefmt.Patterns;
```
**JavaScript**
```JavaScript
var monthdaypattern = datefmt.patterns;
```

В итоге, в зависимости от языка и региона модуля форматирования, будут получены разные результаты. Обратите внимание, что в разных регионах могут использоваться различные составляющие, в разном порядке, с добавлением дополнительных символов и пробелов или без них:

``` syntax
En-US: "{month.full} {day.integer}"
Fr-FR: "{day.integer} {month.full}"
Ja-JP: "{month.integer}月{day.integer}日"
```

Вы можете использовать шаблоны для создания пользовательских [**DateTimeFormatter**](https://msdn.microsoft.com/library/windows/apps/br206828), например такой, созданный на основе шаблона английского языка (США):

**C#**
```CSharp
var datefmt = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("{month.full} {day.integer}");
```
**JavaScript**
```JavaScript
var datefmt = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("{month.full} {day.integer}");
```

Windows возвращает значения, зависящие от языка и региональных параметров, для отдельных составляющих, заключенных в скобки {}. Но при использовании синтаксиса шаблонов порядок составляющих остается неизменным. Вы получаете именно то, что хотели, но соответствие культуре не гарантируется:

``` syntax
En-US: January 1
Fr-FR: janvier 1 (inappropriate for France; non-standard order)
Ja-JP: 1月1 (inappropriate for Japan; the day symbol is missing)
```

Более того, нет гарантии, что шаблоны будут оставаться непротиворечивыми с течением времени. Страны или регионы могут менять свои календарные системы, в результате чего меняется и шаблон формата. Windows обновляет выходные данные модулей форматирования в соответствии с этими изменениями. Поэтому использовать синтаксис шаблонов для форматирования [**DateTime**](https://msdn.microsoft.com/library/windows/apps/br206576) можно только в следующих случаях.

-   Если для вас не важен конкретный результат форматирования.
-   Если вам не нужно, чтобы формат соответствовал какому-либо зависящему от языка и региональных параметров стандарту.
-   Если вам нужно, чтобы шаблон был независимым от языка и региональных параметров.
-   Если вы планируете локализовать шаблон.

Различия между стандартными шаблонами строки и нестандартными шаблонами заключаются в следующем.

**Шаблоны строки, такие как "месяц день":**

-   Абстрактные представления в формате [**DateTime**](https://msdn.microsoft.com/library/windows/apps/br206576), включающие значения месяца и дня, следующие в некотором порядке.
-   Гарантируется получение действительного стандартного формата для всех вариантов языков и регионов, поддерживаемых Windows.
-   Гарантируется получение строки, отформатированной в соответствии с культурой, для заданного языка и региона.
-   Не все комбинации составляющих являются допустимыми. Например, не существует шаблона строки для "dayofweek day" ("день_недели день").

**Шаблоны строки, такие как "{month.full} {day.integer}":**

-   Явно заданная строка, определяющая полное название месяца с последующим пробелом, затем — день целым числом в этом порядке.
-   Может не соответствовать допустимому стандартному формату для некоторых пар "язык-регион".
-   Не гарантируется соответствие культуре.
-   Может быть задана любая комбинация составляющих в любом порядке.

## <a name="tasks"></a>Задачи


Предположим, что требуется отобразить текущие месяц и день вместе с текущим временем в определенном формате. Например, вы хотите, чтобы пользователи с английским языком (США) увидели данные в следующем формате:

``` syntax
June 25 | 1:38 PM
```

Компонент даты соответствует шаблону "month day" ("месяц день"), а компонент времени — шаблону "hour minute" ("час минута"). Поэтому вы можете создать пользовательский формат, объединяющий эти шаблоны.

Сначала получите модули форматирования для нужных шаблонов даты и времени, а затем получите образцы этих шаблонов:

**C#**
```CSharp
// Get formatters for the date part and the time part.
var mydate = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("month day");
var mytime = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("hour minute");

// Get the patterns from these formatters.
var mydatepattern = mydate.Patterns[0];
var mytimepattern = mytime.Patterns[0];
```
**JavaScript**
```JavaScript
// Get formatters for the date part and the time part.
var dtf = Windows.Globalization.DateTimeFormatting;
var mydate = dtf.DateTimeFormatter("month day");
var mytime = dtf.DateTimeFormatter("hour minute");

// Get the patterns from these formatters.
var mydatepattern = mydate.patterns[0];
var mytimepattern = mytime.patterns[0];
```

Пользовательский формат следует сохранить как локализуемую строку ресурса. Например, строка для английского языка (США) будет иметь вид "{date} | {time}". Локализаторы могут настроить эту строку нужным образом. Например, они могут изменить порядок составляющих, если для того или иного языка или региона естественнее указывать время перед датой. Они также могут заменить "|" другим знаком разделения. Во время выполнения части строки {date} и {time} будут заменены соответствующими шаблонами:

**C#**
```CSharp
// Assemble the custom pattern. This string comes from a resource, and should be localizable. 
var resourceLoader = new Windows.ApplicationModel.Resources.ResourceLoader();
var mydateplustime = resourceLoader.GetString("date_plus_time");
mydateplustime = mydateplustime.replace("{date}", mydatepattern);
mydateplustime = mydateplustime.replace("{time}", mytimepattern);
```
**JavaScript**
```JavaScript
// Assemble the custom pattern. This string comes from a resource, and should be localizable. 
var mydateplustime = WinJS.Resources.getString("date_plus_time");
mydateplustime = mydateplustime.replace("{date}", mydatepattern);
mydateplustime = mydateplustime.replace("{time}", mytimepattern);
```

Затем вы можете создать новый модуль форматирования на основе пользовательского шаблона:

**C#**
```CSharp
// Get the custom formatter.
var mydateplustimefmt = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter(mydateplustime);
```
**JavaScript**
```JavaScript
// Get the custom formatter.
var mydateplustimefmt = new dtf.DateTimeFormatter(mydateplustime);
```

## <a name="related-topics"></a>Ссылки по теме


* [Пример форматирования даты и времени](http://go.microsoft.com/fwlink/p/?LinkId=231618)
* [**Windows.Globalization.DateTimeFormatting**](https://msdn.microsoft.com/library/windows/apps/br206859)
* [**Windows.Foundation.DateTime**](https://msdn.microsoft.com/library/windows/apps/br206576)
 

 




