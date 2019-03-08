---
title: GET (/users/xuid({xuid})/scids/{scid}/stats?include=valuemetadata)
assetID: 890e3f93-4fdc-955f-d849-ba9579d5c1eb
permalink: en-us/docs/xboxlive/rest/uri-usersxuidscidsscidstatsgetvaluemetadata.html
description: " GET (/users/xuid({xuid})/scids/{scid}/stats?include=valuemetadata)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 55ad44d4c29a2d7a43c76c4df2a78e08462fa65f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57612719"
---
# <a name="get-usersxuidxuidscidsscidstatsincludevaluemetadata"></a>GET (/users/xuid({xuid})/scids/{scid}/stats?include=valuemetadata)
Получает список указанного статистику, включая метаданные, связанные с статистических значений, для пользователя в конфигурации указанной службы.
Доменом для таких URI является `userstats.xboxlive.com`.

  * ["Примечания"](#ID4EV)
  * [Параметры URI](#ID4EAB)
  * [Параметры строки запроса](#ID4ELB)
  * [Авторизации](#ID4EWC)
  * [Требуемые заголовки запросов](#ID4ERD)
  * [Необязательные заголовки запросов](#ID4EDF)
  * [Текст запроса](#ID4EHG)
  * [Коды состояния HTTP](#ID4ESG)
  * [Текст ответа](#ID4EJCAC)

<a id="ID4EV"></a>


## <a name="remarks"></a>Замечания

? Включают = valuemetadata параметр запроса позволяет ответ, включая все метаданные, связанные с пользовательскими значениями stat, такие как модель и цвет использовать для достижения времени на трассы автомобиля.

Добавляемое значение метаданных в ответ, вызов запроса необходимо также задать значение заголовка X-Xbl-контракт-Version 3.

<a id="ID4EAB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| xuid| Код GUID| Xbox пользователя идентификатор (XUID) пользователя, от лица которого для доступа к конфигурации службы.|
| scid| Код GUID| Идентификатор конфигурации службы, содержащего ресурс к которому выполняется доступ.|

<a id="ID4ELB"></a>


## <a name="query-string-parameters"></a>Параметры строки запроса

| Параметр| Тип| Описание|
| --- | --- | --- | --- | --- | --- |
| statNames| Строка| Разделенный запятыми список имен пользователей статистики. Например следующий URI уведомляет службу, от имени пользователя, указанного в URI запрашиваются четыре статистики. {:: nomakrdown}<br/><br/>`https://userstats.xboxlive.com/users/xuid({xuid})/scids/{scid}/stats/wins,kills,kdratio,headshots?include=valuemetadata`| 
| включить = valuemetadata| Строка| Указывает, что ответ включает в себя любое значение метаданных, связанных с stat используйте значения.|

<a id="ID4EWC"></a>


## <a name="authorization"></a>Authorization

Отсутствует логика авторизации для сценариев изоляции содержимого и управления доступом.

   * Статистика списки лидеров и пользователь может считывать из клиентами на любой платформе, при условии, что вызывающий объект передает допустимый токен XSTS с запросом. Операции записи ограничены клиентов, поддерживаемых платформой данных.
   * Разработчики Title можно пометить статистику как открытые или с ограничением XDP или центра партнеров. Откройте Статистика списки лидеров. Откройте статистики может осуществляться Smartglass, а также iOS, Android, Windows, Windows Phone и веб-приложений до тех пор, пока пользователь авторизован для "песочницы". Авторизация пользователей для песочницы осуществляется через XDP или центра партнеров.

Код для проверки выглядит следующим образом:


```cpp
If (!checkAccess(serviceConfigId, resource, CLAIM[userid, deviceid, titleid]))
{
        Reject request as Unauthorized
}

// else accept request.

```


<a id="ID4ERD"></a>


## <a name="required-request-headers"></a>Требуемые заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Пример значения: «XBL3.0 x =&lt;userhash >;&lt; токен >».|
| X-Xbl контракт Version| Строка| Указывает, какая версия API для использования. Это значение должно быть присвоено «3», чтобы включить значения метаданных в ответ.|

<a id="ID4EDF"></a>


## <a name="optional-request-headers"></a>Необязательные заголовки запросов

| Заголовок| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| X-RequestedServiceVersion|  | Имя или номер, службы, к которому следует направлять этот запрос сборки. Запрос только направляются в, служба после проверки допустимости заголовка, утверждения в маркере проверки подлинности и т. д. Значение по умолчанию: 1.|

<a id="ID4EHG"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4ESG"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP

Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).

| Код| Фраза причины| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 200| ОК| Сеанс был успешно извлечен.|
| 304| Не изменено| Ресурс не были изменены с момента последнего запроса.|
| 400| Неверный запрос| Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.|
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.|
| 403| Запрещено| Запрос не допускается для пользователя или службы.|
| 404| Не найден| Не удалось найти указанный ресурс.|
| 406| Неприемлемо| Версия ресурса не поддерживается.|
| 408| Время ожидания запроса| Версия ресурса не поддерживается; должна быть отклонена уровнем MVC.|

<a id="ID4EJCAC"></a>


## <a name="response-body"></a>Тело ответа

<a id="ID4EPCAC"></a>


### <a name="sample-response"></a>Пример ответа


```cpp
{
  "user": {
    "xuid": "123456789",
    "gamertag": "WarriorSaint",
    "stats": [
      {
        "statname": "Wins",
        "type": "Integer",
        "value": 40,
        "valuemetadata" : "{\"region\" : \"EU\", \"isRanked\" : true}"
      },
      {
        "statname": "Kills",
        "type": "Integer",
        "value": 700,
        "valuemetadata" : "{\"longestKillStreak" : 15, \"favoriteTarget\" : \"CrazyPigeon\"}"
      },
      {
        "statname": "KDRatio",
        "type": "Double",
        "value": 2.23,
        "valuemetadata" : "{\"totalKills\" : 700, \"totalDeaths\" : 314}"
      },
      {
        "statname": "Headshots",
        "type": "Integer",
        "value": 173,
        "valuemetadata" : ""
      }
    ],
  }
}

```


<a id="ID4EZCAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4E2CAC"></a>


##### <a name="parent"></a>Parent

[/scids/ xuid ({xuid}) / users / {scid} / stats](uri-usersxuidscidsscidstats.md)
