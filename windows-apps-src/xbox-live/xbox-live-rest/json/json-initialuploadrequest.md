---
title: InitialUploadRequest (JSON)
assetID: 8b8bce98-cb5f-bbaf-5564-9be2f58d749b
permalink: en-us/docs/xboxlive/rest/json-initialuploadrequest.html
description: " InitialUploadRequest (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 2fbb2417f743d97b487ad16abd241fcb5eea62bb
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57646639"
---
# <a name="initialuploadrequest-json"></a>InitialUploadRequest (JSON)
Тело GameClip после отправки запроса. 
<a id="ID4EN"></a>

 
## <a name="initialuploadrequest"></a>InitialUploadRequest
 
Объект InitialUploadRequest имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| <b>greatestMomentId</b>| Строка| Идентификатор строки для текста для использования в качестве имени клипа. Это управляемый и локализовать, в файле конфигурации для заголовка разработчиком заголовка.| 
| <b>userCaption</b>| Строка| Необязательно. Альтернативное пользователь ввел имя игр клип, максимальная длина — 250 символов.| 
| <b>sessionRef</b>| Строка| Необязательно. Ссылка на игры сеанс, во время которого была выполнена запись.| 
| <b>dateRecorded</b>| DateTime| Время начала записи в формате UTC. Маршалируется как строка в формате ISO 8601 (см. в разделе <a href="https://www.w3.org/TR/NOTE-datetime">форматы даты и времени</a> Дополнительные сведения).| 
| <b>durationInSeconds</b>| 32-разрядного целого числа без знака| Длина клипа в секундах.| 
| <b>expectedBlocks</b>| 32-разрядного целого числа без знака| Необязательно. Число блоков, на которые будет разделить файл. Пропустите, если файл будет передан в одном запросе.| 
| <b>Размер файла</b>| 32-разрядного целого числа без знака| Размер файла в байтах, видео, который будет загружен.| 
| <b>Тип</b>| [Перечисление GameClipType](../enums/gvr-enum-gamecliptypes.md)| Тип коллекции, маршалирован как строковое значение перечисления, с разделителями запятыми.| 
| <b>Источник</b>| [Перечисление GameClipSource](../enums/gvr-enum-gameclipsource.md)| Указывает, каким образом был получен клип, маршалинг в виде строкового значения перечисления.| 
| <b>Видимость</b>| [Перечисление GameClipVisibility](../enums/gvr-enum-gameclipvisibility.md)| Задает видимость игр клипа после его публикации в системе.| 
| <b>titleData</b>| Строка| Необязательно. Контейнер свойств для свойства определенного заголовка, связанные с этой коллекции. Сохраняются и возвращаются в виде — является. Заголовок разработчики могут использовать это поле для сохранения собственные метаданные о клипа.| 
| <b>titleData</b>| Строка| Необязательно. Контейнер свойств для консоли конкретного свойства, связанные с этой коллекции. Сохраняются и возвращаются в виде — является. Это поле можно использовать консоль платформы для сохранения собственные метаданные о клипа.| 
| <b>systemProperties</b>| Строка| Необязательно. Контейнер свойств для консоли конкретного свойства, связанные с этой коллекции. Сохраняются и возвращаются как есть. Это поле можно использовать консоль платформы для сохранения собственные метаданные о клипа.| 
| <b>usersInSession</b>| Массив строк| Необязательно. Список пользователей в текущем сеансе.| 
| <b>ThumbnailSource</b>| [Перечисление ThumbnailSource](../enums/gvr-enum-thumbnailsource.md)| Необязательно. Источник эскиз.| 
| <b>thumbnailOffsetMillseconds</b>| 32-разрядное знаковое целое число| Задает смещение (в миллисекундах) для смещения созданный эскизов. Если указано только <b>thumbnailSource</b> равным смещению.| 
| <b>savedByUser</b>| Логическое значение| Необязательно. Задает клип, чтобы сохранить квоты пользователя вместо хранения FIFO. По умолчанию — false.| 
  
<a id="ID4ERH"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
   "greatestMomentId": "123abc",
   "userCaption": "OMG Look at this!",
   "sessionRef": "4587552a-a5ad-4c4c-a787-5bc5af70e4c9",
   "dateRecorded": "2012-12-23T11:08:08Z",
   "durationInSeconds": 27,
   "expectedBlocks": 7,
   "fileSize": 1234567,
   "type": "MagicMoment, Achievement",
   "source": "Console",
   "visibility": "Default",
   "titleData": "{ 'Boss': 'The Invincible' }",
   "systemProperties": "{ 'Id': '123456', 'Location': 'C:\\videos\\123456.mp4' }",
   "thumbnailSource": "Offset",
   "thumbnailOffsetMillseconds": 20000,
   "savedByUser": false
 }
    
```

  
<a id="ID4E1H"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E3H"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   