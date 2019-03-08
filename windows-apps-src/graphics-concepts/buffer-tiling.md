---
title: Плитки в буфере
description: Ресурс буфера разделен на плитки размером 64 КБ, причем в последней плитке имеется пустое пространство на случай, если размер не будет кратен 64 КБ.
ms.assetid: 577DC6B0-F373-4748-AD80-2784C597C366
keywords:
- Плитки в буфере
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: f3e5e117e05cef478ede508240a6b1d1022dea70
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655729"
---
# <a name="buffer-tiling"></a>Плитки в буфере


[Буферный](introduction-to-buffers.md) ресурс разделен на плитки по 64 КБ. В последней плитке остается некоторое свободное пространство, если размер не кратен 64 КБ.

Для разделения структурированных буферов на плитки буферы не должны иметь ограничений шага. Однако можно пожертвовать возможной оптимизацией производительности оборудования для использования объекта [**StructuredBuffers**](https://msdn.microsoft.com/library/windows/desktop/ff471514), если все равно разделить буферы на плитки.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Как выполняется мозаичное заполнение области потоковой передачи ресурсов](how-a-streaming-resource-s-area-is-tiled.md)

 

 




