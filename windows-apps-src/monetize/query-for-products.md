---
author: mcleanbyron
ms.assetid: D1F233EC-24B5-4F84-A92F-2030753E608E
description: Используйте этот метод в API коллекции Microsoft Store для получения всех продуктов, принадлежащих пользователю, для приложений, связанных с вашим идентификатором клиента Azure AD. Вы можете ограничить запрос определенным продуктом или использовать другие фильтры.
title: Запрос продуктов
ms.author: mcleans
ms.date: 03/16/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, API коллекции Microsoft Store, uwp, просмотр продуктов
ms.localizationpriority: medium
ms.openlocfilehash: 696d2c0ca4f8d0d92bef66efb9d551a2f19d2aa3
ms.sourcegitcommit: 54c2cd58fde08af889093a0c85e7297e33e6a0eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2018
ms.locfileid: "1664970"
---
# <a name="query-for-products"></a>Запрос продуктов


Используйте этот метод в API коллекции Microsoft Store для получения всех продуктов, принадлежащих пользователю, для приложений, связанных с вашим идентификатором клиента Azure AD. Вы можете ограничить запрос определенным продуктом или использовать другие фильтры.

Этот метод вызывается вашей службой в ответ на сообщение из вашего приложения. Ваша служба не должна регулярно опрашивать всех пользователей в расписании.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода вам понадобится:

* Маркер доступа Azure AD, имеющий значение URI аудитории `https://onestore.microsoft.com`.
* Ключ идентификатора Microsoft Store, представляющий удостоверение пользователя, продукты которого вы хотите получить.

Дополнительные сведения см. в разделе [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md).

## <a name="request"></a>Запрос

### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                 |
|--------|-------------------------------------------------------------|
| POST   | ```https://collections.mp.microsoft.com/v6.0/collections/query``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок         | Тип   | Описание                                                                                           |
|----------------|--------|-------------------------------------------------------------------------------------------------------|
| Authorization  | Строка | Обязательное. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;.                           |
| Host           | Строка | Должен иметь значение **collections.mp.microsoft.com**.                                            |
| Content-Length | Число | Длина текста запроса.                                                                       |
| Content-Type   | Строка | Указывает тип запросов и ответов. На данный момент единственным поддерживаемым значением является **application/json**. |


### <a name="request-body"></a>Тело запроса

| Параметр         | Тип         | Описание         | Обязательный |
|-------------------|--------------|---------------------|----------|
| beneficiaries     | UserIdentity | Объект UserIdentity, который представляет пользователя, опрашиваемого на предмет продуктов. Дополнительные сведения см. в таблице ниже.    | Да      |
| continuationToken | строка       | Если имеется несколько наборов продуктов, то по достижении лимита для страницы тело ответа возвращает маркер продолжения. Укажите здесь этот маркер продолжения, чтобы в последующих вызовах получить информацию об оставшихся продуктах.       | Нет       |
| maxPageSize       | Число       | Максимальное количество продуктов, возвращаемых в одном ответе. Стандартное и максимальное значение — 100.                 | Нет       |
| modifiedAfter     | Дата и время     | Если параметр указан, служба возвращает только продукты, которые были изменены после этой даты.        | Нет       |
| parentProductId   | строка       | Если параметр указан, служба возвращает только надстройки, которые соответствуют указанному приложению.      | Нет       |
| productSkuIds     | list&lt;ProductSkuId&gt; | Если параметр указан, служба возвращает только продукты, соответствующие предоставленным парам «продукт — SKU». Дополнительные сведения см. в таблице ниже.      | Нет       |
| productTypes      | строка       | Если параметр указан, служба возвращает только продукты, соответствующие указанным типам продуктов. Поддерживаемые типы продуктов: **Application**, **Durable** и **UnmanagedConsumable**.     | Нет       |
| validityType      | строка       | Если выбрано значение **All**, возвращаются все продукты пользователя, в том числе продукты с истекшим сроком. Если выбрано значение **Valid**, возвращаются только продукты, действительные на данный момент (то есть в активном состоянии, с датой начала &lt; ранее текущего момента и датой окончания &gt; позднее текущего момента). | Нет       |


Объект UserIdentity содержит следующие параметры.

| Параметр            | Тип   |  Описание      | Обязательный |
|----------------------|--------|----------------|----------|
| identityType         | строка | Укажите строковое значение **b2b**.    | Да      |
| identityValue        | string | [Ключ идентификатора Microsoft Store](view-and-grant-products-from-a-service.md#step-4), представляющий пользователя, продукты которого вы хотите запросить.  | Да      |
| localTicketReference | строка | Запрошенный идентификатор для возвращаемых продуктов. Элементы, возвращаемые в теле ответа, будут иметь соответствующее значение параметра *localTicketReference*. Рекомендуется использовать то же значение, что и в утверждении *userId* в ключе идентификатора Microsoft Store. | Да      |


Объект ProductSkuId содержит следующие параметры.

| Параметр | Тип   | Описание          | Обязательный |
|-----------|--------|----------------------|----------|
| productId | string | [Код [продукта](in-app-purchases-and-trials.md#products-skus-and-availabilities) в Store](in-app-purchases-and-trials.md#store-ids) в каталоге Microsoft Store. Пример кода магазина продукта — 9NBLGGH42CFD. | Да      |
| skuID     | string | [Код [SKU](in-app-purchases-and-trials.md#products-skus-and-availabilities) продукта в Store](in-app-purchases-and-trials.md#store-ids) в каталоге Microsoft Store. Пример кода магазина для SKU — 0010.       | Да      |


### <a name="request-example"></a>Пример запроса

```syntax
POST https://collections.mp.microsoft.com/v6.0/collections/query HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1Q…….
Host: collections.mp.microsoft.com
Content-Length: 2531
Content-Type: application/json

