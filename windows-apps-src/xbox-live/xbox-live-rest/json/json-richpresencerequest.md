---
title: RichPresenceRequest (JSON)
assetID: 599266be-f747-0be1-fadf-f8e0262dc27f
permalink: en-us/docs/xboxlive/rest/json-richpresencerequest.html
description: " RichPresenceRequest (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 4c49da63ecd091a886a68f508af09e33fb9c58ac
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57654129"
---
# <a name="richpresencerequest-json"></a>RichPresenceRequest (JSON)
Запрос сведений о том, какие сведения богатыми возможностями присутствия должны использоваться. 
<a id="ID4EN"></a>

 
## <a name="richpresencerequest"></a>RichPresenceRequest
 
Объект RichPresenceRequest имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| id| Строка| <b>FriendlyName</b> богатыми возможностями присутствия строки для использования.| 
| scid| Строка| Scid, сообщающего нам, где определяются строки богатыми возможностями присутствия.| 
| params| Массив строк| Массив <b>friendlyName</b> строки с помощью которого до конца строки богатыми возможностями присутствия. Необходимо указать только перечисления понятных имен, не stats. Если оставить этот пустым удалит все предыдущие значения.| 
  
<a id="ID4EDC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
      id:"playingMapWeapon",
      scid:"abba0123-08ba-48ca-9f1a-21627b189b0f",
    }
    
```

  
<a id="ID4EMC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EOC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   