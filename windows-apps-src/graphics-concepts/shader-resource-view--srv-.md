---
title: "Представление ресурсов шейдера (SRV) и представления неупорядоченного доступа (UAV)"
description: "Представления ресурсов шейдеров обычно оборачивают текстуры в особый формат, чтобы шейдеры имели к ним доступ. Представления неупорядоченного доступа имеют сходную функциональность, но обеспечивают чтение и запись в текстуру (или другой ресурс) в любом порядке."
ms.assetid: 4505BCD2-0EDA-40F2-887C-EC081FE32E8F
keywords:
- "Представление ресурсов шейдера (SRV)"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 4ce8e7a5d17656545cf047c2ab21cda5c5fbb4f0
ms.lasthandoff: 02/07/2017

---

# <a name="shader-resource-view-srv-and-unordered-access-view-uav"></a>Представление ресурсов шейдера (SRV) и представления неупорядоченного доступа (UAV)


Представления ресурсов шейдеров обычно оборачивают текстуры в особый формат, чтобы шейдеры имели к ним доступ. Представления неупорядоченного доступа имеют сходную функциональность, но обеспечивают чтение и запись в текстуру (или другой ресурс) в любом порядке.

Оборачивание одной текстуры, пожалуй, является простейшей формой представления ресурсов шейдера. Более сложный пример это коллекция подресурсов (отдельные массивы, плоскости или цвета из текстуры с MIP-картой), 3D-текстуры, цветовые градиенты 1D-текстуры и т. д.

Представления неупорядоченного доступа немного дороже с точки зрения производительности, но позволяют, например, вести запись в текстуру одновременно с чтением ее данных. Таким образом графический конвейер может повторно использовать обновленную текстуру в других целях . Представления ресурсов шейдера используются только для чтения (это наиболее распространенный метод применения ресурсов).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Представления](views.md)

 

 




