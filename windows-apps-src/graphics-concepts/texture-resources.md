---
title: "Ресурсы текстур"
description: "Текстуры — это тип ресурса, используемого для отрисовки."
ms.assetid: 016F6CDA-D361-4E6B-BA99-49E915A19364
keywords: "Ресурсы текстур"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.openlocfilehash: c82079666f2ee32a4e858414c824142d2bcff932
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="texture-resources"></a>Ресурсы текстур


Текстуры — это тип ресурса, используемого для отрисовки.

## <a name="span-idrenderingwithtextureresourcesspanspan-idrenderingwithtextureresourcesspanspan-idrenderingwithtextureresourcesspanrendering-with-texture-resources"></a><span id="Rendering_with_Texture_Resources"></span><span id="rendering_with_texture_resources"></span><span id="RENDERING_WITH_TEXTURE_RESOURCES"></span>Отрисовка с помощью ресурсов текстур


Direct3D поддерживает наложения нескольких текстур посредством концепции шагов текстурирования. Каждый шаг текстурирования содержит текстуру и операции, которые можно выполнить с текстурой. Текстуры в шагах текстурирования образуют набор текущих текстур. См. раздел [Наложение текстур](texture-blending.md). Состояние каждой текстуры инкапсулируется в шаге текстуры.

Приложения также могут задавать перспективу текстур и состояния фильтрации текстур. См. раздел [Фильтрация текстур](texture-filtering.md).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Текстуры](textures.md)

 

 




