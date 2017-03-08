---
title: "Плитки в буфере"
description: "Ресурс буфера разделен на плитки размером 64 КБ, причем в последней плитке имеется пустое пространство на случай, если размер не будет кратен 64 КБ."
ms.assetid: 577DC6B0-F373-4748-AD80-2784C597C366
keywords:
- "Плитки в буфере"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 2382974de7146f4ba64c2422a01c4d3d297a8977
ms.lasthandoff: 02/07/2017

---

# <a name="buffer-tiling"></a>Плитки в буфере


Ресурс [буфера](introduction-to-buffers.md) разделен на плитки размером 64 КБ, причем в последней плитке имеется пустое пространство на случай, если размер не будет кратен 64 КБ.

Для разделения структурированных буферов на плитки буферы не должны иметь ограничений шага. Однако можно пожертвовать возможной оптимизацией производительности оборудования для использования объекта [**StructuredBuffers**](https://msdn.microsoft.com/library/windows/desktop/ff471514), если все равно разделить буферы на плитки.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Размещение плиток в области потокового ресурса](how-a-streaming-resource-s-area-is-tiled.md)

 

 





