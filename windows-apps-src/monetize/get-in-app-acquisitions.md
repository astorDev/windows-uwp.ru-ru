---
author: Xansky
ms.assetid: 1599605B-4243-4081-8D14-40F6F7734E25
description: Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных о покупках надстройки в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение сведений о покупках надстройки
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, службы Store, API аналитики для Microsoft Store, покупки надстройки
ms.localizationpriority: medium
ms.openlocfilehash: a09bb4ded7325cd1c5f5091be133ded889322bd9
ms.sourcegitcommit: 9354909f9351b9635bee9bb2dc62db60d2d70107
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2018
ms.locfileid: "4684649"
---
# <a name="get-add-on-acquisitions"></a>Получение сведений о покупках надстройки

Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных в формате JSON по приобретениям надстроек для приложения в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [отчете о приобретениях надстроек](../publish/add-on-acquisitions-report.md) на информационной панели Центра разработки для Windows.

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                                |
|--------|----------------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание          |
|---------------|--------|--------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

Параметр *applicationId* или *inAppProductId* обязателен. Для извлечения данных о покупках всех надстроек, зарегистрированных в приложении, укажите параметр *applicationId*. Для извлечения данных о покупках конкретной надстройки укажите параметр *inAppProductId*. Если указаны оба параметра, параметр *applicationId* игнорируется.

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | строка | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для приложения, по которому требуется получить данные о покупках надстройки.  |  Да  |
| inAppProductId | строка | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для надстройки, по которой требуется получить данные о покупках.  | Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные о покупках надстройки. По умолчанию используется текущая дата. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные о покупках надстройки. По умолчанию используется текущая дата |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter |строка  | Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields) | Нет   |
| aggregationLevel | строка | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong> | Нет |
| orderby | string | Оператор, который определяет порядок полученных значений данных для каждой покупки надстройки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>, где параметр <em>field</em> может принимать одно из следующих строковых значений:<ul><li><strong>date,</strong></li><li><strong>acquisitionType,</strong></li><li><strong>ageGroup</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>orderName.</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | строка | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>date</strong></li><li><strong>applicationName</strong></li><li><strong>inAppProductName</strong></li><li><strong>acquisitionType</strong></li><li><strong>ageGroup</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>orderName</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date</strong></li><li><strong>applicationId</strong></li><li><strong>inAppProductId</strong></li><li><strong>acquisitionQuantity</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=ageGroup,market&amp;aggregationLevel=week</em></p> |  Нет  |


### <a name="filter-fields"></a>Поля фильтра

Параметр *filter* запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**; операторы можно комбинировать с помощью **and** или **or**. Несколько примеров параметров *filter*:

-   *filter=market eq 'US' and gender eq 'm';*
-   *filter=(market ne 'US') and (gender ne 'Unknown') and (gender ne 'm') and (market ne 'NO') and (ageGroup ne 'greater than 55' or ageGroup ne ‘less than 13’).*

Список поддерживаемых полей см. в следующей таблице. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки.

| Поля        |  Описание        |
|---------------|-----------------|
| acquisitionType | Одна из следующих строк:<ul><li><strong>free,</strong></li><li><strong>trial,</strong></li><li><strong>paid,</strong></li><li><strong>promotional code,</strong></li><li><strong>iap</strong></li></ul> |
| ageGroup | Одна из следующих строк:<ul><li><strong>less than 13,</strong></li><li><strong>13-17,</strong></li><li><strong>18-24,</strong></li><li><strong>25-34,</strong></li><li><strong>35-44,</strong></li><li><strong>44-55,</strong></li><li><strong>greater than 55,</strong></li><li><strong>Unknown</strong></li></ul> |
| storeClient | Одна из следующих строк:<ul><li><strong>Windows Phone Store (client);</strong></li><li><strong>Microsoft Store (client)</strong></li><li><strong>Microsoft Store (web)</strong></li><li><strong>Volume purchase by organizations;</strong></li><li><strong>Other</strong></li></ul> |
| gender | Одна из следующих строк:<ul><li><strong>m,</strong></li><li><strong>f,</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul> |
| market | Строка, содержащая код страны по стандарту ISO 3166 для рынка, на котором произошла покупка |
| osVersion | Одна из следующих строк:<ul><li><strong>Windows Phone 7.5;</strong></li><li><strong>Windows Phone 8;</strong></li><li><strong>Windows Phone8.1;</strong></li><li><strong>Windows Phone 10;</strong></li><li><strong>Windows 8;</strong></li><li><strong>Windows 8.1</strong></li><li><strong>Windows10</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul> |
| deviceType | Одна из следующих строк:<ul><li><strong>Компьютер</strong></li><li><strong>Телефон</strong></li><li><strong>Console (консоль),</strong></li><li><strong>Интернет вещей</strong></li><li><strong>Holographic (голография),</strong></li><li><strong>Неизвестно</strong></li></ul> |
| orderName | Строка, содержащая имя заказа для рекламного кода, который использовался для приобретения надстройки (применимо только в том случае, если пользователь приобрел надстройку, активировав рекламный код) |


