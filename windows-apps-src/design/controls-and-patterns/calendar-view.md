---
Description: Представление календаря позволяет пользователю просматривать календарь и взаимодействовать с ним, перемещаясь по месяцам, годам и десятилетиям.
title: Представление календаря
ms.assetid: d8ec5ba8-7a9d-405d-a1a5-5a1b502b9e64
label: Calendar view
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: ksulliv
dev-contact: joyate
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 08fac0fedaa3f01ad362a57a6db3abf428771258
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80081261"
---
# <a name="calendar-view"></a>Представление календаря

Представление календаря позволяет пользователю просматривать календарь и взаимодействовать с ним, перемещаясь по месяцам, годам и десятилетиям. Пользователь может выбрать отдельную дату или диапазон дат. Не имеет поверхности выбора, и календарь всегда виден.

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Библиотека пользовательского интерфейса Windows 2.2 или более поздних версий содержит новый шаблон для этого элемента управления, который использует закругленные углы. Дополнительные сведения см. в разделе о [радиусе угла](/windows/uwp/design/style/rounded-corner). WinUI — это пакет NuGet, содержащий новые элементы управления и функции пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **API платформы:**  [класс CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) и [событие SelectedDatesChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selecteddateschanged)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления
Используйте представление календаря, чтобы пользователь мог выбрать дату или диапазон дат в постоянно отображаемом календаре.

Если необходимо предоставить пользователю возможность выбора нескольких дат одновременно, используйте представление календаря. Если необходимо предоставить пользователю возможность выбора одной даты и не нужно, чтобы календарь был виден всегда, рекомендуется использовать элементы управления [Выбор даты календаря](calendar-date-picker.md) или [Выбор даты](date-picker.md).

