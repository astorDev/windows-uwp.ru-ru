---
Description: Использование свойств выравнивания, полей и отбивки для организации макета элементов на странице.
title: Выравнивание, поля и заполнение в макетах
ms.date: 03/19/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 0d7f702d145740703b9fbc4ca2e7fd8eba8957cc
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684465"
---
# <a name="alignment-margin-padding"></a>Выравнивание, поля и отбивка

В приложениях UWP большинство элементов пользовательского интерфейса наследуются от класса [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement). Каждый FrameworkElement имеет свойства размера, выравнивания, полей и отбивки, которые влияют на поведение макета. В разделах ниже представлен обзор способов использования этих свойств макета для того, чтобы пользовательский интерфейс вашего приложения был читаемым и простым в использовании в любом контексте.

## <a name="dimensions-height-width"></a>Измерения (высота, ширина)
Правильная установка размера гарантирует, что все содержимое четко и хорошо читается. Пользователи не должны прибегать к прокрутке или масштабированию для просмотра основного содержимого.

![Схема, на которой показаны размеры](images/dimensions.svg)

- Свойства [**Height**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.height) и [**Width**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.width) определяют размер элемента. По умолчанию используется математическое значение NaN (не число). Можно использовать статические значения, измеренные в [эффективных пикселях](../basics/design-and-ui-intro.md#effective-pixels-and-scaling), функцию **автоматического** выбора размера либо [пропорциональный размер](layout-panels.md#grid) для динамичного поведения.

- Свойства [**ActualHeight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.actualheight) и [**ActualWidth**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.actualwidth) доступны только для чтения и определяют размер элемента во время выполнения. Когда гибкие макеты увеличиваются или уменьшаются, эти значения изменяются в событии [**SizeChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.sizechanged). Обратите внимание, что [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform) не меняет значения ActualHeight и ActualWidth.

- Свойства [**MinWidth**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.minwidth)/[**MaxWidth**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.maxwidth) и [**MinHeight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.minheight)/[**MaxHeight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.maxheight) позволяют определить значения, ограничивающие размер элемента, но в то же время обеспечивают его гибкое изменение.

- [**FontSize**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.fontsize) и другие текстовые свойства позволяют управлять размером макета для текстовых элементов. Хотя текстовые элементы не имеют явно объявленных размеров, у них есть вычисляемые ActualWidth и ActualHeight. 

## <a name="alignment"></a>Соответствие
Выравнивание делает внешний вид пользовательского интерфейса аккуратным, упорядоченным и сбалансированным, а также может использоваться для создания визуальной иерархии и связей между элементами.

![схема, на которой показано выравнивание](images/alignment.svg)

- Свойства [**HorizontalAlignment**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.horizontalalignment) и [**VerticalAlignment**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.verticalalignment) позволяют указать, как элемент должен быть расположен в выделенном родительском контейнере.
    - Значения для **HorizontalAlignment**: **Left**, **Center**, **Right** и **Stretch**.
    - Значения для **VerticalAlignment**: **Top**, **Center**, **Bottom** и **Stretch**.

- Значение **Stretch** используется по умолчанию для обоих свойств, при этом элементы занимают все место в родительском контейнере. Реальные числовые значения Height и Width отменяют значение Stretch, которое будет действовать как значение Center. Некоторые элементы управления, например Button, перезаписывают значение Stretch в своем стиле по умолчанию.

- [**HorizontalContentAlignment**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.horizontalcontentalignment) и [**VerticalContentAlignment**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.verticalcontentalignment) позволяют указать, как дочерние элементы должны быть расположены в родительском контейнере.

- Выравнивание может влиять на обрезку в панели макета. Например, при выравнивании `HorizontalAlignment="Left"` правая часть элемента обрезается, если содержимое больше по размеру, чем ActualWidth.

- Текстовые элементы используют свойство [**TextAlignment**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.textalignment). Мы рекомендуем без необходимости не изменять поведение по умолчанию, то есть выравнивание по левому краю. Дополнительные сведения об оформлении текста см. в [этом разделе](../style/typography.md).

## <a name="margin-and-padding"></a>Поле и заполнение
Свойства полей и отбивки помогают избежать перегруженности или излишней разреженности элементов пользовательского интерфейса, а также упростить использование некоторых типов ввода, таких как перо и сенсорный ввод. На рисунке ниже показаны поля и заполнение для контейнера и его содержимого.

![схема полей и отбивки XAML](images/xaml-layout-margins-padding.svg)

### <a name="margin"></a>Margin
Свойство [**Margin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.margin) позволяет управлять размером пустого пространства вокруг элемента. Margin не добавляет пиксели к ActualHeight и ActualWidth и не считается частью элемента для проверки попадания и порождения событий ввода.

- Значения свойства Margin могут быть универсальными или определенными. Если используется `Margin="20"`, к элементу будет применено универсальное поле в 20 пикселей слева, сверху, справа и снизу. Если используется `Margin="0,10,5,25"`, значения применяются слева, сверху, справа и снизу (в указанном порядке). 

- Поля можно добавить. Если каждый из двух элементов определяет ширину поля в 10 пикселей, и они расположены по соседству в любой ориентации, то расстояние между ними составит 20 пикселей.

- Допустимы отрицательные поля. Однако использование отрицательного поля может вызвать обрезку и наползание соседних элементов друг на друга, поэтому отрицательные поля обычно не используют.

- Значения полей ограничиваются последними, поэтому будьте осторожны с полями, так как контейнеры могут обрезать элементы или ограничивать их размер. Значение Margin может стать причиной того, что элемент не будет отрисовываться, так как одно из его измерений получит значение 0 после применения поля.

### <a name="padding"></a>Заполнение
Свойство [**Padding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.padding) позволяет контролировать объем пространства между внутренними границами элемента и его дочерним содержимым или элементами. Положительное значение Padding уменьшает область содержимого элемента. 

В отличие от Margin свойство Padding не относится к FrameworkElement. Существует несколько классов, определяющих собственное свойство Padding:

-   [**Control.Padding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.padding) наследуется всеми производными классами [**Control**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls). Не во всех элементах управления имеется содержимое, и для таких элементов задание свойства ни на что не влияет. Если элемент управления имеет границу, отбивка применяется внутри этой границы.
-   [**Border.Padding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.border.padding) позволяет определить расстояние между линией прямоугольника, созданной с помощью свойства [**BorderThickness**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.border.borderthickness)/[**BorderBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.border.borderbrush) и элементом [**Child**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.border.child).
-   [**ItemsPresenter.Padding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemspresenter.padding) отвечает за отображение элементов в элементах управления, размещая указанное заполнение вокруг каждого элемента.
-   [**TextBlock.Padding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.padding) и [**RichTextBlock.Padding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richtextblock.padding) увеличивают ограничивающий прямоугольник вокруг текста текстового элемента. Эти текстовые элементы не имеют параметра **Background**, поэтому их визуально трудно заметить. По этой причине для контейнеров [**Block**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.block) лучше использовать параметры [**Margin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.block.margin).

В каждом из этих случаев элементы также имеют свойство Margin. Если свойства Margin и Padding используются одновременно, они дополняют друг друга: очевидное расстояние между внешним контейнером и внутренним содержимым складывается из ширины поля и отбивки.

### <a name="example"></a>Пример
Рассмотрим влияние параметров Margin и Padding на реальные элементы управления. Вот параметр TextBox внутри Grid со значениями параметров Margin и Padding по умолчанию, равными 0.

![TextBox с полем и заполнением 0](images/xaml-layout-textbox-no-margins-padding.svg)

Вот те же самые параметры TextBox и Grid со значениями параметров Margin и Padding в TextBox, как показано в этом коде XAML.

```xaml
<Grid BorderBrush="Blue" BorderThickness="4" Width="200">
    <TextBox Text="This is text in a TextBox." Margin="20" Padding="16,24"/>
</Grid>
```

![Параметр TextBox с положительными значениями поля и заполнения](images/xaml-layout-textbox-with-margins-padding.svg)


## <a name="style-resources"></a>Ресурсы стиля
Вам не придется задавать значения свойства по отдельности для элемента управления. Обычно такой способ более эффективен для группировки значений свойств в ресурс [**Style**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) и применения Style к элементу управления. Это в первую очередь касается случаев, когда необходимо применить одинаковые значения свойства к многочисленным элементам управления. Дополнительные сведения об использовании стилей см. в разделе [Стили XAML](../controls-and-patterns/xaml-styles.md).

## <a name="general-recommendations"></a>Общие рекомендации
- Применяйте значения размеров только к определенным ключевым элементам, а для других элементов используйте поведение гибкого макета. Это обеспечивает [быстрый отклик пользовательского интерфейса](responsive-design.md) при изменении ширины окна.

- При использовании значений размеров **все размеры, поля и отбивки следует устанавливать шагами по 4 эффективных пикселя**. Если на платформе UWP используются [эффективные пиксели и масштабирование](../basics/design-and-ui-intro.md#effective-pixels-and-scaling), чтобы сделать интерфейс вашего приложения разборчивым на всех устройствах и размерах экрана, она масштабирует элементы пользовательского интерфейса в величинах, кратных 4. Использование значений с шагом 4 улучшает качество отрисовки, так как размеры будут иметь целое число пикселей.

- При небольшой ширине окна (менее 640 пикселей) мы рекомендуем использовать внутренние поля в 12 эффективных пикселей, а при большой — 24 эффективных пикселя.

![рекомендуемые внутренние поля](images/12-gutter.svg)

## <a name="related-topics"></a>Связанные темы
* [**FrameworkElement.Height**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.height)
* [**FrameworkElement.Width**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.width)
* [**FrameworkElement.HorizontalAlignment**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.horizontalalignment)
* [**FrameworkElement.VerticalAlignment**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.verticalalignment)
* [**FrameworkElement.Margin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.margin)
* [**Control.Padding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.padding)
