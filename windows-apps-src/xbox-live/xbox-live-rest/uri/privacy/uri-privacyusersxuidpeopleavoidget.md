---
title: GET (/users/{ownerId}/people/avoid)
assetID: e3420658-4738-8e80-44da-8281726fce01
permalink: en-us/docs/xboxlive/rest/uri-privacyusersxuidpeopleavoidget.html
description: " GET (/users/{ownerId}/people/avoid)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 745893b4b975b5fbf64fe76591ec15d18af59d73
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57641619"
---
# <a name="get-usersowneridpeopleavoid"></a>GET (/users/{ownerId}/people/avoid)
Получает список Избегайте для пользователя.

  * ["Примечания"](#ID4EQ)
  * [Параметры URI](#ID4EZ)
  * [Авторизации](#ID4EEB)
  * [Требуемые заголовки запросов](#ID4EJC)
  * [Коды состояния HTTP](#ID4EYD)
  * [Заголовки ответа требуется](#ID4E1F)
  * [Текст ответа](#ID4ESH)

<a id="ID4EQ"></a>


## <a name="remarks"></a>Замечания

Если целевой объект, возвращает пользователя, если они находятся в список заблокированных, либо пустой ли они не.

<a id="ID4EZ"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| ownerId| Строка| Обязательный. Идентификатор пользователя, ресурсом которого осуществляется. Возможные значения: <code>xuid({xuid})</code>. Должен быть авторизованного пользователя. Пример значения: <code>xuid(2603643534573581)</code>. Максимальный размер: нет. |

<a id="ID4EEB"></a>


## <a name="authorization"></a>Authorization

Утверждения авторизации, используемые | Утверждение| Тип| Требуется?| Пример значения|
| --- | --- | --- | --- | --- | --- | --- |
| xuid| 64-разрядное знаковое целое число| Да| 1234567890|

<a id="ID4EJC"></a>


## <a name="required-request-headers"></a>Требуемые заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Authorization | Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: <code>Xauth=&lt;authtoken></code>. Максимальный размер: нет.|
| Принять| Строка| Типы содержимого, которые являются допустимыми. Пример значения: <code>application/json</code>. Максимальный размер: нет.|

<a id="ID4EYD"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP

Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).

| Код| Фраза причины| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 200| ОК| Сеанс был успешно извлечен.|
| 400| Неверный запрос| Идентификатор целевого, заданное в URI не является допустимым.|
| 403| Запрещено| Владелец, заданное в URI не прошедшего проверку подлинности пользователя.|
| 404| Не найден| Владелец, заданное в URI не существует.|

<a id="ID4E1F"></a>


## <a name="required-response-headers"></a>Заголовки ответа требуется

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Content-Type| Строка| Тип MIME тела запроса. Пример значения: <code>application/json</code>. Максимальный размер: нет.|
| Content-Length| Строка| Число байтов, отправляемых в ответе. Пример значения: 34. Максимальный размер: нет.|
| Cache-Control| Строка| Мягкое запрос от сервера, чтобы указать поведение кэширования. Пример значения: <code>application/json</code>. Максимальный размер: нет.|

<a id="ID4ESH"></a>


## <a name="response-body"></a>Тело ответа

<a id="ID4EYH"></a>


### <a name="sample-response"></a>Пример ответа


```cpp
{
    "users":
    [
        { "xuid":"12345" },
        { "xuid":"23456" }
    ]
}

```


<a id="ID4EDAAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EFAAC"></a>


##### <a name="parent"></a>Parent

[/Users/ {ownerId} / people/избежать](uri-privacyusersxuidpeopleavoid.md)
