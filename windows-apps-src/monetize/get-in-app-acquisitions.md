---
ms.assetid: 1599605B-4243-4081-8D14-40F6F7734E25
description: Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных о покупках надстройки в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение сведений о покупках надстройки
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, службы Store, API аналитики для Microsoft Store, покупки надстройки
ms.localizationpriority: medium
ms.openlocfilehash: cd7e907994943dbce83d195e80a15770833f7e4b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57650989"
---
# <a name="get-add-on-acquisitions"></a>Получение сведений о покупках надстройки

Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных в формате JSON по приобретениям надстроек для приложения в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [отчет о приобретениях надстройка](../publish/add-on-acquisitions-report.md) в центре партнеров.

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса                                                                |
|--------|----------------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание          |
|---------------|--------|--------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

Параметр *applicationId* или *inAppProductId* обязателен. Для извлечения данных о покупках всех надстроек, зарегистрированных в приложении, укажите параметр *applicationId*. Для извлечения данных о покупках конкретной надстройки укажите параметр *inAppProductId*. Если указаны оба параметра, параметр *applicationId* игнорируется.

| Параметр        | Тип   |  Описание      |  Обязательно  
|---------------|--------|---------------|------|
| applicationId | Строка | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для приложения, по которому требуется получить данные о покупках надстройки.  |  Да  |
| inAppProductId | Строка | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для надстройки, по которой требуется получить данные о покупках.  | Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные о покупках надстройки. По умолчанию используется текущая дата. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные о покупках надстройки. По умолчанию используется текущая дата. |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку "Далее", которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter |Строка  | Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields) | Нет   |
| aggregationLevel | Строка | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong>. | Нет |
| orderby | Строка | Оператор, который определяет порядок полученных значений данных для каждой покупки надстройки. Используется следующий синтаксис: <em>orderby=field [порядк.],field [порядк.],...</em>. Параметр <em>field</em> может быть одной из следующих строк:<ul><li><strong>Дата</strong></li><li><strong>acquisitionType</strong></li><li><strong>возрастным группам</strong></li><li><strong>клиентам магазинов</strong></li><li><strong>Пол</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>типа устройства</strong></li><li><strong>orderName</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | Строка | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>Дата</strong></li><li><strong>ApplicationName</strong></li><li><strong>inAppProductName</strong></li><li><strong>acquisitionType</strong></li><li><strong>возрастным группам</strong></li><li><strong>клиентам магазинов</strong></li><li><strong>Пол</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>типа устройства</strong></li><li><strong>orderName</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>Дата</strong></li><li><strong>Идентификатор приложения</strong></li><li><strong>inAppProductId</strong></li><li><strong>Количество приобретений</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=ageGroup,market&amp;aggregationLevel=week</em></p> |  Нет  |


### <a name="filter-fields"></a>Поля фильтра

Параметр *filter* запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**; операторы можно комбинировать с помощью **and** или **or**. Несколько примеров параметров *filter*:

-   *Фильтр eq рынка = меня eq «US» и пол "*
-   *Фильтр = (ne рынке "США") и (пол ne «Неизвестно») и (ne Пол меня ") и (рынок ne «Нет») и («больше чем 55" ne возрастным группам или ne возрастным группам «меньше, чем 13")*

Список поддерживаемых полей см. в следующей таблице. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки.

| Поля        |  Описание        |
|---------------|-----------------|
| acquisitionType | Одна из следующих строк:<ul><li><strong>бесплатно</strong></li><li><strong>Пробная версия</strong></li><li><strong>Платные</strong></li><li><strong>Код акции</strong></li><li><strong>Iap</strong></li></ul> |
| ageGroup | Одна из следующих строк:<ul><li><strong>меньше, чем 13</strong></li><li><strong>13-17</strong></li><li><strong>18-24</strong></li><li><strong>25-34</strong></li><li><strong>35-44</strong></li><li><strong>44 55</strong></li><li><strong>больше, чем 55</strong></li><li><strong>Неизвестный</strong></li></ul> |
| storeClient | Одна из следующих строк:<ul><li><strong>Windows Phone Store (клиент)</strong></li><li><strong>Microsoft Store (клиент)</strong></li><li><strong>Microsoft Store (веб)</strong></li><li><strong>Закупка организациями</strong></li><li><strong>Другие</strong></li></ul> |
| gender | Одна из следующих строк:<ul><li><strong>m</strong></li><li><strong>F</strong></li><li><strong>Неизвестный</strong></li></ul> |
| market | Строка, содержащая код страны по стандарту ISO 3166 для рынка, на котором произошла покупка |
| osVersion | Одна из следующих строк:<ul><li><strong>Windows Phone 7.5</strong></li><li><strong>Windows Phone 8</strong></li><li><strong>Windows Phone 8.1</strong></li><li><strong>Windows Phone 10</strong></li><li><strong>Windows 8</strong></li><li><strong>Windows 8.1</strong></li><li><strong>Windows 10</strong></li><li><strong>Неизвестный</strong></li></ul> |
| deviceType | Одна из следующих строк:<ul><li><strong>ПК</strong></li><li><strong>Телефон</strong></li><li><strong>Консоль</strong></li><li><strong>Интернета вещей</strong></li><li><strong>Holographic</strong></li><li><strong>Неизвестный</strong></li></ul> |
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
| Значение      | Массив  | Массив объектов, содержащий сводную информацию о покупках надстройки. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупках надстройки](#add-on-acquisition-values).                                                                                                              |
| @nextLink  | Строка | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках надстроек. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.    |


<span id="add-on-acquisition-values" />

### <a name="add-on-acquisition-values"></a>Значения информации о покупке надстройки

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип    | Описание        |
|---------------------|---------|---------------------|
| date                | Строка  | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| inAppProductId      | Строка  | Код продукта в Магазине для надстройки, по которой запрашиваются данные о покупках.                                                                                                                                                                 |
| inAppProductName    | Строка  | Отображаемое имя надстройки. Это значение отображается в данных ответа только в том случае, если для параметра *aggregationLevel* задано значение **day**. Исключение составляют случаи, когда указано значение для поля **inAppProductName** параметра *groupby*.                                                                                                                                                                                                            |
| applicationId       | Строка  | Код продукта в Магазине для приложения, по которому требуется получить данные о покупках надстройки.                                                                                                                                                           |
| applicationName     | Строка  | Отображаемое имя приложения.                                                                                                                                                                                                             |
| deviceType          | Строка  | Тип устройства, на котором совершена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                  |
| orderName           | Строка  | Имя заказа                                                                                                                                                                                                                   |
| storeClient         | Строка  | Версия Магазина, в котором произведена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                            |
| osVersion           | Строка  | Версия ОС, в которой произведена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                   |
| market              | Строка  | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка                                                                                                                                                                  |
| gender              | Строка  | Пол пользователя, совершившего покупку. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                    |
| ageGroup            | Строка  | Возрастная группа пользователя, совершившего покупку. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                 |
| acquisitionType     | Строка  | Тип приобретения (бесплатное, платное и т. д.). Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                    |
| acquisitionQuantity | целое число | Количество покупок                        |


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

## <a name="related-topics"></a>Статьи по теме

* [Отчет о приобретении надстроек](../publish/add-on-acquisitions-report.md)
* [Доступ к данным аналитики с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получить надстройку преобразования каналом](get-add-on-conversions-by-channel.md)
* [Получить приобретения приложений](get-app-acquisitions.md)
* [Получить данные воронки приобретения приложений](get-acquisition-funnel-data.md)
* [Получить приложение преобразования каналом](get-app-conversions-by-channel.md)

 

 
