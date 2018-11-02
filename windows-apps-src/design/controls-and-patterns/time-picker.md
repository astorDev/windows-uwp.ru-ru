---
author: muhsinking
Description: The time picker gives you a standardized way to let users pick a time value using touch, mouse, or keyboard input.
title: Выбор времени
ms.assetid: 5124ecda-09e6-449e-9d4a-d969dca46aa3
label: Time picker
template: detail.hbs
ms.author: mukin
ms.date: 05/08/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: ksulliv
dev-contact: joyate
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: c30b310deb509b9abfcd49531f85ea109cb12864
ms.sourcegitcommit: 144f5f127fc4fbd852f2f6780ef26054192d68fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5977715"
---
# <a name="time-picker"></a>Управляющий элемент выбора времени
 

Применение элемента управления "Выбор времени" — это стандартизованный способ дать пользователям возможность выбирать значение времени с помощью сенсорного ввода, мыши или клавиатуры. 

> **Важные API-интерфейсы**: [класс TimePicker](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.aspx), [свойство Time](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.timepicker.time.aspx)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления
Используйте элемент выбора времени, чтобы пользователь мог выбрать одно значение времени.

Дополнительные сведения о выборе правильного элемента см. в статье [Элементы управления датой и временем](date-and-time.md).

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/TimePicker">открыть приложение и увидеть TimePicker в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Точка входа отображает выбранное время, и при выборе пользователем точки входа поверхность выбора разворачивается вертикально из середины, чтобы пользователь мог сделать выбор. Элемент выбора времени перекрывает другие элементы пользовательского интерфейса, но не вытесняет их.

![Пример разворачивания элемента выбора времени](images/controls_timepicker_expand.png)

## <a name="create-a-time-picker"></a>Создание элемента выбора времени

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

> [!NOTE]
> Важные сведения о значениях даты и времени см. в разделе [Значения DateTime и Calendar](date-and-time.md#datetime-and-calendar-values) статьи *Элементы управления датой и временем*.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-topics"></a>Смежные разделы

- [Элементы управления датой и временем](date-and-time.md)
- [Управляющий элемент выбора даты в календаре](calendar-date-picker.md)
- [Представление календаря](calendar-view.md)
- [Элемент выбора даты](date-picker.md)
