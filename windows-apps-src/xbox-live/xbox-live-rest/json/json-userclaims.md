---
title: UserClaims (JSON)
assetID: f88d5ee0-2875-fcfb-3098-3cd6afce8748
permalink: en-us/docs/xboxlive/rest/json-userclaims.html
description: " UserClaims (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 21b4322d002747145c3b09e0f3cd7eb03874380b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57623349"
---
# <a name="userclaims-json"></a>UserClaims (JSON)
Возвращает сведения о текущем пользователе прошедшего проверку подлинности. 
<a id="ID4EN"></a>

 
## <a name="userclaims"></a>UserClaims
 
Объект UserClaims имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| тег игрока| Строка| тег игрока пользователя.| 
| xuid| 64-разрядного целого числа без знака| Идентификатор Xbox пользователя (XUID), пользователь.| 
  
<a id="ID4EZB"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
   "xuid" : 2533274790412952,
   "gamertag" : "gamer"

}
    
```

  
<a id="ID4ECC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EEC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   