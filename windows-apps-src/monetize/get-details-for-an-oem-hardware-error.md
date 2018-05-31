---
author: mcleanbyron
ms.assetid: 8425F704-8A03-493F-A3D2-8442E85FD835
description: Используйте этот метод в API аналитики для Microsoft Store, чтобы получить подробные данные об определенной ошибке оборудования. Этот метод предназначен только для изготовителей оборудования.
title: Получение сведений об ошибке оборудования OEM
ms.author: mcleans
ms.date: 01/18/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, службы Store, API аналитики для Microsoft Store, ошибки, подробные сведения
ms.localizationpriority: medium
ms.openlocfilehash: 429ebc5237ce35baa6f9c3f31a25d480410d9c86
ms.sourcegitcommit: 1773bec0f46906d7b4d71451ba03f47017a87fec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
ms.locfileid: "1663214"
---
# <a name="get-details-for-an-oem-hardware-error"></a>Получение сведений об ошибке оборудования OEM

Используйте этот метод в API аналитики для Microsoft Store, чтобы получить подробные данные об определенной ошибке в оборудовании OEM в формате JSON. Перед использованием этого метода вам потребуется воспользоваться методом [получения данных системы отчетов об ошибках оборудования OEM](get-oem-hardware-error-reporting-data.md), чтобы получить идентификатор ошибки, для которой требуется получить подробные сведения.

