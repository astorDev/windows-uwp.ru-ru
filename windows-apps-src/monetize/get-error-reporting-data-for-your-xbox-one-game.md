---
description: Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных отчетов об ошибках в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение данных системы отчетов для Xbox One игры
ms.date: 11/06/2018
ms.topic: article
keywords: Windows 10, UWP, службы Store, API аналитики для Microsoft Store, ошибки
ms.localizationpriority: medium
ms.openlocfilehash: 22dff391e787e1763cb730272ba9cea029758c99
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8791285"
---
# <a name="get-error-reporting-data-for-your-xbox-one-game"></a>Получение данных системы отчетов для Xbox One игры

Используйте этот метод в API аналитики Microsoft Store для получения сводные данные об ошибках для Xbox One игры, которая была добавлена через портал разработчика Xbox (XDP) и доступны на информационной панели центра партнеров аналитики XDP.

Вы можете получить дополнительные сведения об ошибках с помощью методов [получения подробных сведений об ошибке в Xbox One игр](get-details-for-an-error-in-your-xbox-one-game.md), [получения трассировки стека для ошибки в Xbox One игры](get-the-stack-trace-for-an-error-in-your-xbox-one-game.md)и [скачать CAB-файл для ошибки в игры Xbox One](download-the-cab-file-for-an-error-in-your-xbox-one-game.md) .

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failurehits``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | string | **Код продукта в магазине** игры на Xbox One, для которой запрашиваются данные отчетов об ошибках. **Код продукта в магазине** доступен на странице удостоверения приложения в центре партнеров. Пример **Код продукта в магазине** : 9wzdncrfj3q8. |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные отчетов об ошибках. По умолчанию используется текущая дата Если для *aggregationLevel* задано значение **day**, **week** или **month**, этот параметр должен указывать дату в формате ```mm/dd/yyyy```. Если для *aggregationLevel* задано значение **hour**, этот параметр может указать дату в формате ```mm/dd/yyyy``` или дату и время в формате ```yyyy-mm-dd hh:mm:ss```.  |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные отчетов об ошибках. По умолчанию используется текущая дата. Если для *aggregationLevel* задано значение **day**, **week** или **month**, этот параметр должен указывать дату в формате ```mm/dd/yyyy```. Если для *aggregationLevel* задано значение **hour**, этот параметр может указать дату в формате ```mm/dd/yyyy``` или дату и время в формате ```yyyy-mm-dd hh:mm:ss```. |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter |string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Вы можете указать следующие поля из тела ответа:<p/><ul><li>**applicationName**</li><li>**failureName**</li><li>**failureHash,**</li><li>**symbol,**</li><li>**osVersion**</li><li>**osRelease**</li><li>**eventType**</li><li>**market,**</li><li>**deviceType,**</li><li>**packageName,**</li><li>**packageVersion**</li><li>**date**</li></ul> | Нет   |
| aggregationLevel | string | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: **hour**, **day**, **week** или **month**. Если параметр не задан, значение по умолчанию — **day**. Если указать **week** или **month**, значения *failureName* и *failureHash* будут ограничены 1000 сегментов.<p/><p/>**Примечание.**&nbsp;&nbsp;При указании **hour** можно получить данные об ошибках только за предыдущие 72 часа. Чтобы получить данные об ошибках старше 72 часов, укажите **day** или другой уровень статистической обработки.  | Нет |
| orderby | string | Выражение, которое определяет порядок полученных значений данных. Используется следующий синтаксис: *orderby=field [order],field [order],...*, где параметр *field* может принимать одно из следующих строковых значений:<ul><li>**applicationName**</li><li>**failureName**</li><li>**failureHash,**</li><li>**symbol,**</li><li>**osVersion**</li><li>**osRelease**</li><li>**eventType**</li><li>**market,**</li><li>**deviceType,**</li><li>**packageName,**</li><li>**packageVersion**</li><li>**date**</li></ul><p>Параметр *order* является необязательным и может принимать значения **asc** или **desc**, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — **asc**.</p><p>Пример: строка *orderby*: *orderby=date,market*</p> |  Нет  |
| groupby | string | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li>**failureName,**</li><li>**failureHash,**</li><li>**symbol,**</li><li>**osVersion,**</li><li>**eventType,**</li><li>**market,**</li><li>**deviceType,**</li><li>**packageName,**</li><li>**packageVersion.**</li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре *groupby*, а также:</p><ul><li>**date**</li><li>**applicationId**</li><li>**applicationName,**</li><li>**deviceCount,**</li><li>**eventCount.**</li></ul><p>Параметр *groupby* можно использовать вместе с параметром *aggregationLevel*. Например: *&amp;groupby=failureName,market&amp;aggregationLevel=week*</p></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующих примерах показано несколько запросов на получение Xbox One игры данные об ошибках. Замените значение *applicationId* **Код продукта в магазине** для вашей игры.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failurehits?applicationId=BRRT4NJ9B3D1&startDate=1/1/2015&endDate=2/1/2015&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/failurehits?applicationId=BRRT4NJ9B3D1&startDate=8/1/2015&endDate=8/31/2015&skip=0&filter=market eq 'US' and deviceType eq 'Console' HTTP/1.1
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
| date            | строка  | Первая дата в диапазоне дат, для которого требуется получить данные об ошибках, в формате ```yyyy-mm-dd```. Если в запросе указывается один день, это значение равно дате, соответствующей тому дню. Если в запросе указывается более длительный диапазон дат, это значение равно первой дате в этом диапазоне дат. Для запросов, которые определяют значение *aggregationLevel* **часа**, это значение также содержит значения времени в формате ```hh:mm:ss``` в местный часовой пояс, в котором произошла ошибка.  |
| applicationId   | string  | **Код продукта в магазине** игры на Xbox One, для которого требуется получить данные об ошибках.   |
| applicationName | строка  | Отображаемое имя игры.   |
| failureName     | строка  | Имя сбоя, состоящее из четырех частей: один или несколько классов проблемы, код проверки исключения или ошибки, имя образа, в котором произошла ошибка, и соответствующее имя функции.  |
| failureHash     | string  | Уникальный идентификатор ошибки   |
| symbol          | string  | Символ, назначенный этой ошибке |
| osVersion       | string  | Версия ОС, в которой произошла ошибка. Это значение всегда по **Windows 10**.  |
| osRelease       | string  |  Одна из следующих строк, задающих выпуск операционной системы Windows 10 или цикл фокус-тестирования (как подмножество в версиях ОС), на котором произошла ошибка.<p/><ul><li><strong>Версия 1507</strong></li><li><strong>Версия 1511</strong></li><li><strong>Версия 1607</strong></li><li><strong>Версия 1703</strong></li><li><strong>Версия 1709</strong></li><li><strong>Версия 1803</strong></li><li><strong>Release Preview</strong></li><li><strong>Предварительная оценка — ранний доступ</strong></li><li><strong>Предварительная оценка Windows— поздний доступ</strong></li></ul><p>Если выпуск операционной системы или цикл фокус-тестирования неизвестны, это поле имеет значение <strong>Unknown</strong>.</p>    |
| eventType       | строка  | Одна из следующих строк:<ul><li>**crash,**</li><li>**hang,**</li><li>**Сбой памяти**</li></ul>      |
| market          | string  | Код страны рынка устройства по стандарту ISO 3166   |
| deviceType      | строка  | Тип устройства, на котором произошла ошибка. Это значение всегда **консоли**.    |
| packageName     | string  | Игры уникальное имя пакета, связанного с этой ошибкой.      |
| packageVersion  | string  | Версия пакета игры, связанного с этой ошибкой.   |
| deviceCount     | integer | Количество уникальных устройств, соответствующих этой ошибке, на указанном уровне агрегирования.  |
| eventCount      | integer | Количество событий, которые вызваны этой ошибкой, на указанном уровне агрегирования.      |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2017-03-09",
      "applicationId": "BRRT4NJ9B3D1",
      "applicationName": "Contoso Sports",
      "failureName": "APPLICATION_FAULT_8013150a_StoreWrapper.ni.DLL!70475e55",
      "failureHash": "5a6b2170-1661-ed47-24d7-230fed0077af",
      "symbol": "storewrapper_ni!70475e55",
      "osVersion": "Windows 10",
      "osRelease": "Version 1803",
      "eventType": "crash",
      "market": "US",
      "deviceType": "Console",
      "packageName": "",
      "packageVersion": "0.0.0.0",
      "deviceCount": 0.0,
      "eventCount": 1.0
    }
  ],
  "@nextLink": "failurehits?applicationId=BRRT4NJ9B3D1&aggregationLevel=week&startDate=2017/03/01&endDate=2016/02/01&top=1&skip=1",
  "TotalCount": 21
}

```

## <a name="related-topics"></a>Статьи по теме

* [Получение сведений об ошибке в Xbox One игры](get-details-for-an-error-in-your-xbox-one-game.md)
* [Получение трассировки стека при возникновении ошибки в Xbox One игры](get-the-stack-trace-for-an-error-in-your-xbox-one-game.md)
* [Скачать CAB-файл для ошибки в игры Xbox One](download-the-cab-file-for-an-error-in-your-xbox-one-game.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
