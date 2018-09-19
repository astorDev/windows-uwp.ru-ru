---
author: mcleanbyron
description: Используйте этот метод в API аналитики для Microsoft Store для получения данных о для классического приложения.
title: Получение данных о для классического приложения
ms.author: mcleans
ms.date: 07/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, службы магазина, API, аналитический аналитики Microsoft Store
ms.localizationpriority: medium
ms.openlocfilehash: e7ca6eed40af37276b5b4c98ec7b1b709bdadfb9
ms.sourcegitcommit: 68fcac3288d5698a13dbcbd57f51b30592f24860
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "4052096"
---
# <a name="get-insights-data-for-your-desktop-application"></a>Получение данных о для классического приложения

Используйте этот метод в API аналитики для Microsoft Store для получения этих сведений, данные, связанные с показатели работоспособности для классического приложения, которое вы добавили в [программе классических приложений для Windows](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program). Эти данные также доступна в [отчете о работоспособности](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program#health-report) для классических приложений на информационной панели центра разработки для Windows.

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/insights``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | КОД продукта для классического приложения, для которого требуется получить данные этих сведений. Чтобы получить код продукта для классического приложения, откройте любой [отчет аналитики центра разработки для классических приложений](https://msdn.microsoft.com/library/windows/desktop/mt826504) (такой как **отчет о работоспособности**) и получите код продукта из URL-адреса. Если этот параметр не задан, тело ответа будет содержать данные сведения для всех приложений, зарегистрированных в вашей учетной записи.  |  Нет  |
| startDate | date | Начальная дата диапазона дат аналитический данных для извлечения. По умолчанию используется текущая дата минус 30 дней. |  Нет  |
| endDate | date | Конечная дата диапазона дат аналитический данных для извлечения. По умолчанию используется текущая дата. |  Нет  |
| filter | string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Например *filter = dataType eq «приобретения»*. <p/><p/>В настоящее время этот метод поддерживает только фильтр **работоспособности**.  | Нет   |

### <a name="request-example"></a>Пример запроса

В следующем примере показано запроса на получение данных этих сведений. Замените значение *applicationId* соответствующее значение для классического приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/insights?applicationId=10238467886765136388&startDate=6/1/2018&endDate=6/15/2018&filter=dataType eq 'health' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | array  | Массив объектов, содержащих данные о для приложения. Дополнительные сведения о данных в каждом объекте см. ниже раздел " [Аналитические значения](#insight-values) ".                                                                                                                      |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.                 |


### <a name="insight-values"></a>Значения Insight

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| applicationId       | string | КОД продукта для классического приложения, для которого требуется извлечь данные этих сведений.     |
| insightDate                | string | Дата, на котором мы определили изменение определенного метрики. Эта дата представляет конце недели, в котором обнаружено значительное повышение или уменьшение показатель, по сравнению с недели до этого. |
| Тип данных     | string | Строка, указывающая общая аналитика область, которая информирует этого исследования. В настоящее время этот метод поддерживает только **работоспособности**.    |
| insightDetail          | array | Одно или несколько [InsightDetail значения](#insightdetail-values) , представляющие сведения о текущем аналитические сведения.    |


### <a name="insightdetail-values"></a>Значения InsightDetail

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| FactName           | string | Строка, указывающая метрики, описывающий текущее представление или текущего измерения. В настоящее время этот метод поддерживает только значения **числа попаданий**.  |
| SubDimensions         | array |  Один или несколько объектов, описывающие один метрики за аналитические сведения.   |
| PercentChange            | string |  Процент измененного Метрика для своей базы консультантов всем клиентам.  |
| Имя измерения           | string |  Имя метрики, описанные в текущее измерение. Примеры включают **EventType**, **рынка**, **DeviceType**и **PackageVersion**.   |
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

* [Программа классических приложений для Windows](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program)
* [Отчет о работоспособности](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program#health-report)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
