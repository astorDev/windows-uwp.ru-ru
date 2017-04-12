---
author: mcleanbyron
ms.assetid: 78278741-09A4-4406-A112-9AF3C73F5C16
description: "Используйте этот метод в API отправки Магазина Windows для получения информации о надстройке для приложения, которое зарегистрировано в вашей учетной записи Центра разработки для Windows."
title: "Получение надстройки"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API отправки Магазина Windows, надстройка, продукт внутри приложения, IAP"
ms.openlocfilehash: a79c93c1ed7d38b4de1da2bbd05456fd0884692e
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="get-an-add-on"></a>Получение надстройки




Используйте этот метод в API отправки Магазина Windows для получения сведений о надстройке (также называется внутренним продуктом приложения или IAP) для приложения, которое зарегистрировано в вашей учетной записи Центра разработки для Windows.

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

>**Примечание.**&nbsp;&nbsp;Этот метод можно использовать только для учетных записей Центра разработки для Windows, у которых есть разрешение на использование API отправки Магазина Windows. Такое разрешение имеется не у всех учетных записей.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}``` |

<span/>
 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательное. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/>

### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| id | Строка | Обязательный. Код продукта в Магазине для надстройки, которую требуется получить. Код продукта в Магазине отображается на информационной панели Центра разработки.  |

<span/>

### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

<span/>

### <a name="request-example"></a>Пример запроса

В приведенном ниже примере показано, как получить информацию о надстройке.

```
GET https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/9NBLGGH4TNMP HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON, обеспечивающий успешный вызов этого метода. Дополнительные сведения о значениях в тексте ответа см. в разделе [Ресурс надстройки](manage-add-ons.md#add-on-object).

```json
{
  "applications": {
    "value": [
      {
        "id": "9NBLGGH4R315",
        "resourceLocation": "applications/9NBLGGH4R315"
      }
    ],
    "totalCount": 1
  },
  "id": "9NBLGGH4TNMP",
  "productId": "Test-add-on",
  "productType": "Durable",
  "pendingInAppProductSubmission": {
    "id": "1152921504621243619",
    "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243619"
  },
  "lastPublishedInAppProductSubmission": {
    "id": "1152921504621243705",
    "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243705"
  }
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 404  | Не удалось найти указанную надстройку. |
| 409  | Надстройка использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки Магазина Windows](create-and-manage-submissions-using-windows-store-services.md#not_supported).  |

<span/>

## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправками надстроек](manage-add-on-submissions.md)
* [Получение всех надстроек](get-all-add-ons.md)
* [Создание надстройки](create-an-add-on.md)
* [Удаление надстройки](delete-an-add-on.md)
