---
author: mcleanbyron
ms.assetid: 3569C505-8D8C-4D85-B383-4839F13B2466
description: "Используйте этот метод, чтобы обновить ключ Магазина Windows."
title: "Обновление ключа идентификатора Магазина Windows"
ms.sourcegitcommit: 2f4351d6f9bdc0b9a131ad5ead10ffba7e76c437
ms.openlocfilehash: 6255346c568ed24e17c795834ab182f73707c4de

---

# Обновление ключа идентификатора Магазина Windows


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Используйте этот метод, чтобы обновить ключ Магазина Windows. При генерации ключа идентификатора Магазина Windows с помощью метода [**GetCustomerCollectionsIdAsync**](https://msdn.microsoft.com/library/windows/apps/mt608674) и [**GetCustomerPurchaseIdAsync**](https://msdn.microsoft.com/library/windows/apps/mt608675) срок его действия составляет 90 дней. После истечения срока действия ключа его можно использовать для создания нового ключа с помощью данного метода.

## Необходимые условия


Для использования этого метода вам понадобится:

-   маркер доступа Azure AD, созданный с помощью URI аудитории `https://onestore.microsoft.com`;
-   Ключ идентификатора Магазина Windows с истекшим сроком действия, который был создан с помощью метода [**GetCustomerCollectionsIdAsync**](https://msdn.microsoft.com/library/windows/apps/mt608674) или [**GetCustomerPurchaseIdAsync**](https://msdn.microsoft.com/library/windows/apps/mt608675), вызываемого в клиентском коде вашего приложения.

Подробные сведения см. в разделе [Просмотр и предоставление продуктов из службы](view-and-grant-products-from-a-service.md).

## Запрос


### Синтаксис запроса

| Тип ключа    | Метод | URI запроса                                              |
|-------------|--------|----------------------------------------------------------|
| Коллекции | POST   | `https://collections.mp.microsoft.com/v6.0/b2b/keys/renew` |
| Покупка    | POST   | `https://purchase.mp.microsoft.com/v6.0/b2b/keys/renew`    |

<br/> 

### Заголовок запроса

| Заголовок         | Тип   | Описание                                                                                           |
|----------------|--------|-------------------------------------------------------------------------------------------------------|
| Host           | string | Должен иметь значение **collections.mp.microsoft.com** или **purchase.mp.microsoft.com**.           |
| Content-Length | Число | Длина тела запроса.                                                                       |
| Content-Type   | Строка | Указывает тип запросов и ответов. На данный момент единственным поддерживаемым значением является **application/json**. |

<br/> 

### Тело запроса

| Параметр     | Тип   | Описание                       | Обязательный параметр |
|---------------|--------|-----------------------------------|----------|
| serviceTicket | Строка | Маркер доступа Azure AD.        | Да      |
| key           | Строка | Ключ идентификатора Магазина Windows с истекшим сроком действия. | Нет       |

<br/> 

### Пример запроса

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

## Ответ


### Тело ответа

| Параметр | Тип   | Описание                                                                                                            | Обязательный параметр |
|-----------|--------|------------------------------------------------------------------------------------------------------------------------|----------|
| key       | Строка | Обновленный ключ Магазина Windows, который можно использовать при последующих вызовах API-интерфейсов коллекций Магазина Windows или API покупок. | Нет       |

<br/> 

### Пример ответа

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

## Коды ошибок


| Код | Ошибка        | Внутренний код ошибки           | Описание                                                                                                                                                                           |
|------|--------------|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 401  | Unauthorized | AuthenticationTokenInvalid | Маркер доступа Azure AD недействителен. В некоторых случаях сведения об ошибке ServiceError содержат больше информации, например если истек срок действия маркера или отсутствует утверждение *appid*. |
| 401  | Unauthorized | InconsistentClientId       | Утверждение *clientId* в ключе идентификатора Магазина Windows и утверждение *appid* в маркере доступа Azure AD не совпадают.                                                                     |

<br/> 

## Связанные разделы


* [Просмотр и предоставление продуктов из службы](view-and-grant-products-from-a-service.md)
* [Запрос продуктов](query-for-products.md)
* [Объявление потребляемого продукта в качестве выполненного](report-consumable-products-as-fulfilled.md)
* [Предоставление бесплатных продуктов](grant-free-products.md)



<!--HONumber=Jun16_HO4-->


