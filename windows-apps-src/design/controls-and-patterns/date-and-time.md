---
Description: Date and time controls let you view and set the date and time. This article provides design guidelines and helps you pick the right control.
title: Рекомендации по элементам управления датой и временем
ms.assetid: 4641FFBB-8D82-4290-94C1-D87617997F61
label: Calendar, date, and time controls
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: ksulliv
dev-contact: joyate
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: f65ed68db51ea173dfec3c06a9dc81a7f7735afd
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8944213"
---
# <a name="calendar-date-and-time-controls"></a>Элементы управления календарем, датой и временем

 

Элементы управления датой и временем предоставляют пользователю стандартный локализованный способ просмотра и установки даты и времени в приложении. Эта статья содержит рекомендации по проектированию и помогает выбрать соответствующий элемент управления.

> **Важные API-интерфейсы**: [класс CalendarView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendarview.aspx), [класс CalendarDatePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.calendardatepicker.aspx), [класс DatePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.datepicker.aspx), [класс TimePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.aspx)

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/category/DataInput">открыть приложение и увидеть эти элементы управления в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="which-date-or-time-control-should-you-use"></a>Какой элемент управления датой и временем следует использовать?

Существует четыре элемента управления датой и временем. Выбор нужного элемента управления зависит от сценария использования вашего приложения. Эта информация поможет выбрать нужный элемент управления для вашего приложения.

&nbsp;|&nbsp;|&nbsp;                                                                                                                      
--------------------|-------|-------------------------------------------------------------------------------------------------------------------------------
Представление календаря       |![Пример представления календаря](images/controls_calendar_monthview_small.png)|Используется для выбора одной даты или диапазона дат в календаре, который всегда виден.                   
Элемент выбора даты в календаре|![Пример элемента выбора даты в календаре](images/calendar-date-picker-closed.png)|Используется для выбора одной даты с использованием контекстного календаря. 
Управляющий элемент выбора даты         |![Пример элемента выбора даты](images/date-picker-closed.png)|Используется для выбора одной даты, когда контекстно-зависимая информация не важна.
Элемент выбора времени         |![Пример элемента выбора времени](images/time-picker-closed.png)|Используется для выбора одного значения времени.                                        

<!-- This table seems redundant, not sure it's needed.-->

### <a name="calendar-view"></a>Представление календаря

**CalendarView** позволяет пользователю просматривать календарь и взаимодействовать с ним, перемещаясь по месяцам, годам и десятилетиям. Пользователь может выбрать отдельную дату или диапазон дат. Не имеет поверхности выбора, и календарь всегда виден.

Представление календаря состоит из 3 отдельных представлений: представление месяца, представление года и представление десятилетия. По умолчанию он отображается с открытым представлением месяца, но в качестве начального представления можно задать любое из его представлений.

![Пример элемента выбора даты в календаре](images/calendar-view-3-views.png)

- Если необходимо предоставить пользователю возможность выбора нескольких дат, следует использовать **CalendarView**.
- Если необходимо предоставить пользователю возможность выбора одной даты и при этом не нужно, чтобы календарь был виден всегда, рекомендуется использовать элементы управления **CalendarDatePicker** или **DatePicker**.

### <a name="calendar-date-picker"></a>Элемент выбора даты в календаре

**CalendarDatePicker** — это раскрывающийся элемент управления, оптимизированный для выбора отдельной даты из представления календаря, когда важен контекст, например день недели или заполненность календаря. Вы можете изменить календарь таким образом, чтобы обеспечить дополнительный контекст или ограничить доступные даты.

Точка входа отображает замещающий текст, если дата не была установлена; в противном случае отображается выбранная дата. Когда пользователь выбирает точку входа, представление календаря развертывается, чтобы пользователь мог выбрать дату. Представление календаря перекрывает другие элементы пользовательского интерфейса, но не вытесняет их.

![Пример элемента выбора даты в календаре](images/calendar-date-picker-2-views.png)

- Используйте элемент выбора даты в календаре для таких действий, как выбор даты встречи или отъезда. 

### <a name="date-picker"></a>Управляющий элемент выбора даты

Элемент управления **DatePicker** представляет собой стандартизированное средство выбора нужной даты. 

Точка входа отображает выбранную дату, и при выборе пользователем точки входа поверхность выбора разворачивается вертикально от середины, чтобы пользователь мог сделать выбор. Элемент выбора даты накладывается на другие элементы пользовательского интерфейса, не вытесняя их.

![Пример разворачивания элемента выбора даты](images/controls_datepicker_expand.png)

- Используйте элемент выбора даты, чтобы пользователь мог выбрать известную дату, например дату рождения, если контекст календаря не важен.

### <a name="time-picker"></a>Элемент выбора времени

Элемент управления **TimePicker** используется для выбора одного значения времени (например, времени встречи или времени отъезда). Это статический отображаемый элемент, задаваемый пользователем или посредством программного кода. Он не обновляется для отображения текущего времени. 

