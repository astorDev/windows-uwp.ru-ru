---
Description: Используйте метку, чтобы показать пользователю, что он должен ввести в данный элемент управления. Также можно присвоить метку группе связанных элементов управления или показать текст рекомендации рядом с группой связанных элементов управления.
title: Метки
ms.assetid: CFACCCD4-749F-43FB-947E-2591AE673804
label: Labels
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 2b642f1d6c3f2a04bacdf293858492ea095af1a8
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67319521"
---
# <a name="labels"></a>Метки

 

Метка — это имя или название элемента управления или группы связанных элементов.

> **Важные API**: свойство Header, [класс TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock).

В XAML встроенное свойство заголовка, с помощью которого задаются метки, доступно во многих элементах управления. Для элементов управления, не имеющих свойства заголовка, а также для того, чтобы присвоить метку группе элементов управления, можно использовать [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock).

![Снимок экрана, иллюстрирующий стандартный элемент управления «Метка»](images/label-standard.png)

## <a name="recommendations"></a>Рекомендации


-   Используйте метку, чтобы показать пользователю, что он должен ввести в данный элемент управления. Также можно присвоить метку группе связанных элементов управления или показать текст рекомендации рядом с группой связанных элементов управления.
-   Когда вы присваиваете метку элементам управления, пишите ее в виде существительного или краткой именной конструкции, а не в виде предложения или текста рекомендации. Избегайте двоеточия и других знаков препинания.
-   При наличии текста рекомендаций в метке можно не волноваться насчет длины текстовой строки и использовать пунктуацию.


## <a name="get-the-sample-code"></a>Получить пример кода
* [Пример основных элементов пользовательского интерфейса XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics)

## <a name="related-topics"></a>Статьи по теме
* [Текстовые элементы управления](text-controls.md)
* [Свойство TextBox.Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.header)
* [Свойство PasswordBox.Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.header)
* [Свойство ToggleSwitch.Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch.header)
* [Свойство DatePicker.Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.datepicker.header)
* [Свойство TimePicker.Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.timepicker.header)
* [Свойство Slider.Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.slider.header)
* [Свойство ComboBox.Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.combobox.header)
* [Свойство RichEditBox.Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox.header)
* [Класс TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock)

 

 




