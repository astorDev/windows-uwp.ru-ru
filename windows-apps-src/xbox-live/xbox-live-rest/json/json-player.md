---
title: Player (JSON)
assetID: eaf6d082-869b-d2d3-d548-5cef65e54541
permalink: en-us/docs/xboxlive/rest/json-player.html
description: " Player (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: a5967cbfecd47c5675926bd45939442c45dda7b6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57622499"
---
# <a name="player-json"></a>Player (JSON)
Содержит данные для проигрывателя в сеансе игр. 
<a id="ID4EN"></a>

 
## <a name="player"></a>Проигрыватель
 
Следующая спецификация имеет объект Player.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| customData| Массив 8-разрядное целое число без знака| проигрыватель играми данные в кодировке Base64 1024 байт. Это значение является непрозрачным на сервер.| 
| тег игрока| Строка| Тег игрока, более 15 символов — проигрывателя. Клиент должен использовать это значение в пользовательском Интерфейсе при определении проигрыватель. | 
| isCurrentlyInSession| Логическое значение| Указывает, если игрок в настоящее время в сеансе или из сеанса.| 
| seatIndex| 32-разрядное знаковое целое число| Индекс исполнителя в сеансе.| 
| xuid| 64-разрядного целого числа без знака| Идентификатор Xbox пользователя (XUID), проигрыватель.| 
  
<a id="ID4E3C"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "xuid": 2533274790412952,
    "gamertag":"MyTestUser",
    "seatindex": 3
    "customData":"AIHJ2?iE?/jiKE!l5S=T..."
    "isCurrentlyInSession":"true"
}
    
```

  
<a id="ID4EFD"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EHD"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4ERD"></a>

 
##### <a name="reference"></a>Справочные материалы 

[GameSession (JSON)](json-gamesession.md)

   