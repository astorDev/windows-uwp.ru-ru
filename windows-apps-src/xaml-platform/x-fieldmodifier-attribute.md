---
author: jwmsft
description: Изменяет поведение компиляции XAML таким образом, что поля для ссылок на именованный объект определяются с открытым доступом, а не с закрытым доступом по умолчанию.
title: Атрибут xFieldModifier
ms.assetid: 6FBCC00B-848D-4454-8B1F-287CA8406DDF
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: de1d7dedbd2bd3d51bd2e1c1a9652d18f2b78ef0
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2018
ms.locfileid: "6050915"
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

