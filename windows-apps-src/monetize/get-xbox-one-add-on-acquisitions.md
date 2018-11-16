---
author: Xansky
description: Используйте этот метод в API аналитики для Microsoft Store для получения данных о сводных надстройки.
title: Получение сведений о приобретенных надстройках для Xbox One
ms.author: mhopkins
ms.date: 10/18/2018
ms.topic: article
keywords: Windows 10, uwp, службы магазина, API, Xbox One надстроек аналитики Microsoft Store
ms.localizationpriority: medium
ms.openlocfilehash: e703c0c07e981ebf21ad3388ad178eabdd5c068d
ms.sourcegitcommit: e38b334edb82bf2b1474ba686990f4299b8f59c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6837615"
---
# <a name="get-xbox-one-add-on-acquisitions"></a>Получение сведений о приобретенных надстройках для Xbox One

Используйте этот метод в API аналитики Microsoft Store для получения данных о покупках надстройки сводных в формате JSON для Xbox One игры, была добавлена через портал разработчика Xbox (XDP) и доступны на информационной панели центра партнеров аналитики XDP.

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                                |
|--------|----------------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/addonacquisitions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание          |
|---------------|--------|--------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

*ApplicationId* или *addonProductId* параметр является обязательным. Для извлечения данных о покупках всех надстроек, зарегистрированных в приложении, укажите параметр *applicationId*. Чтобы получить данные о покупках надстройки, укажите параметр *addonProductId* . Если указаны оба параметра, параметр *applicationId* игнорируется.

| Параметр        | Тип   |  Описание      |  Обязательный  |
|---------------|--------|---------------|------|
| applicationId | string | *ProductId* игры на Xbox One, по которому запрашиваются данные о покупках. Чтобы получить *productId* вашей игры, перейдите к игре в программе аналитики XDP и получите *productId* по URL-адресу. Кроме того Если загрузить данные о приобретениях из аналитического отчета центра партнеров, *productId* включается в TSV-файл. |  Да  |
| addonProductId | string | *ProductId* надстройки, для которого требуется получить данные о покупках.  | Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные о покупках надстройки. По умолчанию используется текущая дата. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные о покупках надстройки. По умолчанию используется текущая дата |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter |string  | <p>Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов eq или ne; выражения можно комбинировать, используя операторы and или or. В параметре filter строковые значения должны быть заключены в одиночные кавычки. Например, filter=market eq 'US' and gender eq 'm'.</p> <p>Вы можете указать следующие поля из тела ответа:</p> <ul><li><strong>acquisitionType</strong></li><li><strong>age</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>sandboxId</strong></li></ul>| Нет   |
| aggregationLevel | string | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong> | Нет |
| orderby | string | Оператор, который определяет порядок полученных значений данных для каждой покупки надстройки. Используется следующий синтаксис <em>orderby = field [order], field [order], …</em> Параметр <em>поле</em> может принимать одно из следующих строк:<ul><li><strong>date,</strong></li><li><strong>acquisitionType,</strong></li><li><strong>age</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>orderName.</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | string | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>date</strong></li><li><strong>applicationName</strong></li><li><strong>addonProductName</strong></li><li><strong>acquisitionType</strong></li><li><strong>age</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>paymentInstrumentType</strong></li><li><strong>sandboxId</strong></li><li><strong>xboxTitleIdHex</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date</strong></li><li><strong>applicationId</strong></li><li><strong>addonProductId</strong></li><li><strong>acquisitionQuantity</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em> &amp;groupby = возрастной, рынка&amp;aggregationLevel = week</em></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующих примерах демонстрируются несколько запросов на получение информации о покупках надстройки. Замените значения *addonProductId* и *applicationId* соответствующий код продукта в магазине для надстройки или приложения.

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
| Значение      | array  | Массив объектов, содержащий сводную информацию о покупках надстройки. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупке надстройки](#add-on-acquisition-values).                                                                                                              |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках надстроек. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.    |


<span id="add-on-acquisition-values" />

### <a name="add-on-acquisition-values"></a>Значения информации о покупке надстройки

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип    | Описание        |
|---------------------|---------|---------------------|
| date                | string  | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат. |
| addonProductId      | string  | *ProductId* надстройки, по которому запрашиваются данные о покупках.                                                                                                                                                                 |
| addonProductName    | строка  | Отображаемое имя надстройки. Это значение отображается только в данных ответа Если параметр *aggregationLevel* **день**, если вы не укажете поле **addonProductName** в параметре *groupby* .                                                                                                                                                                                                            |
| applicationId       | string  | *ProductId* приложения, для которого требуется получить данные о покупках надстройки.                                                                                                                                                           |
| applicationName     | строка  | Отображаемое имя игры.                                                                                                                                                                                                             |
| deviceType          | строка  | <p>Одна из следующих строк, указывающая тип устройства, на котором было выполнено приобретение:</p> <ul><li>«PC»</li><li>«Телефон»</li><li>«Консоль»</li><li>«IoT»</li><li>«Сервер»</li><li>«Планшет»</li><li>«Holographic»</li><li>«Неизвестно»</li></ul>                                                                                                  |
| storeClient         | строка  | <p>Одна из следующих строк, указывающая версию Store, где совершено приобретение:</p> <ul><li>«Windows Phone Store (client)»</li><li>«Microsoft Store (client)» (или «Windows Store (client)» Если запрашиваются данные до 23 марта 2018 г.)</li><li>«Microsoft Store (web)» (или «Windows Store (web)» Если запрашиваются данные до 23 марта 2018 г.)</li><li>«Volume purchase by organizations»</li><li>«Другие»</li></ul>                                                                                            |
| osVersion           | string  | Версия ОС, в которой произведена покупка. Для этого метода это значение всегда равно «Windows 10».                                                                                                   |
| market              | string  | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка                                                                                                                                                                  |
| gender              | строка  | <p>Одна из следующих строк, указывающая пол пользователя, совершившего приобретение:</p> <ul><li>«m»</li><li>«ф»</li><li>«Неизвестно»</li></ul>                                                                                                    |
| age            | строка  | <p>Одна из следующих строк, указывающая возрастную группу пользователя, совершившего приобретение:</p> <ul><li>«less than 13»</li><li>«13-17»</li><li>«18 до 24»</li><li>«25-34»</li><li>«35-44»</li><li>«44-55»</li><li>«больше, чем 55»</li><li>«Неизвестно»</li></ul>                                                                                                 |
| acquisitionType     | строка  | <p>Одна из следующих строк, указывающих тип приобретения:</p> <ul><li>«Бесплатный»</li><li>«Пробная версия»</li><li>«Платные»</li><li>«Рекламный код»</li><li>«Iap»</li><li>«Подписки Iap»</li><li>«Закрытый аудитории»</li><li>«Подготовка заказа»</li><li>«Xbox Game Pass» (или «Game Pass», если запрашиваются данные до 23 марта 2018 г.)</li><li>«Диск»</li><li>«Prepaid Code»</li><li>«Плата состоянию заказа»</li><li>«Отмена заказа состоянию»</li><li>«Сбой состоянию заказа»</li></ul>                                                                                                    |
| acquisitionQuantity | integer | Количество покупок.                        |


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
