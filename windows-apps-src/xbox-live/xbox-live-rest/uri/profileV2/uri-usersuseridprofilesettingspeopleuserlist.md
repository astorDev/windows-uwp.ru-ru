---
title: /users/{userId}/profile/settings/people/{userList}?settings={settings}
assetID: 0ba20eba-f0ab-28ab-61d3-b4f9e4c07bc5
permalink: en-us/docs/xboxlive/rest/uri-usersuseridprofilesettingspeopleuserlist.html
description: " /users/{userId}/profile/settings/people/{userList}?settings={settings}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 24b58c817156a7c372a8e6acfab895e6b7c51207
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57636969"
---
# <a name="usersuseridprofilesettingspeopleuserlistsettingssettings"></a>/users/{userId}/profile/settings/people/{userList}?settings={settings}
Доступ к профилям пользователя или пользователей с поддержкой моникер людей. Доменом для таких URI является `profile.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| Идентификатор пользователя| Строка| Может быть либо «xuid(12345)», «gt(myGamertag)» или «me».| 
| userList| Строка| Именованный список людей, чтобы получить параметры. Сейчас люди — поддерживается только список.| 
  
<a id="ID4E1B"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[GET (/users/{userId}/profile/settings/people/{userList})](uri-usersuseridprofilesettingspeopleuserlistget.md)

&nbsp;&nbsp;Получить профиль для пользователя или пользователей с моникером людей поддержки.
 
<a id="ID4EEC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EGC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI профилей](atoc-reference-profiles.md)

 [Профиль (JSON)](../../json/json-profile.md)

   