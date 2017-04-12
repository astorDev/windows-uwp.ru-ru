---
author: mcleanbyron
ms.assetid: FAD033C7-F887-4217-A385-089F09242827
description: "Используйте этот метод в API аналитики для Магазина Windows для получения сводных данных об установках приложения в заданном диапазоне дат или с учетом других дополнительных фильтров."
title: "Получение сведений об установках приложения"
ms.author: mcleans
ms.date: 03/17/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, службы Магазина, API аналитики для Магазина Windows, установки приложения"
ms.openlocfilehash: a631dc23e6c0fb07e93a8658e0f266fcb289a167
ms.sourcegitcommit: 64cfb79fd27b09d49df99e8c9c46792c884593a7
translationtype: HT
---
# <a name="get-app-installs"></a>Получение сведений об установках приложения


Используйте этот метод в API аналитики для Магазина Windows, чтобы получить сводные данные об установках приложения в формате JSON в заданном диапазоне дат или с учетом других дополнительных фильтров. Эта информация также доступна в [отчете "Установки"](../publish/installs-report.md) на информационной панели Центра разработки для Windows.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Магазина Windows.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/installs``` |

<span/>

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательное. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/> 

### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | Код продукта в Магазине для приложения, по которому требуется получить данные об установках. Код продукта в Магазине доступен на [странице "Удостоверение приложения"](../publish/view-app-identity-details.md) информационной панели Центра разработки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8 |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные об установках. По умолчанию используется текущая дата. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные об установках. По умолчанию используется текущая дата. |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>market,</strong></li><li><strong>osVersion,</strong></li><li><strong>deviceType,</strong></li><li><strong>packageVersion.</strong></li></ul> | Нет   |
| aggregationLevel | string | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong> | Нет |
| orderby | string | Выражение, которое определяет порядок полученных значений данных для каждой установки. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>. Параметр <em>field</em> может быть одним из следующих полей из тела ответа:<p/><ul><li><strong>applicationName,</strong></li><li><strong>date,</strong><li><strong>deviceType,</strong></li><li><strong>market,</strong></li><li><strong>osVersion,</strong></li><li><strong>packageVersion,</strong></li><li><strong>successfulInstallCount.</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | string | Выражение, которое применяет агрегирование данных только к указанным полям. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>applicationName,</strong></li><li><strong>date,</strong><li><strong>deviceType,</strong></li><li><strong>market,</strong></li><li><strong>osVersion,</strong></li><li><strong>packageVersion.</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>date,</strong></li><li><strong>applicationId,</strong></li><li><strong>successfulInstallCount.</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=ageGroup,market&amp;aggregationLevel=week</em></p> |  Нет  |

<span/>
 
### <a name="request-example"></a>Пример запроса

В следующем примере демонстрируются несколько запросов на получение информации об установках приложения. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/installs?applicationId=9NBLGGGZ5QDR&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/installs?applicationId=9NBLGGGZ5QDR&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US' and deviceType eq 'PC' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Value      | array  | Массив объектов, содержащий сводную информацию об установках. Дополнительные сведения о данных в каждом объекте см. в следующей таблице.                                                                                                                      |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об установках. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.                                                                                                                                                                                                                             
<span/>

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| date                | string | Первая дата в диапазоне дат, для которого требуется получить данные об установках. Если в запросе указан один день, это значение равно дате, соответствующей тому дню. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId       | string | Код продукта в Магазине для приложения, по которому запрашиваются данные об установках.     |
| applicationName     | string | Отображаемое имя приложения.     |
| deviceType          | string | Одна из следующих строк, указывающая тип устройства, на которое была выполнена установки:<p/><ul><li><strong>PC (компьютер),</strong></li><li><strong>Phone (телефон),</strong></li><li><strong>Console (консоль),</strong></li><li><strong>IoT (Интернет вещей),</strong></li><li><strong>Holographic</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>  |
| packageVersion           | string | Версия установленного пакета.  |
| osVersion           | string | Одна из следующих строк, указывающая версию ОС, в которую произведена установка:<p/><ul><li><strong>Windows Phone 7.5;</strong></li><li><strong>Windows Phone 8;</strong></li><li><strong>Windows Phone8.1;</strong></li><li><strong>Windows Phone 10;</strong></li><li><strong>Windows 8;</strong></li><li><strong>Windows 8.1;</strong></li><li><strong>Windows10;</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>   |
| market              | string | Код страны по стандарту ISO 3166 для рынка, на котором произошла установка.    |
| successfulInstallCount | number | Число успешных установок, выполненных на указанном уровне агрегирования.     |

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
      "packageVersion": "1.0.0.0",
      "deviceType": "Phone",
      "market": "IT",
      "osVersion": "Windows Phone 8.1",
      "successfulInstallCount": 1
    }
  ],
  "@nextLink": "installs?applicationId=9NBLGGGZ5QDR&aggregationLevel=day&startDate=2015/01/01&endDate=2016/02/01&top=1&skip=1&orderby=date desc",
  "TotalCount":23012
}
```

## <a name="related-topics"></a>Связанные разделы

* [Отчет "Установки"](../publish/installs-report.md)
* [Доступ к аналитическим данным с помощью служб Магазина Windows](access-analytics-data-using-windows-store-services.md)
