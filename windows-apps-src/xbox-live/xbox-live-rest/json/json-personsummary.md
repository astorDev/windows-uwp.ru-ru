---
title: PersonSummary (JSON)
assetID: 22fedb5f-5602-98d8-04a6-786fe3905921
permalink: en-us/docs/xboxlive/rest/json-personsummary.html
description: " PersonSummary (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: a787992507405a70185140e879be731d72806eff
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57612629"
---
# <a name="personsummary-json"></a>PersonSummary (JSON)
Коллекция [Person (JSON)](json-person.md) объектов. 
<a id="ID4ER"></a>

 
## <a name="personsummary"></a>PersonSummary
 
Объект PersonSummary имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| hasCallerMarkedTargetAsFavorite| Логическое значение| Ли вызывающий объект пометил целевой объект в список избранного. Примеры значений: true| 
| hasCallerMarkedTargetAsKnown| Логическое значение| Является ли вызывающий объект пометил целевой объект как известные. Примеры значений: true| 
| isCallerFollowingTarget| Логическое значение| Является ли вызывающий объект подписан целевой объект. Примеры значений: true| 
| isTargetFollowingCaller| Логическое значение| Является ли целевой объект подписан вызывающий объект. Примеры значений: true| 
| legacyFriendStatus| Строка| Состояние устаревших friend целевой объект как видны вызывающему объекту. Может быть «None», «MutuallyAccepted», «OutgoingRequest» или «IncomingRequest». Примеры значений «MutuallyAccepted»| 
| recentChangeCount| 32-разрядного целого числа без знака| Необязательно. Число последних изменений в социальным графом целевого объекта. Это значение будет существовать только в том случае, когда пользователь просматривает собственные Сводка. Примеры значений 5| 
| targetFollowerCount| > 32-разрядного целого числа без знака| Число людей, который выполняется в соответствии с целевой объект. Примеры значений 1308| 
| targetFollowingCount| 32-разрядного целого числа без знака| Число людей, целевой объект подписан. Примеры значений 112| 
| Водяной знак| Строка| Необязательно. Водяной знак последние изменения для целевого объекта. Это значение будет существовать только в том случае, когда пользователь просматривает собственные Сводка. Примеры значений 5| 
  
<a id="ID4E4D"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "targetFollowingCount": 87,
    "targetFollowerCount": 19,
    "isCallerFollowingTarget": true,
    "isTargetFollowingCaller": false,
    "hasCallerMarkedTargetAsFavorite": true,
    "hasCallerMarkedTargetAsKnown": true,
    "legacyFriendStatus": "None",
    "recentChangeCount": 5,
    "watermark": "5246775845000319351"
}
    
```

  
<a id="ID4EGE"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EIE"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   