---
title: /users/xuid({xuid})/groups/{moniker}/broadcasting/count
assetID: 535c8d46-7001-c31e-3e9d-82ad275095ae
permalink: en-us/docs/xboxlive/rest/uri-usersxuidgroupsmonikerbroadcastingcount.html
description: " /users/xuid({xuid})/groups/{moniker}/broadcasting/count"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 7a39bc9c3302ba26949700774997355a216fe70d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651359"
---
# <a name="usersxuidxuidgroupsmonikerbroadcastingcount"></a>/users/xuid({xuid})/groups/{moniker}/broadcasting/count
Обращений к число пользователям широковещательной рассылки, указанным моникером группы связанных с XUID, который отображается в URI. Доменом для таких URI является `userpresence.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| Строка| Xbox пользователя идентификатор (XUID) пользователя, связанные с XUIDs в группе.| 
| моникер| Строка| Строка, определяющая группу пользователей. Только принятые моникер в настоящее время является «Люди», с заглавной буквы «P».| 
  
<a id="ID4E4B"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[GET (/ users/xuid ({xuid}) /groups/ {моникер} / широковещательной рассылки/количество)](uri-usersxuidgroupsmonikerbroadcastingcountget.md)

&nbsp;&nbsp;Извлекает число пользователей широковещательной рассылки, указанных специальным именем группы, связанные с XUID, который отображается в URI.
 
<a id="ID4EHC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EJC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI присутствия](atoc-reference-presence.md)

   