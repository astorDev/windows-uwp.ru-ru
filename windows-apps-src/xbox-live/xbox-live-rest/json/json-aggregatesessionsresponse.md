---
title: AggregateSessionsResponse (JSON)
assetID: 020ee9b2-c96c-2e65-4e6d-f9f4bd25a374
permalink: en-us/docs/xboxlive/rest/json-aggregatesessionsresponse.html
description: " AggregateSessionsResponse (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: ef026fd5096d047b2014faaf95a667c69827e043
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608309"
---
# <a name="aggregatesessionsresponse-json"></a>AggregateSessionsResponse (JSON)
Содержит статистические данные для пользователя пригодности сеансов. 
<a id="ID4EN"></a>

 
## <a name="aggregatesessionsresponse"></a>AggregateSessionsResponse
 
Объект AggregateSessionsResponse имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| totalDurationInSeconds| 64-разрядное знаковое целое число| Общая длительность сеанса в секундах, в течение периода сбора.| 
| totalJoules| 64-разрядное знаковое целое число| Общее число энергии, потребляемой — в джоулях — в течение периода сбора. | 
| totalSessions| 64-разрядное знаковое целое число| Общее число сеансов, в течение периода сбора.| 
| weightedAverageMets| число с плавающей запятой одиночной точности | Взвешенное среднее metabolic эквивалент значения задач (MET) в течение периода сбора. Значение MET — это отношение претендентов metabolic скорость во время действия относительно лица metabolic скорость при хранении. Поскольку metabolic стоимости хранения является 1.0, независимо от лица веса и значения MET относительны лица, над которым metabolic скорость, они могут использоваться для сравнения интенсивность действие, выполняемая отдельные элементы разный вес.| 
  
<a id="ID4ESC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
   "totalSessions" : 300,
   "totalDurationInSeconds" : 1240,
   "totalJoules" :  21600,
   "weightedAvgMet" : 21,
}

    
```

  
<a id="ID4E2C"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E4C"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   