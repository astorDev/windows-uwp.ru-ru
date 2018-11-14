---
author: Xansky
description: Используйте этот метод в API аналитики для Microsoft Store для получения данных о воронке приобретений приложения в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение данных воронки приобретений приложения
ms.author: mhopkins
ms.date: 08/04/2017
ms.topic: article
keywords: windows 10, uwp, службы Магазина, API аналитики для Microsoft Store, приобретение, воронка
ms.localizationpriority: medium
ms.openlocfilehash: 8aaceb9af3eed04039148bdd863ca3e473c7c245
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6657073"
---
# <a name="get-app-acquisition-funnel-data"></a>Получение данных воронки приобретений приложения

Используйте этот метод в API аналитики для Microsoft Store для получения данных о воронке приобретений приложения в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [отчете о приобретениях](../publish/acquisitions-report.md#acquisition-funnel) в центре партнеров.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/funnel``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | [Код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для приложения, по которому требуется получить данные о воронке приобретений. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8 |  Да  |
| startDate | дата | Начальная дата диапазона дат, для которого требуется получить данные о воронке приобретений. По умолчанию используется текущая дата. |  Нет  |
| endDate | дата | Конечная дата диапазона дат, для которого требуется получить данные о воронке приобретений. По умолчанию используется текущая дата. |  Нет  |
| filter | string  | Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields) | Нет   |

 
### <a name="filter-fields"></a>Поля фильтра

Параметр *filter* запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**; операторы можно комбинировать с помощью **and** или **or**.

Поддерживаются следующие поля фильтра. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки.

| Поля        |  Описание        |
|---------------|-----------------|
| campaignId | Строка идентификатора для [пользовательской кампании по продвижению приложения](../publish/create-a-custom-app-promotion-campaign.md), связанной с этим приобретением. |
| market | Строка, содержащая код страны по стандарту ISO 3166 для рынка, на котором произошла покупка |
| deviceType | Одна из следующих строк, указывающая тип устройства, на котором совершено приобретение:<ul><li><strong>ПК</strong></li><li><strong>Телефон</strong></li><li><strong>Console (консоль),</strong></li><li><strong>Интернет вещей</strong></li><li><strong>Holographic (голография),</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul> |
| ageGroup | Одна из следующих строк, указывающая возрастную группу пользователя, совершившего приобретение:<ul><li><strong>0–17</strong></li><li><strong>18–24</strong></li><li><strong>25–34</strong></li><li><strong>35–49</strong></li><li><strong>50 и более</strong></li><li><strong>Неизвестно</strong></li></ul> |
| gender | Одна из следующих строк, указывающая пол пользователя, совершившего приобретение:<ul><li><strong>M</strong></li><li><strong>F</strong></li><li><strong>Неизвестно</strong></li></ul> |


### <a name="request-example"></a>Пример запроса

В следующем примере демонстрируются несколько запросов на получение информации о воронке приобретений для приложения. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/funnel?applicationId=9NBLGGGZ5QDR&startDate=1/1/2017&endDate=2/1/2017  HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/funnel?applicationId=9NBLGGGZ5QDR&startDate=8/1/2016&endDate=8/31/2016&filter=market eq 'US' and gender eq 'm'  HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | массив  | Массив объектов, содержащий сводную информацию о воронке приобретений для данного приложения. Дополнительные сведения о данных в каждом объекте см. далее в разделе [значения воронки](#funnel-values).                  |
| TotalCount | Целое число    | Общее число объектов в массиве *Value*.        |


### <a name="funnel-values"></a>Значения воронки

Объекты в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| MetricType                | Строка | Одна из следующих строк, задающих [тип данных воронки](../publish/acquisitions-report.md#acquisition-funnel), включенный в этот объект:<ul><li><strong>PageView</strong></li><li><strong>Приобретение</strong></li><li><strong>Установка</strong></li><li><strong>Использование</strong></li></ul> |
| UserCount       | Строка | Количество пользователей, которые выполнили шаг воронки, определяемый значением *MetricType*.             |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "MetricType": "PageView",
      "UserCount": 100
    },
    {
      "MetricType": "Acquisition",
      "UserCount": 80
    },
    {
      "MetricType": "Install",
      "UserCount": 50
    },
    {
      "MetricType": "Usage",
      "UserCount": 10
    }
  ],
  "TotalCount": 4
}
```

## <a name="related-topics"></a>Связанные статьи

* [Отчет "Приобретения"](../publish/acquisitions-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение сведений о покупках приложения](get-app-acquisitions.md)
