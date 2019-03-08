---
title: PermissionCheckBatchRequest (JSON)
assetID: 3100b17c-1b60-fdf2-3af9-7e424f1903ee
permalink: en-us/docs/xboxlive/rest/json-permissioncheckbatchrequest.html
description: " PermissionCheckBatchRequest (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 538547c85648970ab3e9fe3ae413e8a03df814ad
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57623509"
---
# <a name="permissioncheckbatchrequest-json"></a>PermissionCheckBatchRequest (JSON)
Коллекция объектов PermissionCheckBatchRequest. 
<a id="ID4EP"></a>

 
## <a name="permissioncheckbatchrequest"></a>PermissionCheckBatchRequest
 
Объект PermissionCheckBatchRequest имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| Пользователи| Массив пользователей| Обязательный. Массив целевых объектов для проверки разрешений от. Каждая запись в этот массив — это идентификатор пользователя Xbox (XUID) или анонимный пользователь сети для сценариев кросс network («анонимных пользователей»: «allUsers»). | 
| Разрешения| Массив [PermissionId перечисления](../enums/privacy-enum-permissionid.md)| Обязательный. Разрешения, необходимо проверить на соответствие каждого пользователя.| 
  
<a id="ID4E3B"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "users":
    [
        {"xuid":"12345"},
        {"xuid":"54321"}
    ],
    "permissions":
    [
        "ShareFriendList",
        "ShareGameHistory"
    ]
}
    
```

  
<a id="ID4EFC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EHC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   