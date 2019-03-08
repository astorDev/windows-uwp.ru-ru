---
title: POST (/titles/{titleId}/clusters)
assetID: 0977b0b0-872d-f7ad-9ba0-30d56cff4912
permalink: en-us/docs/xboxlive/rest/uri-titlestitleidclusters-post.html
description: " POST (/titles/{titleId}/clusters)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 91d7c49628914f887c5d2243942e10e47d47b095
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608909"
---
# <a name="post-titlestitleidclusters"></a>POST (/titles/{titleId}/clusters)
URI, который позволяет клиенту создать экземпляр сервера вычислений Xbox Live. Доменом для таких URI является `gameserverms.xboxlive.com`.
 
  * [Параметры URI](#ID4EX)
  * [Требуемые заголовки запросов](#ID4EGB)
  * [Авторизации](#ID4ELD)
  * [Текст запроса](#ID4EWD)
  * [Заголовки ответа требуется](#ID4EZE)
  * [Текст ответа](#ID4E5G)
 
<a id="ID4EX"></a>

 
## <a name="uri-parameters"></a>Параметры URI
 
| Параметр| Описание| 
| --- | --- | 
| titleId| Идентификатор заголовок, который следует работать запрос.| 
  
<a id="ID5EG"></a>

 
## <a name="host-name"></a>Имя узла

gameserverms.xboxlive.com
 
<a id="ID4EGB"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
При выполнении запроса, заголовки, показанные в следующей таблице являются обязательными.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | 
| Агент пользователя|  | Сведения об агенте пользователя, выполняющего запрос.| 
| Content-Type| application/json| Тип данных, передаваемых.| 
| Hyper-V| gameserverms.xboxlive.com|  | 
| Content-Length|  | Длина объекта запроса.| 
| x-xbl контракт version| 1| Версия контракта API.| 
| Authorization| XBL3.0 x = [hash]; [токен]| Токен проверки подлинности.| 
  
<a id="ID4ELD"></a>

 
## <a name="authorization"></a>Authorization
 
Запрос должен содержать допустимый заголовок авторизации Xbox Live. Если вызывающий объект не может быть доступ к этому ресурсу, то служба возвращает «403 запрещено» в ответ. Если заголовок отсутствует или является недопустимым, то служба возвращает проверки подлинности 401 в ответе.
  
<a id="ID4EWD"></a>

 
## <a name="request-body"></a>Текст запроса
 
Запрос должен содержать объект JSON со следующими членами.
 
| Участник| Описание| 
| --- | --- | --- | --- | --- | --- | --- | 
| sessionId| Идентификатор сеанса из MPSD.| 
| abortIfQueued| Необязательный параметр, который, будучи задано значение true указывает GSMS не поставить в очередь этого сеанса для ресурса, если он может не быть выполнен немедленно. Если запрос прерван, так как это значение равно true, объект ответа будет содержать <code>"fulfillmentState" : "Aborted"</code>. | 
 
<a id="ID4ERE"></a>

 
### <a name="sample-request"></a>Пример запроса
 

```cpp
{
  "sessionId" : "/serviceconfigs/00000000-0000-0000-0000-000000000000/sessiontemplates/quick/session/scott1",
  "abortIfQueued" : "true"
}

      
```

   
<a id="ID4EZE"></a>

 
## <a name="required-response-headers"></a>Заголовки ответа требуется
 
Ответ всегда будет включать заголовки, показанные в следующей таблице.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Cache-Control|  | Директивы, которые должны быть всей всеми механизмами кэширования цепи запросов и ответов.| 
| Content-Type| application/json| Тип данных в ответе.| 
| Content-Length|  | Длина текста ответа.| 
| X-Content-Type-Options|  |  | 
| X-XblCorrelationId|  | Тип mime тела ответа.| 
| Дата|  |  | 
  
<a id="ID4E5G"></a>

 
## <a name="response-body"></a>Текст ответа
 
Если вызов прошел успешно, служба вернет объект JSON со следующими членами.
 
| Участник| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| pollIntervalMilliseconds| Рекомендуемый интервал в миллисекундах для опроса завершения операции. Обратите внимание, что это не оценку, когда кластер будет готов, но вместо рекомендацию для частоты вызывающего объекта должно выполнять опрос не учитывая текущий пул подписок и нормы запроса и выполнения обновления состояния.| 
| fulfillmentState| Указывает ли указанного сеанса был выделен ресурс немедленно, «Выполнено», добавлено в очередь для обеспечения доступности будущих ресурса, «Queued», или прервана, «Прервано», из-за невозможности выполнить запрос немедленно в том случае, когда запрос Указанный abortIfQueued значение «true». | 
 
<a id="ID4EWH"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{
  "pollIntervalMilliseconds" : "1000",
  "fulfillmentState" : "Fulfilled" | "Queued" | "Aborted"
}
      
```

   
<a id="remarks"></a>

 
## <a name="remarks"></a>Замечания
 
Заголовок следует повторить вызов к службе только в том случае, если получены следующие коды ответов:
 
   * 408 — время ожидания сервера
   * 429 — слишком много запросов
   * 500 — Ошибка сервера
   * 502 — Недопустимый шлюз
   * 503 — Служба недоступна
   * 504 — истекло время ожидания шлюза
   
<a id="ID4EFBAC"></a>

 
## <a name="see-also"></a>См. также
 [/titles/{titleId}/clusters](uri-titlestitleidclusters.md)

  