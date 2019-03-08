---
title: MatchTicket (JSON)
assetID: 12617677-47f2-e517-af53-5ab9687eea2a
permalink: en-us/docs/xboxlive/rest/json-matchticket.html
description: " MatchTicket (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 4bc638dfe7735856295ed92f35e244213be7bc1e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608339"
---
# <a name="matchticket-json"></a>MatchTicket (JSON)
Объект JSON, представляющий соответствие билет, используемый проигрывателями для поиска других игроков через каталог многопользовательской (MPSD). 
<a id="ID4EN"></a>

  
 
Объект MatchTicket JSON имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| serviceConfig| Код GUID| Конфигурации идентификатор службы (SCID) для сеанса.| 
| hopperName| Строка| Имя hopper, в котором следует поместить этот билет.| 
| giveUpDuration| 32-разрядное знаковое целое число| Максимальное время ожидания (целое количество секунд).| 
| preserveSession| перечисление| Значение, указывающее, если сеанс должен использоваться повторно при сеанса, в которым будут сопоставляться. Возможные значения: «всегда» или «никогда». | 
| ticketSessionRef| MultiplayerSessionReference| <b>MultiplayerSessionReference</b> объект для сеанса, в котором проигрыватель или группы в настоящее время воспроизведения. Этот член всегда является обязательным. | 
| ticketAttributes| Массив объектов| Коллекция пользовательских атрибутов и значений о билетах для игроков.| 
| игроков| Массив объектов| Коллекция объектов player, каждый из которых является набором свойств пользовательских атрибутов. | 
  
<a id="ID4EW"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
        "serviceConfig": "07617C5B-3423-4505-B6C6-10A16E1E5DDB",
        "hopperName": "TestHopper",
        "giveUpDuration": 10,
        "preserveSession": "never",
        "ticketSessionRef": {
        "scid": "AFFEABDF-0000-0000-0000-000000000001",
        "templateName": "TestTemplate",
        "sessionName": "5E551041-0000-0000-0000-000000000001"
    },
    "ticketAttributes": {
        "desiredMap": "Test Map",
        "desiredGameType": "Test GameType"
    },
    "players": [
        {
            "xuid": 123412345123,
            "playerAttributes": {
                "skill": 5,
                "ageRange": "teenager"
            }
        },
        {
          "xuid": 123412345124,
          "playerAttributes": {
              "skill": 15,
              "ageRange": "twenty-something"
          }
        }
      ]
    }
  
    
```

  
<a id="ID4EEB"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EGB"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   