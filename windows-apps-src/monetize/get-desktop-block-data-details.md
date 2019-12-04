---
description: Используйте этот универсальный код ресурса (URI) для получения сведений о блокировании для классического приложения в течение заданного диапазона дат и других необязательных фильтров.
title: Получение сведений о блоке обновления для приложения
ms.date: 07/11/2018
ms.topic: article
keywords: Windows 10, блоки классических приложений, программа Windows для настольных приложений
localizationpriority: medium
ms.openlocfilehash: b30594ff3943d1ed9183190a0b5a43824816c080
ms.sourcegitcommit: ae9c1646398bb5a4a888437628eca09ae06e6076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74735119"
---
# <a name="get-upgrade-block-details-for-your-desktop-application"></a>Получение сведений о блоках обновлений для классического приложения

Используйте этот универсальный код ресурса (URI) для получения сведений о устройствах Windows 10, на которых конкретный исполняемый файл в настольном приложении блокирует выполнение обновления Windows 10. Этот URI можно использовать только для настольных приложений, добавленных в [классическую программу Windows](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program). Эти сведения также доступны в [отчете Application Blocks](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program#application-blocks-report) для классических приложений в центре партнеров.

Этот URI похож на [получение блоков обновления для настольного приложения](get-desktop-block-data.md), но возвращает сведения о блоке устройства для определенного исполняемого файла в классическом приложении.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/blockdetails```|


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Введите   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в формате **носителя** &lt;*токен*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Введите   |  Описание      |  Обязательный  
|---------------|--------|---------------|------|
| applicationId | Строка | Идентификатор продукта для классического приложения, для которого требуется получить данные блока. Чтобы получить идентификатор продукта для классического приложения, откройте любой [аналитический отчет для настольного приложения в центре партнеров](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program) (например, **отчет о блоках**) и получите идентификатор продукта из URL-адреса. |  "Да",  |
| fileName | Строка | Имя заблокированного исполняемого файла |
| startDate | date | Начальная дата в диапазоне данных блока, который необходимо получить. Значение по умолчанию — 90 дней до текущей даты. |  Нет  |
| endDate | date | Конечная дата в диапазоне данных блока, которые необходимо получить. По умолчанию используется текущая дата. |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | Строка  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>аппликатионверсион</strong></li><li><strong>AHA</strong></li><li><strong>блокктипе</strong></li><li><strong>Типа устройства</strong></li><li><strong>звонк</strong></li><li><strong>osRelease</strong></li><li><strong>osVersion</strong></li><li><strong>productName</strong></li><li><strong>таржетос</strong></li></ul> | Нет   |
| orderby | Строка | Инструкция, которая упорядочивает значения результирующих данных для каждого блока. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>, где параметр <em>field</em> может принимать одно из следующих значений полей из тела ответа:<p/><ul><li><strong>аппликатионверсион</strong></li><li><strong>AHA</strong></li><li><strong>блокктипе</strong></li><li><strong>date</strong><li><strong>Типа устройства</strong></li><li><strong>звонк</strong></li><li><strong>osRelease</strong></li><li><strong>osVersion</strong></li><li><strong>productName</strong></li><li><strong>таржетос</strong></li><li><strong>девицекаунт</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | Строка | Выражение, которое применяет агрегирование данных только к указанным полям. Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>аппликатионверсион</strong></li><li><strong>AHA</strong></li><li><strong>блокктипе</strong></li><li><strong>Типа устройства</strong></li><li><strong>звонк</strong></li><li><strong>osRelease</strong></li><li><strong>osVersion</strong></li><li><strong>таржетос</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>Кодов</strong></li><li><strong>date</strong></li><li><strong>productName</strong></li><li><strong>девицекаунт</strong></li></ul></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующем примере показано несколько запросов на получение данных о блоках приложений для настольных систем. Замените значение *APPLICATIONID* идентификатором продукта для классического приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/blockdetails?applicationId=10238467886765136388&fileName=contoso.exe&startDate=2018-05-01&endDate=2018-06-07&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/blockdetails?applicationId=10238467886765136388&fileName=contoso.exe&startDate=2018-05-01&endDate=2018-06-07&filter=market eq 'US' and deviceType eq 'PC' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Текст ответа

| Value      | Введите   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Value      | Массив  | Массив объектов, содержащих статистические данные блока. Дополнительные сведения о данных в каждом объекте см. в следующей таблице. |
| @nextLink  | Строка | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается, если параметр **Top** запроса имеет значение 10000, но имеется более 10000 строк блочных данных для запроса. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса. |


Элементы в массиве *Value* содержат следующие значения.

| Value               | Введите   | Описание                           |
|---------------------|--------|-------------------------------------------|
| applicationId       | Строка | Идентификатор продукта для классического приложения, для которого были получены данные блока. |
| date                | Строка | Дата, связанная с числом попаданий в блок. |
| productName         | Строка | Отображаемое имя классического приложения, как оно получено из метаданных его связанных исполняемых файлов. |
| fileName            | Строка | Заблокированный исполняемый файл. |
| applicationVersion  | Строка | Версия заблокированного исполняемого файла приложения. |
| osVersion           | Строка | Одна из следующих строк, указывающая версию ОС, в которой в данный момент работает классическое приложение:<p/><ul><li><strong>Windows 7</strong></li><li><strong>Windows 8.1</strong></li><li><strong>Windows 10</strong></li><li><strong>Windows Server 2016</strong></li><li><strong>Windows Server 1709</strong></li><li><strong>Неизвестный</strong></li></ul>   |
| osRelease           | Строка | Одна из следующих строк, указывающая выпуск ОС или выходное кольцо (в виде подсистемы заполнения в версии ОС), в которой выполняется приложение для настольных компьютеров.<p/><p>Для Windows 10.</p><ul><li><strong>Версия 1507</strong></li><li><strong>Версия 1511</strong></li><li><strong>Версия 1607</strong></li><li><strong>Версия 1703</strong></li><li><strong>Версия 1709</strong></li><li><strong>Предварительная версия</strong></li><li><strong>Быстрый взгляд</strong></li><li><strong>Программа предварительной оценки работает слишком долго</strong></li></ul><p/><p>Для Windows Server 1709</p><ul><li><strong>RTM</strong></li></ul><p>Для Windows Server 2016</p><ul><li><strong>Версия 1607</strong></li></ul><p>Для Windows 8.1.</p><ul><li><strong>Обновление 1</strong></li></ul><p>Для Windows 7.</p><ul><li><strong>Пакет обновления 1 (SP1)</strong></li></ul><p>Если выпуск операционной системы или цикл фокус-тестирования неизвестны, это поле имеет значение <strong>Unknown</strong>.</p> |
| market              | Строка | Код страны ISO 3166, на которой заблокировано настольное приложение. |
| deviceType          | Строка | Одна из следующих строк, указывающая тип устройства, на котором блокируется настольное приложение:<p/><ul><li><strong>ACPI</strong></li><li><strong>Сервером</strong></li><li><strong>Помощью</strong></li><li><strong>Неизвестный</strong></li></ul> |
| блокктипе            | Строка | Одна из следующих строк, указывающая тип блока, найденного на устройстве:<p/><ul><li><strong>Потенциальные седимент</strong></li><li><strong>Временный седимент</strong></li><li><strong>Уведомление среды выполнения</strong></li></ul><p/><p/>Дополнительные сведения об этих типах блоков и о том, что они означают для разработчиков и пользователей, см. в описании [отчета о блокировке приложения](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program#application-blocks-report). |
| architecture        | Строка | Архитектура устройства, на котором существует блок: <p/><ul><li><strong>ARM64</strong></li><li><strong>С</strong></li></ul> |
| таржетос            | Строка | Одна из следующих строк, указывающая выпуск ОС Windows 10, на котором блокируется запуск приложения для настольных систем: <p/><ul><li><strong>Версия 1709</strong></li><li><strong>Версия 1803</strong></li></ul> |
| deviceCount         | number | Количество уникальных устройств с блоками на указанном уровне агрегирования. |


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
  "@nextLink": "desktop/blockdetails?applicationId=123456789&startDate=2018-01-01&endDate=2018-02-01&top=10000&skip=10000&groupby=applicationVersion,deviceType,osVersion,osRelease",
  "TotalCount": 23012
}
```

## <a name="related-topics"></a>Статьи по теме

* [Приложение для настольных приложений Windows](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program)
* [Получение блоков обновления для классического приложения](get-desktop-block-data.md)
