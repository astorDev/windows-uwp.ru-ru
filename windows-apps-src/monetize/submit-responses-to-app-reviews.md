---
author: Xansky
ms.assetid: 038903d6-efab-4da6-96b5-046c7431e6e7
description: Используйте этот метод в API отзывов Microsoft Store, чтобы отправлять ответы на отзывы о вашем приложении.
title: Отправка ответов на отзывы
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, службы Store, API отзывов Microsoft Store, приобретение надстроек
ms.localizationpriority: medium
ms.openlocfilehash: 8a8a336d477e7d66222632821f0fa0855faae6f7
ms.sourcegitcommit: f2c9a050a9137a473f28b613968d5782866142c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2018
ms.locfileid: "6275362"
---
# <a name="submit-responses-to-reviews"></a>Отправка ответов на отзывы


Используйте этот метод в API отзывов Microsoft Store, чтобы отправлять ответы на отзывы о вашем приложении программным образом. При вызове этого метода необходимо указать идентификаторы отзывов, на которые отправляются ответы. Идентификаторы отзывов доступны в данных ответов метода [получения отзывов о приложении](get-app-reviews.md) в API аналитики для Microsoft Store и в [автономном](../publish/download-analytic-reports.md) [отчете об отзывах](../publish/reviews-report.md).

При отправке отзыва клиент может отказаться от получения ответов на него. При попытке ответить на отзыв, для которого клиента заблокировал получение ответов, тело ответа этого метода будет содержать информацию о том, попытка ответа не была удачной. До вызова этого метода, при необходимости вы можете определить разрешено ли отвечать на данный отзыв с помощью метода [получение сведений об ответах на отзывы о приложении](get-response-info-for-app-reviews.md).

> [!NOTE]
> Помимо использования этого метода, чтобы программно отвечать на отзывы, вы можете также отвечать на отзывы [с помощью центра партнеров](../publish/respond-to-customer-reviews.md).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо выполнить следующее:

* Если это еще не сделано, выполните все [необходимые условия](respond-to-reviews-using-windows-store-services.md#prerequisites) для API отзывов Microsoft Store.
* [Получите маркер доступа Azure AD](respond-to-reviews-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Получите идентификаторы отзывов, на которые хотите ответить. Идентификаторы отзывов доступны в данных ответов метода [получения отзывов о приложении](get-app-reviews.md) в API аналитики для Microsoft Store и в [автономном](../publish/download-analytic-reports.md) [отчете об отзывах](../publish/reviews-report.md).

## <a name="request"></a>Запрос

### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| POST    | ```https://manage.devcenter.microsoft.com/v1.0/my/reviews/responses``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

Этот метод не имеет параметров запроса.


### <a name="request-body"></a>Текст запроса

Текст запроса содержит следующие значения.

| Значение        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------|
| Responses | array | Массив объектов, содержащих данные отправляемых ответов. Дополнительные сведения о данных в каждом объекте см. в следующей таблице. |


Каждый объект в массиве *Responses* содержит следующие значения.

| Значение        | Тип   | Описание           |  Обязательное  |
|---------------|--------|-----------------------------|-----|
| ApplicationId | string |  Код продукта для приложения с отзывом, на который вы хотите ответить. Код продукта в магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) центра партнеров. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8   |  Да  |
| ReviewId | string |  Идентификатор отзыва, на который вы хотите ответить (это GUID). Идентификаторы отзывов доступны в данных ответов метода [получения отзывов о приложении](get-app-reviews.md) в API аналитики для Microsoft Store и в [автономном](../publish/download-analytic-reports.md) [отчете об отзывах](../publish/reviews-report.md).   |  Да  |
| ResponseText | string | Отправляемый вами ответ. Необходимо следовать [этим рекомендациям](../publish/respond-to-customer-reviews.md#guidelines-for-responses).   |  Да  |
| SupportEmail | string | Адрес электронной почты службы поддержки вашего приложения, по которому клиент может связаться с вами напрямую. Это должен быть допустимый адрес электронной почты.     |  Да  |
| IsPublic | Boolean |  Если задать **значение true**, ваш ответ будет отображаться ваше приложение в описании в магазине, непосредственно под отзывом пользователя и будет виден всем пользователям. Если указать **значение false** и пользователь не отказался от получения ответов по электронной почте, ваш ответ будет отправлен клиенту по электронной почте, и он не будут видны другим пользователям в описании приложения в магазине. Если указать **значение false** и пользователь отказался от получения ответов на отзывы по электронной почте, будет возвращена ошибка.   |  Да  |


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

* [Ответ на отзывы пользователей с помощью центра партнеров](../publish/respond-to-customer-reviews.md)
* [Ответ на отзывы с помощью служб Microsoft Store](respond-to-reviews-using-windows-store-services.md)
* [Получение сведений об ответах на отзывы о приложении](get-response-info-for-app-reviews.md)
* [Получение отзывов о приложении](get-app-reviews.md)
