---
author: mcleanbyron
ms.assetid: 1599605B-4243-4081-8D14-40F6F7734E25
description: Используйте этот метод в API аналитики Магазина Windows для получения сводных данных о внутренних продуктах приложения (IAP) в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Сведения о покупках IAP
---

# Сведения о покупках IAP


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Используйте этот метод в API аналитики Магазина Windows для получения сводных данных о внутренних продуктах приложения (IAP) в заданном диапазоне дат или с учетом других дополнительных фильтров. Этот метод возвращает данные в формате JSON.

## Необходимые условия


Для использования этого метода необходимо:

-   привязать приложение Azure AD, которое вы будете использовать для вызова этого метода, с учетной записью Центра разработки;

-   получить маркер доступа Azure AD для приложения.

Подробнее: [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md).

## Запрос


### Синтаксис запроса

| Метод | URI запроса                                                                |
|--------|----------------------------------------------------------------------------|
| GET    | https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions |

 

### Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строковый | Обязательное. 1Маркер доступа Azure AD в форме**Bearer**&lt;*token*&gt;. |

 

### Тело запроса

Параметр *applicationId* или *inAppProductId* обязателен. Для извлечения данных о покупках всех IAP, зарегистрированных в приложении, укажите параметр *applicationId*. Для извлечения данных о покупках конкретного IAP укажите параметр *inAppProductId*. Если указать оба, параметр *inAppProductId* игнорируется.

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
<th align="left">Обязательный параметр</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">applicationId</td>
<td align="left">string</td>
<td align="left">Код продукта приложения, для которого требуется получить данные о покупках IAP Код продукта внедрен в ссылку на страницу приложения. Эта ссылка находится на странице [Страница идентификации приложения](https://msdn.microsoft.com/library/windows/apps/mt148561) информационной панели Центра разработки. Пример кода продукта: 9WZDNCRFJ3Q8</td>
<td align="left">Да</td>
</tr>
<tr class="even">
<td align="left">inAppProductId</td>
<td align="left">string</td>
<td align="left">Код продукта IAP, для которого требуется получить данные о покупках</td>
<td align="left">Да</td>
</tr>
<tr class="odd">
<td align="left">startDate</td>
<td align="left">date</td>
<td align="left">Начальная дата диапазона дат, для которого требуется получить данные о покупках IAP. По умолчанию используется текущая дата</td>
<td align="left">Нет</td>
</tr>
<tr class="even">
<td align="left">endDate</td>
<td align="left">date</td>
<td align="left">Конечная дата диапазона дат, для которого требуется получить данные о покупках IAP. По умолчанию используется текущая дата</td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">top</td>
<td align="left">int</td>
<td align="left">Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных</td>
<td align="left">Нет</td>
</tr>
<tr class="even">
<td align="left">skip</td>
<td align="left">int</td>
<td align="left">Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д.</td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">filter</td>
<td align="left">string</td>
<td align="left">Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields)</td>
<td align="left">Нет</td>
</tr>
<tr class="even">
<td align="left">aggregationLevel</td>
<td align="left">строковый</td>
<td align="left">Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong></td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">orderby</td>
<td align="left">string</td>
<td align="left">Оператор, который определяет порядок полученных значений данных для каждой покупки IAP. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>. Параметр <em>field</em> может быть одной из следующих строк:
<ul>
<li><strong>date,</strong></li>
<li><strong>acquisitionType,</strong></li>
<li><strong>ageGroup,</strong></li>
<li><strong>storeClient,</strong></li>
<li><strong>gender,</strong></li>
<li><strong>market,</strong></li>
<li><strong>osVersion,</strong></li>
<li><strong>deviceType,</strong></li>
<li><strong>orderName.</strong></li>
</ul>
<p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p>
<p>Пример: <em>orderby</em> string: <em>orderby=date,market</em></p></td>
<td align="left">Нет</td>
</tr>
</tbody>
</table>

 

### Поля фильтра

Параметр *filter* в теле запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**; операторы можно комбинировать с помощью **and** или **or**. Несколько примеров параметров *filter*:

-   *filter=market eq 'US' and gender eq 'm';*
-   *filter=(market ne 'US') and (gender ne 'Unknown') and (gender ne 'm') and (market ne 'NO') and (ageGroup ne 'greater than 55' or ageGroup ne ‘less than 13’).*

