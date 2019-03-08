---
title: /users/xuid({xuid})/groups/{moniker}/broadcasting
assetID: cf8319f6-46a2-b263-ea4c-f1ce403b571b
permalink: en-us/docs/xboxlive/rest/uri-usersxuidgroupsmonikerbroadcasting.html
description: " /users/xuid({xuid})/groups/{moniker}/broadcasting"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 98eaa60204e3c98eb1b09a13372f7b0c084a6608
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651469"
---
# <a name="usersxuidxuidgroupsmonikerbroadcasting"></a>/users/xuid({xuid})/groups/{moniker}/broadcasting
Обращений к присутствия запись пользователям широковещательной рассылки, указанным моникером группы связанных с XUID, который отображается в URI. Доменом для таких URI является `userpresence.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| Строка| Xbox пользователя идентификатор (XUID) пользователя, связанные с XUIDs в группе.| 
| моникер| Строка| Строка, определяющая группу пользователей. Только принятые моникер в настоящее время является «Люди», с заглавной буквы «P».| 
  
<a id="ID4E4B"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (/groups/ xuid ({xuid}) / users / {моникер} / широковещательная рассылка)](uri-usersxuidgroupsmonikerbroadcastingget.md)

&nbsp;&nbsp;Извлекает запись присутствия широковещательной рассылки пользователям, указанным моникером группы, связанные с XUID, который отображается в URI.
 
<a id="ID4EHC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EJC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI присутствия](atoc-reference-presence.md)

   