---
title: Коды URI сведений о присутствии
assetID: 4ba44d9c-8615-cacc-2eee-7ff5e7c74383
permalink: en-us/docs/xboxlive/rest/atoc-reference-presence.html
description: " Коды URI сведений о присутствии"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 1a46ecd48c2b0bf523ab234a5f20cf9ed6669e75
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57632609"
---
# <a name="presence-uris"></a>Коды URI сведений о присутствии
 
В этом разделе представлены сведения о универсальный идентификатор ресурса (URI) адреса и связанных методов протокола передачи гипертекста (HTTP) от службы Xbox Live для *присутствия*.
 
Эту службу можно использовать только игры, выполняющихся на Xbox 360, на устройстве Windows Phone или в Windows.
 
Доменом для таких URI является userpresence.xboxlive.com.
 
Можно подписаться на изменениях присутствия пользователя с помощью службы реального времени действия возврата.
 
<a id="ID4ERB"></a>

 
## <a name="in-this-section"></a>В этом разделе

[/ users/пакетной службы](uri-usersbatch.md)

&nbsp;&nbsp;Наличие доступа для группы пользователей.

[/ users/me](uri-usersme.md)

&nbsp;&nbsp;Доступ к присутствие текущего пользователя.

[/ users/me/группы / {моникер}](uri-usersmegroupsmoniker.md)

&nbsp;&nbsp;Обращается к PresenceRecord нашей группе.

[/users/xuid({xuid})](uri-usersxuid.md)

&nbsp;&nbsp;Доступ к наличие другого пользователя или клиента.

[/users/xuid({xuid})/devices/current/titles/current](uri-usersxuiddevicescurrenttitlescurrent.md)

&nbsp;&nbsp;Доступ к наличие заголовок или название пользователя.

[/groups/ xuid ({xuid}) / users / {моникер}](uri-usersxuidgroupsmoniker.md)

&nbsp;&nbsp;Обращается к PresenceRecord для группы.

[/groups/ xuid ({xuid}) / users / {моникер} / широковещательная рассылка](uri-usersxuidgroupsmonikerbroadcasting.md)

&nbsp;&nbsp;Обращений к присутствия запись пользователям широковещательной рассылки, указанным моникером группы связанных с XUID, который отображается в URI.

[/ users/xuid ({xuid}) /groups/ {моникер} / широковещательной рассылки/count](uri-usersxuidgroupsmonikerbroadcastingcount.md)

&nbsp;&nbsp;Обращений к число пользователям широковещательной рассылки, указанным моникером группы связанных с XUID, который отображается в URI.
 
<a id="ID4EMC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EOC"></a>

 
##### <a name="parent"></a>Parent 

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)

   