---
title: Доступные операции с потоковыми ресурсами
description: В этом разделе перечислены операции, которые можно выполнять с потоковыми ресурсами.
ms.assetid: 700D8C54-0E20-4B2B-BEA3-20F6F72B8E24
keywords:
- Доступные операции с потоковыми ресурсами
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: ee9f6b3744b038d9f4ae0f5b490cbd763eda08ee
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1043133"
---
# <a name="operations-available-on-streaming-resources"></a>Доступные операции с потоковыми ресурсами


В этом разделе перечислены операции, которые можно выполнять с потоковыми ресурсами.

-   Операции обновления сопоставлений плиток, возвращающие void, и операции копирования сопоставлений плиток, возвращающие void — эти операции служат указателями для расположений плиток в потоковом ресурсе на расположения в пулах плиток или на значение NULL, или на оба вида значений. Эти операции могут обновлять раздельные подмножества указателей плиток.
-   Операции копирования и обновления — все интерфейсы API, которые могут копировать данные в пул поверхности по умолчанию и из него, работают с потоковыми ресурсами. Чтение из несопоставленных плиток дает 0, а операции записи в несопоставленные плитки не выполняются.
-   Операции копирования плитки и обновления плитки — эти операции предусмотрены для копирования плиток на уровне детализации 64 КБ в любой потоковый ресурс и из него, а также в любой ресурс буфера и из него в стандартной схеме памяти. Оборудование и драйвер экрана выполняют все операции адресации памяти, необходимые для потокового ресурса.
-   Привязки к конвейеру Direct3D и привязки к созданным представлениям, которые работают на ресурсах, не относящихся к потоковым, также работают и на потоковых ресурсах.

Элементы управления плиткой доступны в немедленных и отложенных контекстах (так же, как и обновления типичных ресурсов) и после выполнения влияют на последующие обращения к плиткам (но не ранее отправленные операции).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Создание потоковых ресурсов](creating-streaming-resources.md)

 

 



