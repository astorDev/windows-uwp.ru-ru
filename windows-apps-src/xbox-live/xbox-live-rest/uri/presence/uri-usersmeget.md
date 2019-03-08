---
title: GET (/users/me)
assetID: 726c279b-73fb-02ea-cbff-700ff2dc31af
permalink: en-us/docs/xboxlive/rest/uri-usersmeget.html
description: " GET (/users/me)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: b06305fde989d0c30570beda5d4b0aabe7bf0518
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57606729"
---
# <a name="get-usersme"></a>GET (/users/me)
Получить текущего пользователя [PresenceRecord](../../json/json-presencerecord.md) без необходимости знать XUID пользователя.
Доменом для таких URI является `userpresence.xboxlive.com`.

  * [Параметры строки запроса](#ID4EZ)
  * [Авторизации](#ID4EIC)
  * [Требуемые заголовки запросов](#ID4ELD)
  * [Необязательные заголовки запросов](#ID4EPF)
  * [Текст запроса](#ID4EPG)
  * [Текст ответа](#ID4E1G)

<a id="ID4EZ"></a>


## <a name="query-string-parameters"></a>Параметры строки запроса

| Параметр| Тип| Описание|
| --- | --- | --- |
| level| Строка| Необязательно. <ul><li><b>пользователь</b>: Возвращает только те пользователя.</li><li><b>устройство</b>: Возвращает пользователя узлы узла и устройством.</li><li><b>Заголовок</b>: По умолчанию. Возвращает все дерево, за исключением действия.</li><li><b>все</b>: Возвращает все дерево, включая присутствие уровня активности.</li></ul> | 

<a id="ID4EIC"></a>


## <a name="authorization"></a>Authorization

| Тип| Обязательно| Описание| Ответ, если он отсутствует|
| --- | --- | --- | --- | --- | --- | --- |
| XUID| Да| Идентификатор пользователя Xbox (XUID) вызывающего объекта| «403 запрещено»|

<a id="ID4ELD"></a>


## <a name="required-request-headers"></a>Требуемые заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: «XBL3.0 x =&lt;userhash >;&lt; токен >».|
| x-xbl контракт version| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д. Примеры значений 3 vnext.|
| Принять| Строка| Типы содержимого, которые являются допустимыми. Единственный поддерживаемый наличием application/json, но он должен быть указан в заголовке.|
| Примите языка| Строка| Языковой стандарт, допустимые для строк в ответе. Примеры значений: en US.|
| Hyper-V| Строка| Доменное имя сервера. Пример значения: presencebeta.xboxlive.com.|

<a id="ID4EPF"></a>


## <a name="optional-request-headers"></a>Необязательные заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| X-RequestedServiceVersion|  | Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д. Значение по умолчанию: 1.|

<a id="ID4EPG"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4E1G"></a>


## <a name="response-body"></a>Тело ответа

<a id="ID4EAH"></a>


### <a name="sample-response"></a>Пример ответа

Этот метод возвращает [PresenceRecord](../../json/json-presencerecord.md).


```cpp
{
  xuid:"0123456789",
  state:"online",
  devices:
  [{
    type:"D",
    titles:
    [{
      id:"12341234",
      name:"Contoso 5",
      state:"active",
      placement:"fill",
      timestamp:"2012-09-17T07:15:23.4930000",
      activity:
      {
        richPresence:"Team Deathmatch on Nirvana"
      }
    },
    {
      id:"12341235",
      name:"Contoso Waypoint",
      timestamp:"2012-09-17T07:15:23.4930000",
      placement:"snapped",
      state:"active",
      activity:
      {
        richPresence:"Using radar"
      }
    }]
  },
  {
    type:W8,
    titles:
    [{
      id:"23452345",
      name:"Contoso Gamehelp",
      state:"active",
      placement:"full",
      timestamp:"2012-09-17T07:15:23.4930000",
      activity:
      {
        richPresence:"Nirvana page",
      }
    }]
  }]
}

```


<a id="ID4EQH"></a>


## <a name="see-also"></a>См. также

<a id="ID4ESH"></a>


##### <a name="parent"></a>Parent

[/ users/me](uri-usersme.md)
