---
title: GET (/users/{requestorId}/permission/validate)
assetID: 8d22c668-af9a-1d24-8d65-830c2ce913d7
permalink: en-us/docs/xboxlive/rest/uri-privacyusersrequestoridpermissionvalidateget.html
description: " GET (/users/{requestorId}/permission/validate)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 07ccac63b3e6681ea35405314b0cd8b93aa60f9a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57594759"
---
# <a name="get-usersrequestoridpermissionvalidate"></a>GET (/users/{requestorId}/permission/validate)
Возвращает ответ Да или нет, о том, разрешено ли пользователь для выполнения указанного действия с целевого пользователя.

  * [Параметры URI](#ID4EQ)
  * [Параметры строки запроса](#ID4E2)
  * [Авторизации](#ID4EDC)
  * [Требуемые заголовки запросов](#ID4EID)
  * [Текст запроса](#ID4ETE)
  * [Коды состояния HTTP](#ID4E5E)
  * [Заголовки ответа требуется](#ID4ETG)
  * [Текст ответа](#ID4EKAAC)

<a id="ID4EQ"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| requestorId| Строка| Обязательный. Идентификатор пользователя, выполняющего действие. Возможные значения: <code>xuid({xuid})</code> и <code>me</code>. Это должен быть вошедшего в систему пользователя. Пример значения: <code>xuid(0987654321)</code>.|

<a id="ID4E2"></a>


## <a name="query-string-parameters"></a>Параметры строки запроса

| Параметр| Тип| Описание|
| --- | --- | --- | --- | --- | --- |
| настройка| Строковое перечисление| Значение PermissionId необходимо проверить на соответствие. Пример значения: «CommunicateUsingText».|
| target| Строка| Идентификатор пользователя, от которого должна выполнять действие. Возможные значения: <code>xuid({xuid})</code>. Примеры значений: <code>xuid(0987654321)</code>|

<a id="ID4EDC"></a>


## <a name="authorization"></a>Authorization

Утверждения авторизации, используемые | Утверждение| Тип| Требуется?| Пример значения|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| xuid| 64-разрядное знаковое целое число| Да| 1234567890|

<a id="ID4EID"></a>


## <a name="required-request-headers"></a>Требуемые заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Примеры значений: <code>XBL3.0 x=&lt;userhash>;&lt;token></code>|
| X-RequestedServiceVersion| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос будет перенаправлен только к этой службе после проверки допустимости заголовок, утверждения в маркер проверки подлинности и т. д. Пример значения: 1.|

<a id="ID4ETE"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4E5E"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP

Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).

| Код| Фраза причины| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 200| ОК| Сеанс был успешно извлечен.|
| 400| Недопустимый запрос.| Примеры: неверный параметр идентификаторы неверные идентификаторы URI, и т.д.|
| 404| Пользователь, указанный в URI не существует.| Не удалось найти указанный ресурс.|

<a id="ID4ETG"></a>


## <a name="required-response-headers"></a>Заголовки ответа требуется

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Content-Type| Строка| Тип MIME тела запроса. Пример значения: <code>application/json</code>|
| Content-Length| Строка| Число байтов, отправляемых в ответе. Пример значения: 34|
| Cache-Control| Строка| Мягкое запрос от сервера, чтобы указать поведение кэширования. Пример: <code>no-cache, no-store</code>|

<a id="ID4EKAAC"></a>


## <a name="response-body"></a>Тело ответа

См. в разделе [PermissionCheckResponse (JSON)](../../json/json-permissioncheckresponse.md).

<a id="ID4EWAAC"></a>


### <a name="sample-response"></a>Пример ответа


```cpp
{
    "isAllowed": false,
    "reasons":
    [
        {"reason": "BlockedByRequestor"},
        {"reason": "MissingPrivilege", "restrictedSetting": "VideoCommunications"}
    ]
}

```


<a id="ID4EABAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4ECBAC"></a>


##### <a name="parent"></a>Parent

[/users/{requestorId}/permission/validate](uri-privacyusersrequestoridpermissionvalidate.md)

 [Перечисление PermissionId](../../enums/privacy-enum-permissionid.md)
