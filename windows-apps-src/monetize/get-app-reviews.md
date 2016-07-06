---
author: mcleanbyron
ms.assetid: 2967C757-9D8A-4B37-8AA4-A325F7A060C5
description: "Используйте этот метод в API аналитики Магазина Windows для получения отзывов о приложении в заданном диапазоне дат или с учетом других дополнительных фильтров."
title: "Получение отзывов о приложении"
ms.sourcegitcommit: 02131e641cdaa76256845b38bcc50aa42d718601
ms.openlocfilehash: bb0f912bd3380e21e04fa44f2c75244c6585f03a

---

# Получение отзывов о приложении


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Используйте этот метод в API аналитики Магазина Windows для получения отзывов о приложении в заданном диапазоне дат или с учетом других дополнительных фильтров. Этот метод возвращает данные в формате JSON.

## Необходимые условия


Для использования этого метода необходимо:

-   привязать приложение Azure AD, которое вы будете использовать для вызова этого метода, с учетной записью Центра разработки;

-   получить маркер доступа Azure AD для приложения.

Подробнее: [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md).

## Запрос


### Синтаксис запроса

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET    | https://manage.devcenter.microsoft.com/v1.0/my/analytics/reviews |

 

### Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строковый | Обязательное. 1Маркер доступа Azure AD в форме**Bearer**&lt;*token*&gt;. |

 

### Тело запроса

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Параметр</th>
<th align="left">Тип</th>
<th align="left">Описание</th>
<th align="left">Обязательно</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">applicationId</td>
<td align="left">строковый</td>
<td align="left">Код продукта в Магазине для приложения, по которому требуется получить данные об отзывах. Код продукта в Магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) информационной панели Центра разработки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8</td>
<td align="left">Да</td>
</tr>
<tr class="even">
<td align="left">startDate</td>
<td align="left">дата</td>
<td align="left">Начальная дата диапазона дат, для которого требуется получить данные об отзывах. По умолчанию используется текущая дата</td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">endDate</td>
<td align="left">date</td>
<td align="left">Конечная дата диапазона дат, для которого требуется получить данные об отзывах. По умолчанию используется текущая дата</td>
<td align="left">Нет</td>
</tr>
<tr class="even">
<td align="left">top</td>
<td align="left">int</td>
<td align="left">Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных</td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">skip</td>
<td align="left">int</td>
<td align="left">Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д.</td>
<td align="left">Нет</td>
</tr>
<tr class="even">
<td align="left">filter</td>
<td align="left">string</td>
<td align="left">Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields)</td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">orderby</td>
<td align="left">строковый</td>
<td align="left">Оператор, который определяет порядок полученных значений данных для каждой оценки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>. Параметр <em>field</em> может быть одной из следующих строк:
<ul>
<li><strong>date,</strong></li>
<li><strong>osVersion,</strong></li>
<li><strong>market,</strong></li>
<li><strong>deviceType,</strong></li>
<li><strong>isRevised,</strong></li>
<li><strong>packageVersion,</strong></li>
<li><strong>deviceModel,</strong></li>
<li><strong>productFamily,</strong></li>
<li><strong>deviceScreenResolution,</strong></li>
<li><strong>isTouchEnabled,</strong></li>
<li><strong>reviewerName,</strong></li>
<li><strong>reviewTitle,</strong></li>
<li><strong>reviewText,</strong></li>
<li><strong>helpfulCount,</strong></li>
<li><strong>notHelpfulCount,</strong></li>
<li><strong>responseDate,</strong></li>
<li><strong>responseText,</strong></li>
<li><strong>deviceRAM,</strong></li>
<li><strong>deviceStorageCapacity,</strong></li>
<li><strong>rating.</strong></li>
</ul>
<p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p>
<p>Пример: <em>orderby</em> string: <em>orderby=date,market</em></p></td>
<td align="left">Нет</td>
</tr>
</tbody>
</table>

 
### Поля фильтра

Параметр *filter* в теле запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**, и некоторые поля также поддерживают операторы выражения **contains**, **gt**, **lt**, **ge** и **le**. Операторы можно комбинировать с помощью **and** или **or**.

