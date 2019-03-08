---
title: quotaInfo (JSON)
assetID: 3147ab78-e671-e142-0a3a-688dc4079451
permalink: en-us/docs/xboxlive/rest/json-quota.html
description: " quotaInfo (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: f3499fdba972d6e953813fc490d080910921698e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57654119"
---
# <a name="quotainfo-json"></a>quotaInfo (JSON)
Содержит сведения о квотах о группе заголовка. 
<a id="ID4EN"></a>

 
## <a name="quotainfo"></a>QuotaInfo
 
Объект quotaInfo имеет следующие характеристики.
 
Для глобального хранилища
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| quotaBytes| 32-разрядное знаковое целое число | Максимальное число байтов, которые можно использовать для наименования.| 
| usedBytes| 32-разрядное знаковое целое число | Число байтов, используемых для наименования.| 
  
<a id="ID4EXB"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 
В следующем примере показано ответ для глобального хранилища:
 

```json
{
    "quotaInfo":
    {
        "usedBytes":4194304,
        "quotaBytes":536870912
    }
}
      
```

  
<a id="ID4ECC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EEC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   