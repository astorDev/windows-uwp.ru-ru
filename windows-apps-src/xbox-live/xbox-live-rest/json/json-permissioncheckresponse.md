---
title: PermissionCheckResponse (JSON)
assetID: 7a749001-b569-b0e0-2a35-f299474c8710
permalink: en-us/docs/xboxlive/rest/json-permissioncheckresponse.html
description: " PermissionCheckResponse (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 7623a45fa21e30a015bd5c6a7c1f5add19cc189b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57623369"
---
# <a name="permissioncheckresponse-json"></a>PermissionCheckResponse (JSON)
Результаты проверки из одного пользователя для параметра одно разрешение от пользователя с отдельным целевым объектом. 
<a id="ID4EN"></a>

 
## <a name="permissioncheckresponse"></a>PermissionCheckResponse
 
Объект PermissionCheckResponse имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| IsAllowed| Логическое значение| Обязательный. Этот член является <b>true</b> если запрашивающий пользователь может выполнить запрошенное действие с целевым пользователем.| 
| Результаты| Массив [PermissionCheckResult (JSON)](json-permissioncheckresult.md)| Необязательно. Если <b>IsAllowed</b> была ошибочной и проверки был отклонен по нечто, связанное с инициатора запроса, указывает, почему разрешение было запрещено.| 
  
<a id="ID4E3B"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "isAllowed": false,
    "reasons":
    [
        {"reason": "BlockedByRequestor"},
        {"reason": "MissingPrivilege", "restrictedSetting": "VideoCommunications"}
    ]
}
    
```

  
<a id="ID4EFC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EHC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   