Пример строки *filter*: *filter=contains(reviewText,'great') and contains(reviewText,'ads') and deviceRAM lt 2048 and market eq 'US'*.

Список поддерживаемых полей и операторов для каждого поля см. в следующей таблице. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Поля</th>
<th align="left">Поддерживаемые операторы</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">market</td>
<td align="left">eq, ne</td>
<td align="left">Строка, которая содержит код страны рынка устройства по стандарту ISO 3166</td>
</tr>
<tr class="even">
<td align="left">osVersion</td>
<td align="left">eq, ne</td>
<td align="left">Одна из следующих строк:
<ul>
<li><strong>Windows Phone 7.5;</strong></li>
<li><strong>Windows Phone 8;</strong></li>
<li><strong>Windows Phone 8.1;</strong></li>
<li><strong>Windows Phone 10;</strong></li>
<li><strong>Windows 8;</strong></li>
<li><strong>Windows 8.1;</strong></li>
<li><strong>Windows 10;</strong></li>
<li><strong>Unknown</strong></li>
</ul></td>
</tr>
<tr class="odd">
<td align="left">deviceType</td>
<td align="left">eq, ne</td>
<td align="left">Одна из следующих строк:
<ul>
<li><strong>PC,</strong></li>
<li><strong>Tablet,</strong></li>
<li><strong>Phone,</strong></li>
<li><strong>IoT,</strong></li>
<li><strong>Wearable,</strong></li>
<li><strong>Server,</strong></li>
<li><strong>Collaborative,</strong></li>
<li><strong>Other</strong></li>
</ul></td>
</tr>
<tr class="even">
<td align="left">isRevised</td>
<td align="left">eq, ne</td>
<td align="left">Если нужно отфильтровать пересмотренные отзывы, укажите значение <strong>true</strong>, в противном случае — <strong>false</strong></td>
</tr>
<tr class="odd">
<td align="left">packageVersion</td>
<td align="left">eq, ne</td>
<td align="left">Версия пакета приложения, на которую оставлен отзыв</td>
</tr>
<tr class="even">
<td align="left">deviceModel</td>
<td align="left">eq, ne</td>
<td align="left">Тип устройства, на котором был оставлен отзыв на приложение</td>
</tr>
<tr class="odd">
<td align="left">productFamily</td>
<td align="left">eq, ne</td>
<td align="left">Одна из следующих строк:
<ul>
<li><strong>PC,</strong></li>
<li><strong>Tablet,</strong></li>
<li><strong>Phone,</strong></li>
<li><strong>Wearable,</strong></li>
<li><strong>Server,</strong></li>
<li><strong>Collaborative,</strong></li>
<li><strong>Other</strong></li>
</ul></td>
</tr>
<tr class="even">
<td align="left">deviceScreenResolution</td>
<td align="left">eq, ne</td>
<td align="left">Разрешение экрана устройства в формате &quot;<em>ширина</em> x <em>высота</em>&quot;.</td>
</tr>
<tr class="odd">
<td align="left">isTouchEnabled</td>
<td align="left">eq, ne</td>
<td align="left">Если нужно отфильтровать устройства с поддержкой сенсорного ввода, укажите значение <strong>true</strong>, в противном случае — <strong>false</strong></td>
</tr>
<tr class="even">
<td align="left">reviewerName</td>
<td align="left">eq, ne</td>
<td align="left">Имя автора отзыва</td>
</tr>
<tr class="odd">
<td align="left">helpfulCount</td>
<td align="left">eq, ne</td>
<td align="left">Количество оценок отзыва как полезного</td>
</tr>
<tr class="even">
<td align="left">notHelpfulCount</td>
<td align="left">eq, ne</td>
<td align="left">Количество оценок отзыва как бесполезного</td>
</tr>
<tr class="odd">
<td align="left">reviewTitle</td>
<td align="left">eq, ne, contains</td>
<td align="left">Заголовок отзыва</td>
</tr>
<tr class="even">
<td align="left">reviewText</td>
<td align="left">eq, ne, contains</td>
<td align="left">Текстовое содержимое отзыва</td>
</tr>
<tr class="odd">
<td align="left">responseText</td>
<td align="left">eq, ne, contains</td>
<td align="left">Текстовое содержимое ответа</td>
</tr>
<tr class="even">
<td align="left">responseDate</td>
<td align="left">eq, ne</td>
<td align="left">Дата отправки ответа</td>
</tr>
<tr class="odd">
<td align="left">deviceRAM</td>
<td align="left">eq, ne, gt, lt, ge, le</td>
<td align="left">Физическое ОЗУ в МБ</td>
</tr>
<tr class="even">
<td align="left">deviceStorageCapacity</td>
<td align="left">eq, ne, gt, lt, ge, le</td>
<td align="left">Объем основного запоминающего устройства в ГБ</td>
</tr>
<tr class="odd">
<td align="left">rating</td>
<td align="left">eq, ne, gt, lt, ge, le</td>
<td align="left">Оценка приложения в звездах</td>
</tr>
</tbody>
</table>

 

