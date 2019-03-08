---
ms.assetid: 3569C505-8D8C-4D85-B383-4839F13B2466
description: Используйте этот метод, чтобы обновить ключ Microsoft Store.
title: Обновление ключа идентификатора Microsoft Store
ms.date: 03/19/2018
ms.topic: article
keywords: windows 10, uwp, API коллекции Microsoft Store, API покупок Microsoft Store, ключ Microsoft Store, обновление
ms.localizationpriority: medium
ms.openlocfilehash: fd4d7ce26e12f7ff939ced8d456390b97d0c8a0d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57620489"
---
# <a name="renew-a-microsoft-store-id-key"></a>Обновление ключа идентификатора Microsoft Store


Используйте этот метод, чтобы обновить ключ Microsoft Store. При [генерации ключа идентификатора Microsoft Store](view-and-grant-products-from-a-service.md#step-4) ключ действителен в течение 90 дней. После истечения срока действия ключа его можно использовать для создания нового ключа с помощью данного метода.

## <a name="prerequisites"></a>Предварительные условия


Для использования этого метода вам понадобится:

* Маркер доступа Azure AD, имеющий значение URI аудитории `https://onestore.microsoft.com`.
* Ключ идентификатора Microsoft Store с истекшим сроком действия, [сгенерированный из клиентского кода вашего приложения](view-and-grant-products-from-a-service.md#step-4).

Дополнительные сведения см. в разделе [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md).

## <a name="request"></a>Запрос

### <a name="request-syntax"></a>Синтаксис запроса

| Тип ключа    | Метод | Универсальный код ресурса (URI) запроса                                              |
|-------------|--------|----------------------------------------------------------|
| Коллекции | POST   | ```https://collections.mp.microsoft.com/v6.0/b2b/keys/renew``` |
| Приобретение    | POST   | ```https://purchase.mp.microsoft.com/v6.0/b2b/keys/renew```    |


### <a name="request-header"></a>Заголовок запроса

| Заголовок         | Тип   | Описание                                                                                           |
|----------------|--------|-------------------------------------------------------------------------------------------------------|
| Hyper-V           | Строка | Должен иметь значение **collections.mp.microsoft.com** или **purchase.mp.microsoft.com**.           |
| Content-Length | number | Длина текста запроса.                                                                       |
| Content-Type   | Строка | Указывает тип запросов и ответов. На данный момент единственным поддерживаемым значением является **application/json**. |


### <a name="request-body"></a>Тело запроса

| Параметр     | Тип   | Описание                       | Обязательно |
|---------------|--------|-----------------------------------|----------|
| serviceTicket | Строка | Маркер доступа Azure AD.        | Да      |
| key           | Строка | Ключ идентификатора Microsoft Store с истекшим сроком действия. | Да       |


### <a name="request-example"></a>Пример запроса

```syntax
POST https://collections.mp.microsoft.com/v6.0/b2b/keys/renew HTTP/1.1
Content-Length: 2774
Content-Type: application/json
Host: collections.mp.microsoft.com

{
    "serviceTicket": "eyJ0eXAiOiJKV1QiLCJhb….",
    "Key": "eyJ0eXAiOiJKV1QiLCJhbG…."
}
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Параметр | Тип   | Описание                                                                                                            |
|-----------|--------|------------------------------------------------------------------------------------------------------------------------|
| key       | Строка | Обновленный ключ Microsoft Store, который можно использовать при последующих вызовах API-интерфейсов коллекций Microsoft Store или API покупок. |


### <a name="response-example"></a>Пример ответа

```syntax
HTTP/1.1 200 OK
Content-Length: 1646
Content-Type: application/json
MS-CorrelationId: bfebe80c-ff89-4c4b-8897-67b45b916e47
MS-RequestId: 1b5fa630-d672-4971-b2c0-3713f4ea6c85
MS-CV: xu2HW6SrSkyfHyFh.0.0
MS-ServerId: 030011428
Date: Tue, 13 Sep 2015 07:31:12 GMT

{
    "key":"eyJ0eXAi….."
}
```

## <a name="error-codes"></a>Коды ошибок


| Код | Ошибка        | Внутренний код ошибки           | Описание   |
|------|--------------|----------------------------|---------------|
| 401  | Недостаточно прав | AuthenticationTokenInvalid | Маркер доступа Azure AD недействителен. В некоторых случаях сведения об ошибке ServiceError содержат больше информации, например если истек срок действия маркера или отсутствует утверждение *appid*. |
| 401  | Недостаточно прав | InconsistentClientId       | Утверждение *clientId* в ключе идентификатора Microsoft Store и утверждение *appid* в маркере доступа Azure AD не совпадают.                                                                     |


## <a name="related-topics"></a>Статьи по теме


* [Управление прав продукта из службы](view-and-grant-products-from-a-service.md)
* [Запрос для продуктов](query-for-products.md)
* [Сообщить о готовых к использованию продуктов, что выполнен](report-consumable-products-as-fulfilled.md)
* [Предоставьте бесплатные продукты](grant-free-products.md)
