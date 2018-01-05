---
title: "Поведение UAV с несопоставленными плитками"
description: "Поведение операций чтения и записи представлений неупорядоченного доступа (UAV) зависит от уровня поддержки оборудования."
ms.assetid: CDB224E2-CC07-4568-9AAC-C8DC74536561
keywords: "Поведение UAV с несопоставленными плитками"
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: c1429579ddd896d5c717968f509ddf578a79f2e0
ms.sourcegitcommit: c80b9e6589a1ee29c5032a0b942e6a024c224ea7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="span-iddirect3dconceptsuavbehaviorwithnon-mappedtilesspanuav-behavior-with-non-mapped-tiles"></a><span id="direct3dconcepts.uav_behavior_with_non-mapped_tiles"></span>Поведение UAV с несопоставленными плитками


Поведение операций чтения и записи представлений неупорядоченного доступа (UAV) зависит от уровня поддержки оборудования. Описание требований представлено в разделе [Уровни функций для потоковых ресурсов](streaming-resources-features-tiers.md), где рассказывается об общем поведении операций чтений и записи. В этом разделе описывается идеальное поведение.

Операции шейдера, выполняющие чтение из несопоставленной плитки в UAV, возвращают 0 во всех присутствующих компонентах формата и значение по умолчанию для отсутствующих компонентов.

В результате операций шейдера, пытающихся выполнить запись в несопоставленную плитку, в несопоставленную область ничего не записывается (тогда как запись в сопоставленную область происходит). Такое идеальное определение обработки записи не требуется для [второго уровня](tier-2.md). Операции записи в несопоставленные плитки могут оказаться в кэше, откуда данные могут взять последующие операции чтения.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Конвейерный доступ к потоковым ресурсам](pipeline-access-to-streaming-resources.md)

 

 




