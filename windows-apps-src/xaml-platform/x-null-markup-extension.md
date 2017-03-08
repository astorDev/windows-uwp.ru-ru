---
author: jwmsft
description: "В разметке XAML задает для свойства значение null."
title: "Расширение разметки x:Null"
ms.assetid: E6A4038E-4ADA-4E82-9824-582FC16AB037
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 7be1caeca3427f75263019dbdba92c8695b6dde3
ms.lasthandoff: 02/07/2017

---

# <a name="xnull-markup-extension"></a>Расширение разметки {x:Null}

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В разметке XAML задает для свойства значение **null**.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object property="{x:Null}" .../>
```

## <a name="remarks"></a>Комментарии

**null** представляет собой ключевое слово пустой ссылки для C# и C++. Ключевым словом Microsoft Visual Basic для пустой ссылки является **Nothing**.

Исходное значение по умолчанию может отличаться у различных свойств зависимостей, и это не обязательно будет значение **null**. Кроме того, многие свойства зависимостей не будут принимать **null** в качестве значения (ни через разметку, ни через код) из-за своей внутренней реализации. В таких случаях установка значения атрибута XAML при помощи **{x:Null}** может привести к исключению средства синтаксического анализа.

Некоторые типы среды выполнения Windows допускают значения NULL. В тех случаях, когда для типов, допускающих значение NULL, еще не установлено значение **null** по умолчанию, вы можете задать значение **null** в XAML при помощи **{x:Null}**. При использовании расширений компонента Visual C++ (C++/CX) типы, допускающие значение NULL, представлены как [**Platform::IBox<T>**](https://msdn.microsoft.com/library/windows/apps/xaml/jj606120.aspx). При использовании языков Microsoft .NET типы, допускающие значение NULL, представлены как [**Nullable<T>**](https://msdn.microsoft.com/library/windows/apps/xaml/b3h38hb0.aspx).

## <a name="related-topics"></a>Ссылки по теме

* [**Допускающий значение NULL<T>**](https://msdn.microsoft.com/library/windows/apps/xaml/b3h38hb0.aspx)
* [**IReference<T>**](https://msdn.microsoft.com/library/windows/apps/br225864)
 


