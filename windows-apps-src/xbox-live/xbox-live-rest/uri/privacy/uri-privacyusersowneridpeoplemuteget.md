---
title: GET (/users/{ownerId}/people/mute)
assetID: 49b6c830-95f7-3200-0e46-0a1af573971c
permalink: en-us/docs/xboxlive/rest/uri-privacyusersowneridpeoplemuteget.html
description: " GET (/users/{ownerId}/people/mute)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 94e2bf4d04619ffa3348ae08fc37964cdc58e7b5
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57661589"
---
# <a name="get-usersowneridpeoplemute"></a>GET (/users/{ownerId}/people/mute)
Получает список отключить звук для пользователя.

  * ["Примечания"](#ID4EQ)
  * [Параметры URI](#ID4EZ)
  * [Влияние параметров конфиденциальности для ресурса](#ID4EEB)
  * [Авторизации](#ID4ENB)
  * [Требуемые заголовки запросов](#ID4ESC)
  * [Текст запроса](#ID4EPE)
  * [Коды состояния HTTP](#ID4E1E)
  * [Заголовки ответа требуется](#ID4E3G)
  * [Текст ответа](#ID4ETAAC)

<a id="ID4EQ"></a>


## <a name="remarks"></a>Замечания

Если целевой объект, этот URI возвращает только этот пользователь, если пользователь находится в списке выключения звука, либо пустой, если пользователь не является.

<a id="ID4EZ"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| ownerId| Строка| Обязательный. Идентификатор пользователя, ресурсом которого осуществляется. Возможными значениями являются «me» <code>xuid({xuid})</code>, или gt({gamertag}). Должен быть авторизованного пользователя. Примеры значений: <code>xuid(2603643534573581)</code>, <code>gt(SomeGamertag)</code>. Максимальный размер: нет. |

<a id="ID4EEB"></a>


## <a name="effect-of-privacy-settings-on-resource"></a>Влияние параметров конфиденциальности для ресурса

Нет.

<a id="ID4ENB"></a>


## <a name="authorization"></a>Authorization

Утверждения авторизации, используемые | Утверждение| Тип| Требуется?| Пример значения|
| --- | --- | --- | --- | --- | --- | --- |
| xuid| 64-разрядное знаковое целое число| Да| 1234567890|

<a id="ID4ESC"></a>


## <a name="required-request-headers"></a>Требуемые заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Authorization | Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: <code>Xauth=&lt;authtoken></code>. Максимальный размер: нет.|
| X-RequestedServiceVersion| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркере авторизации и т. д. Примеры значений: <code>1</code>, <code>vnext</code>. Максимальный размер: нет.|
| Принять| Строка| Типы содержимого, которые являются допустимыми. Пример значения: <code>application/json</code>. Максимальный размер: нет.|

<a id="ID4EPE"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4E1E"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP

Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).

| Код| Фраза причины| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 200| ОК| Успешный запрос для выключения звука списка.|
| 400| Неверный запрос| Идентификатор целевого, заданное в URI не является допустимым.|
| 403| Запрещено| Владелец, заданное в URI не прошедшего проверку подлинности пользователя.|
| 404| Не найден| Владелец, заданное в URI не существует.|

<a id="ID4E3G"></a>


## <a name="required-response-headers"></a>Заголовки ответа требуется

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Content-Type| Строка| Тип MIME тела запроса. Пример значения: <code>application/json</code>|
| Content-Length| Строка| Число байтов, отправляемых в ответе. Пример значения: 34|
| Cache-Control| Строка| Мягкое запрос от сервера, чтобы указать поведение кэширования. Пример: <code>no-cache, no-store</code>|

<a id="ID4ETAAC"></a>


## <a name="response-body"></a>Тело ответа

<a id="ID4EZAAC"></a>


### <a name="sample-response"></a>Пример ответа

См. в разделе [UserList](../../json/json-userlist.md).


```cpp
{
    "users":
    [
        { "xuid":"12345" },
        { "xuid":"23456" }
    ]
}

```


<a id="ID4EJBAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4ELBAC"></a>


##### <a name="parent"></a>Parent

[/Users/ {ownerId} / people/Выкл.](uri-privacyusersowneridpeoplemute.md)
