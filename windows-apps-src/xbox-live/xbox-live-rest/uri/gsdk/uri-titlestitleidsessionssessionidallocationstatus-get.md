---
title: GET (/titles/{titleId}/sessions/{sessionId}/allocationStatus)
assetID: 613ba53f-03cb-5ed3-a5ba-be59e5a146d1
permalink: en-us/docs/xboxlive/rest/uri-titlestitleidsessionssessionidallocationstatus-get.html
description: " GET (/titles/{titleId}/sessions/{sessionId}/allocationStatus)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 793d634bc1e3dc431b3797759751afb6dfd9c00a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57650859"
---
# <a name="get-titlestitleidsessionssessionidallocationstatus"></a>GET (/titles/{titleId}/sessions/{sessionId}/allocationStatus)
Возвращает состояние размещения sessionhost, идентифицируемый его sessionId. Домены для таких URI не `gameserverds.xboxlive.com` и `gameserverms.xboxlive.com`.
 
  * [Требуемые заголовки запросов](#ID4E4)
  * [Заголовки ответа требуется](#ID4EEB)
  * [Текст ответа](#ID4ELB)
 
<a id="ID4E4"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
Нет.
  
<a id="ID4EEB"></a>

 
## <a name="required-response-headers"></a>Заголовки ответа требуется
 
Нет.
  
<a id="ID4ELB"></a>

 
## <a name="response-body"></a>Текст ответа
 
Если вызов прошел успешно, служба вернет объект JSON со следующими членами.
 
| Участник| Описание| 
| --- | --- | 
| Описание| Возвращает пустую строку (слева в для обеспечения обратной совместимости).| 
| clusterId| Возвращает пустую строку (слева в для обеспечения обратной совместимости).| 
| Имя узла| URL-адрес узла сеансов.| 
| status| Указывает, в очередь, выполненных или прервана.| 
| sessionHostId| Идентификатор сеанса узла.| 
| sessionId| Клиент предоставляет (во время выделения) идентификатор сеанса.| 
| secureContext| Адрес безопасные устройства.| 
| portMappings| Сопоставления порта для экземпляра.| 
| Регион| Расположение экземпляра.| 
| TicketId| Идентификатор текущего сеанса (слева в для обеспечения обратной совместимости).| 
| gameHostId| Текущий sessionHostId, (слева в для обеспечения обратной совместимости).| 
 
<a id="ID4EGD"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{
        "hostName": "r111ybf4drgo12kq25tc-082yo7y9sz72f2odtq1ya5yhda-155169995-ncus.cloudapp.net",
        "portMappings": [
        {
        "Key": "GSDKTCPTest",
        "Value": {
        "internal": 10100,
        "external": 10103
        }
        },
        {
        "Key": "GSDKUDPTest",
        "Value": {
        "internal": 5000,
        "external": 5000
        }
        }
        ],
        "status",:"Fulfilled",
        "region": "WestUS",
        "secureContext": "AQDc8Hen/QCDJwWRPcW/1QEEAiABAACdOJU8JNujcXyUPwUBCnue+g==",
        "sessionId": "05328154-1bbe-4f5b-8caa-4e44106712f9",
        "description": "",
        "clusterId": "",
        "sessionHostId": "r111ybf4drgo12kq25tc-082yo7y9sz72f2odtq1ya5yhda-155169995-ncus.GSDKAgent_IN_0.0",
        "ticketId": "05328154-1bbe-4f5b-8caa-4e44106712f9",
        "gameHostId": "r111ybf4drgo12kq25tc-082yo7y9sz72f2odtq1ya5yhda-155169995-ncus.GSDKAgent_IN_0.0"

      
```

  
<a id="remarks"></a>

 
### <a name="remarks"></a>Замечания
 
Заголовок следует повторить вызов к службе только в том случае, если получены следующие коды ответов:
 
   * 200 — успех 
   * 400 — запрос содержит недопустимые параметры 
   * 401 — не санкционировано 
   * 404 — заголовок ID или идентификатор билета был недопустим или не найден 
   * 500 — неожиданную ошибку сервера. 
    