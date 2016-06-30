---
author: jwmsft
description: "Изменяет поведение компиляции XAML таким образом, что поля для ссылок на именованный объект определяются с открытым доступом, а не с закрытым доступом по умолчанию."
title: "Атрибут xFieldModifier"
ms.assetid: 6FBCC00B-848D-4454-8B1F-287CA8406DDF
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 3f6c759d582d92aa33df5874186aeaa3a7c37b01

---

# Атрибут x:FieldModifier

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Изменяет поведение компиляции XAML таким образом, что поля для ссылок на именованный объект определяются с **открытым** доступом, а не с **закрытым** доступом по умолчанию.

## Использование атрибутов XAML

``` syntax
<object x:FieldModifier="public".../>
```

## Зависимости

[x:Name attribute](x-name-attribute.md) необходимо также указать для того же элемента.

## Комментарии

Значение атрибута **x:FieldModifier** зависит от языка программирования. Используемая строка зависит от того, как на конкретном языке реализован объект **CodeDomProvider**, а также от преобразователей типов, которые он возвращает для определения значений **TypeAttributes.Public** и **TypeAttributes.NotPublic**. В языках C#, Microsoft Visual Basic и расширениях компонента Visual C++ (C++/CX) можно присвоить строковое значение public или Public. Средство синтаксического анализа не учитывает регистр для значения этого атрибута.

В редких случаях можно также указать **NonPublic** (**internal** в C# или C++/CX, **Friend** в Visual Basic). Внутренний доступ не применяется к модели создания кода XAML среды выполнения Windows. По умолчанию используется закрытый доступ.

**x:FieldModifier** имеет смысл только для элементов с [x:Name attribute](x-name-attribute.md), поскольку это имя используется для ссылки на поле, если оно общедоступно.

**Примечание.** Язык XAML среды выполнения Windows не поддерживает **x:ClassModifier** или **x:Subclass**.




<!--HONumber=Jun16_HO4-->


