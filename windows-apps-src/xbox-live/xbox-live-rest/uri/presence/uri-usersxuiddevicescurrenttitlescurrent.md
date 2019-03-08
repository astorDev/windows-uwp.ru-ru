---
title: /users/xuid({xuid})/devices/current/titles/current
assetID: f149c68b-9874-e348-4e1d-6acf5d305c49
permalink: en-us/docs/xboxlive/rest/uri-usersxuiddevicescurrenttitlescurrent.html
description: " /users/xuid({xuid})/devices/current/titles/current"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 0b5c17f1791d69ca8a4c902b6c37736c4da28a31
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57645669"
---
# <a name="usersxuidxuiddevicescurrenttitlescurrent"></a>/users/xuid({xuid})/devices/current/titles/current
Доступ к наличие заголовок или название пользователя. Доменом для таких URI является `userpresence.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| 64-разрядного целого числа без знака| Xbox пользователя идентификатор (XUID) целевого пользователя.| 
  
<a id="ID4EUB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[DELETE (/users/xuid({xuid})/devices/current/titles/current)](uri-usersxuiddevicescurrenttitlescurrentdelete.md)

&nbsp;&nbsp;Удалить наличие закрывающей title, а не ждать [PresenceRecord](../../json/json-presencerecord.md) истекает.

[POST (/users/xuid({xuid})/devices/current/titles/current)](uri-usersxuiddevicescurrenttitlescurrentpost.md)

&nbsp;&nbsp;Замените заголовок присутствие пользователя.
 
<a id="ID4EBC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EDC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI присутствия](atoc-reference-presence.md)

   