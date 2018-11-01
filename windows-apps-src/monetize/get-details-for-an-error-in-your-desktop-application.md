---
author: Xansky
description: Используйте этот метод в API аналитики для Microsoft Store, чтобы получить подробные данные об определенной ошибке в вашем классическом приложении.
title: Получение сведений об ошибке в классическом приложении
ms.author: mhopkins
ms.date: 06/05/2018
ms.topic: article
keywords: Windows 10, UWP, службы Store, API аналитики для Microsoft Store, ошибки, подробные сведения, классическое приложение
ms.localizationpriority: medium
ms.openlocfilehash: 25fbd3e037b2b81743ec2bb137b6df2a04736a76
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5878325"
---
# <a name="get-details-for-an-error-in-your-desktop-application"></a>Получение сведений об ошибке в классическом приложении

Используйте этот метод в API аналитики для Microsoft Store, чтобы получить подробные данные об определенной ошибке в вашем приложении в формате JSON. Этот метод позволяет получать подробные сведения только об ошибках, возникших за последние 30 дней. Подробные данные об ошибках также доступны в [Отчете о работоспособности](https://msdn.microsoft.com/library/windows/desktop/mt826504) для классических приложений на информационной панели Центра разработки для Windows.

Перед использованием этого метода вам потребуется воспользоваться методом [получения данных отчетов об ошибках](get-error-reporting-data.md), чтобы получить идентификатор ошибки, о которой требуется получить подробные сведения.

## <a name="prerequisites"></a>Предварительные условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия токена можно получить новый токен.
* Получите идентификатор ошибки, о которой требуется получить подробные сведения. Чтобы получить этот идентификатор, используйте метод [получения данных отчетов об ошибках](get-error-reporting-data.md) и значение **failureHash** в тексте ответа этого метода.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/failuredetails``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | строка | Код продукта классического приложения, для которого требуется получить данные об ошибках. Чтобы получить код продукта для классического приложения, откройте любой [отчет аналитики центра разработки для классических приложений](https://msdn.microsoft.com/library/windows/desktop/mt826504) (такой как **отчет о работоспособности**) и получите код продукта из URL-адреса. |  Да  |
| failureHash | строка | Уникальный идентификатор ошибки, о которой требуется получить подробные сведения. Чтобы получить это значение для интересующей вас ошибки, используйте метод [получения данных отчетов об ошибках](get-error-reporting-data.md) и значение **failureHash** в тексте ответа этого метода. |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить подробные данные об ошибках. По умолчанию используется текущая дата минус 30 дней.<p/><p/>**Примечание:**&nbsp;&nbsp;этот метод позволяет получать только подробные сведения об ошибках, возникших за последние 30 дней. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить подробные данные об ошибках. По умолчанию используется текущая дата. |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10 и skip=0 извлекаются первые 10 строк данных; при top=10 и skip=10 извлекаются следующие 10 строк данных и т. д. |  Нет  |
| filter |string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>market</strong></li><li><strong>date</strong></li><li><strong>cabIdHash</strong></li><li><strong>cabExpirationTime</strong></li><li><strong>deviceType</strong></li><li><strong>deviceModel</strong></li><li><strong>osVersion</strong></li><li><strong>osRelease</strong></li><li><strong>applicationVersion</strong></li><li><strong>osBuild</strong></li><li><strong>fileName</strong></li></ul> | Нет   |
| orderby | string | Выражение, которое определяет порядок полученных значений данных. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>, где параметр <em>field</em> может принимать одно из следующих строковых значений:<ul><li><strong>market</strong></li><li><strong>date</strong></li><li><strong>cabIdHash</strong></li><li><strong>cabExpirationTime</strong></li><li><strong>deviceType</strong></li><li><strong>deviceModel</strong></li><li><strong>osVersion</strong></li><li><strong>osRelease</strong></li><li><strong>applicationVersion</strong></li><li><strong>osBuild</strong></li><li><strong>fileName</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующих примерах показано несколько запросов на получение подробных данных об ошибках. Замените значение *applicationId* кодом продукта для вашего классического приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/failuredetails?applicationId=10238467886765136388&failureHash=012e33e3-dbc9-b12f-c124-9d9810f05d8b&startDate=2016-11-05&endDate=2016-11-06&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/failuredetails?applicationId=10238467886765136388&failureHash=012e33e3-dbc9-b12f-c124-9d9810f05d8b&startDate=2016-11-05&endDate=2016-11-06&top=10&skip=0&filter=market eq 'US' and deviceType eq 'PC' HTTP/1.1
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
| applicationId   | строка  | Значение кода продукта для классического приложения, для которого требуется извлечь сведения об ошибках.      |
| failureHash     | string  | Уникальный идентификатор ошибки.     |
| failureName     | строка  | Имя сбоя, состоящее из четырех частей: один или несколько классов проблемы, код проверки исключения или ошибки, имя образа, в котором произошла ошибка, и соответствующее имя функции.           |
| date            | string  | Первая дата в диапазоне дат, для которого требуется получить данные об ошибках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат. |
| cabIdHash           | строка  | Уникальный хэш идентификатора CAB-файла, связанного с этой ошибкой.   |
| cabExpirationTime  | string  | Дата и время окончания срока действия CAB-файла, когда его больше нельзя будет скачать, в формате ISO 8601.   |
| market          | string  | Код страны рынка устройства по стандарту ISO 3166.     |
| osBuild         | string  | Номер сборки операционной системы, в которой произошла ошибка.       |
| applicationVersion         | строка  |   Версия исполняемого файла приложения, в котором произошла ошибка.     |
| deviceModel           | string  | Строка, указывающая модель устройства, на котором выполнялось приложение в момент возникновения ошибки.   |
| osVersion       | строка  | Одна из следующих строк, указывающая версию ОС, в которую произведена установка классического приложения:<p/><ul><li><strong>Windows 7</strong></li><li><strong>Windows8.1</strong></li><li><strong>Windows 10</strong></li><li><strong>WindowsServer2016</strong></li><li><strong>WindowsServer1709</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>    |
| osRelease       | string  |  Одна из следующих строк, задающих выпуск операционной системы или цикл фокус-тестирования (как подмножество в версиях ОС), в котором возникла ошибка.<p/><p>Для Windows 10.</p><ul><li><strong>Версия 1507</strong></li><li><strong>Версия 1511</strong></li><li><strong>Версия 1607</strong></li><li><strong>Версия 1703</strong></li><li><strong>Версия 1709</strong></li><li><strong>Версия 1803</strong></li><li><strong>Release Preview</strong></li><li><strong>Предварительная оценка — ранний доступ</strong></li><li><strong>Предварительная оценка Windows— поздний доступ</strong></li></ul><p/><p>Для Windows Server 1709</p><ul><li><strong>RTM</strong></li></ul><p>Для Windows Server 2016</p><ul><li><strong>Версия 1607</strong></li></ul><p>Для Windows 8.1.</p><ul><li><strong>Обновление 1</strong></li></ul><p>Для Windows 7.</p><ul><li><strong>Пакет обновления 1</strong></li></ul><p>Если выпуск операционной системы или цикл фокус-тестирования неизвестны, это поле имеет значение <strong>Unknown</strong>.</p>    |
| deviceType      | строка  | Одна из следующих строк, указывающая тип устройства, на котором произошла ошибка: <p/><ul><li><strong>ПК</strong></li><li><strong>Сервер</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>     |
| cabDownloadable           | Boolean  | Указывает, может ли этот пользователь скачать CAB-файл.   |
| fileName           | строка  | Имя исполняемого файла для классического приложения, для которого требуется извлечь сведения об ошибках.  |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "applicationId": "10238467886765136388",
      "failureHash": "012345-5dbc9-b12f-c124-9d9810f05d8b",
      "failureName": "NULL_CLASS_PTR_WRITE_c0000005_contoso.exe!unknown_error_in_process",
      "date": "2018-01-28 23:55:29",
      "cabIdHash": "54ffb83a-e159-41d2-8158-f36f306cc01e",
      "cabExpirationTime": "2018-02-27 23:55:29",
      "market": "US",
      "osBuild": "10.0.10240",
      "applicationVersion": "2.2.2.0",
      "deviceModel": "Contoso All-in-one",
      "osVersion": "Windows 10",
      "osRelease": "Version 1703",
      "deviceType": "PC",
      "cabDownloadable": false,
      "fileName": "contosodemo.exe"
    }
  ],
  "@nextLink": null,
  "TotalCount": 1
}
```

## <a name="related-topics"></a>Статьи по теме

* [Отчет о работоспособности](../publish/health-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение данных отчетов об ошибках для классического приложения](get-desktop-application-error-reporting-data.md)
* [Получение трассировки стека при возникновении ошибки в классическом приложении](get-the-stack-trace-for-an-error-in-your-desktop-application.md)
* [Скачивание CAB-файла для ошибки в классическом приложении](download-the-cab-file-for-an-error-in-your-desktop-application.md)
