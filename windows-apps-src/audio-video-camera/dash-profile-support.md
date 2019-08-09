---
ms.assetid: 3E0FBB43-F6A4-4558-AA89-20E7760BA73F
description: В этой статье перечислены профили Dynamic Adaptive Streaming через HTTP (DASH), которые поддерживаются для приложений UWP.
title: Поддержка профиля Dynamic Adaptive Streaming over HTTP (DASH)
ms.date: 02/15/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 268020c06be57d8ac300f1202046e52b6e1d2507
ms.sourcegitcommit: 789bfe3756c5c47f7324b96f482af636d12c0ed3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68867385"
---
# <a name="dynamic-adaptive-streaming-over-http-dash-profile-support"></a>Поддержка профиля Dynamic Adaptive Streaming over HTTP (DASH)


## <a name="supported-dash-profiles"></a>Поддерживаемые профили DASH
В следующей таблице перечислены профили DASH, которые поддерживаются для приложений UWP.

|Tag | Тип манифеста | Примечания|Июльский выпуск Windows 10|Windows 10 (версия 1511)|Windows 10 (версия 1607) |Windows 10 (версия 1607) |Windows 10 (версия 1703)| Windows 10, версия 1809
|----------------|------|-------|-----------|--------------|---------|-------|--------|--------|
|urn:mpeg&#58;dash:profile:isoff-live:2011 | Static |     |Поддерживается            |  Поддерживается              | Поддерживается        |Поддерживается| Поддерживается| Поддерживается|
|urn:mpeg&#58;dash:profile:isoff-main:2011 |        | Наилучший | Поддерживается            |  Поддерживается              | Поддерживается        |Поддерживается| Поддерживается| Поддерживается|
|urn:mpeg&#58;dash:profile:isoff-live:2011 | Динамический | В сегментных шаблонах $Time$ поддерживается, но $Number$ не поддерживается | Не поддерживается            | Не поддерживается              | Не поддерживается        |Не поддерживается| Поддерживается| Поддерживается|
|urn:mpeg&#58;dash:profile:isoff-on-demand:2011 |        |  | Не поддерживается            |  Не поддерживается              | Не поддерживается        |Не поддерживается| Не поддерживается| Поддерживается|


## <a name="unsupported-dash-profiles"></a>Неподдерживаемые профили DASH
Профили, не указанные в таблице выше, не поддерживаются, включая, помимо прочего, следующие:

* urn:mpeg&#58;dash:profile:full:2011
* urn:mpeg&#58;dash:profile:mp2t-main:2011
* urn:mpeg&#58;dash:profile:mp2t-simple:2011


## <a name="related-topics"></a>См. также

* [Воспроизведение мультимедиа](media-playback.md)
* [Адаптивная потоковая передача](adaptive-streaming.md)
 

 




