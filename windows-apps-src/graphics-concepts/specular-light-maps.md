---
title: Карты отраженного света
description: При освещении источником света на блестящих объектах из материалов с высокой отражательной способностью будут блики.
ms.assetid: 9B3AC5EC-DDAA-4671-BC81-0E3C79D87A81
keywords:
- Карты отраженного света
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 6bd9db0afa914ef7a56dbd55c938129b86a43743
ms.sourcegitcommit: b7e3d222e229cdbf04e837fcb94fb7d84a93de09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5611339"
---
# <a name="specular-light-maps"></a>Карты отраженного света


При падении света от источников на блестящие объекты, в которых используются материалы с высокой отражающей способностью, на этих объектах появляются блики. Иногда можно получить более точные блики, применив к графическим примитивам карты отраженного света, вместо использования бликов, формируемых модулем освещения.

Для сопоставления отраженного света, добавьте карту отраженного света к текстуре примитива, затем смодулируйте (умножьте результат на) картой света RGB- цвета.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Сопоставление света с текстурами](light-mapping-with-textures.md)

 

 




