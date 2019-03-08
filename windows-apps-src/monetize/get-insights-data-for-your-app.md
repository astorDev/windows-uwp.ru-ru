---
description: Этот метод можно используйте в API анализа Microsoft Store для получения данных аналитики для вашего приложения.
title: Получение данных insights
ms.date: 07/31/2018
ms.topic: article
keywords: Windows 10, uwp, службы Store, API, insights анализа Microsoft Store
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 1847f22f52eb066115b5681e745e74ec74f77f7d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57662849"
---
# <a name="get-insights-data"></a>Получение данных insights

Использование этого метода в API анализа Microsoft Store для получения данных аналитики по для приобретения, работоспособности и метрики использования для приложения в течение указанного диапазона дат и другие необязательные фильтры. Эта информация также доступна в [отчет Insights](../publish/insights-report.md) в центре партнеров.

## <a name="prerequisites"></a>Предварительные условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/insights``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательно  
|---------------|--------|---------------|------|
| applicationId | Строка | [Store ID](in-app-purchases-and-trials.md#store-ids) приложения, для которого требуется извлечь данные аналитики. Если этот параметр не задан, текст ответа будет содержать аналитики по сценариям для всех приложений, зарегистрированных для учетной записи.  |  Нет  |
| startDate | date | Дата начала в диапазоне данных для извлечения. По умолчанию используется текущая дата минус 30 дней. |  Нет  |
| endDate | date | Дата окончания в диапазоне данных для извлечения. По умолчанию используется текущая дата. |  Нет  |
| filter | Строка  | Один или несколько операторов для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Например *фильтра = тип данных eq «приобретение»*. <p/><p/>Можно указать следующие поля фильтра:<p/><ul><li><strong>Приобретения</strong></li><li><strong>Работоспособности</strong></li><li><strong>Использование</strong></li></ul> | Нет   |

### <a name="request-example"></a>Пример запроса

Ниже приведен пример запроса для получения данных аналитики. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/insights?applicationId=9NBLGGGZ5QDR&startDate=6/1/2018&endDate=6/15/2018&filter=dataType eq 'acquisition' or dataType eq 'health' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | Массив  | Массив объектов, содержащих данные insights для приложения. Дополнительные сведения о данных в каждом объекте, см. в разделе [значения Insight](#insight-values) разделе ниже.                                                                                                                      |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.                 |


### <a name="insight-values"></a>Анализ значения

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| applicationId       | Строка | Store идентификатор приложения, для которого извлекаются данные аналитики.     |
| insightDate                | Строка | Дата, на котором мы определили, изменения в конкретной метрики. Эта дата представляет конец недели, в котором мы обнаружили существенное увеличение или уменьшение метрики по сравнению с на прошлой неделе. |
| Тип данных     | Строка | Один из следующих строк, которые задает общие analytics область, которая описывает данные:<p/><ul><li><strong>Приобретения</strong></li><li><strong>Работоспособности</strong></li><li><strong>Использование</strong></li></ul>   |
| insightDetail          | Массив | Один или несколько [InsightDetail значения](#insightdetail-values) , представляющие подробные сведения о текущем insight.    |


### <a name="insightdetail-values"></a>Значения InsightDetail

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| FactName           | Строка | Одно из следующих значений, указывающих метрики, описывающее текущее представление или текущего измерения, на основе **dataType** значение.<ul><li>Для **работоспособности**, это значение всегда равно **числа попаданий**.</li><li>Для **приобретения**, это значение всегда равно **количество приобретений**.</li><li>Для **использования**, это значение может принимать одно из следующих строк:<ul><li><strong>DailyActiveUsers</strong></li><li><strong>engagementDurationMinutes</strong></li><li><strong>DailyActiveDevices</strong></li><li><strong>dailyNewUsers</strong></li><li><strong>dailySessionCount</strong></li></ul></ul>  |
| SubDimensions         | Массив |  Один или несколько объектов, которые описывают одну метрику для анализа.   |
| PercentChange            | Строка |  Процент измененного метрики по всей клиентской базы.  |
| Имя измерения           | Строка |  Имя метрики, описанные в текущем измерении. К ним относятся **EventType**, **рынка**, **Тип_устройства**, **PackageVersion**, **AcquisitionType**, **Возрастным группам** и **Пол**.   |
| DimensionValue              | Строка | Значение метрики, описанного в текущем измерении. Например если **DimensionName** — **EventType**, **DimensionValue** может быть **аварийных** или **зависания** .   |
| FactValue     | Строка | Абсолютное значение метрики на выбранную дату была обнаружена аналитика.  |
| Направление | Строка |  Направление изменения (**положительное** или **отрицательное**).   |
| Дата              | Строка |  Дата, на котором мы определили изменений, связанных с текущей insight или текущего измерения.   |

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

* [Отчет Insights](../publish/insights-report.md)
* [Доступ к данным аналитики с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
