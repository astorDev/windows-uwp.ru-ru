---
author: Jwmsft
Description: Элемент выбора даты — это стандартизованный способ, позволяющий пользователям выбирать локализованное значение даты с помощью сенсорного ввода, мыши или клавиатуры. 
title: Элемент выбора даты
ms.assetid: d4a01425-4dee-4de3-9a05-3e85c3fc03cb
isNew: true
label: Date picker
template: detail.hbs
---

# Элемент выбора даты

Элемент выбора даты — это стандартизованный способ, позволяющий пользователям выбирать локализованное значение даты с помощью сенсорного ввода, мыши или клавиатуры. 

<span class="sidebar_heading" style="font-weight: bold;">Важные API</span>

-   [**Класс DatePicker**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.datepicker.aspx)
-   [**Свойство Date**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.datepicker.date.aspx)

## Выбор правильного элемента управления
Используйте элемент выбора даты, чтобы пользователь мог выбрать известную дату, например дату рождения, если контекст календаря не важен.

Дополнительные сведения о выборе правильного элемента выбора даты см. в статье [Элементы управления датой и временем](date-and-time.md).

## Примеры

Точка входа отображает выбранную дату, и при выборе пользователем точки входа поверхность выбора разворачивается вертикально от середины, чтобы пользователь мог сделать выбор. Элемент выбора даты накладывается на другие элементы пользовательского интерфейса, не вытесняя их.

![Пример разворачивания элемента выбора даты](images/controls_datepicker_expand.png)

## Создание элемента выбора даты

Этот пример демонстрирует, как создать простой элемент выбора даты с заголовком.

```xaml
<DatePicker x:Name=birthDatePicker Header="Date of birth"/>
```

```csharp
DatePicker birthDatePicker = new DatePicker();
birthDatePicker.Header = "Date of birth";
```

Готовый элемент выбора даты выглядит следующим образом:

![Пример элемента выбора даты](images/date-picker-closed.png)

> **Примечание.**
            &nbsp;&nbsp;Важные сведения о значениях даты см. в разделе [Значения DateTime и Calendar](date-and-time.md#datetime-and-calendar-values) статьи «Элементы управления датой и временем».



## Статьи по теме

- [Элементы управления датой и временем](date-and-time.md)
- [Элемент выбора даты в календаре](calendar-date-picker.md)
- [Представление календаря](calendar-view.md)
- [Элемент выбора времени](time-picker.md)


<!--HONumber=May16_HO2-->


