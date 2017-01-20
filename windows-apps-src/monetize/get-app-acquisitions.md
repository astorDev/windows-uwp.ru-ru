---
author: mcleanbyron
ms.assetid: C1E42E8B-B97D-4B09-9326-25E968680A0F
description: "Используйте этот метод в API аналитики для Магазина Windows для получения сводных данных о покупках приложения в заданном диапазоне дат или с учетом других дополнительных фильтров."
title: "Получение сведений о покупках приложения"
translationtype: Human Translation
ms.sourcegitcommit: ce6535508136f8f7bfc8e0da599eea10a936908d
ms.openlocfilehash: 2e35da3b3033ecbad8c48184d831ca9465142322

---

# <a name="get-app-acquisitions"></a>Получение сведений о покупках приложения


Используйте этот метод в API аналитики Магазина Windows для получения сводных данных в формате JSON для приложения в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [отчете о приобретениях](../publish/acquisitions-report.md) на информационной панели Центра разработки для Windows.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода необходимо выполнить следующие действия:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Магазина Windows.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions``` |

<span/>

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/> 

### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | Код продукта в Магазине для приложения, по которому требуется получить данные о покупках. Код продукта в Магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) информационной панели Центра разработки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8 |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | string  | Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields) | Нет   |
| aggregationLevel | string | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong> | Нет |
| orderby | string | Оператор, который определяет порядок полученных значений данных для каждой покупки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>. Параметр <em>field</em> может быть одной из следующих строк:<ul><li><strong>date,</strong></li><li><strong>acquisitionType,</strong></li><li><strong>ageGroup,</strong></li><li><strong>storeClient,</strong></li><li><strong>gender,</strong></li><li><strong>market,</strong></li><li><strong>osVersion,</strong></li><li><strong>deviceType,</strong></li><li><strong>orderName.</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | строка | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>date</strong></li><li><strong>applicationName</strong></li><li><strong>acquisitionType</strong></li><li><strong>ageGroup</strong></li><li><strong>storeClient</strong></li><li><strong>gender</strong></li><li><strong>market</strong></li><li><strong>osVersion</strong></li><li><strong>deviceType</strong></li><li><strong>orderName</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date</strong></li><li><strong>applicationId</strong></li><li><strong>acquisitionQuantity</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=ageGroup,market&amp;aggregationLevel=week</em></p> |  Нет  |

<span/>
 
### <a name="filter-fields"></a>Поля фильтра

Параметр *filter* запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**; операторы можно комбинировать с помощью **and** или **or**. Несколько примеров параметров *filter*:

-   *filter=market eq 'US' and gender eq 'm';*
-   *filter=(market ne 'US') and (gender ne 'Unknown') and (gender ne 'm') and (market ne 'NO') and (ageGroup ne 'greater than 55' or ageGroup ne ‘less than 13’).*

Список поддерживаемых полей см. в следующей таблице. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки.

| Поля        |  Описание        |
|---------------|-----------------|
| acquisitionType | Одна из следующих строк:<ul><li><strong>free,</strong></li><li><strong>trial,</strong></li><li><strong>paid,</strong></li><li><strong>promotional code,</strong></li><li><strong>iap</strong></li></ul> |
| ageGroup | Одна из следующих строк:<ul><li><strong>less than 13,</strong></li><li><strong>13-17,</strong></li><li><strong>18-24,</strong></li><li><strong>25-34,</strong></li><li><strong>35-44,</strong></li><li><strong>44-55,</strong></li><li><strong>greater than 55,</strong></li><li><strong>Unknown</strong></li></ul> |
| storeClient | Одна из следующих строк:<ul><li><strong>Windows Phone Store (client);</strong></li><li><strong>Windows Store (client);</strong></li><li><strong>Windows Store (web);</strong></li><li><strong>Volume purchase by organizations;</strong></li><li><strong>Other</strong></li></ul> |
| gender | Одна из следующих строк:<ul><li><strong>m,</strong></li><li><strong>f,</strong></li><li><strong>Unknown</strong></li></ul> |
| market | Строка, содержащая код страны по стандарту ISO 3166 для рынка, на котором произошла покупка |
| osVersion | Одна из следующих строк:<ul><li><strong>Windows Phone 7.5;</strong></li><li><strong>Windows Phone 8;</strong></li><li><strong>Windows Phone 8.1;</strong></li><li><strong>Windows Phone 10;</strong></li><li><strong>Windows 8;</strong></li><li><strong>Windows 8.1;</strong></li><li><strong>Windows 10</strong></li><li><strong>Неизвестно</strong></li></ul> |
| deviceType | Одна из следующих строк:<ul><li><strong>Компьютер</strong></li><li><strong>Телефон</strong></li><li><strong>Консоль</strong></li><li><strong>Интернет вещей</strong></li><li><strong>Holographic</strong></li><li><strong>Неизвестно</strong></li></ul> |
| orderName | Строка, содержащая имя заказа для рекламного кода, который использовался для приобретения приложения (применимо только в том случае, если пользователь приобрел приложение, активировав рекламный код) |

<span/> 

### <a name="request-example"></a>Пример запроса

В следующем примере демонстрируются несколько запросов на получение информации о покупках приложения. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0  HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US'; and gender eq 'm'  HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Value      | array  | Массив объектов, содержащий сводную информацию об оценках. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупках](#acquisition-values).                                                                                                                      |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса                                                                                                                                                                                                                             |

<span/>
 
### <a name="acquisition-values"></a>Значения в информации о покупках

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| date                | string | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId       | string | Код продукта в Магазине для приложения, по которому запрашиваются данные о покупках.                                                                                                                                                                 |
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

<span/> 

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
* [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Получение сведений об оценках приложения](get-app-ratings.md)
* [Получение отзывов о приложении](get-app-reviews.md)



<!--HONumber=Dec16_HO2-->


