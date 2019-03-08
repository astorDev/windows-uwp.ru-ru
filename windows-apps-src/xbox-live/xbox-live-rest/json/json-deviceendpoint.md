---
title: DeviceEndpoint (JSON)
assetID: bd6c4af8-e491-8885-970e-e53d1d60642b
permalink: en-us/docs/xboxlive/rest/json-deviceendpoint.html
description: " DeviceEndpoint (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 0eaa21072ebf14b6f6d959ff40af34724a45522f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57618879"
---
# <a name="deviceendpoint-json"></a>DeviceEndpoint (JSON)
 
<a id="ID4EO"></a>

 
## <a name="deviceendpoint"></a>DeviceEndpoint
 
Объект DeviceEndpoint имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| deviceName| Строка| Необязательно. Понятное имя для устройства, если применимо. В настоящее время это значение не используется.| 
| значения endpointUri| Строка| Обязательный. URL-адрес, который клиентскую платформу (Windows или Windows Phone) получил от его служба Push-уведомлений (WNS или MPNS).| 
| языковой стандарт| Строка| Обязательный. Язык уведомлений, отправленных в эту конечную точку. Может быть список значений с разделителями запятыми в порядке предпочтения. Пример: «de-DE, en US, en».| 
| Платформы| Строка| Необязательно. В настоящее время поддерживаются значения: «WindowsPhone» и «Windows». Если не указан, он является производным от маркер устройства.| 
| platformVersion| Строка| Необязательно. Формат этой строки для каждой платформы. В настоящее время это значение не используется.| 
| systemId| Код GUID| Обязательный. Уникальный идентификатор для экземпляра приложения «» (сочетание устройства или пользователя). Лучшим типом реализации является для приложения создать случайный идентификатор GUID, после установки и первого запуска и продолжать использовать это значение при следующих запусках приложения.| 
| titleId| 32-разрядного целого числа без знака| Обязательный. Идентификатор заголовок игры, выполнив вызов к службе.| 
  
<a id="ID4EGD"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
  "systemId": "e9af05b4-70de-4920-880f-026c6fb67d1b",
  "userId" : 1234567890
  "endpointUri": "https://cloud.notify.windows.com/.../",
  "platform": "Windows",
  "platformVersion": "1.0",
  "deviceName": "Test Endpoint",
  "locale": "en-US",
  "titleId": 1297290219
}
    
```

  
<a id="ID4EPD"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ERD"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4E4D"></a>

 
##### <a name="reference"></a>Справочные материалы   