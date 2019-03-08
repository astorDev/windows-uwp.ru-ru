---
title: GameClipUri (JSON)
assetID: 03c097e8-7f29-1026-7a77-5c785b8511e9
permalink: en-us/docs/xboxlive/rest/json-gameclipuri.html
description: " GameClipUri (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 1c74d0831c3b841ad2a1366bd2e03fb8a9b0448d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57623499"
---
# <a name="gameclipuri-json"></a>GameClipUri (JSON)
 
<a id="ID4EO"></a>

 
## <a name="gameclipuri"></a>GameClipUri
 
Объект GameClipUri имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| <b>URI</b>| Строка| URI, требуемый ресурс видео.| 
| <b>Размер файла</b>| 32-разрядного целого числа без знака| Общий размер эскиза.| 
| <b>uriType</b>| GameClipUriType| Тип URI.| 
| <b>истечение срока действия</b>| DateTime| Время окончания срока действия URI, который включается в этот ответ. Если URL-адрес пуст или расцениваются как истек срок действия перед воспроизведением, вызывающие объекты должны вызывать RefreshUrl API.| 
  
<a id="ID4EMC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
         "uri": "https://gameclips.xbox.com/clips/7ce5c1a7-1255-46d3-a90e-34a0e2dfab06/clip.mp4",
         "fileSize": 1234565,
         "uriType": "Download",
         "expiration": "9999-12-31T23:59:59.9999999"
       }
    
```

  
<a id="ID4EVC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EXC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   