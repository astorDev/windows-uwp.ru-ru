---
author: stevewhims
Description: Design your app to be global-ready by appropriately formatting dates, times, numbers, phone numbers, and currencies. You'll then be able later to adapt your app for additional cultures, regions, and languages in the global market.
title: Глобализация форматов даты/времени/чисел
ms.assetid: 6ECE8BA4-9A7D-49A6-81EE-AB2BE7F0254F
template: detail.hbs
ms.author: stwhi
ms.date: 11/07/2017
ms.topic: article
keywords: windows 10, uwp, глобализация, локализуемость, локализация
ms.localizationpriority: medium
ms.openlocfilehash: 173198c2c61530704dad02e2e92e6a7e47aae420
ms.sourcegitcommit: 144f5f127fc4fbd852f2f6780ef26054192d68fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5977531"
---
# <a name="globalize-your-datetimenumber-formats"></a>Глобализация форматов даты/времени/чисел

Проектируйте приложение так, чтобы оно соответствовало международному стандарту, выбирая при разработке правильные форматы дат, времени, чисел, номеров телефона и валют. Это позволит позже адаптировать приложение для дополнительных языков и региональных параметров на международном рынке.

## <a name="introduction"></a>Введение

Если при создании своего приложения вы добавите в него поддержку более одного языка и региона, у вас будет меньше (если вообще будут) непредвиденных проблем по мере распространения приложения на новых рынках. Например, в разных странах или на разных языках формат отображения даты, времени, чисел, календаря, валюты, телефонных номеров, единиц измерения и размеров бумаги может отличаться.

В разных регионах используются разные форматы даты и времени. К ним относятся различные правила в отношении порядка записи дня и месяца, часов и минут, а также разделителей. Также различаются способы отображения полных ("среда, 28 марта 2012 г.") и кратких ("28/03/12") форматов дат. И, разумеется, названия и сокращения дней недели и месяцев тоже зависят от языка.

Вы можете просмотреть форматы, используемые для различных языков. Перейдите в раздел **Параметры** > **Время и язык** > **Язык и региональные стандарты** и нажмите **Дополнительные параметры даты и времени, региональные параметры** > **Изменить формат даты, времени или чисел**. На вкладке **Форматы** выберите язык в раскрывающемся списке **Формат** и просмотрите форматы в разделе **Примеры**.

В этом разделе приводятся определения терминов "список языков профиля пользователя", "список языков манифеста приложения" и "список языков среды выполнения приложения". Дополнительные сведения о том, что означают эти термины и как получить доступ к их значениям, см. в разделе [Обзор языков профиля пользователя и языков манифеста приложения](manage-language-and-region.md).

## <a name="format-dates-and-times-for-the-app-runtime-language-list"></a>Задание формата даты и времени для списка языков среды выполнения приложения

Если вы хотите предоставить пользователям возможность выбора даты или времени, используйте стандартные [элементы управления для выбора календаря, даты и времени](../controls-and-patterns/date-and-time.md). Они автоматически используют наиболее подходящий формат даты и времени для списка языков среды выполнения приложения.

Если вы хотите отобразить дату и время самостоятельно, можно использовать класс [**DateTimeFormatter**](/uwp/api/windows.globalization.datetimeformatting?branch=live). По умолчанию класс **DateTimeFormatter** автоматически использует наиболее подходящий формат даты и времени для списка языков среды выполнения приложения. Поэтому код ниже задает формат **DateTime** наилучшим образом для этого списка. Например, предположим, что список языков манифеста приложения включает английский язык (США), который также является языком по умолчанию, и немецкий язык (Германия). Если текущей датой является 6 ноября 2017 года и в списке языков профиля пользователя немецкий язык (Германия) идет первым, модуль форматирования сформирует формат "06.11.2017". Если в списке языков профиля пользователя английский язык (США) идет первым (или если в нем нет ни английского ни немецкого языка), модуль форматирования сформирует формат "6/11/2017" (если языковой код "en-US" подходит или используется по умолчанию).

```csharp
    // Use the DateTimeFormatter class to display dates and times using basic formatters.

    var shortDateFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("shortdate");
    var shortTimeFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("shorttime");

    var dateTimeToFormat = DateTime.Now;

    var shortDate = shortDateFormatter.Format(dateTimeToFormat);
    var shortTime = shortTimeFormatter.Format(dateTimeToFormat);

    var results = "Short Date: " + shortDate + "\n" +
                  "Short Time: " + shortTime;
```

Вы можете проверить код выше на компьютере следующим образом.

- Убедитесь, что в вашем проекте есть файлы ресурсов с квалификаторами "en-US" и "de-DE" (см. раздел [Адаптация ресурсов с учетом языка, масштаба, высокой контрастности и других квалификаторов](../../app-resources/tailor-resources-lang-scale-contrast.md)).
- Измените список языков профиля пользователя в разделе **Параметры** > **Время и язык** > **Язык и региональные стандарты** > **Язык**. Добавьте немецкий язык (Германия), сделайте его языком по умолчанию и запустите код снова.

