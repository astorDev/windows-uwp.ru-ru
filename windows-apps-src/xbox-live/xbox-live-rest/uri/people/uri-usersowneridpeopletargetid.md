---
title: /users/{ownerId}/people/{targetid}
assetID: 9dd19e75-3b48-d7e0-fc65-6760c15ddf62
permalink: en-us/docs/xboxlive/rest/uri-usersowneridpeopletargetid.html
description: " /users/{ownerId}/people/{targetid}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 6238996abdeaca9b7a9a7a20d3f1ae9702e95a73
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57661749"
---
# <a name="usersowneridpeopletargetid"></a>/users/{ownerId}/people/{targetid}
Обращается к человека, идентификатор целевого из коллекции людей вызывающей стороны. Доменом для таких URI является `social.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| ownerId| Строка| Идентификатор пользователя, ресурсом которого осуществляется. Должно соответствовать авторизованного пользователя. Возможными значениями являются «me», xuid({xuid}) или gt({gamertag}).| 
| targetID| Строка| Идентификатор пользователя, в которых данные извлекаются из списка людей владельца, идентификатор пользователя Xbox (XUID) или тег игрока. Примеры значений: xuid(2603643534573581), gt(SomeGamertag).| 
  
<a id="ID4EQB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[ПОЛУЧИТЬ](uri-usersowneridpeopletargetidget.md)

&nbsp;&nbsp;Получает пользователя по Идентификатору целевой объект из коллекции людей вызывающей стороны.
 
<a id="ID4E1B"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E3B"></a>

 
##### <a name="parent"></a>Parent 

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)

   