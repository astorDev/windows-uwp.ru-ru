---
author: DelfCo
Description: "Чтобы приложение соответствовало международному стандарту, выбирайте при разработке правильные форматы дат, времени, чисел, номеров телефона и валют."
title: "Использование форматов международного стандарта"
ms.assetid: 6ECE8BA4-9A7D-49A6-81EE-AB2BE7F0254F
label: Use global-ready formats
template: detail.hbs
ms.author: bobdel
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: a733f3d87f2e6598e49d8926a7b10797e335c79c
ms.lasthandoff: 02/07/2017

---

# <a name="use-global-ready-formats"></a>Использование форматов международного стандарта

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Чтобы приложение соответствовало международному стандарту, выбирайте при разработке правильные форматы дат, времени, чисел, номеров телефона и валют. Это позволит позже адаптировать приложение для дополнительных языков и региональных параметров на международном рынке.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Windows.Globalization.Calendar**](https://msdn.microsoft.com/library/windows/apps/br206724)</li>
<li>[**Windows.Globalization.DateTimeFormatting**](https://msdn.microsoft.com/library/windows/apps/br206859)</li>
<li>[**Windows.Globalization.NumberFormatting**](https://msdn.microsoft.com/library/windows/apps/br226136)</li>
<li>[**Windows.Globalization.PhoneNumberFormatting**](https://msdn.microsoft.com/library/windows/apps/Windows.Globalization.PhoneNumberFormatting)</li>
</ul>
</div>


## <a name="introduction"></a>Введение

Разумеется, многие разработчики создают свои приложения с учетом только собственного языка и культуры. Но когда приложение начинает приобретать известность на рынках других стран, могут возникнуть неожиданные сложности в связи с его адаптацией для других языков и регионов. Например, в разных странах или на разных языках формат отображения даты, времени, чисел, календаря, валюты, телефонных номеров, единиц измерения и размеров бумаги может отличаться.

Процесс адаптации к новым рынкам можно упростить, если учесть несколько моментов при разработке приложения.

## <a name="format-dates-and-times-appropriately"></a>Задавайте соответствующий формат дат и времени.

Существует множество вариантов представления даты и времени. В разных странах действуют различные правила в отношении порядка записи дня и месяца, часов и минут, а также разделителей. Также различаются способы отображения полных ("среда, 28 марта 2012 г.") и кратких ("28/03/12") форматов дат. И, разумеется, названия и сокращения дней недели и месяцев тоже зависят от языка.

Если вы хотите предоставить пользователям возможность выбора даты или времени, используйте стандартные элементы управления для [выбора даты и времени](https://msdn.microsoft.com/library/windows/apps/hh465466). При этом будут автоматически использоваться форматы даты и времени, принятые для выбранного пользователем языка и страны.

Если вы сами хотите отображать дату и время, используйте средства форматирования [**Date/Time**](https://msdn.microsoft.com/library/windows/apps/br206859) и [**Number**](https://msdn.microsoft.com/library/windows/apps/br226136), чтобы автоматически отображать выбранный пользователем формат даты, времени и чисел. Приведенный ниже код преобразует заданный объект DateTime в формат предпочитаемого языка и региона. Например, текущая дата 3 июня 2012 года будет выглядеть как "6/3/2012", если пользователь использует язык "английский (США)", но примет вид "03.06.2012", если избранный язык пользователя — "немецкий (Германия)".

```CSharp
    // Use the Windows.Globalization.DateTimeFormatting.DateTimeFormatter class
    // to display dates and times using basic formatters.

    // Formatters for dates and times, using shortdate format.
    var sdatefmt = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("shortdate");
    var stimefmt = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("shorttime");

    // Obtain the date that will be formatted.
    var dateToFormat = DateTime.Now;

    // Perform the actual formatting.
    var sdate = sdatefmt.Format(dateToFormat);
    var stime = stimefmt.Format(dateToFormat);

    // Results for display.
    var results = "Short Date: " + sdate + "\n" +
                  "Short Time: " + stime;
```

## <a name="format-numbers-and-currencies-appropriately"></a>Задавайте соответствующий формат чисел и валют.

В различных языках и регионах используются разные форматы чисел. Отличия в форматах чисел могут касаться правил записи десятичных долей, разделителей целой и дробной частей и обозначений валют. Используйте [**NumberFormatting**](https://msdn.microsoft.com/library/windows/apps/br226136) для отображения десятичных чисел, процентов, многоразрядных чисел и валют. В большинстве случаев достаточно показывать числа или валюты в соответствии с текущими настройками пользователя. Но можно также применять средства форматирования, чтобы показывать валюту с учетом региональных стандартов.

Приведенный ниже код иллюстрирует отображение валют в соответствии с языком и регионом в настройках пользователя, а также с учетом определенной валютной системы:

```CSharp
    // This scenario uses the Windows.Globalization.NumberFormatting.CurrencyFormatter class
    // to format a number as a currency.

    // Determine the current user's default currency.
    var userCurrency = Windows.System.UserProfile.GlobalizationPreferences.Currencies[0];

    // Number to be formatted.
    var fractionalNumber = 12345.67;

    // Currency formatter using the current user's preference settings for number formatting.
    var userCurrencyFormat = new Windows.Globalization.NumberFormatting.CurrencyFormatter(userCurrency);
    var currencyDefault = userCurrencyFormat.Format(fractionalNumber);

    // Create a formatter initialized to a specific currency,
    // in this case US Dollar (specified as an ISO 4217 code) 
    // but with the default number formatting for the current user.
    var currencyFormatUSD = new Windows.Globalization.NumberFormatting.CurrencyFormatter("USD"); 
    var currencyUSD = currencyFormatUSD.Format(fractionalNumber);

    // Create a formatter initialized to a specific currency.
    // In this case it's the Euro with the default number formatting for France.
    var currencyFormatEuroFR = new Windows.Globalization.NumberFormatting.CurrencyFormatter("EUR", new[] { "fr-FR" }, "FR");
    var currencyEuroFR = currencyFormatEuroFR.Format(fractionalNumber);

    // Results for display.
    var results = "Fixed number (" + fractionalNumber + ")\n" +
                  "With user's default currency: " + currencyDefault + "\n" +
                  "Formatted US Dollar: " + currencyUSD + "\n" +
                  "Formatted Euro (fr-FR defaults): " + currencyEuroFR;
```

## <a name="use-a-culturally-appropriate-calendar"></a>Используйте календарь, соответствующий заданным региональным параметрам.

Для разных стран и языков применяются разные календари. Григорианский календарь по умолчанию применяется не во всех регионах. В некоторых странах пользователи могут выбирать альтернативные календари, например японский или арабский лунный календарь. Даты и время в календаре зависят также от часовых поясов и перехода на летнее время.

Используйте стандартные элементы управления для [выбора даты и времени](https://msdn.microsoft.com/library/windows/apps/hh465466), чтобы пользователи могли выбрать дату в удобном для них календарном формате. Для более сложных сценариев, в которых требуется напрямую работать с календарем, библиотека Windows.Globalization предлагает класс [**Calendar**](https://msdn.microsoft.com/library/windows/apps/br206724), который позволяет отобразить календарь для данного языка, региона и типа календаря.

## <a name="format-phone-numbers-appropriately"></a>Задавайте соответствующий формат номера телефона.
Номера телефона форматируются по-разному в различных регионах. Количество цифр, их группировка и значимость определенных частей номера телефона могут различаться в разных странах. Начиная c Windows 10 версии 1607, используйте [**PhoneNumberFormatting**](https://msdn.microsoft.com/library/windows/apps/Windows.Globalization.PhoneNumberFormatting) для форматирования номера телефона в соответствии с текущим регионом.

[
              Класс **PhoneNumberInfo**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.globalization.phonenumberformatting.phonenumberinfo.aspx) анализирует строку цифр и позволяет определить, являются ли цифры действительным номером телефона в текущем региона, проверить равенство двух номеров, а также извлечь различные функциональные части телефонного номера, такие как код страны или географическая область.

[
              Класс **PhoneNumberFormatter**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.globalization.phonenumberformatting.phonenumberformatter.aspx) форматирует строку цифр или PhoneNumberInfo для отображения, даже если строка цифр представляет собой телефонный номер, указанный не полностью. (Форматирование номера, указанного не полностью можно использовать по мере ввода номера пользователем.) 

В следующем коде показано, как использовать PhoneNumberFormatter для форматирования номера телефона во время его ввода. Каждый раз, когда в элементе TextBox с названием gradualInput меняется текст, содержимое текстового поля форматируется с учетом текущего региона по умолчанию и отображается в элементе TextBlock с названием outBox. В целях демонстрации строка имеет региональный формат Новой Зеландии и отображается в элементе TextBlock с именем NZOutBox.
    
```csharp
    using Windows.Globalization;

    PhoneNumberFormatter currentFormatter, NZFormatter;

    public MainPage()
    {
        this.InitializeComponent();

        // Use the default formatter for the current region
        currentFormatter = new PhoneNumberFormatter();

        // Create an explicit formatter for New Zealand. 
        // Note that you must check the results of TryCreate before you use the formatter.
        PhoneNumberFormatter.TryCreate("NZ", out NZFormatter);

    }

    private void gradualInput_TextChanged(object sender, TextChangedEventArgs e)
    {
        // Format for the default region into outBox.
        outBox.Text = currentFormatter.FormatPartialString(gradualInput.Text);

        // If the NZFormatter was created successfully, format the partial string for the NZOutBox.
        if(NZFormatter != null)
        {
            NZOutBox.Text = NZFormatter.FormatPartialString(gradualInput.Text);
        }
    }
```    

## <a name="respect-the-users-language-and-cultural-preferences"></a>Учитывайте языковые и региональные параметры, заданные пользователем.

Если от языка или региональных параметров зависит функциональность вашего приложения, Windows дает возможность обращаться к этим настройкам с помощью [**Windows.System.UserProfile.GlobalizationPreferences**](https://msdn.microsoft.com/library/windows/apps/br241825). При необходимости используйте класс **GlobalizationPreferences**, чтобы получить данные текущего географического региона, предпочитаемых языков, валют и других настроек пользователя.

## <a name="related-topics"></a>Ссылки по теме

* [Планирование для мирового рынка](https://msdn.microsoft.com/library/windows/apps/hh465405)
* [Рекомендации по элементам управления датой и временем](https://msdn.microsoft.com/library/windows/apps/hh465466)

**Ссылки**
* [**Windows.Globalization.Calendar**](https://msdn.microsoft.com/library/windows/apps/br206724)
* [**Windows.Globalization.DateTimeFormatting**](https://msdn.microsoft.com/library/windows/apps/br206859)
* [**Windows.Globalization.NumberFormatting**](https://msdn.microsoft.com/library/windows/apps/br226136)
* [**Windows.System.UserProfile.GlobalizationPreferences**](https://msdn.microsoft.com/library/windows/apps/br241825)

**Примеры**
* [Подробности о календаре и пример расчетов](http://go.microsoft.com/fwlink/p/?linkid=231636)
* [Пример форматирования даты и времени](http://go.microsoft.com/fwlink/p/?linkid=231618)
* [Пример настроек глобализации](http://go.microsoft.com/fwlink/p/?linkid=231608)
* [Пример форматирования и разбора чисел](http://go.microsoft.com/fwlink/p/?linkid=231620)

