---
author: Jwmsft
Description: "Элемент выбора даты — это стандартизованный способ, позволяющий пользователям выбирать локализованное значение даты с помощью сенсорного ввода, мыши или клавиатуры."
title: "Управляющий элемент выбора даты"
ms.assetid: d4a01425-4dee-4de3-9a05-3e85c3fc03cb
isNew: True
label: Date picker
template: detail.hbs
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 114e91276ee4f080ead825b655fc05fcf460bacb
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="date-picker"></a>Управляющий элемент выбора даты

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Элемент выбора даты — это стандартизованный способ, позволяющий пользователям выбирать локализованное значение даты с помощью сенсорного ввода, мыши или клавиатуры. 

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Класс DatePicker**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.datepicker.aspx)</li>
<li>[**Свойство Date**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.datepicker.date.aspx) </li>

</ul>
</div>


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления
Используйте элемент выбора даты, чтобы пользователь мог выбрать известную дату, например дату рождения, если контекст календаря не важен.

Дополнительные сведения о выборе правильного элемента выбора даты см. в статье [Элементы управления датой и временем](date-and-time.md).

## <a name="examples"></a>Примеры

Точка входа отображает выбранную дату, и при выборе пользователем точки входа поверхность выбора разворачивается вертикально от середины, чтобы пользователь мог сделать выбор. Элемент выбора даты накладывается на другие элементы пользовательского интерфейса, не вытесняя их.

![Пример разворачивания элемента выбора даты](images/controls_datepicker_expand.png)

## <a name="create-a-date-picker"></a>Создание элемента выбора даты

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

> **Примечание.**&nbsp;&nbsp;Важные сведения о значениях даты см. в разделе [Значения DateTime и Calendar](date-and-time.md#datetime-and-calendar-values) статьи "Элементы управления датой и временем".



## <a name="related-articles"></a>Еще по теме

- [Элементы управления датой и временем](date-and-time.md)
- [Управляющий элемент выбора даты в календаре](calendar-date-picker.md)
- [Представление календаря](calendar-view.md)
- [Элемент выбора времени](time-picker.md)
