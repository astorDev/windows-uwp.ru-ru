---
title: /users/xuid({xuid})/groups/{moniker}
assetID: 7c73236b-95ee-723b-e5e0-68252c953e14
permalink: en-us/docs/xboxlive/rest/uri-usersxuidgroupsmoniker.html
description: " /users/xuid({xuid})/groups/{moniker}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 25ddc8120f05f04d5285fbbe4efc5a41f98265ef
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57617559"
---
# <a name="usersxuidxuidgroupsmoniker"></a>/users/xuid({xuid})/groups/{moniker}
Обращается к PresenceRecord для группы. Доменом для таких URI является `userpresence.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| Строка| Xbox пользователя идентификатор (XUID) пользователя, связанные с XUIDs в группе.| 
| моникер| Строка| Строка, определяющая группу пользователей. Только принятые моникер в настоящее время является «Люди», с заглавной буквы «P».| 
  
<a id="ID4E4B"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[GET (/ users/xuid ({xuid}) /groups/ {моникер})](uri-usersxuidgroupsmonikerget.md)

&nbsp;&nbsp;Получает PresenceRecord для группы.
 
<a id="ID4EHC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EJC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI присутствия](atoc-reference-presence.md)

   