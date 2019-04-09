---
description: Этот метод можно используйте в API анализа Microsoft Store, чтобы получить надстройку статистические данные о приобретении.
title: Получение сведений о приобретениях надстроек для Xbox One
ms.date: 03/06/2019
ms.topic: article
keywords: Windows 10, uwp, службы Store, API, Xbox One приобретения надстройки анализа Microsoft Store
ms.localizationpriority: medium
ms.openlocfilehash: 1387e9adc5d6ef3e7a76b6b2e898c863e8434a9b
ms.sourcegitcommit: e63fbd7a63a7e8c03c52f4219f34513f4b2bb411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57822909"
---
# <a name="get-xbox-one-add-on-acquisitions"></a>Получение сведений о приобретениях надстроек для Xbox One

Используйте этот метод в Microsoft Store analytics API для получения надстройки статистические данные о приобретении в формате JSON для Xbox One игр, полученные через портал разработчиков Xbox (XDP) и доступна на панели мониторинга центра партнеров XDP Analytics.

## <a name="prerequisites"></a>Предварительные требования

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса                                                                |
|--------|----------------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/addonacquisitions``` |


### <a name="request-header"></a>Заголовок запроса

| Header        | Тип   | Описание          |
|---------------|--------|--------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

*ApplicationId* или *addonProductId* параметр является обязательным. Для извлечения данных о покупках всех надстроек, зарегистрированных в приложении, укажите параметр *applicationId*. Чтобы получить данные о приобретении одной надстройки, укажите *addonProductId* параметра. Если указаны оба параметра, параметр *applicationId* игнорируется.

| Параметр        | Тип   |  Описание      |  Обязательно  |
|---------------|--------|---------------|------|
| applicationId | строка | *ProductId* игры Xbox One, для которого извлекаются данные о приобретении. Обратите внимание, что это идентификатор Store и не идентификатор XDP продукта. Чтобы получить *productId* своей игры, перейдите в игру в программе Analytics XDP и получить *productId* из URL-адрес. Кроме того, если данные приобретения загрузить отчет по аналитике центра партнеров *productId* включается в TSV-файл. |  Да  |
| addonProductId | строка | *ProductId* надстройки, для которого требуется извлечь данные о приобретении.  | Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные о покупках надстройки. По умолчанию используется текущая дата. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные о покупках надстройки. По умолчанию используется текущая дата. |  Нет  |
| top | ssNoversion | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку "Далее", которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | ssNoversion | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter |строка  | <p>Один или несколько операторов для фильтрации строк в ответе. Каждая инструкция содержит имя поля из текста ответа и значения, которые связаны с операторами eq или ne и инструкции могут быть объединены с помощью и или или. Строковые значения должны заключаться в одинарные кавычки в параметр filter. Например, фильтр = eq рынка меня eq «US» и пол ".</p> <p>Вы можете указать следующие поля из тела ответа:</p> <ul><li><strong>acquisitionType</strong></li><li><strong>Срок действия</strong></li><li><strong>клиентам магазинов</strong></li><li><strong>Пол</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>типа устройства</strong></li><li><strong>sandboxId</strong></li></ul>| Нет   |
| aggregationLevel | строка | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong>. | Нет |
| orderby | строка | Оператор, который определяет порядок полученных значений данных для каждой покупки надстройки. Синтаксис <em>orderby = поле [order], [order],...</em> Параметр <em>field</em> может быть одной из следующих строк:<ul><li><strong>date</strong></li><li><strong>acquisitionType</strong></li><li><strong>Срок действия</strong></li><li><strong>клиентам магазинов</strong></li><li><strong>Пол</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>типа устройства</strong></li><li><strong>orderName</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | строка | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>date</strong></li><li><strong>ApplicationName</strong></li><li><strong>addonProductName</strong></li><li><strong>acquisitionType</strong></li><li><strong>Срок действия</strong></li><li><strong>клиентам магазинов</strong></li><li><strong>Пол</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>типа устройства</strong></li><li><strong>paymentInstrumentType</strong></li><li><strong>sandboxId</strong></li><li><strong>xboxTitleIdHex</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date</strong></li><li><strong>Идентификатор приложения</strong></li><li><strong>addonProductId</strong></li><li><strong>Количество приобретений</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например:  <em>&amp;groupby = возраст, рынка&amp;aggregationLevel = неделя</em></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующих примерах демонстрируются несколько запросов на получение информации о покупках надстройки. Замените *addonProductId* и *applicationId* значений с помощью соответствующий идентификатор Store для надстройки или приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/addonacquisitions?addonProductId=BRRT4NJ9B3D2&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/addonacquisitions?applicationId=BRRT4NJ9B3D1&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/addonacquisitions?addonProductId=BRRT4NJ9B3D2&startDate=1/1/2015&endDate=7/3/2015&top=100&skip=0&filter=market ne 'US' and gender ne 'Unknown' and gender ne 'm' and market ne 'NO' and age ne '>55' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание         |
|------------|--------|------------------|
| Значение      | Массив  | Массив объектов, содержащий сводную информацию о покупках надстройки. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупках надстройки](#add-on-acquisition-values).                                                                                                              |
| @nextLink  | строка | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках надстроек. |
| TotalCount | ssNoversion    | Общее количество строк в результирующих данных для запроса.    |


<span id="add-on-acquisition-values" />

### <a name="add-on-acquisition-values"></a>Значения информации о покупке надстройки

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип    | Описание        |
|---------------------|---------|---------------------|
| date                | строка  | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| addonProductId      | строка  | *ProductId* надстройки, для которого извлекаются данные о приобретении.                                                                                                                                                                 |
| addonProductName    | строка  | Отображаемое имя надстройки. Это значение отображается, только в данные ответа Если *aggregationLevel* параметр имеет значение **день**, если не указано **addonProductName** в *groupby* параметра.                                                                                                                                                                                                            |
| applicationId       | строка  | *ProductId* приложения, для которого требуется извлечь данные о приобретении надстроек. Обратите внимание, что это идентификатор Store и не идентификатор XDP продукта.                                                                                                                                                           |
| applicationName     | строка  | Отображаемое имя игры.                                                                                                                                                                                                             |
| deviceType          | строка  | <p>Одна из следующих строк, указывающая тип устройства, на котором было выполнено приобретение:</p> <ul><li>«PC»</li><li>«Телефон»</li><li>«Консоль»</li><li>«Интернета вещей»</li><li>«Server»</li><li>«Планшета»</li><li>«Holographic»</li><li>«Неизвестно»</li></ul>                                                                                                  |
| storeClient         | строка  | <p>Одна из следующих строк, указывающая версию Store, где совершено приобретение:</p> <ul><li>«Windows Phone Store (клиент)»</li><li>«Microsoft Store (клиент)» (или «Windows Store (клиент)» при запросе данных до 23 марта 2018 г.)</li><li>«Microsoft Store (веб)» (или «Windows Store (веб)» при запросе данных до 23 марта 2018 г.)</li><li>«Volume purchase организациями»</li><li>«Other»</li></ul>                                                                                            |
| osVersion           | строка  | Версия ОС, в которой произведена покупка. Для этого метода это значение всегда равно «Windows 10».                                                                                                   |
| market              | строка  | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка                                                                                                                                                                  |
| gender              | строка  | <p>Одна из следующих строк, указывающая пол пользователя, совершившего приобретение:</p> <ul><li>«m»</li><li>«f»</li><li>«Неизвестно»</li></ul>                                                                                                    |
| age            | строка  | <p>Одна из следующих строк, указывающая возрастную группу пользователя, совершившего приобретение:</p> <ul><li>«меньше, чем 13»</li><li>"13-17"</li><li>"18-24"</li><li>"25-34"</li><li>"35-44"</li><li>"44-55"</li><li>«больше, чем 55»</li><li>«Неизвестно»</li></ul>                                                                                                 |
| acquisitionType     | строка  | <p>Одна из следующих строк, указывающих тип приобретения:</p> <ul><li>«Бесплатный»</li><li>«Пробная версия»</li><li>«Оплачиваемое»</li><li>«Код»</li><li>«Iap»</li><li>«Подписка Iap»</li><li>«Audience Private»</li><li>«Pre Order»</li><li>«Xbox Game Pass» (или «Game Pass» при запросе данных до 23 марта 2018 г.)</li><li>«Диск»</li><li>«Предоплаченные код»</li><li>«Плата Pre Order»</li><li>«Отменена Pre Order»</li><li>«Failed Pre Order»</li></ul>                                                                                                    |
| acquisitionQuantity | целое число | Количество покупок                        |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2018-10-18",
      "addonProductId": " BRRT4NJ9B3D2",
      "addonProductName": "Contoso add-on 7",
      "applicationId": "BRRT4NJ9B3D1",
      "applicationName": "Contoso Demo",
      "deviceType": "Console",
      "storeClient": "Windows Phone Store (client)",
      "osVersion": "Windows 10",
      "market": "GB",
      "gender": "m",
      "age": "50orover",
      "acquisitionType": "iap",
      "acquisitionQuantity": 1
    }
  ],
  "@nextLink": "addonacquisitions?applicationId=BRRT4NJ9B3D1&addonProductId=&aggregationLevel=day&startDate=2015/01/01&endDate=2016/02/01&top=1&skip=1",
  "TotalCount": 33677
}
```
