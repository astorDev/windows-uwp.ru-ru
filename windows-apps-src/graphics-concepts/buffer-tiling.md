---
title: Плитки в буфере
description: Ресурс буфера разделен на плитки размером 64 КБ, причем в последней плитке имеется пустое пространство на случай, если размер не будет кратен 64 КБ.
ms.assetid: 577DC6B0-F373-4748-AD80-2784C597C366
keywords:
- Плитки в буфере
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 34201c889ed984b27d50126bd2a04e9b320a17a3
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370655"
---
# <a name="buffer-tiling"></a>Плитки в буфере


[Буферный](introduction-to-buffers.md) ресурс разделен на плитки по 64 КБ. В последней плитке остается некоторое свободное пространство, если размер не кратен 64 КБ.

Для разделения структурированных буферов на плитки буферы не должны иметь ограничений шага. Однако можно пожертвовать возможной оптимизацией производительности оборудования для использования объекта [**StructuredBuffers**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-structuredbuffer), если все равно разделить буферы на плитки.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Как выполняется мозаичное заполнение области потоковой передачи ресурсов](how-a-streaming-resource-s-area-is-tiled.md)

 

 




