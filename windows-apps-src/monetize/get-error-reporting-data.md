---
author: mcleanbyron
ms.assetid: 252C44DF-A2B8-4F4F-9D47-33E423F48584
description: "Используйте этот метод в API аналитики для Магазина Windows для получения сводных данных отчетов об ошибках в заданном диапазоне дат или с учетом других дополнительных фильтров."
title: "Получение данных отчетов об ошибках"
translationtype: Human Translation
ms.sourcegitcommit: 7b73682ea36574f8b675193a174d6e4b4ef85841
ms.openlocfilehash: 89b1c9b44aaabb49f78953877ae11d2d7a0a2a2f

---

# Получение данных отчетов об ошибках

Используйте этот метод в API аналитики для Магазина Windows для получения сводных данных отчетов об ошибках в формате JSON в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [отчете о работоспособности](../publish/health-report.md) на информационной панели Центра разработки для Windows.

## Необходимые условия


Для использования этого метода необходимо выполнить следующие действия:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Магазина Windows.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## Запрос


### Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/failurehits``` |

<span/> 

### Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

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
<td align="left">string</td>
<td align="left">Код продукта в Магазине для приложения, по которому требуется получить данные отчетов об ошибках. Код продукта в Магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) информационной панели Центра разработки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8</td>
<td align="left">Да</td>
</tr>
<tr class="even">
<td align="left">startDate</td>
<td align="left">date</td>
<td align="left">Начальная дата диапазона дат, для которого требуется получить данные отчетов об ошибках. По умолчанию используется текущая дата</td>
<td align="left">Нет</td>
</tr>
<tr class="odd">
<td align="left">endDate</td>
<td align="left">date</td>
<td align="left">Конечная дата диапазона дат, для которого требуется получить данные отчетов об ошибках. По умолчанию используется текущая дата</td>
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
<td align="left">string</td>
<td align="left">Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong>. Если указать <strong>week</strong> или <strong>month</strong>, значения <em>failureName</em> и <em>failureHash</em> будут ограничены 1000 сегментов.</td>
<td align="left">Нет</td>
</tr>
<tr class="even">
<td align="left">groupby</td>
<td align="left">string</td>
<td align="left">Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:
<ul>
<li><strong>failureName,</strong></li>
<li><strong>failureHash,</strong></li>
<li><strong>symbol,</strong></li>
<li><strong>osVersion,</strong></li>
<li><strong>eventType,</strong></li>
<li><strong>market,</strong></li>
<li><strong>deviceType,</strong></li>
<li><strong>packageName,</strong></li>
<li><strong>packageVersion.</strong></li>
</ul>
<p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p>
<ul>
<li><strong>date,</strong></li>
<li><strong>applicationId,</strong></li>
<li><strong>applicationName,</strong></li>
<li><strong>deviceCount,</strong></li>
<li><strong>eventCount.</strong></li>
</ul>
<p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=failureName,market&amp;aggregationLevel=week</em></p></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">orderby</td>
<td align="left">string</td>
<td align="left">Оператор, который определяет порядок полученных значений данных для каждой покупки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>. Параметр <em>field</em> может быть одной из следующих строк:
<ul>
<li><strong>date,</strong></li>
<li><strong>failureName,</strong></li>
<li><strong>failureHash,</strong></li>
<li><strong>symbol,</strong></li>
<li><strong>osVersion,</strong></li>
<li><strong>eventType,</strong></li>
<li><strong>market,</strong></li>
<li><strong>deviceType,</strong></li>
<li><strong>packageName,</strong></li>
<li><strong>packageVersion.</strong></li>
</ul>
<p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p>
<p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p></td>
<td align="left">Нет</td>
</tr>
</tbody>
</table>

<span/>
 
### Поля фильтра

