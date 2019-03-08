---
title: TitleAssociation (JSON)
assetID: 05f4edbb-cc3d-c17d-0979-aac9e44a4988
permalink: en-us/docs/xboxlive/rest/json-titleassociation.html
description: " TitleAssociation (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 21a583e7556f98b827a63de3948f43d76f25c907
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57592899"
---
# <a name="titleassociation-json"></a>TitleAssociation (JSON)
Заголовок, который связан с достижение. 
<a id="ID4EN"></a>

 
## <a name="titleassociation"></a>TitleAssociation
 
Объект TitleAssociation имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| name| Строка| Локализованное имя содержимого.| 
| id| Строка| TitleId (32-разрядное целое число без знака, возвращаемых в десятичном формате).| 
| version| Строка| Определенную версию связанный заголовок (если применимо).| 
  
<a id="ID4E4B"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
  "name":"Microsoft Achievements Sample",
  "id":3051199919,
  "version":"abc"
}
    
```

  
<a id="ID4EGC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EIC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   