---
description: В разметке XAML для свойства задается значение null.
title: Расширение разметки x:Null
ms.assetid: E6A4038E-4ADA-4E82-9824-582FC16AB037
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d6ee1f4cb1fa0a97c8d1b8a447ccc15cd0b51776
ms.sourcegitcommit: a20457776064c95a74804f519993f36b87df911e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71340450"
---
# <a name="xnull-markup-extension"></a>Расширение разметки {x:Null}


В разметке XAML для свойства задается значение **null**.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object property="{x:Null}" .../>
```

## <a name="remarks"></a>Примечания

**null** представляет собой ключевое слово пустой ссылки для C# и C++. Ключевым словом Microsoft Visual Basic для пустой ссылки является **Nothing**.

Исходное значение по умолчанию может отличаться у различных свойств зависимостей, и это не обязательно будет значение **null**. Кроме того, многие свойства зависимостей не будут принимать **null** в качестве значения (ни через разметку, ни через код) из-за своей внутренней реализации. В таких случаях установка значения атрибута XAML при помощи **{x:Null}** может привести к исключению средства синтаксического анализа.

Некоторые типы среды выполнения Windows допускают значения NULL. В тех случаях, когда для типов, допускающих значение NULL, еще не установлено значение **null** по умолчанию, вы можете задать значение **null** в XAML при помощи **{x:Null}** . При использовании расширений C++ Visual Component Extensions (C++/CX) типы, допускающие значение null, представлены в виде [Platform:: ibox @ no__t-4](https://docs.microsoft.com/cpp/cppcx/platform-ibox-interface). При использовании языков Microsoft .NET типы, допускающие значение NULL, представлены как [**Nullable<T>** ](https://docs.microsoft.com/dotnet/api/system.nullable-1).

## <a name="related-topics"></a>См. также

* [**Nullable @ no__t-2**](https://docs.microsoft.com/dotnet/api/system.nullable-1)
* [**Иреференце @ no__t-2**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IReference_T_)
 

