---
Description: Элементы управления датой и временем позволяют просматривать и устанавливать дату и время. Эта статья содержит рекомендации по проектированию и помогает выбрать соответствующий элемент управления.
title: Рекомендации по элементам управления датой и временем
ms.assetid: 4641FFBB-8D82-4290-94C1-D87617997F61
label: Calendar, date, and time controls
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: ksulliv
dev-contact: joyate
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 0c980acc3b9887dac68712bd65de96e8f3a327a5
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67319065"
---
# <a name="calendar-date-and-time-controls"></a>Элементы управления календарем, датой и временем

 

Элементы управления датой и временем предоставляют пользователю стандартный локализованный способ просмотра и установки даты и времени в приложении. Эта статья содержит рекомендации по проектированию и помогает выбрать соответствующий элемент управления.

> **Важные API**: [класс CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView), [класс CalendarDatePicker](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarDatePicker), [класс DatePicker](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker) и [класс TimePicker](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker)

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/category/DataInput">открыть приложение и увидеть эти элементы управления в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="which-date-or-time-control-should-you-use"></a>Какой элемент управления датой и временем следует использовать?

Существует четыре элемента управления датой и временем. Выбор нужного элемента управления зависит от сценария использования вашего приложения. Эта информация поможет выбрать нужный элемент управления для вашего приложения.

&nbsp;|&nbsp;|&nbsp;                                                                                                                      
--------------------|-------|-------------------------------------------------------------------------------------------------------------------------------
Представление календаря       |![Пример представления календаря](images/controls_calendar_monthview_small.png)|Используется для выбора одной даты или диапазона дат в календаре, который всегда виден.                   
Управляющий элемент выбора даты в календаре|![Пример элемента выбора даты в календаре](images/calendar-date-picker-closed.png)|Используется для выбора одной даты с использованием контекстного календаря. 
Управляющий элемент выбора даты         |![Пример элемента выбора даты](images/date-picker-closed.png)|Используется для выбора одной даты, когда контекстно-зависимая информация не важна.
Управляющий элемент выбора времени         |![Пример элемента выбора времени](images/time-picker-closed.png)|Используется для выбора одного значения времени.                                        

<!-- This table seems redundant, not sure it's needed.-->

### <a name="calendar-view"></a>Представление календаря

**CalendarView** позволяет пользователю просматривать календарь и взаимодействовать с ним, перемещаясь по месяцам, годам и десятилетиям. Пользователь может выбрать отдельную дату или диапазон дат. Не имеет поверхности выбора, и календарь всегда виден.

Представление календаря состоит из 3 отдельных представлений: представление месяца, представление года и представление десятилетия. По умолчанию он отображается с открытым представлением месяца, но в качестве начального представления можно задать любое из его представлений.

![Пример элемента выбора даты в календаре](images/calendar-view-3-views.png)

- Если необходимо предоставить пользователю возможность выбора нескольких дат, следует использовать **CalendarView**.
- Если необходимо предоставить пользователю возможность выбора одной даты и при этом не нужно, чтобы календарь был виден всегда, рекомендуется использовать элементы управления **CalendarDatePicker** или **DatePicker**.

### <a name="calendar-date-picker"></a>Управляющий элемент выбора даты в календаре

**CalendarDatePicker** — это раскрывающийся элемент управления, оптимизированный для выбора отдельной даты из представления календаря, когда важен контекст, например день недели или заполнение календаря. Вы можете изменить календарь таким образом, чтобы обеспечить дополнительный контекст или ограничить доступные даты.

Точка входа отображает замещающий текст, если дата не была установлена; в противном случае отображается выбранная дата. Когда пользователь выбирает точку входа, представление календаря развертывается, чтобы пользователь мог выбрать дату. Представление календаря перекрывает другие элементы пользовательского интерфейса, но не вытесняет их.

![Пример элемента выбора даты в календаре](images/calendar-date-picker-2-views.png)

- Используйте элемент выбора даты в календаре для таких действий, как выбор даты встречи или отъезда. 

### <a name="date-picker"></a>Управляющий элемент выбора даты

Элемент управления **DatePicker** представляет собой стандартизированное средство выбора нужной даты. 

Точка входа отображает выбранную дату, и при выборе пользователем точки входа поверхность выбора разворачивается вертикально от середины, чтобы пользователь мог сделать выбор. Элемент выбора даты накладывается на другие элементы пользовательского интерфейса, не вытесняя их.

![Пример разворачивания элемента выбора даты](images/controls_datepicker_expand.png)

- Используйте элемент выбора даты, чтобы пользователь мог выбрать известную дату, например дату рождения, если контекст календаря не важен.

### <a name="time-picker"></a>Управляющий элемент выбора времени

