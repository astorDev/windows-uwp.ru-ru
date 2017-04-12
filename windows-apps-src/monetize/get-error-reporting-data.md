---
author: mcleanbyron
ms.assetid: 252C44DF-A2B8-4F4F-9D47-33E423F48584
description: "Используйте этот метод в API аналитики для Магазина Windows для получения сводных данных отчетов об ошибках в заданном диапазоне дат или с учетом других дополнительных фильтров."
title: "Получение данных отчетов об ошибках для вашего приложения"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, службы Магазина, API аналитики для Магазина Windows, ошибки"
ms.openlocfilehash: dc5c4092368c0b395e42be122a9e0e7feec8f03e
ms.sourcegitcommit: 64cfb79fd27b09d49df99e8c9c46792c884593a7
translationtype: HT
---
# <a name="get-error-reporting-data-for-your-app"></a>Получение данных отчетов об ошибках для вашего приложения

Используйте этот метод в API аналитики для Магазина Windows для получения сводных данных отчетов об ошибках для вашего приложения в формате JSON в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в разделе **Ошибки** [Отчета о работоспособности](../publish/health-report.md) на информационной панели Центра разработки для Windows.

Вы можете получить дополнительные сведения об ошибках с помощью методов [получения подробных сведений об ошибке в приложении](get-details-for-an-error-in-your-app.md) и [получения трассировки стека ошибки в приложении](get-the-stack-trace-for-an-error-in-your-app.md).

## <a name="prerequisites"></a>Предварительные условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Магазина Windows.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/failurehits``` |

<span/> 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательное. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/> 

### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | Код продукта в Магазине для приложения, по которому требуется получить данные отчетов об ошибках. Код продукта в Магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) информационной панели Центра разработки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8 |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные отчетов об ошибках. По умолчанию используется текущая дата |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные отчетов об ошибках. По умолчанию используется текущая дата |  Нет  |
| top | целое число | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter |строка  | Один или несколько операторов для фильтрации строк в ответе. Дополнительные сведения см. далее в разделе [фильтрация полей](#filter-fields) | Нет   |
| aggregationLevel | строка | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong>. Если указать <strong>week</strong> или <strong>month</strong>, значения <em>failureName</em> и <em>failureHash</em> будут ограничены 1000 сегментов. | Нет |
| orderby | строка | Оператор, который определяет порядок полученных значений данных. Используется следующий синтаксис: <em>orderby=field [порядк.],field [порядк.],...</em>. Параметр <em>field</em> может быть одной из следующих строк:<ul><li><strong>date,</strong></li><li><strong>failureName,</strong></li><li><strong>failureHash,</strong></li><li><strong>symbol,</strong></li><li><strong>osVersion,</strong></li><li><strong>eventType,</strong></li><li><strong>market,</strong></li><li><strong>deviceType,</strong></li><li><strong>packageName,</strong></li><li><strong>packageVersion.</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | строка | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>failureName,</strong></li><li><strong>failureHash,</strong></li><li><strong>symbol,</strong></li><li><strong>osVersion,</strong></li><li><strong>eventType,</strong></li><li><strong>market,</strong></li><li><strong>deviceType,</strong></li><li><strong>packageName,</strong></li><li><strong>packageVersion.</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date,</strong></li><li><strong>applicationId</strong></li><li><strong>applicationName,</strong></li><li><strong>deviceCount,</strong></li><li><strong>eventCount.</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=failureName,market&amp;aggregationLevel=week</em></p></p> |  Нет  |

<span/>
 
### <a name="filter-fields"></a>Поля фильтра

Параметр *filter* запроса содержит один или несколько операторов, фильтрующих строки в ответе. Каждый оператор содержит поле и значение, которые связаны с операторами выражения **eq** или **ne**; операторы можно комбинировать с помощью **and** или **or**. Несколько примеров параметров *filter*:

-   *filter=market eq 'US' and gender eq 'm';*
-   *filter=(market ne 'US') and (gender ne 'Unknown') and (gender ne 'm') and (market ne 'NO') and (ageGroup ne 'greater than 55' or ageGroup ne ‘less than 13’).*

Список поддерживаемых полей см. в следующей таблице. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки.

| Поля        |  Описание        |
|---------------|-----------------|
| failureName | Имя ошибки |
| failureHash | Уникальный идентификатор ошибки |
| symbol | Символ, назначенный этой ошибке |
| osVersion | Одна из следующих строк:<ul><li><strong>Windows Phone 7.5;</strong></li><li><strong>Windows Phone 8;</strong></li><li><strong>Windows Phone8.1;</strong></li><li><strong>Windows Phone 10;</strong></li><li><strong>Windows 8;</strong></li><li><strong>Windows 8.1;</strong></li><li><strong>Windows10</strong></li><li><strong>Unknown</strong></li></ul> |
| eventType | Одна из следующих строк:<ul><li><strong>crash,</strong></li><li><strong>hang,</strong></li><li><strong>memory.</strong></li><li><strong>jse</strong></li></ul> |
| market | Строка, содержащая код страны по стандарту ISO 3166 для рынка, на котором произошла ошибка. |
| deviceType | Одна из следующих строк:<ul><li><strong>Компьютер</strong></li><li><strong>Телефон</strong></li><li><strong>Консоль</strong></li><li><strong>Интернет вещей</strong></li><li><strong>Holographic</strong></li><li><strong>Неизвестно</strong></li></ul> |
| packageName | Уникальное имя пакета приложения, связанного с этой ошибкой. |
| packageVersion | Версия пакета приложения, связанного с этой ошибкой |

<span/> 

### <a name="request-example"></a>Пример запроса

В следующих примерах показано несколько запросов на получение данных отчетов об ошибках. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/failurehits?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/failurehits?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US' and deviceType eq 'phone' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип    | Описание     |
|------------|---------|--------------|
| Значение      | array   | Массив объектов, содержащий сводные данные отчетов об ошибках. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения ошибок](#error-values).     |
| @nextLink  | string  | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об ошибках |
| TotalCount | inumber | Общее количество строк в результирующих данных для запроса     |

<span/>

### <a name="error-values"></a>Значения ошибок

Элементы в массиве *Value* содержат следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|---------------------|
| date            | строка  | Первая дата в диапазоне дат, для которого требуется получить данные об ошибках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId   | строка  | Код продукта в Магазине для приложения, по которому требуется получить данные об ошибках.   |
| applicationName | строка  | Отображаемое имя приложения.   |
| failureName     | строка  | Имя ошибки  |
| failureHash     | строка  | Уникальный идентификатор ошибки   |
| symbol          | string  | Символ, назначенный этой ошибке |
| osVersion       | строка  | Версия ОС, в которой произошла ошибка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)  |
| eventType       | string  | Тип события ошибки. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)      |
| market          | строка  | Код страны рынка устройства по стандарту ISO 3166   |
| deviceType      | строка  | Тип устройства, на котором произошла ошибка. Список поддерживаемых строк см. выше в разделе [Поля фильтра](#filter-fields)    |
| packageName     | string  | Уникальное имя пакета приложения, связанного с этой ошибкой      |
| packageVersion  | строка  | Версия пакета приложения, связанного с этой ошибкой   |
| eventCount      | inumber | Количество событий, которые вызваны этой ошибкой, на указанном уровне агрегирования      |
| deviceCount     | inumber | Количество уникальных устройства, соответствующих этой ошибке, на указанном уровне агрегирования  |

<span/> 

### <a name="response-example"></a>Пример ответа

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

## <a name="related-topics"></a>Статьи по теме

* [Отчет о работоспособности](../publish/health-report.md)
* [Получение сведений об ошибке в приложении](get-details-for-an-error-in-your-app.md)
* [Получение трассировки стека при возникновении ошибки в приложении](get-the-stack-trace-for-an-error-in-your-app.md)
* [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md)
* [Получение сведений о покупках приложения](get-app-acquisitions.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
* [Получение сведений об оценках приложения](get-app-ratings.md)
* [Получение отзывов о приложении](get-app-reviews.md)
