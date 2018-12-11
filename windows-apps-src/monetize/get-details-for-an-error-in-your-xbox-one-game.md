---
description: Используйте этот метод в API аналитики для Microsoft Store, чтобы получить подробные данные об определенной ошибке для Xbox One игры.
title: Получение сведений об ошибке в Xbox One игры
ms.date: 11/06/2018
ms.topic: article
keywords: Windows 10, UWP, службы Store, API аналитики для Microsoft Store, ошибки, подробные сведения
ms.localizationpriority: medium
ms.openlocfilehash: da3252c42a0c2e2bd02465985737125cc053a616
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8880883"
---
# <a name="get-details-for-an-error-in-your-xbox-one-game"></a>Получение сведений об ошибке в Xbox One игры

Используйте этот метод в API аналитики Microsoft Store для получения подробные данные об определенной ошибке для Xbox One игры, была добавлена через портал разработчика Xbox (XDP) и доступны на информационной панели центра партнеров аналитики XDP. Этот метод позволяет получать подробные сведения только об ошибках, возникших за последние 30 дней.

Прежде чем использовать этот метод, необходимо сначала использовать метод [получения данных для игры Xbox One отчетов об ошибках](get-error-reporting-data-for-your-xbox-one-game.md) получить идентификатор ошибки, для которого требуется получить подробные сведения.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия токена можно получить новый токен.
* Получите идентификатор ошибки, о которой требуется получить подробные сведения. Для получения этого идентификатора используйте метод [получения данных отчетов об ошибках для Xbox One игры](get-error-reporting-data-for-your-xbox-one-game.md) и значение **failureHash** в тексте ответа этого метода.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failuredetails``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | **Код продукта в магазине** игры на Xbox One, для которого извлекаются сведения об ошибках. **Код продукта в магазине** доступен на странице удостоверения приложения в центре партнеров. Пример **Код продукта в магазине** : 9wzdncrfj3q8. |  Да  |
| failureHash | string | Уникальный идентификатор ошибки, о которой требуется получить подробные сведения. Чтобы получить это значение для интересующей вас об ошибке, используйте метод [получения данных отчетов об ошибках для Xbox One игры](get-error-reporting-data-for-your-xbox-one-game.md) и значение **failureHash** в тексте ответа этого метода. |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить подробные данные об ошибках. По умолчанию используется текущая дата минус 30 дней. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить подробные данные об ошибках. По умолчанию используется текущая дата. |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10 и skip=0 извлекаются первые 10 строк данных; при top=10 и skip=10 извлекаются следующие 10 строк данных и т. д. |  Нет  |
| filter |string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>market</strong></li><li><strong>date</strong></li><li><strong>cabId</strong></li><li><strong>cabExpirationTime</strong></li><li><strong>deviceType</strong></li><li><strong>deviceModel</strong></li><li><strong>osVersion</strong></li><li><strong>osRelease</strong></li><li><strong>packageVersion</strong></li><li><strong>osBuild</strong></li></ul> | Нет   |
| orderby | string | Выражение, которое определяет порядок полученных значений данных. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>, где параметр <em>field</em> может принимать одно из следующих строковых значений:<ul><li><strong>market</strong></li><li><strong>date</strong></li><li><strong>cabId</strong></li><li><strong>cabExpirationTime</strong></li><li><strong>deviceType</strong></li><li><strong>deviceModel</strong></li><li><strong>osVersion</strong></li><li><strong>osRelease</strong></li><li><strong>packageVersion</strong></li><li><strong>osBuild</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующих примерах показано несколько запросов на получение подробные данные об ошибках для Xbox One игры. Замените значение *applicationId* **Код продукта в магазине** для вашей игры.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failuredetails?applicationId=BRRT4NJ9B3D1&failureHash=012e33e3-dbc9-b12f-c124-9d9810f05d8b&startDate=2016-11-05&endDate=2016-11-06&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failuredetails?applicationId=BRRT4NJ9B3D1&failureHash=012e33e3-dbc9-b12f-c124-9d9810f05d8b&startDate=2016-11-05&endDate=2016-11-06&top=10&skip=0&filter=market eq 'US' and deviceType eq 'Windows.Desktop' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип    | Описание    |
|------------|---------|------------|
| Значение      | array   | Массив объектов, содержащий подробные данные об ошибках. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения подробных сведений об ошибках](#error-detail-values).          |
| @nextLink  | string  | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об ошибках. |
| TotalCount | integer | Общее количество строк в результирующих данных для запроса.        |


<span id="error-detail-values"/>

### <a name="error-detail-values"></a>Значения подробных сведений об ошибках

Элементы в массиве *Value* содержат следующие значения.

| Значение           | Тип    | Описание     |
|-----------------|---------|----------------------------|
| applicationId   | string  | **Код продукта в магазине** игры на Xbox One, для которого требуется извлечь подробные данные об ошибках.      |
| failureHash     | string  | Уникальный идентификатор ошибки.     |
| failureName     | строка  | Имя сбоя, состоящее из четырех частей: один или несколько классов проблемы, код проверки исключения или ошибки, имя образа, в котором произошла ошибка, и соответствующее имя функции.           |
| date            | string  | Первая дата в диапазоне дат, для которого требуется получить данные об ошибках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат. |
| cabId           | string  | Уникальный идентификатор CAB-файла, связанного с этой ошибкой.   |
| cabExpirationTime  | string  | Дата и время окончания срока действия CAB-файла, когда его больше нельзя будет скачать, в формате ISO 8601.   |
| market          | string  | Код страны рынка устройства по стандарту ISO 3166.     |
| osBuild         | string  | Номер сборки операционной системы, в которой произошла ошибка.       |
| packageVersion  | string  | Версия пакета игры, связанного с этой ошибкой.    |
| deviceModel           | string  | Одна из следующих строк, указывающая консоли Xbox One, на котором выполнялось игры, когда произошла ошибка.<p/><ul><li><strong>Microsoft Xbox один</strong></li><li><strong>Microsoft Xbox One S</strong></li><li><strong>Microsoft Xbox One X</strong></li></ul>  |
| osVersion       | string  | Версия ОС, в которой произошла ошибка. Это значение всегда по **Windows 10**.    |
| osRelease       | string  |  Одна из следующих строк, задающих выпуск операционной системы Windows 10 или цикл фокус-тестирования (как подмножество в версиях ОС), на котором произошла ошибка.<p/><ul><li><strong>Версия 1507</strong></li><li><strong>Версия 1511</strong></li><li><strong>Версия 1607</strong></li><li><strong>Версия 1703</strong></li><li><strong>Версия 1709</strong></li><li><strong>Версия 1803</strong></li><li><strong>Release Preview</strong></li><li><strong>Предварительная оценка — ранний доступ</strong></li><li><strong>Предварительная оценка Windows— поздний доступ</strong></li></ul><p>Если выпуск операционной системы или цикл фокус-тестирования неизвестны, это поле имеет значение <strong>Unknown</strong>.</p>    |
| deviceType      | строка  | Тип устройства, на котором произошла ошибка. Это значение всегда **консоли**.     |
| cabDownloadable           | Boolean  | Указывает, может ли этот пользователь скачать CAB-файл.   |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "applicationId": "BRRT4NJ9B3D1",
      "failureHash": "012345-5dbc9-b12f-c124-9d9810f05d8b",
      "failureName": "STOWED_EXCEPTION_System.UriFormatException_exe!ContosoSports.GroupedItems+_ItemView_ItemClick_d__9.MoveNext",
      "date": "2018-02-05 09:11:25",
      "cabId": "133637331323",
      "cabExpirationTime": "2016-12-05 09:11:25",
      "market": "US",
      "osBuild": "10.0.17134",
      "packageVersion": "1.0.2.6",
      "deviceModel": "Microsoft-Xbox One",
      "osVersion": "Windows 10",
      "osRelease": "Version 1803",
      "deviceType": "Console",
      "cabDownloadable": false
    }
  ],
  "@nextLink": null,
  "TotalCount": 1
}
```

## <a name="related-topics"></a>Статьи по теме

* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение данных системы отчетов для Xbox One игры](get-error-reporting-data-for-your-xbox-one-game.md)
* [Получение трассировки стека при возникновении ошибки в Xbox One игры](get-the-stack-trace-for-an-error-in-your-xbox-one-game.md)
* [Скачать CAB-файл для ошибки в игры Xbox One](download-the-cab-file-for-an-error-in-your-xbox-one-game.md)
