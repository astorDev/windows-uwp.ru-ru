---
title: GameClip (JSON)
assetID: 204cb702-4ce4-85a8-f231-3b4fb243405f
permalink: en-us/docs/xboxlive/rest/json-gameclip.html
description: " GameClip (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 4cfc2ac0a635e4aacdc9eeefb5097c6bd946a518
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57646509"
---
# <a name="gameclip-json"></a>GameClip (JSON)
 
<a id="ID4EO"></a>

 
## <a name="gameclip"></a>GameClip
 
Объект GameClip имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| <b>gameClipId</b>| Строка| Идентификатор, присвоенный игр клипа.| 
| <b>Состояние</b>| GameClipState| Состояние игры клипа в системе.| 
| <b>dateRecorded</b>| DateTime| Дата и время начала записи, в формате UTC (в формате ISO 8601).| 
| <b>Дата изменения</b>| DateTime| Время игр клип или его метаданные в формате UTC (в формате ISO 8601) последнего изменения.| 
| <b>userCaption</b>| Строка| Пользователь ввел не Локализованная строка для игр клипа.| 
| <b>Тип</b>| GameClipTypes| Тип коллекции. Может быть несколько значений и будет с разделителями запятыми Если это так.| 
| <b>Источник</b>| GameClipSource| Как было получено клипа.| 
| <b>Видимость</b>| GameClipVisibility| Видимость игр клипа после его публикации в системе.| 
| <b>durationInSeconds</b>| 32-разрядного целого числа без знака| Длительность игр клипа в секундах.| 
| <b>scid</b>| Строка| SCID, с которым связан игр клипа.| 
| <b>Оценка</b>| число с плавающей запятой двойной точности| Оценка, связанные с игр клипа, в диапазоне от 0,0 до 5.0.| 
| <b>ratingCount</b>| 32-разрядного целого числа без знака| Количество раз, когда этот клип были оценены.| 
| <b>Представления</b>| 32-разрядного целого числа без знака| Количество представлений, связанных с игр клипа.| 
| <b>titleData</b>| Строка| Контейнер свойств конкретного заголовка.| 
| <b>titleData</b>| Строка| Контейнер свойств консоли.| 
| <b>Эскизы</b>| Массив GameClipThumbnail| Массив объектов GameClipThumbnail.| 
| <b>gameClipUris</b>| Массив GameClipUri| Массив объектов GameClipUri.| 
| <b>xuid</b>| Строка| XUID владельца игр клипа, маршалинг в виде строки.| 
| <b>clipName</b>| Строка| Локализованная версия именем клипа, основываясь на национальная настройка ввода запроса, как искать из системы управления заголовка.| 
  
<a id="ID4ERH"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
     "id": "7ce5c1a7-1255-46d3-a90e-34a0e2dfab06",
     "xuid": "2716903703773872",
     "state": "Published", 
     "dateRecorded": "2012-12-23T12:00:00Z",
     "lastModified": "2012-10-31T10:45:00Z",
     "clipName": "Forza 4",
     "userCaption": "My awesome car flip",
     "type": "DeveloperInitiated, Achievement",
     "source": "TitleDirect",
     "visibility": "Default",
     "durationInSeconds": 30,
     "scid": "ecb5497e-76d4-4a8a-870d-e76a26306b7d",
     "rating": 1.0,
     "views": 5,
     "thumbnails": [
       {
         "uri": "https://gameclips.xbox.com/thumbnails/7ce5c1a7-1255-46d3-a90e-34a0e2dfab06/small.jpg",
         "fileSize": 123,
         "width": 120,
         "height": 250
       }
     ],
     "gameClipUris": [
       {
         "uri": "https://gameclips.xbox.com/clips/7ce5c1a7-1255-46d3-a90e-34a0e2dfab06/clip.mp4",
         "fileSize": 1234565,
         "uriType": "Download",
         "expiration": "9999-12-31T23:59:59.9999999"
       }
     ]
   }
    
```

  
<a id="ID4E1H"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E3H"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   