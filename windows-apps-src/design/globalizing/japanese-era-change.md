---
title: Подготовка приложения к изменению японской эры
description: Узнайте об изменении японской эры в мае 2019 года и о том, как подготовить свое приложение.
ms.assetid: 5A945F9A-8632-4038-ADD6-C0568091EF27
ms.date: 12/7/2018
ms.topic: article
keywords: windows 10, uwp, локализуемость, локализация, японский, эра
ms.localizationpriority: high
ms.openlocfilehash: 0d5de4c1713ab80afcdf2e028d39340aebcc018b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57617659"
---
# <a name="prepare-your-application-for-the-japanese-era-change"></a>Подготовка приложения к изменению японской эры

Японский календарь разделен на эры и большинство людей, пользовавшихся вычислительными технологиями современной эпохи, находилось в эре Хэйсэй. Тем не менее 1 мая 2019 года начнется новая эра. Так как эра изменится первый раз за долгое время, программное обеспечение, которое поддерживает японский календарь, необходимо будет протестировать, чтобы убедиться, что оно будет работать надлежащим образом, когда начнется новая эра.

В следующих разделах приведены инструкции по подготовке и тестированию приложения для работы в предстоящей новой эре.

> [!NOTE]
> Для этого рекомендуется использовать тестовый компьютер, так как вносимые вами изменения затронут работу всей машины.

## <a name="add-a-registry-key-for-the-new-era"></a>Добавление раздела реестра для новой эры

Очень важно перед сменой эры провести проверку на проблемы совместимости. Это можно сделать, используя имя заполнителя. Чтобы сделать это, добавьте раздел реестра для новой эры, используя **редактор реестра**:

1. Перейдите в раздел **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Calendars\Japanese\Eras**.
2. Выберите **Изменить > Создать > Строковое значение** и присвойте ему имя **2019 05 01**.
3. Щелкните раздел правой кнопкой мыши и выберите **Изменить**.
4. В **значение** введите **??\_?\_?????? \_?** (для простоты можно скопировать и вставить значение отсюда).

