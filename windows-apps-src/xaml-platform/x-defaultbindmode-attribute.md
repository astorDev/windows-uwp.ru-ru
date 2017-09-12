---
author: tbd
description: "В разметке XAML задает режим по умолчанию для x:Bind"
title: "Расширение разметки xDefaultBindMode"
ms.assetid: 
ms.author: 
ms.date: 
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows10, UWP
ms.openlocfilehash: 0fa037b4c59566cb1b9bacd4d2e36520a86c508d
ms.sourcegitcommit: ba0d20f6fad75ce98c25ceead78aab6661250571
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2017
---
# <a name="xdefaultbindmode-markup-extension"></a>Расширение разметки {x:DefaultBindMode}

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В разметке XAML задает режим по умолчанию для x:Bind.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object x:DefaultBindMode="OneTime \| OneWay \| TwoWay" .../>
```

## <a name="remarks"></a>Примечания

x:Bind имеет режим по умолчанию OneTime, выбранный в целях повышения производительности, поскольку при использовании OneTime создается больший объем кода для подключения и обнаружения изменений. **x:DefaultBindMode** можно использовать, чтобы изменить режим по умолчанию для x:Bind для определенного сегмента дерева разметки. Выбранный режим будет применять любые выражения x:Bind к этому элементу и его дочерним элементам, которые явным образом не задают режим в качестве части привязки.

## <a name="related-topics"></a>Статьи по теме

* [**x:Bind**](https://docs.microsoft.com/en-us/windows/uwp/xaml-platform/x-bind-markup-extension)
