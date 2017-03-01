---
title: "Карты отраженного света"
description: "При освещении источником света на блестящих объектах из материалов с высокой отражательной способностью будут блики."
ms.assetid: 9B3AC5EC-DDAA-4671-BC81-0E3C79D87A81
keywords:
- "Карты отраженного света"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 25c5bafd10734439ca1fdf3451be2510525d06be
ms.lasthandoff: 02/07/2017

---

# <a name="specular-light-maps"></a>Карты отраженного света


При освещении источником света на блестящих объектах из материалов с высокой отражательной способностью будут блики. Иногда можно получить более точные блики, применив к графическим примитивам карты отраженного света, вместо использования бликов, формируемых модулем освещения.

Для сопоставления отраженного света, добавьте карту отраженного света к текстуре примитива, затем смодулируйте (умножьте результат на) картой света RGB- цвета.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Сопоставление света с текстурами](light-mapping-with-textures.md)

 

 





