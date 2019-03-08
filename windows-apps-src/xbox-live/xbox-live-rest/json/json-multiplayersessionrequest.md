---
title: MultiplayerSessionRequest (JSON)
assetID: 2e311c6d-3427-5a39-1989-06dc08483057
permalink: en-us/docs/xboxlive/rest/json-multiplayersessionrequest.html
description: " MultiplayerSessionRequest (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 18929c060adeae47f0305422dd312e7410f93981
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57628349"
---
# <a name="multiplayersessionrequest-json"></a>MultiplayerSessionRequest (JSON)
Объект запроса JSON, переданный для операции на **MultiplayerSession** объекта. 
<a id="ID4EQ"></a>

  
 
Объект MultiplayerSessionRequest JSON имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| Константы| Объект| Параметры только для чтения, которые объединяются с помощью шаблона сеанса для создания константы для сеанса. | 
| свойства | Объект | Изменения объединены в свойствах сеанса.| 
| Members.Me | Объект| Константы и свойства, которые очень как их аналоги верхнего уровня. Любой метод PUT пользователь должен быть участником сеанса и добавляет пользователя, при необходимости. Если «me» задается как значение null, элемент, выполняющего запрос удаляется из сеанса. | 
| члены | Объект| Другие объекты, представляющие пользователям добавлять к сеансу, ключом которого является отсчитываемый от нуля индекс. Число элементов в запросе всегда начинается с 0, даже если сеанс уже элементов. Элементы добавляются в сеанс, в порядке, в котором они появляются в запросе. Свойства элементов можно задать только для пользователя, которому они принадлежат. | 
| . | Объект| Значения, указывающие, обновления и дополнения к сеансу набор участников связанным сервером. Если сервер указан как null, запись этого сервера удаляется из сеанса. | 
  
<a id="ID4EZ"></a>

 
## <a name="request-structure"></a>Структура запроса
 

```json
{
  "constants": { /* Property Bag */ },
  "properties": { /* Property Bag */ },
  "members": {
    // Requires a service principal. Existing members can be deleted by index.
    // Not available on large sessions.
    "5": null,

    // Reservation requests must start with zero. New users will get added in order to the end of the session's member list.
    // Large sessions don't support reservations.
    "reserve_0": {
      "constants": { /* Property Bag */ }
    },
    "reserve_1": {
      "constants": { /* Property Bag */ }
    },

    // Requires a user principal with a xuid claim. Can be 'null' to remove myself from the session.
    "me": {
      "constants": { /* Property Bag */ },
      "properties": { /* Property Bag */ },
    }
  },

  // Requires a server principal.
  "servers": {
    // Can be any name. The value can be 'null' to remove the server from the session.
    "name": {
      "constants": {  /* Property Bag */ },
      "properties": {  /* Property Bag */ }
    }
  }
}
```

  
<a id="ID4EAB"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ECB"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4EMB"></a>

 
##### <a name="reference"></a>Справочные материалы 

[MultiplayerSession (JSON)](json-multiplayersession.md)

 [PUT (/serviceconfigs/ {scid} /sessions/ /sessiontemplates/ {sessionTemplateName} {sessionName})](../uri/sessiondirectory/uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnameput.md)

   