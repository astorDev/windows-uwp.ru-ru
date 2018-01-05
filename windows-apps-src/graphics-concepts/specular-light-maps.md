---
title: "Карты отраженного света"
description: "При освещении источником света на блестящих объектах из материалов с высокой отражательной способностью будут блики."
ms.assetid: 9B3AC5EC-DDAA-4671-BC81-0E3C79D87A81
keywords: "Карты отраженного света"
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: 6da5292396a334e50c61fb4638334aae27581d7d
ms.sourcegitcommit: c80b9e6589a1ee29c5032a0b942e6a024c224ea7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="specular-light-maps"></a>Карты отраженного света


При освещении источником света на блестящих объектах из материалов с высокой отражательной способностью будут блики. Иногда можно получить более точные блики, применив к графическим примитивам карты отраженного света, вместо использования бликов, формируемых модулем освещения.

Для сопоставления отраженного света, добавьте карту отраженного света к текстуре примитива, затем смодулируйте (умножьте результат на) картой света RGB- цвета.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Сопоставление света с текстурами](light-mapping-with-textures.md)

 

 




