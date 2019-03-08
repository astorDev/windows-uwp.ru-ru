---
ms.assetid: C09F4B7C-6324-4973-980A-A60035792EFC
description: Используйте этот метод в интерфейсе API отправки Microsoft Store для создания новой отправки надстройки для приложения, зарегистрированный в центре партнеров.
title: Создание отправки надстройки
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, API отправки Microsoft Store, создание отправки надстройки, продукт внутри приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: fcc98252efb1157bc539b68656c96f7afec7104a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57661709"
---
# <a name="create-an-add-on-submission"></a>Создание отправки надстройки


Используйте этот метод в интерфейсе API отправки Microsoft Store для создания новой отправки надстройки (также известная как app продукта или IAP) для приложения, зарегистрированный для вашей учетной записи центра партнеров. После успешного создания новой отправки с помощью этого метода [обновите отправку](update-an-add-on-submission.md), чтобы внести любые необходимые изменения в данные отправки, а затем [зафиксируйте отправку](commit-an-add-on-submission.md) для внедрения и публикации.

Дополнительные сведения об использовании этого метода в процессе создания отправки надстройки с помощью API отправки Microsoft Store см. в разделе [Управление отправками надстроек](manage-add-on-submissions.md).

> [!NOTE]
> При использовании этого метода выполняется создание отправки для имеющейся надстройки. Чтобы создать надстройку, используйте метод [Создание надстройки](create-an-add-on.md).

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.
* Создайте надстройку для одного из ваших приложений. Это можно сделать в центре партнеров, или это можно сделать с помощью [создать надстройку](create-an-add-on.md) метод.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| POST    | ```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}/submissions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| inAppProductId | Строка | Обязательный. Код продукта в Магазине для надстройки, для которой необходимо создать отправку. Идентификатор Store доступен в центре партнеров, и она включена в данные ответа для запросов к [создать надстройку](create-an-add-on.md) или [получение сведений о надстройка](get-all-add-ons.md).  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

В следующем примере кода показано, как создать новую отправку для надстройки.

```
POST https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/9NBLGGH4TNMP/submissions HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. В тексте ответа содержатся сведения о новой отправке. Дополнительные сведения о значениях в теле ответа см. в разделе [Ресурс отправки надстройки](manage-add-on-submissions.md#add-on-submission-object).

```json
{
  "id": "1152921504621243680",
  "contentType": "EMagazine",
  "keywords": [
    "books"
  ],
  "lifetime": "FiveDays",
  "listings": {
    "en": {
      "description": "English add-on description",
      "icon": {
        "fileName": "add-on-en-us-listing2.png",
        "fileStatus": "Uploaded"
      },
      "title": "Add-on Title (English)"
    },
    "ru": {
      "description": "Russian add-on description",
      "icon": {
        "fileName": "add-on-ru-listing.png",
        "fileStatus": "Uploaded"
      },
      "title": "Add-on Title (Russian)"
    }
  },
  "pricing": {
    "marketSpecificPricings": {
      "RU": "Tier3",
      "US": "Tier4",
    },
    "sales": [
      {
         "name": "Sale1",
         "basePriceId": "Free",
         "startDate": "2016-05-21T18:40:11.7369008Z",
         "endDate": "2016-05-22T18:40:11.7369008Z",
         "marketSpecificPricings": {
            "RU": "NotAvailable"
         }
      }
    ],
    "priceId": "Free",
    "isAdvancedPricingModel": true
  },
  "targetPublishDate": "2016-03-15T05:10:58.047Z",
  "targetPublishMode": "Immediate",
  "tag": "SampleTag",
  "visibility": "Public",
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [
      {
        "code": "None",
        "details": "string"
      }
    ],
    "warnings": [
      {
        "code": "ListingOptOutWarning",
        "details": "You have removed listing language(s): []"
      }
    ],
    "certificationReports": [
      {
      }
    ]
  },
  "fileUploadUrl": "https://productingestionbin1.blob.core.windows.net/ingestion/26920f66-b592-4439-9a9d-fb0f014902ec?sv=2014-02-14&sr=b&sig=usAN0kNFNnYE2tGQBI%2BARQWejX1Guiz7hdFtRhyK%2Bog%3D&se=2016-06-17T20:45:51Z&sp=rwl",
  "friendlyName": "Submission 2"
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 400  | Не удалось создать отправку. Недопустимый запрос. |
| 409  | Не удалось создать отправки из-за текущего состояния приложения или приложение использует функцию центра партнеров, которая [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>Статьи по теме

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправкой надстройки](manage-add-on-submissions.md)
* [Получить отправки надстройки](get-an-add-on-submission.md)
* [Зафиксировать отправки надстройки](commit-an-add-on-submission.md)
* [Обновление надстройки отправки](update-an-add-on-submission.md)
* [Удалить надстройку отправки](delete-an-add-on-submission.md)
* [Получение сведений о состоянии отправки надстройки](get-status-for-an-add-on-submission.md)
