---
title: Представление ресурсов шейдера (SRV) и представления неупорядоченного доступа (UAV)
description: Представления ресурсов шейдера обычно оборачивают текстуры в формат, позволяющий шейдерам осуществлять к ним доступ. Представления неупорядоченного доступа имеют сходную функциональность, но обеспечивают чтение и запись в текстуру (или другой ресурс) в любом порядке.
ms.assetid: 4505BCD2-0EDA-40F2-887C-EC081FE32E8F
keywords:
- Представление ресурсов шейдера (SRV)
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: 213ff4e2a120c91211720d887ab8f777b9265106
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1043543"
---
# <a name="shader-resource-view-srv-and-unordered-access-view-uav"></a>Представление ресурсов шейдера (SRV) и представления неупорядоченного доступа (UAV)


Представления ресурсов шейдера обычно оборачивают текстуры в формат, позволяющий шейдерам осуществлять к ним доступ. Представления неупорядоченного доступа имеют сходную функциональность, но обеспечивают чтение и запись в текстуру (или другой ресурс) в любом порядке.

Оборачивание одной текстуры, пожалуй, является простейшей формой представления ресурсов шейдера. Более сложный пример это коллекция подресурсов (отдельные массивы, плоскости или цвета из текстуры с MIP-картой), 3D-текстуры, цветовые градиенты 1D-текстуры и т. д.

Представления неупорядоченного доступа немного дороже с точки зрения производительности, но позволяют, например, вести запись в текстуру одновременно с чтением ее данных. Таким образом графический конвейер может повторно использовать обновленную текстуру в других целях . Представления ресурсов шейдера используются только для чтения (это наиболее распространенный метод применения ресурсов).

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Представления](views.md)

 

 