> [!NOTE]
> Этот метод может использоваться только в учетных записях разработчиков, связанных с [программой Центра разработки оборудования для Windows](https://msdn.microsoft.com/windows/hardware/drivers/dashboard/get-started-with-the-hardware-dashboard).

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия токена можно получить новый токен.
* Получите идентификатор ошибки, для которой требуется получить подробные сведения. Чтобы получить этот идентификатор, используйте метод [получения данных системы отчетов об ошибках оборудования OEM](get-oem-hardware-error-reporting-data.md) и значение **failureHash** в тексте ответа этого метода.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/hardware/failuredetails``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательное. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| failureHash | string | Уникальный идентификатор ошибки, для которой требуется получить подробные сведения. Чтобы получить это значение для интересующей вас ошибки, используйте метод [получения данных системы отчетов об ошибках оборудования OEM](get-oem-hardware-error-reporting-data.md) и значение **failureHash** в тексте ответа этого метода. |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить подробные данные об ошибках. По умолчанию используется текущая дата минус 30 дней. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить подробные данные об ошибках. По умолчанию используется текущая дата. |  Нет  |
| top | целое число | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10 и skip=0 извлекаются первые 10 строк данных; при top=10 и skip=10 извлекаются следующие 10 строк данных и т. д. |  Нет  |
| filter |строка  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Можно указать следующие поля:<p/><ul><li><strong>failureName,</strong></li><li><strong>failureHash,</strong></li><li><strong>symbol,</strong></li><li><strong>osVersion,</strong></li><li><strong>market,</strong></li><li><strong>deviceType,</strong></li><li><strong>moduleName,</strong></li><li><strong>moduleVersion,</strong></li><li><strong>mode,</strong></li><li><strong>architecture,</strong></li><li><strong>model,</strong></li><li><strong>baseboard,</strong></li><li><strong>modelFamily,</strong></li><li><strong>flightRing,</strong></li><li><strong>cabIdHash,</strong></li><li><strong>cabType,</strong></li><li><strong>cabExpirationTime.</strong></li></ul> | Нет   |
| orderby | string | Выражение, которое определяет порядок полученных значений данных. Используется следующий синтаксис: <em>orderby=field [order],field [order],...,</em>. Можно задать следующие поля из тела ответа:<p/><ul><li><strong>date,</strong></li><li><strong>market,</strong></li><li><strong>cabIdHash,</strong></li><li><strong>cabExpirationTime,</strong></li><li><strong>cabType,</strong></li><li><strong>deviceType,</strong></li><li><strong>moduleName,</strong></li><li><strong>moduleVersion,</strong></li><li><strong>osVersion,</strong></li><li><strong>packageVersion</strong></li><li><strong>osBuild,</strong></li><li><strong>mode,</strong></li><li><strong>architecture</strong></li><li><strong>model,</strong></li><li><strong>baseboard,</strong></li><li><strong>modelFamily.</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующих примерах показано несколько запросов на получение подробных данных об ошибках.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/hardware/failuredetails?failureHash=012e33e3-dbc9-b12f-c124-9d9810f05d8b&startDate=2016-11-05&endDate=2016-11-06&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/hardware/failuredetails?failureHash=012e33e3-dbc9-b12f-c124-9d9810f05d8b&startDate=2016-11-05&endDate=2016-11-06&top=10&skip=0&filter=market eq 'US' and deviceType eq 'PC' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип    | Описание    |
|------------|---------|------------|
| Значение      | array   | Массив объектов, содержащий подробные данные об ошибках. Дополнительные сведения о данных в каждом объекте см. в следующей таблице.          |
| @nextLink  | string  | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об ошибках. |
| TotalCount | inumber | Общее количество строк в результирующих данных для запроса.        |


Элементы в массиве *Value* содержат следующие значения.

| Значение           | Тип    | Описание     |
|-----------------|---------|----------------------------|
| date            | строка  | Первая дата в диапазоне дат, для которого требуется получить данные об ошибках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат. |
| sellerId   | string  | Значение идентификатора продавца, связанное с учетной записью разработчика (соответствует значению **Seller ID** в параметрах учетной записи Центра разработки). |
| failureName     | строка  | Имя сбоя, состоящее из четырех частей: один или несколько классов проблемы, код проверки исключения или ошибки, имя образа или драйвера, в котором произошла ошибка, и соответствующее имя функции.             |
| failureHash     | строка  | Уникальный идентификатор ошибки.     |
| osVersion       | string  | Версия сборки из четырех частей для операционной системы, в которой произошла ошибка.    |
| market          | строка  | Код страны рынка устройства по стандарту ISO 3166     |
| deviceType      | string  | Одна из следующих строк, указывающая тип устройства, на котором произошла ошибка:<p/><ul><li><strong>PC (компьютер),</strong></li><li><strong>Телефон</strong></li><li><strong>Console (консоль),</strong></li><li><strong>Интернет вещей</strong></li><li><strong>Holographic (голография),</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>     |
| moduleName     | string  | Уникальное имя модуля, связанного с этой ошибкой.      |
| moduleVersion  | string  | Версия модуля, связанного с этой ошибкой.   |
| architecture | string |  Архитектура устройства, в котором произошла ошибка.  |
| model | string | Название модели устройства, в котором произошла ошибка. |
| baseboard | string | Название основной платы устройства, в котором произошла ошибка. |
| modelFamily | string | Название семейства модели устройства, в котором произошла ошибка. |
| mode | string | Это значение всегда равно *kernel*. |
| cabIdHash         | string  | Уникальный идентификатор CAB-файла, связанного с этой ошибкой.   |
| cabType         | string  | Тип CAB-файла.   |
| cabExpirationTime  | строка  | Дата и время окончания срока действия CAB-файла, когда его больше нельзя будет скачать, в формате ISO 8601.   |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2017-03-14 23:51:11",
      "sellerId": "14313740",
      "mode": "Kernel",
      "failureName": "0x109_18_2_LoadImageNotify",
      "failureHash": "f060c0b6-fbe6-463f-a9f1-b7ebc1cc722f",
      "cabIdHash": "cc1797f9-b783-44d4-b0e5-46ae7ca668bc",
      "cabType": "MINI",
      "cabExpirationTime": "2017-06-12 23:51:11",
      "osVersion": "10.0.14393.10",
      "market": "US",
      "deviceType": "PC",
      "moduleName": "Unknown_Image",
      "moduleVersion": "Unknown",
      "architecture": "x64",
      "model": "101",
      "baseboard": "750-ABX",
      "modelFamily": "ContosoPad",
      "flightRing": "Windows 10 Retail"
    }]
}
```

## <a name="related-topics"></a>Связанные разделы

* [Получение данных системы отчетов об ошибках оборудования OEM](get-oem-hardware-error-reporting-data.md)
* [Скачивание CAB-файла для ошибки оборудования OEM](download-the-cab-file-for-an-oem-hardware-error.md)
