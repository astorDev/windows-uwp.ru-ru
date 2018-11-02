---
title: Поведение средства программной прорисовки с несопоставленными плитками
description: В этом разделе описывается поведение растеризации несопоставленных плиток.
ms.assetid: AC7B818D-E52B-4727-AEA2-39CFDC279CE7
keywords:
- Поведение средства программной прорисовки с несопоставленными плитками
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: f56e8051c8a66cf579a7c3dbcddc738b3a10bcaf
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5937851"
---
# <a name="span-iddirect3dconceptsrasterizerbehaviorwithnon-mappedtilesspanrasterizer-behavior-with-non-mapped-tiles"></a><span id="direct3dconcepts.rasterizer_behavior_with_non-mapped_tiles"></span>Поведение средства программной прорисовки с несопоставленными плитками


В этом разделе описывается поведение растеризации несопоставленных плиток.

## <a name="span-iddepthstencilviewspanspan-iddepthstencilviewspanspan-iddepthstencilviewspandepthstencilview"></a><span id="DepthStencilView"></span><span id="depthstencilview"></span><span id="DEPTHSTENCILVIEW"></span>DepthStencilView


Поведение операций чтения и записи представления трафарета глубины (DSV) зависит от уровня поддержки оборудования. Описание требований представлено в разделе [Уровни функций для потоковых ресурсов](streaming-resources-features-tiers.md), где рассказывается об общем поведении операций чтений и записи.

Ниже описывается идеальное поведение.

Если плитка не сопоставлена в представлении DepthStencilView, возвращаемое значение чтения глубины (0) передается любым операциям, настроенным для чтения значения глубины. Операции записи в плитку с отсутствующей глубиной отклоняются. Такое идеальное определение обработки записи не требуется для [второго уровня](tier-2.md). Операции записи в несопоставленные плитки могут оказаться в кэше, откуда данные могут взять последующие операции чтения.

## <a name="span-idrendertargetviewspanspan-idrendertargetviewspanspan-idrendertargetviewspanrendertargetview"></a><span id="RenderTargetView"></span><span id="rendertargetview"></span><span id="RENDERTARGETVIEW"></span>RenderTargetView


Поведение операций чтения и записи представления целевого объекта отрисовки (RTV) зависит от уровня поддержки оборудования. Описание требований представлено в разделе [Уровни функций для потоковых ресурсов](streaming-resources-features-tiers.md), где рассказывается об общем поведении операций чтений и записи.

Во всех реализациях различные представления RTV (и DSV), привязанные одновременно, могут содержать разные сопоставленные или несопоставленные области и могут использовать различные форматы поверхностей (т. е. разные формы плиток).

Ниже описывается идеальное поведение.

Операции чтения из RTV возвращают значение 0 для отсутствующих плиток, а операции записи отклоняются. Такое идеальное определение обработки записи не требуется для [второго уровня](tier-2.md). Операции записи в несопоставленные плитки могут оказаться в кэше, откуда данные могут взять последующие операции чтения.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Конвейерный доступ к потоковым ресурсам](pipeline-access-to-streaming-resources.md)

 

 




