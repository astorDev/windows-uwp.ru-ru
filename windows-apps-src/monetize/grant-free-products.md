---
author: Xansky
ms.assetid: FA55C65C-584A-4B9B-8451-E9C659882EDE
description: Используйте этот метод в API покупок Microsoft Store, чтобы предоставить бесплатное приложение или надстройку определенному пользователю.
title: Предоставление бесплатных продуктов
ms.author: mhopkins
ms.date: 03/16/2018
ms.topic: article
keywords: windows 10, uwp, API покупок Microsoft Store, предоставление продуктов
ms.localizationpriority: medium
ms.openlocfilehash: 27503148d4406cb0ba1c2ce9782ca7131c8ce081
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "5825554"
---
# <a name="grant-free-products"></a>Предоставление бесплатных продуктов

Используйте этот метод в API покупок Microsoft Store, чтобы предоставить бесплатное приложение или надстройку (также называется внутренним продуктом приложения или IAP) определенному пользователю.

На данный момент можно предоставить только бесплатные продукты. Если ваша служба попытается использовать этот метод, чтобы предоставить продукт, который не является бесплатным, метод вернет ошибку.

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода вам понадобится:

* Маркер доступа Azure AD, имеющий значение URI аудитории `https://onestore.microsoft.com`.
* Ключ в Microsoft Store, представляющий удостоверение пользователя, которому требуется предоставить бесплатный продукт.

Дополнительные сведения см. в разделе [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md).

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                            |
|--------|--------------------------------------------------------|
| POST   | ```https://purchase.mp.microsoft.com/v6.0/purchases/grant``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок         | Тип   | Описание                                                                                           |
|----------------|--------|-------------------------------------------------------------------------------------------------------|
| Authorization  | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;.                           |
| Host           | строка | Должен иметь значение **purchase.mp.microsoft.com**.                                            |
| Content-Length | Число | Длина текста запроса.                                                                       |
| Content-Type   | Строка | Указывает тип запросов и ответов. На данный момент единственным поддерживаемым значением является **application/json**. |


### <a name="request-body"></a>Тело запроса

| Параметр      | Тип   | Описание        | Обязательный |
|----------------|--------|---------------------|----------|
| availabilityId | строка | ИД доступности предоставляемого продукта из каталога Microsoft Store.         | Да      |
| b2bKey         | строка | [Ключ в Microsoft Store](view-and-grant-products-from-a-service.md#step-4), представляющий удостоверение пользователя, которому требуется предоставить продукт.    | Да      |
| devOfferId     | строка | ИД определенного разработчиком предложения, который появится в элементе коллекции после покупки.        |
| language       | string | Язык пользователя.  | Да      |
| market         | string | Рынок пользователя.       | Да      |
| orderId        | Глобальный уникальный идентификатор   | GUID, созданный для заказа. Это значение должно быть уникальным для пользователя, но оно не обязательно должно быть уникальным для всех заказов.    | Да      |
| productId      | string | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для [продукта](in-app-purchases-and-trials.md#products-skus-and-availabilities) в каталоге Microsoft Store. Пример кода магазина продукта — 9NBLGGH42CFD. | Да      |
| quantity       | int    | Объем покупки. На данный момент единственным поддерживаемым значением является «1». Если оно не указано, по умолчанию используется значение «1».   | Нет       |
| skuId          | string | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для [SKU](in-app-purchases-and-trials.md#products-skus-and-availabilities) продукта в каталоге Microsoft Store. Пример кода магазина для SKU — 0010.     | Да      |


### <a name="request-example"></a>Пример запроса

```syntax
POST https://purchase.mp.microsoft.com/v6.0/purchases/grant HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJK……
Content-Length: 1863
Content-Type: application/json

