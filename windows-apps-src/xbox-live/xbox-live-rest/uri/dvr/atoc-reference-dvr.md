---
title: Коды URI DVR для игр
assetID: 472f705e-bf28-7894-b1ba-80933d8746a6
permalink: en-us/docs/xboxlive/rest/atoc-reference-dvr.html
description: " Коды URI DVR для игр"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: b4bfd6e51efce4c6ec85db99a10a44a776dcb840
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57617849"
---
# <a name="game-dvr-uris"></a>Коды URI DVR для игр
 
В этом разделе представлены сведения о универсальный идентификатор ресурса (URI) адреса и связанных методов протокола передачи гипертекста (HTTP) от службы Xbox Live для *DVR для игр*.
 
Только консоли можно записать сообщение игр, но любое устройство, которое можно получить доступ к можно отобразить клипа.
 
В зависимости от функции в URI домены, для таких URI — это:
 
   *  gameclipsmetadata.xboxlive.com 
   *  gameclipstransfer.xboxlive.com 
  
<a id="ID4EZB"></a>

 
## <a name="in-this-section"></a>В этом разделе

[/ public/scids / {scid} / отсекает](uri-publicscidclips.md)

&nbsp;&nbsp;Клипов открытый доступ. Это фактически можно указать код URI в двух формах `/public/scids/{scid}/clips` и `/public/titles/{titleId}/clips`. Ниже приведены дополнительные сведения.

[/ {uri}](uri-uri.md)

&nbsp;&nbsp;Доступ к данным игр клипов.

[/ users/me/scids / {scid} / отсекает](uri-usersmescidclips.md)

&nbsp;&nbsp;Первоначальный доступ отправьте запрос.

[/users/me/scids/{scid}/clips/{gameClipId}](uri-usersmescidclipsgameclipid.md)

&nbsp;&nbsp;Доступ к данным игр клипов и метаданные.

[/Users/ {ownerId} / отсекает](uri-usersowneridclips.md)

&nbsp;&nbsp;Список доступа пользователя клипов.

[/users/{ownerId}/scids/{scid}/clips/{gameClipId}](uri-usersowneridscidclipsgameclipid.md)

&nbsp;&nbsp;Доступ к игр клип из системы, если известны все идентификаторы для ее поиска.
 
<a id="ID4EOC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EQC"></a>

 
##### <a name="parent"></a>Parent 

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)

   