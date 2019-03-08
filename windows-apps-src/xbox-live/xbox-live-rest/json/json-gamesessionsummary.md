---
title: GameSessionSummary (JSON)
assetID: 50cf91ba-29d3-1260-7643-bcb3f8d74fc0
permalink: en-us/docs/xboxlive/rest/json-gamesessionsummary.html
description: " GameSessionSummary (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 8dace19404ae7c8b1d1ef296a21c874e4dd14c6f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57613389"
---
# <a name="gamesessionsummary-json"></a>GameSessionSummary (JSON)
Объект JSON, представляющий сводных данных для сеанса игры. 
<a id="ID4EN"></a>

  
 
Объект GameSessionSummary JSON имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| CreationTime| DateTime| Дата и время создания сеанса, в формате UTC. | 
| customData| Массив 8-разрядных целых чисел без знака| 1024 байт данных сеанса играми. Это значение является непрозрачным на сервер. | 
| displayName| Строка| Сеанс отображаемое имя игры с максимальной длиной 128 символов. Это значение является непрозрачным на сервер. | 
| hasEnded| Логическое значение| Значение true, если сеанс завершен и false в противном случае. Установка для этого поля true метки игр сеанс только для чтения, предотвращая дополнительные данные, отправляемые в сеанс. | 
| sessionId| Строка, идентификатор сеанса. | 
| titleId| 32-разрядного целого числа без знака| Идентификатор заголовка, созданию игр сеанса.| 
| Variant| 32-разрядное знаковое целое число| Игры variant. Это значение является непрозрачным на сервер.| 
  
<a id="ID4EID"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "sessionId": "702e5aaf-e7bd-4a7c-abea-9dd4be10edec",
    "titleId": 1297287259,
    "variant": 1,
    "displayName": "Contoso Cards",
    "creationTime": "2011-06-23T17:13:06Z",
    "customData": null,
    "hasEnded": false,
}
    
```

  
<a id="ID4ERD"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ETD"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4E4D"></a>

 
##### <a name="reference"></a>Справочные материалы 

[GameSession (JSON)](json-gamesession.md)

   