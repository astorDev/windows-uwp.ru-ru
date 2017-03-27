---
title: "Форматы трафаретов не поддерживаются потоковыми ресурсами"
description: "Форматы, которые содержат трафарет, не поддерживаются с потоковыми ресурсами."
ms.assetid: 90A572A4-3C76-4795-BAE9-FCC72B5F07AD
keywords:
- "Форматы трафаретов не поддерживаются потоковыми ресурсами"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 96eb1ec610b5aba743caf92584f52f5785046155
ms.lasthandoff: 02/07/2017

---

# <a name="stencil-formats-not-supported-with-streaming-resources"></a>Форматы трафаретов не поддерживаются потоковыми ресурсами


Форматы, которые содержат трафарет, не поддерживаются с потоковыми ресурсами.

Форматы, содержащие трафарет: DXGI\_FORMAT\_D24\_UNORM\_S8\_UINT (и связанные форматы в семействе R24G8) и DXGI\_FORMAT\_D32\_FLOAT\_S8X24\_UINT (и связанные форматы в семействе R32G8X24).

Некоторые реализации хранят данные о глубине и трафарете в отдельных распределениях, другие хранят их вместе. Управление плитками для этих двух схем будет отличаться, в одном API-интерфейсе нельзя объединить или рационализировать эти различия. Мы рекомендуем в будущем оборудовании поддерживать независимые поверхности глубины и трафарета, каждую с независимой разбивкой по плиткам.

32-разрядная буфер глубины будет содержать плитки 128x128, а 8-разрядный трафарет — плитки 256x256. Таким образом приложения будут иметь дело с рассогласованием формы плиток между буферами глубины и трафарета. Однако та же проблема уже существует с различными форматами целевой поверхности прорисовки.

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Потоковые ресурсы: совместное использование процессов и устройств](streaming-resource-cross-process-and-device-sharing.md)

 

 




