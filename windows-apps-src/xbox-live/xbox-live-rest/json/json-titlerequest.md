---
title: TitleRequest (JSON)
assetID: 43aeb6f9-726d-9260-e2ba-f005ea688bf1
permalink: en-us/docs/xboxlive/rest/json-titlerequest.html
description: " TitleRequest (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: a90f42c2f830ba6f04f77a1acaba067a2746a062
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57593799"
---
# <a name="titlerequest-json"></a>TitleRequest (JSON)
Запрос сведений о заголовок. 
<a id="ID4EN"></a>

 
## <a name="titlerequest"></a>TitleRequest
 
Объект TitleRequest имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| id| 32-разрядного целого числа без знака| Идентификатор заголовка.| 
| активность| [ActivityRequest](json-activityrequest.md)| Сведения, включая обширной информации о присутствии и мультимедиа, если он доступен в заголовок.| 
| Состояние| Строка| Что пользователь активно или нет. Это поле требуется пометить как неактивные пользователя. Значение по умолчанию является «активным».| 
| Размещение| Строка| Режим размещения заголовка. Возможные значения включают «full», «fill», «прикрепленного» или «фон». По умолчанию используется «full».| 
  
<a id="ID4EJC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
  id:"12341234",
  placement:"snapped",
  state:"active",
  activity:
  {
    richPresence:
    {
      id:"playingMapWeapon",
      scid:"82b11353-08ba-48ca-9f1a-21627b189b0f"
    }
  }
}
    
```

  
<a id="ID4ESC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EUC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4E5C"></a>

 
##### <a name="reference"></a>Справочные материалы 

[POST (/users/xuid({xuid})/devices/current/titles/current)](../uri/presence/uri-usersxuiddevicescurrenttitlescurrentpost.md)

   