---
title: PeopleList (JSON)
assetID: ac538652-c10c-44e5-c1e3-5314ebe8ba83
permalink: en-us/docs/xboxlive/rest/json-peoplelist.html
description: " PeopleList (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 1f9ab412088707752d62cc20fd54da2639f26ddc
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57613329"
---
# <a name="peoplelist-json"></a>PeopleList (JSON)
Коллекция [Person](json-person.md) объектов. 
<a id="ID4ER"></a>

 
## <a name="peoplelist"></a>PeopleList
 
Объект PeopleList имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| people| Массив [Person](json-person.md)| [Person](json-person.md) объекты, составляющие списка людей.| 
| totalCount| 32-разрядного целого числа без знака| Общее число [Person](json-person.md) объектов, доступных в наборе. Это значение может использоваться клиентами для разбиения на страницы, так как он представляет размер всего набора, не только последнего ответа. Пример значения: 680.| 
  
<a id="ID4EAC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "people": [
        {
            "xuid": "2603643534573573",
            "isFavorite": true,
            "isFollowingCaller": false,
            "socialNetworks": ["LegacyXboxLive"]
        },
        {
            "xuid": "2603643534573572",
            "isFavorite": true,
            "isFollowingCaller": false,
            "socialNetworks": ["LegacyXboxLive"]
        },
        {
            "xuid": "2603643534573577",
            "isFavorite": false,
            "isFollowingCaller": false
        },
    ],
    "totalCount": 3
}
    
```

  
<a id="ID4EJC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ELC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4EVC"></a>

 
##### <a name="reference"></a>Справочные материалы 

[GET (/users/{ownerId}/people)](../uri/people/uri-usersowneridpeopleget.md)

 [POST (/users/ {ownerId} / people/xuids)](../uri/people/uri-usersowneridpeoplexuidspost.md)

   