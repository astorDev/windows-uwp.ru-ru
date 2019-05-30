---
description: Предоставляет способ указания источника привязки в терминах относительной связи внутри графа объекта среды выполнения.
title: Расширение разметки RelativeSource
ms.assetid: B87DEF36-BE1F-4C16-B32E-7A896BD09272
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 716ca61fc9925846377157d215ca3326191915b7
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371140"
---
# <a name="relativesource-markup-extension"></a>Расширение разметки {RelativeSource}


Предоставляет способ указания источника привязки в терминах относительной связи внутри графа объекта среды выполнения.

## <a name="xaml-attribute-usage-self-mode"></a>Использование атрибута языка XAML (режим Self)

``` syntax
<Binding RelativeSource="{RelativeSource Self}" .../>
-or-
<object property="{Binding RelativeSource={RelativeSource Self} ...}" .../>
```

## <a name="xaml-attribute-usage-templatedparent-mode"></a>Использование атрибута языка XAML (узел TemplatedParent)

``` syntax
<Binding RelativeSource="{RelativeSource TemplatedParent}" .../>
-or-
<object property="{Binding RelativeSource={RelativeSource TemplatedParent} ...}" .../>
```

## <a name="xaml-values"></a>Значения XAML

| Термин | Описание |
|------|-------------|
| {RelativeSource Self} | Создает значение [<strong>Mode</strong>](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.relativesource.mode) для <strong>Self</strong>. В качестве источника этой привязки следует использовать целевой элемент. Это полезно для привязки одного из свойств элемента к другому свойству того же элемента. |
| {RelativeSource TemplatedParent} | Создает элемент [<strong>ControlTemplate</strong>](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate), который служит источником этой привязки. Это полезно для применения информации времени выполнения к привязкам на уровне шаблонов. | 

## <a name="remarks"></a>Примечания

Класс [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding) может задать [**Binding.RelativeSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.relativesource) как атрибут элемента объекта **Binding** или как компонент в расширении разметки [{Binding}](binding-markup-extension.md). Именно по этой причине отображаются два различных синтаксиса XAML.

**RelativeSource** аналогичен [расширению разметки {Binding}](binding-markup-extension.md).  Схожесть заключается в том, что расширение разметки может возвращать свои экземпляры, поддерживая конструкцию на основе строки, которая, таким образом, передает аргумент конструктору. В этом случае передаваемый аргумент — это значение [**Mode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.relativesource.mode).

Режим **Self** полезен для привязки одного свойства элемента к другому свойству того же элемента; это вариант привязки [**ElementName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.elementname), не требующей именования элемента и установки для него ссылки на себя самого. Если нужно привязать одно свойство элемента к другому свойству того же элемента, эти свойства либо должны быть одного типа, либо на их привязке необходимо использовать [**Converter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converter) для преобразования значений. Например, можно использовать [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) как источник для [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) без преобразования, но чтобы использовать [**IsEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.isenabled) в качестве источника для [**Visibility**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Visibility), понадобится преобразователь.

Рассмотрим пример. [  **Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) использует [расширение разметки {Binding}](binding-markup-extension.md), чтобы значения [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) всегда были равны и при отрисовке отображался квадрат. Только значение Height является фиксированным. Для этого объекта **Rectangle** значение [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) по умолчанию равно **null**, а не **this**. Таким образом, чтобы установить источник контекста данных как объект (и включить привязку к другим его свойствам), используется аргумент `RelativeSource={RelativeSource Self}` в качестве расширения разметки {Binding}.

```XML
<Rectangle
  Fill="Orange" Width="200"
  Height="{Binding RelativeSource={RelativeSource Self}, Path=Width}"
/>
```

Также `RelativeSource={RelativeSource Self}` можно использовать, чтобы задать для свойства [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) объекта самого себя.  Например, может появиться этот прием в некоторых примерах SDK где [ **страницы** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page) класс расширен с настраиваемое свойство, которое уже предоставляет модель представления готовые к использованию для свои собственные привязки данных Например: `<common:LayoutAwarePage ... DataContext="{Binding DefaultViewModel, RelativeSource={RelativeSource Self}}">`

**Примечание**  использование XAML для **RelativeSource** информацию только об использовании, для которого предназначен: задание значения для [ **Binding.RelativeSource** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.relativesource)в XAML как часть выражения привязки. Теоретически возможны и другие способы задания свойства, значением которого является [**RelativeSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.RelativeSource).

## <a name="related-topics"></a>См. также

* [Обзор языка XAML](xaml-overview.md)
* [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth)
* [Расширение разметки {binding}](binding-markup-extension.md)
* [**Привязки**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding)
* [**RelativeSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.RelativeSource)

