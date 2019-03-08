---
title: ServiceErrorResponse (JSON)
assetID: a2077df8-f76c-0233-8e41-68267b681862
permalink: en-us/docs/xboxlive/rest/json-serviceerrorresponse.html
description: " ServiceErrorResponse (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 86f9389f6f76c1c51955a6c784393e9b05909298
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57597509"
---
# <a name="serviceerrorresponse-json"></a>ServiceErrorResponse (JSON)
При обнаружении ошибки службы, будет возвращаться соответствующего кода ошибки HTTP. При необходимости служба может также включать объект ServiceErrorResponse, как определено ниже. В производственной среде меньше данных могут быть включены. 
<a id="ID4EN"></a>

 
## <a name="serviceerrorresponse"></a>ServiceErrorResponse
 
Объект ServiceErrorResponse имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| <b>код ошибки</b>| 32-разрядное знаковое целое число| Код, связанный с ошибкой (может иметь значение null).| 
| <b>Сообщение об ошибке</b>| Строка| Дополнительные сведения об ошибке.| 
  
<a id="ID4EVB"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
   "errorCode": 8377,
   "errorMessage": "XUID specified in the claim does not match URI XUID."
 }
    
```

  
<a id="ID4E5B"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EAC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   