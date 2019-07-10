---
Description: Управляющий элемент выбора даты в календаре — это раскрывающийся элемент управления, оптимизированный для выбора отдельной даты в представлении календаря, когда важна контекстная информация, например день недели или заполненность календаря.
title: Управляющий элемент выбора даты в календаре
ms.assetid: 9e0213e0-046a-4906-ba86-0b49be51ca99
label: Calendar date picker
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: ksulliv
dev-contact: joyate
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 4de2f1cefc47e8740bfebbe7853ae317d25ab9d0
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66363222"
---
# <a name="calendar-date-picker"></a>Управляющий элемент выбора даты в календаре

 

Управляющий элемент выбора даты в календаре — это раскрывающийся элемент управления, оптимизированный для выбора отдельной даты в представлении календаря, когда важна контекстная информация, например день недели или заполненность календаря. Вы можете изменить календарь таким образом, чтобы обеспечить дополнительный контекст или ограничить доступные даты.

> **Важные API**: [класс CalendarDatePicker](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarDatePicker), [свойство Date](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.date), [событие DateChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.datechanged)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления
Используйте **управляющий элемент выбора даты в календаре**, чтобы дать пользователю возможность выбрать одну дату в контекстном представлении календаря. Используйте этот элемент управления для таких действий, как выбор даты встречи или отъезда.

Чтобы пользователь мог выбрать известную дату, например дату рождения, если контекст календаря не важен, можно использовать [управляющий элемент выбора даты](date-picker.md).

Дополнительные сведения о выборе правильного элемента см. в статье [Элементы управления датой и временем](date-and-time.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/CalendarDatePicker">открыть приложение и увидеть CalendarDatePicker в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
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

Управляющий элемент выбора даты в календаре содержит внутренний элемент [CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) для выбора даты. В CalendarDatePicker доступен набор свойств CalendarView (например, [IsTodayHighlighted](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.istodayhighlighted) и [FirstDayOfWeek](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.firstdayofweek)), которые перенаправляются во внутренний элемент CalendarView для редактирования. 

Но изменить [SelectionMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendarview.selectionmode) внутреннего элемента CalendarView, чтобы разрешить множественный выбор, нельзя. Если требуется, чтобы пользователь мог выбрать несколько дат или чтобы календарь всегда отображался, можно использовать представление календаря вместо элемента выбора даты в календаре. Более подробные сведения о том, как можно изменить отображение календаря, см. в статье [Представление календаря](calendar-view.md).

### <a name="selecting-dates"></a>Выбора дат

Используйте свойство [Date](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.date), чтобы получить или установить выбранную дату. По умолчанию для свойства Date используется значение **null**. Когда пользователь выбирает дату в представлении календаря, это свойство обновляется. Пользователь может очистить дату, щелкнув выбранную дату в представлении календаря, чтобы отменить выбор. 

В коде дату можно задать, как показано ниже.

```csharp
myCalendarDatePicker.Date = new DateTime(1977, 1, 5);
```

Если задать Date в коде, значение будет ограничено свойствами [MinDate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.mindate) и [MaxDate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.maxdate).
- Если значение **Date** меньше значения **MinDate**, будет использоваться **MinDate**.
- Если значение **Date** больше значения **MaxDate**, будет использоваться **MaxDate**.

Можно обработать событие [DateChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.datechanged) так, чтобы получать уведомление при изменении значения Date.

> [!NOTE]
> Важные сведения о значениях даты см. в разделе [Значения DateTime и Calendar](date-and-time.md#datetime-and-calendar-values) статьи "Элементы управления датой и временем".

### <a name="setting-a-header-and-placeholder-text"></a>Настройка текста заголовка и замещающего текста

К элементу выбора даты в календаре можно добавить [Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.header) (или метку) и [PlaceholderText](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.placeholdertext) (или водяной знак), чтобы пользователь понимал, для чего он используется. Чтобы настроить оформление заголовка, задайте свойство [HeaderTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.calendardatepicker.headertemplate) вместо Header.

По умолчанию в качестве замещающего текста используется фраза "Выберите дату". Вы можете удалить эту фразу, установив для свойства PlaceholderText пустую строку, или написать собственный текст, как показано ниже.

```xaml
<CalendarDatePicker x:Name="arrivalCalendarDatePicker" Header="Arrival date" 
                    PlaceholderText="Choose your arrival date"/>
```

## <a name="get-the-sample-code"></a>Получить пример кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Связанные статьи

- [Элементы управления датой и временем](date-and-time.md)
- [Представление календаря](calendar-view.md)
- [Выбор даты](date-picker.md)
- [Выбор времени](time-picker.md)
