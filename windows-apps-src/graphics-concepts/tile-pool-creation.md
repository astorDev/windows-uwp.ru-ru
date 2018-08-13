---
title: Создание пула плиток
description: Приложения могут создавать один или несколько пулов плиток для каждого устройства Direct3D. Общий размер каждого пула плиток ограничен предельным размером ресурсов Direct3D 11, который равен примерно 1/4 ОЗУ графического процессора (GPU).
ms.assetid: BD51EDD3-4AD3-4733-B014-DD77B9D743BB
keywords:
- Создание пула плиток
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4336d38bca354da3c30cfe2d7e4b092cff15af83
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1043593"
---
# <a name="tile-pool-creation"></a>Создание пула плиток


Приложения могут создавать один или несколько пулов плиток для каждого устройства Direct3D. Общий размер каждого пула плиток ограничен предельным размером ресурсов Direct3D 11, который равен примерно 1/4 ОЗУ графического процессора (GPU).

Пул плиток состоит из плиток по 64 КБ, но операционная система (видеодрайвер) управляет всем пулом как одним или несколькими выделениями памяти незаметно — разбивка недоступна приложениям. Потоковые ресурсы определяют содержимое путем указания плиток в пуле плиток. Отмена сопоставления плитки потоковому ресурсу осуществляется путем указания плитке значения **NULL**. Такие несопоставленные плитки имеют правила поведения операций чтения или записи; см. раздел [Отслеживание рисков и ресурсы пула плиток](hazard-tracking-versus-tile-pool-resources.md).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Сопоставления в пуле плиток](mappings-are-into-a-tile-pool.md)

 

 




