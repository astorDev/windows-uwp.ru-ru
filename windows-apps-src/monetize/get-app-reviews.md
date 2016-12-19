---
author: mcleanbyron
ms.assetid: 2967C757-9D8A-4B37-8AA4-A325F7A060C5
description: "Используйте этот метод в API аналитики для Магазина Windows для получения отзывов о приложении в заданном диапазоне дат или с учетом других дополнительных фильтров."
title: "Получение отзывов о приложении"
translationtype: Human Translation
ms.sourcegitcommit: 7d05c8953f1f50be0b388a044fe996f345d45006
ms.openlocfilehash: 49d3f3cb608f3207306af443c67b684a0ae9f319

---

# <a name="get-app-reviews"></a>Получение отзывов о приложении


Используйте этот метод в API аналитики для Магазина Windows для получения рецензий в формате JSON в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [отчете о рецензиях](../publish/reviews-report.md) на информационной панели Центра разработки для Windows.

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода необходимо выполнить следующие действия:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Магазина Windows.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос

### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/reviews``` |

<span/> 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|---------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/> 

### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | Код продукта в Магазине для приложения, по которому требуется получить данные об отзывах. Код продукта в Магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) информационной панели Центра разработки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8 |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные об отзывах. По умолчанию используется текущая дата |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные об отзывах. По умолчанию используется текущая дата |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter |string  | Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields) | Нет   |
| orderby | строка | Оператор, который определяет порядок полученных значений данных. Используется следующий синтаксис: <em>orderby=field [порядк.],field [порядк.],...</em>. Параметр <em>field</em> может быть одной из следующих строк:<ul><li><strong>date,</strong></li><li><strong>osVersion,</strong></li><li><strong>market,</strong></li><li><strong>deviceType,</strong></li><li><strong>isRevised,</strong></li><li><strong>packageVersion,</strong></li><li><strong>deviceModel,</strong></li><li><strong>productFamily,</strong></li><li><strong>deviceScreenResolution,</strong></li><li><strong>isTouchEnabled,</strong></li><li><strong>reviewerName,</strong></li><li><strong>reviewTitle,</strong></li><li><strong>reviewText,</strong></li><li><strong>helpfulCount,</strong></li><li><strong>notHelpfulCount,</strong></li><li><strong>responseDate,</strong></li><li><strong>responseText,</strong></li><li><strong>deviceRAM,</strong></li><li><strong>deviceStorageCapacity,</strong></li><li><strong>rating.</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |

<span/>
 
### <a name="filter-fields"></a>Поля фильтра

Параметр *filter* запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**, и некоторые поля также поддерживают операторы выражения **contains**, **gt**, **lt**, **ge** и **le**. Операторы можно комбинировать с помощью **and** или **or**.

Пример строки *filter*: *filter=contains(reviewText,'great') and contains(reviewText,'ads') and deviceRAM lt 2048 and market eq 'US'*.

Список поддерживаемых полей и операторов для каждого поля см. в следующей таблице. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки.

| Поля        | Поддерживаемые операторы   |  Описание        |
|---------------|--------|-----------------|
| market | eq, ne | Строка, которая содержит код страны рынка устройства по стандарту ISO 3166 |
| osVersion  | eq, ne  | Одна из следующих строк:<ul><li><strong>Windows Phone 7.5;</strong></li><li><strong>Windows Phone 8;</strong></li><li><strong>Windows Phone 8.1;</strong></li><li><strong>Windows Phone 10;</strong></li><li><strong>Windows 8;</strong></li><li><strong>Windows 8.1;</strong></li><li><strong>Windows 10</strong></li><li><strong>Неизвестно</strong></li></ul>  |
| deviceType  | eq, ne  | Одна из следующих строк:<ul><li><strong>Компьютер</strong></li><li><strong>Телефон</strong></li><li><strong>Консоль</strong></li><li><strong>Интернет вещей</strong></li><li><strong>Holographic</strong></li><li><strong>Неизвестно</strong></li></ul>  |
| isRevised  | eq, ne  | Если нужно отфильтровать пересмотренные отзывы, укажите значение <strong>true</strong>, в противном случае — <strong>false</strong>  |
| packageVersion  | eq, ne  | Версия пакета приложения, на которую оставлен отзыв  |
| deviceModel  | eq, ne  | Тип устройства, на котором был оставлен отзыв на приложение  |
| productFamily  | eq, ne  | Одна из следующих строк:<ul><li><strong>Компьютер</strong></li><li><strong>Планшет</strong></li><li><strong>Телефон</strong></li><li><strong>Носимое устройство</strong></li><li><strong>Server</strong></li><li><strong>Collaborative</strong></li><li><strong>Прочее</strong></li></ul>  |
| deviceRAM  | eq, ne, gt, lt, ge, le  | Физическое ОЗУ в МБ.  |
| deviceScreenResolution  | eq, ne  | Разрешение экрана устройства в формате &quot;<em>ширина</em> x <em>высота</em>&quot;.   |
| deviceStorageCapacity  | eq, ne, gt, lt, ge, le   | Объем основного запоминающего устройства в ГБ.  |
| isTouchEnabled  | eq, ne  | Если нужно отфильтровать устройства с поддержкой сенсорного ввода, укажите значение <strong>true</strong>, в противном случае — <strong>false</strong>   |
| reviewerName  | eq, ne  |  Имя автора отзыва. |
| rating  | eq, ne, gt, lt, ge, le  | Оценка приложения в звездах.  |
| reviewTitle  | eq, ne, contains  | Заголовок отзыва.  |
| reviewText  | eq, ne, contains  |  Текстовое содержимое отзыва. |
| helpfulCount  | eq, ne  |  Количество оценок отзыва как полезного. |
| notHelpfulCount  | eq, ne  | Количество оценок отзыва как бесполезного.  |
| responseDate  | eq, ne  | Дата отправки ответа.  |
| responseText  | eq, ne, contains  | Текстовое содержимое ответа.  |


