---
author: mcleanbyron
ms.assetid: 038903d6-efab-4da6-96b5-046c7431e6e7
description: "Используйте этот метод API-интерфейса отзывов Магазина Windows для отправки ответов на отзывы о вашем приложении."
title: "Отправка ответов на отзывы о приложении"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, службы Магазина, API отзывов Магазина Windows , приобретение надстроек"
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 1531059831b4c20d11661eb87fceda7b8dcb7f02
ms.lasthandoff: 02/08/2017

---

# <a name="submit-responses-to-app-reviews"></a>Отправка ответов на отзывы о приложении


Используйте этот метод API-интерфейса отзывов Магазина Windows для программной отправки ответов на отзывы о вашем приложении. При вызове этого метода необходимо указать идентификаторы отзывов, на которые отправляются ответы. Идентификаторы отзывов доступны в данных ответа метода [получение отзывов о приложении](get-app-reviews.md) API аналитики Магазина Windows и в [автономная загрузка](../publish/download-analytic-reports.md) [отчета об отзывах](../publish/reviews-report.md).

При отправке отзыва клиент может отказаться от получения ответов на него. При попытке ответить на отзыв, для которого клиента заблокировал получение ответов, тело ответа этого метода будет содержать информацию о том, попытка ответа не была удачной. До вызова этого метода, при необходимости вы можете определить разрешено ли отвечать на данный отзыв с помощью метода [получение сведений об ответах на отзывы о приложении](get-response-info-for-app-reviews.md).

>**Примечание**&nbsp;&nbsp;Помимо использования этого метода для программного ответа на отзывы , вы также можете реагировать на отзывы [с помощью информационной панели Центра разработки для Windows](../publish/respond-to-customer-reviews.md).

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо выполнить следующее:

* Если это еще не сделано, выполните все [необходимые условия](respond-to-reviews-using-windows-store-services.md#prerequisites) для API отзывов Магазина Windows.
* [Получите маркер доступа Azure AD](respond-to-reviews-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Получите идентификаторы отзывов, на которые хотите ответить. Идентификаторы отзывов доступны в данных ответа метода [получение отзывов о приложении](get-app-reviews.md) API аналитики Магазина Windows и в [автономная загрузка](../publish/download-analytic-reports.md) [отчета об отзывах](../publish/reviews-report.md).

## <a name="request"></a>Запрос

### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| POST    | ```https://manage.devcenter.microsoft.com/v1.0/my/reviews/responses``` |

<span/> 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/> 

### <a name="request-parameters"></a>Параметры запроса

Этот метод не имеет параметров запроса.

<span/> 

### <a name="request-body"></a>Текст запроса

Текст запроса содержит следующие значения.

| Значение        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------|
| Responses | array | Массив объектов, содержащих данные отправляемых ответов. Дополнительные сведения о данных в каждом объекте см. в следующей таблице. |

Каждый объект в массиве *Responses* содержит следующие значения.

| Значение        | Тип   | Описание           |  Обязательное  |
|---------------|--------|-----------------------------|-----|
| ApplicationId | string |  Код продукта для приложения с отзывом, на который вы хотите ответить. Код продукта в Магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) информационной панели Центра разработки. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8.   |  Да  |
| ReviewId | string |  Идентификатор отзыва, на который вы хотите ответить (это GUID). Идентификаторы отзывов доступны в данных ответа метода [получение отзывов о приложении](get-app-reviews.md) API аналитики Магазина Windows и в [автономная загрузка](../publish/download-analytic-reports.md) [отчета об отзывах](../publish/reviews-report.md).   |  Да  |
| ResponseText | string | Отправляемый вами ответ. Необходимо следовать [этим рекомендациям](../publish/respond-to-customer-reviews.md#guidelines-for-responses).   |  Да  |
| SupportEmail | string | Адрес электронной почты службы поддержки вашего приложения, по которому клиент может связаться с вами напрямую. Это должен быть допустимый адрес электронной почты.     |  Да  |
| IsPublic | Boolean |  Значение **true** показывает, что ваш ответ будет отображаться на странице описания приложения в Магазине, непосредственно под отзывом клиента и будет виден всем пользователям. Значение **false** показывает, что ваш ответ будет отправлен клиенту по электронной почте и не будет виден другим пользователям на странице описания приложения в Магазине.     |  Да  |

### <a name="request-example"></a>Пример запроса

В следующем примере показано, как использовать этот метод для отправки ответов на несколько отзывов.

```json
POST https://manage.devcenter.microsoft.com/v1.0/my/reviews/responses HTTP/1.1
Authorization: Bearer <your access token>
Content-Type: application/json
{
  "Responses": [
    {
      "ApplicationId": "9WZDNCRFJ3Q8",
      "ReviewId": "6be543ff-1c9c-4534-aced-af8b4fbe0316",
      "ResponseText": "Thank you for pointing out this bug. I fixed it and published an update, you should have the fix soon",
      "SupportEmail": "support@contoso.com",
      "IsPublic": "true"
    },
    {
      "ApplicationId": "9NBLGGH1RP08",
      "ReviewId": "80c9671a-96c2-4278-bcbc-be0ce5a32a7c",
      "ResponseText": "Thank you for submitting your review. Can you tell more about what you were doing in the app when it froze? Thanks very much for your help.",
      "SupportEmail": "support@contoso.com",
      "IsPublic": "false"
    }
  ]
}
```

## <a name="response"></a>Ответ

### <a name="response-body"></a>Тело ответа

| Значение        | Тип   | Описание            |
|---------------|--------|---------------------|
| Result | array | Массив объектов, содержащих данные о каждом ответе, отправленном вами. Дополнительные сведения о данных в каждом объекте см. в следующей таблице.  |

Каждый объект в массиве *Result* содержит следующие значения.

| Значение        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------|
| ApplicationId | string |  Код продукта для приложения с отзывом, на который вы ответили. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8.   |
| ReviewId | string |  Идентификатор отзыва, на который вы ответили. Это идентификатор GUID.   |
| Successful | string | Значение **true** показывает, что ваш ответ был отправлен успешно. Значение **false** указывает, что ваш ответ не удалось отправить.    |
| FailureReason | string | Если **Successful** равно **false**, то это значение содержит причину сбоя. Если **Successful** равно **true**, то значение будет пустым.      |

### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "Result": [
    {
      "ApplicationId": "9WZDNCRFJ3Q8",
      "ReviewId": "6be543ff-1c9c-4534-aced-af8b4fbe0316",
      "Successful": "true",
      "FailureReason": ""
    },
    {
      "ApplicationId": "9NBLGGH1RP08",
      "ReviewId": "80c9671a-96c2-4278-bcbc-be0ce5a32a7c",
      "Successful": "false",
      "FailureReason": "No Permission"
    }
  ]
}
```

## <a name="related-topics"></a>Статьи по теме

* [Ответ на отзывы клиентов с помощью информационной панели Центра разработки](../publish/respond-to-customer-reviews.md)
* [Реакция на отзывы с помощью служб Магазина Windows](respond-to-reviews-using-windows-store-services.md)
* [Получение сведений об ответах на отзывы о приложении](get-response-info-for-app-reviews.md)
* [Получение отзывов о приложении](get-app-reviews.md)