Элемент управления **TimePicker** используется для выбора одного значения времени (например, времени встречи или времени отъезда). Это статический отображаемый элемент, задаваемый пользователем или посредством программного кода. Он не обновляется для отображения текущего времени. 

Точка входа отображает выбранное время, и при выборе пользователем точки входа поверхность выбора разворачивается вертикально из середины, чтобы пользователь мог сделать выбор. Элемент выбора времени перекрывает другие элементы пользовательского интерфейса, но не вытесняет их.

![Пример разворачивания элемента выбора времени](images/controls_timepicker_expand.png)

- Используйте элемент выбора времени, чтобы пользователь мог выбрать одно значение времени.

## <a name="create-a-date-or-time-control"></a>Создание элемента управления датой и временем

Сведения и примеры, связанные с каждым элементом управления датой и временем см. в следующих статьях.

- [Представление календаря](calendar-view.md)
- [Выбор даты в календаре](calendar-date-picker.md)
- [Выбор даты](date-picker.md)
- [Управляющий элемент выбора времени](time-picker.md)

### <a name="globalization"></a>Глобализация

Элементы управления датами XAML поддерживает все календарные системы, поддерживаемые Windows. Эти календари определены в классе [Windows.Globalization.CalendarIdentifiers](https://docs.microsoft.com/uwp/api/Windows.Globalization.CalendarIdentifiers). Каждый элемент управления использует календарь, соответствующий языку по умолчанию, используемому в приложении. Кроме того, вы можете задать значение свойства **CalendarIdentifier**, чтобы использовать конкретную календарную систему.

Элемент управления "Выбор времени" поддерживает все системы часов, определенные в классе [Windows.Globalization.ClockIdentifiers](https://docs.microsoft.com/uwp/api/Windows.Globalization.ClockIdentifiers). Вы можете задать значение свойства [ClockIdentifier](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.timepicker.clockidentifier), чтобы использовать 12- или 24-часовой формат отображения времени. Значение свойства имеет строковый тип, но следует использовать значения, соответствующие статическим строковым свойствам класса ClockIdentifiers. Эти способы перечислены ниже. TwelveHour (строка 12HourClock) и TwentyFourHour (строка 24HourClock). По умолчанию используется значение 12HourClock.


### <a name="datetime-and-calendar-values"></a>Значения DateTime и Calendar

Объекты даты, используемые в элементах управления выбором даты и времени в XAML, имеют другое представление в зависимости от применяемого вами языка программирования. 
- Языки C# и Visual Basic используют структуру [System.DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?redirectedfrom=MSDN), которая предоставляется в .NET. 
- Языки C++ и CX используют структуру [Windows::Foundation::DateTime](https://docs.microsoft.com/windows/desktop/api/windows.foundation/ns-windows-foundation-datetime). 

С этим также связан класс Calendar, который влияет на интерпретацию дат в контексте. Все приложения среды выполнения Windows могут использовать класс [Windows.Globalization.Calendar](https://docs.microsoft.com/uwp/api/Windows.Globalization.Calendar). Приложения на C# и Visual Basic могут также использовать класс [System.Globalization.Calendar](https://docs.microsoft.com/dotnet/api/system.globalization.calendar?redirectedfrom=MSDN) с очень похожими функциями. (Приложения среды выполнения Windows могут использовать базовый класс .NET Calendar, но не определенные реализации; например GregorianCalendar.)

.NET также поддерживает тип [DateTime](https://docs.microsoft.com/dotnet/api/system.datetime?redirectedfrom=MSDN), который неявно преобразуется в [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?redirectedfrom=MSDN). Вы могли видеть тип DateTime, который используется в коде .NET для задания значений, которые на самом деле имеют тип DateTimeOffset. См. подробнее об отличиях между типами DateTime и DateTimeOffset в разделе "Примечания" в описании класса [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?redirectedfrom=MSDN).

> **Примечание**&nbsp;&nbsp;Свойствам, которые принимают объекты даты в качестве значений, нельзя присвоить строку атрибута XAML, так как анализатор XAML среды выполнения Windows не содержит логику преобразования строк в даты в виде объектов DateTime или DateTimeOffset. Эти значения обычно задаются в коде. Также можно использовать другой способ — определить дату, которая доступна как объект данных, или дату, доступную в контексте данных. Затем необходимо задать свойство как атрибут XAML, ссылающийся на выражение [расширения разметки \{Binding\}](../../xaml-platform/binding-markup-extension.md), которое может получать доступ к дате как к данным.

## <a name="get-the-sample-code"></a>Получить пример кода
* [Пример основных элементов пользовательского интерфейса XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics)


## <a name="related-topics"></a>Статьи по теме

**Разработчикам (XAML)**
- [Класс CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView)
- [Класс CalendarDatePicker](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarDatePicker)
- [Класс DatePicker](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker)
- [Класс TimePicker](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker)
