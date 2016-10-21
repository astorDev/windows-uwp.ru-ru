---
author: mcleanbyron
ms.assetid: E3DF5D11-8791-4CFC-8131-4F59B928A228
description: "Используйте этот метод в API отправки Магазина Windows для получения данных для существующей отправки надстройки."
title: "Получение отправки надстройки с помощью API отправки Магазина Windows"
translationtype: Human Translation
ms.sourcegitcommit: 5f975d0a99539292e1ce91ca09dbd5fac11c4a49
ms.openlocfilehash: 699f26e8a73e1777f5966faf346945807d460315

---

# Получение отправки надстройки с помощью API отправки Магазина Windows




Используйте этот метод в API отправки Магазина Windows для получения данных для существующей отправки надстройки (также называется внутренним продуктом приложения или IAP). Дополнительные сведения о процессе создания отправки надстройки с помощью API отправки Магазина Windows см. в разделе [Управление отправками надстроек](manage-add-on-submissions.md).

## Необходимые условия

Для использования этого метода необходимо выполнить следующие действия:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте отправку надстройки для приложения в учетной записи Центра разработки. Это можно сделать на информационной панели Центра разработки или с помощью метода [Создание отправки надстройки](create-an-add-on-submission.md).

>**Примечание.**&nbsp;&nbsp;Этот метод можно использовать только для учетных записей Центра разработки для Windows, у которых есть разрешение на использование API отправки Магазина Windows. Такое разрешение имеется не у всех учетных записей.

## Запрос

У этого метода следующий синтаксис. Примеры использования и описание заголовка и тела запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET   | ```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}/submissions/{submissionId} ``` |

<span/>
 

### Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/>

### Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| inAppProductId | string | Обязательный. Код продукта в Магазине, принадлежащий надстройке, которая содержит отправку для получения. Этот код отображается на информационной панели Центра разработки, а также добавляется в данные ответов для запросов на [Создание надстройки](create-an-add-on.md) или [Получение сведений о надстройке](get-all-add-ons.md).  |
| submissionId | string | Обязательный. Идентификатор отправки, которую требуется получить. Этот идентификатор отображается на информационной панели Центра разработки, а также добавляется в данные ответов для запросов на [Создание отправки надстройки](create-an-add-on-submission.md).  |

<span/>

### Тело запроса

Предоставлять тело запроса для этого метода не требуется.

### Пример запроса

В следующем примере показано, как получить отправку надстройки.

```
GET https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/9NBLGGH4TNMP/submissions/1152921504621243680 HTTP/1.1
Authorization: Bearer <your access token>
```

## Ответ

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
    "priceId": "Free"
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

## Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 404  | Не удалось найти отправку. |
| 419  | Отправка не принадлежит к указанной надстройке, или надстройка использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки Магазина Windows](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   

<span/>


## Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Создание отправки надстройки](create-an-add-on-submission.md)
* [Подтверждение отправки надстройки](commit-an-add-on-submission.md)
* [Обновление отправки надстройки](update-an-add-on-submission.md)
* [Удаление отправки надстройки](delete-an-add-on-submission.md)
* [Получение состоянии отправки надстройки](get-status-for-an-add-on-submission.md)



<!--HONumber=Aug16_HO5-->

