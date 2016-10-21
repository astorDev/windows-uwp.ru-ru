---
author: jwmsft
description: "Изменяет поведение компиляции XAML таким образом, что поля для ссылок на именованный объект определяются с открытым доступом, а не с закрытым доступом по умолчанию."
title: "Атрибут xFieldModifier"
ms.assetid: 6FBCC00B-848D-4454-8B1F-287CA8406DDF
translationtype: Human Translation
ms.sourcegitcommit: 3144758352b99f8c145a3c7be8a6c43d6a002104
ms.openlocfilehash: f812c9498d5519aac8ab750f0c55423966a63464

---

# Атрибут x:FieldModifier

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Изменяет поведение компиляции XAML таким образом, что поля для ссылок на именованный объект определяются с **открытым** доступом, а не с **закрытым** доступом по умолчанию.

## Использование атрибутов XAML

``` syntax
<object x:FieldModifier="public".../>
```

## Зависимости

Для этого элемента необходимо также указать [атрибут x:Name](x-name-attribute.md).

## Комментарии

Значение атрибута **x:FieldModifier** зависит от языка программирования. Допустимые значения: **private**, **public**, **protected**, **internal** и **friend**. В языках C#, Microsoft Visual Basic и расширениях компонента VisualC++ (C++/CX) можно присвоить строковое значение public или Public. Средство синтаксического анализа не учитывает регистр для значения этого атрибута.

По умолчанию используется закрытый доступ (**Private**).

**x:FieldModifier** имеет смысл только для элементов с [x:Name attribute](x-name-attribute.md), поскольку это имя используется для ссылки на поле, если оно общедоступно.

**Примечание.** Язык XAML среды выполнения Windows не поддерживает **x:ClassModifier** или **x:Subclass**.




<!--HONumber=Aug16_HO3-->


