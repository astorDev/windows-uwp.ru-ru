---
ms.assetid: E8751EBF-AE0F-4107-80A1-23C186453B1C
description: Используйте этот метод в API отправки Microsoft Store для обновления существующей отправки приложения.
title: Обновление отправки приложения
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки Microsoft Store, отправка приложения, обновление
ms.localizationpriority: medium
ms.openlocfilehash: 77c033ff09d56448f42d1f8084265ac0aa5d5212
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371432"
---
# <a name="update-an-app-submission"></a>Обновление отправки приложения

Используйте этот метод в API отправки Microsoft Store для обновления существующей отправки приложения. После успешного обновления отправки с помощью этого метода необходимо [зафиксировать отправку](commit-an-app-submission.md) для проверки и публикации.

Дополнительные сведения об использовании этого метода в процессе создания отправки приложения с помощью API отправки Microsoft Store см. в разделе [Управление отправками приложений](manage-app-submissions.md).

## <a name="prerequisites"></a>Предварительные требования

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.
* Создайте такие данные для одного из ваших приложений. Это можно сделать в центре партнеров, или это можно сделать с помощью [создать Отправка приложения](create-an-app-submission.md) метод.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| PUT   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}  ``` |


### <a name="request-header"></a>Заголовок запроса

| Header        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | строка | Обязательный. Код продукта в Магазине для приложения, отправку которого необходимо обновить. Подробнее о коде продукта в Магазине см. в статье [Просмотр сведений об идентификации приложений](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| submissionId | строка | Обязательный. Идентификатор отправки для обновления. Этот идентификатор добавляется в данные ответов для запросов на [создание отправки приложения](create-an-app-submission.md). Для отправки, который был создан в центре партнеров этот идентификатор также доступна в URL-АДРЕСЕ для отправки страницы в центре партнеров.  |


### <a name="request-body"></a>Тело запроса

Текст запроса содержит следующие параметры.

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| applicationCategory           | строка  |   Строка, указывающая [категорию или подкатегорию](https://docs.microsoft.com/windows/uwp/publish/category-and-subcategory-table) для вашего приложения. Категории и подкатегории объединяются в одну строку с помощью символа подчеркивания "_", например **BooksAndReference_EReader**.      |  
| pricing           |  Объект  | Объект, содержащий сведения о цене приложения. Дополнительные сведения см. в разделе [Ресурс Pricing](manage-app-submissions.md#pricing-object).       |   
| visibility           |  строка  |  Видимость приложения. Может принимать одно из следующих значений. <ul><li>Hidden (Скрыто)</li><li>Public (Общее)</li><li>Private (Частное)</li><li>NotSet (Не задано)</li></ul>       |   
| targetPublishMode           | строка  | Режим публикации для отправки. Может принимать одно из следующих значений. <ul><li>Immediate (Незамедлительно)</li><li>Manual (Вручную)</li><li>SpecificDate (Указанная дата)</li></ul> |
| targetPublishDate           | строка  | Дата публикации отправки в формате ISO 8601, если для *targetPublishMode* задано значение SpecificDate.  |  
| listings           |   Объект  |  Словарь пар "ключ-значение", где каждый ключ является кодом страны, а каждое значение — объект [ресурса Listing](manage-app-submissions.md#listing-object), содержащий данные описания приложения.       |   
| hardwarePreferences           |  Массив  |   Массив строк, определяющих [предпочтения оборудования](https://docs.microsoft.com/windows/uwp/publish/enter-app-properties) для приложения. Может принимать одно из следующих значений. <ul><li>Сенсорный ввод</li><li>Клавиатура</li><li>Мышь</li><li>Камера</li><li>NfcHce</li><li>Nfc</li><li>BluetoothLE</li><li>Telephony (Телефония)</li></ul>     |   
| automaticBackupEnabled           |  Логический  |   Указывает, может ли Windows включать данные этого приложения в автоматические резервные копии, записываемые в OneDrive. Подробные сведения см. в разделе [Объявления приложений](https://docs.microsoft.com/windows/uwp/publish/app-declarations).   |   
| canInstallOnRemovableMedia           |  Логический  |   Указывает, могут ли клиенты устанавливать приложение на съемный носитель. Подробные сведения см. в разделе [Объявления приложений](https://docs.microsoft.com/windows/uwp/publish/app-declarations).     |   
| isGameDvrEnabled           |  Логический |   Указывает, включена ли для приложения функция DVR для игр.    |   
| gamingOptions           |  Объект |   Массив, содержащий один [ресурс параметров игры](manage-app-submissions.md#gaming-options-object), который определяет относящиеся к игре параметры для приложения.     |   
| hasExternalInAppProducts           |     Логический          |   Указывает, позволяет ли приложение пользователям делать покупки без использования коммерческой системы Microsoft Store. Подробные сведения см. в разделе [Объявления приложений](https://docs.microsoft.com/windows/uwp/publish/app-declarations).     |   
| meetAccessibilityGuidelines           |    Логический           |  Указывает, проверено ли приложение на соответствие рекомендациям по специальным возможностям. Подробные сведения см. в разделе [Объявления приложений](https://docs.microsoft.com/windows/uwp/publish/app-declarations).      |   
| notesForCertification           |  строка  |   Содержит [заметки по сертификации](https://docs.microsoft.com/windows/uwp/publish/notes-for-certification) приложения.    |    
| applicationPackages           |   Массив  | Содержит объекты, предоставляющие сведения о каждом пакете в отправке. Дополнительные сведения см. в разделе [Пакет приложения](manage-app-submissions.md#application-package-object). При вызове этого метода для обновления отправки приложения в теле запроса должны присутствовать только значения *fileName*, *fileStatus*, *minimumDirectXVersion* и *minimumSystemRam* этих объектов. Другие значения заполняются с помощью центра партнеров.   |    
| packageDeliveryOptions    | Объект  | Содержит параметры постепенного выпуска пакета и обязательного обновления для отправки. Подробнее см. в разделе [Объект параметров доставки пакета](manage-app-submissions.md#package-delivery-options-object).  |
| enterpriseLicensing           |  строка  |  Одно из значений, связанных с [корпоративным лицензированием](manage-app-submissions.md#enterprise-licensing), указывающих на поведение приложения в отношении корпоративного лицензирования.  |    
| allowMicrosftDecideAppAvailabilityToFutureDeviceFamilies           |  Логический   |  Указывает, разрешено ли Майкрософт [делать приложение доступным для будущих семейств устройств Windows 10](https://docs.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability).    |    
| allowTargetFutureDeviceFamilies           | Логический   |  Указывает, может ли приложение [быть предназначено для будущих семейств устройств Windows 10](https://docs.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability).     |   
| trailers           |  Массив |   Массив, содержащий до [ресурсов трейлеров](manage-app-submissions.md#trailer-object), представляющие видеотрейлеры для описания приложения.   |   


### <a name="request-example"></a>Пример запроса

В следующем примере показано обновление отправки приложения.

```json
PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/submissions/1152921504621230023 HTTP/1.1
Authorization: Bearer <your access token>
Content-Type: application/json
{
  "applicationCategory": "BooksAndReference_EReader",
  "pricing": {
    "trialPeriod": "FifteenDays",
    "marketSpecificPricings": {},
    "sales": [],
    "priceId": "Tier2"
  },
  "visibility": "Public",
  "targetPublishMode": "Manual",
  "targetPublishDate": "1601-01-01T00:00:00Z",
  "listings": {
    "en-us": {
      "baseListing": {
        "copyrightAndTrademarkInfo": "",
        "keywords": [
              "epub"
            ],
        "licenseTerms": "",
        "privacyPolicy": "",
        "supportContact": "",
        "websiteUrl": "",
        "description": "Description",
        "features": [
              "Free ebook reader"
            ],
        "releaseNotes": "",
        "images": [
          {
            "fileName": "contoso.png",
            "fileStatus": "Uploaded",
            "id": "1152921504672272757",
            "imageType": "Screenshot"
          }
        ],
        "recommendedHardware": [],
        "title": "Contoso ebook reader"
      },
      "platformOverrides": {
        "Windows81": {
          "description": "Ebook reader for Windows 8.1"
        }
      }
    }
  },
  "hardwarePreferences": [
    "Touch"
  ],
  "automaticBackupEnabled": false,
  "canInstallOnRemovableMedia": true,
  "isGameDvrEnabled": false,
  "gamingOptions": [],
  "hasExternalInAppProducts": false,
  "meetAccessibilityGuidelines": true,
  "notesForCertification": "",
  "applicationPackages": [
    {
      "fileName": "contoso_app.appx",
      "fileStatus": "PendingUpload",
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None"
    }
  ],
  "packageDeliveryOptions": {
    "packageRollout": {
        "isPackageRollout": false,
        "packageRolloutPercentage": 0.0,
        "packageRolloutStatus": "PackageRolloutNotStarted",
        "fallbackSubmissionId": "0"
    },
    "isMandatoryUpdate": false,
    "mandatoryUpdateEffectiveDate": "1601-01-01T00:00:00.0000000Z"
  },
  "enterpriseLicensing": "Online",
  "allowMicrosoftDecideAppAvailabilityToFutureDeviceFamilies": true,
  "allowTargetFutureDeviceFamilies": {
    "Desktop": false,
    "Mobile": true,
    "Holographic": true,
    "Xbox": false,
    "Team": true
  },
  "trailers": []
}
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. В теле ответа содержатся сведения об обновленной отправке. Дополнительные сведения о значениях в тексте ответа см. в разделе [Ресурс отправки приложения](manage-app-submissions.md#app-submission-object).

```json
{
  "id": "1152921504621243540",
  "applicationCategory": "BooksAndReference_EReader",
  "pricing": {
    "trialPeriod": "FifteenDays",
    "marketSpecificPricings": {},
    "sales": [],
    "priceId": "Tier2"
  },
  "visibility": "Public",
  "targetPublishMode": "Manual",
  "targetPublishDate": "1601-01-01T00:00:00Z",
  "listings": {
    "en-us": {
      "baseListing": {
        "copyrightAndTrademarkInfo": "",
        "keywords": [
           "epub"
        ],
        "licenseTerms": "",
        "privacyPolicy": "",
        "supportContact": "",
        "websiteUrl": "",
        "description": "Description",
        "features": [
          "Free ebook reader"
        ],
        "releaseNotes": "",
        "images": [
          {
            "fileName": "contoso.png",
            "fileStatus": "Uploaded",
            "id": "1152921504672272757",
            "imageType": "Screenshot"
          }
        ],
        "recommendedHardware": [],
        "title": "Contoso ebook reader"
      },
      "platformOverrides": {
        "Windows81": {
          "description": "Ebook reader for Windows 8.1",
        }
      }
    }
  },
  "hardwarePreferences": [
    "Touch"
  ],
  "automaticBackupEnabled": false,
  "canInstallOnRemovableMedia": true,
  "isGameDvrEnabled": false,
  "gamingOptions": [],
  "hasExternalInAppProducts": false,
  "meetAccessibilityGuidelines": true,
  "notesForCertification": "",
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [],
    "warnings": [],
    "certificationReports": []
  },
  "fileUploadUrl": "https://productingestionbin1.blob.core.windows.net/ingestion/387a9ea8-a412-43a9-8fb3-a38d03eb483d?sv=2014-02-14&sr=b&sig=sdd12JmoaT6BhvC%2BZUrwRweA%2Fkvj%2BEBCY09C2SZZowg%3D&se=2016-06-17T18:32:26Z&sp=rwl",
  "applicationPackages": [
    {
      "fileName": "contoso_app.appx",
      "fileStatus": "PendingUpload",
      "id": "1152921504620138797",
      "version": "1.0.0.0",
      "architecture": "ARM",
      "languages": [
        "en-US"
      ],
      "capabilities": [
        "ID_RESOLUTION_HD720P",
        "ID_RESOLUTION_WVGA",
        "ID_RESOLUTION_WXGA"
      ],
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None",
      "targetDeviceFamilies": [
        "Windows.Mobile min version 10.0.10240.0"
      ]
    }
  ],
  "packageDeliveryOptions": {
    "packageRollout": {
        "isPackageRollout": false,
        "packageRolloutPercentage": 0.0,
        "packageRolloutStatus": "PackageRolloutNotStarted",
        "fallbackSubmissionId": "0"
    },
    "isMandatoryUpdate": false,
    "mandatoryUpdateEffectiveDate": "1601-01-01T00:00:00.0000000Z"
  },
  "enterpriseLicensing": "Online",
  "allowMicrosoftDecideAppAvailabilityToFutureDeviceFamilies": true,
  "allowTargetFutureDeviceFamilies": {
    "Desktop": false,
    "Mobile": true,
    "Holographic": true,
    "Xbox": false,
    "Team": true
  },
  "friendlyName": "Submission 2",
  "trailers": []
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 400  | Не удалось обновить отправку. Недопустимый запрос. |
| 409  | Не удалось обновить отправки из-за текущего состояния приложения или приложение использует функцию центра партнеров, которая [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>См. также

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Получить Отправка приложения](get-an-app-submission.md)
* [Создание, отправка приложения](create-an-app-submission.md)
* [Зафиксировать Отправка приложения](commit-an-app-submission.md)
* [Удалить Отправка приложения](delete-an-app-submission.md)
* [Получить состояние отправки приложения](get-status-for-an-app-submission.md)
