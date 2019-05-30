---
description: Используйте этот URI REST для получения данных агрегатные установки для классического приложения во время указанного диапазона дат и другие необязательные фильтры.
title: Получение данных об установках классического приложения
ms.date: 03/01/2018
ms.topic: article
keywords: Windows 10, классическое приложение устанавливает программа приложения Windows Desktop
localizationpriority: medium
ms.openlocfilehash: 27ee2f0b6977c551d50fce9dec26c864e3858413
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372133"
---
# <a name="get-desktop-application-installs"></a>Получение данных об установках классического приложения


Использовать этот URI REST для получения данных агрегатные установки в формате JSON для классического приложения, добавленные в [программы приложения Windows Desktop](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program). Этот URI позволяет получить данные установки во время указанного диапазона дат и другие необязательные фильтры. Эта информация также доступна в [устанавливает отчетов](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program) для классических приложений в центре партнеров.

## <a name="prerequisites"></a>предварительные требования


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/installbasedaily```|


### <a name="request-header"></a>Заголовок запроса

| Header        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательно  
|---------------|--------|---------------|------|
| applicationId | строка | Идентификатор продукта классического приложения, для которого требуется извлечь данных установки. Чтобы получить идентификатор продукта для настольного приложения, открыть любой [analytics отчетов для своего настольного приложения в центре партнеров](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program) (такие как **устанавливает отчетов**) и получить идентификатор продукта из URL-адрес. |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные об установках. По умолчанию — 90 дней до текущей даты. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные об установках. По умолчанию используется текущая дата. |  Нет  |
| top | ssNoversion | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку "Далее", которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | ssNoversion | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | строка  | Один или несколько операторов для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>applicationVersion</strong></li><li><strong>типа устройства</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>osRelease</strong></li></ul> | Нет   |
| orderby | строка | Выражение, которое определяет порядок полученных значений данных для каждой установки. Используется следующий синтаксис: <em>orderby=field [порядк.],field [порядк.],...</em>. Параметр <em>field</em> может быть одним из следующих полей из тела ответа:<p/><ul><li><strong>ProductName</strong></li><li><strong>date</strong><li><strong>applicationVersion</strong></li><li><strong>типа устройства</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>osRelease</strong></li><li><strong>installBase</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | строка | Оператор, который применяет агрегирование данных только к указанным полям. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>applicationVersion</strong></li><li><strong>типа устройства</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>osRelease</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>Идентификатор приложения</strong></li><li><strong>date</strong></li><li><strong>ProductName</strong></li><li><strong>installBase</strong></li></ul></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

Следующий пример демонстрирует несколько запросов для получения классического приложения устанавливать данные. Замените *applicationId* значение с Идентификатором продукта для своего настольного приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/installbasedaily?applicationId=1234567890&startDate=2018-01-01&endDate=2018-02-01&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/installbasedaily?applicationId=1234567890&startDate=2018-01-01&endDate=2018-02-01&filter=market eq 'US' and deviceType eq 'PC' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | Массив  | Массив объектов, содержащий сводную информацию об установках. Дополнительные сведения о данных в каждом объекте см. в следующей таблице. |
| @nextLink  | строка | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об установках. |
| TotalCount | ssNoversion    | Общее количество строк в результирующих данных для запроса. |


Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| date                | строка | Дата, связанная с базовым значением установки. |
| applicationId       | строка | Классическое приложение, для которого необходимо получить идентификатор продукта установить данных. |
| productName         | строка | Отображаемое имя классического приложения, как оно получено из метаданных его связанных исполняемых файлов. |
| applicationVersion  | строка | Версия исполняемого файла приложения, который был установлен. |
| deviceType          | строка | Один из следующих строк, которое указывает тип устройства, на котором установлен классического приложения:<p/><ul><li><strong>ПК</strong></li><li><strong>Server</strong></li><li><strong>Планшета</strong></li><li><strong>Неизвестный</strong></li></ul> |
| market              | строка | Код страны ISO 3166 рынка, в которой установлена настольного приложения. |
| osVersion           | строка | Одна из следующих строк, указывающая версию ОС, в которую произведена установка классического приложения:<p/><ul><li><strong>Windows 7</strong></li><li><strong>Windows 8.1</strong></li><li><strong>Windows 10</strong></li><li><strong>Windows Server 2016</strong></li><li><strong>Windows Server 1709</strong></li><li><strong>Неизвестный</strong></li></ul>   |
| osRelease           | строка | Одна из следующих строк, задающих выпуск операционной системы или цикл фокус-тестирования (как подмножество в версиях ОС), в котором установлено классическое приложение.<p/><p>Для Windows 10.</p><ul><li><strong>Версии 1507</strong></li><li><strong>Версии 1511</strong></li><li><strong>Версии 1607</strong></li><li><strong>Версии 1703</strong></li><li><strong>Версия 1709</strong></li><li><strong>Release Preview</strong></li><li><strong>Insider Fast</strong></li><li><strong>Insider медленно</strong></li></ul><p/><p>Для Windows Server 1709</p><ul><li><strong>RTM</strong></li></ul><p>Для Windows Server 2016</p><ul><li><strong>Версии 1607</strong></li></ul><p>Для Windows 8.1.</p><ul><li><strong>Обновление 1</strong></li></ul><p>Для Windows 7.</p><ul><li><strong>Пакет обновления 1</strong></li></ul><p>Если выпуск операционной системы или цикл фокус-тестирования неизвестны, это поле имеет значение <strong>Unknown</strong>.</p> |
| installBase         | number | Количество отдельных устройств, которые бы продукту, установленным на уровне указанного статистической обработки. |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2018-01-24",
      "applicationId": "123456789",
      "productName": "Contoso Demo",
      "applicationVersion": "1.0.0.0",
      "deviceType": "PC",
      "market": "All",
      "osVersion": "Windows 10",
      "osRelease": "Version 1709",
      "installBase": 348218.0
    }
  ],
  "@nextLink": "desktop/installbasedaily?applicationId=123456789&startDate=2018-01-01&endDate=2018-02-01&top=10000&skip=10000&groupby=applicationVersion,deviceType,osVersion,osRelease",
  "TotalCount": 23012
}
```

## <a name="related-topics"></a>См. также

* [Приложение Windows Desktop](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program)
