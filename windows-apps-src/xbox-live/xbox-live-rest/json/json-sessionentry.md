---
title: SessionEntry (JSON)
assetID: b5cf5c3d-83b8-635f-d1a5-0be5d9434ea5
permalink: en-us/docs/xboxlive/rest/json-sessionentry.html
description: " SessionEntry (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 73133f898ff219477cb60f54798cbd81acb87ebe
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57589739"
---
# <a name="sessionentry-json"></a>SessionEntry (JSON)
Содержит данные для сеанса пригодности. 
<a id="ID4EN"></a>

 
## <a name="sessionentry"></a>SessionEntry
 
Объект SessionEntry имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| durationInSeconds| 32-разрядное знаковое целое число | Длительность — в секундах — сеанса. | 
| джоулях| 32-разрядное знаковое целое число | Энергии — в джоулях — записать в сеансе. | 
| выполнено| число с плавающей запятой одиночной точности| Среднее значение выполнено на протяжении сеанса. Значение MET — это отношение претендентов metabolic скорость во время действия относительно лица metabolic скорость при хранении. Поскольку metabolic стоимости хранения является 1.0, независимо от лица веса и значения MET относительны лица, над которым metabolic скорость, они могут использоваться для сравнения интенсивность действие, выполняемая отдельные элементы разный вес.| 
| serverTimestamp| DateTime| Время — исчисляется по UTC, операция была введена на сервере. | 
| Источник| 8-разрядное целое число без знака| Источник сеанса.| 
| Метка времени| DateTime| Время — зависимости в формате UTC (UTC), операция была создана на стороне клиента. | 
| titleId| 64-разрядного целого числа без знака| Заголовок — в десятичном формате, который создал запись.| 
  
<a id="ID4EFE"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
   "titleId" : "1234567",
   "timestamp" : "2011-11-18T08:08:46Z",
   "serverTimestamp" : "2011-11-20T04:04:23Z",
   "durationInSeconds" : 240,
   "joules" :  1600,
   "met" :  "124"
   "source" :  "1"
}
    
```

  
<a id="ID4EOE"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EQE"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   