---
author: mcleanbyron
ms.assetid: 7b07a6ca-4be1-497c-a901-0a2da3762555
description: "Используйте этот метод в API рекламных акций Магазина Windows для создания, редактирования и получения рекламных кампаний."
title: "Управление рекламными кампаниями"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, API рекламных акций Магазина Windows, uwp, рекламные кампании"
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: bde9588176c1e52ccab169ad3f51ad15781e06ee
ms.lasthandoff: 02/08/2017

---

# <a name="manage-ad-campaigns"></a>Управление рекламными кампаниями

Используйте эти методы в [API рекламных акций Магазина Windows](run-ad-campaigns-using-windows-store-services.md) для создания, редактирования и получения рекламных кампаний для вашего приложения. Каждую создаваемую вами с помощью этого метода кампанию можно связать только с одним приложением.

>**Примечание**.&nbsp;&nbsp;Также можно создавать рекламные кампании и управлять ими с помощью информационной панели Центра разработки для Windows, а доступ к создаваемым программным путем кампаниям можно также получить на информационной панели. Дополнительные сведения об управлении рекламными кампаниями на информационной панели см. в разделе [Создание рекламной кампании для вашего приложения](../publish/create-an-ad-campaign-for-your-app.md).

Если вы используете эти методы для создания и обновления кампании, вы обычно также вызываете один или несколько из следующих методов для управления *линиями поставки*, *профилями таргетинга* и *рекламными материалами*, которые связаны с этой кампанией. Дополнительные сведения о связи между кампаниями, линиями поставки, профилями таргетинга и рекламными материалами см. в разделе [Проведение рекламных кампаний с помощью служб Магазина Windows](run-ad-campaigns-using-windows-store-services.md#call-the-windows-store-promotions-api).

* [Управление линиями поставки для рекламных кампаний](manage-delivery-lines-for-ad-campaigns.md)
* [Управление профилями таргетинга рекламных кампаний](manage-targeting-profiles-for-ad-campaigns.md)
* [Управление рекламными материалами для рекламных кампаний](manage-creatives-for-ad-campaigns.md)

## <a name="prerequisites"></a>Предварительные условия

Для использования этих методов сначала необходимо сделать следующее.

* Если вы еще не сделали этого, выполните все [необходимые условия](run-ad-campaigns-using-windows-store-services.md#prerequisites) для API рекламных акций Магазина Windows.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этих методов. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия маркера можно получить новый маркер.

<span/> 
## <a name="request"></a>Запрос

Эти методы имеют следующие URI.

| Тип метода | Универсальный код ресурса (URI) запроса                                                      |  Описание  |
|--------|------------------------------------------------------------------|---------------|
| POST   | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/campaign``` |  Создает новую рекламную кампанию.  |
| PUT    | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/campaign/{campaignId}``` |  Редактирует рекламную кампанию, заданную *campaignId*.  |
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/campaign/{campaignId}``` |  Получает рекламную кампанию, заданную *campaignId*.  |
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/campaign``` |  Запросы для рекламных кампаний. В разделе [Параметры](#parameters) приведены поддерживаемые параметры запросов.  |

<span/> 
### <a name="header"></a>Заголовок

| Заголовок        | Тип   | Описание         |
|---------------|--------|---------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |
| Tracking ID   | Код GUID   | Необязательный параметр. Идентификатор, который отслеживает поток вызовов.                                  |

<span id="parameters"/> 
### <a name="parameters"></a>Параметры

Метод GET для запроса рекламных кампаний поддерживает следующие необязательные параметры запроса.

| Название        | Тип   |  Описание      |    
|-------------|--------|---------------|------|
| skip  |  int   | Количество строк, пропускаемых в запросе. Используйте этот параметр для постраничного перемещения по наборам данных. Например, при fetch=10 и skip=0 извлекаются первые 10 строк данных; при top=10 и skip=10 извлекаются следующие 10 строк данных и т. д.    |       
| fetch  |  int   | Количество строк данных, возвращаемых в запросе.    |       
| campaignSetSortColumn  |  строка   | Заказывает объекты [Кампания](#campaign) в теле ответа с помощью указанного поля. Используется следующий синтаксис: <em>CampaignSetSortColumn = field</em>, где параметр <em>field</em> параметр может принимать одно из следующих строковых значений:</p><ul><li><strong>id</strong></li><li><strong>createdDateTime</strong></li></ul><p>Значение по умолчанию — **createdDateTime**.     |     
| isDescending  |  Boolean (логическое)   | Сортирует объекты [Кампания](#campaign) в теле ответа в порядке убывания или возрастания.   |         
| applicationId,  |  строка   | Используйте это значение для возвращения только тех рекламные кампаний, которые связаны с приложением с указанным [кодом продукта в Магазине](in-app-purchases-and-trials.md#store-ids). Пример кода продукта в Магазине — 9nblggh42cfd.   |         
| label  |  строка   | Используйте это значение для возвращения только тех рекламных кампаний, которые включают указанный параметр *label* в объект [Кампания](#campaign).    |       |    

<span/>
### <a name="request-body"></a>Текст запроса

Методы POST и PUT требуют использовать тело запроса JSON с обязательными полями объекта [Кампания](#campaign) и любыми другими полями, которые вы хотите установить или изменить.

<span/>
### <a name="request-examples"></a>Примеры запросов

Следующий пример демонстрирует вызов метода POST для создания рекламной кампании.

```json
POST https://manage.devcenter.microsoft.com/v1.0/my/promotion/campaign HTTP/1.1
Authorization: Bearer <your access token>

{
    "name": "Contoso App Campaign",
    "applicationId": "9nblggh42cfd",
    "configuredStatus": "Active",
    "objective": "DriveInstalls",
    "type": "Community"
}
```

Ниже показано, как вызвать метод GET для получения конкретной рекламной кампании.

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/promotion/campaign/31043481  HTTP/1.1
Authorization: Bearer <your access token>
```

Ниже показано, как вызвать метод GET для запроса для набора рекламных кампаний, отсортированных по дате создания.

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/promotion/campaign?applicationId=9nblggh42cfd&fetch=100&skip=0&campaignSetSortColumn=createdDateTime HTTP/1.1
Authorization: Bearer <your access token>
```

<span/> 
## <a name="response"></a>Ответ

Эти методы возвращают тело ответа JSON с одним или несколькими объектами [Кампания](#campaign) в зависимости от того, какой метод вызван. В следующем примере показано тело ответа для метода GET для конкретной кампании.

```json
{
    "Data": {
        "id": 31043481,
        "name": "Contoso App Campaign",
        "createdDate": "2017-01-17T10:12:15Z",
        "applicationId": "9nblggh42cfd",
        "configuredStatus": "Active",
        "effectiveStatus": "Active",
        "effectiveStatusReasons": [
            "{\"ValidationStatusReasons\":null}"
        ],
        "labels": [],
        "objective": "DriveInstalls",
        "type": "Paid",
        "lines": [
            {
                "id": 31043476,
                "name": "Contoso App Campaign - Paid Line"
            }
        ]
    }
}
```

<span id="campaign"/>
## <a name="campaign-object"></a>Объект Campaign

Для этих методов тела запроса и ответа содержат следующие поля. В этой таблице показаны поля, которые доступны только для чтения (это означает, что они не могут изменяться в методе PUT), и поля, которые необходимы в теле запроса для метода POST.

| Поле        | Тип   |  Описание      |  Только чтение  | Default (по умолчанию)  | Обязательный для POST |  
|--------------|--------|---------------|------|-------------|------------|
|  id   |  целое число   |  Идентификатор рекламной кампании.     |   Да    |      |  Нет     |       
|  name   |  строка   |   Название рекламной кампании.    |    Нет   |      |  Да     |       
|  configuredStatus   |  строка   |  Одно из следующих значений, которое указывает статус рекламной кампании, заданный разработчиком: <ul><li>**Активный**</li><li>**Неактивный**</li></ul>     |  Нет     |  Активный    |   Да    |       
|  effectiveStatus   |  строка   |   Одно из следующих значений, определяющих действующий статус рекламной кампании, в зависимости от проверки системы: <ul><li>**Активный**</li><li>**Неактивный**</li><li>**Обработка**</li></ul>    |    Да   |      |   Нет      |       
|  effectiveStatusReasons   |  массив   |  Одно или несколько из следующих значений, задающих причину нынешнего состояния рекламной кампании: <ul><li>**AdCreativesInactive**</li><li>**BillingFailed**</li><li>**AdLinesInactive**</li><li>**ValidationFailed**</li><li>**Failed**</li></ul>      |  Да     |     |    Нет     |       
|  applicationId   |  строка   |  [Код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для приложения, с которым связана эта рекламная кампания. Пример кода продукта в Магазине — 9nblggh42cfd.     |   Да    |      |  Да     |       
|  labels   |  массив   |   Одна или несколько строк, представляющих пользовательские метки для кампании. Эти метки могут использоваться для поиска и добавления тегов кампании.    |   Нет    |  null    |    Нет     |       
|  type   | строка    |  Одно из следующих значений, указывающее тип кампании: <ul><li>**Paid**</li><li>**House**</li><li>**Community**</li></ul>      |   Да    |      |   Да    |       
|  objective   |  строка   |  Одно из следующих значений, определяющих цель кампании: <ul><li>**DriveInstall**</li><li>**DriveReengagement**</li><li>**DriveInAppPurchase**</li></ul>     |   Нет    |  DriveInstall    |   Да    |       
|  lines   |  массив   |   Один или несколько объектов, которые определяют [линии поставки](manage-delivery-lines-for-ad-campaigns.md#line), которые связаны с рекламной кампанией. Каждый объект в этом поле состоит из поля *идентификатора* и *имени*, которое определяет идентификатор и название линии поставки.     |   Нет    |      |    Нет     |       
|  createdDate   |  строка   |  Дата и время создания рекламной кампании в формате ISO 8601.     |  Да     |      |     Нет    |       |

## <a name="related-topics"></a>Статьи по теме

* [Проведение рекламных кампаний с помощью служб Магазина Windows](run-ad-campaigns-using-windows-store-services.md)
* [Управление линиями поставки для рекламных кампаний](manage-delivery-lines-for-ad-campaigns.md)
* [Управление профилями таргетинга рекламных кампаний](manage-targeting-profiles-for-ad-campaigns.md)
* [Управление рекламными средствами для рекламных кампаний](manage-creatives-for-ad-campaigns.md)
* [Получение данных об эффективности рекламной кампании](get-ad-campaign-performance-data.md)
