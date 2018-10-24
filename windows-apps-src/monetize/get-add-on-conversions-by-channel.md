---
author: Xansky
description: Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных о конверсиях по каждому каналу для надстройки в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение конверсий надстройки по каналу
ms.author: mhopkins
ms.date: 08/04/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, службы Магазина, API аналитики для Microsoft Store, конверсии надстройки, канал
ms.localizationpriority: medium
ms.openlocfilehash: af29c790df5508a22c545cdc5a2ca2faac15e134
ms.sourcegitcommit: 4b97117d3aff38db89d560502a3c372f12bb6ed5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "5431003"
---
# <a name="get-add-on-conversions-by-channel"></a>Получение конверсий надстройки по каналу

Используйте этот метод в API аналитики для Microsoft Store для получения сводных сведений о конверсиях по каждому каналу для надстройки в заданном диапазоне дат или с учетом других дополнительных фильтров.

* *Конверсия* означает, что пользователь (выполнивший вход с помощью учетной записи Майкрософт) получил новую лицензию на вашу надстройку (независимо от того, берете ли вы за это деньги или предоставляете лицензию бесплатно).
* *Канал* — это способ, с использованием которого пользователь оказался на странице описания вашего приложения (например, через Магазин или [настраиваемую кампанию по продвижению приложения](../publish/create-a-custom-app-promotion-campaign.md)).