Точка входа отображает выбранное время, и при выборе пользователем точки входа поверхность выбора разворачивается вертикально из середины, чтобы пользователь мог сделать выбор. Элемент выбора времени перекрывает другие элементы пользовательского интерфейса, но не вытесняет их.

![Пример разворачивания элемента выбора времени](images/controls_timepicker_expand.png)

- Используйте элемент выбора времени, чтобы пользователь мог выбрать одно значение времени.

## <a name="create-a-date-or-time-control"></a>Создание элемента управления датой и временем

Сведения и примеры, связанные с каждым элементом управления датой и временем см. в следующих статьях.

- [Представление календаря](calendar-view.md)
- [Элемент выбора даты в календаре](calendar-date-picker.md)
- [Управляющий элемент выбора даты](date-picker.md)
- [Элемент выбора времени](time-picker.md)

### <a name="globalization"></a>Глобализация

Элементы управления датами XAML поддерживает все календарные системы, поддерживаемые Windows. Эти календари определены в классе [Windows.Globalization.CalendarIdentifiers](https://msdn.microsoft.com/library/windows/apps/xaml/windows.globalization.calendaridentifiers.aspx). Каждый элемент управления использует календарь, соответствующий языку по умолчанию, используемому в приложении. Кроме того, вы можете задать значение свойства **CalendarIdentifier**, чтобы использовать конкретную календарную систему.

Элемент управления "Выбор времени" поддерживает все системы часов, определенные в классе [Windows.Globalization.ClockIdentifiers](https://msdn.microsoft.com/library/windows/apps/xaml/windows.globalization.clockidentifiers.aspx). Вы можете задать значение свойства [ClockIdentifier](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.clockidentifier.aspx), чтобы использовать 12- либо 24-часовой формат отображения времени. Значение свойства имеет строковый тип, но следует использовать значения, соответствующие статическим строковым свойствам класса ClockIdentifiers. А именно: TwelveHour (строка 12HourClock) и TwentyFourHour (строка 24HourClock). По умолчанию используется значение 12HourClock.


### <a name="datetime-and-calendar-values"></a>Значения DateTime и Calendar

Объекты даты, используемые в элементах управления выбором даты и времени в XAML, имеют другое представление в зависимости от применяемого вами языка программирования. 
- Языки C# и Visual Basic используют структуру [System.DateTimeOffset](https://msdn.microsoft.com/library/windows/apps/xaml/system.datetimeoffset.aspx), которая входит в платформу .NET. 
- Языки C++ и CX используют структуру [Windows::Foundation::DateTime](https://msdn.microsoft.com/library/windows/apps/xaml/br205770.aspx). 

С этим также связан класс Calendar, который влияет на интерпретацию дат в контексте. Все приложения среды выполнения Windows могут использовать класс [Windows.Globalization.Calendar](https://msdn.microsoft.com/library/windows/apps/xaml/windows.globalization.calendar.aspx). Приложения на C# и Visual Basic могут также использовать класс [System.Globalization.Calendar](https://msdn.microsoft.com/library/windows/apps/xaml/system.globalization.calendar.aspx), который имеет очень похожий функционал. (Приложения среды выполнения Windows могут использовать базовый класс .NET Calendar, но не определенные реализации; например GregorianCalendar.)

.NET также поддерживает тип под названием [DateTime](https://msdn.microsoft.com/library/windows/apps/xaml/system.datetime.aspx), который неявно преобразуется в [DateTimeOffset](https://msdn.microsoft.com/library/windows/apps/xaml/system.datetimeoffset.aspx). Вы могли видеть тип DateTime, который используется в коде .NET для задания значений, которые на самом деле имеют тип DateTimeOffset. Подробнее о различиях между типами DateTime и DateTimeOffset см. в разделе примечаний в описании класса [DateTimeOffset](https://msdn.microsoft.com/library/windows/apps/xaml/system.datetimeoffset.aspx).

> **Примечание.**&nbsp;&nbsp;Свойствам, принимающим в качестве значений объекты даты, нельзя присвоить строку атрибута XAML, поскольку анализатор XAML среды выполнения Windows не содержит логику преобразования строк в даты в виде объектов DateTime или DateTimeOffset. Эти значения обычно задаются в коде. Также можно использовать другой способ — определить дату, которая доступна как объект данных, или дату, доступную в контексте данных. Затем необходимо задать свойство как атрибут XAML, ссылающийся на выражение расширения разметки [\{Binding\}](../../xaml-platform/binding-markup-extension.md), которое может получать доступ к дате как к данным.

## <a name="get-the-sample-code"></a>Получить пример кода
* [Основы создания пользовательского интерфейса XAML](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/XamlUIBasics)


## <a name="related-topics"></a>Еще по теме

**Для разработчиков (XAML)**
- [Класс CalendarView](https://msdn.microsoft.com/library/windows/apps/dn890052)
- [Класс CalendarDatePicker](https://msdn.microsoft.com/library/windows/apps/dn950083)
- [Класс DatePicker](https://msdn.microsoft.com/library/windows/apps/dn298584)
- [Класс TimePicker](https://msdn.microsoft.com/library/windows/apps/dn299280)
