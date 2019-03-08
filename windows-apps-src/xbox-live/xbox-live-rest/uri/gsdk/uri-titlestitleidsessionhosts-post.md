---
title: POST (/titles/{Title Id}/sessionhosts)
assetID: 8558b336-1af9-8143-9752-477ceb3a8e4e
permalink: en-us/docs/xboxlive/rest/uri-titlestitleidsessionhosts-post.html
description: " POST (/titles/{Title Id}/sessionhosts)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 47e3ecbf0a519b92ae467199e5d454523864310a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655149"
---
# <a name="post-titlestitle-idsessionhosts"></a>POST (/titles/{Title Id}/sessionhosts)
Создание запроса на новый кластер. Доменом для таких URI является `gameserverms.xboxlive.com`.
 
  * [Параметры URI](#ID4EX)
  * [Требуемые заголовки запросов](#ID4EGB)
  * [Текст запроса](#ID4E5B)
  * [Заголовки ответа требуется](#ID4ELD)
  * [Текст ответа](#ID4ESD)
 
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
| Content-Type| application/json| Тип данных, передаваемых.| 
  
<a id="ID4E5B"></a>

 
## <a name="request-body"></a>Текст запроса
 
Запрос должен содержать объект JSON со следующими членами.
 
| Участник| Описание| 
| --- | --- | --- | --- | --- | --- | --- | 
| sessionId| Это, заданное вызывающим объектом идентификатор. Он назначен узел сеанса, который выделяется и возвращается. Позже может ссылаться на определенные sessionhost, этот идентификатор. Оно должно быть глобально уникальным (т. е. GUID).| 
| sandboxId| "Песочницы", который следует узла сеансов для распределения в.| 
| cloudGameId| Идентификатор облачных игр.| 
| Расположения| Упорядоченный список предпочтительных расположений, хотелось бы выделяются из сеанса.| 
| sessionCookie| Это заданное вызывающим объектом непрозрачная строка. Он связан с sessionhost и можно ссылаться в коде игр. Этот элемент можно используйте для передачи небольшого количества информации от клиента к серверу (максимальный размер равен 4 КБ).| 
| gameModelId| Идентификатор режима игры.| 
 
<a id="ID4EDD"></a>

 
### <a name="sample-request"></a>Пример запроса
 

```cpp
{
        "sessionId": "3536d3e6-e85d-4f47-b898-9617d19dabcd",
        "sandboxId": "ISST.0",
        "cloudGameId": "1b7f9925-369c-4301-b1f7-1125dce25776",
        "locations": [
        "West US",
        "East US",
        "West Europe"
        ],
        "sessionCookie": "Caller provided opaque string",
        "gameModeId": "2162d32c-7ac8-40e9-9b1f-56676b8b2513"
        }
      
```

   
<a id="ID4ELD"></a>

 
## <a name="required-response-headers"></a>Заголовки ответа требуется
 
Нет.
  
<a id="ID4ESD"></a>

 
## <a name="response-body"></a>Текст ответа
 
Если вызов прошел успешно, служба вернет объект JSON со следующими членами.
 
| Участник| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Имя узла| Имя узла экземпляра.| 
| portMappings| Сопоставления портов.| 
| Регион| Область экземпляра размещается в.| 
| secureContext| Адрес безопасные устройства.| 
 
<a id="ID4ESE"></a>

 
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
        "region": "WestUS",
        "secureContext": "AQDc8Hen/QCDJwWRPcW/1QEEAiABAACdOJU8JNujcXyUPwUBCnue+g=="
        }
      
```

   
<a id="remarks"></a>

 
## <a name="remarks"></a>Замечания
 
Заголовок следует повторить вызов к службе только в том случае, если получены следующие коды ответов:
 
   * 200 — успешно - ответе, возвращенном.
   * 400 — недопустимые параметры или текст запроса имеет неправильный формат.
   * 401 — не санкционировано
   * 404 — код не удалось найти подписки, назначенные ему.
   * 409 — возможна при идентичных запроса (тем же идентификатором sessionId) в примерно то же время, этот ответ. Если выполняется запрос на выделение и узла сеансов уже имеет указанный sessionId и уже активен возвращаются сведения, детализирующие, sessionhost. Если узел сеанса тем не менее, не Active, но возникнет конфликт.
   * 500 — неожиданную ошибку сервера.
   * 503 — не sessionhosts StandingBy. Повторите запрос, если некоторые из этих ресурсов предоставляются бесплатно.
   
<a id="ID4EFG"></a>

 
## <a name="see-also"></a>См. также
 [/titles/{titleId}/sessionhosts](uri-titlestitleidsessionhosts.md)

  