Дополнительные сведения о формате этих разделов рееста см. в статье [Работа с эрами японского календаря](https://docs.microsoft.com/windows/desktop/Intl/era-handling-for-the-japanese-calendar).

После объявления об имени новой эры обновление с новым разделом реестра для поддерживаемых версий Windows будет содержать это имя и вы сможете проверить, правильно ли его обрабатывает ваше приложение. Это обновление будет распространяться для поддерживаемых более ранних версий Windows 10, в том числе для Windows 8 и 7.

После завершения тестирования приложения вы можете удалить раздел реестра заполнителя. Это позволит гарантировать, что он не будет влиять на новый раздел реестра, который будет добавлен при обновлении Windows.

## <a name="change-your-devices-calendar-format"></a>Изменение формата календаря устройства

После добавления раздела реестра для новой эры необходимо настроить устройство, чтобы можно было использовать японский календарь. Для этого не требуется использовать устройство с интерфейсом, локализованным на японский язык. С целью проведения тщательного тестирования может также потребоваться установить японский языковой пакет, однако это не требуется для выполнения базовой проверки.

Чтобы настроить устройство для использования японского календаря, выполните следующие действия.

1. Откройте файл **intl.cpl** (найдите его с помощью строки поиска Windows).
2. В раскрывающемся меню **Формат** выберите **Японский (Япония)**.
3. Выберите **Дополнительные параметры**.
4. Откройте вкладку **Дата**.
5. В раскрывающемся меню **Тип календаря** выберите **和暦** (*wareki*, то есть японский календарь). Это должен быть второй пункт.
6. Нажмите кнопку **ОК**.
7. Нажмите кнопку **ОК** в окне **Регион**.

Теперь ваше устройство должно быть настроено для использования японского календаря, и оно будет показывать любые эры в реестре. Ниже приведен пример того, что теперь можно увидеть в правом нижнем углу экрана.

![Дата и время в японском календаре](images/japanese-calendar-format.png)

## <a name="adjust-your-devices-clock"></a>Настройка часов устройства

Чтобы проверить, что приложение работает правильно с новой эрой, необходимо настроить часы компьютера для даты 1 мая 2019 года или более поздней даты. Приведенные ниже инструкции предназначены для Windows 10, но в Windows 8 и 7 эта процедура должна работать аналогичным образом.

1. Щелкните правой кнопкой мыши дату и время эры в нижнем правом углу экрана.
2. Выберите **Настройка даты и времени**.
3. В приложении "Параметры" в разделе **Изменить дату и время** выберите **Изменить**.
4. Выберите дату 1 мая 2019 года или более позднюю дату.

> [!NOTE]
> Вы не сможете изменить дату, на основе параметров организации; Если это так, обратитесь к администратору. Кроме того вы можете изменить ключ реестра заполнитель для дата которых уже прошло.

## <a name="test-your-application"></a>Тестирование приложения

Теперь проверьте, как ваше приложение обрабатывает новую эру. Проверьте места, где отображается дата, например метки времени и управляющие элементы выбора даты. Убедитесь в правильности эру до 1 мая 2019 г. (Хэйсэй, 平成) и после (?(?).

### <a name="gannen-"></a>*Ганнэн* (元年)

Обычно имеет формат японского календаря  **&lt;имя эры&gt; &lt;году эры&gt;**. Например, 2018 год — это **Хэйсэй 30** (平成30年).  Тем не менее первый год эры является особенным. Для него вместо **&lt;Название эры&gt; 1** отображается **&lt;Название эры&gt;元年**(*Ганнэн*). Поэтому первым годом эры Хэйсэй будет 平成元年 (*Хэйсэй Ганнэн*). Убедитесь, что приложение правильно обрабатывает первый год выручка от новой эры и правильно выводит?? 元年.

## <a name="related-apis"></a>Связанные API-интерфейсы

Есть несколько API-интерфейсов WinRT, .NET и Win32, которые будут обновлены для обработки изменения эры. Если вы решите их использовать, они выполнят большую часть работы. Тем не менее, даже если вы решите полностью положиться на эти API-интерфейсы, все равно рекомендуется протестировать приложение и убедиться, что вы получаете необходимое поведение, особенно в том случае, если вы выполняете с их помощью какие-то особые действия, например синтаксический анализ.

Сведения об обновлениях для операционной системы и пакета SDK см. в разделе [Обновления изменений японской эры в мае 2019 года](https://support.microsoft.com/help/4470918/updates-for-may-2019-japan-era-change).

Будут затронуты следующие API-интерфейсы.

### <a name="winrt"></a>WinRT

* [Пространство имен Windows.Globalization](https://docs.microsoft.com/uwp/api/windows.globalization)
    * [Класс календаря](https://docs.microsoft.com/uwp/api/windows.globalization.calendar)
        * [Метод AddDays](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.adddays)
        * [Метод AddEras](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.adderas)
        * [Метод AddHours](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.addhours)
        * [Метод AddMinutes](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.addminutes)
        * [Метод AddMonths](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.addmonths)
        * [Метод AddNanoseconds](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.addnanoseconds)
        * [Метод AddPeriods](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.addperiods)
        * [Метод AddSeconds](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.addseconds)
        * [Метод AddWeeks](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.addweeks)
        * [Метод AddYears](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.addyears)
        * [Свойство эры](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.era)
        * [Метод EraAsString](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.eraasstring)
        * [Свойство FirstYearInThisEra](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.firstyearinthisera)
        * [Свойство LastEra](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.lastera)
        * [Свойство LastYearInThisEra](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.lastyearinthisera)
        * [Свойство NumberOfYearsInThisEra](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.numberofyearsinthisera)     
* [Пространство имен Windows.Globalization.DateTimeFormatting](https://docs.microsoft.com/uwp/api/windows.globalization.datetimeformatting)
    * [Класс DateTimeFormatter](https://docs.microsoft.com/uwp/api/windows.globalization.datetimeformatting.datetimeformatter)
        * [Format-метод](https://docs.microsoft.com/uwp/api/windows.globalization.datetimeformatting.datetimeformatter.format)

### <a name="net"></a>.NET

* [Пространство имен System](https://docs.microsoft.com/dotnet/api/system)
    * [Структура даты и времени](https://docs.microsoft.com/dotnet/api/system.datetime)
    * [Структура DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset)
* [Пространство имен System.Globalization](https://docs.microsoft.com/dotnet/api/system.globalization)
    * [Класс календаря](https://docs.microsoft.com/dotnet/api/system.globalization.calendar)
    * [Класс DateTimeFormatInfo](https://docs.microsoft.com/dotnet/api/system.globalization.datetimeformatinfo)
    * [Класс JapaneseCalendar](https://docs.microsoft.com/dotnet/api/system.globalization.japanesecalendar)
    * [Класс JapaneseLunisolarCalendar](https://docs.microsoft.com/dotnet/api/system.globalization.japaneselunisolarcalendar)

### <a name="win32"></a>Win32

* [Заголовок datetimeapi.h](https://docs.microsoft.com/windows/desktop/api/datetimeapi/)
    * [Функция GetDateFormatA](https://docs.microsoft.com/windows/desktop/api/datetimeapi/nf-datetimeapi-getdateformata)
    * [Функция GetDateFormatEx](https://docs.microsoft.com/windows/desktop/api/datetimeapi/nf-datetimeapi-getdateformatex)
    * [Функция GetDateFormatW](https://docs.microsoft.com/windows/desktop/api/datetimeapi/nf-datetimeapi-getdateformatw)
* [Заголовок winnls.h](https://docs.microsoft.com/windows/desktop/api/winnls/)
    * [Функция EnumDateFormatsA](https://docs.microsoft.com/windows/desktop/api/winnls/nf-winnls-enumdateformatsa)
    * [Функция EnumDateFormatsExA](https://docs.microsoft.com/windows/desktop/api/winnls/nf-winnls-enumdateformatsexa)
    * [Функция EnumDateFormatsExEx](https://docs.microsoft.com/windows/desktop/api/winnls/nf-winnls-enumdateformatsexex)
    * [Функция EnumDateFormatsExW](https://docs.microsoft.com/windows/desktop/api/winnls/nf-winnls-enumdateformatsexw)
    * [Функция EnumDateFormatsW](https://docs.microsoft.com/windows/desktop/api/winnls/nf-winnls-enumdateformatsw)
    * [Функция GetCalendarInfoA](https://docs.microsoft.com/windows/desktop/api/winnls/nf-winnls-getcalendarinfoa)
    * [Функция GetCalendarInfoEx](https://docs.microsoft.com/windows/desktop/api/winnls/nf-winnls-getcalendarinfoex)
    * [Функция GetCalendarInfoW](https://docs.microsoft.com/windows/desktop/api/winnls/nf-winnls-getcalendarinfow)

## <a name="see-also"></a>См. также

* [Обработка для японского календаря эры](https://docs.microsoft.com/windows/desktop/Intl/era-handling-for-the-japanese-calendar)
* [Момент Y2K японского календаря](https://blogs.msdn.microsoft.com/shawnste/2018/04/12/the-japanese-calendars-y2k-moment/)
* [С помощью реестра, чтобы проверка новой японской эры в Windows](https://blogs.msdn.microsoft.com/shawnste/2018/08/07/using-the-registry-to-test-the-new-japanese-era-on-windows/)
* [Vs Ганнэн Ichinen](https://blogs.msdn.microsoft.com/shawnste/2018/11/12/gannen-vs-ichinen/)
* [Обновления может измениться эры Японии 2019 г.](https://support.microsoft.com/help/4470918/updates-for-may-2019-japan-era-change)
* [Обработка новой эре японского календаря в .NET](https://blogs.msdn.microsoft.com/dotnet/2018/11/14/handling-a-new-era-in-the-japanese-calendar-in-net/)