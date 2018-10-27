---
author: stevewhims
Description: Use the Windows.Globalization.DateTimeFormatting API with custom templates and patterns to display dates and times in exactly the format you wish.
title: Использование шаблонов для форматирования даты и времени
ms.assetid: 012028B3-9DA2-4E72-8C0E-3E06BEC3B3FE
label: Use patterns to format dates and times
template: detail.hbs
ms.author: stwhi
ms.date: 11/09/2017
ms.topic: article
keywords: windows 10, uwp, глобализация, локализуемость, локализация
ms.localizationpriority: medium
ms.openlocfilehash: 04a0288d0b28c12eb68cf56225747224e8df9777
ms.sourcegitcommit: 086001cffaf436e6e4324761d59bcc5e598c15ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2018
ms.locfileid: "5710383"
---
# <a name="use-templates-and-patterns-to-format-dates-and-times"></a>Использование шаблонов для форматирования даты и времени

Для отображения даты и времени в желаемом формате используйте классы в пространстве имен [**Windows.Globalization.DateTimeFormatting**](/uwp/api/windows.globalization.datetimeformatting?branch=live) с пользовательскими шаблонами.

## <a name="introduction"></a>Введение

Класс [**DateTimeFormatter**](/uwp/api/windows.globalization.datetimeformatting?branch=live) предоставляет различные способы форматирования даты и времени в соответствии с языковыми и региональными стандартами, применяемыми во всем мире. Вы можете использовать стандартные форматы для года, месяца, дня и т. п. Можно также передать шаблон формата аргументу *formatTemplate* конструктора **DateTimeFormatter**, например "longdate" ("длинная дата") или "month day" ("месяц день").

Однако, когда вам требуется еще больше возможностей управления порядком и форматом компонентов объекта [**DateTime**](/uwp/api/windows.foundation.datetime?branch=live), который необходимо отобразить, вы можете передать шаблон формата аргументу *formatTemplate* конструктора. Шаблон формата использует специальный синтаксис, который позволяет получить отдельные компоненты объекта **DateTime**, например только название месяца или только значение года, чтобы отобразить их в выбранном пользовательском формате. Кроме того, шаблон можно локализовать, чтобы адаптировать его к другим языкам и регионам.

**Примечание**это обзор только шаблонов формата. Более подробные сведения о стандартных и нестандартных шаблонах формата см. в разделе "Замечания" класса [**DateTimeFormatter**](/uwp/api/windows.globalization.datetimeformatting?branch=live).

## <a name="the-difference-between-format-templates-and-format-patterns"></a>Разница между стандартными и нестандартными шаблонами формата

Стандартный шаблон формата — это строка формата, не зависящая от языка и региональных параметров. Таким образом, при создании **DateTimeFormatter** с помощью стандартного шаблона формата модуль форматирования отображает компоненты формата в правильном порядке для текущего языка. И наоборот — нестандартный шаблон формата зависит от языка и региональных параметров. При построении **DateTimeFormatter** с помощью нестандартного шаблона формата модуль форматирования будет использовать шаблон в исходном виде. Следовательно, шаблон не обязательно будет допустимым для различных региональных параметров.

Давайте проиллюстрируем это отличие на примере. Мы передаем простой шаблон формата (а не нестандартный шаблон) конструктору **DateTimeFormatter**. Это стандартный шаблон формата "month day".

```csharp
var dateFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("month day");
```

В результате создается модуль форматирования на основе языка и региона в текущем контексте. Порядок компонентов в стандартном шаблоне формата не имеет значения; модуль форматирования отображает их в правильном порядке для текущего языка. Поэтому, для английского языка (США) будет отображаться "January1", для французского языка (Франция) — "1janvier", а для японского — "1月1日".

С другой стороны нестандартный шаблон формата зависит от языка и региональных параметров. Давайте осуществим доступ к нестандартному формату шаблона для стандартного шаблона формата.

```csharp
IReadOnlyList<string> monthDayPatterns = dateFormatter.Patterns;
```

В итоге, в зависимости от языка и региона среды выполнения, будут получены разные результаты. В разных регионах могут использоваться различные компоненты, в разном порядке, с добавлением дополнительных символов и пробелов или без них.

```syntax
En-US: "{month.full} {day.integer}"
Fr-FR: "{day.integer} {month.full}"
Ja-JP: "{month.integer}月{day.integer}日"
```

В приведенном выше примере мы вставили строку в формате, не зависящем от языка и региональных параметров, и получили строку в формате, зависящем от языка и региональных параметров (строку, которая была следствием языка и региона, использовавшимися при вызове `dateFormatter.Patterns`). Таким образом, при построении **DateTimeFormatter** из нестандартного шаблона формата, зависящего от языка и региональных параметров, он будет действителен только для определенных языков и регионов.

```csharp
var dateFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("{month.full} {day.integer}");
```

