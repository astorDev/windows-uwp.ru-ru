---
author: Xansky
ms.assetid: DD4F6BC4-67CD-4AEF-9444-F184353B0072
description: Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных об оценках приложения в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение сведений об оценках приложения
ms.author: mhopkins
ms.date: 11/29/2017
ms.topic: article
keywords: windows 10, uwp, службы Магазина, API аналитики для Microsoft Store, оценки
ms.localizationpriority: medium
ms.openlocfilehash: 6b118abe32fe350277e02ed1f1f1d721e4c1690e
ms.sourcegitcommit: bdc40b08cbcd46fc379feeda3c63204290e055af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "6162074"
---
# <a name="get-app-ratings"></a>Получение сведений об оценках приложения

Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных об оценках в формате JSON в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [отчет отзывы](../publish/reviews-report.md) в центре партнеров.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.


## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/ratings``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для приложения, по которому требуется получить данные об оценках.  |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные об оценках. По умолчанию используется текущая дата |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные об оценках. По умолчанию используется текущая дата |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | string  | Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields) | Нет   |
| aggregationLevel | string | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong> | Нет |
| orderby | string | Оператор, который определяет порядок полученных значений данных для каждой оценки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>, где параметр <em>field</em> может принимать одно из следующих строковых значений:<ul><li><strong>date,</strong></li><li><strong>osVersion,</strong></li><li><strong>market,</strong></li><li><strong>deviceType</strong></li><li><strong>isRevised.</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | string | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>date</strong></li><li><strong>applicationName</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>isRevised</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date</strong></li><li><strong>applicationId</strong></li><li><strong>fiveStars</strong></li><li><strong>fourStars</strong></li><li><strong>threeStars</strong></li><li><strong>twoStars</strong></li><li><strong>oneStar</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=osVersion,market&amp;aggregationLevel=week</em></p> |  Нет  |

 
### <a name="filter-fields"></a>Поля фильтра

Параметр *filter* запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**; операторы можно комбинировать с помощью **and** или **or**.

Пример строки *filter*: *filter=market eq 'US' and deviceType eq 'phone' and isRevised eq true*.

Список поддерживаемых полей см. в следующей таблице. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки.

| Поля        |  Описание        |
|---------------|-----------------|
| market | Строка, содержащая код страны по стандарту ISO 3166 для рынка, на котором оценено ваше приложение. |
| osVersion | Одна из следующих строк:<ul><li><strong>Windows Phone 7.5;</strong></li><li><strong>Windows Phone 8;</strong></li><li><strong>Windows Phone8.1;</strong></li><li><strong>Windows Phone 10;</strong></li><li><strong>Windows 8;</strong></li><li><strong>Windows 8.1</strong></li><li><strong>Windows10</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul> |
| deviceType | Одна из следующих строк:<ul><li><strong>Компьютер</strong></li><li><strong>Телефон</strong></li><li><strong>Console (консоль),</strong></li><li><strong>Интернет вещей</strong></li><li><strong>Holographic (голография),</strong></li><li><strong>Неизвестно</strong></li></ul> |
| isRevised | Если нужно отфильтровать пересмотренные оценки, укажите значение <strong>true</strong>, в противном случае — <strong>false</strong> |


### <a name="request-example"></a>Пример запроса

Ниже приведено несколько примеров запросов для получения данных об оценках. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/ratings?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/ratings?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US' and deviceType eq 'phone' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                            |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Значение      | array  | Массив объектов, содержащий сводную информацию об оценках. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения оценок](#rating-values).                                                                                                                           |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об оценках. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.                               |


### <a name="rating-values"></a>Значения оценок

Элементы в массиве *Value* содержат следующие значения.

| Значение           | Тип    | Описание       |
|-----------------|---------|-------------------|
| date            | string  | Первая дата в диапазоне дат, для которого требуется получить данные об оценках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId   | string  | Код продукта в Магазине для приложения, по которому запрашиваются данные об оценках.         |
| applicationName | string  | Отображаемое имя приложения.    |
| market          | string  | Код страны рынка, на котором была поставлена оценка, по стандарту ISO 3166        |
| osVersion       | string  | Версия ОС, в которой была поставлена оценка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)            |
| deviceType      | string  | Тип устройства, на котором была поставлена оценка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)            |
| isRevised       | Boolean | Значение **true** указывает, что оценка была пересмотрена; в противном случае используется значение **false**   |
| oneStar         | number  | Количество оценок «одна звезда»        |
| twoStars        | number  | Количество оценок «две звезды»    |
| threeStars      | number  | Количество оценок «три звезды»   |
| fourStars       | number  | Количество оценок «четыре звезды»    |
| fiveStars       | number  | Количество оценок «пять звезд»    |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2015-02-13",
      "applicationId": "9NBLGGGZ5QDR",
      "applicationName": "Contoso demo",
      "market": "CN",
      "osVersion": "8.0.10517.0",
      "deviceType": "Phone",
      "isRevised": false,
      "oneStar": 0,
      "twoStars": 0,
      "threeStars": 0,
      "fourStars": 0,
      "fiveStars": 2
    }
  ],
  "@nextLink": "ratings?applicationId=9NBLGGGZ5QDR&aggregationLevel=day&startDate=2015/01/01&endDate=2016/02/01&top=1&skip=1",
  "TotalCount": 15242
}

```

## <a name="related-topics"></a>Связанные статьи

* [Отчет "Оценки"](../publish/reviews-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение сведений о покупках приложения](get-app-acquisitions.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Получение отзывов о приложении](get-app-reviews.md)
