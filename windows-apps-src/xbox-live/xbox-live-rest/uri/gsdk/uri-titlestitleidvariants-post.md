---
title: POST (/titles/{titleId}/variants)
assetID: 84303448-5a11-d96f-907d-77f57f859741
permalink: en-us/docs/xboxlive/rest/uri-titlestitleidvariants-post.html
description: " POST (/titles/{titleId}/variants)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 17974ddf7dec26abac18ccee9fda5249bc9d656f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57618499"
---
# <a name="post-titlestitleidvariants"></a>POST (/titles/{titleId}/variants)
URI, вызывается клиентом, который извлекает список игр варианты для указанного заголовка идентификатор. Домены для таких URI не `gameserverds.xboxlive.com` и `gameserverms.xboxlive.com`.
 
  * [Параметры URI](#ID4EZ)
  * [Требуемые заголовки запросов](#ID4EIB)
  * [Необязательные заголовки запросов](#ID4EED)
  * [Авторизации](#ID4E3D)
  * [Текст запроса](#ID4EEE)
  * [Заголовки ответа требуется](#ID4ELF)
  * [Заголовки ответа необязательно](#ID4EMG)
  * [Текст ответа](#ID4EEH)
 
<a id="ID4EZ"></a>

 
## <a name="uri-parameters"></a>Параметры URI
 
| Параметр| Описание| 
| --- | --- | 
| titleid| Идентификатор заголовок, который следует работать запрос.| 
  
<a id="ID5EG"></a>

 
## <a name="host-name"></a>Имя узла

gameserverds.xboxlive.com
 
<a id="ID4EIB"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
При выполнении запроса, заголовки, показанные в следующей таблице являются обязательными.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | 
| Content-Type| application/json| Тип данных, передаваемых.| 
| Hyper-V| gameserverds.xboxlive.com|  | 
| Content-Length|  | Длина объекта запроса.| 
| x-xbl контракт version| 1| Версия контракта API.| 
| Authorization| XBL3.0 x = [hash]; [токен]| Токен проверки подлинности.| 
  
<a id="ID4EED"></a>

 
## <a name="optional-request-headers"></a>Необязательные заголовки запросов
 
При выполнении запроса, заголовки, показанные в следующей таблице являются необязательными.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| X-XblCorrelationId|  | Тип mime тела запроса.| 
  
<a id="ID4E3D"></a>

 
## <a name="authorization"></a>Authorization

Запрос должен содержать допустимый заголовок авторизации Xbox Live. Если вызывающий объект не может быть доступ к этому ресурсу, то служба возвращает «403 запрещено» в ответ. Если заголовок отсутствует или является недопустимым, то служба возвращает проверки подлинности 401 в ответе.
 
<a id="ID4EEE"></a>

 
## <a name="request-body"></a>Текст запроса
 
Запрос должен содержать объект JSON со следующими членами.
 
| Участник| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| языковой стандарт| Локальная варианты для возврата.| 
| maxVariants| Максимальное число вариантов для возврата.| 
| publisherOnly|  | 
| Ограничение|  | 
 
<a id="ID4EDF"></a>

 
### <a name="sample-request"></a>Пример запроса
 

```cpp
{
  "locale": "en-us",
  "maxVariants": "100",
  "publisherOnly": "false",
  "restriction": null
}

```

   
<a id="ID4ELF"></a>

 
## <a name="required-response-headers"></a>Заголовки ответа требуется
 
Ответ всегда будет включать заголовки, показанные в следующей таблице.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Content-Type| application/json| Тип данных в тексте ответа.| 
| Content-Length|  | Длина текста ответа.| 
  
<a id="ID4EMG"></a>

 
## <a name="optional-response-headers"></a>Заголовки ответа необязательно
 
Ответ может включают в себя заголовки, показанные ниже.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| X-XblCorrelationId|  | Тип mime тела ответа.| 
  
<a id="ID4EEH"></a>

 
## <a name="response-body"></a>Текст ответа
 
Если вызов прошел успешно, служба вернет объект JSON со следующими членами.
 
| Участник| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Варианты| Массив типа Variant.| 
| variantId| Идентификатор типа variant.| 
| name| Имя свойства типа variant.| 
| isPublisher|  | 
| Ранг|  | 
| gameVariantSchemaId|  | 
| variantSchemas| Массив variant схемы.| 
| variantSchemaId| Идентификатор схемы.| 
| schemaContent| Содержимое схемы| 
| name| Имя схемы| 
| gsiSets| Задает массив GSI.| 
| minRequiredPlayers| Минимальное число исполнителей для варианта.| 
| maxAllowedPlayers| Максимальное число исполнителей для варианта.| 
| gsiSetId| Идентификатор набора GSI.| 
| gsiSetName| Имя набора GSI.| 
| selectionOrder|  | 
| variantSchemaId| Идентификатор varaint схемы, используемой в GSI набора.| 
 
<a id="ID4EYBAC"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{
 "variants": [
     { 
       "variantId": "8B6EF8A0-7807-42C4-9CB0-1D9B8B8CE742", 
       "name": "tankWarsV2.0",
       "isPublisher": "true",
       "rank": "1",
       "gameVariantSchemaId": "9742DBA5-23FD-4760-9D74-6CFA211B9CFB"
     }],
  "variantSchemas": [
     {
        "variantSchemaId": "9742DBA5-23FD-4760-9D74-6CFA211B9CFB",
        "schemaContent": "&lt;?xml version=\"1.0\" encoding=\"UTF-8\" ?>&lt;xs:schema xmlns:xs=\"http://www.w3.org/2001/XMLSchema\">&lt;xs:element name=\"root\">&lt;/xs:element>&lt;/xs:schema>"
        "name": "tanksSchema"
     }],
     "gsiSets":
     [{ 
          "minRequiredPlayers": "5", 
          "maxAllowedPlayers": "10", 
          "gsiSetId": "B28047F5-B52F-477E-97C2-4C1C39E31D42",
          "gsiSetName": "TanksGSISet",
          "selectionOrder": "1",
          "variantSchemaId": "9742DBA5-23FD-4760-9D74-6CFA211B9CFB"
     }]
 }

  

```

   
<a id="ID4ERCAC"></a>

 
## <a name="see-also"></a>См. также
 [/titles/{titleId}/variants](uri-titlestitleidvariants.md)

  