### Пример запроса

Ниже приведено несколько примеров запросов для получения данных об отзывах. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/reviews?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/reviews?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=contains(reviewText,'great') and contains(reviewText,'ads') and deviceRAM lt 2048 and market eq 'US' HTTP/1.1
Authorization: Bearer <your access token>
```

## Ответ


### Тело ответа

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                            |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Value      | array  | Массив объектов, содержащий информацию об отзывах. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения отзывов](#review-values)                                                                                                                                      |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса                                                                                                                                                                                                                             |

 
### Значения отзывов

Элементы в массиве *Value* содержат следующие значения.

| Значение                  | Тип    | Описание                                                                                                                                                                                                                          |
|------------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| date                   | string  | Первая дата в диапазоне дат, для которого требуется получить данные об оценках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId          | строковый  | Код продукта в Магазине для приложения, по которому запрашиваются данные об оценках.                                                                                                                                                                 |
| applicationName        | string  | Отображаемое имя приложения                                                                                                                                                                                                         |
| market                 | string  | Код страны рынка, на котором была поставлена оценка, по стандарту ISO 3166                                                                                                                                                              |
| osVersion              | string  | Версия ОС, в которой была поставлена оценка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                               |
| deviceType             | string  | Тип устройства, на котором была поставлена оценка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                           |
| isRevised              | Boolean | Значение **true** указывает, что отзыв был пересмотрен; в противном случае используется значение **false**                                                                                                                                                       |
| packageVersion         | string  | Версия пакета приложения, на которую оставлен отзыв                                                                                                                                                                                    |
| deviceModel            | string  | Тип устройства, на котором был оставлен отзыв на приложение                                                                                                                                                                                    |
| productFamily          | string  | Имя семейства устройств. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                                         |
| deviceScreenResolution | string  | Разрешение экрана устройства в формате «*ширина* x *высота*»                                                                                                                                                                     |
| isTouchEnabled         | Boolean | Значение **true** указывает, что сенсорный ввод включен; в противном случае используется значение **false**                                                                                                                                                             |
| reviewerName           | string  | Имя автора отзыва                                                                                                                                                                                                                   |
| helpfulCount           | number  | Количество оценок отзыва как полезного                                                                                                                                                                                   |
| notHelpfulCount        | number  | Количество оценок отзыва как бесполезного                                                                                                                                                                               |
| reviewTitle            | string  | Заголовок отзыва                                                                                                                                                                                                             |
| reviewText             | string  | Текстовое содержимое отзыва                                                                                                                                                                                                     |
| responseText           | string  | Текстовое содержимое ответа                                                                                                                                                                                                   |
| responseDate           | string  | Дата отправки ответа                                                                                                                                                                                                   |
| deviceRAM              | number  | Физическое ОЗУ в МБ                                                                                                                                                                                                             |
| deviceStorageCapacity  | number  | Объем основного запоминающего устройства в ГБ                                                                                                                                                                                     |
| rating                 | number  | Оценка приложения в звездах                                                                                                                                                                                                            |

 

### Пример ответа

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

## Связанные разделы

* [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md)
* [Сведения о покупках приложения](get-app-acquisitions.md)
* [Сведения о покупках IAP](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Сведения об оценках приложения](get-app-ratings.md)



<!--HONumber=Jun16_HO5-->


