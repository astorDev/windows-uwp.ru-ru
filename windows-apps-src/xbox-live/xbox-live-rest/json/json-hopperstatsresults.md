---
title: HopperStatsResults (JSON)
assetID: 91927da1-2e97-f7bc-ae62-7e0e9966b98e
permalink: en-us/docs/xboxlive/rest/json-hopperstatsresults.html
description: " HopperStatsResults (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 38e345fc20e92cdf6446c6ae1100e347fe634eff
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57646209"
---
# <a name="hopperstatsresults-json"></a>HopperStatsResults (JSON)
Объект JSON, представляющий статистику для hopper. 
<a id="ID4EN"></a>

  
 
Объект HopperStatsResults JSON имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| hopperName| Строка| Имя выбранного hopper.| 
| Время ожидания| 32-разрядное знаковое целое число| Среднее значение соответствия время hopper (целое количество секунд). | 
| Заполнение| 32-разрядное знаковое целое число| Число людей, Ожидание совпадений в hopper.| 
  
<a id="ID4EW"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON 
 

```json
{
      "hopperName":"contosoawesome2",
      "waitTime":30,
      "population":1
    }
  
    
```

  
<a id="ID4EGB"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EIB"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4EUB"></a>

 
##### <a name="reference"></a>Справочные материалы 

[Получение (/hoppers/ /serviceconfigs/ {scid} {name} / stats)](../uri/matchtickets/uri-serviceconfigsscidhoppershoppernamestatsget.md)

   