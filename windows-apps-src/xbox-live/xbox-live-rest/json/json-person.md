---
title: Person (JSON)
assetID: b49234b1-03cd-f16e-c293-c74174382167
permalink: en-us/docs/xboxlive/rest/json-person.html
description: " Person (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 175d66ffc7744ca8203fe7681fcb0167e150f012
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57640869"
---
# <a name="person-json"></a>Person (JSON)
Метаданные о одним человеком в системе людей. 
<a id="ID4EN"></a>

 
## <a name="person"></a>Person
 
Объект Person имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| xuid| Строка| Обязательный. Xbox пользователя идентификатор (XUID) в десятичном формате. Пример значения: 2603643534573573.| 
| isFavorite| Логическое значение| Обязательный. Это лицо, является ли, он заботится о многое другое. Так как пользователи могут иметь большое количество пользователей в свой список людей, избранных пользователей следует по приоритетам в интерфейсы и показано раньше других, которые не являются "Избранное".| 
| isFollowingCaller| Логическое значение| Необязательно. Ли он подписан пользователь, от лица которого API был выполнен вызов.| 
| socialNetworks| Массив строк| Необязательно. В какие внешние сети пользователь и это лицо имеют связь.| 
  
<a id="ID4EHC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "xuid": "2603643534573581",
    "isFavorite": false,
    "isFollowingCaller": false,
    "socialNetworks": ["LegacyXboxLive"]
}
    
```

  
<a id="ID4EQC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ESC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4E3C"></a>

 
##### <a name="reference"></a>Справочные материалы 

[/users/{ownerId}/people/{targetid}](../uri/people/uri-usersowneridpeopletargetid.md)

 [/Users/ {ownerId} / people/xuids](../uri/people/uri-usersowneridpeoplexuids.md)

   