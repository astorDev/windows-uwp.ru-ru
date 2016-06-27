---
author: Jwmsft
Description: "Управляющий элемент выбора даты в календаре — это раскрывающийся элемент управления, оптимизированный для выбора отдельной даты в представлении календаря, когда важна контекстная информация, например день недели или заполненность календаря."
title: "Управляющий элемент выбора даты в календаре"
ms.assetid: 9e0213e0-046a-4906-ba86-0b49be51ca99
label: Calendar date picker
template: detail.hbs
ms.sourcegitcommit: c183f7390c5b4f99cf0f31426c1431066e1bc96d
ms.openlocfilehash: 75f6bb925db63838e4985df15b50977b93805ffe

---

# Управляющий элемент выбора даты в календаре

Управляющий элемент выбора даты в календаре — это раскрывающийся элемент управления, оптимизированный для выбора отдельной даты в представлении календаря, когда важна контекстная информация, например день недели или заполненность календаря. Вы можете изменить календарь таким образом, чтобы обеспечить дополнительный контекст или ограничить доступные даты.

<span class="sidebar_heading" style="font-weight: bold;">Важные API</span>

-   [**Класс TimePicker**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.aspx)
-   [**Свойство Time**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.time.aspx)

## Это правильный элемент управления?
Используйте **Выбор даты в календаре**, чтобы пользователь мог выбрать дату в контекстном представлении календаря. Используйте этот элемент управления для таких действий, как выбор даты встречи или отъезда.

Чтобы пользователь мог выбрать известную дату, например дату рождения, если контекст календаря не важен, можно использовать [**Выбор даты**](date-picker.md).

Дополнительные сведения о выборе правильного элемента см. в статье [Элементы управления датой и временем](date-and-time.md).

## Примеры.

Точка входа отображает замещающий текст, если дата не была установлена; в противном случае отображается выбранная дата. Когда пользователь выбирает точку входа, представление календаря развертывается, чтобы пользователь мог выбрать дату. Представление календаря перекрывает другие элементы пользовательского интерфейса, но не вытесняет их.

![Пример элемента выбора даты в календаре](images/calendar-date-picker-2-views.png)

## Создание элемента выбора даты

```xaml
<CalendarDatePicker x:Name="arrivalCalendarDatePicker" Header="Arrival date"/>
```

```csharp
CalendarDatePicker arrivalCalendarDatePicker = new CalendarDatePicker();
arrivalCalendarDatePicker.Header = "Arrival date";
```

Готовый элемент выбора даты в календаре выглядит следующим образом:

![Пример элемента выбора даты в календаре](images/calendar-date-picker-closed.png)

Элемент выбора даты в календаре содержит внутренний элемент [**CalendarView**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendarview.aspx) для выбора даты. В CalendarDatePicker доступно подмножество свойств CalendarView, таких как [**IsTodayHighlighted**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.istodayhighlighted.aspx) и [**FirstDayOfWeek**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.firstdayofweek.aspx), которые направляются во внутренний CalendarView для возможности их редактирования. 

Однако, изменить [**SelectionMode**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendarview.selectionmode.aspx) внутреннего CalendarView для обеспечения возможности множественного выбора нельзя. Если требуется, чтобы пользователь мог выбрать несколько дат или чтобы календарь всегда отображался, можно использовать представление календаря вместо элемента выбора даты в календаре. Более подробные сведения о том, как можно изменить отображение календаря, см. в статье [Представление календаря](calendar-view.md).

### Выбора дат

Используйте свойство [**Date**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.date.aspx), чтобы получить или установить выбранную дату. По умолчанию для свойства Date используется значение **null**. Когда пользователь выбирает дату в представлении календаря, это свойство обновляется. Пользователь может очистить дату, щелкнув выбранную дату в представлении календаря, чтобы отменить выбор. 

В коде дату можно задать, как показано ниже.

```csharp
myCalendarDatePicker.Date = new DateTime(1977, 1, 5);
```

Если задать Date в коде, значение будет ограничено свойствами [**MinDate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.mindate.aspx) и [**MaxDate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.maxdate.aspx).
- Если значение **Date** меньше значения **MinDate**, будет использоваться **MinDate**.
- Если значение **Date** больше значения **MaxDate**, будет использоваться **MaxDate**.

Можно обработать событие [**DateChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.datechanged.aspx), чтобы получать уведомление при изменении значения Date.

> **Примечание.**
            &nbsp;&nbsp;Важные сведения о значениях даты см. в разделе [Значения DateTime и Calendar](date-and-time.md#datetime-and-calendar-values) статьи «Элементы управления датой и временем».

### Настройка текста заголовка и замещающего текста

К элелементу выбора даты в календаре можно добавить [**Header**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.header.aspx) (или метку) и [**PlaceholderText**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.placeholdertext.aspx) (или водяной знак), чтобы пользователь понимал, для чего он используется. Чтобы настроить оформление заголовка, задайте свойство [**HeaderTemplate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.headertemplate.aspx) вместо Header.

По умолчанию в качестве замещающего текста используется фраза "Выберите дату". Вы можете удалить эту фразу, установив для свойства PlaceholderText пустую строку, или написать собственный текст, как показано ниже.

```xaml
<CalendarDatePicker x:Name="arrivalCalendarDatePicker" Header="Arrival date" PlaceholderText="Choose your arrival date"/>
```


## Связанные статьи

- [Элементы управления датой и временем](date-and-time.md)
- [Представление календаря](calendar-view.md)
- [Управляющий элемент выбора даты](date-picker.md)
- [Управляющий элемент выбора времени](time-picker.md)



<!--HONumber=Jun16_HO3-->


