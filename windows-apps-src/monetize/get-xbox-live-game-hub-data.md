---
author: mcleanbyron
description: Используйте этот метод в API аналитики для Microsoft Store для получения центра игр Xbox Live.
title: Получение данных центра игр в Xbox Live
ms.author: mcleans
ms.date: 04/16/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, службы Store, API аналитики для Microsoft Store, аналитика Xbox Live, центры игр
ms.localizationpriority: medium
ms.openlocfilehash: 0d34c95e615a10131b3e7f3ffe9ceb246b652727
ms.sourcegitcommit: 91511d2d1dc8ab74b566aaeab3ef2139e7ed4945
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2018
ms.locfileid: "1815789"
---
# <a name="get-xbox-live-game-hub-data"></a>Получение данных центра игр в Xbox Live


Используйте этот метод в API аналитики для Microsoft Store для получения данных центра игр для своей [игры с поддержкой Xbox Live](../xbox-live/index.md). Эта информация также доступна в [отчете аналитики Xbox](../publish/xbox-analytics-report.md) на информационной панели Центра разработки для Windows.

> [!IMPORTANT]
> Сейчас этот метод работает только для игр с поддержкой для Xbox Live, опубликованных [партнерами Майкрософт](../xbox-live/developer-program-overview.md#microsoft-partners) или отправленных по этой [ID@Xbox программе](../xbox-live/developer-program-overview.md#id). Он не возвращает данные для игр, которые были отправлены с использованием [Xbox Live Creators Program](../xbox-live/developer-program-overview.md#xbox-live-creators-program).

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
| applicationId | строка | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для игры, по которой требуется получить данные центра игр Xbox Live.  |  Да  |
| metricType | строка | Строка, определяющая тип аналитических данных Xbox Live, которые необходимо получить. Для этого метода укажите значение **communitymanagergamehub**.  |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные центра игр. По умолчанию используется текущая дата минус 30 дней. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные центра игр. По умолчанию используется текущая дата. |  Нет  |
| top | целое число | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |


### <a name="request-example"></a>Пример запроса

Ниже приведен пример запроса на получение данных центра игр для вашей игры с поддержкой Xbox Live. Замените значение *applicationId* кодом продукта в Store для вашей игры.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/gameanalytics?applicationId=9NBLGGGZ5QDR&metrictype=communitymanagergamehub&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | массив  | Массив объектов, содержащих данные центра игр по каждой дате в заданном диапазоне дат. Дополнительные сведения о данных в каждом объекте см. в следующей таблице.                                                                                                                      |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10000, но для данного запроса имеется больше 10000 строк данных. |
| TotalCount | целое число    | Общее количество строк в результирующих данных для запроса.  |


Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| date                | строка | Дата для данных центра игр в этом объекте. |
| applicationId       | строка | Код продукта в Store для игры, по которой запрашиваются данные центра игр.     |
| gameHubLikeCount     | число |   Количество отметок "Нравится", добавленных на странице центра игр в указанную дату.   |
| gameHubCommentCount          | число |  Количество комментариев, добавленных на странице центра игр для вашего приложения в указанную дату.  |
| gameHubShareCount           | число | Количество раз, когда пользователи поделились с друзьями страницей в центре игр для вашего приложения в указанную дату.   |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2018-01-04",
      "applicationId": "9NBLGGGZ5QDR",
      "gameHubLikeCount": 10,
      "gameHubCommentCount": 1,
      "gameHubShareCount": 0
    },
    {
      "date": "2018-01-05",
      "applicationId": "9NBLGGGZ5QDR",
      "gameHubLikeCount": 12,
      "gameHubCommentCount": 1,
      "gameHubShareCount": 0
    }
  ],
  "@nextLink": null,
  "TotalCount": 26
}
```

## <a name="related-topics"></a>Статьи по теме

* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение аналитических данных Xbox Live](get-xbox-live-analytics.md)
* [Получение данных о достижениях в Xbox Live](get-xbox-live-achievements-data.md)
* [Получение данных работоспособности в Xbox Live](get-xbox-live-health-data.md)
* [Получение данных клуба в Xbox Live](get-xbox-live-club-data.md)
* [Получение многопользовательских данных в Xbox Live](get-xbox-live-multiplayer-data.md)
