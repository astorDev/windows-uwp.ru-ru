---
title: MultiplayerSessionReference (JSON)
assetID: 6e03e060-8c9b-b394-415f-af7e85be569f
permalink: en-us/docs/xboxlive/rest/json-multiplayersessionreference.html
description: " MultiplayerSessionReference (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 5986079e1cae3338d8cc24a9e85f6941cf4fbec4
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651249"
---
# <a name="multiplayersessionreference-json"></a>MultiplayerSessionReference (JSON)
Объект JSON, представляющий **MultiplayerSessionReference**. 
<a id="ID4EQ"></a>

  
 
Объект MultiplayerSessionReference JSON имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| scid| Код GUID| Идентификатор конфигурации службы (SCID). Часть 1 из идентификатор сеанса.| 
| templateName | Строка | Имя текущего экземпляра шаблона сеанса. Часть 2 из идентификатор сеанса. | 
| name | Строка | Имя сеанса. Часть 3 из идентификатор сеанса. | 
  
<a id="ID4EZ"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON 
 

```json
{
  "scid": "8d050174-412b-4d51-a29b-d55a34edfdb7",
  "templateName": "integration",
  "name": "19de0095d8bb41048f19edbbb6bc6b04"
}
  
    
```

  
<a id="ID4EJB"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ELB"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4EVB"></a>

 
##### <a name="reference"></a>Справочные материалы 

[MultiplayerSession (JSON)](json-multiplayersession.md)

   