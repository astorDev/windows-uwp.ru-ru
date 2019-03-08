---
description: Этот метод можно используйте в API анализа Microsoft Store для получения данных приобретения подписки надстройки во время указанного диапазона дат и другие необязательные фильтры.
title: Получение сведений о приобретениях надстройки с подпиской
ms.date: 01/25/18
ms.topic: article
keywords: Windows 10, uwp, службы Store, API, подписок анализа Microsoft Store
ms.openlocfilehash: e33a3ded219fb4d223137b40ebe871f66589addf
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57594779"
---
# <a name="get-subscription-add-on-acquisitions"></a>Получение сведений о приобретениях надстройки с подпиской

Этот метод можно используйте в API анализа Microsoft Store для получения данных агрегатные приобретения подписки надстройки для приложения во время указанного диапазона дат и другие необязательные фильтры.

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса                                                                |
|--------|----------------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/subscriptions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание          |
|---------------|--------|--------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательно  
|---------------|--------|---------------|------|
| applicationId | Строка | [Store ID](in-app-purchases-and-trials.md#store-ids) приложения, для которого требуется извлечь данные о приобретении подписки надстройки. |  Да  |
| subscriptionProductId  | Строка | [Store ID](in-app-purchases-and-trials.md#store-ids) надстройки подписки, для которого требуется извлечь данные о приобретении. Если это значение не указано, этот метод возвращает данные о приобретении для всех надстроек подписки для указанного приложения.  | Нет  |
| startDate | date | Дата начала в диапазоне данных приобретения подписки надстройки для получения. По умолчанию используется текущая дата. |  Нет  |
| endDate | date | Дата окончания в диапазоне данных приобретения подписки надстройки для получения. По умолчанию используется текущая дата. |  Нет  |
| top | int | Количество строк данных, возвращаемых в запросе. Максимальное значение и значение по умолчанию, если не указан — 100. Если в запросе содержится больше строк, то тело ответа будет содержать ссылку "Далее", которую можно использовать для запроса следующей страницы данных |  Нет  |
| skip | int | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по большим наборам данных. Например, при top=100 и skip=0 извлекаются первые 100 строк данных; при top=100 и skip=100 извлекаются следующие 100 строк данных и т. д. |  Нет  |
| filter | Строка  | Одно или несколько выражений для фильтрации текста ответа. Каждый оператор может использовать операторы **eq** или **ne**; кроме того, операторы можно объединять с помощью **и** или **или**. Можно указать следующие строки в инструкции фильтра (они соответствуют [значения в тексте ответа](#subscription-acquisition-values)): <ul><li><strong>Дата</strong></li><li><strong>subscriptionProductName</strong></li><li><strong>ApplicationName</strong></li><li><strong>SkuId</strong></li><li><strong>на рынке</strong></li><li><strong>типа устройства</strong></li></ul><p>Вот пример *фильтра* параметр: <em>фильтра = Дата eq "2017-07-08"</em>.</p> | Нет   |
| aggregationLevel | Строка | Определяет диапазон времени, для которого требуется получить сводные данные. Можно использовать следующие строки: <strong>day</strong>, <strong>week</strong> или <strong>month</strong>. Если параметр не задан, значение по умолчанию — <strong>day</strong>. | Нет |
| orderby | Строка | Оператор, производится упорядочение результирующего значения данных для каждого приобретения подписки надстройки. Используется следующий синтаксис: <em>orderby=field [порядк.],field [порядк.],...</em>. Параметр <em>field</em> может быть одной из следующих строк:<ul><li><strong>Дата</strong></li><li><strong>subscriptionProductName</strong></li><li><strong>ApplicationName</strong></li><li><strong>SkuId</strong></li><li><strong>на рынке</strong></li><li><strong>типа устройства</strong></li></ul><p>Параметр <em>order</em> является необязательным и может принимать значения <strong>asc</strong> или <strong>desc</strong>, которые указывают, соответственно, порядок сортировки по возрастанию или по убыванию для каждого поля. Значение по умолчанию — <strong>asc</strong>.</p><p>Пример: строка <em>orderby</em>: <em>orderby=date,market</em></p> |  Нет  |
| groupby | Строка | Оператор, который применяет агрегирование данных только к указанным полям. Можно указать следующие поля:<ul><li><strong>Дата</strong></li><li><strong>subscriptionProductName</strong></li><li><strong>ApplicationName</strong></li><li><strong>SkuId</strong></li><li><strong>на рынке</strong></li><li><strong>типа устройства</strong></li></ul><p>Параметр <em>groupby</em> можно использовать вместе с параметром <em>aggregationLevel</em>. Например: <em>groupby = рынка&amp;aggregationLevel = неделя</em></p> |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующем примере показано, как получить данные о приобретении подписки надстройки. Замените *applicationId* значение с соответствующий Идентификатором Store для приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/subscriptions?applicationId=9NBLGGGZ5QDR&startDate=2017-07-07&endDate=2017-07-08 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание         |
|------------|--------|------------------|
| Значение      | Массив  | Массив объектов, которые содержат данные о приобретении агрегатные подписки надстройки. Дополнительные сведения о данных в каждом объекте, см. в разделе [значения приобретения подписки](#subscription-acquisition-values) разделе ниже.             |
| @nextLink  | Строка | При наличии дополнительных страниц данных эта строка содержит универсальный код ресурса (URI), который можно использовать для запроса следующей страницы данных. Например, это значение возвращается, если **верхней** параметр запроса имеет значение 100, но есть более чем 100 строк данных приобретения подписки надстройки для запроса. |
| TotalCount | int    | Общее количество строк в результирующих данных для запроса.       |


<span id="subscription-acquisition-values" />

### <a name="subscription-acquisition-values"></a>Значения приобретения подписки

Элементы в массиве *Value* содержат следующие значения.

| Значение               | Тип    | Описание        |
|---------------------|---------|---------------------|
| date                | Строка  | Первая дата в диапазоне дат, для которого требуется получить данные о покупках. Если в запросе указан один день, это значение равно соответствующей дате. Если запрос указывает неделю, месяц или другой диапазон дат, это значение равно первой дате в этом диапазоне дат |
| subscriptionProductId      | Строка  | [Store ID](in-app-purchases-and-trials.md#store-ids) надстройки подписки, для которого извлекаются данные о приобретении.    |
| subscriptionProductName    | Строка  | Отображаемое имя подписки надстройки.         |
| applicationId       | Строка  | [Store ID](in-app-purchases-and-trials.md#store-ids) приложения, для которого извлекаются данные о приобретении подписки надстройки.   |
| applicationName     | Строка  | Отображаемое имя приложения.     |
| skuId     | Строка  | Идентификатор [SKU](in-app-purchases-and-trials.md#products-skus) надстройки подписки, для которого извлекаются данные о приобретении.     |
| deviceType          | Строка  |  Одна из следующих строк, указывающая тип устройства, на котором было выполнено приобретение:<ul><li><strong>ПК</strong></li><li><strong>Телефон</strong></li><li><strong>Консоль</strong></li><li><strong>Интернета вещей</strong></li><li><strong>Holographic</strong></li><li><strong>Неизвестный</strong></li></ul>       |
| market           | Строка  | Код страны по стандарту ISO 3166 для рынка, на котором произошла покупка     |
| currencyCode         | Строка  | Код валюты в формате ISO 4217 для валовые продажи до налогов.       |
| grossSalesBeforeTax           | целое число  | Валовые продажи в местной валюте, определяемое *currencyCode* значение.     |
| totalActiveCount             | целое число  | Число общее активные подписки во время заданного периода времени. Это эквивалентно сумму *goodStandingActiveCount*, *pendingGraceActiveCount*, *graceActiveCount*, и *lockedActiveCount* значения.  |
| totalChurnCount              | целое число  | Общее число подписок, которые были отключены во время заданного периода времени. Это эквивалентно сумму *billingChurnCount*, *nonRenewalChurnCount*, *refundChurnCount*, *chargebackChurnCount*, *earlyChurnCount*, и *otherChurnCount* значения.   |
| newCount            | целое число  | Число новых приобретения подписки в течение указанного периода времени, включая пробные версии.    |
| renewCount     | целое число  | Число обновление подписки в течение указанного периода времени, включая продление, инициированного пользователем и автоматического обновления.        |
| goodStandingActiveCount | целое число | Число подписок, которые были активны во время заданного периода времени и где дату истечения срока действия > = *endDate* значение для запроса.    |
| pendingGraceActiveCount | целое число | Число подписок, были активны в течение указанного периода времени, однако сбой выставления счетов и где дата окончания срока действия подписки > = *endDate* значение для запроса.     |
| graceActiveCount | целое число | Число подписок, которые были активны в течение указанного периода времени, но сбой выставления счетов, и где:<ul><li>Дата окончания срока действия подписки — < *endDate* значение для запроса.</li><li>Окончание периода отсрочки — > = *endDate* значение.</li></ul>        |
| lockedActiveCount | целое число | Число подписок, которые были в *dunning* (то есть подписки близка к истечения срока действия и пытается получить средства автоматического возобновления действия подписки Microsoft) в заданное время период, а также где:<ul><li>Дата окончания срока действия подписки — < *endDate* значение для запроса.</li><li>Окончание периода отсрочки — < = *endDate* значение.</li></ul>        |
| billingChurnCount | целое число | Число подписок, были отключены во время заданного периода времени, из-за сбоя для обработки расчетный счет и где подписки были ранее в dunning.        |
| nonRenewalChurnCount | целое число | Число подписок, которые были отключены во время заданного периода времени, так как они не были обновлены.        |
| refundChurnCount | целое число | Число подписок, которые были отключены во время заданного периода времени, так как они были компенсируются.        |
| chargebackChurnCount | целое число | Число подписок, которые были отключены во время заданного периода времени, из-за взимания средств за использование.        |
| earlyChurnCount | целое число | Число подписок, которые были отключены во время заданного периода времени, пока они работали в хорошем состоянии.        |
| otherChurnCount | целое число | Число подписок, которые были отключены в течение указанного периода времени по другим причинам.        |


### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Value": [
    {
      "date": "2017-07-08",
      "subscriptionProductId": "9KDLGHH6R365",
      "subscriptionProductName": "Contoso App Subscription with One Month Free Trial",
      "applicationId": "9NBLGGH4R315",
      "applicationName": "Contoso App",
      "skuId": "0020",
      "market": "Unknown",
      "deviceType": "PC",
      "currencyCode": "USD",
      "grossSalesBeforeTax": 0.0,
      "totalActiveCount": 1,
      "totalChurnCount": 0,
      "newCount": 0,
      "renewCount": 0,
      "goodStandingActiveCount": 1,
      "pendingGraceActiveCount": 0,
      "graceActiveCount": 0,
      "lockedActiveCount": 0,
      "billingChurnCount": 0,
      "nonRenewalChurnCount": 0,
      "refundChurnCount": 0,
      "chargebackChurnCount": 0,
      "earlyChurnCount": 0,
      "otherChurnCount": 0
    },
    {
      "date": "2017-07-08",
      "subscriptionProductId": "9JJFDHG4R478",
      "subscriptionProductName": "Contoso App Monthly Subscription",
      "applicationId": "9NBLGGH4R315",
      "applicationName": "Contoso App",
      "skuId": "0020",
      "market": "US",
      "deviceType": "PC",
      "currencyCode": "USD",
      "grossSalesBeforeTax": 0.0,
      "totalActiveCount": 1,
      "totalChurnCount": 0,
      "newCount": 0,
      "renewCount": 0,
      "goodStandingActiveCount": 1,
      "pendingGraceActiveCount": 0,
      "graceActiveCount": 0,
      "lockedActiveCount": 0,
      "billingChurnCount": 0,
      "nonRenewalChurnCount": 0,
      "refundChurnCount": 0,
      "chargebackChurnCount": 0,
      "earlyChurnCount": 0,
      "otherChurnCount": 0
    }
  ],
  "@nextLink": null,
  "TotalCount": 2
}
```

## <a name="related-topics"></a>Статьи по теме

* [Отчет о приобретении надстроек](../publish/add-on-acquisitions-report.md)
* [Доступ к данным аналитики с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)

 

 
