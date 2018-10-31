---
title: Плитки в буфере
description: Ресурс буфера разделен на плитки размером 64КБ, причем в последней плитке имеется пустое пространство на случай, если размер не будет кратен 64КБ.
ms.assetid: 577DC6B0-F373-4748-AD80-2784C597C366
keywords:
- Плитки в буфере
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 1817f501962ccae4cfaf9c0ce075724abd5e7672
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5860037"
---
# <a name="buffer-tiling"></a>Плитки в буфере


Ресурс [буфера](introduction-to-buffers.md) разделен на плитки размером 64КБ, причем в последней плитке имеется пустое пространство на случай, если размер не будет кратен 64КБ.

Для разделения структурированных буферов на плитки буферы не должны иметь ограничений шага. Однако можно пожертвовать возможной оптимизацией производительности оборудования для использования объекта [**StructuredBuffers**](https://msdn.microsoft.com/library/windows/desktop/ff471514), если все равно разделить буферы на плитки.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Размещение плиток в области потокового ресурса](how-a-streaming-resource-s-area-is-tiled.md)

 

 




