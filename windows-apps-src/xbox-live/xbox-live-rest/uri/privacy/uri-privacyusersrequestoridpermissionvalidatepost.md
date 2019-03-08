---
title: POST (/users/{requestorId}/permission/validate)
assetID: 7a5ea583-ffca-5da7-a02a-535c52535928
permalink: en-us/docs/xboxlive/rest/uri-privacyusersrequestoridpermissionvalidatepost.html
description: " POST (/users/{requestorId}/permission/validate)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: edd91560ffb5d81b30da4b1453612cc5853a456f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57623429"
---
# <a name="post-usersrequestoridpermissionvalidate"></a>POST (/users/{requestorId}/permission/validate)
Получает набор Да или нет ответов на вопросы о разрешено ли пользователю для выполнения указанных действий с группы целевых пользователей.

  * ["Примечания"](#ID4EQ)
  * [Параметры URI](#ID4ECB)
  * [Авторизации](#ID4ENB)
  * [Требуемые заголовки запросов](#ID4ESC)
  * [Текст запроса](#ID4E4D)
  * [Коды состояния HTTP](#ID4ETE)
  * [Заголовки ответа требуется](#ID4EIG)
  * [Текст ответа](#ID4E5H)

<a id="ID4EQ"></a>


## <a name="remarks"></a>Замечания

Текст запроса берет список пользователей и список параметров, а результатом является разрешенного и запрещенного результат для каждой пары пользователя и зафиксировать.

В сценариях многопользовательские кросс сети (где конфиденциальности проверки связи необходимо выполнить пользователям, имеющим идентификатор пользователя Xbox (XUID) и пользователей сети, которые этого не сделать) обратитесь к [PermissionCheckBatchRequest (JSON)](../../json/json-permissioncheckbatchrequest.md) для типов пользователей.

<a id="ID4ECB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| requestorId| Строка| Обязательный. Идентификатор пользователя, выполняющего действие. Возможные значения: <code>xuid({xuid})</code> и <code>me</code>. Это должен быть вошедшего в систему пользователя. Пример значения: <code>xuid(0987654321)</code>.|

<a id="ID4ENB"></a>


## <a name="authorization"></a>Authorization

Утверждения авторизации, используемые | Утверждение| Тип| Требуется?| Пример значения|
| --- | --- | --- | --- | --- | --- | --- |
| xuid| 64-разрядное знаковое целое число| Да| 1234567890|

<a id="ID4ESC"></a>


## <a name="required-request-headers"></a>Требуемые заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Примеры значений: <code>XBL3.0 x=&lt;userhash>;&lt;token></code>|
| X-RequestedServiceVersion| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос будет перенаправлен только к этой службе после проверки допустимости заголовок, утверждения в маркер проверки подлинности и т. д. Пример значения: 1.|

<a id="ID4E4D"></a>


## <a name="request-body"></a>Тело запроса

<a id="ID4EDE"></a>


### <a name="required-members"></a>Обязательные члены

См. в разделе [PermissionCheckBatchRequest (JSON)](../../json/json-permissioncheckbatchrequest.md).


```cpp
{
    "users":
    [
        {"xuid":"12345"},
        {"xuid":"54321"}
    ],
    "permissions":
    [
        "ViewTargetGameHistory",
        "ViewTargetProfile"
    ]
}

```


<a id="ID4ETE"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP

Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).

| Код| Фраза причины| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 200| ОК| Сеанс был успешно извлечен.|
| 400| Недопустимый запрос.| Примеры: неверный параметр идентификаторы неверные идентификаторы URI, и т.д.|
| 404| Пользователь, указанный в URI не существует.| Не удалось найти указанный ресурс.|

<a id="ID4EIG"></a>


## <a name="required-response-headers"></a>Заголовки ответа требуется

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Content-Type| Строка| Тип MIME тела запроса. Пример значения: <code>application/json</code>|
| Content-Length| Строка| Число байтов, отправляемых в ответе. Пример значения: 34|
| Cache-Control| Строка| Мягкое запрос от сервера, чтобы указать поведение кэширования. Пример: <code>no-cache, no-store</code>|

<a id="ID4E5H"></a>


## <a name="response-body"></a>Тело ответа

См. в разделе [PermissionCheckBatchResponse (JSON)](../../json/json-permissioncheckbatchresponse.md).

<a id="ID4ELAAC"></a>


### <a name="sample-response"></a>Пример ответа


```cpp
{
    "responses":
    [
        {
            "user": {"xuid":"12345"},
            "permissions":
            [
                {
                    "isAllowed":true
                },
                {
                    "isAllowed":true
                }
            ]
        },
        {
            "user": {"xuid":"54321"},
            "permissions":
            [
                {
                    "isAllowed":false,
                    "reasons":
                    [
                        {"reason":"NotAllowed"}
                    ]
                },
                {
                    "isAllowed":false,
                    "reasons":
                    [
                        {"reason":"PrivilegeRest", "restrictedSetting":"AllowProfileViewing"}
                    ]
                }
            ]
        }
    ]
}

```


<a id="ID4EVAAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EXAAC"></a>


##### <a name="parent"></a>Parent

[/users/{requestorId}/permission/validate](uri-privacyusersrequestoridpermissionvalidate.md)

 [Перечисление PermissionId](../../enums/privacy-enum-permissionid.md)
