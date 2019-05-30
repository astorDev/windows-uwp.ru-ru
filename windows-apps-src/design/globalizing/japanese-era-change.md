---
title: Подготовка приложения к изменению японской эры
description: Узнайте об изменении японской эры в мае 2019 года и о том, как подготовить свое приложение.
ms.assetid: 5A945F9A-8632-4038-ADD6-C0568091EF27
ms.date: 4/26/2019
ms.topic: article
keywords: windows 10, uwp, локализуемость, локализация, японский, эра
ms.localizationpriority: high
ms.openlocfilehash: 54d66d0426e5f0c41d48b93ba96781786d6fab92
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66363782"
---
# <a name="prepare-your-application-for-the-japanese-era-change"></a>Подготовка приложения к изменению японской эры

> [!NOTE]
> На 1 апреля 2019 г. было объявлено имя новой эры: Reiwa (令和). 25 апреля корпорация Майкрософт выпустила пакеты для разных операционных систем Windows, содержащий обновленные реестра с именем новой эры. Обновление устройства и проверьте реестр, чтобы определить наличие нового ключа и затем протестировать приложение. Проверьте [в этой статье поддержка](https://support.microsoft.com/help/4469068/summary-of-new-japanese-era-updates-kb4469068) чтобы убедиться в том операционной системы должны были получить обновленные реестра.

Японский календарь делится эр, и для большинства современных возраст вычислений, мы были эры Хэйсэй; Тем не менее на 1 мая 2019 г., начнет новую эру. Так как эра изменится первый раз за долгое время, программное обеспечение, которое поддерживает японский календарь, необходимо будет протестировать, чтобы убедиться, что оно будет работать надлежащим образом, когда начнется новая эра.

В следующих разделах приведены инструкции по подготовке и тестированию приложения для работы в предстоящей новой эре.

> [!NOTE]
> Для этого рекомендуется использовать тестовый компьютер, так как вносимые вами изменения затронут работу всей машины.

## <a name="add-a-registry-key-for-the-new-era"></a>Добавление раздела реестра для новой эры

> [!NOTE]
> Приведенные ниже инструкции предназначены для устройств, которые еще не были обновлены с помощью нового раздела реестра. Сначала проверьте, содержит ли устройство новый раздел реестра и если это не так, проверьте, следуя приведенным инструкциям.

Очень важно проверить наличие проблем совместимости, прежде чем эру изменилось, и можно сделать, так что теперь с помощью имени новой эры. Чтобы сделать это, добавьте раздел реестра для новой эры, используя **редактор реестра**:

1. Перейдите в раздел **Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Calendars\Japanese\Eras**.
2. Выберите **Изменить > Создать > Строковое значение** и присвойте ему имя **2019 05 01**.
3. Щелкните раздел правой кнопкой мыши и выберите **Изменить**.
4. В **значение** введите**令和_令_Reiwa_R** (можно скопировать и вставить здесь, чтобы упростить).

Дополнительные сведения о формате этих разделов рееста см. в статье [Работа с эрами японского календаря](https://docs.microsoft.com/windows/desktop/Intl/era-handling-for-the-japanese-calendar).

На 1 апреля 2019 г. было объявлено имя новой эры. 25 апреля обновление с помощью нового раздела реестра для поддерживаемых версий Windows, содержащая имя было выпущено, что позволяет проверить, что приложение обрабатывает ее должным образом. Это обновление распространяется на поддерживаемые более ранних версиях Windows 10, Windows 8 и 7.

После завершения тестирования приложения вы можете удалить раздел реестра заполнителя. Это позволит гарантировать, что он не будет влиять на новый раздел реестра, который будет добавлен при обновлении Windows.

## <a name="change-your-devices-calendar-format"></a>Изменение формата календаря устройства

После добавления раздела реестра для новой эры необходимо настроить устройство, чтобы можно было использовать японский календарь. Для этого не требуется использовать устройство с интерфейсом, локализованным на японский язык. С целью проведения тщательного тестирования может также потребоваться установить японский языковой пакет, однако это не требуется для выполнения базовой проверки.

Чтобы настроить устройство для использования японского календаря, выполните следующие действия.

1. Откройте файл **intl.cpl** (найдите его с помощью строки поиска Windows).
2. В раскрывающемся меню **Формат** выберите **Японский (Япония)** .
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

Теперь проверьте, как ваше приложение обрабатывает новую эру. Проверьте места, где отображается дата, например метки времени и управляющие элементы выбора даты. Убедитесь, что эру верен, до 1 мая 2019 г. (Хэйсэй 平成) и после (Reiwa 令和).

### <a name="gannen-"></a>*Ганнэн* (元年)

Обычно имеет формат японского календаря  **&lt;имя эры&gt; &lt;году эры&gt;** . Например, 2018 год — это **Хэйсэй 30** (平成30年).  Тем не менее первый год эры является особенным. Для него вместо **&lt;Название эры&gt; 1** отображается **&lt;Название эры&gt;元年**(*Ганнэн*). Поэтому первым годом эры Хэйсэй будет 平成元年 (*Хэйсэй Ганнэн*). Убедитесь, что приложение правильно обрабатывает первый год выручка от новой эры, а правильно выводит 令和元年.

## <a name="related-apis"></a>Связанные API-интерфейсы

Есть несколько API-интерфейсов WinRT, .NET и Win32, которые будут обновлены для обработки изменения эры. Если вы решите их использовать, они выполнят большую часть работы. Тем не менее, даже если вы решите полностью положиться на эти API-интерфейсы, все равно рекомендуется протестировать приложение и убедиться, что вы получаете необходимое поведение, особенно в том случае, если вы выполняете с их помощью какие-то особые действия, например синтаксический анализ.

Можно выполнить обновления ОС и пакетов SDK в [обновления для можете случайно изменить эры Японии 2019](https://support.microsoft.com/help/4470918/updates-for-may-2019-japan-era-change).

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

* [datetimeapi.h header](https://docs.microsoft.com/windows/desktop/api/datetimeapi/)
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
* [Обработка новой эре японского календаря в .NET](https://devblogs.microsoft.com/dotnet/handling-a-new-era-in-the-japanese-calendar-in-net/)
