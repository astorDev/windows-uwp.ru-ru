---
title: DELETE (/users/xuid({xuid})/devices/current/titles/current)
assetID: 3bf75247-0a2a-0e4c-afcc-9e7654a89648
permalink: en-us/docs/xboxlive/rest/uri-usersxuiddevicescurrenttitlescurrentdelete.html
description: " DELETE (/users/xuid({xuid})/devices/current/titles/current)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: dd916fee5455276d45e4437e4ee90cacbde30bf9
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57604219"
---
# <a name="delete-usersxuidxuiddevicescurrenttitlescurrent"></a>DELETE (/users/xuid({xuid})/devices/current/titles/current)
Удалить наличие закрывающей title, а не ждать [PresenceRecord](../../json/json-presencerecord.md) истекает. Доменом для таких URI является `userpresence.xboxlive.com`.
 
  * [Параметры URI](#ID4EZ)
  * [Авторизации](#ID4EEB)
  * [Требуемые заголовки запросов](#ID4ERD)
  * [Необязательные заголовки запросов](#ID4EVF)
  * [Текст запроса](#ID4EVG)
  * [Текст ответа](#ID4EAH)
 
<a id="ID4EZ"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| 64-разрядного целого числа без знака| Xbox пользователя идентификатор (XUID) целевого пользователя.| 
  
<a id="ID4EEB"></a>

 
## <a name="authorization"></a>Authorization
 
| Тип| Обязательно| Описание| Ответ, если он отсутствует| 
| --- | --- | --- | --- | --- | --- | --- | 
| XUID| Да| Идентификатор пользователя Xbox (XUID) вызывающего объекта| «403 запрещено»| 
| titleId| Да| titleId заголовка| «403 запрещено»| 
| Идентификатор устройства| Да для всех, кроме Windows и веб-| deviceId вызывающей стороны| «403 запрещено»| 
| deviceType| Да для всех, кроме Web| типа устройства, вызывающего объекта| «403 запрещено»| 
  
<a id="ID4ERD"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: «XBL3.0 x =&lt;userhash >;&lt; токен >».| 
| x-xbl контракт version| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д. Примеры значений 3 vnext.| 
| Content-Type| Строка| Тип mime тела запроса пример значения: application/json.| 
| Content-Length| Строка| Длина текста запроса. Пример значения: 312.| 
| Hyper-V| Строка| Доменное имя сервера. Пример значения: presencebeta.xboxlive.com.| 
  
<a id="ID4EVF"></a>

 
## <a name="optional-request-headers"></a>Необязательные заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| X-RequestedServiceVersion|  | Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д. Значение по умолчанию: 1.| 
  
<a id="ID4EVG"></a>

 
## <a name="request-body"></a>Тело запроса
 
Объекты не будут отправлены в тексте этого запроса.
  
<a id="ID4EAH"></a>

 
## <a name="response-body"></a>Тело ответа
 
В случае успеха без текста ответа возвращается код состояния HTTP.
 
В случае ошибки (HTTP 4xx или 5xx) сведения об ошибке возвращается в тексте ответа.
  
<a id="ID4ELH"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ENH"></a>

 
##### <a name="parent"></a>Parent 

[/users/xuid({xuid})/devices/current/titles/current](uri-usersxuiddevicescurrenttitlescurrent.md)

   