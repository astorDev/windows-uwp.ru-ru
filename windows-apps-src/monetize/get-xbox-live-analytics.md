---
author: mcleanbyron
description: Используйте этот метод в API аналитики для Microsoft Store для получения данных аналитики Xbox Live.
title: Получение аналитических данных Xbox Live
ms.author: mcleans
ms.date: 04/16/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, службы Store, API аналитики для Microsoft Store, аналитика Xbox Live
ms.localizationpriority: medium
ms.openlocfilehash: 82c24ee285070d733f3310b4ccec210adeafc27f
ms.sourcegitcommit: 91511d2d1dc8ab74b566aaeab3ef2139e7ed4945
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2018
ms.locfileid: "1817272"
---
# <a name="get-xbox-live-analytics-data"></a>Получение аналитических данных Xbox Live

Используйте этот метод в API аналитики для Microsoft Store, чтобы получить общие аналитические данные за последние 30 дней для пользователей, игравших в вашу [игру с поддержкой Xbox Live](../xbox-live/index.md), в том числе об использовании периферийных устройств, типе подключения к Интернету, распределении игровых баллов, статистики по игре и данных по друзьям и подписчикам. Эта информация также доступна в [отчете аналитики Xbox](../publish/xbox-analytics-report.md) на информационной панели Центра разработки для Windows.

