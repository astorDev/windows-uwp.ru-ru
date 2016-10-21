---
author: Jwmsft
Description: "Применение элемента управления &quot;Выбор времени&quot; — это стандартизованный способ дать пользователям возможность выбирать значение времени с помощью сенсорного ввода, мыши или клавиатуры."
title: "Управляющий элемент выбора времени"
ms.assetid: 5124ecda-09e6-449e-9d4a-d969dca46aa3
label: Time picker
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: eb6744968a4bf06a3766c45b73b428ad690edc06
ms.openlocfilehash: 69f682b0edddbcf88515af537c33b3d8297f91f0

---
# Управляющий элемент выбора времени
<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Применение элемента управления "Выбор времени" — это стандартизованный способ дать пользователям возможность выбирать значение времени с помощью сенсорного ввода, мыши или клавиатуры. 

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.aspx"><strong>Класс TimePicker</strong></a></li>
<li><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.time.aspx"><strong>Свойство Time</strong></a></li>
</ul>

</div>
</div>




## Выбор правильного элемента управления
Используйте элемент выбора времени, чтобы пользователь мог выбрать одно значение времени.

Дополнительные сведения о выборе правильного элемента см. в статье [Элементы управления датой и временем](date-and-time.md).

## Примеры:

Точка входа отображает выбранное время, и при выборе пользователем точки входа поверхность выбора разворачивается вертикально из середины, чтобы пользователь мог сделать выбор. Элемент выбора времени перекрывает другие элементы пользовательского интерфейса, но не вытесняет их.

![Пример разворачивания элемента выбора времени](images/controls_timepicker_expand.png)

## Создание элемента выбора времени

Этот пример демонстрирует, как создать простой элемент выбора времени с заголовком.

```xaml
<TimePicker x:Name=arrivalTimePicker Header="Arrival time"/>
```

```csharp
TimePicker arrivalTimePicker = new TimePicker();
arrivalTimePicker.Header = "Arrival time";
```

Готовый элемент выбора времени выглядит следующим образом:

![Пример элемента выбора времени](images/time-picker-closed.png)

> **Примечание.**&nbsp;&nbsp;Важные сведения о значениях даты и времени см. в разделе [Значения DateTime и Calendar](date-and-time.md#datetime-and-calendar-values) статьи *«Элементы управления датой и временем»*.



## Ссылки по теме

- [Элементы управления датой и временем](date-and-time.md)
- [Управляющий элемент выбора даты в календаре](calendar-date-picker.md)
- [Представление календаря](calendar-view.md)
- [Элемент выбора даты](date-picker.md)



<!--HONumber=Aug16_HO3-->