{
  "maxPageSize": 100,
  "beneficiaries": [
    {
      "localTicketReference": "1055521810674918",
      "identityValue": "eyJ0eXAiOiJ……",
      "identityType": "b2b"
    }
  ],
  "modifiedAfter": "\/Date(-62135568000000)\/",
  "productSkuIds": [
    {
      "productId": "9NBLGGH5WVP6",
      "skuId": "0010"
    }
  ],
  "productTypes": [
    "UnmanagedConsumable"
  ],
  "validityType": "All"
}
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Параметр         | Тип                     | Описание          | Обязательный |
|-------------------|--------------------------|-----------------------|----------|
| continuationToken | строка                   | Если имеется несколько наборов продуктов, то по достижении лимита для страницы возвращается этот маркер. Вы можете указать маркер продолжения в последующих вызовах для извлечения остальных продуктов. | Нет       |
| items             | CollectionItemContractV6 | Массив продуктов для указанного пользователя. Дополнительные сведения см. в таблице ниже.        | Нет       |


Объект CollectionItemContractV6 содержит следующие параметры.

| Параметр            | Тип               | Описание            | Обязательный |
|----------------------|--------------------|-------------------------|----------|
| acquiredDate         | Дата и время           | Дата приобретения элемента пользователем.                  | Да      |
| campaignId           | строка             | Идентификатор кампании, предоставленный во время покупки для этого элемента.                  | Нет       |
| devOfferId           | строка             | Идентификатор предложения покупки из приложения.              | Нет       |
| endDate              | Дата и время           | Дата окончания срока действия элемента.              | Да      |
| fulfillmentData      | строка             | Н/д         | Нет       |
| inAppOfferToken      | строка             | Указанная разработчиком строка кода продукта, назначенная элементу на информационной панели Центра разработки для Windows. Пример кода продукта: *product123*. | Нет       |
| itemId               | строка             | Идентификатор, отличающий этот элемент коллекции от других элементов, принадлежащих пользователю. Этот идентификатор уникален для каждого продукта.   | Да      |
| localTicketReference | string             | Идентификатор ранее предоставленного в тексте запроса параметра *localTicketReference*.                  | Да      |
| modifiedDate         | Дата и время           | Дата последнего изменения элемента.              | Да      |
| orderId              | строка             | Если он указан, идентификатор заказа, из которого был получен данный элемент.              | Нет       |
| orderLineItemId      | строка             | Если указан, означает позицию конкретного заказа данного элемента.              | Нет       |
| ownershipType        | string             | Строка *OwnedByBeneficiary*.   | Да      |
| productId            | string             | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для [продукта](in-app-purchases-and-trials.md#products-skus-and-availabilities) в каталоге Microsoft Store. Пример кода магазина продукта — 9NBLGGH42CFD.          | Да      |
| productType          | строка             | Один из следующих типов продукта: **Application**, **Durable** и **UnmanagedConsumable**.        | Да      |
| purchasedCountry     | строка             | Н/д   | Нет       |
| purchaser            | IdentityContractV6 | Если он указан, означает удостоверение покупателя элемента. Сведения для этого объекта см. ниже.        | Нет       |
| quantity             | number             | Количество экземпляров элемента. На данный момент это значение всегда составляет 1.      | Нет       |
| skuId                | string             | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для [SKU](in-app-purchases-and-trials.md#products-skus-and-availabilities) продукта в каталоге Microsoft Store. Пример кода магазина для SKU — 0010.     | Да      |
| skuType              | строка             | Тип SKU. Возможные значения включают **Trial**, **Full** и **Rental**.        | Да      |
| startDate            | datetime           | Дата, начиная с которой элемент действителен.       | Да      |
| status               | строка             | Состояние элемента. Возможные значения включают **Active**, **Expired**, **Revoked** и **Banned**.    | Да      |
| tags                 | строка             | Н/Д    | Да      |
| transactionId        | guid               | Идентификатор транзакции в результате покупки элемента. Может использоваться для объявления элемента в качестве выполненного.      | Да      |


Объект IdentityContractV6 содержит следующие параметры.

| Параметр     | Тип   | Описание                                                                        | Обязательный |
|---------------|--------|------------------------------------------------------------------------------------|----------|
| identityType  | string | Содержит значение *pub*.                                                      | Да      |
| identityValue | Строка | Строковое значение *publisherUserId* конкретного ключа идентификатора Microsoft Store. | Да      |


### <a name="response-example"></a>Пример ответа

```syntax
HTTP/1.1 200 OK
Content-Length: 7241
Content-Type: application/json
MS-CorrelationId: 699681ce-662c-4841-920a-f2269b2b4e6c
MS-RequestId: a9988cf9-652b-4791-beba-b0e732121a12
MS-CV: xu2HW6SrSkyfHyFh.0.1
MS-ServerId: 020022359
Date: Tue, 22 Sep 2015 20:28:18 GMT

{
  "items" : [
    {
      "acquiredDate" : "2015-09-22T19:22:51.2068724+00:00",
      "devOfferId" : "f9587c53-540a-498b-a281-8a349491ed47",
      "endDate" : "9999-12-31T23:59:59.9999999+00:00",
      "fulfillmentData" : [],
      "inAppOfferToken" : "consumable2",
      "itemId" : "4b8fbb13127a41f299270ea668681c1d",
      "localTicketReference" : "1055521810674918",
      "modifiedDate" : "2015-09-22T19:22:51.2513155+00:00",
      "orderId" : "4ba5960d-4ec6-4a81-ac20-aafce02ddf31",
      "ownershipType" : "OwnedByBeneficiary",
      "productId" : "9NBLGGH5WVP6",
      "productType" : "UnmanagedConsumable",
      "purchaser" : {
        "identityType" : "pub",
        "identityValue" : "user123"
      },
      "skuId" : "0010",
      "skuType" : "Full",
      "startDate" : "2015-09-22T19:22:51.2068724+00:00",
      "status" : "Active",
      "tags" : [],
      "transactionId" : "4ba5960d-4ec6-4a81-ac20-aafce02ddf31"
    }
  ]
}
```

## <a name="related-topics"></a>Статьи по теме

* [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md)
* [Объявление потребляемого продукта в качестве выполненного](report-consumable-products-as-fulfilled.md)
* [Предоставление бесплатных продуктов](grant-free-products.md)
* [Обновление ключа идентификатора Microsoft Store](renew-a-windows-store-id-key.md)
