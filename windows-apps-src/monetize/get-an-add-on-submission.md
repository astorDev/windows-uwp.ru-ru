---
author: Xansky
ms.assetid: E3DF5D11-8791-4CFC-8131-4F59B928A228
description: Используйте этот метод в API отправки в Microsoft Store, чтобы получить данные для существующей отправки надстройки.
title: Получение отправки надстройки
ms.author: mhopkins
ms.date: 04/17/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, API отправки в Microsoft Store, отправка надстройки, продукт внутри приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: e126ce53da515724f5fb1f9992ed8ca27c02ce06
ms.sourcegitcommit: e16c9845b52d5bd43fc02bbe92296a9682d96926
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "4964740"
---
# <a name="get-an-add-on-submission"></a>Получение отправки надстройки

Используйте этот метод в API отправки в Microsoft Store для получения данных для существующей отправки надстройки (также называется внутренним продуктом приложения или IAP). Дополнительные сведения о процессе создания отправки надстройки с помощью API отправки в Microsoft Store см. в разделе [Управление отправками надстроек](manage-add-on-submissions.md).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте отправку надстройки для приложения в учетной записи центра разработки. Это можно сделать на информационной панели центра разработки или с помощью метода [Создание отправки надстройки](create-an-add-on-submission.md).

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET   | ```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}/submissions/{submissionId} ``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| inAppProductId | string | Обязательный. Код продукта в Магазине, принадлежащий надстройке, которая содержит отправку для получения. Этот код отображается на информационной панели Центра разработки, а также добавляется в данные ответов для запросов на [Создание надстройки](create-an-add-on.md) или [Получение сведений о надстройке](get-all-add-ons.md).  |
| submissionId | string | Обязательный. Идентификатор отправки, которую требуется получить. Этот идентификатор добавляется в данные ответов для запросов на [создание отправки надстройки](create-an-add-on-submission.md). Для отправки, которая была создана в информационной панели центра разработки, этот код также доступен по URL-адресу страницы отправки на информационной панели.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

В следующем примере показано, как получить отправку надстройки.

```
GET https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/9NBLGGH4TNMP/submissions/1152921504621243680 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлено тело ответа JSON в случае успешного вызова этого метода. В теле ответа содержатся сведения об указанной отправке. Дополнительные сведения о значениях в теле ответа см. в разделе [Ресурс отправки надстройки](manage-add-on-submissions.md#add-on-submission-object).

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
| 409  | Отправка не принадлежит к указанной надстройке, или надстройка использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки в Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Создание отправки надстройки](create-an-add-on-submission.md)
* [Подтверждение отправки надстройки](commit-an-add-on-submission.md)
* [Обновление отправки надстройки](update-an-add-on-submission.md)
* [Удаление отправки надстройки](delete-an-add-on-submission.md)
* [Получение состоянии отправки надстройки](get-status-for-an-add-on-submission.md)
