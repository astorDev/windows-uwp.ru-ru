---
author: Xansky
ms.assetid: D34447FF-21D2-44D0-92B0-B3FF9B32D6F7
description: Используйте этот метод в API отправки Microsoft Store для создания новой отправки для приложения, которое зарегистрировано в вашей учетной записи Центра разработки для Windows.
title: Создание отправки приложения
ms.author: mhopkins
ms.date: 07/10/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, API отправки Microsoft Store, создание отправки приложения
ms.localizationpriority: medium
ms.openlocfilehash: 02aa06359f4f15d8763d75d0ab5381ce890ede4a
ms.sourcegitcommit: c4d3115348c8b54fcc92aae8e18fdabc3deb301d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2018
ms.locfileid: "5409574"
---
# <a name="create-an-app-submission"></a>Создание отправки приложения

Используйте этот метод в API отправки Microsoft Store для создания новой отправки для приложения, которое зарегистрировано в вашей учетной записи Центра разработки для Windows. После успешного создания новой отправки с помощью этого метода [обновите отправку](update-an-app-submission.md), чтобы внести любые необходимые изменения в данные отправки, а затем [зафиксируйте отправку](commit-an-app-submission.md) для внедрения и публикации.

Дополнительные сведения об использовании этого метода в процессе создания отправки приложения с помощью API отправки Microsoft Store см. в разделе [Управление отправками приложений](manage-app-submissions.md).


## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Убедитесь, что приложение уже содержит не менее одной отправки, для которой указаны сведения [возрастной категории](https://msdn.microsoft.com/windows/uwp/publish/age-ratings).

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| POST    | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | string | Обязательный. Код продукта в Магазине для приложения, для которого необходимо создать отправку. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

В следующем примере кода показано, как создать новую отправку для приложения.

```
POST https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/submissions HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON, обеспечивающий успешный вызов этого метода. В тексте ответа содержатся сведения о новой отправке. Дополнительные сведения о значениях в тексте ответа см. в разделе [Ресурс отправки приложения](manage-app-submissions.md#app-submission-object).

```json
{
  "id": "1152921504621243540",
  "applicationCategory": "BooksAndReference_EReader",
  "pricing": {
    "trialPeriod": "FifteenDays",
    "marketSpecificPricings": {},
    "sales": [],
    "priceId": "Tier2",
    "isAdvancedPricingModel": true
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
      "fileStatus": "Uploaded",
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
| 400  | Не удалось создать отправку. Недопустимый запрос. |
| 409  | Не удалось создать отправку из-за текущего состояния приложения или в связи с тем, что приложение использует компонент панели мониторинга Центра разработки, [который в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Получение отправки приложения](get-an-app-submission.md)
* [Подтверждение отправки приложения](commit-an-app-submission.md)
* [Обновление отправки приложения](update-an-app-submission.md)
* [Удаление отправки приложения](delete-an-app-submission.md)
* [Получение состояния отправки приложения](get-status-for-an-app-submission.md)
