---
author: Jwmsft
Description: "Используйте метку, чтобы показать пользователю, что он должен ввести в данный элемент управления. Также можно присвоить метку группе связанных элементов управления или показать текст рекомендации рядом с группой связанных элементов управления."
title: "Метки"
ms.assetid: CFACCCD4-749F-43FB-947E-2591AE673804
label: Labels
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: eb6744968a4bf06a3766c45b73b428ad690edc06
ms.openlocfilehash: aa605b3862c33633904822bb88de7e647a2b3d5b

---
# Метки

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Метка— это имя или название элемента управления или группы связанных элементов.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>Свойство Header</li>
<li><a href="https://msdn.microsoft.com/library/windows/apps/br209652"><strong>Класс TextBlock</strong></a></li>
</ul>

</div>
</div>






В XAML встроенное свойство заголовка, с помощью которого задаются метки, доступно во многих элементах управления. Для элементов управления, не имеющих свойства заголовка, а также для того чтобы присвоить метку группе элементов управления, можно использовать [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652).


## Пример


![Снимок экрана, иллюстрирующий стандартный элемент управления «Метка»](images/label-standard.png)

## Рекомендации


-   Используйте метку, чтобы показать пользователю, что он должен ввести в данный элемент управления. Также можно присвоить метку группе связанных элементов управления или показать текст рекомендации рядом с группой связанных элементов управления.
-   Когда вы присваиваете метку элементам управления, пишите ее в виде существительного или краткой именной конструкции, а не в виде предложения или текста рекомендации. Избегайте двоеточия и других знаков препинания.
-   При наличии текста рекомендаций в метке можно не волноваться по поводу длины текстовой строки и использовать пунктуацию.

## Связанные разделы
* [Элементы управления текстом](text-controls.md)

**Для разработчиков**
* [**Свойство TextBox.Header**](https://msdn.microsoft.com/library/windows/apps/dn252861)
* [**Свойство PasswordBox.Header**](https://msdn.microsoft.com/library/windows/apps/dn299051)
* [**Свойство ToggleSwitch.Header**](https://msdn.microsoft.com/library/windows/apps/br209713)
* [**Свойство DatePicker.Header**](https://msdn.microsoft.com/library/windows/apps/dn279460)
* [**Свойство TimePicker.Header**](https://msdn.microsoft.com/library/windows/apps/dn299286)
* [**Свойство Slider.Header**](https://msdn.microsoft.com/library/windows/apps/dn252829)
* [**Свойство ComboBox.Header**](https://msdn.microsoft.com/library/windows/apps/dn279416)
* [**Свойство RichEditBox.Header**](https://msdn.microsoft.com/library/windows/apps/dn252726)
* [**Класс TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652)

 

 







<!--HONumber=Aug16_HO3-->


