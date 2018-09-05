---
author: Xansky
ms.assetid: 99DB5622-3700-4FB2-803B-DA447A1FD7B7
description: Этот метод можно используйте в Microsoft Store analytics API для получения данных об использовании ежедневно app для указанного диапазона дат и другие необязательные фильтры.
title: Получить ежедневного использования приложения
ms.author: mhopkins
ms.date: 08/15/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, службы хранилища, хранилища Microsoft analytics API, использование
ms.localizationpriority: medium
ms.openlocfilehash: 5060c24df7242d62e2895231d7441e904987d522
ms.sourcegitcommit: 7aa1933e6970f878faf50d59e1f799b90afd7cc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "3376130"
---
# <a name="get-daily-app-usage"></a>Получить ежедневного использования приложения

Используйте этот метод в интерфейсе API Microsoft Store analytics для получения приложения во время указанного диапазона дат (за последние 90 дней только) и другие необязательные фильтры (не включая многопользовательские Xbox) статистические данные в формате JSON. Эта информация также доступна в [отчет об использовании](../publish/usage-report.md) в панели мониторинга Центр разработчиков Windows.

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос

### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                               |
|--------|---------------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/usagedaily``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр     | Тип   |  Описание                                                                                                    |  Обязательный  |
|---------------|--------|-----------------------------------------------------------------------------------------------------------------|------------|
| applicationId | string | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для приложения, по которому требуется получить данные об отзывах. |  Да       |
| startDate     | date   | Начальная дата диапазона дат, для которого требуется получить данные об отзывах. По умолчанию используется текущая дата                   |  Нет        |
| endDate       | date   | Конечная дата диапазона дат, для которого требуется получить данные об отзывах. По умолчанию используется текущая дата                     |  Нет        |
| top           | int    | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию (если параметр не указан) — 10 000. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку «Далее», которую можно использовать для запроса следующей страницы данных                          |  Нет        |
| skip          | int    | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=10000 и skip=0 извлекаются первые 10 000 строк данных; при top=10000 и skip=10000 извлекаются следующие 10 000 строк данных и т. д.                         |  Нет        |  
| filter        |string  | Одно или несколько выражений для фильтрации строк в ответе. Каждое выражение содержит имя поля из тела ответа и значение, которое связано с помощью операторов eq или ne; выражения можно комбинировать, используя операторы and или or. В параметре filter строковые значения должны быть заключены в одиночные кавычки. Вы можете указать следующие поля из тела ответа: <ul><li>**market**</li><li>**deviceType**</li><li>**packageVersion.**</li></ul>                                                                                                                                              | Нет         |  
| orderby       | string | Выражение, которое определяет порядок полученных значений данных. Используется следующий синтаксис: <em>orderby=field [order],field [order],...</em>, где параметр <em>field</em> может принимать одно из следующих строковых значений:<ul><li>**date**</li><li>**applicationId**</li><li>**applicationName**</li><li>**market**</li><li>**packageVersion**</li><li>**deviceType**</li><li>**subscriptionName**</li><li>**dailySessionCount**</li><li>**engagementDurationMinutes**</li><li>**dailyActiveUsers**</li><li>**dailyActiveDevices**</li><li>**dailyNewUsers**</li><li>**monthlyActiveUsers**</li><li>**monthlyActiveDevices**</li><li>**monthlyNewUsers**</li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения **asc** или **desc**, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — **asc**.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p>                                                                                                   |  Нет        |
| groupby       | string | Выражение, которое применяет агрегирование данных только к указанным полям. Вы можете указать следующие поля из тела ответа: <ul><li>**applicationName**</li><li>**subscriptionName**</li><li>**deviceType**</li><li>**packageVersion**</li><li>**market**</li><li>**date**</li></ul><p>Возвращенные строки данных будут содержать поля, указанные в параметре <em>groupby</em>, а также:</p><ul><li>**applicationId**</li><li>**subscriptionName**</li><li>**dailySessionCount**</li><li>**engagementDurationMinutes**</li><li>**dailyActiveUsers**</li><li>**dailyActiveDevices**</li><li>**dailyNewUsers**</li><li>**monthlyActiveUsers**</li><li>**monthlyActiveDevices**</li><li>**monthlyNewUsers**</li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>&amp;groupby=ageGroup,market&amp;aggregationLevel=week</em></p>                                                                                                             |  Нет        |


### <a name="request-example"></a>Пример запроса

В следующем примере демонстрируется запрос на получение данных ежедневного использования приложения. Замените значение *applicationId* кодом продукта в Магазине для вашего приложения.

