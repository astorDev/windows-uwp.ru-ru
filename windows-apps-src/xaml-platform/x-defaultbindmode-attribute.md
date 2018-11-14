---
author: jwmsft
description: В разметке XAML задает режим по умолчанию для x:Bind.
title: атрибут xDefaultBindMode
ms.author: jimwalk
ms.date: 02/08/2018
ms.topic: article
keywords: Windows10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: 2696cb46591757421795b15083ea7fdab54943c5
ms.sourcegitcommit: f2c9a050a9137a473f28b613968d5782866142c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6252632"
---
# <a name="xdefaultbindmode-attribute"></a>атрибут x:DefaultBindMode

В разметке XAML задает режим по умолчанию для x:Bind.

**x:DefaultBindMode** доступен начиная с Windows 10 (версия 1607, юбилейное обновление), SDK версии 14393.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object x:DefaultBindMode="OneTime \| OneWay \| TwoWay" .../>
```

## <a name="remarks"></a>Примечания.

[x: Bind](x-bind-markup-extension.md) имеет режим по умолчанию **OneTime **. Он был выбран в целях повышения производительности, поскольку при использовании **OneWay** создается больший объем кода для подключения и обнаружения изменений. **x:DefaultBindMode** можно использовать, чтобы изменить режим по умолчанию для x:Bind для определенного сегмента дерева разметки. Выбранный режим применяет любые выражения x:Bind к этому элементу и его дочерним элементам, которые явным образом не задают режим в качестве части привязки.

## <a name="related-topics"></a>Статьи по теме

* [Расширение разметки x:Bind](x-bind-markup-extension.md)
