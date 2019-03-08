---
title: InitialUploadResponse (JSON)
assetID: 6abb7d37-2c35-2cc3-d9e5-eff695235262
permalink: en-us/docs/xboxlive/rest/json-initialuploadresponse.html
description: " InitialUploadResponse (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: dab59fefb389cf550a1bc4fc6429f6b0970f50ab
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57589839"
---
# <a name="initialuploadresponse-json"></a>InitialUploadResponse (JSON)
 
<a id="ID4EO"></a>

 
## <a name="initialuploadresponse"></a>InitialUploadResponse
 
Объект InitialUploadResponse имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| <b>gameClipId</b>| Строка| Идентификатор, назначенный для запроса на передачу данных.| 
| <b>uploadUri</b>| URI| Расположение, в которой будет передан игр клипа.| 
| <b>largeThumbnailUri</b>| URI| Необязательно. Расположение, к которому должен быть загружен большой эскиз. Наличие этого поля определяется [перечисления ThumbnailSource](../enums/gvr-enum-thumbnailsource.md) значение в <b>InitialUploadRequest</b> (будет присутствовать при указании передачи).| 
| <b>smallThumbnailUri</b>| URI| Необязательно. Расположение, к которому должен быть загружен небольшой эскиз. Наличие этого поля определяется [перечисления ThumbnailSource](../enums/gvr-enum-thumbnailsource.md) значение в <b>InitialUploadRequest</b> (будет присутствовать при указании передачи).| 
  
<a id="ID4EYC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
   "gameClipId": "6b364924-5650-480f-86a7-fc002a1ee752"  ,  
   "uploadUri": "https://gameclips.xbox.live/upload/xuid(2716903703773872)/6b364924-5650-480f-86a7-fc002a1ee752/container",
   "largeThumbnailUri": "https://gameclips.xbox.live/upload/xuid(2716903703773872)/6b364924-5650-480f-86a7-fc002a1ee752/container/thumbnails/large",
   "smallThumbnailUri": "https://gameclips.xbox.live/upload/xuid(2716903703773872)/6b364924-5650-480f-86a7-fc002a1ee752/container/thumbnails/small"
 }
    
```

  
<a id="ID4EBD"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EDD"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   