Модуль форматирования выше возвращает значения, зависящие от языка и региональных параметров для отдельных компонентов в квадратных скобках {}. Однако порядок компонентов в нестандартном шаблоне формата остается неизменным. Вы получаете именно то, что хотели, но соответствие культуре не гарантируется: Этот модуль форматирования действителен для английского языка (США), но не для французского (Франция) или японского языка.

``` syntax
En-US: January 1
Fr-FR: janvier 1 (inappropriate for France; non-standard order)
Ja-JP: 1月1 (inappropriate for Japan; the day symbol 日 is missing)
```

Кроме того, нестандартный шаблон, который в настоящее время является правильным, может не подойти в будущем. Страны или регионы могут менять свои календарные системы, в результате чего меняется и нестандартный шаблон формата. Windows обновляет выходные данные модулей форматирования на основе стандартных шаблонов формата в соответствии с этими изменениями. Таким образом, синтаксис нестандартного шаблона следует использовать только при одном или нескольких из этих условий.

-   Если для вас не важен конкретный результат форматирования.
-   Если вам не нужно, чтобы формат соответствовал какому-либо зависящему от языка и региональных параметров стандарту.
-   Если вам нужно, чтобы шаблон был независимым от языка и региональных параметров.
-   Если вы планируете локализовать саму строку нестандартного шаблона формата самостоятельно.

Ниже приведено краткое описание различий между стандартными и нестандартными шаблонами формата.

**Стандартные шаблоны формата, такие как "month day"**

-   Абстрактные представления в формате [DateTime](/uwp/api/windows.foundation.datetime?branch=live), включающие значения месяца, дня и т. д. и следующие в любом порядке.
-   Гарантируется получение действительного стандартного формата для всех вариантов языков и регионов, поддерживаемых Windows.
-   Гарантируется получение строки, отформатированной в соответствии с культурой, для заданного языка и региона.
-   Не все комбинации компонентов являются допустимыми. Например, шаблон формата "dayofweek day" ("день_недели день") не является допустимым.

**Нестандартные шаблоны формата, такие как "{month.full} {day.integer}".**

-   Явно заданная строка, определяющая полное название месяца с последующим пробелом, затем — день целым числом в этом порядке или в любом заданном вами нестандартном шаблоне формата.
-   Может не соответствовать допустимому стандартному формату для некоторых пар "язык-регион".
-   Не гарантируется соответствие культуре.
-   Может быть задана любая комбинация компонентов в любом порядке.

## <a name="examples"></a>Примеры.

Предположим, что требуется отобразить текущие месяц и день вместе с текущим временем в определенном формате. Например, вы хотите, чтобы пользователи с английским языком (США) увидели данные в следующем формате:

``` syntax
June 25 | 1:38 PM
```

Компонент даты соответствует стандартному шаблону формата "month day" ("месяц день"), а компонент времени— стандартному шаблону формата "hour minute" ("час минута"). Таким образом, можно создавать модули формирования для соответствующих стандартных шаблонов формата даты и времени, а затем объединять их выходные данные, используя строку формата, которую можно локализовать.

```csharp
var dateToFormat = System.DateTime.Now;
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView();

var dateFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("month day");
var timeFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("hour minute");

var date = dateFormatter.Format(dateToFormat);
var time = timeFormatter.Format(dateToFormat);

string output = string.Format(resourceLoader.GetString("CustomDateTimeFormatString"), date, time);
```

`CustomDateTimeFormatString` — идентификатор ресурса, который ссылается на локализуемый ресурс в файле ресурсов (.resw). Для языка по умолчанию — английский язык (США), этому идентификатору будет задано значение "{0} | {1}«вместе с комментарием, это означает, что»{0}» — это дата и "{1}«— это время. Таким образом, переводчики могут настраивать элементы формата нужным образом. Например, они могут изменить порядок элементов, если для того или иного языка или региона естественнее указывать время перед датой. Они также могут заменить "|" другим знаком разделения.

Другой способ реализовать этот пример — запросить у двух модулей форматирования их нестандартные шаблоны формата, объединить их вместе, а затем создать третий модуль форматирования из результирующего нестандартного шаблона формата.

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView();

var dateFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("month day");
var timeFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("hour minute");

string dateFormatterPattern = dateFormatter.Patterns[0];
string timeFormatterPattern = timeFormatter.Patterns[0];

string pattern = string.Format(resourceLoader.GetString("CustomDateTimeFormatString"), dateFormatterPattern, timeFormatterPattern);

var patternFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter(pattern);

string output = patternFormatter.Format(System.DateTime.Now);
```

## <a name="important-apis"></a>Важные API

* [Windows.Globalization.DateTimeFormatting](/uwp/api/windows.globalization.datetimeformatting?branch=live)
* [DateTimeFormatter](/uwp/api/windows.globalization.datetimeformatting?branch=live)
* [DateTime](/uwp/api/windows.foundation.datetime?branch=live)

## <a name="related-topics"></a>Смежные разделы

* [Пример форматирования даты и времени](http://go.microsoft.com/fwlink/p/?LinkId=231618)