{
    "b2bKey" : "eyJ0eXAiOiJK……",
    "availabilityId" : "9RT7C09D5J3W",
    "productId" : "9NBLGGH5WVP6",
    "skuId" : "0010",
    "language" : "en-us",
    "market" : "us",
    "orderId" : "3eea1529-611e-4aee-915c-345494e4ee76",
}
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Параметр                 | Тип                        | Описание             | Обязательный |
|---------------------------|-----------------------------|-----------------------|----------|
| clientContext             | ClientContextV6             | Контекстно-зависимая информация клиента об этом заказе. Присваивается значению *clientID* маркера Azure AD.    | Да      |
| createdtime               | datetimeoffset              | Время создания заказа.         | Да      |
| currencyCode              | string                      | Код валюты для *totalAmount* и *totalTaxAmount*. Недоступно для бесплатных элементов.     | Да      |
| friendlyName              | string                      | Понятное имя для заказа. Недоступно для заказов, осуществляемых с помощью API покупок Microsoft Store. | Да      |
| isPIRequired              | boolean                     | Указывает, является ли платежное средство (PI) обязательной частью заказа на покупку.  | Да      |
| language                  | string                      | Код языка для заказа (например, «en»).       | Да      |
| market                    | string                      | Код рынка для заказа (например, «США»).  | Да      |
| orderId                   | string                      | Код, который идентифицирует данный заказ для конкретного пользователя.                | Да      |
| orderLineItems            | list&lt;OrderLineItemV6&gt; | Список позиций для заказа. Обычно в заказе имеется 1 позиция.       | Да      |
| orderState                | string                      | Состояние заказа. Допустимые состояния: **Editing**, **CheckingOut**, **Pending**, **Purchased**, **Refunded**, **ChargedBack** и **Cancelled**. | Да      |
| orderValidityEndTime      | string                      | Крайний срок перед отправкой, до которого цена заказа является действительной. Недоступно для бесплатных приложений.      | Да      |
| orderValidityStartTime    | string                      | Начало периода времени перед отправкой, в течение которого цена заказа является действительной. Недоступно для бесплатных приложений.          | Да      |
| purchaser                 | IdentityV6                  | Объект, который описывает удостоверение покупателя.       | Да      |
| totalAmount               | decimal                     | Общая сумма покупки для всех элементов заказа, включая налоги.       | Да      |
| totalAmountBeforeTax      | decimal                     | Общая сумма покупки для всех элементов заказа без учета налогов.      | Да      |
| totalChargedToCsvTopOffPI | decimal                     | При использовании отдельного платежного инструмента и сохраненного значения (CSV) — сумма, выставленная CSV.            | Да      |
| totalTaxAmount            | decimal                     | Общая сумма налогов для всех позиций.    | Да      |


Объект ClientContext содержит следующие параметры.

| Параметр | Тип   | Описание                           | Обязательный |
|-----------|--------|---------------------------------------|----------|
| client    | string | Идентификатор клиента, создавшего заказ. | Нет       |


Объект OrderLineItemV6 содержит следующие параметры.

