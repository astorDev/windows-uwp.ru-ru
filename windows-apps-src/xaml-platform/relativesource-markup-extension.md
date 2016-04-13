---
description: Предоставляет способ указания источника привязки в терминах относительной связи внутри графа объекта среды выполнения.
title: Расширение разметки RelativeSource
ms.assetid: B87DEF36-BE1F-4C16-B32E-7A896BD09272
---

# Расширение разметки {RelativeSource}

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Предоставляет способ указания источника привязки в терминах относительной связи внутри графа объекта среды выполнения.

## Использование атрибута языка XAML (режим Self)

``` syntax
<Binding RelativeSource="{RelativeSource Self}" .../>
-or-
<object property="{Binding RelativeSource={RelativeSource Self} ...}" .../>
```

## Использование атрибута языка XAML (узел TemplatedParent)

``` syntax
<Binding RelativeSource="{RelativeSource TemplatedParent}" .../>
-or-
<object property="{Binding RelativeSource={RelativeSource TemplatedParent} ...}" .../>
```

## Значения XAML

| Условие | Описание |
| {RelativeSource Self} | Создает [<strong>режим</strong>](https://msdn.microsoft.com/library/windows/apps/br209915) со значением, равным <strong>Self</strong>. В качестве источника этой привязки следует использовать целевой элемент. Это полезно для привязки одного из свойств элемента к другому свойству того же элемента. |
| {RelativeSource TemplatedParent} | Создает [<strong>ControlTemplate</strong>](https://msdn.microsoft.com/library/windows/apps/br209391), который является источником этой привязки. Это полезно для применения информации времени выполнения к привязкам на уровне шаблонов. | 

## Комментарии

Класс [**Binding**](https://msdn.microsoft.com/library/windows/apps/br209820) может задать [**Binding.RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209831) как атрибут элемента объекта **Binding** или как компонент в расширении разметки [{Binding}](binding-markup-extension.md). Именно по этой причине отображаются два различных синтаксиса XAML.

Расширение разметки **RelativeSource** аналогично расширению [{Binding}](binding-markup-extension.md), так как тоже является расширением разметки и способно возвращать свои экземпляры, поддерживая конструкцию на основе строки, которая, таким образом, передает аргумент конструктору. В этом случае передаваемым аргументом является значение [**Mode**](https://msdn.microsoft.com/library/windows/apps/br209915).

Режим **Self** полезен в случаях, когда один и тот же элемент следует использовать и как исходный объект, и как конечный объект для привязки, но источником и целью являются разные свойства. Это полезно для привязки одного свойства элемента к другому свойству того же элемента; это вариант привязки [**ElementName**](https://msdn.microsoft.com/library/windows/apps/br209828), не требующей именования элемента и установки для него ссылки на себя самого. Если нужно привязать одно свойство элемента к другому свойству того же элемента, эти свойства либо должны быть одного типа, либо на их привязке необходимо использовать [**Converter**](https://msdn.microsoft.com/library/windows/apps/br209826) для преобразования значений. Например, можно использовать [**Height**](https://msdn.microsoft.com/library/windows/apps/br208718) как источник для [**Width**](https://msdn.microsoft.com/library/windows/apps/br208751) без преобразования, но чтобы использовать [**IsEnabled**](https://msdn.microsoft.com/library/windows/apps/br209419) в качестве источника для [**Visibility**](https://msdn.microsoft.com/library/windows/apps/br209006), понадобится преобразователь.

Пример: [
            **Rectangle**](https://msdn.microsoft.com/library/windows/apps/br243371) использует [расширение разметки {Binding}](binding-markup-extension.md), чтобы значения [**Height**](https://msdn.microsoft.com/library/windows/apps/br208718) и [**Width**](https://msdn.microsoft.com/library/windows/apps/br208751) всегда были равны и при отрисовке отображался квадрат. Только значение Height является фиксированным. Для этого объекта **Rectangle** значение [**DataContext**](https://msdn.microsoft.com/library/windows/apps/br208713) по умолчанию равно **null**, а не **this**. Таким образом, чтобы установить источник контекста данных как объект (и включить привязку к другим его свойствам), используется аргумент `RelativeSource={RelativeSource Self}` в качестве расширения разметки {Binding}.

```XAML
<Rectangle
  Fill="Orange" Width="200"
  Height="{Binding RelativeSource={RelativeSource Self}, Path=Width}"
/>
```

Кроме того, может быть полезно использовать `RelativeSource={RelativeSource Self}`, чтобы установить свойство [**DataContext**](https://msdn.microsoft.com/library/windows/apps/br208713) объекта само на себя, где класс [**Page**](https://msdn.microsoft.com/library/windows/apps/br227503) расширен настраиваемым свойством, которое уже предоставляет готовую модель представления для собственной привязки данных. Этот способ используется в некоторых примерах SDK: `<common:LayoutAwarePage ... DataContext="{Binding DefaultViewModel, RelativeSource={RelativeSource Self}}">`

**Примечание.**  Использование XAML для **RelativeSource** показывает только изначально предполагаемый способ использования: задание значения [**Binding.RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209831) в XAML как части выражения привязки. Теоретически возможны и другие способы задания свойства, значением которого является [**RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209913).

## Связанные разделы

* [Обзор языка XAML](xaml-overview.md)
* [Подробно о привязке данных](https://msdn.microsoft.com/library/windows/apps/mt210946)
* [Расширение разметки {Binding}](binding-markup-extension.md)
* [**Привязка**](https://msdn.microsoft.com/library/windows/apps/br209820)
* [**RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209913)



<!--HONumber=Mar16_HO1-->


