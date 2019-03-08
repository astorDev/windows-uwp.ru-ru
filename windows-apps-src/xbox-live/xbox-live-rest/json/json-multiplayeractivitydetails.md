---
title: MultiplayerActivityDetails (JSON)
assetID: f982aa5e-2694-4ef9-bc55-6c099a3cf9ec
permalink: en-us/docs/xboxlive/rest/json-multiplayeractivitydetails.html
description: " MultiplayerActivityDetails (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 188bcebb8d6bff879f30dcc83d7039fbcbfae0b2
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57658109"
---
# <a name="multiplayeractivitydetails-json"></a>MultiplayerActivityDetails (JSON)
Объект JSON, представляющий **Microsoft.Xbox.Services.Multiplayer.MultiplayerActivityDetails**. 

> [!NOTE] 
> Этот объект реализуется многопользовательскую 2015 и применяется только к этой многопользовательские версии и более поздних версий. Он предназначен для использования с контрактом шаблона 104/105 или более поздней версии.  

 
<a id="ID4ES"></a>

  
 
Объект MultiplayerActivityDetails JSON имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | --- | 
| SessionReference| MultiplayerSessionReference| Объект <b>Microsoft.Xbox.Services.Multiplayer.MultiplayerSessionReference</b> объект, представляющий идентификационные сведения для сеанса.| 
| handleId| 64-разрядного целого числа без знака| Идентификатор дескриптора, соответствующее действие.| 
| titleId| 32-разрядного целого числа без знака| Идентификатор заголовка, который должен быть запущен, чтобы соединить действие.| 
| Видимость| MultiplayerSessionVisibility| Объект <b>Microsoft.Xbox.Services.Multiplayer.MultiplayerSessionVisibility</b> значение, указывающее состояние видимости сеанса.| 
| JoinRestriction| MultiplayerSessionJoinRestriction| Объект <b>Microsoft.Xbox.Services.Multiplayer.MultiplayerSessionJoinRestriction</b> значение, указывающее, ограничение соединения для сеанса. Это ограничение применяется, если видимость поля задано значение «открыть».| 
| Закрыто| Логическое значение| Значение true, если сеанс временно закрыто для присоединения и false в противном случае.| 
| OwnerXboxUserId| 64-разрядного целого числа без знака| Идентификатор пользователя Xbox участника, ответственного за действие.| 
| MaxMembersCount| 32-разрядного целого числа без знака| Число слотов общее.| 
| MembersCount| 32-разрядного целого числа без знака| Число слотов занят.| 
  
<a id="ID4E3D"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
  "results": [{
    "id": "11111111-ebe0-42da-885f-033860a818f6",
    "type": "activity",
    "version": 1,
    "sessionRef": {
      "scid": "8dfb0100-ebe0-42da-885f-033860a818f6",
      "templateName": "party",
      "name": "e3a836aeac6f4cbe9bcab985494d3175"
    },
    "titleId": "1234567",
    "ownerXuid": "3212",

    // Only if ?include=relatedInfo
    "relatedInfo": {
      "visibility": "open",
      "joinRestriction": "followed",
      "closed": true,
      "maxMembersCount": 8,
      "membersCount": 4,
    }
  },
  {
    "id": "11111111-ebe0-42da-885f-033860a818f7",
    "type": "activity",
    "version": 1,
    "sessionRef": {
      "scid": "8dfb0100-ebe0-42da-885f-033860a818f6",
      "templateName": "TitleStorageTestDefault",
      "name": "795fcaa7-8377-4281-bd7e-e86c12843632"
    },
    "titleId": "1234567",
    "ownerXuid": "3212",

    // Only if ?include=relatedInfo
    "relatedInfo": {
      "visibility": "open",
      "joinRestriction": "followed",
      "closed": false,
      "maxMembersCount": 8,
      "membersCount": 4,
    }
  }]
}
    
```

  
<a id="ID4EFE"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EHE"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   