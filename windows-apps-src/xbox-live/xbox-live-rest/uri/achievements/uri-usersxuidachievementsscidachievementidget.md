---
title: GET (/users/xuid({xuid})/achievements/{scid}/{achievementid})
assetID: 27318886-f084-d6a8-e582-3eb070ccbc38
permalink: en-us/docs/xboxlive/rest/uri-usersxuidachievementsscidachievementidget.html
description: " GET (/users/xuid({xuid})/achievements/{scid}/{achievementid})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 19083937d48d8c312215f1734513d83c59f52f0d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57627509"
---
# <a name="get-usersxuidxuidachievementsscidachievementid"></a>GET (/users/xuid({xuid})/achievements/{scid}/{achievementid})
Получение сведений о достижения. Доменом для таких URI является `achievements.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
  * [Авторизации](#ID4EAB)
  * [Влияние параметров конфиденциальности для ресурса](#ID4E4C)
  * [Требуемые заголовки запросов](#ID4EPG)
  * [Необязательные заголовки запросов](#ID4EPH)
  * [Текст запроса](#ID4ECBAC)
  * [Коды состояния HTTP](#ID4ENBAC)
  * [Текст ответа](#ID4EBGAC)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| 64-разрядного целого числа без знака| Xbox пользователя идентификатор (XUID) пользователя, ресурсом которого осуществляется. Должно соответствовать XUID пользователя, прошедшего проверку подлинности.| 
| scid| Код GUID| Уникальный идентификатор конфигурации службы, достижение которого осуществляется доступ.| 
| achievementid| 32-разрядного целого числа без знака| (В пределах указанного SCID) идентификатор достижения, к которому выполняется обращение.| 
  
<a id="ID4EAB"></a>

 
## <a name="authorization"></a>Authorization
 
Утверждения авторизации, используемые | Утверждение| Требуется?| Описание| Поведение, если отсутствует| 
| --- | --- | --- | --- | --- | --- | --- | 
| Пользователь| Да| Является допустимым пользователем в Xbox LIVE, для которого был выполнен запрос.| «403 запрещено»| 
| Заголовок| Нет| Вызывающий заголовок.| Зависит от AuthZ. Начиная с 1 мая 2013 г. AuthZ не поддерживает утверждения, когда отсутствует и поэтому будет запрещать доступ к любой SCIDs, не помечен как общедоступный.| 
| Песочница| Нет| "Песочницы", из которого требуется извлечь результаты.| Зависит от AuthZ. Начиная с 1 мая 2013 г AuthZ не поддерживает утверждения по умолчанию, когда отсутствует.| 
  
<a id="ID4E4C"></a>

 
## <a name="effect-of-privacy-settings-on-resource"></a>Влияние параметров конфиденциальности для ресурса
 
Влияние параметров конфиденциальности для ресурса | Запрашивающий пользователь| Целевые параметры конфиденциальности| Поведение| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Мне| -| Как описано.| 
| Friend| Все| ОК| 
| Friend| только друзей| ОК| 
| Friend| заблокировано| Запрещено.| 
| пользователь не friend| Все| ОК| 
| пользователь не friend| только друзей| Запрещено.| 
| пользователь не friend| заблокировано| Запрещено.| 
| Сторонний сайт| Все| ОК| 
| Сторонний сайт| только друзей| Запрещено.| 
| Сторонний сайт| заблокировано| Запрещено.| 
  
<a id="ID4EPG"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: «XBL3.0 x =&lt;userhash >;&lt; токен >».| 
  
<a id="ID4EPH"></a>

 
## <a name="optional-request-headers"></a>Необязательные заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| X-RequestedServiceVersion| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркер проверки подлинности и т. д. Значение по умолчанию: 1.| 
| x-xbl контракт version| Строка| По умолчанию версии 1.| 
| Примите языка| Строка| Список нужного языковые стандарты и в случае ошибки (например, "fr-FR", "fr", "en-GB, en-WW, en US"). Достижения служба будет работать по списку, пока не найдет соответствующие локализованные строки. Если ничего не найдено, предпринимается попытка сопоставить расположение, определенное в токен пользователя, который поставляется вместе с IP-адреса пользователя. Если по-прежнему не соответствующие локализованные строки не найдены, в нем строки по умолчанию, предоставляемые title разработчика или издателя. | 
  
<a id="ID4ECBAC"></a>

 
## <a name="request-body"></a>Тело запроса
 
Объекты не будут отправлены в тексте этого запроса.
  
<a id="ID4ENBAC"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 200| ОК| Сеанс был успешно извлечен.| 
| 301| Перемещено навсегда| Служба была перемещена на другой URI.| 
| 307| Временное перенаправление| URI для этого ресурса временно изменилась.| 
| 400| Неверный запрос| Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.| 
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.| 
| 403| Запрещено| Запрос не допускается для пользователя или службы.| 
| 404| Не найден| Не удалось найти указанный ресурс.| 
| 406| Неприемлемо| Версия ресурса не поддерживается; должна быть отклонена уровнем MVC.| 
| 408| Время ожидания запроса| Запрос заняло слишком много времени.| 
| 410| Прошло| Запрошенный ресурс больше не доступен.| 
  
<a id="ID4EBGAC"></a>

 
## <a name="response-body"></a>Тело ответа
 
<a id="ID4EHGAC"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{
    "achievement":
    {
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
    }
}
         
```

   
<a id="ID4ERGAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ETGAC"></a>

 
##### <a name="parent"></a>Parent 

[/achievements/ xuid ({xuid}) / users / {scid} / {achievementid}](uri-usersxuidachievementsscidachievementid.md)

   