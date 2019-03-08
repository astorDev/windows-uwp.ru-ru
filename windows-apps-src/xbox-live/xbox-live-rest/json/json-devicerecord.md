---
title: DeviceRecord (JSON)
assetID: aca4f4d3-f9b4-8919-5b6d-5ae0fe11e162
permalink: en-us/docs/xboxlive/rest/json-devicerecord.html
description: " DeviceRecord (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 9746706c00a09cd8b64913b4ae8b5c3426551e48
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57646149"
---
# <a name="devicerecord-json"></a>DeviceRecord (JSON)
Сведения об устройстве, включая его тип и активен в заголовки. 
<a id="ID4EN"></a>

 
## <a name="devicerecord"></a>DeviceRecord
 
Объект DeviceRecord имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| type| Строка| Тип устройства, устройства. Существует несколько возможностей «D», «Xbox360», «MoLIVE» (Windows), «WindowsPhone», «WindowsPhone7» и «PC» (G4WL). Если тип неизвестен (для примера iOS, Android или title, внедренного в веб-браузер), возвращается «Web».| 
| заголовки| Массив [TitleRecord](json-titlerecord.md)| Список заголовков, активных на этом устройстве.| 
  
<a id="ID4EWB"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
[{
    type:"D",
    titles:
    [{
      id:"12341234",
      name:"Contoso 5",
      state:"active",
      placement:"fill",
      timestamp:"2012-09-17T07:15:23.4930000",
      activity:
      {
        richPresence:"Team Deathmatch on Nirvana"
      }
    },
    {
      id:"12341235",
      name:"Contoso Waypoint",
      timestamp:"2012-09-17T07:15:23.4930000",
      placement:"snapped",
      state:"active",
      activity:
      {
        richPresence:"Using radar"
      }
    }]
  }]
    
```

  
<a id="ID4E6B"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EBC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4ENC"></a>

 
##### <a name="reference"></a>Справочные материалы   