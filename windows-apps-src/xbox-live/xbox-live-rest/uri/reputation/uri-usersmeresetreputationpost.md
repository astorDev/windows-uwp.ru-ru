---
title: POST (/users/me/resetreputation)
assetID: 1a4fed45-f608-dac2-3384-2ee493112f7b
permalink: en-us/docs/xboxlive/rest/uri-usersmeresetreputationpost.html
description: " POST (/users/me/resetreputation)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: fc770673ac7e4034004f58032c7fa66cb28413e7
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57632689"
---
# <a name="post-usersmeresetreputation"></a>POST (/users/me/resetreputation)
Позволяет команде разработчиков применение оценки репутации текущего пользователя некоторые произвольные значения после равным (к примеру) захват учетной записи. Доменом для таких URI является `reputation.xboxlive.com`.
 
  * ["Примечания"](#ID4EV)
  * [Авторизации](#ID4E5)
  * [Требуемые заголовки запросов](#ID4ETB)
  * [Текст запроса](#ID4END)
  * [Коды состояния HTTP](#ID4EDE)
  * [Текст ответа](#ID4EFH)
 
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания
 
Этот метод также может называться с любыми другими партнерами для всех "песочницы", за исключением розничной торговли и пользователями в все "песочницы" за исключением розничная версия, для целей тестирования. Обратите внимание, что этот запрос задает пользователя «основные» оценки репутации его положительный результат оценки будут все обнуляется. Фактический репутации пользователя после внесения этого вызова будут этих базовых показателей, а также свои премии посредник, а также свои премии последователей будет соответствовать.
  
<a id="ID4E5"></a>

 
## <a name="authorization"></a>Authorization
 
От партнера: Для песочницы розничной торговли **PartnerClaim** от принудительного применения группы; для всех других "песочницы", **PartnerClaim**.
 
От пользователя: Для всех "песочницы", за исключением розничной торговли **XuidClaim** и **TitleClaim**.
  
<a id="ID4ETB"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
Из всех: **Тип содержимого: application/json**.
 
От партнера: **X-Xbl-контракт-Version** (текущая версия — 101), **X Xbl "песочницы"**.
 
От пользователя: **X-Xbl-контракт-Version** (текущая версия — 101).
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | 
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: «XBL3.0 x =&lt;userhash >;&lt; токен >».| 
| X-RequestedServiceVersion|  | Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д. Значение по умолчанию: 101.| 
  
<a id="ID4END"></a>

 
## <a name="request-body"></a>Тело запроса
 
<a id="ID4ETD"></a>

 
### <a name="sample-request"></a>Пример запроса
 
Текст запроса — это простой [ResetReputation (JSON)](../../json/json-resetreputation.md) документа.
 

```cpp
{
    "fairplayReputation": 75,
    "commsReputation": 75,
    "userContentReputation": 75
}
      
```

   
<a id="ID4EDE"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | 
| 200| ОК| ОК.| 
| 400| Неверный запрос| Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.| 
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.| 
| 404| Не найден| Не удалось найти указанный ресурс.| 
| 500| Внутренняя ошибка сервера| Сервер обнаружил непредвиденное условие, что помешало ему выполнить запрос.| 
| 503| Служба недоступна| Запрос был отрегулирован, выполните запрос еще раз после повторных попыток клиента значение в секундах (например 5 секунд позже).| 
  
<a id="ID4EFH"></a>

 
## <a name="response-body"></a>Тело ответа
 
При успешном выполнении текст ответа пуст. В случае сбоя [ServiceError (JSON)](../../json/json-serviceerror.md) возвращается документа.
 
<a id="ID4ERH"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{
    "code": 4000,
    "source": "ReputationFD",
    "description": "No targetXuid field was supplied in the request"
}
         
```

   
<a id="ID4E2H"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E4H"></a>

 
##### <a name="parent"></a>Parent 

[/Users/Me/resetreputation](uri-usersmeresetreputation.md)

   