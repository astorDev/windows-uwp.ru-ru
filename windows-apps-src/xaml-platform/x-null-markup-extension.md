---
author: jwmsft
description: "В разметке XAML для свойства задается значение null."
title: "Расширение разметки x:Null"
ms.assetid: E6A4038E-4ADA-4E82-9824-582FC16AB037
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: a367594cab5e1f29ce6c5f45ee869b025c4bf47e
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="xnull-markup-extension"></a>Расширение разметки {x:Null}

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В разметке XAML для свойства задается значение **null**.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object property="{x:Null}" .../>
```

## <a name="remarks"></a>Комментарии

**null** представляет собой ключевое слово пустой ссылки для C# и C++. Ключевым словом Microsoft Visual Basic для пустой ссылки является **Nothing**.

Исходное значение по умолчанию может отличаться у различных свойств зависимостей, и это не обязательно будет значение **null**. Кроме того, многие свойства зависимостей не будут принимать **null** в качестве значения (ни через разметку, ни через код) из-за своей внутренней реализации. В таких случаях установка значения атрибута XAML при помощи **{x:Null}** может привести к исключению средства синтаксического анализа.

Некоторые типы среды выполнения Windows допускают значения NULL. В тех случаях, когда для типов, допускающих значение NULL, еще не установлено значение **null** по умолчанию, вы можете задать значение **null** в XAML при помощи **{x:Null}**. При использовании расширений компонента VisualC++ (C++/CX) типы, допускающие значение NULL, представлены как [**Platform::IBox<T>**](https://msdn.microsoft.com/library/windows/apps/xaml/jj606120.aspx). При использовании языков Microsoft .NET типы, допускающие значение NULL, представлены как [**Nullable<T>**](https://msdn.microsoft.com/library/windows/apps/xaml/b3h38hb0.aspx).

## <a name="related-topics"></a>Ссылки по теме

* [**Допускающий значение NULL<T>**](https://msdn.microsoft.com/library/windows/apps/xaml/b3h38hb0.aspx)
* [**IReference<T>**](https://msdn.microsoft.com/library/windows/apps/br225864)
 

