---
title: GameSession (JSON)
assetID: 325af9ae-a9a9-5b36-8342-1aff5aff01d1
permalink: en-us/docs/xboxlive/rest/json-gamesession.html
description: " GameSession (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: ca7276ccdc13d896d19873811b4fa9df9a831cd1
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57646519"
---
# <a name="gamesession-json"></a>GameSession (JSON)
Объект JSON, представляющий данные игры для многопользовательской. 
<a id="ID4ER"></a>

  
 
Объект GameSession JSON имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| CreationTime| DateTime| Дата и время создания сеанса, в формате UTC. | 
| customData| Массив 8-разрядных целых чисел без знака| 1024 байт данных сеанса играми. Это значение является непрозрачным на сервер. | 
| displayName| Строка| Сеанс отображаемое имя игры с максимальной длиной 128 символов. Это значение является непрозрачным на сервер. | 
| hasEnded| Логическое значение| Значение true, если сеанс завершен и false в противном случае. Установка для этого поля true метки игр сеанс только для чтения, предотвращая дополнительные данные, отправляемые в сеанс. | 
| isClosed| Логическое значение| Значение true, если сеанс закрывается, и не больше игроков может быть добавлена и значение false в противном случае. Если это значение равно true, запросы, чтобы присоединиться к сеансу, отклоняются. | 
| maxPlayers| 32-разрядное знаковое целое число| Максимальное число исполнителей, которые могут быть в сеансе одновременно. Диапазон значений — 2 – 16. После сеанса содержит максимальное число исполнителей, дополнительные запросы, чтобы присоединиться к сеансу отклоняются. | 
| playersCanBeRemovedBy| PlayerAcl| Значение, указывающее игрок, который может быть удалить другие игроки из сеанса. Возможные значения: Self и AnyPlayer. | 
| список| Массив объектов проигрывателя| Массив игроков в сеансе. Список содержит текущий игроков и проигрыватели, которые были ранее в сеансе, но осталась. Порядок исполнителей в список никогда не меняется. Новые проигрыватели, добавляются в конец массива. | 
| seatsAvailable| 32-разрядное знаковое целое число| Число исполнителей, которые по-прежнему можно подключиться к сеансу, достигнуто максимальное число исполнителей. Это значение только для чтения и всегда меньше, чем значение поля maxPlayers. | 
| sessionId| Строка| Идентификатор сеанса, назначаемый MPSD, при создании сеанса. Это значение обычно входит в URI, при доступе к информации, хранимой в сеансе.| 
| titleId| 32-разрядного целого числа без знака| Идентификатор заголовка, созданию игр сеанса.| 
| Variant| 32-разрядное знаковое целое число| Игры variant. Это значение является непрозрачным на сервер.| 
| visibility| VisibilityLevel| Значение, показывающее видимость сеанса. Возможные значения: PlayersCurrentlyInSession PlayersEverInSession и всех пользователей.| 
  
<a id="ID4EEF"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "sessionId": "702e5aaf-e7bd-4a7c-abea-9dd4be10edec",
    "titleId": 1297287259,
    "variant": 1,
    "displayName": "Contoso Cards",
    "creationTime": "2011-06-23T17:13:06Z",
    "customData": null,
    "maxPlayers": 4,
    "seatsAvailable": 4,
    "isClosed": false,
    "hasEnded": false,
    "roster": [],
    "visibility": "PlayersCurrentlyInSession",
    "playersCanBeRemovedBy": "Self",
 }
    
```

  
<a id="ID4ENF"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EPF"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4EZF"></a>

 
##### <a name="reference"></a>Справочные материалы 

[GameMessage (JSON)](json-gamemessage.md)

 [GameSessionSummary (JSON)](json-gamesessionsummary.md)

 [Проигрыватель (JSON)](json-player.md)

   