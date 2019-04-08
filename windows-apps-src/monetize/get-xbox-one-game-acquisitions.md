---
ms.assetid: C1E42E8B-B97D-4B09-9326-25E968680A0F
description: Используйте этот метод в API аналитики для Microsoft Store для получения сводных данных о покупках игры для Xbox One в заданном диапазоне дат или с учетом других дополнительных фильтров.
title: Получение сведений о покупках игр на Xbox One
ms.date: 10/18/2018
ms.topic: article
keywords: windows 10, uwp, службы Store, API аналитики для Microsoft Store, покупки игры для Xbox One
ms.localizationpriority: medium
ms.openlocfilehash: 348430f7ceee66a9c4e82f258a70e57d8f344943
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57590549"
---
# <a name="get-xbox-one-game-acquisitions"></a>Получение сведений о покупках игр на Xbox One

Используйте этот метод в Microsoft Store analytics API для получения приобретения статистических данных в формате JSON для Xbox One игр, полученные через портал разработчиков Xbox (XDP) и доступны на панели мониторинга аналитики XDP.

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/acquisitions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательно  
|---------------|--------|---------------|------|
| applicationId | Строка | Код продукта для игры на Xbox One, для которой запрашиваются данные о покупках. Чтобы получить идентификатор продукта своей игры, перейдите в игру в программе Analytics XDP и получить идентификатор продукта из URL-адрес. Кроме того при загрузке данных приобретения из отчет по аналитике центра партнеров, идентификатор продукта входит в TSV-файл.  |  Да  |
| startDate | date | Начальная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата. |  Нет  |
| endDate | date | Конечная дата диапазона дат, для которого требуется получить данные о покупках. По умолчанию используется текущая дата. |  Нет  |
| top | int | Количество возвращаемых строк данных. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку "Далее", которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д. |  Нет  |
| filter | Строка  | Один или несколько операторов для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов **eq** или **ne**; выражения можно комбинировать, используя операторы **and** или **or**. В параметре *filter* строковые значения должны быть заключены в одиночные кавычки. Например, *filter=market eq 'US' and gender eq 'm'*. <p/><p/>Вы можете указать следующие поля из тела ответа:<p/><ul><li><strong>acquisitionType</strong></li><li><strong>Срок действия</strong></li><li><strong>клиентам магазинов</strong></li><li><strong>Пол</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>типа устройства</strong></li><li><strong>sandboxId</strong></li></ul> | Нет   |
| aggregationLevel | Строка | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong>. | Нет |
| orderby | Строка | Оператор, который определяет порядок полученных значений данных для каждой покупки. Используется следующий синтаксис: <em>orderby=field [порядк.],field [порядк.],...</em>. Параметр <em>field</em> может быть одной из следующих строк:<ul><li><strong>Дата</strong></li><li><strong>acquisitionType</strong></li><li><strong>Срок действия</strong></li><li><strong>клиентам магазинов</strong></li><li><strong>Пол</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>типа устройства</strong></li><li><strong>paymentInstrumentType</strong></li><li><strong>sandboxId</strong></li><li><strong>xboxTitleIdHex</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | Строка | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>Дата</strong></li><li><strong>ApplicationName</strong></li><li><strong>acquisitionType</strong></li><li><strong>возрастным группам</strong></li><li><strong>клиентам магазинов</strong></li><li><strong>Пол</strong></li><li><strong>на рынке</strong></li><li><strong>версия ОС</strong></li><li><strong>типа устройства</strong></li><li><strong>paymentInstrumentType</strong></li><li><strong>sandboxId</strong></li><li><strong>xboxTitleIdHex</strong></li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li><strong>Дата</strong></li><li><strong>Идентификатор приложения</strong></li><li><strong>Количество приобретений</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=ageGroup,market&amp;aggregationLevel=week</em></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующем примере демонстрируются несколько запросов на получение информации о покупках игры на Xbox One. Замените *applicationId* значение с Идентификатором продукта для игры.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/acquisitions?applicationId=BRRT4NJ9B3D1&startDate=1/1/2017&endDate=2/1/2017&top=10&skip=0 HTTP/1.1
Authorization: Bearer <your access token>

GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/acquisitions?applicationId=BRRT4NJ9B3D1&startDate=8/1/2017&endDate=8/31/2017&skip=0&filter=market eq 'US' and gender eq 'm' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                  |
|------------|--------|-------------------------------------------------------|
| Значение      | Массив  | Массив объектов, содержащий сводную информацию о покупках для игры. Дополнительные сведения о данных в каждом объекте см. далее в разделе [Значения информации о покупках](#acquisition-values).                                                                                                                      |
| @nextLink  | Строка | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией о покупках |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.              |


### <a name="acquisition-values"></a>Значения в информации о покупках

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип   | Описание                           |
|---------------------|--------|-------------------------------------------|
| date                | Строка | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| applicationId       | Строка | Код продукта для игры на Xbox One, для которой запрашиваются данные о покупках. |
| applicationName     | Строка | Отображаемое имя игры.       |
| acquisitionType     | Строка | Одна из следующих строк, указывающих тип приобретения:<ul><li><strong>бесплатно</strong></li><li><strong>Пробная версия</strong></li><li><strong>Платные</strong></li><li><strong>Код акции</strong></li><li><strong>Iap</strong></li><li><strong>Подписка Iap</strong></li><li><strong>Частные аудитории</strong></li><li><strong>Pre заказа</strong></li><li><strong>Xbox Game Pass</strong> (или <strong>Game Pass</strong> при запросе данных до 23 марта 2018 г.)</li><li><strong>диск</strong></li><li><strong>Предоплаченные кода</strong></li><li><strong>Оплата за Pre заказа</strong></li><li><strong>Отмененный заказ Pre</strong></li><li><strong>Невыполненных заказов Pre</strong></li></ul>    |
| age                 | Строка | Одна из следующих строк, указывающая возрастную группу пользователя, совершившего приобретение:<ul><li><strong>меньше, чем 13</strong></li><li><strong>13-17</strong></li><li><strong>18-24</strong></li><li><strong>25-34</strong></li><li><strong>35-44</strong></li><li><strong>44 55</strong></li><li><strong>больше, чем 55</strong></li><li><strong>Неизвестный</strong></li></ul>     |
| deviceType          | Строка | Одна из следующих строк, указывающая тип устройства, на котором было выполнено приобретение:<ul><li><strong>ПК</strong></li><li><strong>Телефон</strong></li><li><strong>Консоль</strong></li><li><strong>Интернета вещей</strong></li><li><strong>Сервер</strong></li><li><strong>Планшета</strong></li><li><strong>Holographic</strong></li><li><strong>Неизвестный</strong></li></ul>  |
| gender              | Строка | Одна из следующих строк, указывающая пол пользователя, совершившего приобретение:<ul><li><strong>m</strong></li><li><strong>F</strong></li><li><strong>Неизвестный</strong></li></ul>     |
| market              | Строка | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка  |
| osVersion           | Строка | Версия ОС, в которой произведена покупка. Для этого метода это значение всегда равно **Windows 10**.</li></ul>    |
| paymentInstrumentType           | Строка | Одна из следующих строк, указывающая платежную инструкцию, использовавшуюся для приобретения:<ul><li><strong>Кредитная карта</strong></li><li><strong>Прямой дебетовой карты</strong></li><li><strong>Выводимые покупки</strong></li><li><strong>Баланс MS</strong></li><li><strong>Оператор мобильной связи</strong></li><li><strong>Online банковский перевод</strong></li><li><strong>PayPal</strong></li><li><strong>Операция разбиения</strong></li><li><strong>Маркер активации</strong></li><li><strong>Ноль выплаченная сумма</strong></li><li><strong>eWallet</strong></li><li><strong>Неизвестный</strong></li></ul>    |
| sandboxId              | Строка | Идентификатор песочницы, созданный для этой игры. Это может быть значение **RETAIL** или идентификатор для частной песочницы.  |
| storeClient         | Строка | Одна из следующих строк, указывающая версию Store, где совершено приобретение:<ul><li>**Windows Phone Store (клиент)**</li><li>**Microsoft Store (client)** (или **Windows Store (client)**, если запрашиваются данные до 23 марта 2018 г.)</li><li>**Microsoft Store (web)** (или **Windows Store (web)**, если запрашиваются данные до 23 марта 2018 г.)</li><li>**Закупка организациями**</li><li>**Другие**</li></ul>                             |
| xboxTitleIdHex              | Строка | Идентификатор продукта Xbox Live (представлен в шестнадцатеричной системе счисления), назначенный порталом разработчиков для Xbox (XDP) для игр с поддержкой Xbox Live.  |
| acquisitionQuantity | number | Число покупок, выполненных на указанном уровне агрегирования     |
| purchasePriceUSDAmount | number | Сумма, уплаченная пользователем за приобретение с конвертацией в доллары США по ежемесячно обновляемому обменному курсу.    |
| taxUSDAmount     | number | Сумма налога, применяемого к приобретению, с конвертацией в доллары США. |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2017-02-01",
      "applicationId": "BRRT4NJ9B3D1 ",
      "applicationName": "Contoso Game",
      "acquisitionType": "Paid",
      "age": "35-49",
      "deviceType": "Console",
      "gender": "m",
      "market": "US",
      "osVersion": "Windows 10",
      "PaymentInstrumentType": "Credit Card ",
      "sandboxId": "RETAIL",
      "storeClient": "Windows Store (web)",
      "xboxTitleIdHex": "",
      "acquisitionQuantity": 1,
      "purchasePriceUSDAmount": 29.99,
      "taxUSDAmount": 2.99
    }
  ],
  "@nextLink": "xbox/acquisitions?applicationId=BRRT4NJ9B3D1&aggregationLevel=day&startDate=2017/02/01&endDate=2017/03/01&top=1&skip=1&orderby=date desc",
  "TotalCount": 39812
}
```

## <a name="related-topics"></a>Статьи по теме

* [Доступ к данным аналитики с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
