---
ms.assetid: C1E42E8B-B97D-4B09-9326-25E968680A0F
description: Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных о покупках приложения в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение сведений о приобретениях приложения
ms.date: 03/23/2018
ms.topic: article
keywords: windows 10, uwp, службы Магазина, API аналитики для Microsoft Store, приобретения приложения
ms.localizationpriority: medium
ms.openlocfilehash: 4ef5c9cedcb828f6c7df8a294fc4aad87e9f74ae
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8704655"
---
# <a name="get-app-acquisitions"></a>Получение сведений о приобретениях приложения


Используйте этот метод в API аналитики Microsoft Store для получения сводных данных в формате JSON для приложения в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [отчете о приобретениях](../publish/acquisitions-report.md) в центре партнеров.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для приложения, по которому требуется получить данные о покупках IAP.  |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Например, *filter=market eq 'US' and gender eq 'm'*. <p/><p/>Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>acquisitionType</strong></li><li><strong>ageGroup</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>orderName</strong></li></ul> | Нет   |
| aggregationLevel | string | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong> | Нет |
| orderby | string | Оператор, который определяет порядок полученных значений данных для каждой покупки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>, где параметр <em>field</em> может принимать одно из следующих строковых значений:<ul><li><strong>date,</strong></li><li><strong>acquisitionType,</strong></li><li><strong>ageGroup</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>orderName.</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | string | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>date</strong></li><li><strong>applicationName</strong></li><li><strong>acquisitionType</strong></li><li><strong>ageGroup</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>orderName</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date</strong></li><li><strong>applicationId</strong></li><li><strong>acquisitionQuantity</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=ageGroup,market&amp;aggregationLevel=week</em></p> |  Нет  |

### <a name="request-example"></a>Пример запроса

В следующем примере демонстрируются несколько запросов на получение информации о покупках приложения. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0  HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US' and gender eq 'm'  HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | array  | Массив объектов, содержащий сводную информацию о покупках для данного приложения. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупках](#acquisition-values).                                                                                                                      |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса                 |


### <a name="acquisition-values"></a>Значения информации о покупках

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| date                | string | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId       | string | Код продукта в Магазине для приложения, по которому запрашиваются данные о покупках.     |
| applicationName     | string | Отображаемое имя приложения.   |
| deviceType          | string | Одна из следующих строк, указывающая тип устройства, на котором совершено приобретение:<ul><li><strong>ПК</strong></li><li><strong>Телефон</strong></li><li><strong>Console (консоль),</strong></li><li><strong>Интернет вещей</strong></li><li><strong>Holographic (голография),</strong></li><li><strong>Неизвестно</strong></li></ul>    |
| orderName           | string | Имя заказа  |
| storeClient         | строка | Одна из следующих строк, указывающая версию Store, где совершено приобретение:<ul><li>**Windows Phone Store (client);**</li><li>**Microsoft Store (client)** (или **Windows Store (client)**, если запрашиваются данные до 23 марта 2018 г.)</li><li>**Microsoft Store (web)** (или **Windows Store (web)**, если запрашиваются данные до 23 марта 2018 г.)</li><li>**Volume purchase by organizations;**</li><li>**Другое**</li></ul>                                                                                            |
| osVersion           | строка | Одна из следующих строк, указывающая версию ОС, в которой произведено приобретение:<ul><li><strong>Windows Phone 7.5</strong></li><li><strong>Windows Phone 8;</strong></li><li><strong>Windows Phone8.1;</strong></li><li><strong>Windows Phone 10;</strong></li><li><strong>Windows 8;</strong></li><li><strong>Windows 8.1</strong></li><li><strong>Windows10</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>  |
| market              | string | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка  |
| gender              | строка | Одна из следующих строк, указывающая пол пользователя, совершившего приобретение:<ul><li><strong>m</strong></li><li><strong>f,</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>    |
| ageGroup            | строка | Одна из следующих строк, указывающая возрастную группу пользователя, совершившего приобретение:<ul><li><strong>less than 13,</strong></li><li><strong>13-17,</strong></li><li><strong>18-24,</strong></li><li><strong>25-34,</strong></li><li><strong>35-44,</strong></li><li><strong>44-55,</strong></li><li><strong>greater than 55,</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>  |
| acquisitionType     | строка | Одна из следующих строк, указывающих тип приобретения:<ul><li><strong>Free</strong></li><li><strong>Trial</strong></li><li><strong>Paid</strong></li><li><strong>Promotional code</strong></li><li><strong>Iap</strong></li><li><strong>Iap подписки</strong></li><li><strong>Частная аудитория</strong></li><li><strong>Подготовка заказа</strong></li><li><strong>Xbox Game Pass</strong> (или <strong>Game Pass</strong> при запросе данных до 23 марта 2018 г.)</li><li><strong>Disk</strong></li><li><strong>Prepaid Code</strong></li></ul>   |
| acquisitionQuantity | number | Число покупок, выполненных на указанном уровне агрегирования    |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2016-02-01",
      "applicationId": "9NBLGGGZ5QDR",
      "applicationName": "Contoso Demo",
      "deviceType": "Phone",
      "orderName": "",
      "storeClient": "Windows Phone Store (client)",
      "osVersion": "Windows Phone 8.1",
      "market": "IT",
      "gender": "m",
      "ageGroup": "0-17",
      "acquisitionType": "Free",
      "acquisitionQuantity": 1
    }
  ],
  "@nextLink": "appacquisitions?applicationId=9NBLGGGZ5QDR&aggregationLevel=day&startDate=2015/01/01&endDate=2016/02/01&top=1&skip=1&orderby=date desc",
  "TotalCount": 466766
}
```

## <a name="related-topics"></a>Связанные статьи

* [Отчет "Приобретения"](../publish/acquisitions-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение данных воронки приобретений приложения](get-acquisition-funnel-data.md)
* [Получение преобразований приложения по каналу](get-app-conversions-by-channel.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
