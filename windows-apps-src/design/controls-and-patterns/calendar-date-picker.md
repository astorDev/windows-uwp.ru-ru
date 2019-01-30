---
Description: The calendar date picker is a drop down control that’s optimized for picking a single date from a calendar view where contextual information like the day of the week or fullness of the calendar is important.
title: Управляющий элемент выбора даты в календаре
ms.assetid: 9e0213e0-046a-4906-ba86-0b49be51ca99
label: Calendar date picker
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: ksulliv
dev-contact: joyate
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 94d50554baf412c7329521df68eb19f58340387f
ms.sourcegitcommit: a60ab85e9f2f9690e0141050ec3aa51f18ec61ec
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "9037226"
---
# <a name="calendar-date-picker"></a>Управляющий элемент выбора даты в календаре

 

Управляющий элемент выбора даты в календаре— это раскрывающийся элемент управления, оптимизированный для выбора отдельной даты в представлении календаря, когда важна контекстная информация, например день недели или заполненность календаря. Вы можете изменить календарь таким образом, чтобы обеспечить дополнительный контекст или ограничить доступные даты.

> **Важные API-интерфейсы**: [класс CalendarDatePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.aspx), [свойство Date](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.date.aspx), [событие DateChanged](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.datechanged.aspx)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления
Используйте **управляющий элемент выбора даты в календаре**, чтобы дать пользователю возможность выбрать одну дату в контекстном представлении календаря. Используйте этот элемент управления для таких действий, как выбор даты встречи или отъезда.

Чтобы пользователь мог выбрать известную дату, например дату рождения, если контекст календаря не важен, можно использовать [Выбор даты](date-picker.md).

Дополнительные сведения о выборе правильного элемента см. в статье [Элементы управления датой и временем](date-and-time.md).

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/CalendarDatePicker">открыть приложение и увидеть CalendarDatePicker в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Точка входа отображает замещающий текст, если дата не была установлена; в противном случае отображается выбранная дата. Когда пользователь выбирает точку входа, представление календаря развертывается, чтобы пользователь мог выбрать дату. Представление календаря перекрывает другие элементы пользовательского интерфейса, но не вытесняет их.

![Пример элемента выбора даты в календаре](images/calendar-date-picker-2-views.png)

## <a name="create-a-date-picker"></a>Создание элемента выбора даты

```xaml
<CalendarDatePicker x:Name="arrivalCalendarDatePicker" Header="Arrival date"/>
```

```csharp
CalendarDatePicker arrivalCalendarDatePicker = new CalendarDatePicker();
arrivalCalendarDatePicker.Header = "Arrival date";
```

Готовый элемент выбора даты в календаре выглядит следующим образом:

![Пример элемента выбора даты в календаре](images/calendar-date-picker-closed.png)

Элемент выбора даты в календаре содержит внутренний элемент [CalendarView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendarview.aspx) для выбора даты. В CalendarDatePicker доступно подмножество свойств CalendarView, таких как [IsTodayHighlighted](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.istodayhighlighted.aspx) и [FirstDayOfWeek](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.firstdayofweek.aspx), которые направляются во внутренний CalendarView для возможности их редактирования. 

Однако, изменить [SelectionMode](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendarview.selectionmode.aspx) внутреннего CalendarView для обеспечения возможности множественного выбора нельзя. Если требуется, чтобы пользователь мог выбрать несколько дат или чтобы календарь всегда отображался, можно использовать представление календаря вместо элемента выбора даты в календаре. Более подробные сведения о том, как можно изменить отображение календаря, см. в статье [Представление календаря](calendar-view.md).

### <a name="selecting-dates"></a>Выбора дат

Используйте свойство [Date](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.date.aspx), чтобы получить или установить выбранную дату. По умолчанию для свойства Date используется значение **null**. Когда пользователь выбирает дату в представлении календаря, это свойство обновляется. Пользователь может очистить дату, щелкнув выбранную дату в представлении календаря, чтобы отменить выбор. 

В коде дату можно задать, как показано ниже.

```csharp
myCalendarDatePicker.Date = new DateTime(1977, 1, 5);
```

Если задать Date в коде, значение будет ограничено свойствами [MinDate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.mindate.aspx) и [MaxDate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.maxdate.aspx).
- Если значение **Date** меньше значения **MinDate**, будет использоваться **MinDate**.
- Если значение **Date** больше значения **MaxDate**, будет использоваться **MaxDate**.

Можно обработать событие [DateChanged](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.datechanged.aspx) так, чтобы получать уведомление при изменении значения Date.

> [!NOTE]
Важные сведения о значениях даты см. в разделе [Значения DateTime и Calendar](date-and-time.md#datetime-and-calendar-values) статьи "Элементы управления датой и временем".

### <a name="setting-a-header-and-placeholder-text"></a>Настройка текста заголовка и замещающего текста

К элелементу выбора даты в календаре можно добавить [Header](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.header.aspx) (или метку) и [PlaceholderText](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.placeholdertext.aspx) (или водяной знак), чтобы пользователь понимал, для чего он используется. Чтобы настроить оформление заголовка, задайте свойство [HeaderTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.headertemplate.aspx) вместо Header.

По умолчанию в качестве замещающего текста используется фраза "Выберите дату". Вы можете удалить эту фразу, установив для свойства PlaceholderText пустую строку, или написать собственный текст, как показано ниже.

```xaml
<CalendarDatePicker x:Name="arrivalCalendarDatePicker" Header="Arrival date" 
                    PlaceholderText="Choose your arrival date"/>
```

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

- [Элементы управления датой и временем](date-and-time.md)
- [Представление календаря](calendar-view.md)
- [Управляющий элемент выбора даты](date-picker.md)
- [Управляющий элемент выбора времени](time-picker.md)