## <a name="format-dates-and-times-for-the-user-profile-language-list"></a>Задание формата даты и времени для списка языков профиля пользователя

Помните, что по умолчанию **DateTimeFormatter** соответствует списку языков среды выполнения приложения. Таким образом, если при отображении таких строк, как "The date is &lt;date&gt;" (Датой является &lt;дата&gt;), язык будет соответствовать формату даты.

Если по какой-либо причине вам потребуется форматировать дату и/или время только в соответствии со списком языков профиля пользователя, это можно сделать с помощью кода, как в примере ниже. Однако, если вы решите это сделать, необходимо понимать, что пользователь может выбрать язык, для которого в вашем приложении нет переведенных строк. Например, если ваше приложение не локализовано на немецкий язык (Германия), но пользователь выбирает его в качестве предпочитаемого языка, это может привести к отображению странно выглядящих строк, таких как "The date is 06.11.2017" ("Датой является 06.11.2017").

```csharp
    // Use the DateTimeFormatter class to display dates and times using basic formatters.

    var userLanguages = Windows.System.UserProfile.GlobalizationPreferences.Languages;

    var shortDateFormatter = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter("shortdate", userLanguages);

    var results = "Short Date: " + shortDateFormatter.Format(DateTime.Now);
```

## <a name="format-numbers-and-currencies-appropriately"></a>Задавайте соответствующий формат чисел и валют

В различных языках и регионах используются разные форматы чисел. Отличия в форматах чисел могут касаться правил записи десятичных долей, разделителей целой и дробной частей и обозначений валют. Используйте классы в пространстве имен [**NumberFormatting**](/uwp/api/windows.globalization.numberformatting?branch=live) для отображения десятичных чисел, процентов, многоразрядных чисел и валют. В большинстве случаев вам потребуется, чтобы эти классы модуля форматирования использовали наиболее подходящий формат для профиля пользователя. Но можно также применять модули форматирования, чтобы показывать валюту с учетом региональных стандартов.

Приведенный ниже пример иллюстрирует отображение валют в соответствии с профилем пользователя, а также с учетом определенной валютной системы.

```csharp
    // This scenario uses the CurrencyFormatter class to format a number as a currency.

    var userCurrency = Windows.System.UserProfile.GlobalizationPreferences.Currencies[0];

    var valueToBeFormatted = 12345.67;

    var userCurrencyFormatter = new Windows.Globalization.NumberFormatting.CurrencyFormatter(userCurrency);
    var userCurrencyValue = userCurrencyFormatter.Format(valueToBeFormatted);

    // Create a formatter initialized to a specific currency,
    // in this case US Dollar (specified as an ISO 4217 code) 
    // but with the default number formatting for the current user.
    var currencyFormatUSD = new Windows.Globalization.NumberFormatting.CurrencyFormatter("USD");
    var currencyValueUSD = currencyFormatUSD.Format(valueToBeFormatted);

    // Create a formatter initialized to a specific currency.
    // In this case it's the Euro with the default number formatting for France.
    var currencyFormatEuroFR = new Windows.Globalization.NumberFormatting.CurrencyFormatter("EUR", new[] { "fr-FR" }, "FR");
    var currencyValueEuroFR = currencyFormatEuroFR.Format(valueToBeFormatted);

    // Results for display.
    var results = "Fixed number (" + valueToBeFormatted + ")\n" +
                    "With user's default currency: " + userCurrencyValue + "\n" +
                    "Formatted US Dollar: " + currencyValueUSD + "\n" +
                    "Formatted Euro (fr-FR defaults): " + currencyValueEuroFR;
```

Можно проверить код выше на вашем компьютере, изменив страну или регион в разделе **Параметры** > **Время и язык** > **Язык и региональные стандарты** > **Страна или регион**. Выберите страну или регион (например, Исландию) и запустите код снова.

## <a name="use-a-culturally-appropriate-calendar"></a>Используйте календарь, соответствующий заданным региональным параметрам

Для разных стран и языков применяются разные календари. Григорианский календарь по умолчанию применяется не во всех регионах. В некоторых странах пользователи могут выбирать альтернативные календари, например японский или арабский лунный календарь. Даты и время в календаре зависят также от часовых поясов и перехода на летнее время.

Используйте стандартные [элементы управления для выбора календаря, даты и времени](../controls-and-patterns/date-and-time.md), чтобы обеспечить использование предпочтительного календарного формата. Для более сложных сценариев, в которых требуется напрямую работать с календарем, библиотека **Windows.Globalization** предлагает класс [**Calendar**](/uwp/api/windows.globalization.calendar?branch=live), который позволяет отобразить календарь для данного языка, региона и типа календаря.

## <a name="format-phone-numbers-appropriately"></a>Задавайте соответствующий формат номера телефона.

Номера телефона форматируются по-разному в различных регионах. Количество цифр, их группировка и значимость определенных частей номера телефона могут различаться в разных странах. Начиная c Windows 10 версии 1607, используйте классы в пространстве имен [**PhoneNumberFormatting**](/uwp/api/windows.globalization.phonenumberformatting?branch=live) для форматирования номера телефона в соответствии с текущим регионом.

