---
author: mcleanbyron
ms.assetid: E9BEB2D2-155F-45F6-95F8-6B36C3E81649
description: "Используйте этот метод в API коллекции Магазина Windows, чтобы объявить потребляемый продукт в качестве выполненного для указанного покупателя. Перед повторной покупкой продукта пользователем ваше приложение или служба должны сообщить о нем как о выполненном для этого пользователя."
title: "Объявление потребляемого продукта в качестве выполненного"
ms.sourcegitcommit: 2f4351d6f9bdc0b9a131ad5ead10ffba7e76c437
ms.openlocfilehash: b099bdc26565ef218eaf1f73c5bb3ec9c24065c3

---

# Объявление потребляемого продукта в качестве выполненного


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Используйте этот метод в API коллекции Магазина Windows, чтобы объявить потребляемый продукт в качестве выполненного для указанного покупателя. Перед повторной покупкой продукта пользователем ваше приложение или служба должны сообщить о нем как о выполненном для этого пользователя.

Существует два способа использования данного метода, которые позволяют объявить потребляемый продукт в качестве выполненного.

-   Укажите код товара (он возвращается параметром **itemId** [запроса продуктов](query-for-products.md)) и предоставляемый вами уникальный ИД отслеживания. Если один и тот же ИД отслеживания будет использован несколько раз, будет возвращен тот же самый результат, даже если элемент уже потреблен. Если вы не уверены что запрос потребления был выполнен успешно, ваша служба должна повторно отправить запросы потребления с тем же самым ИД отслеживания. ИД отслеживания всегда будет привязан к этому запросу потребления и может отправляться неограниченное количество раз.
-   Укажите код продукта (он возвращается параметром **productId** [запроса продуктов](query-for-products.md)) и код транзакции, получаемый от одного или нескольких источников, перечисленных в описании параметра **transactionId** в разделе «Тело запроса» этой статьи.

## Необходимые условия


Для использования этого метода вам понадобится:

-   маркер доступа Azure AD, созданный с помощью URI аудитории `https://onestore.microsoft.com`;
-   ключ идентификатора Магазина Windows, который был создан с помощью метода [**GetCustomerCollectionsIdAsync**](https://msdn.microsoft.com/library/windows/apps/mt608674), вызываемого в клиентском коде вашего приложения.

Подробные сведения см. в разделе [Просмотр и предоставление продуктов из службы](view-and-grant-products-from-a-service.md).

## Запрос


### Синтаксис запроса

| Метод | URI запроса                                                   |
|--------|---------------------------------------------------------------|
| POST   | `https://collections.mp.microsoft.com/v6.0/collections/consume` |

<br/> 

### Заголовок запроса

| Заголовок         | Тип   | Описание                                                                                           |
|----------------|--------|-------------------------------------------------------------------------------------------------------|
| Authorization  | string | Обязательное. 1Маркер доступа Azure AD в форме**Bearer**&lt;*token*&gt;.                           |
| Host           | строковый | Должен иметь значение **collections.mp.microsoft.com**.                                            |
| Content-Length | Число | Длина тела запроса.                                                                       |
| Content-Type   | Строка | Указывает тип запросов и ответов. На данный момент единственным поддерживаемым значением является **application/json**. |

<br/> 

### Тело запроса

| Параметр     | Тип         | Описание         | Обязательный параметр |
|---------------|--------------|---------------------|----------|
| beneficiary   | UserIdentity | Пользователь, для которого выполняется потребление этого элемента.                                                                                                                                                                                                                                                                 | Да      |
| itemId        | Строка       | Значение itemId, возвращаемое [запросом продуктов](query-for-products.md). Используйте этот параметр с trackingId                                                                                                                                                                                                  | Нет       |
| trackingId    | Guid         | Уникальный ИД отслеживания, предоставляемый разработчиком. Используйте этот параметр с itemId.                                                                                                                                                                                                                                     | Нет       |
| productId     | Строка       | Значение productId, возвращаемое [запросом продуктов](query-for-products.md). Используйте этот параметр с transactionId                                                                                                                                                                                            | Нет       |
| transactionId | Guid         | Код транзакции получаемый от одного следующих источников:                                                                                                                                                                                                                                      | Нет       |
|               |              | * Свойство [TransactionID](https://msdn.microsoft.com/library/windows/apps/dn263396) класса [PurchaseResults](https://msdn.microsoft.com/library/windows/apps/dn263392).   |        |
|               |              | * Квитанция приложения или продукта, возвращаемая [RequestProductPurchaseAsync](https://msdn.microsoft.com/library/windows/apps/dn263381), [RequestAppPurchaseAsync](https://msdn.microsoft.com/library/windows/apps/hh967813) или [GetAppReceiptAsync](https://msdn.microsoft.com/library/windows/apps/hh967811).   |        |
|               |              | * Параметр transactionId, возвращаемый [запросом продуктов](query-for-products.md).   |        |        
|               |              | Используйте этот параметр с productId.   |        |
 
<br/>

Объект UserIdentity содержит следующие параметры.

| Параметр            | Тип   | Описание                                                                                                                                 | Обязательный параметр |
|----------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------|----------|
| identityType         | Строка | Укажите строковое значение **b2b**.                                                                                                           | Да      |
| identityValue        | Строка | Строковое значение ключа идентификатора Магазина Windows.                                                                                                   | Да      |
| localTicketReference | Строка | Запрошенный идентификатор для возвращаемого ответа. Рекомендуется использовать то же значение, которое использует утверждение *userId* в ключе идентификатора Магазина Windows. | Да      |

<br/> 

### Примеры запросов

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

## Ответ


Если потребление выполнено успешно, никакие данные не возвращаются.

### Пример ответа

```syntax
HTTP/1.1 204 No Content
Content-Length: 0
MS-CorrelationId: 386f733d-bc66-4bf9-9b6f-a1ad417f97f0
MS-RequestId: e488cd0a-9fb6-4c2c-bb77-e5100d3c15b1
MS-CV: 5.1
MS-ServerId: 030011326
Date: Tue, 22 Sep 2015 20:40:55 GMT
```

## Коды ошибок


| Код | Ошибка        | Внутренний код ошибки           | Описание                                                                                                                                                                           |
|------|--------------|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 401  | Unauthorized | AuthenticationTokenInvalid | Маркер доступа Azure AD недействителен. В некоторых случаях сведения об ошибке ServiceError содержат больше информации, например если истек срок действия маркера или отсутствует утверждение *appid*. |
| 401  | Unauthorized | PartnerAadTicketRequired   | Маркер доступа Azure AD не был передан службе в заголовке авторизации.                                                                                                   |
| 401  | Unauthorized | InconsistentClientId       | Утверждение *clientId* в ключе идентификатора Магазина Windows в теле запроса и утверждение *appid* в маркере доступа Azure AD в заголовке авторизации не совпадают.                     |

<br/> 

## Связанные разделы

* [Просмотр и предоставление продуктов из службы](view-and-grant-products-from-a-service.md)
* [Запрос продуктов](query-for-products.md)
* [Предоставление бесплатных продуктов](grant-free-products.md)
* [Обновление ключа идентификатора Магазина Windows](renew-a-windows-store-id-key.md)
 

 



<!--HONumber=Jun16_HO5-->


