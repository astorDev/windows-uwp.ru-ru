---
title: Ресурсы текстур
description: Текстуры — это тип ресурса, используемого для отрисовки.
ms.assetid: 016F6CDA-D361-4E6B-BA99-49E915A19364
keywords:
- Ресурсы текстур
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: 7991d4e611bc27c59f9d63b5ff1d34823f60ed60
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1044463"
---
# <a name="texture-resources"></a>Ресурсы текстур


Текстуры — это тип ресурса, используемого для отрисовки.

## <a name="span-idrenderingwithtextureresourcesspanspan-idrenderingwithtextureresourcesspanspan-idrenderingwithtextureresourcesspanrendering-with-texture-resources"></a><span id="Rendering_with_Texture_Resources"></span><span id="rendering_with_texture_resources"></span><span id="RENDERING_WITH_TEXTURE_RESOURCES"></span>Отрисовка с помощью ресурсов текстур


Direct3D поддерживает наложения нескольких текстур посредством концепции шагов текстурирования. Каждый шаг текстурирования содержит текстуру и операции, которые можно выполнить с текстурой. Текстуры в шагах текстурирования образуют набор текущих текстур. См. раздел [Наложение текстур](texture-blending.md). Состояние каждой текстуры инкапсулируется в шаге текстуры.

Приложения также могут задавать перспективу текстур и состояния фильтрации текстур. См. раздел [Фильтрация текстур](texture-filtering.md).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Текстуры](textures.md)

 

 




