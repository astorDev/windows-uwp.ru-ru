---
description: Используйте этот метод в API аналитики для Microsoft Store для получения аналитических данных для вашего приложения.
title: Получение аналитических данных
ms.date: 07/31/2018
ms.topic: article
keywords: Windows 10, uwp, службы магазина, API, аналитический аналитики Microsoft Store
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 1847f22f52eb066115b5681e745e74ec74f77f7d
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8887664"
---
# <a name="get-insights-data"></a>Получение аналитических данных

Используйте этот метод в API аналитики Microsoft Store для получения аналитических данных, связанных с приобретения, работоспособности и использования метрик для приложения, заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [аналитический отчет](../publish/insights-report.md) в центре партнеров.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/insights``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | [Код продукта в магазине](in-app-purchases-and-trials.md#store-ids) для приложения, для которого требуется получить аналитических данных. Если этот параметр не задан, тело ответа будет содержать аналитических данных для всех приложений, зарегистрированных в вашей учетной записи.  |  Нет  |
| startDate | date | Начальная дата диапазона дат аналитических данных для извлечения. По умолчанию используется текущая дата минус 30 дней. |  Нет  |
| endDate | date | Конечная дата диапазона дат аналитических данных для извлечения. По умолчанию используется текущая дата. |  Нет  |
| filter | string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Например *filter = dataType eq «приобретения»*. <p/><p/>Можно указать следующие поля фильтра:<p/><ul><li><strong>приобретение</strong></li><li><strong>работоспособности</strong></li><li><strong>Использование</strong></li></ul> | Нет   |

### <a name="request-example"></a>Пример запроса

В следующем примере показано запроса на получение аналитических данных. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/insights?applicationId=9NBLGGGZ5QDR&startDate=6/1/2018&endDate=6/15/2018&filter=dataType eq 'acquisition' or dataType eq 'health' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | array  | Массив объектов, содержащих аналитических данных для приложения. Дополнительные сведения о данных в каждом объекте см. ниже раздел " [Аналитические значения](#insight-values) ".                                                                                                                      |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.                 |


### <a name="insight-values"></a>Понимание значения

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| applicationId       | string | Код продукта в магазине для приложения, по которому запрашиваются аналитических данных.     |
| insightDate                | string | Дата, на котором мы определили изменение определенного метрики. Эту дату представляет собой конце недели, в котором обнаружено значительное увеличение или уменьшение метрики по сравнению с кружка. |
| Тип данных     | string | Одна из следующих строк, указывающая общая аналитика область, которая описывает этого исследования:<p/><ul><li><strong>приобретение</strong></li><li><strong>работоспособности</strong></li><li><strong>Использование</strong></li></ul>   |
| insightDetail          | array | Одно или несколько [InsightDetail значения](#insightdetail-values) , представляющие сведения о текущем аналитические сведения.    |


### <a name="insightdetail-values"></a>Значения InsightDetail

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| FactName           | string | Одно из следующих значений, указывающих метрики, описывающий текущее представление или текущего измерения, на основе **типа данных** значения.<ul><li>Для **работоспособности**это значение всегда равно **числа попаданий**.</li><li>Для **приобретения**это значение всегда равно **AcquisitionQuantity**.</li><li>Для **использования**это значение может быть одна из следующих строк:<ul><li><strong>DailyActiveUsers</strong></li><li><strong>EngagementDurationMinutes</strong></li><li><strong>DailyActiveDevices</strong></li><li><strong>DailyNewUsers</strong></li><li><strong>DailySessionCount</strong></li></ul></ul>  |
| SubDimensions         | array |  Один или несколько объектов, которые описывают один метрики за аналитические сведения.   |
| PercentChange            | string |  Процент измененного метрики для своей базы консультантов всем клиентам.  |
| Имя измерения           | string |  Имя метрики, описанные в текущего измерения. Примеры включают **EventType**, **рынка**, **DeviceType**, **PackageVersion**, **AcquisitionType**, **AgeGroup** и **Пол**.   |
| DimensionValue              | string | Значение показателя, описанное в текущее измерение. Например если **имя измерения** **EventType**, **DimensionValue** может быть **Сбой** или **зависание**.   |
| FactValue     | string | Абсолютное значение метрики на выбранную дату аналитические была обнаружена.  |
| Direction | string |  Направление изменение (**положительное** или **отрицательное**).   |
| Дата              | строка |  Дата, на котором мы определили изменения, связанные с текущей аналитические или текущего измерения.   |

### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "applicationId": "9NBLGGGZ5QDR",
      "insightDate": "2018-06-03T00:00:00",
      "dataType": "health",
      "insightDetail": [
        {
          "FactName": "HitCount",
          "SubDimensions": [
            {
              "FactName:": "HitCount",
              "PercentChange": "21",
              "DimensionValue:": "DE",
              "FactValue": "109",
              "Direction": "Positive",
              "Date": "6/3/2018 12:00:00 AM",
              "DimensionName": "Market"
            }
          ],
          "DimensionValue": "crash",
          "Date": "6/3/2018 12:00:00 AM",
          "DimensionName": "EventType"
        },
        {
          "FactName": "HitCount",
          "SubDimensions": [
            {
              "FactName:": "HitCount",
              "PercentChange": "71",
              "DimensionValue:": "JP",
              "FactValue": "112",
              "Direction": "Positive",
              "Date": "6/3/2018 12:00:00 AM",
              "DimensionName": "Market"
            }
          ],
          "DimensionValue": "hang",
          "Date": "6/3/2018 12:00:00 AM",
          "DimensionName": "EventType"
        },
      ],
      "insightId": "9CY0F3VBT1AS942AFQaeyO0k2zUKfyOhrOHc0036Iwc="
    }
  ],
  "@nextLink": null,
  "TotalCount": 2
}
```

## <a name="related-topics"></a>Статьи по теме

* [Аналитический отчет](../publish/insights-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
