---
author: mcleanbyron
description: Используйте этот метод в аналитике хранилища Майкрософт API для получения данных полезные сведения о для своего приложения.
title: Получение наблюдений
ms.author: mcleans
ms.date: 07/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, хранилище служб, аналитика хранилища Майкрософт API, полезные сведения о
ms.localizationpriority: medium
ms.openlocfilehash: 53fbd91437e5dc702f8672c6cbadeea32a8a96bf
ms.sourcegitcommit: 9c79fdab9039ff592edf7984732d300a14e81d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "2814586"
---
# <a name="get-insights-data"></a>Получение наблюдений

Использовать этот метод в аналитике хранилища Майкрософт API для получения данных полезные сведения о связанных с приобретения, работоспособности и показатели использования для приложения во время для указанного диапазона дат и другие дополнительные фильтры. Эти сведения также доступен в [отчете полезные сведения о](../publish/insights-report.md) панели мониторинга Центр разработчиков Windows.

## <a name="prerequisites"></a>Предварительные условия


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
| applicationId | string | [Идентификатор хранилища](in-app-purchases-and-trials.md#store-ids) приложения, для которого требуется извлечь наблюдений. Если этот параметр не указан, тело ответа будет содержать полезные сведения о данных для всех приложений, зарегистрированных в свою учетную запись.  |  Нет  |
| startDate | date | Дата начала в диапазон дат полезные сведения о данных для извлечения. По умолчанию используется текущая дата минус 30 дней. |  Нет  |
| endDate | date | Дата окончания в диапазон дат полезные сведения о данных для извлечения. По умолчанию используется текущая дата. |  Нет  |
| filter | string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Например *фильтра = тип данных eq «приобретения»*. <p/><p/>Можно указать следующие поля фильтра:<p/><ul><li><strong>приобретение</strong></li><li><strong>работоспособности</strong></li><li><strong>об использовании</strong></li></ul> | Нет   |

### <a name="request-example"></a>Пример запроса

В следующем примере демонстрируется запрос на получение наблюдений. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/insights?applicationId=9NBLGGGZ5QDR&startDate=6/1/2018&endDate=6/15/2018&filter=dataType eq 'acquisition' or dataType eq 'health' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | array  | Массив объектов, которые содержат полезные сведения о данных для приложения. Дополнительные сведения о данных в каждом объекте обратитесь к разделу [возможность получения значения](#insight-values) ниже.                                                                                                                      |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.                 |


### <a name="insight-values"></a>Возможность получения значения

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| applicationId       | string | Идентификатор хранилища приложения, для которого необходимо извлечь данные средствами.     |
| insightDate                | string | Дата, на котором мы определяемую переход на определенные показатели. Эта дата окончания недели, в котором обнаружено значительное увеличение или уменьшение в показатель, по сравнению с недели до этого. |
| Тип данных     | string | Один из следующих строк, определяющий область Общие аналитики с описанием этого исследования:<p/><ul><li><strong>приобретение</strong></li><li><strong>работоспособности</strong></li><li><strong>об использовании</strong></li></ul>   |
| insightDetail          | array | Одно или несколько [значений InsightDetail](#insightdetail-values) , представляющих сведений для текущее представление.    |


### <a name="insightdetail-values"></a>InsightDetail значения

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| FactName           | string | Одно из следующих значений, которые указывает метрику, описывающее текущее представление или текущего измерения, на основе **типа данных** значения.<ul><li>Для **работоспособности**это значение всегда равно **числа попаданий**.</li><li>Для **приобретения**это значение всегда равно **AcquisitionQuantity**.</li><li>Для **использования**это значение может иметь одно из следующих строк:<ul><li><strong>DailyActiveUsers</strong></li><li><strong>EngagementDurationMinutes</strong></li><li><strong>DailyActiveDevices</strong></li><li><strong>DailyNewUsers</strong></li><li><strong>DailySessionCount</strong></li></ul></ul>  |
| SubDimensions         | array |  Один или несколько объектов, которые описывают одного метрики для понимание назначения.   |
| PercentChange            | string |  Процент, который метрику изменен во всей клиенты.  |
| Имя измерения           | string |  Имя метрики описано в текущем измерения. Примеры включают **EventType**, **рынка**, **DeviceType**, **PackageVersion**, **AcquisitionType**, **AgeGroup** и **Пол**.   |
| DimensionValue              | string | Значение метрики, как описано в текущем измерения. Например если **имя измерения** **EventType**, **DimensionValue** может быть **Сбой** или **зависание**.   |
| FactValue     | string | Абсолютное значение метрики на дату, на которую был обнаружен понимание назначения.  |
| Direction | string |  Направление изменение (**положительное** или **отрицательное**).   |
| Дата              | строка |  Дата, на котором мы определяемую изменения, связанные с текущей insight или текущего измерения.   |

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

* [Полезные сведения о отчета](../publish/insights-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
