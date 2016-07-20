---
author: mcleanbyron
ms.assetid: DD4F6BC4-67CD-4AEF-9444-F184353B0072
description: "Используйте этот метод в API аналитики Магазина Windows для получения сводных данных об оценках приложения в заданном диапазоне дат или с учетом других дополнительных фильтров."
title: "Сведения об оценках приложения"
translationtype: Human Translation
ms.sourcegitcommit: f7e67a4ff6cb900fb90c5d5643e2ddc46cbe4dd2
ms.openlocfilehash: 6f6a94e030f1733ca4224766526386ef1956ff03

---

# Сведения об оценках приложения


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Используйте этот метод в API аналитики Магазина Windows для получения сводных данных об оценках приложения в заданном диапазоне дат или с учетом других дополнительных фильтров. Этот метод возвращает данные в формате JSON.

## Необходимые условия


Для использования этого метода необходимо:

-   привязать приложение Azure AD, которое вы будете использовать для вызова этого метода, с учетной записью Центра разработки;

-   получить маркер доступа Azure AD для приложения.

Более подробную информацию см. в разделе [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md).

## Запрос


### Синтаксис запроса

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/ratings``` |

 

### Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **Bearer**&lt;*token*&gt;. |

<span/> 

### Параметры запроса

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
<th align="left">Обязательный</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">applicationId</td>
<td align="left">Строка</td>
<td align="left">Код продукта в Магазине для приложения, по которому требуется получить данные об оценках. Код продукта в Магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) информационной панели Центра разработки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8</td>
<td align="left">Да</td>
</tr>
<tr class="even">
<td align="left">startDate</td>
<td align="left">дата</td>
<td align="left">Начальная дата диапазона дат, для которого требуется получить данные об оценках. По умолчанию используется текущая дата</td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">endDate</td>
<td align="left">date</td>
<td align="left">Конечная дата диапазона дат, для которого требуется получить данные об оценках. По умолчанию используется текущая дата</td>
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
<td align="left">Строка</td>
<td align="left">Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields)</td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">aggregationLevel</td>
<td align="left">Строка</td>
<td align="left">Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong></td>
<td align="left">Нет</td>
</tr>
<tr class="even">
<td align="left">orderby</td>
<td align="left">Строка</td>
<td align="left">Оператор, который определяет порядок полученных значений данных для каждой оценки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>. Параметр <em>field</em> может быть одной из следующих строк:
<ul>
<li><strong>date,</strong></li>
<li><strong>osVersion,</strong></li>
<li><strong>market,</strong></li>
<li><strong>deviceType,</strong></li>
<li><strong>isRevised.</strong></li>
</ul>
<p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p>
<p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p></td>
<td align="left">Нет</td>
</tr>
</tbody>
</table>

<span/>
 
### Поля фильтра

Параметр *filter* запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**; операторы можно комбинировать с помощью **and** или **or**.

Пример строки *filter*: *filter=market eq 'US' and deviceType eq 'phone' and isRevised eq true*.

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
<td align="left">market</td>
<td align="left">Строка, которая содержит код страны рынка устройства по стандарту ISO 3166</td>
</tr>
<tr class="even">
<td align="left">osVersion</td>
<td align="left">Одна из следующих строк:
<ul>
<li><strong>Windows Phone 7.5;</strong></li>
<li><strong>Windows Phone 8;</strong></li>
<li><strong>Windows Phone8.1;</strong></li>
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
<td align="left">isRevised</td>
<td align="left">Если нужно отфильтровать пересмотренные оценки, укажите значение <strong>true</strong>, в противном случае — <strong>false</strong></td>
</tr>
</tbody>
</table>

<span/> 

### Пример запроса

Ниже приведено несколько примеров запросов для получения данных об оценках. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/ratings?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/ratings?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US' and deviceType eq 'phone' HTTP/1.1
Authorization: Bearer <your access token>
```

## Ответ


### Тело ответа

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                            |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Value      | array  | Массив объектов, содержащий сводную информацию об оценках. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения оценок](#rating-values).                                                                                                                           |
| @nextLink  | Строка | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса                                                                                                                                                                                                                             |

<span/>

### Значения оценок

Элементы в массиве *Value* содержат следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| date            | Строка  | Первая дата в диапазоне дат, для которого требуется получить данные об оценках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId   | Строка  | Код продукта в Магазине для приложения, по которому запрашиваются данные об оценках.                                                                                                                                                                 |
| applicationName | Строка  | Отображаемое имя приложения                                                                                                                                                                                                         |
| market          | Строка  | Код страны рынка, на котором была поставлена оценка, по стандарту ISO 3166                                                                                                                                                              |
| osVersion       | Строка  | Версия ОС, в которой была поставлена оценка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                               |
| deviceType      | Строка  | Тип устройства, на котором была поставлена оценка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                           |
| isRevised       | Boolean | Значение **true** указывает, что оценка была пересмотрена; в противном случае используется значение **false**                                                                                                                                                       |
| oneStar         | number  | Количество оценок «одна звезда»                                                                                                                                                                                                      |
| twoStars        | number  | Количество оценок «две звезды»                                                                                                                                                                                                      |
| threeStars      | number  | Количество оценок «три звезды»                                                                                                                                                                                                    |
| fourStars       | number  | Количество оценок «четыре звезды»                                                                                                                                                                                                     |
| fiveStars       | number  | Количество оценок «пять звезд»                                                                                                                                                                                                     |
 
<span/>

### Пример ответа

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

## Связанные разделы

* [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md)
* [Сведения о покупках приложения](get-app-acquisitions.md)
* [Сведения о покупках IAP](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Получение отзывов о приложении](get-app-reviews.md)



<!--HONumber=Jul16_HO1-->


