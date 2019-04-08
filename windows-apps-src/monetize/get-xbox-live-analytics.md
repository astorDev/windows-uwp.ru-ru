---
description: Используйте этот метод в API аналитики для Microsoft Store для получения данных аналитики Xbox Live.
title: Получение аналитических данных Xbox Live
ms.date: 06/04/2018
ms.topic: article
keywords: Windows 10, uwp, службы Store, API аналитики для Microsoft Store, аналитика Xbox Live
ms.localizationpriority: medium
ms.openlocfilehash: 74c898630641e8b0d53a181d1874c6df62baaa78
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57637089"
---
# <a name="get-xbox-live-analytics-data"></a>Получение аналитических данных Xbox Live

Используйте этот метод в API аналитики для Microsoft Store, чтобы получить общие аналитические данные за последние 30 дней для пользователей, игравших в вашу [игру с поддержкой Xbox Live](../xbox-live/index.md), в том числе об использовании периферийных устройств, типе подключения к Интернету, распределении игровых баллов, статистики по игре и данных по друзьям и подписчикам. Эта информация также доступна в [отчет по аналитике в Xbox](../publish/xbox-analytics-report.md) в центре партнеров.

> [!IMPORTANT]
> Этот метод поддерживает только игры для Xbox или игры, использующие службы Xbox Live. Эти игры, в том числе игры, опубликованные [партнерами Майкрософт](../xbox-live/developer-program-overview.md#microsoft-partners), и отправленные в рамках программы [ID@Xbox, должны пройти [процесс утверждения концепции](../gaming/concept-approval.md)](../xbox-live/developer-program-overview.md#id). В настоящее время этот метод не поддерживает игры, опубликованные в рамках программы [Xbox Live Creators Program](../xbox-live/get-started-with-creators/get-started-with-xbox-live-creators.md).

Дополнительные аналитические данные для игр с поддержкой Xbox Live доступны с помощью следующих методов:
* [Получение данных достижения Xbox Live](get-xbox-live-achievements-data.md)
* [Получение данных работоспособности Xbox Live](get-xbox-live-health-data.md)
* [Получение данных концентратора-игр Xbox Live](get-xbox-live-game-hub-data.md)
* [Получение данных клуба Xbox Live](get-xbox-live-club-data.md)
* [Получение данных в многопользовательской, Xbox Live](get-xbox-live-multiplayer-data.md)
* [Получение данных одновременное использование Xbox Live](get-xbox-live-concurrent-usage-data.md)

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/gameanalytics``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательно  
|---------------|--------|---------------|------|
| applicationId | Строка | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для игры, по которой требуется получить общие аналитические данные Xbox Live.  |  Да  |
| metricType | Строка | Строка, определяющая тип аналитических данных Xbox Live, которые необходимо получить. Для этого метода укажите значение **productvalues**.  |  Да  |


### <a name="request-example"></a>Пример запроса

Ниже приведен пример запроса на получение общих аналитических данных по пользователям, играющим в вашу игру с поддержкой Xbox Live. Замените значение *applicationId* кодом продукта в Store для вашей игры.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/gameanalytics?applicationId=9NBLGGGZ5QDR&metrictype=productvalues HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

Этот метод возвращает массив *Value*, содержащий следующие объекты.

| Объект      | Описание                  |
|-------------|---------------------------------------------------|
| ProductData   |   Содержит один объект [DeviceProperties](#deviceproperties) и один объект [UserProperties](#userproperties), содержащие аналитические данные по пользователям и устройствам для вашей игры за последние 30 дней.    |  
| XboxwideData   |  Содержит один объект [DeviceProperties](#deviceproperties) и один объект [UserProperties](#userproperties), содержащие усредненные аналитические данные по пользователям и устройствам для всех пользователей Xbox Live в виде процентов. Эти данные включаются для сравнения с данными по вашей игре.   |                                           


### <a name="deviceproperties"></a>DeviceProperties

Этот ресурс содержит данные об использовании устройств по вашей игре либо усредненные данные по использованию устройств для всех пользователей Xbox Live за последние 30 дней.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
|  applicationId               |    Строка     |  [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для игры, по которой получены аналитические данные.   |
|  connectionTypeDistribution               |    Массив     |   Содержит объекты, которые указывают, сколько пользователей используют проводное подключение к Интернету и сколько беспроводное подключение к Интернету на консоли Xbox. Каждый объект содержит два поля строковых значений. <ul><li>**conType**: Указывает тип соединения.</li><li>**deviceCount**: В **ProductData** объекта, это поле указывает количество клиентов свою игру, использующих тип подключения. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использующих этот тип подключения.</li></ul>   |     
|  deviceCount,               |   Строка      |  В объекте **ProductData** в этом поле указывается количество устройств пользователей, на которых в вашу игру играли за последние 30 дней. В объекте **XboxwideData** в этом поле всегда стоит значение 1, указывающее исходное значение 100% для данных по всем пользователям Xbox Live.   |     
|  eliteControllerPresentDeviceCount               |   Строка      |  В объекте **ProductData** в этом поле указывается количество пользователей вашей игры, использующих беспроводной геймпад Xbox Elite. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использующих беспроводной геймпад Xbox Elite.  |     
|  externalDrivePresentDeviceCount               |   Строка      |  В объекте **ProductData** в этом поле указывается количество пользователей вашей игры, использующих внешний жесткий диск на Xbox. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использующих внешний жесткий диск на Xbox.  |


### <a name="userproperties"></a>UserProperties

Этот ресурс содержит данные о пользователях по вашей игре либо усредненные данные для всех пользователей Xbox Live за последние 30 дней.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
|  applicationId               |    Строка     |   [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для игры, по которой получены аналитические данные.  |
|  userCount               |    Строка     |   В объекте **ProductData** в этом поле указывается количество пользователей, игравших в вашу игру в последние 30 дней. В объекте **XboxwideData** в этом поле всегда стоит значение 1, указывающее исходное значение 100% для данных по всем пользователям Xbox Live.   |     
|  dvrUsageCounts               |   Массив      |  Содержит объекты, которые указывают, сколько пользователей использовали DVR для игр для записи и просмотра игрового процесса. Каждый объект содержит два поля строковых значений. <ul><li>**dvrName**: Указывает DVR для игр функция, используемая. Возможные значения: **gameClipUploads**, **gameClipViews**, **screenshotUploads** и **screenshotViews**.</li><li>**userCount**: В **ProductData** объекта, это поле указывает количество клиентов свою игру, использующие указанный компонент DVR для игр. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использовавших указанную функцию DVR для игр.</li></ul>   |     
|  followerCountPercentiles               |   Массив      |  Содержит объекты, предоставляющие сведения о количестве подписчиков у пользователей. Каждый объект содержит два поля строковых значений. <ul><li>**Процент**: В настоящее время это значение всегда равно 50, что говорит об ее ведомый данные как значение медианы.</li><li>**Значение**: В **ProductData** объекта, это поле указывает число для медианы подписчиков клиентам свою игру. В объекте **XboxwideData** в этом поле указывается медианное количество подписчиков для всех пользователей Xbox Live.</li></ul>  |   
|  friendCountPercentiles               |   Массив      |  Содержит объекты, предоставляющие сведения о количестве друзей у пользователей. Каждый объект содержит два поля строковых значений. <ul><li>**Процент**: В настоящее время это значение всегда равно 50, указывающее на то, что данные друзей предоставляется как значение медианы.</li><li>**Значение**: В **ProductData** объекта, это поле указывает число для медианы друзей клиентам свою игру. В объекте **XboxwideData** в этом поле указывается медианное количество друзей для всех пользователей Xbox Live.</li></ul>  |     
|  gamerScoreRangeDistribution               |   Массив      |  Содержит объекты, предоставляющие сведения о распределении игровых баллов по пользователям. Каждый объект содержит два поля строковых значений. <ul><li>**scoreRange**: Диапазон игровые достижения, для которой следующее поле предоставляет данные об использовании. Например, **10K-25K**.</li><li>**userCount**: В **ProductData** объекта, это поле указывает количество клиентов свою игру с достижениями в указанном диапазоне для все они запускавшиеся игры. В объекте **XboxwideData** в этом поле указывается процент всех пользователей Xbox Live, имеющих число игровых баллов в указанном диапазоне для всех игр, в которые они играли.</li></ul>  |
|  titleGamerScoreRangeDistribution               |   Массив      |  Содержит объекты, предоставляющие сведения о распределении игровых баллов для вашей игры. Каждый объект содержит два поля строковых значений. <ul><li>**scoreRange**: Диапазон игровые достижения, для которой следующее поле предоставляет данные об использовании. Например, **100-200**.</li><li>**userCount**: В **ProductData** объекта, это поле указывает количество клиентов свою игру с достижениями в указанный диапазон для игры. В объекте **XboxwideData** в этом поле указывается процент всех пользователей Xbox Live, имеющих число игровых баллов в указанном диапазоне для вашей игры.</li></ul>   |
|  socialUsageCounts               |   Массив      |  Содержит объекты, предоставляющие сведения об использовании социальных возможностей по пользователям. Каждый объект содержит два поля строковых значений. <ul><li>**scName**: Тип использования социальных сетей. Например, **gameInvites** и **textMessages**.</li><li>**userCount**: В **ProductData** объекта, это поле указывает количество клиентов свою игру, принявших участие в указанный тип социальных сетей. В объекте **XboxwideData** в этом поле указывается процент всех пользователей Xbox Live, участвовавших в указанном типе социального взаимодействия.</li></ul>   |
|  streamingUsageCounts               |   Массив      |  Содержит объекты, предоставляющие сведения об использовании потоковой передачи по пользователям. Каждый объект содержит два поля строковых значений. <ul><li>**stName**: Тип платформа для потоковой передачи. Например, **youtubeUsage**, **twitchUsage** и **mixerUsage**.</li><li>**userCount**: В **ProductData** объекта, это поле указывает количество клиентов свою игру, в которых использовался указанный платформа потоковой передачи. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использовавших указанную платформу для потоковой передачи.</li></ul>  |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "ProductData": {
        "DeviceProperties": [
          {
            "applicationId": "9NBLGGGZ5QDR",
            "connectionTypeDistribution": [
              {
                "conType": "WIRED",
                "deviceCount": "43806"
              },
              {
                "conType": "WIRELESS",
                "deviceCount": "104035"
              }
            ],
            "deviceCount": "148063",
            "eliteControllerPresentDeviceCount": "10615",
            "externalDrivePresentDeviceCount": "46388"
          }
        ],
        "UserProperties": [
          {
            "applicationId": "9NBLGGGZ5QDR",
            "userCount": "142345",
            "dvrUsageCounts": [
              {
                "dvrName": "gameClipUploads",
                "userCount": "31264"
              },
              {
                "dvrName": "gameClipViews",
                "userCount": "52236"
              },
              {
                "dvrName": "screenshotUploads",
                "userCount": "27051"
              },
              {
                "dvrName": "screenshotViews",
                "userCount": "45640"
              }
            ],
            "followerCountPercentiles": [
              {
                "percentage": "50",
                "value": "11"
              }
            ],
            "friendCountPercentiles": [
              {
                "percentage": "50",
                "value": "11"
              }
            ],
            "gamerScoreRangeDistribution": [
              {
                "scoreRange": "10K-25K",
                "userCount": "30015"
              },
              {
                "scoreRange": "25K-50K",
                "userCount": "20495"
              },
              {
                "scoreRange": "3K-10K",
                "userCount": "32438"
              },
              {
                "scoreRange": "50K-100K",
                "userCount": "10608"
              },
              {
                "scoreRange": "<3K",
                "userCount": "45726"
              },
              {
                "scoreRange": ">100K",
                "userCount": "3063"
              }
            ],
            "titleGamerScoreRangeDistribution": [
              {
                "scoreRange": "400-600",
                "userCount": "133875"
              },
              {
                "scoreRange": "800-1000",
                "userCount": "45960"
              },
              {
                "scoreRange": "<100",
                "userCount": "269137"
              },
              {
                "scoreRange": "≥1K",
                "userCount": "11634"
              },
              {
                "scoreRange": "100-200",
                "userCount": "334471"
              },
              {
                "scoreRange": "600-800",
                "userCount": "123044"
              },
              {
                "scoreRange": "200-400",
                "userCount": "396725"
              }
            ],
            "socialUsageCounts": [
              {
                "scName": "gameInvites",
                "userCount": "82390"
              },
              {
                "scName": "textMessages",
                "userCount": "91880"
              },
              {
                "scName": "partySessionCount",
                "userCount": "68129"
              }
            ],
            "streamingUsageCounts": [
              {
                "stName": "youtubeUsage",
                "userCount": "74092"
              },
              {
                "stName": "twitchUsage",
                "userCount": "13401"
              }
              {
                "stName": "mixerUsage",
                "userCount": "22907"
              }
            ]
          }
        ]
      },
      "XboxwideData": {
        "DeviceProperties": [
          {
            "applicationId": "XBOXWIDE",
            "connectionTypeDistribution": [
              {
                "conType": "WIRED",
                "deviceCount": "0.213677732584786"
              },
              {
                "conType": "WIRELESS",
                "deviceCount": "0.786322267415214"
              }
            ],
            "deviceCount": "1",
            "eliteControllerPresentDeviceCount": "0.0476609278128012",
            "externalDrivePresentDeviceCount": "0.173747147416134"
          }
        ],
        "UserProperties": [
          {
            "applicationId": "XBOXWIDE",
            "userCount": "1",
            "dvrUsageCounts": [
              {
                "dvrName": "gameClipUploads",
                "userCount": "0.173210623993245"
              },
              {
                "dvrName": "gameClipViews",
                "userCount": "0.202104713778096"
              },
              {
                "dvrName": "screenshotUploads",
                "userCount": "0.136682414274251"
              },
              {
                "dvrName": "screenshotViews",
                "userCount": "0.158057895120314"
              }
            ],
            "followerCountPercentiles": [
              {
                "percentage": "50",
                "value": "5"
              }
            ],
            "friendCountPercentiles": [
              {
                "percentage": "50",
                "value": "5"
              }
            ],
            "gamerScoreRangeDistribution": [
              {
                "scoreRange": "10K-25K",
                "userCount": "0.134709282586519"
              },
              {
                "scoreRange": "25K-50K",
                "userCount": "0.0549468789343825"
              },
              {
                "scoreRange": "50K-100K",
                "userCount": "0.017301313342277"
              },
              {
                "scoreRange": "3K-10K",
                "userCount": "0.216512780268453"
              },
              {
                "scoreRange": "<3K",
                "userCount": "0.573515440094644"
              },
              {
                "scoreRange": ">100K",
                "userCount": "0.00301430477372488"
              }
            ],
            "titleGamerScoreRangeDistribution": [
              {
                "scoreRange": "100-200",
                "userCount": "0.178055695637076"
              },
              {
                "scoreRange": "200-400",
                "userCount": "0.173283639825241"
              },
              {
                "scoreRange": "400-600",
                "userCount": "0.0986865193958259"
              },
              {
                "scoreRange": "600-800",
                "userCount": "0.0506375775462092"
              },
              {
                "scoreRange": "800-1000",
                "userCount": "0.0232398822856435"
              },
              {
                "scoreRange": "<100",
                "userCount": "0.456443551582991"
              },
              {
                "scoreRange": "≥1K",
                "userCount": "0.0196531337270126"
              }
            ],
            "socialUsageCounts": [
              {
                "scName": "gameInvites",
                "userCount": "0.460375855738335"
              },
              {
                "scName": "textMessages",
                "userCount": "0.429256324070832"
              },
              {
                "scName": "partySessionCount",
                "userCount": "0.378446577751268"
              },
              {
                "scName": "gamehubViews",
                "userCount": "0.000197115778147329"
              }
            ],
            "streamingUsageCounts": [
              {
                "stName": "youtubeUsage",
                "userCount": "0.330320919178683"
              },
              {
                "stName": "twitchUsage",
                "userCount": "0.040666241835399"
              }
              {
                "stName": "mixerUsage",
                "userCount": "0.140193816053558"
              }
            ]
          }
        ]
      }
    }
  ],
  "@nextLink": null,
  "TotalCount": 4
}
```

## <a name="related-topics"></a>Статьи по теме

* [Доступ к данным аналитики с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение данных достижения Xbox Live](get-xbox-live-achievements-data.md)
* [Получение данных работоспособности Xbox Live](get-xbox-live-health-data.md)
* [Получение данных концентратора игр Xbox Live](get-xbox-live-game-hub-data.md)
* [Получение данных клуба Xbox Live](get-xbox-live-club-data.md)
* [Получение данных в многопользовательской, Xbox Live](get-xbox-live-multiplayer-data.md)
* [Получение данных одновременное использование Xbox Live](get-xbox-live-concurrent-usage-data.md)
