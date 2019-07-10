---
title: Подготовка приложения к изменению японского летосчисления
description: Узнайте об изменениях японской эры в мае 2019 года и о том, как подготовить свое приложение.
ms.assetid: 5A945F9A-8632-4038-ADD6-C0568091EF27
ms.date: 4/26/2019
ms.topic: article
keywords: windows 10, uwp, localizability, localization, japanese, era
ms.localizationpriority: high
ms.openlocfilehash: 54d66d0426e5f0c41d48b93ba96781786d6fab92
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66363782"
---
# <a name="prepare-your-application-for-the-japanese-era-change"></a>Подготовка приложения к изменению японского летосчисления

> [!NOTE]
> 1 апреля 2019 года было объявлено название новой эры: Рэйва (令和). 25 апреля корпорация Майкрософт выпустила пакеты для разных операционных систем Windows, содержащие обновленный раздел реестра с именем новой эры. Обновите устройство и проверьте реестр, чтобы узнать, есть ли у него новый раздел, а затем протестируйте приложение. См. [эту справочную статью](https://support.microsoft.com/help/4469068/summary-of-new-japanese-era-updates-kb4469068), чтобы узнать как проверить, что операционная система получила обновленный раздел реестра.

Японский календарь разделен на эры и большинство людей, пользовавшихся вычислительными технологиями современной эпохи, находилось в эре Хэйсэй. Однако 1 мая 2019 года начнется новая эра. Так как эра изменится первый раз за долгое время, программное обеспечение, поддерживающее японский календарь, необходимо протестировать, чтобы убедиться в его правильной работе в начале новой эры.

В следующих разделах приведены инструкции по подготовке и тестированию приложения для работы в наступающей новой эре.

> [!NOTE]
> Для этого рекомендуется использовать тестовый компьютер, поскольку вносимые вами изменения затронут работу всей машины.

## <a name="add-a-registry-key-for-the-new-era"></a>Добавление раздела реестра для новой эры

> [!NOTE]
> Приведенные ниже инструкции предназначены для устройств, которые еще не были обновлены с помощью нового раздела реестра. Сначала проверьте, содержит ли устройство новый раздел реестра и если это не так, проверьте, следуя приведенным инструкциям.

Важно проверить наличие проблем совместимости перед сменой эры, и это можно сделать сейчас, используя новое имя эры. Чтобы сделать это, добавьте раздел реестра для новой эры, используя **редактор реестра**.

1. Перейдите к разделу **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Calendars\Japanese\Eras**.
2. Выберите **Изменить > Создать > Строковое значение** и присвойте ему имя **2019 05 01**.
3. Щелкните раздел правой кнопкой мыши и выберите **Изменить**.
4. В поле **Value data** (данные значения) введите**令和_令_Reiwa_R** (можно скопировать здесь и вставить, чтобы упростить процесс).

Дополнительные сведения о формате этих разделов рееста см. в статье [Работа с эрами японского календаря](https://docs.microsoft.com/windows/desktop/Intl/era-handling-for-the-japanese-calendar).

1 апреля 2019 года было объявлено название новой эры. 25 апреля было выпущено обновление с новым разделом реестра для поддерживаемых версий Windows, содержащее имя, позволяющее проверить правильность обработки приложением. Это обновление распространяется на поддерживаемые более ранние версии Windows 10, а также Windows 8 и 7.

Свой раздел реестра можно удалить после завершения тестирования приложения. Это послужит гарантией того, что он не будет мешать новому разделу реестра, который будет добавлен при обновлении Windows.

## <a name="change-your-devices-calendar-format"></a>Изменение формата календаря устройства

После добавления раздела реестра для новой эры необходимо настроить устройство на использование японского календаря. Для этого не требуется использовать устройство с интерфейсом, локализованным на японский язык. С целью проведения тщательного тестирования можно установить японский языковой пакет, однако этого не требуется для выполнения базового тестирования.

Настройка устройства для использования японского календаря:

1. откройте файл **intl.cpl** (найдите его с помощью строки поиска Windows);
2. в раскрывающемся меню **Формат** выберите **Японский (Япония)** ;
3. выберите **Дополнительные параметры**;
4. откройте вкладку **Дата**;
5. в раскрывающемся меню **Тип календаря** выберите **和暦** (*wareki*, то есть японский календарь). Это должен быть второй пункт.
6. Нажмите кнопку **ОК**.
7. Нажмите кнопку **ОК** в окне **Регион**.

Теперь ваше устройство должно быть настроено на использование японского календаря, и оно будет отображать, все эры входящие в реестр. Ниже приведен пример того, что теперь можно увидеть в правом нижнем углу экрана:

![дата и время в японском календаре](images/japanese-calendar-format.png)

## <a name="adjust-your-devices-clock"></a>Настройка часов устройства

Чтобы проверить, что приложение работает правильно с новой эрой, необходимо перевести часы компьютера на 1 мая 2019 года или более позднюю дату. Приведенные ниже инструкции предназначены для Windows 10, но в Windows 8 и 7 эта процедура должна работать аналогичным образом:

1. щелкните правой кнопкой мыши дату и время эры в нижнем правом углу экрана;
2. выберите **Настройка даты и времени**;
3. в приложении "Настройки" в разделе **Изменить дату и время** выберите **Изменить**;
4. выберите дату 1 мая 2019 года или более позднюю дату.

> [!NOTE]
> Возможно, вы не сможете изменить дату в зависимости от настроек организации; в таком случае обратитесь к администратору. Кроме того можно изменить раздел реестра для дат, которые уже прошли.

## <a name="test-your-application"></a>Тестирование приложения

Теперь проверьте, как ваше приложение обрабатывает новую эру. Проверьте места, где отображается дата, например, отметки времени и указатели даты. Убедитесь, что эра отображается правильно до даты 1 мая 2019 года (Хэйсэй, 平成) и после нее (Рэйва, 令 和).

### <a name="gannen-"></a>*Ганнэн* (元年)

Обычно формат японского календаря следующий: **&lt;Название эры&gt;&lt;Год эры&gt;** . Например, 2018 год — это **Хэйсэй 30** (平成30年).  Тем не менее первый год эры является особенным. Для него вместо **&lt;Название эры&gt; 1** отображается **&lt;Название эры&gt;元年**(*Ганнэн*). Таким образом, первый год эры Хэйсэй будет 平成元年 (*Хэйсэй Ганнэн*). Убедитесь, что приложение правильно обрабатывает первый год в новой эре и правильно выводит знаки 令和元年.

## <a name="related-apis"></a>Связанные API-интерфейсы

Несколько API-интерфейсов (WinRT, .NET и Win32) будут обновлены для обработки изменения эры. Если вы решите их использовать, они выполнят большую часть работы. Однако, даже если вы полностью полагаетесь на эти API-интерфейсы, все равно рекомендуется протестировать приложение и убедиться, что вы получаете необходимое поведение, особенно в том случае,когда вы выполняете с их помощью какие-то особые действия, например, синтаксический анализ.

Сведения об обновлениях для операционной системы и пакета SDK см. в разделе [Обновления изменений японской эры в мае 2019 года](https://support.microsoft.com/help/4470918/updates-for-may-2019-japan-era-change).

Будут затронуты следующие API-интерфейсы:

### <a name="winrt"></a>WinRT

* [Пространство имен Windows.Globalization](https://docs.microsoft.com/uwp/api/windows.globalization)
  * [Класс Calendar](https://docs.microsoft.com/uwp/api/windows.globalization.calendar)
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
    * [Свойство Era](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.era)
    * [Метод EraAsString](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.eraasstring)
    * [Свойство FirstYearInThisEra](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.firstyearinthisera)
    * [Свойство LastEra](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.lastera)
    * [Свойство LastYearInThisEra](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.lastyearinthisera)
    * [Свойство NumberOfYearsInThisEra](https://docs.microsoft.com/uwp/api/windows.globalization.calendar.numberofyearsinthisera)
* [Пространство имен Windows.Globalization.DateTimeFormatting](https://docs.microsoft.com/uwp/api/windows.globalization.datetimeformatting)
  * [Класс DateTimeFormatter](https://docs.microsoft.com/uwp/api/windows.globalization.datetimeformatting.datetimeformatter)
    * [Метод Format](https://docs.microsoft.com/uwp/api/windows.globalization.datetimeformatting.datetimeformatter.format)

### <a name="net"></a>.NET

* [Пространство имен System](https://docs.microsoft.com/dotnet/api/system)
  * [Структура DateTime](https://docs.microsoft.com/dotnet/api/system.datetime)
  * [Структура DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset)
* [Пространство имен System.Globalization](https://docs.microsoft.com/dotnet/api/system.globalization)
  * [Класс Calendar](https://docs.microsoft.com/dotnet/api/system.globalization.calendar)
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

* [Работа с эрами японского календаря](https://docs.microsoft.com/windows/desktop/Intl/era-handling-for-the-japanese-calendar)
* [Проблема перехода компьютеров на 2000 год в японском календаре](https://blogs.msdn.microsoft.com/shawnste/2018/04/12/the-japanese-calendars-y2k-moment/)
* [Использование реестра для тестирования новой японской эры в Windows](https://blogs.msdn.microsoft.com/shawnste/2018/08/07/using-the-registry-to-test-the-new-japanese-era-on-windows/)
* [Ганнэн и Ичинен](https://blogs.msdn.microsoft.com/shawnste/2018/11/12/gannen-vs-ichinen/)
* [Обновления изменений японской эры в мае 2019 года](https://support.microsoft.com/help/4470918/updates-for-may-2019-japan-era-change)
* [Обработка новой эры в японском календаре в .NET](https://devblogs.microsoft.com/dotnet/handling-a-new-era-in-the-japanese-calendar-in-net/)
