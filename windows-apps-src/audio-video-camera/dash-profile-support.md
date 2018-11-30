---
ms.assetid: 3E0FBB43-F6A4-4558-AA89-20E7760BA73F
description: В этой статье перечислены профили Dynamic Adaptive Streaming через HTTP (DASH), которые поддерживаются для приложений UWP.
title: Поддержка профиля Dynamic Adaptive Streaming over HTTP (DASH)
ms.date: 02/15/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d680f7d4a3510f66cba74d1c8b30d8883b07369a
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8214766"
---
# <a name="dynamic-adaptive-streaming-over-http-dash-profile-support"></a>Поддержка профиля Dynamic Adaptive Streaming over HTTP (DASH)


## <a name="supported-dash-profiles"></a>Поддерживаемые профили DASH
В следующей таблице перечислены профили DASH, которые поддерживаются для приложений UWP.

|Тег | Тип манифеста | Примечания|Июльский выпуск Windows 10|Windows 10 (версия 1511)|Windows 10 (версия 1607) |Windows 10 (версия 1607) |Windows 10 (версия 1703)|
|----------------|------|-------|-----------|--------------|---------|-------|--------|
|urn:mpeg&#58;dash:profile:isoff-live:2011 | Статический |     |Поддерживается            |  Поддерживается              | Поддерживается        |Поддерживается| Поддерживается|
|urn:mpeg&#58;dash:profile:isoff-main:2011 |        | Наилучший | Поддерживается            |  Поддерживается              | Поддерживается        |Поддерживается| Поддерживается|
|urn:mpeg&#58;dash:profile:isoff-live:2011 | Динамический | В сегментных шаблонах $Time$ поддерживается, но $Number$ не поддерживается | Не поддерживается            | Не поддерживается              | Не поддерживается        |Не поддерживается| Поддерживается|


## <a name="unsupported-dash-profiles"></a>Неподдерживаемые профили DASH
Профили, не указанные в таблице выше, не поддерживаются, включая, помимо прочего, следующие:

* urn:mpeg&#58;dash:profile:full:2011
* urn:mpeg&#58;dash:profile:isoff-on-demand:2011
* urn:mpeg&#58;dash:profile:mp2t-main:2011
* urn:mpeg&#58;dash:profile:mp2t-simple:2011


## <a name="related-topics"></a>Еще по теме:

* [Воспроизведение мультимедиа](media-playback.md)
* [Адаптивная потоковая передача](adaptive-streaming.md)
 

 




