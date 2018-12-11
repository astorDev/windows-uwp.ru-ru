---
title: Плитки в буфере
description: Ресурс буфера разделен на плитки размером 64КБ, причем в последней плитке имеется пустое пространство на случай, если размер не будет кратен 64КБ.
ms.assetid: 577DC6B0-F373-4748-AD80-2784C597C366
keywords:
- Плитки в буфере
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: f3e5e117e05cef478ede508240a6b1d1022dea70
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8920744"
---
# <a name="buffer-tiling"></a>Плитки в буфере


Ресурс [буфера](introduction-to-buffers.md) разделен на плитки размером 64КБ, причем в последней плитке имеется пустое пространство на случай, если размер не будет кратен 64КБ.

Для разделения структурированных буферов на плитки буферы не должны иметь ограничений шага. Однако можно пожертвовать возможной оптимизацией производительности оборудования для использования объекта [**StructuredBuffers**](https://msdn.microsoft.com/library/windows/desktop/ff471514), если все равно разделить буферы на плитки.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Размещение плиток в области потокового ресурса](how-a-streaming-resource-s-area-is-tiled.md)

 

 