### <a name="request-example"></a>Пример запроса

В следующих примерах демонстрируются несколько запросов на получение информации о покупках надстройки. Замените значения *inAppProductId* и *applicationId* на соответствующие коды продукта в Магазине для вашей надстройки или приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?inAppProductId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?inAppProductId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=7/3/2015&top=100&skip=0&filter=market ne 'US' and gender ne 'Unknown' and gender ne 'm' and market ne 'NO' and ageGroup ne '>55' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание         |
|------------|--------|------------------|
| Значение      | array  | Массив объектов, содержащий сводную информацию о покупках надстройки. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупке надстройки](#add-on-acquisition-values).                                                                                                              |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках надстроек. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.    |


<span id="add-on-acquisition-values" />

### <a name="add-on-acquisition-values"></a>Значения информации о покупке надстройки

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип    | Описание        |
|---------------------|---------|---------------------|
| date                | string  | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| inAppProductId      | string  | Код продукта в Магазине для надстройки, по которой запрашиваются данные о покупках.                                                                                                                                                                 |
| inAppProductName    | строка  | Отображаемое имя надстройки. Это значение отображается в данных ответа только в том случае, если для параметра *aggregationLevel* задано значение **day**. Исключение составляют случаи, когда указано значение для поля **inAppProductName** параметра *groupby*.                                                                                                                                                                                                            |
| applicationId       | строка  | Код продукта в Магазине для приложения, по которому требуется получить данные о покупках надстройки.                                                                                                                                                           |
| applicationName     | строка  | Отображаемое имя приложения.                                                                                                                                                                                                             |
| deviceType          | string  | Тип устройства, на котором совершена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                  |
| orderName           | string  | Имя заказа                                                                                                                                                                                                                   |
| storeClient         | string  | Версия Магазина, в котором произведена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                            |
| osVersion           | string  | Версия ОС, в которой произведена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                   |
| market              | string  | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка                                                                                                                                                                  |
| gender              | string  | Пол пользователя, совершившего покупку. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                    |
| ageGroup            | string  | Возрастная группа пользователя, совершившего покупку. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                 |
| acquisitionType     | string  | Тип приобретения (бесплатное, платное и т. д.). Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                    |
| acquisitionQuantity | integer | Количество покупок.                        |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2015-01-02",
      "inAppProductId": "9NBLGGH3LHKL",
      "inAppProductName": "Contoso add-on 7",
      "applicationId": "9NBLGGGZ5QDR",
      "applicationName": "Contoso Demo",
      "deviceType": "Phone",
      "orderName": "",
      "storeClient": "Windows Phone Store (client)",
      "osVersion": "Windows Phone 8.1",
      "market": "GB",
      "gender": "m",
      "ageGroup": "50orover",
      "acquisitionType": "iap",
      "acquisitionQuantity": 1
    }
  ],
  "@nextLink": "inappacquisitions?applicationId=9NBLGGGZ5QDR&inAppProductId=&aggregationLevel=day&startDate=2015/01/01&endDate=2016/02/01&top=1&skip=1",
  "TotalCount": 33677
}
```

## <a name="related-topics"></a>Связанные статьи

* [Отчет о приобретении надстроек](../publish/add-on-acquisitions-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение конверсий надстройки по каналу](get-add-on-conversions-by-channel.md)
* [Получение сведений о покупках приложения](get-app-acquisitions.md)
* [Получение данных воронки приобретений приложения](get-acquisition-funnel-data.md)
* [Получение конверсий приложения по каналу](get-app-conversions-by-channel.md)

 

 
