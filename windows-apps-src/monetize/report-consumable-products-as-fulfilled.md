---
author: Xansky
ms.assetid: E9BEB2D2-155F-45F6-95F8-6B36C3E81649
description: Используйте этот метод в API коллекции Microsoft Store, чтобы объявить потребляемый продукт в качестве выполненного для указанного покупателя. Перед повторной покупкой продукта пользователем ваше приложение или служба должны сообщить о нем как о выполненном для этого пользователя.
title: Объявление потребляемого продукта в качестве выполненного
ms.author: mhopkins
ms.date: 03/16/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, API коллекции Microsoft Store, исполнение, потребляемый продукт
ms.localizationpriority: medium
ms.openlocfilehash: 2cbacd35a25e8eaf9673d118fcbece835572e289
ms.sourcegitcommit: 1c6325aa572868b789fcdd2efc9203f67a83872a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "4747243"
---
# <a name="report-consumable-products-as-fulfilled"></a>Объявление потребляемого продукта в качестве выполненного

Используйте этот метод в API коллекции Microsoft Store, чтобы объявить потребляемый продукт в качестве выполненного для указанного покупателя. Перед повторной покупкой продукта пользователем ваше приложение или служба должны сообщить о нем как о выполненном для этого пользователя.

Существует два способа использования данного метода, которые позволяют объявить потребляемый продукт в качестве выполненного.

* Укажите код товара (он возвращается параметром **itemId** [запроса продуктов](query-for-products.md)) и предоставляемый вами уникальный ИД отслеживания. Если один и тот же ИД отслеживания будет использован несколько раз, будет возвращен тот же самый результат, даже если элемент уже потреблен. Если вы не уверены что запрос потребления был выполнен успешно, ваша служба должна повторно отправить запросы потребления с тем же самым ИД отслеживания. ИД отслеживания всегда будет привязан к этому запросу потребления и может отправляться неограниченное количество раз.
* Укажите код продукта (он возвращается параметром **productId** [запроса продуктов](query-for-products.md)) и код транзакции, получаемый от одного или нескольких источников, перечисленных в описании параметра **transactionId** в разделе «Текст запроса» этой статьи.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода вам понадобится:

* Маркер доступа Azure AD, имеющий значение URI аудитории `https://onestore.microsoft.com`.
* Ключ идентификатора Microsoft Store, представляющий удостоверение пользователя, для которого потребляемый продукт необходимо объявить в качестве выполненного.

Дополнительные сведения см. в разделе [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md).

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                   |
|--------|---------------------------------------------------------------|
| POST   | ```https://collections.mp.microsoft.com/v6.0/collections/consume``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок         | Тип   | Описание                                                                                           |
|----------------|--------|-------------------------------------------------------------------------------------------------------|
| Authorization  | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;.                           |
| Host           | Строка | Должен иметь значение **collections.mp.microsoft.com**.                                            |
| Content-Length | Число | Длина текста запроса.                                                                       |
| Content-Type   | Строка | Указывает тип запросов и ответов. На данный момент единственным поддерживаемым значением является **application/json**. |


### <a name="request-body"></a>Тело запроса

| Параметр     | Тип         | Описание         | Обязательный |
|---------------|--------------|---------------------|----------|
| beneficiary   | UserIdentity | Пользователь, для которого выполняется потребление этого элемента. Подробная информация приводится в следующей таблице.        | Да      |
| itemId        | string       | Значение *itemId*, возвращаемое [запросом продуктов](query-for-products.md). Используйте этот параметр с *trackingId*      | Нет       |
| trackingId    | guid         | Уникальный ИД отслеживания, предоставляемый разработчиком. Используйте этот параметр с *itemId*.         | Нет       |
| productId     | string       | Значение *productId*, возвращаемое [запросом продуктов](query-for-products.md). Используйте этот параметр с *transactionId*   | Нет       |
| transactionId | guid         | Код транзакции, получаемый от одного следующих источников. Используйте этот параметр с *productId*.<ul><li>Свойство [TransactionID](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.purchaseresults.transactionid) класса [PurchaseResults](https://msdn.microsoft.com/library/windows/apps/dn263392).</li><li>Квитанция приложения или продукта, возвращаемая [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync), [RequestAppPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestapppurchaseasync) или [GetAppReceiptAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getappreceiptasync).</li><li>Параметр *transactionId*, возвращаемый [запросом продуктов](query-for-products.md).</li></ul>   | Нет       |


Объект UserIdentity содержит следующие параметры.

| Параметр            | Тип   | Описание       | Обязательный |
|----------------------|--------|-------------------|----------|
| identityType         | строка | Укажите строковое значение **b2b**.    | Да      |
| identityValue        | string | [Ключ идентификатора Microsoft Store](view-and-grant-products-from-a-service.md#step-4), представляющий удостоверение пользователя, для которого потребляемый продукт необходимо объявить в качестве выполненного.      | Да      |
| localTicketReference | строка | Запрошенный идентификатор для возвращаемого ответа. Рекомендуется использовать то же значение, что и в [утверждении](view-and-grant-products-from-a-service.md#claims-in-a-microsoft-store-id-key) *userId* в ключе идентификатора Microsoft Store. | Да      |


### <a name="request-examples"></a>Примеры запросов

В следующем примере используются *itemId* и *trackingId*.

```syntax
POST https://collections.mp.microsoft.com/v6.0/collections/consume HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1…..
Host: collections.mp.microsoft.com
Content-Length: 2050
Content-Type: application/json