Эта информация также доступна в [отчете о приобретениях надстроек](../publish/add-on-acquisitions-report.md#add-on-page-views-and-conversions-by-campaign-id) на информационной панели Центра разработки для Windows.

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappchannelconversions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | Строка | [Код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для приложения, по которому требуется получить данные о конверсиях надстройки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8 |  Да  |
| inAppProductId | Строка | [Код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для надстройки, по которой требуется получить данные о конверсиях.  | Да  |
| startDate | Дата | Начальная дата диапазона дат, для которого требуется получить данные о конверсиях. Значение по умолчанию: 1/1/2016. |  Нет  |
| endDate | Дата | Конечная дата диапазона дат, для которого требуется получить данные о конверсиях. По умолчанию используется текущая дата. |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | Строка  | Одно или несколько выражений для фильтрации текста ответа. Каждое выражение может использовать операторы **eq** или **ne**; кроме того, операторы можно объединять с помощью **и** или **или**. Можно указать следующие строки в инструкциях фильтра.  Описания см. в разделе [значения конверсии](#conversion-values) в этой статье. <ul><li><strong>applicationName</strong></li><li><strong>appType</strong></li><li><strong>customCampaignId</strong></li><li><strong>referrerUriDomain</strong></li><li><strong>channelType</strong></li><li><strong>storeClient</strong></li><li><strong>deviceType</strong></li><li><strong>market</strong></li></ul><p>Вот пример параметра *filter*: <em>filter=deviceType eq 'PC'</em>.</p> | Нет   |
| aggregationLevel | string | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong> | Нет |
| orderby | Строка | Оператор, который определяет порядок полученных значений данных для каждой конверсии. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>, где параметр <em>field</em> может принимать одно из следующих строковых значений:<ul><li><strong>date</strong></li><li><strong>applicationName</strong></li><li><strong>inAppProductName</strong></li><li><strong>appType</strong></li><li><strong>customCampaignId</strong></li><li><strong>referrerUriDomain</strong></li><li><strong>channelType</strong></li><li><strong>storeClient</strong></li><li><strong>deviceType</strong></li><li><strong>market</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | string | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<p/><ul><li><strong>date</strong></li><li><strong>applicationName</strong></li><li><strong>inAppProductName</strong></li><li><strong>appType</strong></li><li><strong>customCampaignId</strong></li><li><strong>referrerUriDomain</strong></li><li><strong>channelType</strong></li><li><strong>storeClient</strong></li><li><strong>deviceType</strong></li><li><strong>market</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date</strong></li><li><strong>applicationId</strong></li><li><strong>inAppProductId</strong></li><li><strong>inAppProductName</strong></li><li><strong>conversionCount</strong></li><li><strong>clickCount</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>groupby=ageGroup,market&amp;aggregationLevel=week</em></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующем примере демонстрируются несколько запросов на получение информации о конверсиях приложения. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappchannelconversions?applicationId=9NBLGGGZ5QDR&startDate=1/1/2017&endDate=2/1/2017&top=10&skip=0  HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappchannelconversions?applicationId=9NBLGGGZ5QDR&startDate=1/1/2017&endDate=4/31/2017&skip=0&filter=market eq 'US'  HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | Массив  | Массив объектов, содержащий сводную информацию о конверсиях для этой надстройки. Дополнительные сведения о данных в каждом объекте см. далее в разделе [значения конверсии](#conversion-values).                     |
| @nextLink  | Строка | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о конверсиях. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса                                                                                                                                                                                                                             |

### <a name="conversion-values"></a>Значения конверсии

Объекты в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| date                | Строка | Первая дата в диапазоне дат, для которого требуется получить данные о конверсиях. Если в запросе указан один день, это значение равно дате, соответствующей тому дню. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| inAppProductId      | Строка  | [Код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для надстройки, по которой запрашиваются данные о конверсиях.     |
| inAppProductName    | Строка  | Отображаемое название надстройки, по которой запрашиваются данные о конверсиях.   |
| applicationId       | Строка | [Код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для приложения, по которому запрашиваются данные о конверсиях.     |
| applicationName     | Строка | Отображаемое название приложения, по которому запрашиваются данные о конверсиях.        |
| appType          | Строка |  Тип продукта, для которого запрашиваются данные о конверсиях. Для этого метода единственным поддерживаемым значением является **Add-On**.            |
| customCampaignId           | Строка |  Строка идентификатора для [пользовательской кампании по продвижению приложения](../publish/create-a-custom-app-promotion-campaign.md), связанной с этим приложением.   |
| referrerUriDomain           | Строка |  Указывает домен, на котором было активировано описание приложения с идентификатором настраиваемой кампании по продвижению приложения.   |
| channelType           | Строка |  Одно из следующих строковых значений, определяющих канал конверсии:<ul><li><strong>CustomCampaignId</strong></li><li><strong>Трафик Магазина</strong></li><li><strong>Другое</strong></li></ul>    |
| storeClient         | Строка | Версия Магазина, в котором произведена конверсия. На данный момент единственным поддерживаемым значением является **SFC**.    |
| deviceType          | строка | Одна из следующих строк:<ul><li><strong>Компьютер</strong></li><li><strong>Телефон</strong></li><li><strong>Console (консоль),</strong></li><li><strong>Интернет вещей</strong></li><li><strong>Holographic (голография),</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>            |
| market              | Строка | Код страны по стандарту ISO 3166 для рынка, на котором произошла конверсия.    |
| clickCount              | Число  |     Число пользователей, щелкнувших по ссылке на описание вашего приложения.      |           
| conversionCount            | Число  |   Количество конверсий пользователей.         |         


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2016-01-01",
      "inAppProductId": "9NBLGGH3LHKL",
      "inAppProductName": "Contoso Add-On",
      "applicationId": "9NBLGGGZ5QDR",
      "applicationName": "Contoso App",
      "appType": "Add-On",
      "customCampaignId": "",
      "referrerUriDomain": "Universal Client Store",
      "channelType": "Store Traffic",
      "storeClient": "SFC",
      "deviceType": "PC",
      "market": "CN",
      "clickCount": 1,
      "conversionCount": 0
    }
  ],
  "@nextLink": null,
  "TotalCount": 1
}
```

## <a name="related-topics"></a>Связанные статьи

* [Отчет о приобретении надстроек](../publish/add-on-acquisitions-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
