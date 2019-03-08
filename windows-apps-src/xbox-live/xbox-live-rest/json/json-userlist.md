---
title: UserList (JSON)
assetID: 894f5a39-4eed-0c5b-fc45-cb0097dc46fd
permalink: en-us/docs/xboxlive/rest/json-userlist.html
description: " UserList (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 46e5323f4eae8e91b61295c4112b5bacfc8a1759
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598499"
---
# <a name="userlist-json"></a>UserList (JSON)
Коллекция [пользователя](json-user.md) объектов. 
<a id="ID4ER"></a>

 
## <a name="userlist"></a>userList
 
Объект UserList имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| пользователи| Массив [пользователя (JSON)](json-user.md)| См. ниже пример JSON.| 
  
<a id="ID4EPB"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "users":
    [
        { "xuid":"12345" },
        { "xuid":"23456" }
    ] 
}
    
```

  
<a id="ID4EYB"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E1B"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   