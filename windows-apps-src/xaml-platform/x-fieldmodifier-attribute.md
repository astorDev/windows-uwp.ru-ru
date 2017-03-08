---
author: jwmsft
description: "Изменяет поведение компиляции XAML таким образом, что поля для ссылок на именованный объект определяются с открытым доступом, а не с закрытым доступом по умолчанию."
title: "Атрибут xFieldModifier"
ms.assetid: 6FBCC00B-848D-4454-8B1F-287CA8406DDF
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 2c1bf910303f0c26761a3c63c3e1159dd2d0c6bb
ms.lasthandoff: 02/07/2017

---

# <a name="xfieldmodifier-attribute"></a>Атрибут x:FieldModifier

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Изменяет поведение компиляции XAML таким образом, что поля для ссылок на именованный объект определяются с **открытым** доступом, а не с **закрытым** доступом по умолчанию.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object x:FieldModifier="public".../>
```

## <a name="dependencies"></a>Зависимости

Для этого элемента необходимо также указать [атрибут x:Name](x-name-attribute.md).

## <a name="remarks"></a>Комментарии

Значение атрибута **x:FieldModifier** зависит от языка программирования. Допустимые значения: **private**, **public**, **protected**, **internal** и **friend**. В языках C#, Microsoft Visual Basic и расширениях компонента Visual C++ (C++/CX) можно присвоить строковое значение public или Public. Средство синтаксического анализа не учитывает регистр для значения этого атрибута.

По умолчанию используется закрытый доступ (**Private**).

**x:FieldModifier** имеет смысл только для элементов с [x:Name attribute](x-name-attribute.md), поскольку это имя используется для ссылки на поле, если оно общедоступно.

**Примечание.** Язык XAML среды выполнения Windows не поддерживает **x:ClassModifier** или **x:Subclass**.


