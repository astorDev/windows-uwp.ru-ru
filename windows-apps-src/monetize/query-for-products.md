---
ms.assetid: D1F233EC-24B5-4F84-A92F-2030753E608E
description: Используйте этот метод в API коллекции Microsoft Store для получения всех продуктов, принадлежащих пользователю, для приложений, связанных с вашим идентификатором клиента Azure AD. Вы можете ограничить запрос определенным продуктом или использовать другие фильтры.
title: Запрос продуктов
ms.date: 03/19/2018
ms.topic: article
keywords: Windows 10, API коллекции Microsoft Store, uwp, просмотр продуктов
ms.localizationpriority: medium
ms.openlocfilehash: 700cb111f74a4534f2f5e1de70eddfb88b456aa7
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57596809"
---
# <a name="query-for-products"></a>Запрос продуктов


Используйте этот метод в API коллекции Microsoft Store для получения всех продуктов, принадлежащих пользователю, для приложений, связанных с вашим идентификатором клиента Azure AD. Вы можете ограничить запрос определенным продуктом или использовать другие фильтры.

Этот метод вызывается вашей службой в ответ на сообщение из вашего приложения. Ваша служба не должна регулярно опрашивать всех пользователей в расписании.

## <a name="prerequisites"></a>Предварительные условия


Для использования этого метода вам понадобится:

* Маркер доступа Azure AD, имеющий значение URI аудитории `https://onestore.microsoft.com`.
* Ключ идентификатора Microsoft Store, представляющий удостоверение пользователя, продукты которого вы хотите получить.

Дополнительные сведения см. в разделе [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md).

## <a name="request"></a>Запрос

### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса                                                 |
|--------|-------------------------------------------------------------|
| POST   | ```https://collections.mp.microsoft.com/v6.0/collections/query``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок         | Тип   | Описание                                                                                           |
|----------------|--------|-------------------------------------------------------------------------------------------------------|
| Authorization  | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;.                           |
| Hyper-V           | Строка | Должен иметь значение **collections.mp.microsoft.com**.                                            |
| Content-Length | number | Длина текста запроса.                                                                       |
| Content-Type   | Строка | Указывает тип запросов и ответов. На данный момент единственным поддерживаемым значением является **application/json**. |


### <a name="request-body"></a>Тело запроса

| Параметр         | Тип         | Описание         | Обязательно |
|-------------------|--------------|---------------------|----------|
| beneficiaries     | UserIdentity | Объект UserIdentity, который представляет пользователя, опрашиваемого на предмет продуктов. Дополнительные сведения см. в таблице ниже.    | Да      |
| continuationToken | Строка       | Если имеется несколько наборов продуктов, то по достижении лимита для страницы тело ответа возвращает маркер продолжения. Укажите здесь этот маркер продолжения, чтобы в последующих вызовах получить информацию об оставшихся продуктах.       | Нет       |
| maxPageSize       | number       | Максимальное количество продуктов, возвращаемых в одном ответе. Стандартное и максимальное значение — 100.                 | Нет       |
| modifiedAfter     | datetime     | Если параметр указан, служба возвращает только продукты, которые были изменены после этой даты.        | Нет       |
| parentProductId   | Строка       | Если параметр указан, служба возвращает только надстройки, которые соответствуют указанному приложению.      | Нет       |
| productSkuIds     | list&lt;ProductSkuId&gt; | Если параметр указан, служба возвращает только продукты, соответствующие предоставленным парам «продукт — SKU». Дополнительные сведения см. в таблице ниже.      | Нет       |
| productTypes      | список&lt;строки&gt;       | Указывает, какие типы продуктов для возврата в результатах запроса. Поддерживаемые типы продуктов: **Application**, **Durable** и **UnmanagedConsumable**.     | Да       |
| validityType      | Строка       | Если выбрано значение **All**, возвращаются все продукты пользователя, в том числе продукты с истекшим сроком. Если выбрано значение **Valid**, возвращаются только продукты, действительные на данный момент (то есть в активном состоянии, с датой начала &lt; ранее текущего момента и датой окончания &gt; позднее текущего момента). | Нет       |


Объект UserIdentity содержит следующие параметры.

| Параметр            | Тип   |  Описание      | Обязательно |
|----------------------|--------|----------------|----------|
| identityType         | Строка | Укажите строковое значение **b2b**.    | Да      |
| identityValue        | Строка | [Ключ идентификатора Microsoft Store](view-and-grant-products-from-a-service.md#step-4), представляющий пользователя, продукты которого вы хотите запросить.  | Да      |
| localTicketReference | Строка | Запрошенный идентификатор для возвращаемых продуктов. Элементы, возвращаемые в теле ответа, будут иметь соответствующее значение параметра *localTicketReference*. Рекомендуется использовать то же значение, что и в утверждении *userId* в ключе идентификатора Microsoft Store. | Да      |


Объект ProductSkuId содержит следующие параметры.

| Параметр | Тип   | Описание          | Обязательно |
|-----------|--------|----------------------|----------|
| productId | Строка | Код [продукта](in-app-purchases-and-trials.md#products-skus-and-availabilities) в [Store](in-app-purchases-and-trials.md#store-ids) в каталоге Microsoft Store. Пример кода продукта в Магазине: 9NBLGGH42CFD. | Да      |
| skuID     | Строка | Код [SKU](in-app-purchases-and-trials.md#products-skus-and-availabilities) продукта в [Store](in-app-purchases-and-trials.md#store-ids) в каталоге Microsoft Store. Пример кода продукта в Магазине для номера SKU: 0010.       | Да      |


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

| Параметр         | Тип                     | Описание          | Обязательно |
|-------------------|--------------------------|-----------------------|----------|
| continuationToken | Строка                   | Если имеется несколько наборов продуктов, то по достижении лимита для страницы возвращается этот маркер. Вы можете указать маркер продолжения в последующих вызовах для извлечения остальных продуктов. | Нет       |
| элементы             | CollectionItemContractV6 | Массив продуктов для указанного пользователя. Дополнительные сведения см. в таблице ниже.        | Нет       |


Объект CollectionItemContractV6 содержит следующие параметры.

| Параметр            | Тип               | Описание            | Обязательно |
|----------------------|--------------------|-------------------------|----------|
| acquiredDate         | datetime           | Дата приобретения элемента пользователем.                  | Да      |
| campaignId           | Строка             | Идентификатор кампании, предоставленный во время покупки для этого элемента.                  | Нет       |
| devOfferId           | Строка             | Идентификатор предложения покупки из приложения.              | Нет       |
| endDate              | datetime           | Дата окончания срока действия элемента.              | Да      |
| fulfillmentData      | Строка             | Н/Д         | Нет       |
| inAppOfferToken      | Строка             | Строка идентификатора продукта указанных разработчиком, назначенный элементу в центре партнеров. Пример продукта — идентификатор *product123*. | Нет       |
| itemId               | Строка             | Идентификатор, отличающий этот элемент коллекции от других элементов, принадлежащих пользователю. Этот идентификатор уникален для каждого продукта.   | Да      |
| localTicketReference | Строка             | Идентификатор ранее предоставленного в тексте запроса параметра *localTicketReference*.                  | Да      |
| modifiedDate         | datetime           | Дата последнего изменения элемента.              | Да      |
| orderId              | Строка             | Если он указан, идентификатор заказа, из которого был получен данный элемент.              | Нет       |
| orderLineItemId      | Строка             | Если указан, означает позицию конкретного заказа данного элемента.              | Нет       |
| ownershipType        | Строка             | Строка *OwnedByBeneficiary*.   | Да      |
| productId            | Строка             | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для [продукта](in-app-purchases-and-trials.md#products-skus-and-availabilities) в каталоге Microsoft Store. Пример кода продукта в Магазине: 9NBLGGH42CFD.          | Да      |
| productType          | Строка             | Один из следующих типов продукта: **Приложение**, **устойчивых**, и **UnmanagedConsumable**.        | Да      |
| purchasedCountry     | Строка             | Н/Д   | Нет       |
| purchaser            | IdentityContractV6 | Если он указан, означает удостоверение покупателя элемента. Сведения для этого объекта см. ниже.        | Нет       |
| quantity             | number             | Количество экземпляров элемента. На данный момент это значение всегда составляет 1.      | Нет       |
| skuId                | Строка             | [Код продукта в Store](in-app-purchases-and-trials.md#store-ids) для [SKU](in-app-purchases-and-trials.md#products-skus-and-availabilities) продукта в каталоге Microsoft Store. Пример кода продукта в Магазине для номера SKU: 0010.     | Да      |
| skuType              | Строка             | Тип SKU. Возможные значения включают **Trial**, **Full** и **Rental**.        | Да      |
| startDate            | datetime           | Дата, начиная с которой элемент действителен.       | Да      |
| status               | Строка             | Состояние элемента. Возможные значения включают **Active**, **Expired**, **Revoked** и **Banned**.    | Да      |
| tags                 | Строка             | Н/Д    | Да      |
| transactionId        | guid               | Идентификатор транзакции в результате покупки элемента. Может использоваться для объявления элемента в качестве выполненного.      | Да      |


Объект IdentityContractV6 содержит следующие параметры.

| Параметр     | Тип   | Описание                                                                        | Обязательно |
|---------------|--------|------------------------------------------------------------------------------------|----------|
| identityType  | Строка | Содержит значение *pub*.                                                      | Да      |
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

* [Управление прав продукта из службы](view-and-grant-products-from-a-service.md)
* [Сообщить о готовых к использованию продуктов, что выполнен](report-consumable-products-as-fulfilled.md)
* [Предоставьте бесплатные продукты](grant-free-products.md)
* [Обновить ключ идентификатор Microsoft Store](renew-a-windows-store-id-key.md)
