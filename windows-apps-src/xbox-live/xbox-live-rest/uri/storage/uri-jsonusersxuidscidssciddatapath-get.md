---
title: GET (/json/users/xuid({xuid})/scids/{scid}/data/{path})
assetID: ab73c1af-d914-b498-6a12-8f74eec349d0
permalink: en-us/docs/xboxlive/rest/uri-jsonusersxuidscidssciddatapath-get.html
description: " GET (/json/users/xuid({xuid})/scids/{scid}/data/{path})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: f292ca5d1d0968445d91a507384188af1db5cf5b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57594049"
---
# <a name="get-jsonusersxuidxuidscidssciddatapath"></a>GET (/json/users/xuid({xuid})/scids/{scid}/data/{path})
Перечисляет сведения о файле по указанному пути. Доменом для таких URI является `titlestorage.xboxlive.com`.
 
  * [Параметры URI](#ID4EX)
  * [Дополнительные параметры строки запроса](#ID4ECB)
  * [Авторизации](#ID4EUC)
  * [Требуемые заголовки запросов](#ID4EBD)
  * [Текст запроса](#ID4EKE)
  * [Коды состояния HTTP](#ID4EXE)
  * [Текст ответа](#ID4EKCAC)
 
<a id="ID4EX"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| 64-разрядных целых беззнаковых| Xbox идентификатора пользователя (XUID) исполнителя, выполняющего запрос.| 
| scid| guid| Идентификатор конфигурации службы для поиска.| 
| путь| Строка| Путь к возвращаемых элементов данных. Все каталоги и подкаталоги сопоставления вернуться. Допустимые символы включать прописные буквы (A-Z), строчные буквы (a – z), цифры (0 – 9), символ подчеркивания (_) и косой черты (/). Может быть пустым. Максимальная длина 256.| 
  
<a id="ID4ECB"></a>

 
## <a name="optional-query-string-parameters"></a>Дополнительные параметры строки запроса 
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | 
| skipItems| int| Возвращает элементы, начиная с N + 1 в коллекции, например, пропустить N элементов.| 
| continuationToken| Строка| Возвращает элементы, начиная с токен данного продолжения. Параметр continuationToken имеет приоритет над skipItems, если указаны оба. Другими словами skipItems параметр учитывается, если присутствует параметр continuationToken.| 
| maxItems| int| Максимальное количество элементов, возвращаемых из коллекции, которые можно объединять с skipItems и continuationToken можно получить диапазон элементов. Служба может предоставлять значение по умолчанию если maxItems отсутствует и может вернуть меньше, чем maxItems, даже если на последней странице результаты еще не были возвращены. | 
  
<a id="ID4EUC"></a>

 
## <a name="authorization"></a>Authorization 
 
Запрос должен содержать допустимый заголовок авторизации Xbox LIVE. Если вызывающий объект не может быть доступ к этому ресурсу, служба вернет ответ 403 запрещено. Если заголовок отсутствует или является недопустимым, служба вернет ответ 401 Нет доступа. 
  
<a id="ID4EBD"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| x-xbl контракт version| 1| Версия контракта API.| 
| Authorization| XBL3.0 x = [hash]; [токен]| Токен проверки подлинности STS. STSTokenString заменяется маркером, возвращенным в запросе проверки подлинности. Дополнительные сведения о получении маркера службы маркеров безопасности и создании заголовок авторизации см. аутентификация и авторизация Xbox LIVE запросов к службам.| 
  
<a id="ID4EKE"></a>

 
## <a name="request-body"></a>Тело запроса 
 
Объекты не будут отправлены в тексте этого запроса.
  
<a id="ID4EXE"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP 
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 200| ОК | Запрос выполнен успешно.| 
| 201| Создано | Сущность была создана.| 
| 400| Неверный запрос | Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.| 
| 401| Недостаточно прав | Запрос требует проверки подлинности пользователя.| 
| 403| Запрещено | Запрос не допускается для пользователя или службы.| 
| 404| Не найден | Не удалось найти указанный ресурс.| 
| 406| Неприемлемо | Версия ресурса не поддерживается.| 
| 408| Время ожидания запроса | Запрос заняло слишком много времени.| 
| 500| Внутренняя ошибка сервера | Сервер обнаружил непредвиденное условие, что помешало ему выполнить запрос.| 
| 503| Служба недоступна | Запрос был отрегулирован, выполните запрос еще раз после повторных попыток клиента значение в секундах (например 5 секунд позже).| 
  
<a id="ID4EKCAC"></a>

 
## <a name="response-body"></a>Тело ответа
 
Если вызов прошел успешно, служба вернет массив [TitleBlob](../../json/json-titleblob.md) объектов.
 
<a id="ID4EYCAC"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{
"blobs":
[
    {
        "fileName":"foo\bar\blob.txt,json",
        "clientFileTime":"2012-01-01T01:02:03.1234567Z",
        "displayName":"Friendly Name",
        "size":12,
        "etag":"0x8CEB3E4F8F3A5BF"
    },
    {
        "fileName":"foo\bar\blob2.txt,json",
        "displayName":"Blob 2",
        "size":4,
        "etag":"0x8CEB3FE57F1A142"
    },
    {
        "fileName":"foo\jsonblob.txt,json",
        "size":15,
        "etag":"0x8CEB40152B4A6F8"
    }
],
"pagingInfo":
    {
        "continuationToken":"54",
    }
}
         
```

   
<a id="ID4EEDAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EGDAC"></a>

 
##### <a name="parent"></a>Parent  

[/data/ /JSON/Users/xuid({xuid}) /scids/ {scid} {path}](uri-jsonusersxuidscidssciddatapath.md)

  
<a id="ID4ESDAC"></a>

 
##### <a name="reference--titleblob-jsonjsonjson-titleblobmd"></a>Справочник по [TitleBlob (JSON)](../../json/json-titleblob.md)

 [PagingInfo (JSON)](../../json/json-paginginfo.md)

   