| Параметр               | Тип           | Описание                                                                                                  | Обязательный |
|-------------------------|----------------|--------------------------------------------------------------------------------------------------------------|----------|
| agent                   | IdentityV6     | Агент, который последним редактировал позицию. Подробные сведения об этом объекте см. в таблице ниже.       | Нет       |
| availabilityId          | строка         | Идентификатор доступности приобретаемого продукта из каталога Microsoft Store.                           | Да      |
| beneficiary             | IdentityV6     | Удостоверение бенефициара заказа.                                                                | Нет       |
| billingState            | string         | Состояние выставления счета заказа. По завершении меняется на **Charged**.                                   | Нет       |
| campaignId              | string         | Идентификатор кампании для этого заказа.                                                                              | Нет       |
| currencyCode            | string         | Код валюты, используемый для сведений о цене.                                                                    | Да      |
| description             | string         | Локализованное описание позиции.                                                                    | Да      |
| devofferId              | string         | Идентификатор конкретного заказа, если таковой имеется.                                                           | Нет       |
| fulfillmentDate         | datetimeoffset | Дата выполнения.                                                                           | Нет       |
| fulfillmentState        | string         | Состояние выполнения этой позиции. По завершении меняется на **Fulfilled**.                      | Нет       |
| isPIRequired            | boolean        | Указывает, является ли платежное средство обязательным параметром для этой позиции.                                       | Да      |
| isTaxIncluded           | boolean        | Указывает, включены ли налоги в сведения о цене позиции.                                        | Да      |
| legacyBillingOrderId    | string         | Устаревший идентификатор для выставления счетов.                                                                                       | Нет       |
| lineItemId              | string         | Идентификатор позиции для элемента в этом заказе.                                                                 | Да      |
| listPrice               | decimal        | Заявленная цена позиции в этом заказе.                                                                    | Да      |
| productId               | строка         | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для [продукта](in-app-purchases-and-trials.md#products-skus-and-availabilities) представляющий позицию в каталоге Microsoft Store. Пример кода продукта в Store: 9NBLGGH42CFD.   | Да      |
| productType             | string         | Тип продукта. Поддерживаемые значения: **Durable**, **Application** и **UnmanagedConsumable**. | Да      |
| quantity                | int            | Количество экземпляров заказанной позиции.                                                                            | Да      |
| retailPrice             | decimal        | Розничная цена заказанного элемента.                                                                        | Да      |
| revenueRecognitionState | string         | Состояние признания дохода.                                                                               | Да      |
| skuId                   | строка         | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для [SKU](in-app-purchases-and-trials.md#products-skus-and-availabilities) позиции в каталоге Microsoft Store. Пример кода продукта в Store для номера SKU: 0010.                                                                   | Да      |
| taxAmount               | decimal        | Сумма налогов для позиции.                                                                            | Да      |
| taxType                 | string         | Тип применимых налогов.                                                                       | Да      |
| Title                   | string         | Локализованное название позиции.                                                                        | Да      |
| totalAmount             | decimal        | Общая сумма покупки для позиции, включая налоги.                                                    | Да      |


Объект IdentityV6 содержит следующие параметры.

| Параметр     | Тип   | Описание                                                                        | Обязательный |
|---------------|--------|------------------------------------------------------------------------------------|----------|
| identityType  | строка | Содержит значение **"pub"**.                                                      | Да      |
| identityValue | Строка | Строковое значение *publisherUserId* конкретного ключа идентификатора Microsoft Store. | Да      |


### <a name="response-example"></a>Пример ответа

```syntax
Content-Length: 1203
Content-Type: application/json
MS-CorrelationId: fb2e69bc-f26a-4aab-a823-7586c19f5762
MS-RequestId: c1bc832c-f742-47e4-a76c-cf061402f698
MS-CV: XjfuNWLQlEuxj6Mt.8
MS-ServerId: 030032362
Date: Tue, 13 Oct 2015 21:21:51 GMT

{
    "clientContext": {
        "client": "86b78998-d05a-487b-b380-6c738f6553ea"
    },
    "createdTime": "2015-10-13T21:21:51.1863494+00:00",
    "currencyCode": "USD",
    "isPIRequired": false,
    "language": "en-us",
    "market": "us",
    "orderId": "3eea1529-611e-4aee-915c-345494e4ee76",
    "orderLineItems": [{
        "availabilityId": "9RT7C09D5J3W",
        "beneficiary": {
            "identityType": "pub",
            "identityValue": "user1"
        },
        "billingState": "Charged",
        "currencyCode": "USD",
        "description": "Jewels, Jewels, Jewels - Consumable 2",
        "fulfillmentDate": "2015-10-13T21:21:51.639478+00:00",
        "fulfillmentState": "Fulfilled",
        "isPIRequired": false,
        "isTaxIncluded": true,
        "lineItemId": "2814d758-3ee3-46b3-9671-4fb3bdae9ffe",
        "listPrice": 0.0,
        "payments": [],
        "productId": "9NBLGGH5WVP6",
        "productType": "UnmanagedConsumable",
        "quantity": 1,
        "retailPrice": 0.0,
        "revenueRecognitionState": "None",
        "skuId": "0010",
        "taxAmount": 0.0,
        "taxType": "NoApplicableTaxes",
        "title": "Jewels, Jewels, Jewels - Consumable 2",
        "totalAmount": 0.0
    }],
    "orderState": "Purchased",
    "orderValidityEndTime": "2015-10-14T21:21:51.1863494+00:00",
    "orderValidityStartTime": "2015-10-13T21:21:51.1863494+00:00",
    "purchaser": {
        "identityType": "pub",
        "identityValue": "user1"
    },
    "testScenarios": "None",
    "totalAmount": 0.0,
    "totalTaxAmount": 0.0
}
```

## <a name="error-codes"></a>Коды ошибок


| Код | Ошибка        | Внутренний код ошибки           | Описание   |
|------|--------------|----------------------------|----------------|
| 401  | Unauthorized | AuthenticationTokenInvalid | Маркер доступа Azure AD недействителен. В некоторых случаях сведения об ошибке ServiceError содержат больше информации, например если истек срок действия маркера или отсутствует утверждение *appid*. |
| 401  | Unauthorized | PartnerAadTicketRequired   | Маркер доступа Azure AD не был передан службе в заголовке авторизации.   |
| 401  | Unauthorized | InconsistentClientId       | Утверждение *clientId* в ключе идентификатора Microsoft Store в теле запроса и утверждение *appid* в маркере доступа Azure AD в заголовке авторизации не совпадают.       |
| 400  | BadRequest   | InvalidParameter           | Подробности содержат информацию о теле запроса и полях с недопустимым значением.           |

<span/> 

## <a name="related-topics"></a>Статьи по теме

* [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md)
* [Запрос продуктов](query-for-products.md)
* [Объявление потребляемого продукта в качестве выполненного](report-consumable-products-as-fulfilled.md)
* [Обновление ключа идентификатора Microsoft Store](renew-a-windows-store-id-key.md)
