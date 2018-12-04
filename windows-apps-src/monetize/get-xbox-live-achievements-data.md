---
description: Используйте этот метод в API аналитики для Microsoft Store для получения данных достижений Xbox Live.
title: Получение данных о достижениях в Xbox Live
ms.date: 06/04/2018
ms.topic: article
keywords: Windows 10, uwp, службы Store, API аналитики для Microsoft Store, аналитика Xbox Live, достижения
ms.localizationpriority: medium
ms.openlocfilehash: 23a99c637dfd466ba21169626315803dec60e4e8
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8476096"
---
# <a name="get-xbox-live-achievements-data"></a>Получение данных о достижениях в Xbox Live

Используйте этот метод в API аналитики для Microsoft Store, чтобы определить число пользователей, которые разблокировали каждое достижение в вашей [игре с поддержкой Xbox Live](../xbox-live/index.md) за последний день, по которому доступны данные о достижениях, за предшествующие этому дню 30 дней за все время существования вашей игры до этого дня. Эта информация также доступна в [аналитический отчет Xbox](../publish/xbox-analytics-report.md) в центре партнеров.

> [!IMPORTANT]
> Этот метод поддерживает только игры для Xbox или игры, использующие службы Xbox Live. Эти игры, в том числе игры, опубликованные [партнерами Майкрософт](../xbox-live/developer-program-overview.md#microsoft-partners), и отправленные в рамках программы [ID@Xbox, должны пройти [процесс утверждения концепции](../gaming/concept-approval.md)](../xbox-live/developer-program-overview.md#id). В настоящее время этот метод не поддерживает игры, опубликованные в рамках программы [Xbox Live Creators Program](../xbox-live/get-started-with-creators/get-started-with-xbox-live-creators.md).

## <a name="prerequisites"></a>Что вам понадобится

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/gameanalytics``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса


| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | строка | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для игры, по которой требуется получить данные о достижениях Xbox Live.  |  Да  |
| metricType | строка | Строка, определяющая тип аналитических данных Xbox Live, которые необходимо получить. Для этого метода укажите значение **achievements**.  |  Да  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |


### <a name="request-example"></a>Пример запроса

Ниже приведен пример запроса на получение данных о достижениях по тем пользователям, которые разблокировали первые 10 достижений в игре с поддержкой Xbox Live. Замените значение *applicationId* кодом продукта в Store для вашей игры.


```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/gameanalytics?applicationId=9NBLGGGZ5QDR&metrictype=achievements&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | массив  | Массив объектов, содержащих данные для каждого достижения в игре. Дополнительные сведения о данных в каждом объекте см. в следующей таблице.                                                                                                                      |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 100, но для данного запроса имеется больше 100 строк данных. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.  |


Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| applicationId       | строка | Код продукта в Store для игры, по которому запрашиваются данные о достижениях.     |
| reportDateTime     | строка |  Дата для данных о достижениях.    |
| achievementId          | число |  Идентификатор достижения. |
| achievementName           | строка | Имя достижения.  |
| gamerscore           | число |  Награда в баллах за достижение.  |
| dailyUnlocks           | число |  Количество пользователей, разблокировавших достижение в день, заданный *reportDateTime*.  |
| monthlyUnlocks              | число |  Количество пользователей, разблокировавших достижение за дней дня до дня, заданного *reportDateTime*.   |
| totalUnlocks | число |  Количество пользователей, разблокировавших определенное достижение за все время существования игры до дня, заданного в *reportDateTime*.   |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "applicationId": "9NBLGGGZ5QDR",
      "reportDateTime": "2018-01-30T00:00:00",
      "achievementId": 6,
      "achievementName": "Yoink!",
      "gamerscore": 10,
      "dailyUnlocks": 0,
      "monthlyUnlocks": 10310,
      "totalUnlocks": 1215360
    },
    {
      "applicationId": "9NBLGGGZ5QDR",
      "reportDateTime": "2018-01-30T00:00:00",
      "achievementId": 7,
      "achievementName": "Ding!",
      "gamerscore": 10,
      "dailyUnlocks": 0,
      "monthlyUnlocks": 10897,
      "totalUnlocks": 1282524
    },
    {
      "applicationId": "9NBLGGGZ5QDR",
      "reportDateTime": "2018-01-30T00:00:00",
      "achievementId": 8,
      "achievementName": "End of the Beginning",
      "gamerscore": 30,
      "dailyUnlocks": 0,
      "monthlyUnlocks": 9848,
      "totalUnlocks": 1105074
    }
  ],
  "@nextLink": null,
  "TotalCount": 3
}
```

## <a name="related-topics"></a>Статьи по теме

* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение аналитических данных Xbox Live](get-xbox-live-analytics.md)
* [Получение данных работоспособности в Xbox Live](get-xbox-live-health-data.md)
* [Получение данных центра игр в Xbox Live](get-xbox-live-game-hub-data.md)
* [Получение данных клуба в Xbox Live](get-xbox-live-club-data.md)
* [Получение многопользовательских данных в Xbox Live](get-xbox-live-multiplayer-data.md)
