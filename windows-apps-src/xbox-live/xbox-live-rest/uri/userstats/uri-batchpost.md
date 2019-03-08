---
title: POST (/batch)
assetID: f5997c8e-82c7-0fba-9991-ce1116db4830
permalink: en-us/docs/xboxlive/rest/uri-batchpost.html
description: " POST (/batch)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: a854fc830c87afbf675a379599916bf3db919539
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57645839"
---
# <a name="post-batch"></a>POST (/batch)
Метод, который работает как метод GET для сложных пакетных запросов для нескольких проигрывателя статистики по несколько наименований POST. Доменом для таких URI является `userstats.xboxlive.com`.
 
<a id="ID4ET"></a>

 
## <a name="remarks"></a>Замечания
 
Разработчики Title можно пометить статистику как открытые или с ограничением XDP или центра партнеров. Откройте Статистика списки лидеров. Откройте статистики может осуществляться Smartglass, а также iOS, Android, Windows, Windows Phone и веб-приложений до тех пор, пока пользователь авторизован для "песочницы". Авторизация пользователей для песочницы осуществляется через XDP или центра партнеров.
  
  * ["Примечания"](#ID4ET)
  * ["Примечания"](#ID4EFB)
  * [Авторизации](#ID4EUB)
  * [Требуемые заголовки запросов](#ID4ETC)
  * [Необязательные заголовки запросов](#ID4E3D)
  * [Текст запроса](#ID4EAF)
  * [Коды состояния HTTP](#ID4EWF)
  * [Текст ответа](#ID4ENBAC)
 
<a id="ID4EFB"></a>

 
## <a name="remarks"></a>Замечания
 
Вызывающий объект предоставляет тела сообщения с помощью массива пользователей, конфигурация службы идентификаторов (SCIDs) и списка имен статистический показатель на SCIDs, для которого требуется извлечь статистику.
 
Вы может оказаться более полезной для просмотра простой статистический показатель одним [получить](uri-usersxuidscidsscidstatsget.md) метод перед прочтением этой более сложные странице пакетного режима.
  
<a id="ID4EUB"></a>

 
## <a name="authorization"></a>Authorization
 
Отсутствует логика авторизации для сценариев изоляции содержимого и управления доступом.
 
   * Статистика списки лидеров и пользователь может считывать из клиентами на любой платформе, при условии, что вызывающий объект передает допустимый токен XSTS с запросом. Операции записи, очевидно, что ограничены клиентов, поддерживаемых данным.
   * Разработчики Title можно пометить статистику как открытые или с ограничением XDP или центра партнеров. Откройте Статистика списки лидеров. Откройте статистики может осуществляться Smartglass, а также iOS, Android, Windows, Windows Phone и веб-приложений до тех пор, пока пользователь авторизован для "песочницы". Авторизация пользователей для песочницы осуществляется через XDP или центра партнеров.
  
Ниже приведен код для проверки:
 

```cpp
If (!checkAccess(serviceConfigId, resource, CLAIM[userid, deviceid, titleid]))
{
        Reject request as Unauthorized
}

// else accept request.
         
```

  
<a id="ID4ETC"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | 
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: «XBL3.0 x =&lt;userhash >;&lt; токен >».| 
  
<a id="ID4E3D"></a>

 
## <a name="optional-request-headers"></a>Необязательные заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | 
| X-RequestedServiceVersion|  | Имя или номер, службы, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркере проверки подлинности и т. д. Значение по умолчанию: 1.| 
  
<a id="ID4EAF"></a>

 
## <a name="request-body"></a>Тело запроса
 
<a id="ID4EIF"></a>

 
### <a name="sample-request"></a>Пример запроса
 
Следующий текст POST уведомляет службу, что четыре статистические данные были запрошены из двух разных SCIDs для двух разных пользователей.
 

```cpp
{    
"requestedusers": [
                1234567890123460,
                1234567890123234
            ],
            "requestedscids": [
                {
                    "scid": "c402ff50-3e76-11e2-a25f-0800200c1212",
                    "requestedstats": [
                        "Test4FirefightKills",
                        "Test4FirefightHeadshots"
                    ]
                },
                {
                    "scid": "c402ff50-3e76-11e2-a25f-0800200c0343",
                    "requestedstats": [
                        "OverallTestKills",
                        "TestHeadshots"
                    ]
                }
            ] 
}
      
```

   
<a id="ID4EWF"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 200| ОК| Сеанс был успешно извлечен.| 
| 304| Не изменено| Ресурс не были изменены с момента последнего запроса.| 
| 400| Неверный запрос| Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.| 
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.| 
| 403| Запрещено| Запрос не допускается для пользователя или службы.| 
| 404| Не найден| Не удалось найти указанный ресурс.| 
| 406| Неприемлемо| Версия ресурса не поддерживается.| 
| 408| Время ожидания запроса| Версия ресурса не поддерживается; должна быть отклонена уровнем MVC.| 
  
<a id="ID4ENBAC"></a>

 
## <a name="response-body"></a>Тело ответа
 
<a id="ID4EXBAC"></a>

 
### <a name="sample-response"></a>Пример ответа
 

```cpp
{    
   "users":[          
       {    
      "xuid": "123456789"
        "gamertag": "WarriorSaint",
        "scids":[
          {
             "scid":"c402ff50-3e76-11e2-a25f-0800200c1212",
             "stats":  [
          {
                 "statname":"Test4FirefightKills",
                 "type":"Integer",
                 "value":7
             },
          {
                 "statname":"Test4FirefightHeadshots",
                 "type":"Integer",
                 "value":4
             }]
                        },
          {
             "scid":"c402ff50-3e76-11e2-a25f-0800200c0343",
             "stats":  [
          {
                 "statname":"OverallTestKills",
                 "type":"Integer",
                 "value":3434
             },
          {
                 "statname":"TestHeadshots",
                 "type":"Integer",
                 "value":41
             }]
          }],
                   },
    {    
                   "gamertag":"TigerShark",
                   "xuid":1234567890123234
        "scids":[
          {
             "scid":"123456789",
             "stats":  [
          {
                 "statname":"Test4FirefightKills",
                 "type":"Integer",
                 "value":63
             },
          {
                 "statname":"Test4FirefightHeadshots",
                 "type":"Integer",
                 "value":12
             }]
                        },
          {
"scid":"987654321",
             "stats":  [
          {
                 "statname":"OverallTestKills",
                 "type":"Integer",
                 "value":375
             },
          {
                 "statname":"TestHeadshots",
                 "type":"Integer",
                 "value":34
             }]
          }],
                   }]
}
         
```

   
<a id="ID4EDCAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EFCAC"></a>

 
##### <a name="parent"></a>Parent 

[аргументов](uri-batch.md)

   