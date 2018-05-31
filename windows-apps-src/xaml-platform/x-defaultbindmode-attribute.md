---
author: jwmsft
description: В разметке XAML задает режим по умолчанию для x:Bind.
title: атрибут xDefaultBindMode
ms.author: jimwalk
ms.date: 02/08/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: 4eae77a51f925eaff27a7919ecfa60552d0fbae5
ms.sourcegitcommit: b8c77ac8e40a27cf762328d730c121c28de5fbc4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2018
ms.locfileid: "1672981"
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
