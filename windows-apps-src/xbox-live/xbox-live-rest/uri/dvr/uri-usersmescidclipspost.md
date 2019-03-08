---
title: POST (/users/me/scids/{scid}/clips)
assetID: 44535926-9fb8-5498-b1c8-514c0763e6c9
permalink: en-us/docs/xboxlive/rest/uri-usersmescidclipspost.html
description: " POST (/users/me/scids/{scid}/clips)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 7a8973390ccbf5dd9980410f60f03a7edd78c134
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608789"
---
# <a name="post-usersmescidsscidclips"></a>POST (/users/me/scids/{scid}/clips)
Выполните запрос первоначальной отправки. Домены для таких URI не `gameclipsmetadata.xboxlive.com` и `gameclipstransfer.xboxlive.com`, в зависимости от функции в URI.
 
  * ["Примечания"](#ID4EX)
  * [Параметры URI](#ID4EFB)
  * [Авторизации](#ID4EQB)
  * [Требуемые заголовки запросов](#ID4EKC)
  * [Необязательные заголовки запросов](#ID4ENE)
  * [Текст запроса](#ID4ENF)
  * [Пример запроса](#ID4E1F)
  * [Коды состояния HTTP](#ID4EDG)
  * [Текст ответа](#ID4EVAAC)
  * [Пример ответа](#ID4EFBAC)
 
<a id="ID4EX"></a>

 
## <a name="remarks"></a>Замечания
 
Это первая часть процесс передачи GameClip. При записи видео рекомендуется для вызова службы GameClips немедленно, чтобы получить идентификатор и URI для передачи битов, в том случае, даже если передача не запланирован запуск прямо сейчас. Этот вызов будет выполнять проверки квоты пользователя и другие проверки, изолируя содержимого, конфиденциальности и т. д, чтобы увидеть, если видео следует даже запланировать отправки клиентом. Положительный ответ, этот вызов указывает, что служба будет принимать видеоролик для загрузки. Отправить все клипы должно быть связано с определенного продукта (с помощью SCID) был принят в системе.
 
Этот вызов не является идемпотентной; Последующие вызовы вызовет выдавать разные идентификаторы и URI. Повторные попытки в случае сбоя необходимо следовать стандартные отсрочки поведения на стороне клиента.
  
<a id="ID4EFB"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| scid| Строка| Идентификатор конфигурации службы ресурса, к которому выполняется обращение. Должно соответствовать SCID пользователя, прошедшего проверку подлинности.| 
  
<a id="ID4EQB"></a>

 
## <a name="authorization"></a>Authorization
 
Для этого метода требуются следующие утверждения:
 
   * xuid
   * Тип_устройства - должно быть устройство для отправки
   * Идентификатор устройства
   * titleId
   * TitleSandboxId
   
<a id="ID4EKC"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | 
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Примеры значений <b>XAUTH =&lt;authtoken ></b>| 
| X-RequestedServiceVersion| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос будет перенаправлен только к этой службе после проверки допустимости заголовок, утверждения в маркер проверки подлинности и т. д. Примеры 1 vnext.| 
| Content-Type| Строка| Тип MIME тела ответа. Пример: <b>application/json</b>.| 
| Принять| Строка| Допустимое значение Content-Type. Пример: <b>application/json</b>.| 
  
<a id="ID4ENE"></a>

 
## <a name="optional-request-headers"></a>Необязательные заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Accept-Encoding| Строка| Алгоритмы сжатия приемлемым. Примеры значений: gzip, deflate, удостоверение.| 
  
<a id="ID4ENF"></a>

 
## <a name="request-body"></a>Тело запроса
 
Тело запроса должно быть [InitialUploadRequest](../../json/json-initialuploadrequest.md) объект в формате JSON.
  
<a id="ID4E1F"></a>

 
## <a name="sample-request"></a>Пример запроса
 

```cpp
{
   "clipNameStringId": "1193045",
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

  
<a id="ID4EDG"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 200| ОК| Сеанс был успешно извлечен.| 
| 400| Неверный запрос| Произошла ошибка в тексте запроса, или он превышает квоту.| 
| 401| Недостаточно прав| Возникла проблема с формат маркера проверки подлинности в запросе.| 
| 403| Запрещено| Некоторые необходимые утверждения отсутствуют или не является типа устройства.| 
| 503| Неприемлемо| Служба или некоторые подчиненные зависимости не работают. Повторите попытку, указав стандартное поведение переключения в пассивный режим.| 
  
<a id="ID4EVAAC"></a>

 
## <a name="response-body"></a>Тело ответа
 
Ответ может быть [InitialUploadResponse](../../json/json-initialuploadresponse.md) объекта или [ServiceErrorResponse](../../json/json-serviceerrorresponse.md) объект в формате JSON.
  
<a id="ID4EFBAC"></a>

 
## <a name="sample-response"></a>Пример ответа
 

```cpp
{
   "clipName": "ClipName",
   "gameClipId": "6b364924-5650-480f-86a7-fc002a1ee752",  
   "titleName": "TitleName",
   "uploadUri": "https://gameclips.xbox.live/upload/xuid(2716903703773872)/6b364924-5650-480f-86a7-fc002a1ee752/container",
   "largeThumbnailUri": "https://gameclips.xbox.live/upload/xuid(2716903703773872)/6b364924-5650-480f-86a7-fc002a1ee752/container/thumbnails/large",
   "smallThumbnailUri": "https://gameclips.xbox.live/upload/xuid(2716903703773872)/6b364924-5650-480f-86a7-fc002a1ee752/container/thumbnails/small"
 }
         
```

  
<a id="ID4EOBAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EQBAC"></a>

 
##### <a name="parent"></a>Parent 

[/ users/me/scids / {scid} / отсекает](uri-usersmescidclips.md)

   