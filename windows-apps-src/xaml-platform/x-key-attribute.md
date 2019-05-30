---
description: Присваивает уникальный идентификатор элементам, которые были созданы и отмечены как ресурсы и которые существуют в ResourceDictionary.
title: Атрибут xKey
ms.assetid: 141FC5AF-80EE-4401-8A1B-17CB22C2277A
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: cb42fcb17cfcad76989732b1a1482d9fbc85be5e
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372324"
---
# <a name="xkey-attribute"></a>Атрибут x:Key


Присваивает уникальный идентификатор элементам, которые были созданы и отмечены как ресурсы и которые существуют в [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary).

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
| Объект | Любой объект, к которому можно открыть общий доступ. См. раздел [Ссылки на ресурсы ResourceDictionary и XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/resourcedictionary-and-xaml-resource-references). |
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

-   Символов только нижнего диапазона ASCII и точнее латинского алфавита прописные и строчные буквы, цифры и символ подчеркивания (\_) символов.
-   Диапазон символов Юникод не поддерживается.
-   Имя не может начинаться с цифры.

## <a name="remarks"></a>Примечания

Дочерние элементы [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary) обычно содержат атрибут **x:Key**, который указывает уникальное значение ключа в этом словаре. Уникальность ключа требуется процессором XAML во время загрузки. Неуникальные значения **x:Key** приведут к исключениям синтаксического анализа XAML. Если этого требует [Расширение разметки {StaticResource}](staticresource-markup-extension.md), неразрешенный ключ также приведет к исключениям синтаксического анализа XAML.

**x:Key** и [x:Name](x-name-attribute.md) не являются идентичными концепциями. **x:Key** используется только в словарях ресурсов. x:Name используется во всех областях XAML. Вызов [**FindName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.findname) с помощью значения ключа не возвращает ресурс с ключом. Объекты, определенные в словаре ресурсов, могут иметь **x:Key**, **x:Name** или и то, и другое. Ключ и имя могут не совпадать.

Обратите внимание, что в приведенном примере неявного синтаксиса объект [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary) подразумевает, каким образом процессор XAML создает новый объект, чтобы заполнить коллекцию [**Resources**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.resources).

Эквивалентом назначения **x:Key** на уровне кода является любая операция, в которой используется ключ с базовым [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary). Например, атрибут **x:Key**, примененный в разметке к ресурсу, эквивалентен значению параметра *key* для **Insert**, когда вы добавляете ресурс в **ResourceDictionary**.

Элемент в словаре ресурсов может пропустить значение для **x:Key**, если оно является конечным объектом для [**Style**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) или [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate). В каждом из этих вариантов неявный ключ элемента ресурса представляет собой значение **TargetType**, обрабатываемое как строка. Дополнительные сведения см. в разделах [Краткое руководство: стили элементов управления](https://docs.microsoft.com/previous-versions/windows/apps/hh465498(v=win.10)) и [Ссылки на ресурсы ResourceDictionary и XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/resourcedictionary-and-xaml-resource-references).

