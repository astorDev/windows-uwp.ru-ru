---
title: GameMessage (JSON)
assetID: c11606e6-701f-5807-4aef-5608c98ad831
permalink: en-us/docs/xboxlive/rest/json-gamemessage.html
description: " GameMessage (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: a2bddd9e26b4716fd1e33c4b5bbde56672b5d3f8
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651299"
---
# <a name="gamemessage-json"></a>GameMessage (JSON)
Объект JSON, определение данных для сообщения в очередь сообщений игр сеанса. 
<a id="ID4EN"></a>

  
 
Объект GameMessage JSON имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| данные| Массив 8-разрядных целых чисел без знака| Данные с кодировкой Base64, игр клиенту необходимо отправить клиентам других игр. Это значение является непрозрачным на сервер. | 
| senderXuid| 64-разрядного целого числа без знака| Идентификатор пользователя Xbox проигрывателя, отправляющий сообщение. | 
| sequenceNumber| 32-разрядное знаковое целое число| Порядковый номер сообщения игр. Это значение назначается сервером. Порядковые номера будут гарантированно монотонно возрастает, но не может быть последовательными. Порядковые номера являются уникальными в пределах очередь сообщений, но не между очередями сообщений. | 
| queueIndex| 32-разрядное знаковое целое число| Индекс сеанса очереди сообщений для сообщения. Возможные значения: 0-3.| 
| Метка времени| DateTime| Время создания игр сообщение в очереди на сервере, в формате UTC. | 
  
<a id="ID4ERC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "queueIndex": 0,
    "sequenceNumber": 5,
    "senderXuid": 65536,
    "timestamp": "2011-06-23T18:49:50Z",
    "data": null
}
    
```

  
<a id="ID4E1C"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E3C"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4EGD"></a>

 
##### <a name="reference"></a>Справочные материалы 

[GameSession (JSON)](json-gamesession.md)

   