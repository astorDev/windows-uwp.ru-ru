---
author: drewbatgit
ms.assetid: 3E0FBB43-F6A4-4558-AA89-20E7760BA73F
description: "В этой статье перечислены профили Dynamic Adaptive Streaming через HTTP (DASH), которые поддерживаются для приложений UWP."
title: "Поддержка профиля Dynamic Adaptive Streaming over HTTP (DASH)"
ms.author: drewbat
ms.date: 02/15/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 9ca8f2d1783a73ae38fed1d3ee1e58b809ddd2aa
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="dynamic-adaptive-streaming-over-http-dash-profile-support"></a>Поддержка профиля Dynamic Adaptive Streaming over HTTP (DASH)
В следующей таблице перечислены профили DASH, которые поддерживаются для приложений UWP.



|Тег | Тип манифеста | Примечания|Июльский выпуск Windows 10|Windows 10 (версия 1511)|Windows 10 (версия 1607) |Windows 10 (версия 1607) |Windows 10 (версия 1703)|
|----------------|------|-------|-----------|--------------|---------|-------|--------|
|urn:mpeg:dash:profile:isoff-live:2011 | Статический |     |Поддерживается            |  Поддерживается              | Поддерживается        |Поддерживается| Поддерживается|
|urn:mpeg:dash:profile:isoff-main:2011 |        | Наилучший | Поддерживается            |  Поддерживается              | Поддерживается        |Поддерживается| Поддерживается|
|urn:mpeg:dash:profile:isoff-live:2011 | Динамический | В сегментных шаблонах $Time$ поддерживается, но $Number$ не поддерживается | Не поддерживается            | Не поддерживается              | Не поддерживается        |Не поддерживается| Поддерживается|


## <a name="related-topics"></a>Еще по теме

* [Воспроизведение мультимедиа](media-playback.md)
* [Адаптивная потоковая передача](adaptive-streaming.md)
 

 