<span/> 

### <a name="request-example"></a>Пример запроса

Ниже приведено несколько примеров запросов для получения данных об отзывах. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/reviews?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/reviews?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=contains(reviewText,'great') and contains(reviewText,'ads') and deviceRAM lt 2048 and market eq 'US' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                            |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Value      | array  | Массив объектов, содержащий информацию об отзывах. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения отзывов](#review-values)                                                                                                                                      |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об отзывах. |
| TotalCount | целое число    | Общее количество строк в результирующих данных для запроса.                                                                                                                                                                                                                             |

<span/>
 
### <a name="review-values"></a>Значения отзывов

Элементы в массиве *Value* содержат следующие значения.

| Значение                  | Тип    | Описание                                                                                                                                                                                                                          |
|------------------------|---------|---------------------|
| date                   | строка  | Первая дата в диапазоне дат, для которого требуется получить данные об отзывах. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат  |
| applicationId          | строка  | Код продукта в Магазине для приложения, по которому запрашиваются данные об отзывах.        |
| applicationName        | строка  | Отображаемое имя приложения.   |
| market                 | строка  | Код страны рынка, на котором был отправлен отзыв, по стандарту ISO 3166.       |
| osVersion              | строка  | Версия ОС, в которой был отправлен отзыв. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)         |
| deviceType             | строка  | Тип устройства, на котором был отправлен отзыв. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)      |
| isRevised              | Boolean | Значение **true** указывает, что отзыв был пересмотрен; в противном случае используется значение **false**         |
| packageVersion         | string  | Версия пакета приложения, на которую оставлен отзыв   |
| deviceModel            | string  | Тип устройства, на котором был оставлен отзыв на приложение      |
| productFamily          | string  | Имя семейства устройств. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)  |
| deviceRAM              | число  | Физическое ОЗУ в МБ.        |
| deviceScreenResolution | строка  | Разрешение экрана устройства в формате "*ширина* x *высота*"        |
| deviceStorageCapacity  | число  | Объем основного запоминающего устройства в ГБ.   |
| isTouchEnabled         | Логическое | Значение **true** указывает, что сенсорный ввод включен; в противном случае используется значение **false**      |
| reviewerName           | строка  | Имя автора отзыва.      |
| rating                 | число  | Оценка приложения в звездах.         |
| reviewTitle            | строка  | Заголовок отзыва.       |
| reviewText             | строка  | Текстовое содержимое отзыва.     |
| helpfulCount           | число  | Количество оценок отзыва как полезного.     |
| notHelpfulCount        | число  | Количество оценок отзыва как бесполезного.               |
| responseDate           | строка  | Дата отправки ответа.                 |
| responseText           | строка  | Текстовое содержимое ответа.        |

<span/> 

### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2015-07-29",
      "applicationId": "9NBLGGGZ5QDR",
      "applicationName": "Contoso demo",
      "market": "US",
      "osVersion": "10.0.10240.16410",
      "deviceType": "PC",
      "isRevised": true,
      "packageVersion": "",
      "deviceModel": "Microsoft Corporation-Virtual Machine",
      "productFamily": "PC",
      "deviceRAM": -1,
      "deviceScreenResolution": "1024 x 768",
      "deviceStorageCapacity": 51200,
      "isTouchEnabled": false,
      "reviewerName": "ALeksandra",
      "rating": 5,
      "reviewTitle": "Love it",
      "reviewText": "Great app for demos and great dev response.",
      "helpfulCount": 0,
      "notHelpfulCount": 0,
      "responseDate": "2015-08-07T01:50:22.9874488Z",
      "responseText": "1"
    }
  ],
  "@nextLink": null,
  "TotalCount": 1
}
```

## <a name="related-topics"></a>Связанные статьи

* [Отчет "Рецензии"](../publish/reviews-report.md)
* [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md)
* [Получение сведений о покупках приложения](get-app-acquisitions.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Получение сведений об оценках приложения](get-app-ratings.md)



<!--HONumber=Dec16_HO1-->


