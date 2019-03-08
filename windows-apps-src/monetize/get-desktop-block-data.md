---
description: Используйте этот URI REST для получения блока данных для классического приложения во время указанного диапазона дат и другие необязательные фильтры.
title: Получение блоков обновлений для классического приложения
ms.date: 07/11/2018
ms.topic: article
keywords: Windows 10, блоки приложения на рабочем столе Windows Desktop прикладной программы
localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 08c3265fd0ff908c210a6586f561aba62ba9155a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57641109"
---
# <a name="get-upgrade-blocks-for-your-desktop-application"></a>Получение блоков обновлений для классического приложения

Используйте этот URI REST для получения сведений об устройствах Windows 10, на которых своего настольного приложения блокирует выполнение обновления Windows 10. Этот URI можно использовать только для настольных приложений, которые были добавлены к [программы приложения Windows Desktop](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program). Эта информация также доступна в [приложение блокирует отчетов](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program#application-blocks-report) для классических приложений в центре партнеров.

Чтобы получить сведения о блоках устройства для определенный исполняемый файл в приложении рабочего стола, см. в разделе [обновления блока Get подробные данные своего настольного приложения](get-desktop-block-data-details.md).

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/blockhits```|


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательно  
|---------------|--------|---------------|------|
| applicationId | Строка | Идентификатор продукта классического приложения, для которого требуется извлечь блока данных. Чтобы получить идентификатор продукта для настольного приложения, открыть любой [analytics отчетов для своего настольного приложения в центре партнеров](https://msdn.microsoft.com/library/windows/desktop/mt826504) (такие как **блокирует отчетов**) и получить идентификатор продукта из URL-адрес. |  Да  |
| startDate | date | Дата начала в диапазоне дат, блок данных для извлечения. По умолчанию — 90 дней до текущей даты. |  Нет  |
| endDate | date | Дата окончания в диапазоне дат, блок данных для извлечения. По умолчанию используется текущая дата. |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку "Далее", которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | Строка  | Один или несколько операторов для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>applicationVersion</strong></li><li><strong>Архитектура</strong></li><li><strong>blockType</strong></li><li><strong>типа устройства</strong></li><li><strong>Имя файла</strong></li><li><strong>на рынке</strong></li><li><strong>osRelease</strong></li><li><strong>версия ОС</strong></li><li><strong>ProductName</strong></li><li><strong>параметр "targetos"</strong></li></ul> | Нет   |
| orderby | Строка | Оператор, производится упорядочение результирующего значения данных для каждого блока. Используется следующий синтаксис: <em>orderby=field [порядк.],field [порядк.],...</em>. Параметр <em>field</em> может быть одним из следующих полей из тела ответа:<p/><ul><li><strong>applicationVersion</strong></li><li><strong>Архитектура</strong></li><li><strong>blockType</strong></li><li><strong>Дата</strong><li><strong>типа устройства</strong></li><li><strong>Имя файла</strong></li><li><strong>на рынке</strong></li><li><strong>osRelease</strong></li><li><strong>версия ОС</strong></li><li><strong>ProductName</strong></li><li><strong>параметр "targetos"</strong></li><li><strong>deviceCount</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | Строка | Оператор, который применяет агрегирование данных только к указанным полям. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>applicationVersion</strong></li><li><strong>Архитектура</strong></li><li><strong>blockType</strong></li><li><strong>типа устройства</strong></li><li><strong>Имя файла</strong></li><li><strong>на рынке</strong></li><li><strong>osRelease</strong></li><li><strong>версия ОС</strong></li><li><strong>параметр "targetos"</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>Идентификатор приложения</strong></li><li><strong>Дата</strong></li><li><strong>ProductName</strong></li><li><strong>deviceCount</strong></li></ul></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующем примере показано несколько запросов для получения данных блока настольного приложения. Замените *applicationId* значение с Идентификатором продукта для своего настольного приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/blockhits?applicationId=5126873772241846776&startDate=2018-05-01&endDate=2018-06-07&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/blockhits?applicationId=5126873772241846776&startDate=2018-05-01&endDate=2018-06-07&filter=market eq 'US' and deviceType eq 'PC' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | Массив  | Массив объектов, содержащих статистические блока данных. Дополнительные сведения о данных в каждом объекте см. в следующей таблице. |
| @nextLink  | Строка | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается, если **верхней** параметр запроса имеет значение 10000, но есть более 10000 строк блок данных для запроса. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса. |


Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| applicationId       | Строка | Идентификатор продукта классическое приложение, для которого необходимо получить блок данных. |
| date                | Строка | Дата, связанная со значением попаданий блока. |
| productName         | Строка | Отображаемое имя классического приложения, как оно получено из метаданных его связанных исполняемых файлов. |
| fileName            | Строка | Исполняемый файл, который был заблокирован. |
| applicationVersion  | Строка | Версия исполняемого файла приложения, который был заблокирован. |
| osVersion           | Строка | Один из следующих строк, которое указывает версию операционной системы, на котором в настоящее время выполняется классического приложения:<p/><ul><li><strong>Windows 7</strong></li><li><strong>Windows 8.1</strong></li><li><strong>Windows 10</strong></li><li><strong>Windows Server 2016</strong></li><li><strong>Windows Server 1709</strong></li><li><strong>Неизвестный</strong></li></ul>   |
| osRelease           | Строка | Одно из следующих строк, на которые указывает выпуск ОС или фокус-тестирования кольцо (как subpopulation в версии ОС), на который классическое приложение выполняется в данный момент.<p/><p>Для Windows 10.</p><ul><li><strong>Версии 1507</strong></li><li><strong>Версии 1511</strong></li><li><strong>Версии 1607</strong></li><li><strong>Версии 1703</strong></li><li><strong>Версия 1709</strong></li><li><strong>Release Preview</strong></li><li><strong>Insider Fast</strong></li><li><strong>Insider медленно</strong></li></ul><p/><p>Для Windows Server 1709</p><ul><li><strong>RTM</strong></li></ul><p>Для Windows Server 2016</p><ul><li><strong>Версии 1607</strong></li></ul><p>Для Windows 8.1.</p><ul><li><strong>Обновление 1</strong></li></ul><p>Для Windows 7.</p><ul><li><strong>Пакет обновления 1</strong></li></ul><p>Если выпуск операционной системы или цикл фокус-тестирования неизвестны, это поле имеет значение <strong>Unknown</strong>.</p> |
| market              | Строка | Код страны ISO 3166 рынка, в котором блокируется настольного приложения. |
| deviceType          | Строка | Один из следующих строк, которое указывает тип устройства, на котором блокируется классического приложения:<p/><ul><li><strong>ПК</strong></li><li><strong>Сервер</strong></li><li><strong>Планшета</strong></li><li><strong>Неизвестный</strong></li></ul> |
| blockType            | Строка | Один из следующих строк, которое указывает тип блока, обнаруженные на устройстве:<p/><ul><li><strong>Потенциальные Sediment</strong></li><li><strong>Временный Sediment</strong></li><li><strong>Уведомления среды выполнения</strong></li></ul><p/><p/>Дополнительные сведения об этих типах блоков и их значение для разработчиков и пользователей см. описание [приложение блокирует отчета](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program#application-blocks-report).  |
| architecture        | Строка | Архитектура устройства, на котором существует блока: <p/><ul><li><strong>ARM64</strong></li><li><strong>X86</strong></li></ul> |
| параметр "targetos"            | Строка | Один из следующих строк, определяющих выпуск ОС Windows 10, на котором настольного приложения, блокируются: <p/><ul><li><strong>Версия 1709</strong></li><li><strong>Версии 1803</strong></li></ul> |
| deviceCount,         | number | Количество отдельных устройств, которые имеют блоки на уровне указанные статистические. |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
     "applicationId": "10238467886765136388",
     "date": "2018-06-03",
     "productName": "Contoso Demo",
     "fileName": "contosodemo.exe",
     "applicationVersion": "2.2.2.0",
     "osVersion": "Windows 8.1",
     "osRelease": "Update 1",
     "market": "ZA",
     "deviceType": "All",
     "blockType": "Runtime Notification",
     "architecture": "X86",
     "targetOs": "RS4",
     "deviceCount": 120
    }
  ],
  "@nextLink": "desktop/blockhits?applicationId=123456789&startDate=2018-01-01&endDate=2018-02-01&top=10000&skip=10000&groupby=applicationVersion,deviceType,osVersion,osRelease",
  "TotalCount": 23012
}
```

## <a name="related-topics"></a>Статьи по теме

* [Приложение Windows Desktop](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program)
* [Возвращает сведения о блокировки для своего настольного приложения](get-desktop-block-data-details.md)
