---
ms.assetid: E3DF5D11-8791-4CFC-8131-4F59B928A228
description: Используйте этот метод в API отправки в Microsoft Store, чтобы получить данные для существующей отправки надстройки.
title: Получение отправки надстройки
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, отправка надстройки, продукт внутри приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: 8d997dd4d1369ef09f29a165f9e0554d6f3ad7c7
ms.sourcegitcommit: 6a7dd4da2fc31ced7d1cdc6f7cf79c2e55dc5833
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58334750"
---
# <a name="get-an-add-on-submission"></a>Получение отправки надстройки

Используйте этот метод в API отправки в Microsoft Store для получения данных для существующей отправки надстройки (также называется внутренним продуктом приложения или IAP). Дополнительные сведения о процессе создания отправки надстройки с помощью API отправки в Microsoft Store см. в разделе [Управление отправками надстроек](manage-add-on-submissions.md).

## <a name="prerequisites"></a>предварительные требования

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.
* Создайте надстройку адресация, одно из приложений. Это можно сделать в центре партнеров, или это можно сделать с помощью [создать надстройку отправки](create-an-add-on-submission.md) метод.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| GET   | `https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}/submissions/{submissionId} ` |


### <a name="request-header"></a>Заголовок запроса

| Header        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| inAppProductId | строка | Обязательный. Код продукта в Магазине, принадлежащий надстройке, которая содержит отправку для получения. Идентификатор Store доступен в центре партнеров, и она включена в данные ответа для запросов к [создать надстройку](create-an-add-on.md) или [получение сведений о надстройка](get-all-add-ons.md).  |
| submissionId | строка | Обязательный. Идентификатор отправки, которую требуется получить. Этот идентификатор добавляется в данные ответов для запросов на [создание отправки надстройки](create-an-add-on-submission.md). Для отправки, который был создан в центре партнеров этот идентификатор также доступна в URL-АДРЕСЕ для отправки страницы в центре партнеров.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

В следующем примере показано, как получить отправку надстройки.

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/9NBLGGH4TNMP/submissions/1152921504621243680 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. В теле ответа содержатся сведения об указанной отправке. Дополнительные сведения о значениях в теле ответа см. в разделе [Ресурс отправки надстройки](manage-add-on-submissions.md#add-on-submission-object).

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
    "sales": [],
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
| 404  | Не удалось найти отправку. |
| 409  | Отправка не принадлежит указанная надстройка или надстройка использует возможности центра партнеров, [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>См. также

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Создайте надстройку отправки](create-an-add-on-submission.md)
* [Зафиксировать отправки надстройки](commit-an-add-on-submission.md)
* [Обновление надстройки отправки](update-an-add-on-submission.md)
* [Удалить надстройку отправки](delete-an-add-on-submission.md)
* [Получение сведений о состоянии отправки надстройки](get-status-for-an-add-on-submission.md)
