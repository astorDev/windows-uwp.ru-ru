---
title: Поведение UAV с несопоставленными плитками
description: Поведение операций чтения и записи представлений неупорядоченного доступа (UAV) зависит от уровня поддержки оборудования.
ms.assetid: CDB224E2-CC07-4568-9AAC-C8DC74536561
keywords:
- Поведение UAV с несопоставленными плитками
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: a5418de3646f70a815f5c482f9063ea3e48ddfa1
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5831823"
---
# <a name="span-iddirect3dconceptsuavbehaviorwithnon-mappedtilesspanuav-behavior-with-non-mapped-tiles"></a><span id="direct3dconcepts.uav_behavior_with_non-mapped_tiles"></span>Поведение UAV с несопоставленными плитками


Поведение операций чтения и записи представлений неупорядоченного доступа (UAV) зависит от уровня поддержки оборудования. Описание требований представлено в разделе [Уровни функций для потоковых ресурсов](streaming-resources-features-tiers.md), где рассказывается об общем поведении операций чтений и записи. В этом разделе описывается идеальное поведение.

Операции шейдера, выполняющие чтение из несопоставленной плитки в UAV, возвращают 0 во всех присутствующих компонентах формата и значение по умолчанию для отсутствующих компонентов.

В результате операций шейдера, пытающихся выполнить запись в несопоставленную плитку, в несопоставленную область ничего не записывается (тогда как запись в сопоставленную область происходит). Такое идеальное определение обработки записи не требуется для [второго уровня](tier-2.md). Операции записи в несопоставленные плитки могут оказаться в кэше, откуда данные могут взять последующие операции чтения.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Конвейерный доступ к потоковым ресурсам](pipeline-access-to-streaming-resources.md)

 

 