Список поддерживаемых полей см. в следующей таблице. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Поле</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">acquisitionType</td>
<td align="left">Одна из следующих строк:
<ul>
<li><strong>free,</strong></li>
<li><strong>trial,</strong></li>
<li><strong>paid,</strong></li>
<li><strong>promotional code,</strong></li>
<li><strong>iap</strong></li>
</ul></td>
</tr>
<tr class="even">
<td align="left">ageGroup</td>
<td align="left">Одна из следующих строк:
<ul>
<li><strong>less than 13,</strong></li>
<li><strong>13-17,</strong></li>
<li><strong>18-24,</strong></li>
<li><strong>25-34,</strong></li>
<li><strong>35-44,</strong></li>
<li><strong>44-55,</strong></li>
<li><strong>greater than 55,</strong></li>
<li><strong>Unknown</strong></li>
</ul></td>
</tr>
<tr class="odd">
<td align="left">storeClient</td>
<td align="left">Одна из следующих строк:
<ul>
<li><strong>Windows Phone Store (client);</strong></li>
<li><strong>Windows Store (client);</strong></li>
<li><strong>Windows Store (web);</strong></li>
<li><strong>Volume purchase by organizations;</strong></li>
<li><strong>Other</strong></li>
</ul></td>
</tr>
<tr class="even">
<td align="left">gender</td>
<td align="left">Одна из следующих строк:
<ul>
<li><strong>m,</strong></li>
<li><strong>f,</strong></li>
<li><strong>Unknown</strong></li>
</ul></td>
</tr>
<tr class="odd">
<td align="left">market</td>
<td align="left">Строка, содержащая код страны по стандарту ISO 3166 для рынка, на котором произошла покупка</td>
</tr>
<tr class="even">
<td align="left">osVersion</td>
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
<td align="left">orderName</td>
<td align="left">Строка, содержащая имя заказа для рекламного кода, который использовался для приобретения приложения (применимо только в том случае, если пользователь приобрел приложение, активировав рекламный код)</td>
</tr>
</tbody>
</table>

 

### Пример запроса

В следующих примерах демонстрируются несколько запросов на получение информации о покупках IAP. Замените значения *inAppProductId* или *applicationId* на соответствующие коды продукта для вашего приложения или IAP.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?inAppProductId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?inAppProductId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=7/3/2015&top=100&skip=0&filter=market ne 'US' and gender ne 'Unknown' and gender ne 'm' and market ne 'NO' and ageGroup ne '>55' HTTP/1.1
Authorization: Bearer <your access token>
```

## Ответ


### Тело ответа

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                                |
|------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Value      | array  | Массив объектов, содержащий сводную информацию о покупках IAP. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупках IAP](#iap-acquisition-values).                                                                                                              |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках IAP |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса                                                                                                                                                                                                                                 |


### Значения в информации о покупках IAP

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип    | Описание                                                                                                                                                                                                                              |
|---------------------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| date                | string  | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| inAppProductId      | string  | Код продукта IAP, для которого запрашиваются данные о покупках                                                                                                                                                                 |
| inAppProductName    | string  | Отображаемое имя IAP                                                                                                                                                                                                             |
| applicationId       | string  | Код продукта приложения, для которого требуется получить данные о покупках IAP                                                                                                                                                           |
| applicationName     | string  | Отображаемое имя приложения                                                                                                                                                                                                             |
| deviceType          | string  | Тип устройства, на котором совершена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                  |
| orderName           | string  | Имя заказа                                                                                                                                                                                                                   |
| storeClient         | string  | Версия Магазина, в котором произведена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                            |
| osVersion           | string  | Версия ОС, в которой произведена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                   |
| market              | string  | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка                                                                                                                                                                  |
| gender              | string  | Пол пользователя, совершившего покупку. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                    |
| ageGroup            | string  | Возрастная группа пользователя, совершившего покупку. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                 |
| acquisitionType     | string  | Тип приобретения (бесплатное, платное и т. д.). Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                    |
| acquisitionQuantity | inumber | Количество покупок                                                                                                                                                                                                |

 

### Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2015-01-02",
      "inAppProductId": "9NBLGGH3LHKL",
      "inAppProductName": "Contoso IAP 7",
      "applicationId": "9NBLGGGZ5QDR",
      "applicationName": "Contoso Demo",
      "deviceType": "Phone",
      "orderName": "",
      "storeClient": "Windows Phone Store (client)",
      "osVersion": "Windows Phone 8.1",
      "market": "GB",
      "gender": "m",
      "ageGroup": "50orover",
      "acquisitionType": "Iap",
      "acquisitionQuantity": 1
    }
  ],
  "@nextLink": "inappacquisitions?applicationId=9NBLGGGZ5QDR&inAppProductId=&aggregationLevel=day&startDate=2015/01/01&endDate=2016/02/01&top=1&skip=1",
  "TotalCount": 33677
}
```

## Связанные разделы

* [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md)
* [Сведения о покупках приложения](get-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Сведения об оценках приложения](get-app-ratings.md)
* [Получение отзывов о приложении](get-app-reviews.md)

 

 


<!--HONumber=May16_HO2-->