{
    "beneficiary": {
        "localTicketReference": "testreference",
        "identityValue": "eyJ0eXAiOi…..",
        "identityType": "b2b"
    },
    "itemId": "44c26106-4979-457b-af34-609ae97a084f",
    "trackingId": "44db79ca-e31d-49e9-8896-fa5c7f892b40"
}
```

В следующем примере используются *productId* и *transactionId*.

```syntax
POST https://collections.mp.microsoft.com/v6.0/collections/consume HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1……
Content-Length: 1880
Content-Type: application/json
Host: collections.md.mp.microsoft.com

{
    "beneficiary" : {
        "localTicketReference" : "testReference",
        "identityValue" : "eyJ0eXAiOiJ…..",
        "identitytype" : "b2b"
    },
    "productId" : "9NBLGGH5WVP6",
    "transactionId" : "08a14c7c-1892-49fc-9135-190ca4f10490"
}
```


## <a name="response"></a>Ответ

Если потребление выполнено успешно, никакие данные не возвращаются.

### <a name="response-example"></a>Пример ответа

```syntax
HTTP/1.1 204 No Content
Content-Length: 0
MS-CorrelationId: 386f733d-bc66-4bf9-9b6f-a1ad417f97f0
MS-RequestId: e488cd0a-9fb6-4c2c-bb77-e5100d3c15b1
MS-CV: 5.1
MS-ServerId: 030011326
Date: Tue, 22 Sep 2015 20:40:55 GMT
```

## <a name="error-codes"></a>Коды ошибок


| Код | Ошибка        | Внутренний код ошибки           | Описание           |
|------|--------------|----------------------------|-----------------------|
| 401  | Unauthorized | AuthenticationTokenInvalid | Маркер доступа Azure AD недействителен. В некоторых случаях сведения об ошибке ServiceError содержат больше информации, например если истек срок действия маркера или отсутствует утверждение *appid*. |
| 401  | Unauthorized | PartnerAadTicketRequired   | Маркер доступа Azure AD не был передан службе в заголовке авторизации.                                                                                                   |
| 401  | Unauthorized | InconsistentClientId       | Утверждение *clientId* в ключе идентификатора Microsoft Store в теле запроса и утверждение *appid* в маркере доступа Azure AD в заголовке авторизации не совпадают.                     |

<span/> 

## <a name="related-topics"></a>Статьи по теме

* [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md)
* [Запрос продуктов](query-for-products.md)
* [Предоставление бесплатных продуктов](grant-free-products.md)
* [Обновление ключа идентификатора Microsoft Store](renew-a-windows-store-id-key.md)
