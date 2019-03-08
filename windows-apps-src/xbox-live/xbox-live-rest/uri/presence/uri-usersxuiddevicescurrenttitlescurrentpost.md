---
title: POST (/users/xuid({xuid})/devices/current/titles/current)
assetID: d5e2d12d-ba75-2d04-2805-c69a4c143f73
permalink: en-us/docs/xboxlive/rest/uri-usersxuiddevicescurrenttitlescurrentpost.html
description: " POST (/users/xuid({xuid})/devices/current/titles/current)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: b29a0bc796712d7b7c44a1fe8512f99bf09eb4fc
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57649529"
---
# <a name="post-usersxuidxuiddevicescurrenttitlescurrent"></a>POST (/users/xuid({xuid})/devices/current/titles/current)
Замените заголовок присутствие пользователя. Доменом для таких URI является `userpresence.xboxlive.com`.
 
  * ["Примечания"](#ID4EV)
  * [Параметры URI](#ID4EEB)
  * [Авторизации](#ID4EPB)
  * [Требуемые заголовки запросов](#ID4ENE)
  * [Необязательные заголовки запросов](#ID4ERG)
  * [Текст запроса](#ID4ERH)
  * [Текст ответа](#ID4EKAAC)
 
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания
 
Этот URI может использоваться все названия на платформах, отличных от консоли, добавлять и обновлять присутствия, Развитые функции контроля присутствия и данные о присутствии мультимедиа для названия.
 
**SandboxId** теперь извлекается из утверждения в XToken и принудительно. Если **SandboxId** не существует, то службы обнаружения развлечения (EDS) будет выдавать ошибку 400 — Неверный запрос.
  
<a id="ID4EEB"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| 64-разрядного целого числа без знака| Xbox пользователя идентификатор (XUID) целевого пользователя.| 
  
<a id="ID4EPB"></a>

 
## <a name="authorization"></a>Authorization
 
| Тип| Обязательно| Описание| Ответ, если он отсутствует| 
| --- | --- | --- | --- | --- | --- | --- | 
| XUID| Да| Идентификатор пользователя Xbox (XUID) вызывающего объекта| «403 запрещено»| 
| titleId| Да| titleId заголовка| «403 запрещено»| 
| Идентификатор устройства| Да для всех, кроме Windows и веб-| deviceId вызывающей стороны| «403 запрещено»| 
| deviceType| Да для всех, кроме Web| типа устройства, вызывающего объекта| «403 запрещено»| 
| sandboxId| Да для вызовов, поступающих от | "песочницы" вызывающего объекта| «403 запрещено»| 
  
<a id="ID4ENE"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: «XBL3.0 x =&lt;userhash >;&lt; токен >».| 
| x-xbl контракт version| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д. Примеры значений 3 vnext.| 
| Content-Type| Строка| Тип mime тела запроса пример значения: application/json.| 
| Content-Length| Строка| Длина текста запроса. Пример значения: 312.| 
| Hyper-V| Строка| Доменное имя сервера. Пример значения: presencebeta.xboxlive.com.| 
  
<a id="ID4ERG"></a>

 
## <a name="optional-request-headers"></a>Необязательные заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| X-RequestedServiceVersion|  | Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д. Значение по умолчанию: 1.| 
  
<a id="ID4ERH"></a>

 
## <a name="request-body"></a>Тело запроса
 
Объект запроса является [TitleRequest](../../json/json-titlerequest.md). Обновляются только те свойства, которые фактически присутствует в тексте. Какие-либо свойства не являются частью тела но наличие на сервере останутся без изменений.
 
<a id="ID4EAAAC"></a>

 
### <a name="sample-request"></a>Пример запроса
 

```cpp
{
  id:"12341234",
  placement:"snapped",
  state:"active"
}
      
```

   
<a id="ID4EKAAC"></a>

 
## <a name="response-body"></a>Тело ответа
 
В случае успеха код состояния HTTP 200 или 201 Создано не возвращается соответствующим образом.
 
В случае ошибки (HTTP 4xx или 5xx) сведения об ошибке возвращается в тексте ответа.
  
<a id="ID4EVAAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EXAAC"></a>

 
##### <a name="parent"></a>Parent 

[/users/xuid({xuid})/devices/current/titles/current](uri-usersxuiddevicescurrenttitlescurrent.md)

  
<a id="ID4EBBAC"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Идентификаторы URI Marketplace](../marketplace/atoc-reference-marketplace.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   