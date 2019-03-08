---
title: PermissionCheckResult (JSON)
assetID: 1cf147fa-4ff1-3299-0822-0fc1726d1600
permalink: en-us/docs/xboxlive/rest/json-permissioncheckresult.html
description: " PermissionCheckResult (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: dc13826be1b6f81201d069f5ade7ea5ba6668cd0
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598449"
---
# <a name="permissioncheckresult-json"></a>PermissionCheckResult (JSON)
Результаты проверки из одного пользователя для параметра одно разрешение от пользователя с отдельным целевым объектом. 
<a id="ID4EP"></a>

 
## <a name="permissioncheckresult"></a>PermissionCheckResult
 
Объект PermissionCheckResult имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| reason| Строка| Необязательно. Объект <b>PermissionResultCode</b> значение, указывающее, почему разрешение было запрещено Если <b>IsAllowed</b> была ошибочной.| 
| restrictedSetting| Строка| Необязательно. Если <b>PermissionResultCode</b> значение в <b>Причина</b> элемент указывает, что не удалось выполнить проверку прав доступа для запросившей стороны, это указывает на сбой какие привилегии.| 
  
<a id="ID4E6B"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "reason": "MissingPrivilege",
    "restrictedSetting": "VideoCommunications"
}
    
```

  
<a id="ID4EIC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EKC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   