Класс [**PhoneNumberInfo**](/uwp/api/windows.globalization.phonenumberformatting.phonenumberinfo?branch=live) анализирует строку цифр и позволяет определить, являются ли цифры действительным номером телефона в текущем региона, проверить равенство двух номеров, а также извлечь различные функциональные части телефонного номера, такие как код страны или географическая область.

Класс [**PhoneNumberFormatter**](/uwp/api/windows.globalization.phonenumberformatting.phonenumberformatter?branch=live) форматирует строку цифр или **PhoneNumberInfo** для отображения, даже если строка цифр представляет собой телефонный номер, указанный не полностью. (Форматирование номера, указанного не полностью можно использовать по мере ввода номера пользователем.)

В следующем примере показано, как использовать **PhoneNumberFormatter** для форматирования номера телефона во время его ввода. Каждый раз, когда в элементе **TextBox** с названием phoneNumberInputTextBox меняется текст, содержимое текстового поля форматируется с учетом текущего региона по умолчанию и отображается в элементе **TextBlock** с названием phoneNumberOutputTextBlock. В целях демонстрации строка имеет региональный формат Новой Зеландии и отображается в элементе TextBlock с именем phoneNumberOutputTextBlockNZ.
  
```csharp
    using Windows.Globalization.PhoneNumberFormatting;

    PhoneNumberFormatter currentFormatter, NZFormatter;

    public MainPage()
    {
        this.InitializeComponent();

        // Use the default formatter for the current region
        this.currentFormatter = new PhoneNumberFormatter();

        // Create an explicit formatter for New Zealand. 
        PhoneNumberFormatter.TryCreate("NZ", out this.NZFormatter);
    }

    private void phoneNumberInputTextBox_TextChanged(object sender, TextChangedEventArgs e)
    {
        // Format for the default region.
        this.phoneNumberOutputTextBlock.Text = currentFormatter.FormatPartialString(this.phoneNumberInputTextBox.Text);

        // If the NZFormatter was created successfully, format the partial string for the NZ TextBlock.
        if(this.NZFormatter != null)
        {
            this.phoneNumberOutputTextBlockNZ.Text = this.NZFormatter.FormatPartialString(this.phoneNumberInputTextBox.Text);
        }
    }
```    

Можно проверить код выше на вашем компьютере, изменив страну или регион в разделе **Параметры** > **Время и язык** > **Язык и региональные стандарты** > **Страна или регион**. Выберите страну или регион (например, Новую Зеландию, чтобы убедиться в соответствии форматов) и запустите код снова. Для данных тестов можно выполнить поиск в Интернете, чтобы найти номер телефона компании, находящейся в Новой Зеландии.

## <a name="the-users-language-and-cultural-preferences"></a>Языковые и региональные параметры, заданные пользователем

Если от языка или региональных параметров зависит функциональность вашего приложения, Windows дает возможность обращаться к этим параметрам с помощью [**Windows.System.UserProfile.GlobalizationPreferences**](/uwp/api/windows.system.userprofile.globalizationpreferences?branch=live). При необходимости используйте класс **GlobalizationPreferences**, чтобы получить данные текущего географического региона, предпочитаемых языков, валют и других настроек пользователя. Однако помните, что если строки/изображения в вашем приложении не локализованы для предпочитаемого языка пользователя, дата и время, а также другие данные, форматированные для этого предпочитаемого языка, не будут соответствовать отображаемым строкам.

## <a name="important-apis"></a>Важные API

* [DateTimeFormatter](/uwp/api/windows.globalization.datetimeformatting?branch=live)
* [NumberFormatting](/uwp/api/windows.globalization.numberformatting?branch=live)
* [Calendar](/uwp/api/windows.globalization.calendar?branch=live)
* [PhoneNumberFormatting](/uwp/api/windows.globalization.phonenumberformatting?branch=live)
* [GlobalizationPreferences](/uwp/api/windows.system.userprofile.globalizationpreferences?branch=live)

## <a name="related-topics"></a>Смежные разделы

* [Элементы управления календарем, датой и временем](../controls-and-patterns/date-and-time.md)
* [Обзор языков профиля пользователя и языков манифеста приложения](manage-language-and-region.md)
* [Адаптация ресурсов с учетом языка, масштаба, высокой контрастности и других квалификаторов](../../app-resources/tailor-resources-lang-scale-contrast.md)

## <a name="samples"></a>Примеры

* [Подробности о календаре и пример расчетов](http://go.microsoft.com/fwlink/p/?linkid=231636)
* [Пример форматирования даты и времени](http://go.microsoft.com/fwlink/p/?linkid=231618)
* [Пример настроек глобализации](http://go.microsoft.com/fwlink/p/?linkid=231608)
* [Пример форматирования и разбора чисел](http://go.microsoft.com/fwlink/p/?linkid=231620)
