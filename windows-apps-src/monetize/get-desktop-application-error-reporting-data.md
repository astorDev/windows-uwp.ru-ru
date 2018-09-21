---
author: mcleanbyron
description: Используйте этот метод в API аналитики для Microsoft Store, чтобы получить сводные данные отчетов об ошибках классического приложения в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение данных отчетов об ошибках для классического приложения
ms.author: mcleans
ms.date: 09/04/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, службы Store, API аналитики для Microsoft Store, ошибки, классическое приложение
ms.localizationpriority: medium
ms.openlocfilehash: 66594fd6f59281011fccab21ad5618827db322dd
ms.sourcegitcommit: 5dda01da4702cbc49c799c750efe0e430b699502
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2018
ms.locfileid: "4113442"
---
# <a name="get-error-reporting-data-for-your-desktop-application"></a>Получение данных отчетов об ошибках для классического приложения

Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных отчетности об ошибках для классического приложения, которое вы добавили в [программу для разработчиков классических приложений для Windows](https://msdn.microsoft.com/library/windows/desktop/mt826504). Этот метод позволяет получать только ошибок, возникших за последние 30 дней. Эта информация также доступна в [отчете о работоспособности](https://msdn.microsoft.com/library/windows/desktop/mt826504) для классических приложений на информационной панели Центра разработки для Windows.

## <a name="prerequisites"></a>Что вам понадобится

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/failurehits``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | строка | Код продукта классического приложения, для которого требуется получить данные отчетов об ошибках. Чтобы получить код продукта для классического приложения, откройте любой [отчет аналитики центра разработки для классических приложений](https://msdn.microsoft.com/library/windows/desktop/mt826504) (такой как **отчет о работоспособности**) и получите код продукта из URL-адреса. |  Да  |
| startDate | дата | Начальная дата диапазона дат, для которого требуется получить данные отчетов об ошибках, в формате ```mm/dd/yyyy```. По умолчанию используется текущая дата.<p/><p/>**Примечание:**&nbsp;&nbsp;этот метод позволяет получать только ошибок, возникших за последние 30 дней.  |  Нет  |
| endDate | дата | Конечная дата диапазона дат, для которого требуется получить данные отчетов об ошибках, в формате ```mm/dd/yyyy```. По умолчанию используется текущая дата.   |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter |string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>fileName</strong></li><li><strong>applicationVersion</strong></li><li><strong>failureName</strong></li><li><strong>failureHash,</strong></li><li><strong>symbol,</strong></li><li><strong>osVersion</strong></li><li><strong>osBuild</strong></li><li><strong>osRelease</strong></li><li><strong>eventType</strong></li><li><strong>market,</strong></li><li><strong>deviceType</strong></li><li><strong>productName</strong></li><li><strong>дата</strong></li></ul> | Нет   |
| aggregationLevel | string | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: **day**, **week** или **month**. Если параметр не задан, значение по умолчанию — **day**. Если указать **week** или **month**, значения *failureName* и *failureHash* будут ограничены 1000 сегментов.<p/>  | Нет |
| orderby | string | Выражение, которое определяет порядок полученных значений данных. Используется следующий синтаксис: *orderby=field [order],field [order],...*, где параметр *field* может принимать одно из следующих строковых значений:<ul><li><strong>fileName</strong></li><li><strong>applicationVersion</strong></li><li><strong>failureName</strong></li><li><strong>failureHash,</strong></li><li><strong>symbol,</strong></li><li><strong>osVersion</strong></li><li><strong>osBuild</strong></li><li><strong>osRelease</strong></li><li><strong>eventType</strong></li><li><strong>market,</strong></li><li><strong>deviceType</strong></li><li><strong>productName</strong></li><li><strong>дата</strong></li></ul>Параметр *order* является необязательным и может принимать значения **asc** или **desc**, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — **asc**.</p><p>Пример: строка *orderby*: *orderby=date,market*</p> |  Нет  |
| groupby | string | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li>**failureName,**</li><li>**failureHash,**</li><li>**symbol,**</li><li>**osVersion,**</li><li>**eventType,**</li><li>**market,**</li><li>**deviceType**</li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре *groupby*, а также:</p><ul><li>**date**</li><li>**applicationId**</li><li>**applicationName**</li><li>**eventCount**</li></ul><p>Параметр *groupby* можно использовать вместе с параметром *aggregationLevel*. Например: *&amp;groupby=failureName,market&amp;aggregationLevel=week*</p></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующих примерах показано несколько запросов на получение данных отчетов об ошибках. Замените значение *applicationId* кодом продукта для вашего классического приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/failurehits?applicationId=10238467886765136388&startDate=1/1/2018&endDate=2/1/2018&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/failurehits?applicationId=10238467886765136388&startDate=8/1/2017&endDate=8/31/2017&skip=0&filter=market eq 'US' and deviceType eq 'PC' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип    | Описание     |
|------------|---------|--------------|
| Значение      | array   | Массив объектов, содержащий сводные данные отчетов об ошибках. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения ошибок](#error-values).     |
| @nextLink  | string  | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об ошибках |
| TotalCount | integer | Общее количество строк в результирующих данных для запроса.     |


### <a name="error-values"></a>Значения ошибок

Элементы в массиве *Value* содержат следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|---------------------|
| date            | строка  | Первая дата в диапазоне дат, для которого требуется получить данные об ошибках, в формате ```yyyy-mm-dd```. Если в запросе указывается один день, это значение равно дате, соответствующей тому дню. Если в запросе указывается более длительный диапазон дат, это значение равно первой дате в этом диапазоне дат. Для запросов, которые определяют значение *aggregationLevel* в виде **hour**, это значение также содержит значения времени в формате ```hh:mm:ss```.  |
| applicationId   | строка  | Значение кода продукта для классического приложения, для которого требуется извлечь данные об ошибках.    |
| productName | строка  | Отображаемое имя классического приложения, как оно получено из метаданных его связанных исполняемых файлов.   |
| appName | строка  |  TBD  |
| fileName | строка  | Имя исполняемого файла для классического приложения.   |
| failureName     | строка  | Имя сбоя, состоящее из четырех частей: один или несколько классов проблемы, код проверки исключения или ошибки, имя образа, в котором произошла ошибка, и соответствующее имя функции.  |
| failureHash     | string  | Уникальный идентификатор ошибки   |
| symbol          | string  | Символ, назначенный этой ошибке |
| osBuild       | строка  | Номер сборки из четырех частей для операционной системы, в которой произошла ошибка.  |
| osVersion       | строка  | Одна из следующих строк, указывающая версию ОС, в которую произведена установка классического приложения:<p/><ul><li><strong>Windows 7</strong></li><li><strong>Windows8.1</strong></li><li><strong>Windows10</strong></li><li><strong>WindowsServer2016</strong></li><li><strong>WindowsServer1709</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>   |   
| osRelease | string  | Одна из следующих строк, задающих выпуск операционной системы или цикл фокус-тестирования (как подмножество в версиях ОС), в котором возникла ошибка.<p/><p>Для Windows 10.</p><ul><li><strong>Версия 1507</strong></li><li><strong>Версия 1511</strong></li><li><strong>Версия 1607</strong></li><li><strong>Версия 1703</strong></li><li><strong>Версия 1709</strong></li><li><strong>Версия 1803</strong></li><li><strong>Release Preview</strong></li><li><strong>Предварительная оценка — ранний доступ</strong></li><li><strong>Предварительная оценка Windows— поздний доступ</strong></li></ul><p/><p>Для Windows Server 1709</p><ul><li><strong>RTM</strong></li></ul><p>Для Windows Server 2016</p><ul><li><strong>Версия 1607</strong></li></ul><p>Для Windows 8.1.</p><ul><li><strong>Обновление 1</strong></li></ul><p>Для Windows 7.</p><ul><li><strong>Пакет обновления 1</strong></li></ul><p>Если выпуск операционной системы или цикл фокус-тестирования неизвестны, это поле имеет значение <strong>Unknown</strong>.</p> |
| eventType       | строка  | Одна из следующих строк, указывающих тип события ошибки:<ul><li>**crash**</li><li>**hang,**</li><li>**memory.**</li><li>**jse**</li></ul>       |
| market          | string  | Код страны рынка устройства по стандарту ISO 3166   |
| deviceType      | string  | Одна из следующих строк, указывающая тип устройства, на котором произошла ошибка:<p/><ul><li><strong>ПК</strong></li><li><strong>Сервер</strong></li><li><strong>Планшет</strong></li><li><strong>Unknown (неизвестно).</strong></li></ul>    |
| applicationVersion     | строка  |   Версия исполняемого файла приложения, в котором произошла ошибка.    |
| eventCount      | число | Количество событий, которые вызваны этой ошибкой, на указанном уровне агрегирования.      |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2018-02-01",
      "applicationId": "10238467886765136388",
      "productName": "Contoso Demo",
      "appName": "Contoso Demo",
      "fileName": "contosodemo.exe",
      "failureName": "SVCHOSTGROUP_localservice_IN_PAGE_ERROR_c0000006_hardware_disk!Unknown",
      "failureHash": "11242ef3-ebd8-d525-838d-b5497b225695",
      "symbol": "hardware_disk!Unknown",
      "osBuild": "10.0.15063.850",
      "osVersion": "Windows 10",
      "osRelease": "Version 1703",
      "eventType": "crash",
      "market": "US",
      "deviceType": "PC",
      "applicationVersion": "2.2.2.0",
      "eventCount": 0.0012422360248447205
    }
  ],
  "@nextLink": "desktop/failurehits?applicationId=10238467886765136388&aggregationLevel=week&startDate=2018/02/01&endDate2018/02/08&top=1&skip=1",
  "TotalCount": 21
}

```

## <a name="related-topics"></a>Статьи по теме

* [Отчет о работоспособности](../publish/health-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение сведений об ошибке в классическом приложении](get-details-for-an-error-in-your-desktop-application.md)
* [Получение трассировки стека при возникновении ошибки в классическом приложении](get-the-stack-trace-for-an-error-in-your-desktop-application.md)
* [Скачивание CAB-файла для ошибки в классическом приложении](download-the-cab-file-for-an-error-in-your-desktop-application.md)
