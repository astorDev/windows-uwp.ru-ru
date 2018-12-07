---
description: Присваивает уникальный идентификатор элементам, которые были созданы и отмечены как ресурсы и которые существуют в ResourceDictionary.
title: Атрибут xKey
ms.assetid: 141FC5AF-80EE-4401-8A1B-17CB22C2277A
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 407ceeb4964e616bdbcacb14620ed1a488a0072b
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8784605"
---
# <a name="xkey-attribute"></a>Атрибут x:Key


Присваивает уникальный идентификатор элементам, которые были созданы и отмечены как ресурсы и которые существуют в [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794).

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<ResourceDictionary>
  <object x:Key="stringKeyValue".../>
</ResourceDictionary>
```

## <a name="xaml-attribute-usage-implicit-resourcedictionary"></a>Использование атрибута языка XAML (неявный **ResourceDictionary**)

``` syntax
<object.Resources>
  <object x:Key="stringKeyValue".../>
</object.Resources>
```

## <a name="xaml-values"></a>Значения XAML

| Термин | Описание |
|------|-------------|
| object | Любой объект, к которому можно открыть общий доступ. См. раздел [Ссылки на ресурсы ResourceDictionary и XAML](https://msdn.microsoft.com/library/windows/apps/mt187273). |
| stringKeyValue | Строка true используется в качестве ключа, который должен соответствовать грамматике _XamlName_. См. раздел "Грамматика XamlName" ниже. | 

##  <a name="xamlname-grammar"></a>Грамматика XamlName

Ниже приведена нормативная грамматика для строки, используемой в качестве ключа в данной реализации XAML универсальной платформы для Windows (UWP).

``` syntax
XamlName ::= NameStartChar (NameChar)*
NameStartChar ::= LetterCharacter | '_'
NameChar ::= NameStartChar | DecimalDigit
LetterCharacter ::= ('a'-'z') | ('A'-'Z')
DecimalDigit ::= '0'-'9'
CombiningCharacter::= none
```

-   Можно использовать только нижний диапазон символов ASCII, то есть латинские буквы верхнего и нижнего регистров, цифры и символ подчеркивания ("_").
-   Диапазон символов Юникод не поддерживается.
-   Имя не может начинаться с цифры.

## <a name="remarks"></a>Комментарии

Дочерние элементы [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794) обычно содержат атрибут **x:Key**, который указывает уникальное значение ключа в этом словаре. Уникальность ключа требуется процессором XAML во время загрузки. Неуникальные значения **x:Key** приведут к исключениям синтаксического анализа XAML. Если этого требует [Расширение разметки {StaticResource}](staticresource-markup-extension.md), неразрешенный ключ также приведет к исключениям синтаксического анализа XAML.

**x:Key** и [x:Name](x-name-attribute.md) не являются идентичными концепциями. **x:Key** используется только в словарях ресурсов. x:Name используется во всех областях XAML. Вызов [**FindName**](https://msdn.microsoft.com/library/windows/apps/br208715) с помощью значения ключа не возвращает ресурс с ключом. Объекты, определенные в словаре ресурсов, могут иметь **x:Key**, **x:Name** или и то, и другое. Ключ и имя могут не совпадать.

Обратите внимание, что в приведенном примере неявного синтаксиса объект [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794) подразумевает, каким образом процессор XAML создает новый объект, чтобы заполнить коллекцию [**Resources**](https://msdn.microsoft.com/library/windows/apps/br208740).

Эквивалентом назначения **x:Key** на уровне кода является любая операция, в которой используется ключ с базовым [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794). Например, атрибут **x:Key**, примененный в разметке к ресурсу, эквивалентен значению параметра *key* для **Insert**, когда вы добавляете ресурс в **ResourceDictionary**.

Элемент в словаре ресурсов может пропустить значение для **x:Key**, если оно является конечным объектом для [**Style**](https://msdn.microsoft.com/library/windows/apps/br208849) или [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391). В каждом из этих вариантов неявный ключ элемента ресурса представляет собой значение **TargetType**, обрабатываемое как строка. Дополнительные сведения см. в разделах [Краткое руководство: стили элементов управления](https://msdn.microsoft.com/library/windows/apps/hh465498) и [Ссылки на ресурсы ResourceDictionary и XAML](https://msdn.microsoft.com/library/windows/apps/mt187273).

