---
author: mcleanbyron
ms.assetid: dc632a4c-ce48-400b-8e6e-1dddbd13afff
description: "Используйте этот метод в API рекламных акций Магазина Windows для управления линиями поставки в рекламных кампаниях."
title: "Управление линиями поставки для рекламных кампаний"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, API рекламных акций Магазина Windows, uwp, рекламные кампании"
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 5f9ede6a2e645a644e4650f3af7e5476bd52dd53
ms.lasthandoff: 02/08/2017

---

# <a name="manage-delivery-lines-for-ad-campaigns"></a>Управление линиями поставки для рекламных кампаний

Используйте эти методы в API рекламных акций магазина Windows для создания одной или нескольких *линий поставки* для приобретения товаров и поставки своих рекламных объявлений для рекламных кампаний. Для каждой линии поставки можно задать таргетинг, установить ставку цены и решить, сколько вы хотите тратить, установив бюджет, а также указать ссылки на рекламные материалы, которые вы хотите использовать.

Дополнительные сведения о связи между линиями поставки, кампаниями, целевыми профилями и рекламными материалами см. в разделе [Проведение рекламных кампаний с помощью служб Магазина Windows](run-ad-campaigns-using-windows-store-services.md#call-the-windows-store-promotions-api).

## <a name="prerequisites"></a>Предварительные условия

Для использования этих методов сначала необходимо сделать следующее.

* Если вы еще не сделали этого, выполните все [необходимые условия](run-ad-campaigns-using-windows-store-services.md#prerequisites) для API рекламных акций Магазина Windows.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этих методов. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос

Эти методы имеют следующие URI.

| Тип метода | Универсальный код ресурса (URI) запроса         |  Описание  |
|--------|---------------------------|---------------|
| POST   | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/line``` |  Создает новую линию поставки.  |
| PUT    | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/line/{lineId}``` |  Редактирует линию поставки, заданную *lineId*.  |
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/line/{lineId}``` |  Получает строку поставки, заданную *lineId*.  |

<span/> 
### <a name="header"></a>Заголовок

| Заголовок        | Тип   | Описание         |
|---------------|--------|---------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |
| Tracking ID   | Код GUID   | Необязательный параметр. Идентификатор, который отслеживает поток вызовов.                                  |

<span/>
### <a name="request-body"></a>Текст запроса

Методы POST и PUT требуют использовать тело запроса JSON с обязательными полями объекта [Линия поставки](#line) и любыми другими полями, которые вы хотите установить или изменить.
 
<span/>
### <a name="request-examples"></a>Примеры запросов

Следующий пример демонстрирует вызов метода POST для создания линии поставки.

```json
POST https://manage.devcenter.microsoft.com/v1.0/my/promotion/line HTTP/1.1
Authorization: Bearer <your access token>

{
    "name": "Contoso App Campaign - Paid Line",
    "configuredStatus": "Active",
    "startDateTime": "2017-01-19T12:09:34Z",
    "endDateTime": "2017-01-31T23:59:59Z",
    "bidAmount": 0.4,
    "dailyBudget": 20,
    "targetProfileId": {
        "id": 310021746
    },
    "creatives": [
        {
            "id": 106851
        }
    ],
    "campaignId": 31043481,
    "minMinutesPerImp ": 1
}
```

Следующий пример демонстрирует вызов метода GET для получения линии поставки.

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/promotion/line/31019990  HTTP/1.1
Authorization: Bearer <your access token>
```

<span/> 
## <a name="response"></a>Ответ

Эти методы возвращают тело ответа JSON с объектом [Линия поставки](#line), содержащим сведения о линии поставки, которая была создана, обновлена или получена. В следующем примере показано тело ответа для этих методов.

```json
{
    "Data": {
        "id": 31043476,
        "name": "Contoso App Campaign - Paid Line",
        "configuredStatus": "Active",
        "effectiveStatus": "Active",
        "effectiveStatusReasons": [
            "{\"ValidationStatusReasons\":null}"
        ],
        "startDateTime": "2017-01-19T12:09:34Z",
        "endDateTime": "2017-01-31T23:59:59Z",
        "createdDateTime": "2017-01-17T10:28:34Z",
        "bidType": "CPM",
        "bidAmount": 0.4,
        "dailyBudget": 20,
        "targetProfileId": {
            "id": 310021746
        },
        "creatives": [
            {
                "id": 106126
            }
        ],
        "campaignId": 31043481,
        "minMinutesPerImp ": 1,
        "pacingType ": "SpendEvenly",
        "currencyId ": 732
    }
}

```

<span id="line"/>
## <a name="delivery-line-object"></a>Объект линии поставки

Для этих методов тела запроса и ответа содержат следующие поля. В этой таблице показаны поля, которые доступны только для чтения (это означает, что они не могут изменяться в методе PUT), и поля, которые необходимы в теле запроса для методов POST или PUT.

| Поле        | Тип   |  Описание      |  Только чтение  | По умолчанию  | Обязательный для POST/PUT |   
|--------------|--------|---------------|------|-------------|------------|
|  id   |  целое число   |  Идентификатор линии поставки.     |   Да    |      |  Нет      |    
|  name   |  строка   |   Имя линии поставки.    |    Нет   |      |  POST     |     
|  configuredStatus   |  строка   |  Одно из следующих значений, которое указывает статус линии поставки, заданный разработчиком: <ul><li>**Active**</li><li>**Inactive**</li></ul>     |  Нет     |      |   POST    |       
|  effectiveStatus   |  строка   |   Одно из следующих значений, определяющих действующий статус линии поставки, в зависимости от проверки системы: <ul><li>**Active**</li><li>**Inactive**</li><li>**Processing**</li><li>**Failed**</li></ul>    |    Да   |      |  Нет      |      
|  effectiveStatusReasons   |  массив   |  Одно или несколько из следующих значений, задающих причину нынешнего состояния линии поставки: <ul><li>**AdCreativesInactive**</li><li>**ValidationFailed**</li></ul>      |  Да     |     |    Нет    |           
|  startDatetime   |  строка   |  Дата и время начала для линии поставки в формате ISO 8601. Невозможно изменить это значение, если оно уже в прошлом.     |    Нет   |      |    POST, PUT     |
|  endDatetime   |  строка   |  Дата и время окончания для линии поставки в формате ISO 8601. Невозможно изменить это значение, если оно уже в прошлом.     |   Нет    |      |  POST, PUT     |
|  createdDatetime   |  строка   |  Дата и время создания линии поставки в формате ISO 8601.      |    Да   |      |  Нет      |
|  bidType   |  строка   |  Значение, указывающее тип ставки для линии поставки. На данный момент единственным поддерживаемым значением является **CPM**.      |    Нет   |  CPM    |  Нет     |
|  bidAmount   |  decimal   |  Сумма ставки для использования при торге за любой запрос рекламного объявления.      |    Нет   |  Среднее значение CPM в зависимости от целевых рынков (это значение периодически пересматривается).    |    Нет    |  
|  dailyBudget   |  decimal   |  Ежедневный бюджет для линии поставки. Одно из значений *dailyBudget* и *lifetimeBudget* должно быть задано.      |    Нет   |      |   POST, PUT (если *lifetimeBudget* не задано)       |
|  lifetimeBudget   |  decimal   |   Полный бюджет для линии поставки. Одно из значений dailyBudget* и *lifetimeBudget должно быть задано.      |    Нет   |      |   POST, PUT (если *dailyBudget* не задано)    |
|  targetingProfileId   |  объект   |  На объекте, который определяет [профиль таргетинга](manage-targeting-profiles-for-ad-campaigns.md#targeting-profile), описывающий пользователей, регионы и типы продуктов, по которым вы хотите производить таргетинг для этой линии поставки. Этот объект состоит из одного поля *id*, которое определяет идентификатор профиля таргетинга.     |    Нет   |      |  Нет      |  
|  creatives   |  массив   |  Один или несколько объектов, представляющих [рекламные материалы](manage-creatives-for-ad-campaigns.md#creative), связанные с линией поставки. Каждый объект в этом поле состоит из одного поля *id*, которое определяет идентификатор рекламного материала.      |    Нет   |      |   Нет     |  
|  campaignId   |  целое число   |  Идентификатор вышестоящей рекламной кампании.      |    Нет   |      |   Нет     |  
|  minMinutesPerImp   |  целое число   |  Определяет минимальный интервал времени (в минутах) между двумя показами одному пользователю из этой линии поставки.      |    Нет   |  4000    |  Нет      |  
|  pacingType   |  строка   |  Одно из следующих значений, определяющих темп расходования: <ul><li>**SpendEvenly**</li><li>**SpendAsFastAsPossible**</li></ul>      |    Нет   |  SpendEvenly    |  Нет      |
|  currencyId   |  целое число   |  Код валюты для кампании.      |    Да   |  Валюта учетной записи разработчика (от вас не требуется указывать это поле в вызовах PUT или POST).    |   Нет     |      |


## <a name="related-topics"></a>Статьи по теме

* [Проведение рекламных кампаний с помощью служб Магазина Windows](run-ad-campaigns-using-windows-store-services.md)
* [Управление рекламными кампаниями](manage-ad-campaigns.md)
* [Управление профилями таргетинга рекламных кампаний](manage-targeting-profiles-for-ad-campaigns.md)
* [Управление рекламными средствами для рекламных кампаний](manage-creatives-for-ad-campaigns.md)
* [Получение данных об эффективности рекламной кампании](get-ad-campaign-performance-data.md)

