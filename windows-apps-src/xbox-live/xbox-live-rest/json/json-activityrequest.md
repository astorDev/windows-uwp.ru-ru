---
title: ActivityRequest (JSON)
assetID: 9eb03ab7-352c-4465-ec86-d544e76f63f9
permalink: en-us/docs/xboxlive/rest/json-activityrequest.html
description: " ActivityRequest (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 791a566d278b92aeb34ab36d38719b44e9cc6c8f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57628159"
---
# <a name="activityrequest-json"></a>ActivityRequest (JSON)
Запрос на сведения о богатыми возможностями присутствия один или несколько пользователей. 
<a id="ID4EN"></a>

 
## <a name="activityrequest"></a>ActivityRequest
 
Объект ActivityRequest имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| richPresence| [RichPresenceRequest](json-richpresencerequest.md)| Понятное имя строка богатыми возможностями присутствия, который должен использоваться.| 
| мультимедиа| MediaRequest| Сведения о мультимедиа для пользователя Просмотр или прослушивание.| 
  
<a id="ID4EVB"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    richPresence:
    {
      id:"playingMapWeapon",
      scid:"abba0123-08ba-48ca-9f1a-21627b189b0f"
    }
  }
    
```

  
<a id="ID4E5B"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EAC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   