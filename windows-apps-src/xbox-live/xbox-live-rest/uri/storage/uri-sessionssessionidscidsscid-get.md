---
title: GET (/sessions/{sessionId}/scids/{scid})
assetID: 1feaceed-ba0d-0b0c-e809-44ba41f2e4ea
permalink: en-us/docs/xboxlive/rest/uri-sessionssessionidscidsscid-get.html
description: " GET (/sessions/{sessionId}/scids/{scid})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 8a0dcd24c57cbc7dce596961afcfd7e0eba476c3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57650499"
---
# <a name="get-sessionssessionidscidsscid"></a>GET (/sessions/{sessionId}/scids/{scid})
Извлекает сведения о квотах для этого типа хранения. Доменом для таких URI является `titlestorage.xboxlive.com`.
 
  * [Параметры URI](#ID4EX)
  * [Авторизации](#ID4ECB)
  * [Требуемые заголовки запросов](#ID4ENB)
  * [Текст запроса](#ID4EWC)
  * [Коды состояния HTTP](#ID4EBD)
  * [Текст ответа](#ID4E2H)
 
<a id="ID4EX"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| sessionId| Строка| Идентификатор сеанса для поиска.| 
| scid| guid| Идентификатор конфигурации службы для поиска.| 
  
<a id="ID4ECB"></a>

 
## <a name="authorization"></a>Authorization
 
Запрос должен содержать допустимый заголовок авторизации Xbox LIVE. Если вызывающий объект не может быть доступ к этому ресурсу, служба вернет ответ 403 запрещено. Если заголовок отсутствует или является недопустимым, служба вернет ответ 401 Нет доступа. 
  
<a id="ID4ENB"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | --- | 
| x-xbl контракт version| 1| Версия контракта API.| 
| Authorization| XBL3.0 x = [hash]; [токен]| Токен проверки подлинности STS. STSTokenString заменяется маркером, возвращенным в запросе проверки подлинности. Дополнительные сведения о получении маркера службы маркеров безопасности и создании заголовок авторизации см. аутентификация и авторизация Xbox LIVE запросов к службам.| 
  
<a id="ID4EWC"></a>

 
## <a name="request-body"></a>Тело запроса
 
Объекты не будут отправлены в тексте этого запроса.
  
<a id="ID4EBD"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 200| ОК| Запрос выполнен успешно.| 
| 201| Создано| Сущность была создана.| 
| 400| Неверный запрос| Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.| 
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.| 
| 403| Запрещено| Запрос не допускается для пользователя или службы.| 
| 404| Не найден| Не удалось найти указанный ресурс.| 
| 406| Неприемлемо| Версия ресурса не поддерживается.| 
| 408| Время ожидания запроса| Запрос заняло слишком много времени.| 
| 500| Внутренняя ошибка сервера| Сервер обнаружил непредвиденное условие, что помешало ему выполнить запрос.| 
| 503| Служба недоступна| Запрос был отрегулирован, выполните запрос еще раз после повторных попыток клиента значение в секундах (например 5 секунд позже).| 
  
<a id="ID4E2H"></a>

 
## <a name="response-body"></a>Тело ответа
 
Если вызов выполняется успешно, служба вернет [quotaInfo (JSON)](../../json/json-quota.md) объекта. 
 
<a id="ID4EKAAC"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{
  "quotaInfo":
  {
    "usedBytes":619,
    "quotaBytes":16777216
  }
}
         
```

   
<a id="ID4EWAAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EYAAC"></a>

 
##### <a name="parent"></a>Parent 

[/sessions/{sessionId}/scids/{scid}](uri-sessionssessionidscidsscid.md)

  
<a id="ID4ECBAC"></a>

 
##### <a name="reference"></a>Справочные материалы 

[quotaInfo (JSON)](../../json/json-quota.md)

   