> [!IMPORTANT]
> Сейчас этот метод работает только для игр с поддержкой для Xbox Live, опубликованных [партнерами Майкрософт](../xbox-live/developer-program-overview.md#microsoft-partners) или отправленных по этой [ID@Xbox программе](../xbox-live/developer-program-overview.md#id). Он не возвращает данные для игр, которые были отправлены с использованием [Xbox Live Creators Program](../xbox-live/developer-program-overview.md#xbox-live-creators-program).

Дополнительные аналитические данные для игр с поддержкой Xbox Live доступны с помощью следующих методов:
* [Получение данных о достижениях в Xbox Live](get-xbox-live-achievements-data.md)
* [Получение данных работоспособности в Xbox Live](get-xbox-live-health-data.md)
* [Получение данных центра игр в Xbox Live](get-xbox-live-game-hub-data.md)
* [Получение данных клуба в Xbox Live](get-xbox-live-club-data.md)
* [Получение многопользовательских данных в Xbox Live](get-xbox-live-multiplayer-data.md)
* [Получение данных параллельного использования Xbox Live](get-xbox-live-concurrent-usage-data.md)

## <a name="prerequisites"></a>Что вам понадобится

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите токен доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/gameanalytics``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Авторизация | Строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | строка | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для игры, по которой требуется получить общие аналитические данные Xbox Live.  |  Да  |
| metricType | строка | Строка, определяющая тип аналитических данных Xbox Live, которые необходимо получить. Для этого метода укажите значение **productvalues**.  |  Да  |


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
|  applicationId               |    строка     |  [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для игры, по которой получены аналитические данные.   |
|  connectionTypeDistribution               |    массив     |   Содержит объекты, которые указывают, сколько пользователей используют проводное подключение к Интернету и сколько беспроводное подключение к Интернету на консоли Xbox. Каждый объект содержит два поля строковых значений. <ul><li>**conType**: указывает тип подключения.</li><li>**deviceCount**: в объекте **ProductData** в этом поле указывается количество пользователей вашей игры, использующих этот тип подключения. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использующих этот тип подключения.</li></ul>   |     
|  deviceCount               |   строка      |  В объекте **ProductData** в этом поле указывается количество устройств пользователей, на которых в вашу игру играли за последние 30 дней. В объекте **XboxwideData** в этом поле всегда стоит значение 1, указывающее исходное значение 100% для данных по всем пользователям Xbox Live.   |     
|  eliteControllerPresentDeviceCount               |   строка      |  В объекте **ProductData** в этом поле указывается количество пользователей вашей игры, использующих беспроводной геймпад Xbox Elite. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использующих беспроводной геймпад Xbox Elite.  |     
|  externalDrivePresentDeviceCount               |   строка      |  В объекте **ProductData** в этом поле указывается количество пользователей вашей игры, использующих внешний жесткий диск на Xbox. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использующих внешний жесткий диск на Xbox.  |


### <a name="userproperties"></a>UserProperties

Этот ресурс содержит данные о пользователях по вашей игре либо усредненные данные для всех пользователей Xbox Live за последние 30 дней.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
|  applicationId               |    строка     |   [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для игры, по которой получены аналитические данные.  |
|  userCount               |    строка     |   В объекте **ProductData** в этом поле указывается количество пользователей, игравших в вашу игру в последние 30 дней. В объекте **XboxwideData** в этом поле всегда стоит значение 1, указывающее исходное значение 100% для данных по всем пользователям Xbox Live.   |     
|  dvrUsageCounts               |   массив      |  Содержит объекты, которые указывают, сколько пользователей использовали DVR для игр для записи и просмотра игрового процесса. Каждый объект содержит два поля строковых значений. <ul><li>**dvrName**: указывает используемую функцию DVR для игр. Возможные значения: **gameClipUploads**, **gameClipViews**, **screenshotUploads** и **screenshotViews**.</li><li>**userCount**: в объекте **ProductData** в этом поле указывается количество пользователей вашей игры, использовавших указанную функцию DVR для игр. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использовавших указанную функцию DVR для игр.</li></ul>   |     
|  followerCountPercentiles               |   массив      |  Содержит объекты, предоставляющие сведения о количестве подписчиков у пользователей. Каждый объект содержит два поля строковых значений. <ul><li>**percentage**: в настоящее время это значение всегда равно 50, что указывает на выдачу данных по подписчикам в виде медианного значения.</li><li>**value**: в объекте **ProductData** в этом поле указывается медианное количество подписчиков для пользователей вашей игры. В объекте **XboxwideData** в этом поле указывается медианное количество подписчиков для всех пользователей Xbox Live.</li></ul>  |   
|  friendCountPercentiles               |   массив      |  Содержит объекты, предоставляющие сведения о количестве друзей у пользователей. Каждый объект содержит два поля строковых значений. <ul><li>**percentage**: в настоящее время это значение всегда равно 50, что указывает на выдачу данных по друзьям в виде медианного значения.</li><li>**value**: в объекте **ProductData** в этом поле указывается медианное количество друзей для пользователей вашей игры. В объекте **XboxwideData** в этом поле указывается медианное количество друзей для всех пользователей Xbox Live.</li></ul>  |     
|  gamerScoreRangeDistribution               |   массив      |  Содержит объекты, предоставляющие сведения о распределении игровых баллов по пользователям. Каждый объект содержит два поля строковых значений. <ul><li>**scoreRange**: диапазон игровых баллов, для которого в следующем поле предоставляются данные об использовании. Например, **10K-25K**.</li><li>**userCount**: в объекте **ProductData** в этом поле указывается количество пользователей вашей игры, имеющих число игровых баллов в указанном диапазоне для всех игр, в которые они играли. В объекте **XboxwideData** в этом поле указывается процент всех пользователей Xbox Live, имеющих число игровых баллов в указанном диапазоне для всех игр, в которые они играли.</li></ul>  |
|  titleGamerScoreRangeDistribution               |   массив      |  Содержит объекты, предоставляющие сведения о распределении игровых баллов для вашей игры. Каждый объект содержит два поля строковых значений. <ul><li>**scoreRange**: диапазон игровых баллов, для которого в следующем поле предоставляются данные об использовании. Например, **100-200**.</li><li>**userCount**: в объекте **ProductData** в этом поле указывается количество пользователей вашей игры, имеющих число игровых баллов в указанном диапазоне для вашей игры. В объекте **XboxwideData** в этом поле указывается процент всех пользователей Xbox Live, имеющих число игровых баллов в указанном диапазоне для вашей игры.</li></ul>   |
|  socialUsageCounts               |   массив      |  Содержит объекты, предоставляющие сведения об использовании социальных возможностей по пользователям. Каждый объект содержит два поля строковых значений. <ul><li>**scName**: тип использования социальных возможностей. Например, **gameInvites** и **textMessages**.</li><li>**userCount**: в объекте **ProductData** в этом поле указывается количество пользователей вашей игры, участвовавших в указанном типе социального взаимодействия. В объекте **XboxwideData** в этом поле указывается процент всех пользователей Xbox Live, участвовавших в указанном типе социального взаимодействия.</li></ul>   |
|  streamingUsageCounts               |   массив      |  Содержит объекты, предоставляющие сведения об использовании потоковой передачи по пользователям. Каждый объект содержит два поля строковых значений. <ul><li>**stName**: тип платформы потоковой передачи. Например, **youtubeUsage**, **twitchUsage** и **mixerUsage**.</li><li>**userCount**: в объекте **ProductData** в этом поле указывается количество пользователей вашей игры, использовавших указанную платформу для потоковой передачи. В объекте **XboxwideData** в этом поле указывается процент от всех пользователей Xbox Live, использовавших указанную платформу для потоковой передачи.</li></ul>  |


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

* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение данных о достижениях в Xbox Live](get-xbox-live-achievements-data.md)
* [Получение данных работоспособности в Xbox Live](get-xbox-live-health-data.md)
* [Получение данных центра игр в Xbox Live](get-xbox-live-game-hub-data.md)
* [Получение данных клуба в Xbox Live](get-xbox-live-club-data.md)
* [Получение многопользовательских данных в Xbox Live](get-xbox-live-multiplayer-data.md)
* [Получение данных параллельного использования Xbox Live](get-xbox-live-concurrent-usage-data.md)
