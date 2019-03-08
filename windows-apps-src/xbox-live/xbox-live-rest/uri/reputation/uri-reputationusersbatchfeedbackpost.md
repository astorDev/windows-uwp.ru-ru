---
title: POST (/users/batchfeedback)
assetID: f94dcf19-a4e3-5bd0-5276-23e43bdcae52
permalink: en-us/docs/xboxlive/rest/uri-reputationusersbatchfeedbackpost.html
description: " POST (/users/batchfeedback)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 0906d32a0e15b2eaaf9c33e7f658e9e9f0cd5124
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57622729"
---
# <a name="post-usersbatchfeedback"></a>POST (/users/batchfeedback)
Используется заголовок вашей службы отправки обратной связи в виде пакета за пределами интерфейса напротив названия. Доменом для таких URI является `reputation.xboxlive.com`.
 
  * [Текст запроса](#ID4EX)
  * [Обязательные заголовки](#ID4E3E)
  * [Коды состояния HTTP](#ID4EWG)
  * [Текст ответа](#ID4EDAAC)
 
<a id="ID4EX"></a>

 
## <a name="request-body"></a>Тело запроса 
 
Вызывающим объектам необходимо включить их утверждения сертификата в разделе ClientCertificates их веб-объект запроса.
 
<a id="ID4EBB"></a>

 
### <a name="required-members"></a>Обязательные члены 
 
Запрос должен содержать массив **BatchFeedback** объектов. 
  
<a id="ID4EPB"></a>

 
### <a name="prohibited-members"></a>Запрещенные элементы 
 
Все остальные элементы запрещены в запросе.
  
<a id="ID4E3B"></a>

 
### <a name="sample-request"></a>Пример запроса 
 

```cpp
{
    "items" :
    [
        {
            "targetXuid": "33445566778899",
            "titleId": "6487",
            "sessionRef":
            {
                "scid": "372D829B-FA8E-471F-B696-07B61F09EC20",
                "templateName": "CaptureFlag5",
                "name": "Halo556932",
            },
            "feedbackType": "FairPlayKillsTeammates",
            "textReason": "Killed 19 team members in a single session",
            "evidenceId": null
        },
        {
            "targetXuid": "33445566778899",
            "titleId": "6487",
            "sessionRef":
            {
                "scid": "372D829B-FA8E-471F-B696-07B61F09EC20",
                "templateName": "CaptureFlag5",
                "name": "Halo556932",
            },
            "feedbackType": "FairPlayQuitter",
            "textReason": "Quit 6 times from 9 sessions",
            "evidenceId": null
        }
    ]
}

      
```

 
| <b>Поле</b>| <b>Тип JSON</b>| <b>Описание</b>| 
| --- | --- | --- | 
| элементы| Массив| Коллекция документов JSON обратной связи.| 
| targetXuid| Строка| XUID указанного пользователя| 
| titleId| Строка| Заголовок, который был отправлен отзыв, или значение NULL.| 
| sessionRef| Объект| Объект, описывающий сеанса MPSD этот отзыв относится, или значение NULL.| 
| feedbackType| Строка| Строковая версия значения в перечислении FeedbackType.| 
| textReason| Строка| Текст, поставляемых партнерами, отправитель может добавлять в получите более подробные сведения об обратной связи, которая была отправлена.| 
| evidenceId| Строка| Идентификатор ресурса, который можно использовать в качестве свидетельства, отправляемые обратной связи. например идентификатор видеофайла.| 
   
<a id="ID4E3E"></a>

 
## <a name="required-headers"></a>Обязательные заголовки
 
При делает запрос на Xbox Live Services требуются следующие заголовки. 

> [!NOTE] 
> Сертификат утверждения партнера должны отправляться в запросе, чтобы отправить отзыв о пакете. 


 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | --- | --- | 
| x-xbl контракт version| 101| Версия контракта API.| 
| Content-Type| application/json| Тип данных, передаваемых.| 
| Authorization| «XBL3.0 x =&lt;userhash >;&lt; токен >»| Учетные данные проверки подлинности для проверки подлинности HTTP.| 
| X-RequestedServiceVersion| 101| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д.| 
  
<a id="ID4EWG"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 400| Неверный запрос| Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.| 
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.| 
| 404| Не найден| Не удалось найти указанный ресурс.| 
| 500| Внутренняя ошибка сервера| Сервер обнаружил непредвиденное условие, что помешало ему выполнить запрос.| 
| 503| Служба недоступна| Запрос был отрегулирован, выполните запрос еще раз после повторных попыток клиента значение в секундах (например 5 секунд позже).| 
  
<a id="ID4EDAAC"></a>

 
## <a name="response-body"></a>Тело ответа 
 
Если вызов был успешным, объекты не возвращаются из этого ответа. В противном случае служба возвращает [ServiceError](../../json/json-serviceerror.md) объекта.
  
<a id="ID4EXAAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EZAAC"></a>

 
##### <a name="parent"></a>Parent 

[/ users/batchfeedback](uri-reputationusersbatchfeedback.md)

  
<a id="ID4EFBAC"></a>

 
##### <a name="reference"></a>Справочные материалы 

[Отзыв (JSON)](../../json/json-feedback.md)

 [ServiceError (JSON)](../../json/json-serviceerror.md)

   