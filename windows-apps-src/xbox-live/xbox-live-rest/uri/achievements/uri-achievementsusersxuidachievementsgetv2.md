---
title: GET (/users/xuid({xuid})/achievements)
assetID: 381d49d1-7a4b-4a1e-1baf-cf674f7e0d54
permalink: en-us/docs/xboxlive/rest/uri-achievementsusersxuidachievementsgetv2.html
description: " GET (/users/xuid({xuid})/achievements)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 720170e88808db7ef95b88896fbca4f1cda4a091
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655269"
---
# <a name="get-usersxuidxuidachievements"></a>GET (/users/xuid({xuid})/achievements)
Получает список достижений, определенные в заголовке, были разблокирован пользователем или те, которые пользователь имеет в процессе выполнения. Доменом для таких URI является `achievements.xboxlive.com`.
 
  * [Параметры URI](#ID4EX)
  * [Параметры строки запроса](#ID4ECB)
  * [Авторизации](#ID4ENF)
  * [Требуемые заголовки запросов](#ID4ESG)
  * [Необязательные заголовки запросов](#ID4ESH)
  * [Текст запроса](#ID4EIBAC)
  * [Текст ответа](#ID4ETBAC)
 
<a id="ID4EX"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| 64-разрядного целого числа без знака| Xbox пользователя идентификатор (XUID) пользователя при одновременном которого (ресурс). Должно соответствовать XUID пользователя, прошедшего проверку подлинности.| 
  
<a id="ID4ECB"></a>

 
## <a name="query-string-parameters"></a>Параметры строки запроса
 
| Параметр| Обязательно| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | 
| <b>skipItems</b>| Нет| 32-разрядное знаковое целое число| Возвращает элементы, начиная с версии после заданного числа элементов. Например <b>skipItems = «3»</b> будет извлекать элементы получены, начиная с четвертого элемента. | 
| <b>ContinuationToken</b>| Нет| Строка| Возвращает элементы, начиная с токен данного продолжения. | 
| <b>maxItems</b>| Нет| 32-разрядное знаковое целое число| Максимальное количество элементов, возвращаемых из коллекции, которые можно объединять с <b>skipItems</b> и <b>continuationToken</b> можно получить диапазон элементов. Служба может предоставлять значение по умолчанию, если <b>maxItems</b> отсутствует и может вернуть меньше, чем <b>maxItems</b>, даже если на последней странице результаты еще не были возвращены. | 
| <b>titleId</b>| Нет| Строка| Фильтр для возвращенных результатов. Принимает один или несколько идентификаторов с разделителями запятыми, decimal title.| 
| <b>unlockedOnly</b>| Нет| Логическое значение| Фильтр для возвращенных результатов. Если значение <b>true</b>, будет возвращать только достижения разблокирован для пользователя. По умолчанию используется <b>false</b>.| 
| <b>possibleOnly</b>| Нет| Логическое значение| Фильтр для возвращенных результатов. Если значение <b>true</b>, возвращает все возможные результаты, но не просто разблокировать метаданные — достижение, возвращенными XMS. По умолчанию используется <b>false</b>.| 
| <b>Типы</b>| Нет| Строка| Фильтр для возвращенных результатов. Может быть «Persistent» или «Запрос». Значение по умолчанию — все поддерживаемые типы.| 
| <b>OrderBy</b>| Нет| Строка| Указывает порядок, в котором возвращаются результаты. Может быть «Неупорядоченный», «Title», «UnlockTime» или «EndingSoon». Значение по умолчанию — «Неупорядоченных».| 
  
<a id="ID4ENF"></a>

 
## <a name="authorization"></a>Authorization
 
| Утверждение| Требуется?| Описание| Поведение, если отсутствует| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Пользователь| Вызывающий объект является авторизованный пользователь Xbox LIVE.| Вызывающий объект должен быть допустимым пользователем в Xbox LIVE.| «403 запрещено»| 
  
<a id="ID4ESG"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: «XBL3.0 x =&lt;userhash >;&lt; токен >».| 
  
<a id="ID4ESH"></a>

 
## <a name="optional-request-headers"></a>Необязательные заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| <b>X-RequestedServiceVersion</b>| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос будет перенаправлен только к этой службе после проверки допустимости заголовок, утверждения в маркер проверки подлинности и т. д. Значение по умолчанию: 1.| 
| <b>x-xbl-contract-version</b>| 32-разрядного целого числа без знака| При наличии и значение 2, на версию V2 этот API будет использоваться. В противном случае V1.| 
| <b>Примите языка</b>| Строка| Список нужного языковые стандарты и в случае ошибки (например, "fr-FR", "fr", "en-GB, en-WW, en US"). Достижения служба будет работать по списку, пока не найдет соответствующие локализованные строки. Если ничего не найдено, предпринимается попытка сопоставить расположение, определенное в токен пользователя, который поставляется вместе с IP-адреса пользователя. Если по-прежнему не соответствующие локализованные строки не найдены, в нем строки по умолчанию, предоставляемые title разработчика или издателя. | 
  
<a id="ID4EIBAC"></a>

 
## <a name="request-body"></a>Тело запроса
 
Объекты не будут отправлены в тексте этого запроса.
  
<a id="ID4ETBAC"></a>

 
## <a name="response-body"></a>Тело ответа
 
Если вызов прошел успешно, то служба возвращает массив [награду (JSON)](../../json/json-achievementv2.md) объектов и [PagingInfo (JSON)](../../json/json-paginginfo.md) объекта.
 
<a id="ID4ECCAC"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{
    "achievements":
    [{
        "id":"3",
        "serviceConfigId":"b5dd9daf-0000-0000-0000-000000000000",
        "name":"Default NameString for Microsoft Achievements Sample Achievement 3",
        "titleAssociations":
        [{
                "name":"Microsoft Achievements Sample",
                "id":3051199919,
                "version":"abc"
        }],
        "progressState":"Achieved",
        "progression":
        {
                "achievementState":"Achieved",
                "requirements":null,
                "timeUnlocked":"2013-01-17T03:19:00.3087016Z",
        },
        "mediaAssets":
        [{
                "name":"Icon Name",
                "type":"Icon",
                "url":"https://www.xbox.com"
        }],
        "platform":"D",
        "isSecret":true,
        "description":"Default DescriptionString for Microsoft Achievements Sample Achievement 3",
        "lockedDescription":"Default UnachievedString for Microsoft Achievements Sample Achievement 3",
        "productId":"12345678-1234-1234-1234-123456789012",
        "achievementType":"Challenge",
        "participationType":"Individual",
        "timeWindow":
        {
                "startDate":"2013-02-01T00:00:00Z",
                "endDate":"2100-07-01T00:00:00Z"
        },
        "rewards":
        [{
                "name":null,
                "description":null,
                "value":"10",
                "type":"Gamerscore",
                "valueType":"Int"
        },
        {
                "name":"Default Name for InAppReward for Microsoft Achievements Sample Achievement 3",
                "description":"Default Description for InAppReward for Microsoft Achievements Sample Achievement 3",
                "value":"1",
                "type":"InApp",
                "valueType":"String"
        }],
        "estimatedTime":"06:12:14",
        "deeplink":"aWFtYWRlZXBsaW5r",
        "isRevoked":false
        }],
        "pagingInfo":
        {
                "continuationToken":null,
                "totalRecords":1
        }
}
         
```

   
<a id="ID4EPCAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ERCAC"></a>

 
##### <a name="parent"></a>Parent 

[/Users/xuid({xuid})/achievements](uri-achievementsusersxuidachievementsv2.md)

  
<a id="ID4E2CAC"></a>

 
##### <a name="reference"></a>Справочные материалы 

[Достижение (JSON)](../../json/json-achievementv2.md)

 [PagingInfo (JSON)](../../json/json-paginginfo.md)

 [Параметры разбиения по страницам](../../additional/pagingparameters.md)

   