---
author: mcleanbyron
ms.assetid: 3569C505-8D8C-4D85-B383-4839F13B2466
description: "Используйте этот метод, чтобы обновить ключ Магазина Windows."
title: "Обновление ключа идентификатора Магазина Windows"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, API коллекции Магазина Windows, API покупок Магазина Windows, ключ Магазина Windows, обновление"
ms.openlocfilehash: 22db5f1ae693c26ecf727c94a9f6746225325f74
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="renew-a-windows-store-id-key"></a>Обновление ключа идентификатора Магазина Windows


Используйте этот метод, чтобы обновить ключ Магазина Windows. При [генерации ключа идентификатора Магазина Windows](view-and-grant-products-from-a-service.md#step-4) ключ действителен в течение 90 дней. После истечения срока действия ключа его можно использовать для создания нового ключа с помощью данного метода.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода вам понадобится:

* маркер доступа Azure AD, созданный с помощью URI аудитории `https://onestore.microsoft.com`;
* ключ идентификатора Магазина Windows с истекшим сроком действия, [сгенерированный из клиентского кода вашего приложения](view-and-grant-products-from-a-service.md#step-4).

Дополнительные сведения см. в разделе [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md).

## <a name="request"></a>Запрос

### <a name="request-syntax"></a>Синтаксис запроса

| Тип ключа    | Метод | URI запроса                                              |
|-------------|--------|----------------------------------------------------------|
| Коллекции | POST   | ```https://collections.mp.microsoft.com/v6.0/b2b/keys/renew``` |
| Покупка    | POST   | ```https://purchase.mp.microsoft.com/v6.0/b2b/keys/renew```    |

<span/>

### <a name="request-header"></a>Заголовок запроса

| Заголовок         | Тип   | Описание                                                                                           |
|----------------|--------|-------------------------------------------------------------------------------------------------------|
| Host           | строка | Должен иметь значение **collections.mp.microsoft.com** или **purchase.mp.microsoft.com**.           |
| Content-Length | Число | Длина текста запроса.                                                                       |
| Content-Type   | Строка | Указывает тип запросов и ответов. На данный момент единственным поддерживаемым значением является **application/json**. |

<span/>

### <a name="request-body"></a>Тело запроса

| Параметр     | Тип   | Описание                       | Обязательный |
|---------------|--------|-----------------------------------|----------|
| serviceTicket | строка | Маркер доступа Azure AD.        | Да      |
| key           | строка | Ключ идентификатора Магазина Windows с истекшим сроком действия. | Нет       |

<span/> 

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

| Параметр | Тип   | Описание                                                                                                            | Обязательный |
|-----------|--------|------------------------------------------------------------------------------------------------------------------------|----------|
| key       | строка | Обновленный ключ Магазина Windows, который можно использовать при последующих вызовах API-интерфейсов коллекций Магазина Windows или API покупок. | Нет       |

<span/>

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


| Код | Ошибка        | Внутренний код ошибки           | Описание                                                                                                                                                                           |
|------|--------------|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 401  | Unauthorized | AuthenticationTokenInvalid | Маркер доступа Azure AD недействителен. В некоторых случаях сведения об ошибке ServiceError содержат больше информации, например если истек срок действия маркера или отсутствует утверждение *appid*. |
| 401  | Unauthorized | InconsistentClientId       | Утверждение *clientId* в ключе идентификатора Магазина Windows и утверждение *appid* в маркере доступа Azure AD не совпадают.                                                                     |

<span/>

## <a name="related-topics"></a>Статьи по теме


* [Управление правами на продукты из службы](view-and-grant-products-from-a-service.md)
* [Запрос продуктов](query-for-products.md)
* [Объявление потребляемого продукта в качестве выполненного](report-consumable-products-as-fulfilled.md)
* [Предоставление бесплатных продуктов](grant-free-products.md)
