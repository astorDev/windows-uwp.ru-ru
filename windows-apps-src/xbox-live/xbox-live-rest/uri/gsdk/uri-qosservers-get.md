---
title: GET (/qosservers)
assetID: 8b940c1b-947c-eab3-78ed-4384f57ea0bd
permalink: en-us/docs/xboxlive/rest/uri-qosservers-get.html
description: " GET (/qosservers)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 02d24dbf1d189b759784dbbfa7052e2c218ec27e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57632069"
---
# <a name="get-qosservers"></a>GET (/qosservers)
URI, вызывается клиентом, чтобы получить список серверов качества обслуживания, доступных для использования с вычислений Xbox Live. Домены для таких URI не `gameserverds.xboxlive.com` и `gameserverms.xboxlive.com`.
 
  * [Требуемые заголовки запросов](#ID4EBB)
  * [Заголовки ответа требуется](#ID4EUC)
  * [Текст ответа](#ID4EVD)
 
<a id="ID5EG"></a>

 
## <a name="host-name"></a>Имя узла

gameserverds.xboxlive.com
 
<a id="ID4EBB"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
При выполнении запроса, заголовки, показанные в следующей таблице являются обязательными.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | 
| Content-Type| application/json| Тип данных, передаваемых.| 
| Hyper-V| gameserverds.xboxlive.com|  | 
| Content-Length|  | Длина объекта запроса.| 
| x-xbl контракт version| 1| Версия контракта API.| 
  
<a id="ID4EUC"></a>

 
## <a name="required-response-headers"></a>Заголовки ответа требуется
 
Ответ всегда будет включать заголовки, показанные в следующей таблице.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | --- | 
| Content-Type| application/json| Тип данных в тексте ответа.| 
| Content-Length|  | Длина текста ответа.| 
  
<a id="ID4EVD"></a>

 
## <a name="response-body"></a>Текст ответа
 
Если вызов прошел успешно, служба вернет объект JSON со следующими членами.
 
| Участник| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| qosservers| Массив сведений о сервере.| 
| serverFqdn| Полное доменное имя сервера.| 
| serverSecureDeviceAddress| Защита устройства адрес сервера.| 
| TargetLocation| Географическое расположение сервера.| 
 
<a id="ID4EUE"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{ 
  "qosServers" : 
  [ 
    { "serverFqdn" : "xblqosncus.cloudapp.net", "serverSecureDeviceAddress" : "&lt;base-64 encoded blob>", "targetLocation" : "North Central US" },
    { "serverFqdn" : "xblqoswus.cloudapp.net", "serverSecureDeviceAddress" : "&lt;base-64 encoded blob>", "targetLocation" : "West US" },
  ]
}

      
```

   
<a id="ID4EBF"></a>

 
## <a name="see-also"></a>См. также
 [/qosservers](uri-qosservers.md)

  