Дополнительные сведения о выборе правильного элемента см. в статье [Элементы управления датой и временем](date-and-time.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/CalendarView">открыть приложение и увидеть CalendarView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Представление календаря состоит из 3 отдельных представлений: представление месяца, представление года и представление десятилетия. По умолчанию он запускается с открытым представлением месяца. Чтобы указать желаемое начальное представление, задайте свойство [DisplayMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.displaymode).

![3 представления представления календаря](images/calendar-view-3-views.png)

Пользователю необходимо нажать на заголовок в представлении месяца, чтобы открыть представление года, и нажать на заголовок в представлении года, чтобы открыть представление десятилетия. Чтобы вернуться в представление года, необходимо выбрать год в представление десятилетия, и выбрать месяц в представлении года, чтобы вернуться в представление месяца. С помощью двух стрелок возле заголовка можно перемещаться вперед или назад по месяцам, годам и десятилетиям.

## <a name="create-a-calendar-view"></a>Создание представления календаря

Этот пример демонстрирует, как создать простое представление календаря.

```xaml
<CalendarView/>
```

Созданное представление календаря будет выглядеть следующим образом:

![Пример представления календаря](images/controls_calendar_monthview.png)

### <a name="selecting-dates"></a>Выбора дат

По умолчанию для свойства [SelectionMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selectionmode) задано значение **Single**. Это позволяет пользователю выбрать дату в календаре. Установите для SelectionMode значение **None**, чтобы отключить выбор даты.

Установите для SelectionMode значение **Multiple**, чтобы пользователь мог выбрать несколько дат. Чтобы программным способом выбрать несколько дат, добавьте объекты [DateTime](https://docs.microsoft.com/dotnet/api/system.datetime)/[DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset) в коллекцию [SelectedDates](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selecteddates), как показано здесь:

```csharp
calendarView1.SelectedDates.Add(DateTimeOffset.Now);
calendarView1.SelectedDates.Add(new DateTime(1977, 1, 5));
```

Пользователь может отменить выбор даты, нажав эту дату в сетке календаря.

Можно обработать событие [SelectedDatesChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selecteddateschanged) так, чтобы оно получало оповещение в случае изменения коллекции [SelectedDates](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selecteddates).

> [!NOTE]
> Важные сведения о значениях даты см. в разделе [Значения DateTime и Calendar](date-and-time.md#datetime-and-calendar-values) статьи "Элементы управления датой и временем".

### <a name="customizing-the-calendar-views-appearance"></a>Настройка вида представления календаря

Представление календаря состоит как из элементов XAML , определенных в ControlTemplate, так и визуальных элементов, обработанных непосредственно элементом управления.
- Элементы XAML, определенные в шаблоне элементов управления, включают границу, которая содержит элемент управления, заголовок, кнопки "Назад" и "Далее", а также элементы DayOfWeek. Можно задать стиль и изменить шаблон этих элементов, как и любого другого элемента управления XAML.
- Сетка календаря состоит из объектов [CalendarViewDayItem](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarViewDayItem). Задать стиль и изменить шаблон этих элементов невозможно, но для настройки их внешнего вида предоставляются различные свойства.

На данной диаграмме показаны элементы, которые составляют представление месяца в календаре. См. подробнее в разделе "Примечания" в описании класса [CalendarViewDayItem](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarViewDayItem).

![Элементы представления месяца в календаре](images/calendar-view-month-elements.png)

В следующей таблице приведен список свойств, которые можно редактировать для изменения внешнего вида элементов календаря.

Элемент | Свойства
--------|-----------
DayOfWeek | [DayOfWeekFormat](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.dayofweekformat)
CalendarItem | [CalendarItemBackground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.calendaritembackground), [CalendarItemBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.calendaritemborderbrush), [CalendarItemBorderThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.calendaritemborderthickness), [CalendarItemForeground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.calendaritemforeground)
DayItem | [DayItemFontFamily](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.dayitemfontfamily), [DayItemFontSize](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.dayitemfontsize), [DayItemFontStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.dayitemfontstyle), [DayItemFontWeight](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.dayitemfontweight), [HorizontalDayItemAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.horizontaldayitemalignment), [VerticalDayItemAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.verticaldayitemalignment), [CalendarViewDayItemStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.calendarviewdayitemstyle)
MonthYearItem (в представлениях года и десятилетия эквивалентно DayItem) | [MonthYearItemFontFamily](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.monthyearitemfontfamily), [MonthYearItemFontSize](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.monthyearitemfontsize), [MonthYearItemFontStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.monthyearitemfontstyle), [MonthYearItemFontWeight](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.monthyearitemfontweight)
FirstOfMonthLabel | [FirstOfMonthLabelFontFamily](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.firstofmonthlabelfontfamily), [FirstOfMonthLabelFontSize](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.firstofmonthlabelfontsize), [FirstOfMonthLabelFontStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.firstofmonthlabelfontstyle), [FirstOfMonthLabelFontWeight](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.firstofmonthlabelfontweight), [HorizontalFirstOfMonthLabelAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.horizontalfirstofmonthlabelalignment), [VerticalFirstOfMonthLabelAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.verticalfirstofmonthlabelalignment), [IsGroupLabelVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.isgrouplabelvisible)
FirstofYearDecadeLabel (в представлениях года и десятилетия эквивалентно FirstOfMonthLabel) | [FirstOfYearDecadeLabelFontFamily](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.firstofyeardecadelabelfontfamily), [FirstOfYearDecadeLabelFontSize](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.firstofyeardecadelabelfontsize), [FirstOfYearDecadeLabelFontStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.firstofyeardecadelabelfontstyle), [FirstOfYearDecadeLabelFontWeight](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.firstofyeardecadelabelfontweight)
Границы визуального состояния | [FocusBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.focusborderbrush), [HoverBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.hoverborderbrush), [PressedBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.pressedborderbrush), [SelectedBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selectedborderbrush), [SelectedForeground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selectedforeground), [SelectedHoverBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selectedhoverborderbrush), [SelectedPressedBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selectedpressedborderbrush)
OutofScope | [IsOutOfScopeEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.isoutofscopeenabled), [OutOfScopeBackground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.outofscopebackground), [OutOfScopeForeground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.outofscopeforeground)
Сегодня | [IsTodayHighlighted](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.istodayhighlighted), [TodayFontWeight](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.todayfontweight), [TodayForeground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.todayforeground)

 По умолчанию в представлении месяца отображается одновременно 6 недель. Чтобы изменить количество отображаемых недель, задайте свойство [NumberOfWeeksInView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.numberofweeksinview). Минимальное количество отображаемых недель — 2; максимальное — 8.

По умолчанию представления года и десятилетия отображаются в сетке 4x4. Чтобы изменить количество строк или столбцов, вызовите [SetYearDecadeDisplayDimensions](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.setyeardecadedisplaydimensions) с желаемым количеством строк и столбцов. Это изменит сетку для представлений и года, и десятилетия.

Здесь представления года и десятилетия устанавливаются для отображения в сетке 3x4.

```csharp
calendarView1.SetYearDecadeDisplayDimensions(3, 4);
```

По умолчанию минимальная дата, отображаемая в представлении календаря, —100 лет до текущей даты, а максимальная дата — 100 лет после текущей даты. Чтобы изменить минимальную и максимальную даты, отображаемые в календаре, задайте свойства [MinDate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.mindate) и [MaxDate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.maxdate).

```csharp
calendarView1.MinDate = new DateTime(2000, 1, 1);
calendarView1.MaxDate = new DateTime(2099, 12, 31);
```

### <a name="updating-calendar-day-items"></a>Обновление элементов дня в календаре

Каждый день в календаре представлен объектом [CalendarViewDayItem](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarViewDayItem). Для получения доступа к отдельному элементу дня и использования его свойств и методов, обработайте событие [CalendarViewDayItemChanging](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.calendarviewdayitemchanging) и используйте свойство Item аргументов события для доступа к CalendarViewDayItem.

Чтобы запретить выбор дня в представлении календаря, установите для свойства [CalendarViewDayItem.IsBlackout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarviewdayitem.isblackout) значение **true**.

Чтобы отобразить контекстные данные о плотности событий дня, вызовите метод [CalendarViewDayItem.SetDensityColors](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarviewdayitem.setdensitycolors). Можно отобразить от 0 до 10 шкал плотности для каждого дня и установить цвет для каждой шкалы.

Вот некоторые элементы дня в календаре. Дни 1 и 2 затемнены. Для дней 2, 3 и 4 установлены разные шкалы плотности.

![Дни в календаре со шкалами плотности](images/calendar-view-density-bars.png)

### <a name="phased-rendering"></a>Поэтапная отрисовка

Представление календаря может содержать большое количество объектов CalendarViewDayItem. Чтобы обеспечить быстрое реагирование пользовательского интерфейса и плавные переходы в календаре, представление календаря поддерживает поэтапную отрисовку. Позволяет разбить обработку элемента дня на этапы. Если день перемещен из представления до завершения выполнения всех этапов, время на попытку обработки и отрисовки этого элемента больше тратиться не будет.

Этот пример иллюстрирует поэтапную отрисовку представления календаря для планирования встреч.
- На этапе 0 отрисовывается элемент дня по умолчанию.
- На этапе 1 затемняются даты, которые нельзя забронировать. Сюда входят прошлые даты, воскресенья, а также полностью забронированные даты.
- На этапе 2 проверяется каждая встреча, забронированная на этот день. Для каждой подтвержденной встречи отображается зеленая шкала плотности, а для каждойнеподтвержденной встречи — синяя.

Класс `Bookings` в этом примере взят из несуществующего приложения резервирования встреч и не отображается.

```xaml
<CalendarView CalendarViewDayItemChanging="CalendarView_CalendarViewDayItemChanging"/>
```

```csharp
private void CalendarView_CalendarViewDayItemChanging(CalendarView sender,
                                   CalendarViewDayItemChangingEventArgs args)
{
    // Render basic day items.
    if (args.Phase == 0)
    {
        // Register callback for next phase.
        args.RegisterUpdateCallback(CalendarView_CalendarViewDayItemChanging);
    }
    // Set blackout dates.
    else if (args.Phase == 1)
    {
        // Blackout dates in the past, Sundays, and dates that are fully booked.
        if (args.Item.Date < DateTimeOffset.Now ||
            args.Item.Date.DayOfWeek == DayOfWeek.Sunday ||
            Bookings.HasOpenings(args.Item.Date) == false)
        {
            args.Item.IsBlackout = true;
        }
        // Register callback for next phase.
        args.RegisterUpdateCallback(CalendarView_CalendarViewDayItemChanging);
    }
    // Set density bars.
    else if (args.Phase == 2)
    {
        // Avoid unnecessary processing.
        // You don't need to set bars on past dates or Sundays.
        if (args.Item.Date > DateTimeOffset.Now &&
            args.Item.Date.DayOfWeek != DayOfWeek.Sunday)
        {
            // Get bookings for the date being rendered.
            var currentBookings = Bookings.GetBookings(args.Item.Date);

            List<Color> densityColors = new List<Color>();
            // Set a density bar color for each of the days bookings.
            // It's assumed that there can't be more than 10 bookings in a day. Otherwise,
            // further processing is needed to fit within the max of 10 density bars.
            foreach (booking in currentBookings)
            {
                if (booking.IsConfirmed == true)
                {
                    densityColors.Add(Colors.Green);
                }
                else
                {
                    densityColors.Add(Colors.Blue);
                }
            }
            args.Item.SetDensityColors(densityColors);
        }
    }
}
```

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи

- [Элементы управления датой и временем](date-and-time.md)
- [Выбор даты в календаре](calendar-date-picker.md)
- [Выбор даты](date-picker.md)
- [Выбор времени](time-picker.md)
