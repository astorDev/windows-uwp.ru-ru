---
title: ServiceError (JSON)
assetID: 81c43f6e-bfff-c4b5-d25c-eace22649f01
permalink: en-us/docs/xboxlive/rest/json-serviceerror.html
description: " ServiceError (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: da3d682a1b66d25a12f21a93e9596d13afae7f90
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57631709"
---
# <a name="serviceerror-json"></a>ServiceError (JSON)
Содержит сведения об ошибке возвращается, если вызов службы завершился ошибкой. 
<a id="ID4EN"></a>

 
## <a name="serviceerror"></a>ServiceError
 
Объект ServiceError имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| code| 32-разрядное знаковое целое число | Тип ошибки. Возможные значения см. | 
| Источник| Строка | Имя службы, в котором возникла ошибка. Например, значение <code>ReputationFD</code> означает, что ошибка не в службу URS. | 
| Описание| Строка| Описание ошибки. | 
 
<a id="ID4EBC"></a>

 
### <a name="error-codes"></a>Коды ошибок
 
| Значение| Описание| 
| --- | --- | --- | --- | --- | 
| 0| Успех нет ошибок| 
| 4000| Недопустимый документ JSON текста запроса, отправленные с возможностью проверки не удалось выполнить запрос POST. См. в разделе поле описания, Дополнительные сведения. | 
| 4100| Пользователь Does не существует XUID содержащихся в запросе URI не представляет допустимого пользователя в XBOX Live.| 
| 4500| Ошибка авторизации вызывающий объект не имеет прав для выполнения запрошенной операции.| 
| 5000| Служба ошибка произошла внутренняя ошибка в службе| 
| 5300| Служба недоступна служба недоступна.| 
   
<a id="ID4EQE"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "code": 4000,
    "source": "ReputationFD",
    "description": "No targetXuid field was supplied in the request"
}
    
```

  
<a id="ID4EZE"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E2E"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   