```http
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/usagedaily?applicationId=XXXXXXXXXXXX&startDate=2018-08-10&endDate=2018-08-14 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ



### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                                                                                                                         |
|------------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| Значение      | array  | Массив объектов, содержащих статистические данные. Дополнительные сведения о данных в каждом объекте см. в следующей таблице. |
| @nextLink  | string | При наличии дополнительных страниц данных эта строка содержит URI-адрес, который можно использовать для запроса следующей страницы данных. Например, это значение возвращается в том случае, если параметр **top** запроса имеет значение 10 000, но для данного запроса имеется больше 10 000 строк с информацией об отзывах.                 |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.                                                                          |

 
### <a name="usage-values"></a>Использование значений

Элементы в массиве *Value* содержат следующие значения.

| Значение                     | Тип    | Описание                                                               |
|---------------------------|---------|---------------------------------------------------------------------------|
| date                      | string  | Первая дата в диапазон дат для данных об использовании. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат        |
| applicationId             | string  | КОД магазина приложения, для которого необходимо извлечь данные об использовании.          |
| applicationName           | string  | Отображаемое имя приложения.                                              |
| deviceType                | string  | Одна из следующих строк, задающий тип устройства, где произошло использования:<ul><li>**Компьютер**</li><li>**Телефон**</li><li>**Console (консоль),**</li><li>**Планшет**</li><li>**IoT**</li><li>**Server**</li><li>**Holographic (голография),**</li><li>**Unknown (неизвестно).**</li></ul>                                                                                                         |
| packageVersion            | string  | Версия пакета, где произошло использование.                          |
| market                    | string  | Код страны ISO 3166 рынка, где используется приложение клиента. |
| subscriptionName          | строка  | Указывает, было ли использование через проход игры Xbox.                            |
| dailySessionCount         | long    | Число сеансов пользователя в тот же день.                                  |
| engagementDurationMinutes | double  | Минут, где активно используется приложение измеренное различных период времени начинается при запуске приложения (Начало процесса) и заканчивается при прерывании (завершение процесса) или после определенного периода бездействия пользователей.             |
| dailyActiveUsers          | long    | Количество клиентов, использующих приложение этот день.                           |
| dailyActiveDevices        | long    | Число ежедневных устройств, используемых для взаимодействия с приложения всеми пользователями.  |
| dailyNewUsers             | long    | Число клиентов, используется приложение в первый раз в этот день.    |
| monthlyActiveUsers        | long    | Количество клиентов, использующих приложение месяца.                         |
| monthlyActiveDevices      | long    | Количество устройств, выполнение приложений для различных периода времени, начинается при запуске приложения (Начало процесса) и заканчивается, когда она завершается (завершение процесса) или после определенного периода бездействия.                                      |
| monthlyNewUsers           | long    | Число клиентов, используется приложение в первый раз этот месяц.  |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2018-08-10",
      "applicationId": "XXXXXXXXXXXX",
      "applicationName": "My App",
      "market": "All",
      "packageVersion": "All",
      "deviceType": "All",
      "subscriptionName": "All",
      "dailySessionCount": 17718,
      "engagementDurationMinutes": 582540.2,
      "dailyActiveUsers": 7078,
      "dailyActiveDevices": 6964,
      "dailyNewUsers": 1727,
      "monthlyActiveUsers": 123609,
      "monthlyActiveDevices": 116723,
      "monthlyNewUsers": 72271
    },
    {
      "date": "2018-08-11",
      "applicationId": "XXXXXXXXXXXX",
      "applicationName": "My App",
      "market": "All",
      "packageVersion": "All",
      "deviceType": "All",
      "subscriptionName": "All",
      "dailySessionCount": 19899,
      "engagementDurationMinutes": 655379.7,
      "dailyActiveUsers": 7877,
      "dailyActiveDevices": 7789,
      "dailyNewUsers": 2062,
      "monthlyActiveUsers": 123666,
      "monthlyActiveDevices": 116770,
      "monthlyNewUsers": 72276
    },
    {
      "date": "2018-08-12",
      "applicationId": "XXXXXXXXXXXX",
      "applicationName": "My App",
      "market": "All",
      "packageVersion": "All",
      "deviceType": "All",
      "subscriptionName": "All",
      "dailySessionCount": 21349,
      "engagementDurationMinutes": 735640.5,
      "dailyActiveUsers": 8456,
      "dailyActiveDevices": 8309,
      "dailyNewUsers": 2433,
      "monthlyActiveUsers": 124241,
      "monthlyActiveDevices": 117289,
      "monthlyNewUsers": 72732
    }
  ],
  "@nextLink": null,
  "TotalCount": 3
}
```

## <a name="related-topics"></a>Статьи по теме

* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получить месяц ussage app](get-app-usage-monthly.md)
* [Получение сведений о покупках приложения](get-app-acquisitions.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
