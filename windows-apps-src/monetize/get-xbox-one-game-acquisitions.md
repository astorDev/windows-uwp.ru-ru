---
author: Xansky
ms.assetid: C1E42E8B-B97D-4B09-9326-25E968680A0F
description: Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных о покупках игры для Xbox One в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение сведений о покупках игр на Xbox One
ms.author: mhopkins
ms.date: 03/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, службы Store, API аналитики для Microsoft Store, покупки игры для Xbox One
ms.localizationpriority: medium
ms.openlocfilehash: a175096860fe2d2e73259ab9e82cbd5c33b8b889
ms.sourcegitcommit: 106aec1e59ba41aae2ac00f909b81bf7121a6ef1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "4614108"
---
# <a name="get-xbox-one-game-acquisitions"></a>Получение сведений о покупках игр на Xbox One

Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных в формате JSON для игры Xbox One, которая добавлена через портал разработчиков для Xbox (XDP), доступных также на информационной панели центра разработки аналитики XDP.

## <a name="prerequisites"></a>Что вам понадобится

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/acquisitions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | строка | Код продукта для игры на Xbox One, для которой запрашиваются данные о покупках. Чтобы получить код продукта для игры, перейдите к игре на портале разработчиков для Xbox (XDP) и получите код продукта из URL-адреса. Кроме того, если загрузить данные о приобретениях из аналитического отчета Центра разработки для Windows, код продукта включается в TSV-файл.  |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата |  Нет  |
| top | Целое число | Количество возвращаемых строк данных. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Например, *filter=market eq 'US' and gender eq 'm'*. <p/><p/>Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>acquisitionType</strong></li><li><strong>ageGroup</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>sandboxId</strong></li></ul> | Нет   |
| aggregationLevel | string | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong> | Нет |
| orderby | string | Оператор, который определяет порядок полученных значений данных для каждой покупки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>, где параметр <em>field</em> может принимать одно из следующих строковых значений:<ul><li><strong>date,</strong></li><li><strong>acquisitionType,</strong></li><li><strong>ageGroup</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>paymentInstrumentType</strong></li><li><strong>sandboxId</strong></li><li><strong>xboxTitleIdHex</strong></li><li><strong>purchasePriceUSDAmount</strong></li><li><strong>taxUSDAmount</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | string | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>date</strong></li><li><strong>applicationName</strong></li><li><strong>acquisitionType</strong></li><li><strong>ageGroup</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>paymentInstrumentType</strong></li><li><strong>sandboxId</strong></li><li><strong>xboxTitleIdHex</strong></li><li><strong>purchasePriceUSDAmount</strong></li><li><strong>taxUSDAmount</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date</strong></li><li><strong>applicationId</strong></li><li><strong>acquisitionQuantity</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=ageGroup,market&amp;aggregationLevel=week</em></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующем примере демонстрируются несколько запросов на получение информации о покупках игры на Xbox One. Замените значение *applicationId* кодом продукта в Store для вашей игры.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/acquisitions?applicationId=BRRT4NJ9B3D1&startDate=1/1/2017&endDate=2/1/2017&top=10&skip=0  HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/acquisitions?applicationId=BRRT4NJ9B3D1&startDate=8/1/2017&endDate=8/31/2017&skip=0&filter=market eq 'US' and gender eq 'm'  HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | массив  | Массив объектов, содержащий сводную информацию о покупках для игры. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупках](#acquisition-values).                                                                                                                      |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса              |


### <a name="acquisition-values"></a>Значения информации о покупках

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| date                | string | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId       | строка | Код продукта для игры на Xbox One, для которой запрашиваются данные о покупках. |
| applicationName     | строка | Отображаемое имя игры.       |
| acquisitionType     | строка | Одна из следующих строк, указывающих тип приобретения:<ul><li><strong>Free</strong></li><li><strong>Trial</strong></li><li><strong>Paid</strong></li><li><strong>Promotional code</strong></li><li><strong>Pre-order</strong></li><li>**Xbox Game Pass** (или **Game Pass** при запросе данных до 23 марта 2018 г.)</li><li><strong>Disk</strong></li><li><strong>Prepaid Code</strong></li></ul>    |
| ageGroup            | строка | Одна из следующих строк, указывающая возрастную группу пользователя, совершившего приобретение:<ul><li><strong>less than 13</strong></li><li><strong>13-17,</strong></li><li><strong>18-24,</strong></li><li><strong>25-34,</strong></li><li><strong>35-44,</strong></li><li><strong>44-55,</strong></li><li><strong>greater than 55,</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>     |
| deviceType          | строка | Одна из следующих строк, указывающая тип устройства, на котором было выполнено приобретение:<ul><li><strong>ПК</strong></li><li><strong>Телефон</strong></li><li><strong>Console (консоль),</strong></li><li><strong>IoT</strong></li><li><strong>Server</strong></li><li><strong>Tablet</strong></li><li><strong>Holographic</strong></li><li><strong>Неизвестно</strong></li></ul>  |
| gender              | строка | Одна из следующих строк, указывающая пол пользователя, совершившего приобретение:<ul><li><strong>m</strong></li><li><strong>f,</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>     |
| market              | string | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка  |
| osVersion           | string | Версия ОС, в которой произведена покупка. Для этого метода это значение всегда равно **Windows 10**.</li></ul>    |
| paymentInstrumentType           | строка | Одна из следующих строк, указывающая платежную инструкцию, использовавшуюся для приобретения:<ul><li><strong>Кредитная карта</strong></li><li><strong>Карта с прямым дебетованием</strong></li><li><strong>Предполагаемое приобретение</strong></li><li><strong>MS Balance</strong></li><li><strong>Мобильный оператор</strong></li><li><strong>Онлайн-перевод в банке</strong></li><li><strong>PayPal</strong></li><li><strong>Раздельная транзакция</strong></li><li><strong>Использование токена</strong></li><li><strong>Уплачена нулевая сумма</strong></li><li><strong>eWallet</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>    |
| sandboxId              | строка | Идентификатор песочницы, созданный для этой игры. Это может быть значение **RETAIL** или идентификатор для частной песочницы.  |
| storeClient         | строка | Одна из следующих строк, указывающая версию Store, где совершено приобретение:<ul><li>**Windows Phone Store (client);**</li><li>**Microsoft Store (client)** (или **Windows Store (client)**, если запрашиваются данные до 23 марта 2018 г.)</li><li>**Microsoft Store (web)** (или **Windows Store (web)**, если запрашиваются данные до 23 марта 2018 г.)</li><li>**Volume purchase by organizations;**</li><li>**Другое**</li></ul>                             |
| xboxTitleIdHex              | строка | Идентификатор продукта Xbox Live (представлен в шестнадцатеричной системе счисления), назначенный порталом разработчиков для Xbox (XDP) для игр с поддержкой Xbox Live.  |
| acquisitionQuantity | number | Число покупок, выполненных на указанном уровне агрегирования     |
| purchasePriceUSDAmount | число | Сумма, уплаченная пользователем за приобретение с конвертацией в доллары США по ежемесячно обновляемому обменному курсу.    |
| taxUSDAmount     | число | Сумма налога, применяемого к приобретению, с конвертацией в доллары США. |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2017-02-01",
      "applicationId": "BRRT4NJ9B3D1 ",
      "applicationName": "Contoso Game",
      "acquisitionType": "Paid",
      "ageGroup": "35-49",
      "deviceType": "Console",
      "gender": "m",
      "market": "US",
      "osVersion": "Windows 10",
      "PaymentInstrumentType": "Credit Card ",
      "sandboxId": "RETAIL",
      "storeClient": "Windows Store (web)",
      "xboxTitleIdHex": "",
      "acquisitionQuantity": 1,
      "purchasePriceUSDAmount": 29.99,
      "taxUSDAmount": 2.99
    }
  ],
  "@nextLink": "xbox/acquisitions?applicationId=BRRT4NJ9B3D1&aggregationLevel=day&startDate=2017/02/01&endDate=2017/03/01&top=1&skip=1&orderby=date desc",
  "TotalCount": 39812
}
```

## <a name="related-topics"></a>Статьи по теме

* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
