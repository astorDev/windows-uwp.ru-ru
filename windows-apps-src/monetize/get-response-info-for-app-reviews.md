---
author: Xansky
ms.assetid: fb6bb856-7a1b-4312-a602-f500646a3119
description: Используйте этот метод в API отзывов для Microsoft Store, чтобы определять возможность ответа на определенный или любой отзыв о конкретном приложении.
title: Получение сведений об ответах на отзывы
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, службы Store, API отзывов для Microsoft Store, сведения об ответах
ms.localizationpriority: medium
ms.openlocfilehash: 466455a5e8da9364206245f1e0ac10acfed07ee7
ms.sourcegitcommit: 4d88adfaf544a3dab05f4660e2f59bbe60311c00
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6464618"
---
# <a name="get-response-info-for-reviews"></a>Получение сведений об ответах на отзывы

Если вы хотите программно отвечать на отзыв пользователя о вашем приложении, можно использовать этот метод в API отзывов для Microsoft Store, чтобы указать, есть у вас разрешение оставлять ответ на отзыв или нет. Невозможно ответить на отзывы от пользователей, которые отказались получать ответы на отзывы. Убедившись, что вы можете ответить на отзыв, вы можете воспользоваться методом [отправки ответов на отзывы о приложении](submit-responses-to-app-reviews.md) для программного ответа на него.


## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](respond-to-reviews-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](respond-to-reviews-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия токена можно получить новый токен.
* Получите ИД отзыва, которые требуется проверить, чтобы определить, можете ли вы на него ответить. Идентификаторы отзывов доступны в данных ответов метода [получения отзывов о приложении](get-app-reviews.md) в API аналитики для Microsoft Store и в [автономном](../publish/download-analytic-reports.md) [отчете об отзывах](../publish/reviews-report.md).

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/reviews/{reviewId}/apps/{applicationId}/responses/info``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   | Описание                                     |  Обязательный  |
|---------------|--------|--------------------------------------------------|--------------|
| applicationId | строка | Код продукта в Магазине для приложения, содержащего отзыв, возможность ответа на который требуется определить. Код продукта в магазине доступен на [странице удостоверения приложения](../publish/view-app-identity-details.md) в центре партнеров. Пример кода продукта в Магазине: 9WZDNCRFJ3Q8 |  Да  |
| reviewId | строка | Идентификатор отзыва, на который вы хотите ответить (это GUID). Идентификаторы отзывов доступны в данных ответов метода [получения отзывов о приложении](get-app-reviews.md) в API аналитики для Microsoft Store и в [автономном](../publish/download-analytic-reports.md) [отчете об отзывах](../publish/reviews-report.md). <br/>Если вы опустите этот параметр, в тексте ответа для этого метода будет указано, есть ли у вас разрешения на ответ на какие-либо отзывы об указанном приложении. |  Нет  |


### <a name="request-example"></a>Пример запроса

В следующем примере показано, как использовать этот метод, чтобы определить, можете ли вы ответить на конкретный отзыв.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/reviews/6be543ff-1c9c-4534-aced-af8b4fbe0316/apps/9WZDNCRFJ3Q8/responses/info HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ


### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание    |  
|------------|--------|-----------------------|
| CanRespond      | Логическое  | Значение **true** указывает, что вы можете ответить на указанный отзыв или что у вас есть разрешение на ответ на любой отзыв об указанном приложении. В противном случае значением будет **false**.       |
| DefaultSupportEmail  | строка |  [Адрес электронной почты службы поддержки](../publish/enter-app-properties.md#support-contact-info) вашего приложения, указанный в описании вашего приложения в Магазине. Если вы не указали адрес электронной почты службы поддержки, это поле будет пустым.    |

 
### <a name="response-example"></a>Пример ответа

В следующем примере демонстрируется пример тела ответа JSON на данный запрос.

```json
{
  "CanRespond": true,
  "DefaultSupportEmail": "support@contoso.com"
}
```

## <a name="related-topics"></a>Статьи по теме

* [Отправка ответов на отзывы с помощью API аналитики для Microsoft Store](submit-responses-to-app-reviews.md)
* [Ответ на отзывы пользователей с помощью центра партнеров](../publish/respond-to-customer-reviews.md)
* [Ответ на отзывы с помощью служб Microsoft Store](respond-to-reviews-using-windows-store-services.md)
* [Получение отзывов о приложении](get-app-reviews.md)
