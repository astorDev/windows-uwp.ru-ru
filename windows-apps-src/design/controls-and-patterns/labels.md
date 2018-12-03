---
Description: Use a label to indicate to the user what they should enter into an adjacent control. You can also label a group of related controls, or display instructional text near a group of related controls.
title: Метки
ms.assetid: CFACCCD4-749F-43FB-947E-2591AE673804
label: Labels
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 4345daf5b879fed7ba9805e4a448c473299031d7
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8460854"
---
# <a name="labels"></a>Метки

 

Метка— это имя или название элемента управления или группы связанных элементов.

> **Важные API-интерфейсы**: свойство Header, [класс TextBlock](https://msdn.microsoft.com/library/windows/apps/br209652)

В XAML встроенное свойство заголовка, с помощью которого задаются метки, доступно во многих элементах управления. Для элементов управления, не имеющих свойства заголовка, а также для того, чтобы присвоить метку группе элементов управления, можно использовать [TextBlock](https://msdn.microsoft.com/library/windows/apps/br209652).

![Снимок экрана, иллюстрирующий стандартный элемент управления «Метка»](images/label-standard.png)

## <a name="recommendations"></a>Рекомендации


-   Используйте метку, чтобы показать пользователю, что он должен ввести в данный элемент управления. Также можно присвоить метку группе связанных элементов управления или показать текст рекомендации рядом с группой связанных элементов управления.
-   Когда вы присваиваете метку элементам управления, пишите ее в виде существительного или краткой именной конструкции, а не в виде предложения или текста рекомендации. Избегайте двоеточия и других знаков препинания.
-   При наличии текста рекомендаций в метке можно не волноваться насчет длины текстовой строки и использовать пунктуацию.


## <a name="get-the-sample-code"></a>Получить пример кода
* [Основы создания пользовательского интерфейса XAML](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/XamlUIBasics)

## <a name="related-topics"></a>Еще по теме
* [Элементы управления текстом](text-controls.md)
* [Свойство TextBox.Header](https://msdn.microsoft.com/library/windows/apps/dn252861)
* [Свойство PasswordBox.Header](https://msdn.microsoft.com/library/windows/apps/dn299051)
* [Свойство ToggleSwitch.Header](https://msdn.microsoft.com/library/windows/apps/br209713)
* [Свойство DatePicker.Header](https://msdn.microsoft.com/library/windows/apps/dn279460)
* [Свойство TimePicker.Header](https://msdn.microsoft.com/library/windows/apps/dn299286)
* [Свойство Slider.Header](https://msdn.microsoft.com/library/windows/apps/dn252829)
* [Свойство ComboBox.Header](https://msdn.microsoft.com/library/windows/apps/dn279416)
* [Свойство RichEditBox.Header](https://msdn.microsoft.com/library/windows/apps/dn252726)
* [Класс TextBlock](https://msdn.microsoft.com/library/windows/apps/br209652)

 

 




