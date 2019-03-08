---
title: TitleBlob (JSON)
assetID: fd1c904d-e8d0-f61f-e403-40b25bd4ac14
permalink: en-us/docs/xboxlive/rest/json-titleblob.html
description: " TitleBlob (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 51a0b17a46d1c71ffdf9098d4637ca59d840c90a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57612589"
---
# <a name="titleblob-json"></a>TitleBlob (JSON)
Содержит сведения из хранилища. 
<a id="ID4EP"></a>

 
## <a name="titleblob"></a>TitleBlob
 
Объект TitleBlob имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| clientFileTime| DateTime| [необязательно] Дата и время последней передачи файла.| 
| displayName| Строка| [необязательно] Имя файла, который отображается для пользователя.| 
| ETag| Строка| Тег для файла, используемого в загрузки и отправки запросов.| 
| fileName| Строка| Имя файла.| 
| size| 64-разрядное знаковое целое число| Размер файла в байтах.| 
| smartBlobType| Строка| [необязательно] Тип данных. Возможными значениями являются: config, json, двоичное.| 
  
<a id="ID4E6C"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
    "fileName":"foo\bar\blob.txt,binary",
    "clientFileTime":"2012-01-01T01:02:03.1234567Z",
    "displayName":"Friendly Name",
    "size":12,
    "etag":"0x8CEB3E4F8F3A5BF",
    "smartBlobType":"binary"
}
      
```

  
<a id="ID4EID"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EKD"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   