Параметр *filter* запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**; операторы можно комбинировать с помощью **and** или **or**. Несколько примеров параметров *filter*:

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
<td align="left">failureName</td>
<td align="left">Имя ошибки</td>
</tr>
<tr class="even">
<td align="left">failureHash</td>
<td align="left">Уникальный идентификатор ошибки</td>
</tr>
<tr class="odd">
<td align="left">symbol</td>
<td align="left">Символ, назначенный этой ошибке</td>
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
<td align="left">eventType</td>
<td align="left">Одна из следующих строк:
<ul>
<li><strong>crash,</strong></li>
<li><strong>hang,</strong></li>
<li><strong>memory.</strong></li>
<li><strong>jse</strong></li>
</ul></td>
</tr>
<tr class="even">
<td align="left">market</td>
<td align="left">Строка, которая содержит код страны рынка устройства по стандарту ISO 3166</td>
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
<td align="left">packageName</td>
<td align="left">Уникальное имя пакета приложения, связанного с этой ошибкой</td>
</tr>
<tr class="odd">
<td align="left">packageVersion</td>
<td align="left">Версия пакета приложения, связанного с этой ошибкой</td>
</tr>
</tbody>
</table>

<span/> 

### Пример запроса

В следующих примерах показано несколько запросов на получение данных отчетов об ошибках. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/failurehits?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/failurehits?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US' and deviceType eq 'phone' HTTP/1.1
Authorization: Bearer <your access token>
```

## Ответ


### Тело ответа

| Значение      | Тип    | Описание                                                                                                                                                                                                                                                                    |
|------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Value      | array   | Массив объектов, содержащий сводные данные отчетов об ошибках. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения ошибок](#error-values)                                                                                                          |
| @nextLink  | string  | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об ошибках |
| TotalCount | inumber | Общее количество строк в результирующих данных для запроса                                                                                                                                                                                                                     |

<span/>

### Значения ошибок

Элементы в массиве *Value* содержат следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                              |
|-----------------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| date            | string  | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId   | string  | Код продукта в Магазине для приложения, по которому требуется получить данные о покупках надстройки.                                                                                                                                                           |
| applicationName | string  | Отображаемое имя приложения                                                                                                                                                                                                             |
| failureName     | string  | Имя ошибки                                                                                                                                                                                                                 |
| failureHash     | string  | Уникальный идентификатор ошибки                                                                                                                                                                                                   |
| symbol          | string  | Символ, назначенный этой ошибке                                                                                                                                                                                                       |
| osVersion       | string  | Версия ОС, в которой произошла ошибка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                       |
| eventType       | string  | Тип события ошибки. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                                          |
| market          | string  | Код страны рынка устройства по стандарту ISO 3166                                                                                                                                                                                          |
| deviceType      | string  | Тип устройства, на котором совершена покупка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)                                                                                                  |
| packageName     | string  | Уникальное имя пакета приложения, связанного с этой ошибкой                                                                                                                                                                 |
| packageVersion  | string  | Версия пакета приложения, связанного с этой ошибкой                                                                                                                                                                     |
| eventCount      | inumber | Количество событий, которые вызваны этой ошибкой, на указанном уровне агрегирования                                                                                                                                            |
| deviceCount     | inumber | Количество уникальных устройства, соответствующих этой ошибке, на указанном уровне агрегирования                                                                                                                                        |

<span/> 

### Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2015-03-09",
      "applicationId": "9NBLGGGZ5QDR",
      "applicationName": "Contoso Demo",
      "failureName": "APPLICATION_FAULT_8013150a_StoreWrapper.ni.DLL!70475e55",
      "failureHash": "5a6b2170-1661-ed47-24d7-230fed0077af",
      "symbol": "storewrapper_ni!70475e55",
      "osVersion": "Windows Phone 8",
      "eventType": "crash",
      "market": "US",
      "deviceType": "mobile",
      "packageName": "",
      "packageVersion": "0.0.0.0",
      "deviceCount": 0.0,
      "eventCount": 1.0
    }
  ],
  "@nextLink": "failurehits?applicationId=9NBLGGGZ5QDR&aggregationLevel=week&startDate=2015/03/01&endDate=2016/02/01&top=1&skip=1",
  "TotalCount": 191753
}

```

## Связанные статьи

* [Отчет о работоспособности](../publish/health-report.md)
* [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md)
* [Получение сведений о покупках приложения](get-app-acquisitions.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
* [Получение сведений об оценках приложения](get-app-ratings.md)
* [Получение отзывов о приложении](get-app-reviews.md)



<!--HONumber=Nov16_HO1-->


