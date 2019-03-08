---
title: ResetReputation (JSON)
assetID: 15edb5e7-a00b-4188-9b49-9db5774c4a10
permalink: en-us/docs/xboxlive/rest/json-resetreputation.html
description: " ResetReputation (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: d09c8bbc1130f91dfea3d4c35e391dcf9adcf127
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57649419"
---
# <a name="resetreputation-json"></a>ResetReputation (JSON)
Содержит новый базовый оценки репутации, к которым следует изменить существующий показатели пользователя. 
<a id="ID4EN"></a>

 
## <a name="resetreputation"></a>ResetReputation
 
Объект ResetReputation имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| fairplayReputation| number| Требуемый новый базовый оценки репутации Fairplay для пользователя (допустимый диапазон 0-75).| 
| commsReputation| number| Требуемый новый базовый оценки репутации сообщений для пользователя (допустимый диапазон 0-75).| 
| userContentReputation| number| Требуемый новый базовый оценки репутации UserContent для пользователя (допустимый диапазон 0-75).| 
  
<a id="ID4E4B"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "fairplayReputation": 75,
    "commsReputation": 75,
    "userContentReputation": 75
}
    
```

  
<a id="ID4EGC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EIC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   