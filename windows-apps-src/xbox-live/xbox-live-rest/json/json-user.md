---
title: User (JSON)
assetID: dbc733e4-0348-0e3d-1f55-17b465e599d6
permalink: en-us/docs/xboxlive/rest/json-user.html
description: " User (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 5c1b3a34ef329696d51e615dd79d57783a132d05
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57641359"
---
# <a name="user-json"></a>User (JSON)
Содержит список лидеров пользовательских данных. 
<a id="ID4EN"></a>

 
## <a name="user"></a>Пользователь
 
Объект пользователя имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| тег игрока| Строка| Тег игрока проигрывателя (не более 15 символов). Клиент должен использовать это значение в пользовательском Интерфейсе при определении проигрыватель.| 
| Ранг| 32-разрядное знаковое целое число| Ранг пользователя относительно пользователя, запрашивающего данные списка лидеров.| 
| rating| Строка| Пользовательскую оценку.| 
| xuid| 64-разрядного целого числа без знака| Идентификатор Xbox пользователя (XUID), пользователь.| 
  
<a id="ID4EMC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{ 
   "gamertag":"TrueBlue402",
   "rank":2,
   "rating":"2:19:21.17",
   "xuid":1234567890123456 
}
    
```

  
<a id="ID4EVC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EXC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   