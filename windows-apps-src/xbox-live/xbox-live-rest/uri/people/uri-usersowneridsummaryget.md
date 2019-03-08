---
title: GET (/users/{ownerId}/summary)
assetID: 754190c9-b15d-f34b-1dca-5c92f6f67d12
permalink: en-us/docs/xboxlive/rest/uri-usersowneridsummaryget.html
description: " GET (/users/{ownerId}/summary)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 3b228adab7b035ec8f4e65fc8b7458228a677987
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57613999"
---
# <a name="get-usersowneridsummary"></a>GET (/users/{ownerId}/summary)
Возвращает сводные данные о владельце с точки зрения вызывающей стороны.

  * [Параметры URI](#ID4EQ)
  * [Авторизации](#ID4E2)
  * [Требуемые заголовки запросов](#ID4EBC)
  * [Необязательные заголовки запросов](#ID4EHD)
  * [Текст запроса](#ID4EXE)
  * [Коды состояния HTTP](#ID4ECF)
  * [Заголовки ответа требуется](#ID4EZG)
  * [Текст ответа](#ID4EGAAC)

<a id="ID4EQ"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| ownerId| Строка| Идентификатор пользователя, ресурсом которого осуществляется. Возможными значениями являются «me», xuid({xuid}) или gt({gamertag}). Примеры значений: <code>me</code>, <code>xuid(2603643534573581)</code>, <code>gt(SomeGamertag)</code>|

<a id="ID4E2"></a>


## <a name="authorization"></a>Authorization

| <b>Название</b>| <b>Тип</b>| <b>Описание</b>|
| --- | --- | --- | --- | --- | --- |
| xuid| 64-разрядного целого числа без знака| Обязательный. идентификатор пользователя, вызывающего объекта. Пример значения: 2533274790395904|

<a id="ID4EBC"></a>


## <a name="required-request-headers"></a>Требуемые заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Authorization| Строка| Данные авторизации. Обычно это зашифрованный маркер XSTS. Пример значения: <b>XBL3.0 x = [hash]; [токен] </b>.|

<a id="ID4EHD"></a>


## <a name="optional-request-headers"></a>Необязательные заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| x-xbl контракт version| Строка| Имя или номер, службы, к которому следует направлять этот запрос сборки. Запрос будет перенаправлен только к этой службе после проверки допустимости заголовок, утверждения в маркер проверки подлинности и т. д. Примеры значений 1|
| Принять| Строка| Типы содержимого, которые являются допустимыми. Все ответы будут <code>application/json</code>.|

<a id="ID4EXE"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4ECF"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP

Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).

| Код| Фраза причины| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 200| ОК| Сеанс был успешно извлечен.|
| 400| Неверный запрос| Идентификаторы пользователей были имеет неправильный формат.|
| 403| Запрещено| Не удалось проанализировать XUID утверждения из заголовка авторизации.|

<a id="ID4EZG"></a>


## <a name="required-response-headers"></a>Заголовки ответа требуется

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Content-Length| Строка| Число байтов, отправляемых в ответе. Пример значения: 232.|
| Content-Type| Строка| Тип MIME тела ответа. Это должен быть <b>application/json</b>.|

<a id="ID4EGAAC"></a>


## <a name="response-body"></a>Тело ответа

См. в разделе [PersonSummary (JSON)](../../json/json-personsummary.md).

<a id="ID4ESAAC"></a>


### <a name="sample-response"></a>Пример ответа


```cpp
{
    "targetFollowingCount": 87,
    "targetFollowerCount": 19,
    "isCallerFollowingTarget": true,
    "isTargetFollowingCaller": false,
    "hasCallerMarkedTargetAsFavorite": true,
    "hasCallerMarkedTargetAsKnown": true,
    "legacyFriendStatus": "None",
    "recentChangeCount": 5,
    "watermark": "5246775845000319351"
}

```


<a id="ID4E3AAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4E5AAC"></a>


##### <a name="parent"></a>Parent

[/Users/ {ownerId} / summary](uri-usersowneridsummary.md)
