---
title: POST (/users/xuid({xuid})/feedback)
assetID: 48a7a510-a658-f2a3-c6bc-28a3610006e7
permalink: en-us/docs/xboxlive/rest/uri-reputationusersxuidfeedbackpost.html
description: " POST (/users/xuid({xuid})/feedback)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: d8a6e118811203fd34c310840e8688c2255c6beb
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57660049"
---
# <a name="post-usersxuidxuidfeedback"></a>POST (/users/xuid({xuid})/feedback)
Использовать из названия, чтобы добавить параметр отзыва в игре, а не с помощью оболочки. Доменом для таких URI является `reputation.xboxlive.com`.
 
  * [Параметры URI](#ID4EZ)
  * [Требуемые заголовки запросов](#ID4EEB)
  * [Текст запроса](#ID4ENC)
  * [Обязательные заголовки](#ID4EDE)
  * [Авторизации](#ID4EXF)
  * [Коды состояния HTTP](#ID4EEG)
  * [Текст ответа](#ID4EZH)
 
<a id="ID4EZ"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| ulong| Xbox пользователя идентификатор (XUID) сообщаются пользователя.| 
  
<a id="ID4EEB"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | 
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: «XBL3.0 x =&lt;userhash >;&lt; токен >».| 
| X-RequestedServiceVersion|  | Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д. Значение по умолчанию: 101.| 
  
<a id="ID4ENC"></a>

 
## <a name="request-body"></a>Тело запроса 
 
<a id="ID4EVC"></a>

 
### <a name="required-members"></a>Обязательные члены 
 
Запрос должен содержать [отзывы](../../json/json-feedback.md) объекта. 
  
<a id="ID4EED"></a>

 
### <a name="prohibited-members"></a>Запрещенные элементы 
 
Все остальные элементы запрещены в запросе.
  
<a id="ID4ETD"></a>

 
### <a name="sample-request"></a>Пример запроса 
 

```cpp
{
    "sessionRef":
    {
        "scid": "372D829B-FA8E-471F-B696-07B61F09EC20",
        "templateName": "CaptureFlag5",
        "name": "Halo556932",
    },
    "feedbackType": "CommsAbusiveVoice",
    "textReason": "He called me a doodoo-head!",
    "voiceReasonId": null,
    "evidenceId": null
}

      
```

   
<a id="ID4EDE"></a>

 
## <a name="required-headers"></a>Обязательные заголовки
 
При делает запрос на Xbox Live Services требуются следующие заголовки.
 
| <b>Заголовок</b>| <b>Значение</b>| <b>Deacription</b>| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| x-xbl контракт version| 101| Версия контракта API.| 
| Authorization| XBL3.0 x = [hash]; [токен]| Токен проверки подлинности STS. STSTokenString заменяется маркером, возвращенным в запросе проверки подлинности.| 
Content-Type| 
application/json| 
Тип данных, передаваемых.| 
  
<a id="ID4EXF"></a>

 
## <a name="authorization"></a>Authorization
 
Запрос должен содержать допустимый заголовок авторизации Xbox Live. Если вызывающий объект не может быть доступ к этому ресурсу, служба возвращает код 403 запрещено. Если заголовок отсутствует или является недопустимым, служба возвращает код 401 несанкционированный.
  
<a id="ID4EEG"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 204| Нет содержимого| Запрос завершен, но не имеет содержимого для возврата.| 
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.| 
| 404| Не найден| Не удалось найти указанный ресурс.| 
| 406| Неприемлемо| Версия ресурса не поддерживается.| 
| 408| Время ожидания запроса| Запрос заняло слишком много времени.| 
| 409| Конфликт| Токен продолжения более не является допустимым.| 
  
<a id="ID4EZH"></a>

 
## <a name="response-body"></a>Тело ответа 
 
Если вызов был успешным, объекты не возвращаются из этого ответа. В противном случае служба возвращает [ServiceError](../../json/json-serviceerror.md) объекта.
  
<a id="ID4EOAAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EQAAC"></a>

 
##### <a name="parent"></a>Parent 

[/Users/xuid({xuid})/Feedback](uri-reputationusersxuidfeedback.md)

  
<a id="ID4E3AAC"></a>

 
##### <a name="reference"></a>Справочные материалы 

[Отзыв (JSON)](../../json/json-feedback.md)

 [ServiceError (JSON)](../../json/json-serviceerror.md)

   