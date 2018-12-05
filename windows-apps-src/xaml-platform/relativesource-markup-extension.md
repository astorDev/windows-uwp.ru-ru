---
description: Предоставляет способ указания источника привязки в терминах относительной связи внутри графа объекта среды выполнения.
title: Расширение разметки RelativeSource
ms.assetid: B87DEF36-BE1F-4C16-B32E-7A896BD09272
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 26cde97f82e6962d530721f1e0230138e5917016
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8685180"
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
| {RelativeSource Self} | Создает значение [<strong>Mode</strong>](https://msdn.microsoft.com/library/windows/apps/br209915) для <strong>Self</strong>. В качестве источника этой привязки следует использовать целевой элемент. Это полезно для привязки одного из свойств элемента к другому свойству того же элемента. |
| {RelativeSource TemplatedParent} | Создает элемент [<strong>ControlTemplate</strong>](https://msdn.microsoft.com/library/windows/apps/br209391), который служит источником этой привязки. Это полезно для применения информации времени выполнения к привязкам на уровне шаблонов. | 

## <a name="remarks"></a>Комментарии

Класс [**Binding**](https://msdn.microsoft.com/library/windows/apps/br209820) может задать [**Binding.RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209831) как атрибут элемента объекта **Binding** или как компонент в расширении разметки [{Binding}](binding-markup-extension.md). Именно по этой причине отображаются два различных синтаксиса XAML.

**RelativeSource** аналогичен [расширению разметки {Binding}](binding-markup-extension.md).  Схожесть заключается в том, что расширение разметки может возвращать свои экземпляры, поддерживая конструкцию на основе строки, которая, таким образом, передает аргумент конструктору. В этом случае передаваемый аргумент — это значение [**Mode**](https://msdn.microsoft.com/library/windows/apps/br209915).

Режим **Self** полезен для привязки одного свойства элемента к другому свойству того же элемента; это вариант привязки [**ElementName**](https://msdn.microsoft.com/library/windows/apps/br209828), не требующей именования элемента и установки для него ссылки на себя самого. Если нужно привязать одно свойство элемента к другому свойству того же элемента, эти свойства либо должны быть одного типа, либо на их привязке необходимо использовать [**Converter**](https://msdn.microsoft.com/library/windows/apps/br209826) для преобразования значений. Например, можно использовать [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) как источник для [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) без преобразования, но чтобы использовать [**IsEnabled**](https://msdn.microsoft.com/library/windows/apps/br209419) в качестве источника для [**Visibility**](https://msdn.microsoft.com/library/windows/apps/br209006), понадобится преобразователь.

Пример: [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) использует [расширение разметки {Binding}](binding-markup-extension.md), чтобы значения [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) всегда были равны и при отрисовке отображался квадрат. Только значение Height является фиксированным. Для этого объекта **Rectangle** значение [**DataContext**](https://msdn.microsoft.com/library/windows/apps/br208713) по умолчанию равно **null**, а не **this**. Таким образом, чтобы установить источник контекста данных как объект (и включить привязку к другим его свойствам), используется аргумент `RelativeSource={RelativeSource Self}` в качестве расширения разметки {Binding}.

```XML
<Rectangle
  Fill="Orange" Width="200"
  Height="{Binding RelativeSource={RelativeSource Self}, Path=Width}"
/>
```

Также `RelativeSource={RelativeSource Self}` можно использовать, чтобы задать для свойства [**DataContext**](https://msdn.microsoft.com/library/windows/apps/br208713) объекта самого себя.  Например, это можно увидеть в некоторых примерах SDK, где класс [**Page**](https://msdn.microsoft.com/library/windows/apps/br227503) дополнен настраиваемым свойством, которое уже предоставляет готовую модель представления для собственной привязки данных, например: `<common:LayoutAwarePage ... DataContext="{Binding DefaultViewModel, RelativeSource={RelativeSource Self}}">`

**Примечание**использование XAML для **RelativeSource** показано только использование, для которого он предназначен: задать значение для [**Binding.RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209831) в XAML как часть выражения привязки. Теоретически возможны и другие способы задания свойства, значением которого является [**RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209913).

## <a name="related-topics"></a>Еще по теме

* [Обзор языка XAML](xaml-overview.md)
* [Подробно о привязке данных](https://msdn.microsoft.com/library/windows/apps/mt210946)
* [Расширение разметки {Binding}](binding-markup-extension.md)
* [**Привязка**](https://msdn.microsoft.com/library/windows/apps/br209820)
* [**RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209913)

