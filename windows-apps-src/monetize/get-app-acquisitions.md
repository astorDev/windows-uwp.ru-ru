---
author: mcleanbyron
ms.assetid: C1E42E8B-B97D-4B09-9326-25E968680A0F
description: "Используйте этот метод в API аналитики Магазина Windows для получения сводных данных о покупках приложения в заданном диапазоне дат или с учетом других дополнительных фильтров."
title: "Сведения о покупках приложения"
ms.sourcegitcommit: 02131e641cdaa76256845b38bcc50aa42d718601
ms.openlocfilehash: 7f87f931c92eca1f64fbd23b4fcba3359293f94a

---

# Сведения о покупках приложения


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Используйте этот метод в API аналитики Магазина Windows для получения сводных данных о покупках приложения в заданном диапазоне дат или с учетом других дополнительных фильтров. Этот метод возвращает данные в формате JSON.

## Необходимые условия


Для использования этого метода необходимо:

-   привязать приложение Azure AD, которое вы будете использовать для вызова этого метода, с учетной записью Центра разработки;

-   получить маркер доступа Azure AD для приложения.

Подробнее: [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md).

## Запрос


### Синтаксис запроса

| Метод | URI запроса                                                              |
|--------|--------------------------------------------------------------------------|
| GET    | https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions |

 

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
<td align="left">Код продукта в Магазине для приложения, по которому требуется получить данные о покупках. Код продукта в Магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) информационной панели Центра разработки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8</td>
<td align="left">Да</td>
</tr>
<tr class="even">
<td align="left">startDate</td>
<td align="left">дата</td>
<td align="left">Начальная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата</td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">endDate</td>
<td align="left">date</td>
<td align="left">Конечная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата</td>
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
<td align="left">aggregationLevel</td>
<td align="left">строковый</td>
<td align="left">Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong></td>
<td align="left">Нет</td>
</tr>
<tr class="even">
<td align="left">orderby</td>
<td align="left">string</td>
<td align="left">Оператор, который определяет порядок полученных значений данных для каждой покупки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>. Параметр <em>field</em> может быть одной из следующих строк:
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
<th align="left">Поля</th>
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

В следующем примере демонстрируются несколько запросов на получение информации о покупках приложения. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0  HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US'; and gender eq 'm'  HTTP/1.1
Authorization: Bearer <your access token>
```

## Ответ


### Тело ответа

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                            |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Value      | array  | Массив объектов, содержащий сводную информацию об оценках. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупках](#acquisition-values).                                                                                                                      |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса                                                                                                                                                                                                                             |

 
### Значения в информации о покупках

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                                                                                                                                                                                                                              |
|---------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| date                | string | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId       | строковый | Код продукта в Магазине для приложения, по которому запрашиваются данные о покупках.                                                                                                                                                                 |
| applicationName     | string | Отображаемое имя приложения                                                                                                                                                                                                             |
| deviceType          | string | Тип устройства, на котором совершена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                  |
| orderName           | string | Имя заказа                                                                                                                                                                                                                   |
| storeClient         | string | Версия Магазина, в котором произведена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                            |
| osVersion           | string | Версия ОС, в которой произведена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                   |
| market              | string | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка                                                                                                                                                                  |
| gender              | string | Пол пользователя, совершившего покупку. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                    |
| ageGroup            | string | Возрастная группа пользователя, совершившего покупку. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                 |
| acquisitionType     | string | Тип приобретения (бесплатное, платное и т. д.). Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                    |
| acquisitionQuantity | number | Число покупок, выполненных на указанном уровне агрегирования                                                                                                                                                         |

 

### Пример ответа

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

## Связанные разделы

* [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md)
* [Сведения о покупках IAP](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Сведения об оценках приложения](get-app-ratings.md)
* [Получение отзывов о приложении](get-app-reviews.md)



<!--HONumber=Jun16_HO4-->


