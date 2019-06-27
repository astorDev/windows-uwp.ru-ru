---
Description: Элемент выбора даты — это стандартизованный способ, позволяющий пользователям выбирать локализованное значение даты с помощью сенсорного ввода, мыши или клавиатуры.
title: Управляющий элемент выбора даты
ms.assetid: d4a01425-4dee-4de3-9a05-3e85c3fc03cb
isNew: true
label: Date picker
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: ksulliv
dev-contact: joyate
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 7e0a7d1732833f8e6fc750f8ee481fa3c1116b50
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66823597"
---
# <a name="date-picker"></a>Управляющий элемент выбора даты

 

Элемент выбора даты — это стандартизованный способ, позволяющий пользователям выбирать локализованное значение даты с помощью сенсорного ввода, мыши или клавиатуры. 

> **Важные API**: [класс DatePicker](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker), [свойство Date](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.datepicker.date)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления
Используйте элемент выбора даты, чтобы пользователь мог выбрать известную дату, например дату рождения, если контекст календаря не важен.

Дополнительные сведения о выборе правильного элемента выбора даты см. в статье [Элементы управления датой и временем](date-and-time.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/DatePicker">открыть приложение и увидеть DatePicker в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Точка входа отображает выбранную дату, и при выборе пользователем точки входа поверхность выбора разворачивается вертикально от середины, чтобы пользователь мог сделать выбор. Элемент выбора даты накладывается на другие элементы пользовательского интерфейса, не вытесняя их.

![Пример разворачивания элемента выбора даты](images/controls_datepicker_expand.png)

## <a name="create-a-date-picker"></a>Создание элемента выбора даты

Этот пример демонстрирует, как создать простой элемент выбора даты с заголовком.

```xaml
<DatePicker x:Name="birthDatePicker" Header="Date of birth"/>
```

```csharp
DatePicker birthDatePicker = new DatePicker();
birthDatePicker.Header = "Date of birth";
```

Готовый элемент выбора даты выглядит следующим образом:

![Пример элемента выбора даты](images/date-picker-closed.png)

> **Примечание.** &nbsp;&nbsp;Важные сведения о значениях даты см. в разделе [Значения DateTime и Calendar](date-and-time.md#datetime-and-calendar-values) статьи "Элементы управления датой и временем".

## <a name="get-the-sample-code"></a>Получить пример кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Связанные статьи

- [Элементы управления датой и временем](date-and-time.md)
- [Выбор даты в календаре](calendar-date-picker.md)
- [Представление календаря](calendar-view.md)
- [Выбор времени](time-picker.md)
