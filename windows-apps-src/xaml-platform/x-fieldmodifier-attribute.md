---
description: Изменяет поведение компиляции XAML таким образом, что поля для ссылок на именованный объект определяются с открытым доступом, а не с закрытым доступом по умолчанию.
title: Атрибут xFieldModifier
ms.assetid: 6FBCC00B-848D-4454-8B1F-287CA8406DDF
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 751cda36fc58d0e6add9204327a74ec947c9fc53
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7979054"
---
# <a name="xfieldmodifier-attribute"></a>Атрибут x:FieldModifier


Изменяет поведение компиляции XAML таким образом, что поля для ссылок на именованный объект определяются с **открытым** доступом, а не с **закрытым** доступом по умолчанию.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object x:FieldModifier="public".../>
```

## <a name="dependencies"></a>Зависимости

Для этого элемента необходимо также указать [атрибут x:Name](x-name-attribute.md).

## <a name="remarks"></a>Комментарии

Значение атрибута **x:FieldModifier** зависит от языка программирования. Допустимые значения: **private**, **public**, **protected**, **internal** и **friend**. Для C#, Microsoft Visual Basic или VisualC ++ расширения компонентов (C + +/ CX), можно присвоить строковое значение «общедоступной» или «Общедоступной»; средство синтаксического анализа не учитывает регистр для значения этого атрибута.

По умолчанию используется закрытый доступ (**Private**).

**x:FieldModifier** имеет смысл только для элементов с [x:Name attribute](x-name-attribute.md), поскольку это имя используется для ссылки на поле, если оно общедоступно.

**Примечание**XAML среды выполнения Windows не поддерживает **x: ClassModifier** или **x: Subclass**.

