---
author: mcleanbyron
ms.assetid: C7428551-4B31-4259-93CD-EE229007C4B8
description: "Используйте эти методы в API отправки Магазина Windows для управления отправками для приложений, которые зарегистрированы в вашей учетной записи Центра разработки для Windows."
title: "Управление отправками приложений с помощью API отправки Магазина Windows"
translationtype: Human Translation
ms.sourcegitcommit: 9b76a11adfab838b21713cb384cdf31eada3286e
ms.openlocfilehash: 49d60048a0dd5dae3e80abb9fd4e21b8cf7b417e

---

# Управление отправками приложений с помощью API отправки Магазина Windows


Используйте приведенные ниже методы в API отправки Магазина Windows для управления отправками для приложений, которые зарегистрированы в вашей учетной записи Центра разработки для Windows. Введение в API отправки Магазина Windows, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md).

>**Примечание.**&nbsp;&nbsp;Эти методы могут применяться только для учетных записей Центра разработки для Windows, у которых имеется разрешение на использование API отправки Магазина Windows. Такое разрешение имеется не у всех учетных записей.

>**Важно.**&nbsp;&nbsp;В ближайшее время корпорация Майкрософт изменит модель данных ценообразования для отправки приложений в Центре разработки для Windows. После реализации этого изменения ресурс **Pricing** больше не будет поддерживаться, и вы временно не сможете получать и изменять данные пробного периода, ценообразования и продаж для отправок приложений с помощью API отправки Магазина Windows. Мы будем обновлять API в будущем для создания нового способа программного доступа к информации о ценообразовании для отправок приложений. Дополнительные сведения см. в разделе [Ресурс Pricing](#pricing-object).


| Метод        | URI    | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| GET | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}``` | Получение данных для существующей отправки приложения. Дополнительные сведения см. в [этой статье](get-an-app-submission.md). |
| GET | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/status``` | Получение состояния существующей отправки приложения. Дополнительные сведения см. в [этой статье](get-status-for-an-app-submission.md). |
| POST | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions``` | Создание новой отправки для приложения, которое зарегистрировано в вашей учетной записи Центра разработки для Windows. Дополнительные сведения см. в [этой статье](create-an-app-submission.md). |
| POST | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/commit``` | Передача новой или обновленной отправки приложения в Центр разработки для Windows. Дополнительные сведения см. в [этой статье](commit-an-app-submission.md). |
| PUT | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}``` | Обновление существующей отправки приложения. Дополнительные сведения см. в [этой статье](update-an-app-submission.md). |
| DELETE | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}``` | Удаление отправки приложения. Дополнительные сведения см. в [этой статье](delete-an-app-submission.md). |
| GET | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/packagerollout``` | Получение сведений постепенного выпуска для отправки приложения. Дополнительные сведения см. в [этой статье](get-package-rollout-info-for-an-app-submission.md). |
| POST | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/updatepackagerolloutpercentage``` | Обновление процента постепенного выпуска для отправки приложения. Дополнительные сведения см. в [этой статье](update-the-package-rollout-percentage-for-an-app-submission.md). |
| POST | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/haltpackagerollout``` | Остановка постепенного выпуска для отправки приложения. Дополнительные сведения см. в [этой статье](halt-the-package-rollout-for-an-app-submission.md). |
| POST | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/finalizepackagerollout``` | Завершение постепенного выпуска для отправки приложения. Дополнительные сведения см. в [этой статье](finalize-the-package-rollout-for-an-app-submission.md). |

<span id="create-an-app-submission">
## Создание отправки приложения

Чтобы создать отправку для приложения, выполните следующие действия.

1. Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.

  >**Примечание.**&nbsp;&nbsp;Убедитесь, что приложение уже содержит не менее одной завершенной отправки, для которой указаны сведения [возрастной категории](https://msdn.microsoft.com/windows/uwp/publish/age-ratings).

2. [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token). Этот маркер доступа необходимо передавать методам из API отправки Магазина Windows. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

3. [Создание отправки приложения](create-an-app-submission.md) путем выполнения следующего метода в API отправки Магазина Windows. Этот метод создает новую выполняющуюся отправку, которая является копией последней опубликованной отправки.

  ```
  POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions
  ```

  Тело ответа содержит три элемента: идентификатор новой отправки, данные для новой отправки (в том числе все описания и информацию о ценах), а также URI подписанного URL-адреса (Shared Access Signature, SAS) для передачи любых пакетов приложений и изображений описания для отправки. Дополнительные сведения о SAS см. в разделе [Подписанные URL-адреса, часть 1. Общие сведения о модели SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

4. При добавлении новых пакетов или изображений для отправки [подготовьте пакеты приложения](https://msdn.microsoft.com/windows/uwp/publish/app-package-requirements) и [подготовьте снимки экранов и изображения](https://msdn.microsoft.com/windows/uwp/publish/app-screenshots-and-images). Добавьте все эти файлы в ZIP-архив.

5. Проверьте данные отправки, внеся все необходимые для новой отправки изменения, затем выполните следующий метод для [обновления отправки приложения](update-an-app-submission.md).

  ```
  PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}
  ```

  >**Примечание.**&nbsp;&nbsp;Если в отправку добавляются новые пакеты или изображения, обязательно обновите данные отправки, чтобы они ссылались на имя и относительный путь этих файлов в ZIP-архиве.

4. При добавлении новых пакетов или изображений для отправки передайте ZIP-архив по URI SAS, полученному в теле ответа метода POST, который вызывался на шаге 2. Дополнительные сведения см. в разделе [Подписанные URL-адреса, часть 2. Создание и использование SAS с хранилищем BLOB-объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/).

  В следующем фрагменте кода показано, как передать архив с помощью класса [CloudBlockBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx) клиентской библиотеки службы хранилища Azure для .NET.

  ```csharp
  string sasUrl = "https://productingestionbin1.blob.core.windows.net/ingestion/26920f66-b592-4439-9a9d-fb0f014902ec?sv=2014-02-14&sr=b&sig=usAN0kNFNnYE2tGQBI%2BARQWejX1Guiz7hdFtRhyK%2Bog%3D&se=2016-06-17T20:45:51Z&sp=rwl";

  Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob blockBob =
      new Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob(new System.Uri(sasUrl));
  await blockBob.UploadFromStreamAsync(stream);
  ```

5. [Подтвердите отправку приложения](commit-an-app-submission.md), выполнив следующий метод. Таким образом Центр разработки оповещается, что отправка готова и обновления требуется применить к вашей учетной записи.

  ```
  POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/commit
  ```

6. Проверка состояния подтверждения путем выполнения следующего метода для [получения сведений о состоянии отправки приложения](get-status-for-an-app-submission.md).

    ```
    GET https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/status
    ```

    Чтобы проверить состояние отправки, посмотрите значение поля *status* в теле ответа. Это значение должно измениться с **CommitStarted** на **PreProcessing**, если запрос выполнен успешно, или на **CommitFailed**, если в запросе возникли ошибки. Если имеются ошибки, поле *statusDetails* содержит дополнительные сведения об ошибке.

7. После успешного завершения подтверждения отправки она отправляется в Магазин для внедрения. Вы можете продолжать отслеживать ход выполнения отправки с помощью предыдущего метода или на информационной панели Центра разработки.

<span id="manage-gradual-package-rollout">
## Управление постепенным выпуском пакета для отправки приложения

Вы можете постепенно выполнять выпуск обновленных пакетов в отправке приложения для части пользователей вашего приложения в Windows 10. Это позволяет контролировать отзывы и аналитические данные для конкретных пакетов, чтобы вы могли убедиться в правильности обновления до его более широкого распространения. Можно изменить процент выпуска (или остановить обновление) для опубликованной отправки без необходимости создания новой отправки. Дополнительные сведения, включая инструкции по включению и управлению постепенным выпуском пакета на информационной панели Центра разработки, см. в [этой статье](../publish/gradual-package-rollout.md).

Можно также программным образом включить и осуществлять управление постепенным выпуском пакета для отправки приложения с помощью следующих методов в API отправки Магазина Windows.

* Включение постепенного выпуска пакета для отправки приложения.

  1. [Создайте отправку приложения](create-an-app-submission.md) или [получите отправку приложения](get-an-app-submission.md).
  2. В данных ответа найдите ресурс [packageRollout](#package-rollout-object), задайте в поле *isPackageRollout* значение true, а в поле *packageRolloutPercentage* задайте процент пользователей вашего приложения, которые должны получить обновленные пакеты.
  3. Передайте обновленные данные отправки приложения методу [обновления отправки приложения](update-an-app-submission.md).

<span/>

* Для [получения сведений выпуска пакета для отправки приложения](get-package-rollout-info-for-an-app-submission.md) выполните следующий метод.

  ```
  GET https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/packagerollout
  ```

<span/>

* Для [изменения процента выпуска пакета для отправки приложения](update-the-package-rollout-percentage-for-an-app-submission.md) выполните следующий метод.

  ```
  POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/updatepackagerolloutpercentage  
  ```

<span/>

* Для [остановки выпуска пакета для отправки приложения](halt-the-package-rollout-for-an-app-submission.md) выполните следующий метод.

  ```
  POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/haltpackagerollout   
  ```  

<span/>

* Для [завершения выпуска пакета для отправки приложения](finalize-the-package-rollout-for-an-app-submission.md) выполните следующий метод.

  ```
  POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/finalizepackagerollout
  ```

## Ресурсы

В этих методах для форматирования данных используются указанные ниже ресурсы.

<span id="app-submission-object" />
### Отправка приложения

Данный ресурс представляет отправку для приложения. В следующем примере показан формат этого ресурса.

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
        "keywords": [],
        "licenseTerms": "",
        "privacyPolicy": "",
        "supportContact": "",
        "websiteUrl": "",
        "description": "Description",
        "features": [],
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
        "title": "ApiTestApp For Devbox"
      },
      "platformOverrides": {}
    }
  },
  "hardwarePreferences": [
    "Touch"
  ],
  "automaticBackupEnabled": false,
  "canInstallOnRemovableMedia": true,
  "isGameDvrEnabled": false,
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
        "packageRolloutPercentage": 0,
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
  "friendlyName": "Submission 2"
}
```

Этот ресурс содержит следующие значения.

| Значение      | Тип   | Описание      |
|------------|--------|-------------------|
| id            | string  | Идентификатор отправки.  |
| applicationCategory           | string  |   Строка, указывающая [категорию или подкатегорию](https://msdn.microsoft.com/windows/uwp/publish/category-and-subcategory-table) для вашего приложения. Категории и подкатегории объединяются в одну строку с помощью символа подчеркивания "_", например **BooksAndReference_EReader**.      |  
| pricing           |  объект  | Объект, содержащий сведения о цене приложения. Дополнительные сведения см. в разделе [Ресурс цены](#pricing-object) ниже.       |   
| visibility           |  string  |  Видимость приложения. Может принимать одно из следующих значений. <ul><li>Hidden (Скрыто)</li><li>Public (Общее)</li><li>Private (Частное)</li><li>NotSet (Не задано)</li></ul>       |   
| targetPublishMode           | строка  | Режим публикации для отправки. Может принимать одно из следующих значений. <ul><li>Immediate (Незамедлительно)</li><li>Manual (Вручную)</li><li>SpecificDate (Указанная дата)</li></ul> |
| targetPublishDate           | строка  | Дата публикации отправки в формате ISO 8601, если для *targetPublishMode* задано значение SpecificDate.  |  
| listings           |   объект  |  Словарь пар "ключ-значение", где каждый ключ является кодом страны, а каждое значение — объектом [ресурса описания](#listing-object), содержащим данные описания приложения.       |   
| hardwarePreferences           |  массив  |   Массив строк, определяющих [предпочтения оборудования](https://msdn.microsoft.com/windows/uwp/publish/enter-app-properties#hardware_preferences) для приложения. Может принимать одно из следующих значений. <ul><li>Touch (Сенсорное устройство)</li><li>Keyboard (Клавиатура)</li><li>Mouse (Мышь)</li><li>Camera (Камера)</li><li>NfcHce</li><li>Nfc</li><li>BluetoothLE</li><li>Telephony (Телефония)</li></ul>     |   
| automaticBackupEnabled           |  логический  |   Указывает, может ли Windows включать данные этого приложения в автоматические резервные копии, записываемые в OneDrive. Подробные сведения см. в разделе [Объявления приложений](https://msdn.microsoft.com/windows/uwp/publish/app-declarations).   |   
| canInstallOnRemovableMedia           |  логический  |   Указывает, могут ли клиенты устанавливать приложение на съемный носитель. Подробные сведения см. в разделе [Объявления приложений](https://msdn.microsoft.com/windows/uwp/publish/app-declarations).     |   
| isGameDvrEnabled           |  логический |   Указывает, включена ли для приложения функция DVR для игр.    |   
| hasExternalInAppProducts           |     логический          |   Указывает, позволяет ли приложение пользователям делать покупки без использования коммерческой системы Магазина Windows. Подробные сведения см. в разделе [Объявления приложений](https://msdn.microsoft.com/windows/uwp/publish/app-declarations).     |   
| meetAccessibilityGuidelines           |    логический           |  Указывает, проверено ли приложение на соответствие рекомендациям по специальным возможностям. Подробные сведения см. в разделе [Объявления приложений](https://msdn.microsoft.com/windows/uwp/publish/app-declarations).      |   
| notesForCertification           |  string  |   Содержит [заметки по сертификации](https://msdn.microsoft.com/windows/uwp/publish/notes-for-certification) приложения.    |    
| status           |   string  |  Состояние отправки. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>Canceled (Отменено)</li><li>PendingCommit (Ожидание фиксации)</li><li>CommitStarted (Фиксация запущена)</li><li>CommitFailed (Сбой фиксации)</li><li>PendingPublication (Ожидание публикации)</li><li>Publishing (Выполняется публикация)</li><li>Published (Опубликовано)</li><li>PublishFailed (Сбой публикации)</li><li>PreProcessing (Предварительная обработка)</li><li>PreProcessingFailed (Сбой предварительной обработки)</li><li>Certification (Сертификация)</li><li>CertificationFailed (Сбой сертификации)</li><li>Release (Выпуск)</li><li>ReleaseFailed (Сбой выпуска)</li></ul>      |    
| statusDetails           |   object  |  Содержит дополнительные сведения о состоянии отправки, включая сведения об ошибках. Дополнительные сведения см. далее в разделе [Сведения о состоянии](#status-details-object).       |    
| fileUploadUrl           |   string  | URI подписанного URL-адреса (SAS) для передачи пакетов для отправки. При добавлении новых пакетов или изображений для отправки выложите ZIP-архив, содержащий пакеты и изображения, по этому URI. Дополнительную информацию см. в разделе [Создание отправки приложения](#create-an-app-submission).       |    
| applicationPackages           |   array  | Содержит объекты, предоставляющие сведения о каждом пакете в отправке. Дополнительные сведения см. в разделе [Пакет приложения](#application-package-object) ниже. |    
| packageDeliveryOptions    | object  | Содержит параметры постепенного выпуска пакета и обязательного обновления для отправки. Дополнительные сведения см. в разделе [Объект параметров доставки пакета](#package-delivery-options-object) ниже.  |
| enterpriseLicensing           |  string  |  Одно из значений, связанных с [корпоративным лицензированием](#enterprise-licensing), указывающих на поведение приложения в отношении корпоративного лицензирования.  |    
| allowMicrosftDecideAppAvailabilityToFutureDeviceFamilies           |  логический   |  Указывает, разрешено ли Microsoft [делать приложение доступным для будущих семейств устройств Windows 10](https://msdn.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability#windows-10-device-families).    |    
| allowTargetFutureDeviceFamilies           | object   |  Словарь пар "ключ-значение", в котором каждый ключ представляет собой [семейство устройств Windows 10](https://msdn.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability#windows-10-device-families), а каждое значение является логическим значением, которое указывает, разрешено ли нацеливание приложения на указанное семейство устройств.     |    
| friendlyName           |   string  |  Понятное имя приложения, используемое для отображения.       |  


<span id="listing-object" />
### Описание

Этот ресурс содержит информацию описания для приложения. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|------|
|  baseListing               |   object      |  Информация [базового описания](#base-listing-object) для приложения, которая определяет описание по умолчанию на всех платформах.   |     
|  platformOverrides               | object |   Словарь пар "ключ-значение", где каждый ключ является строкой, идентифицирующей платформу, для которой необходимо переопределить информацию в описании, а каждое значение является объектом [базового описания](#base-listing-object) (содержащим только значения от описания до заголовка), определяющим информацию для переопределения описания для указанной платформы. Ключи могут иметь следующие значения: <ul><li>Unknown (Неизвестно)</li><li>Windows80</li><li>Windows81</li><li>WindowsPhone71</li><li>WindowsPhone80</li><li>WindowsPhone81</li></ul>     |      |     

<span id="base-listing-object" />
### Базовое описание

Этот ресурс содержит базовую информацию описания для приложения. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание       |
|-----------------|---------|------|
|  copyrightAndTrademarkInfo                |   string      |  Необязательные [сведения об авторских правах и (или) товарных знаках](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#copyright-and-trademark-info).  |
|  keywords                |  array       |  Массив [ключевых слов](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#keywords), способствующих появлению вашего приложения в результатах поиска.    |
|  licenseTerms                |    string     | Необязательные [условия лицензионного соглашения](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#additional-license-terms) для вашего приложения.     |
|  privacyPolicy                |   string      |   URL-адрес [политики конфиденциальности](https://msdn.microsoft.com/windows/uwp/publish/privacy-policy) для вашего приложения.    |
|  supportContact                |   string      |  URL-адрес или адрес электронной почты для [контактных данных о поддержке](https://msdn.microsoft.com/windows/uwp/publish/support-contact-info) вашего приложения.     |
|  websiteUrl                |   string      |  URL-адрес [веб-страницы](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#website) вашего приложения.    |    
|  description               |    string     |   [Описание](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#description) для описания приложения.   |     
|  features               |    array     |  Массив размером до 20 строк со списком [функций](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#app-features) вашего приложения.     |
|  releaseNotes               |  string       |  [Заметки о выпуске](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#release-notes) для вашего приложения.    |
|  images               |   array      |  Массив данных [изображений и значков](#image-object) для описания приложения.  |
|  recommendedHardware               |   array      |  Массив размером до 11 строк со списком [рекомендуемой конфигурации оборудования](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#recommended-hardware) для вашего приложения.     |
|  title               |     string    |   Название для описания приложения.   |  


<span id="image-object" />
### Изображение

Этот ресурс содержит данные изображений и значков для описания приложения. Дополнительные сведения об изображениях и значках для описания см. в разделе [Снимки экранов приложения и изображения](https://msdn.microsoft.com/windows/uwp/publish/app-screenshots-and-images). Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание           |
|-----------------|---------|------|
|  fileName               |    string     |   Имя файла изображения в ZIP-архиве, который был передан для отправки.    |     
|  fileStatus               |   string      |  Состояние файла изображения. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>PendingUpload (Ожидает передачи)</li><li>Uploaded (Передан)</li><li>PendingDelete (Ожидает удаления)</li></ul>   |
|  id  |  string  | Идентификатор изображения, указанный Центром разработки.  |
|  description  |  string  | Описание изображения.  |
|  imageType  |  string  | Одна из следующих строк, указывающих тип изображения: <ul><li>Unknown (Неизвестно)</li><li>Screenshot (Снимок экрана)</li><li>PromotionalArtwork414X180 (Рекламное изображение 414X180)</li><li>PromotionalArtwork846X468 (Рекламное изображение 846X468)</li><li>PromotionalArtwork558X756 (Рекламное изображение 558X756)</li><li>PromotionalArtwork414X468 (Рекламное изображение 414X468)</li><li>PromotionalArtwork558X558 (Рекламное изображение 558X558)</li><li>PromotionalArtwork2400X1200 (Рекламное изображение 2400X1200)</li><li>Icon (Значок)</li><li>WideIcon358X173 (Широкий значок 358X173)</li><li>BackgroundImage1000X800 (Фоновое изображение 1000X800)</li><li>SquareIcon358X358 (Фоновое изображение 358X358)</li><li>MobileScreenshot (Снимок экрана мобильного устройства)</li><li>XboxScreenshot (Снимок экрана Xbox)</li><li>SurfaceHubScreenshot (Снимок экрана SurfaceHub)</li><li>HoloLensScreenshot (Снимок экрана HoloLens)</li></ul>      |


<span id="pricing-object" />
### Pricing

Этот ресурс содержит сведения о ценах для приложения.

>**Важно.**&nbsp;&nbsp;В ближайшее время корпорация Майкрософт изменит модель данных ценообразования для отправки приложений в Центре разработки для Windows. После реализации этого изменения ресурс **Pricing** больше не будет поддерживаться, и вы временно не сможете получать и изменять данные пробного периода, ценообразования и продаж для отправок приложений с помощью API отправки Магазина Windows. Вы заметите следующие изменения в поведении:

   > * После вызова [метода GET для получения отправки приложения](get-an-app-submission.md) ресурс **Pricing** будет пустым. Вы можете продолжать использовать информационную панель Центра разработки для получения данных ценообразования для отправки приложения.
   > * При вызове [метода PUT для обновления отправки приложения](update-an-app-submission.md) сведения в ресурсе **Pricing** будут игнорироваться. Вы можете продолжать использовать информационную панель Центра разработки для изменения данных ценообразования для отправки приложения.

> В дальнейшем мы будем обновлять API отправки Магазина Windows для внедрения нового программного способа получения и обновления информации о ценообразовании для отправки приложения.

Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|------|
|  trialPeriod               |    string     |  Строка, указывающая срок действия пробной версии приложения. Может принимать одно из следующих значений. <ul><li>NoFreeTrial (Нет пробного периода)</li><li>OneDay (Один день)</li><li>TrialNeverExpires (Бессрочный пробный период)</li><li>SevenDays (7 дней)</li><li>FifteenDays (15 дней)</li><li>ThirtyDays (30 дней)</li></ul>    |
|  marketSpecificPricings               |    object     |  Словарь пар "ключ-значение", где каждый ключ представляет собой код страны ISO 3166-1 alpha-2 из двух букв, а каждое значение — [ценовую категорию](#price-tiers). Эти элементы представляют [особые цены на ваше приложение для определенных рынков](https://msdn.microsoft.com/windows/uwp/publish/define-pricing-and-market-selection#markets-and-custom-prices). Любые элементы этого словаря переопределяют базовую цену, заданную значением *priceId* для указанного рынка.      |     
|  sales               |   array      |  Массив объектов, содержащих сведения о продажах для приложения. Дополнительные сведения см. далее в разделе [Продажа](#sale-object).    |     
|  priceId               |   string      |  [Ценовая категория](#price-tiers), указывающая [базовую цену](https://msdn.microsoft.com/windows/uwp/publish/define-pricing-and-market-selection#base-price) для приложения.   |


<span id="sale-object" />
### Продажа

Этот ресурс содержит сведения о продажах для приложения.

>**Важно.**&nbsp;&nbsp;В ближайшее время корпорация Майкрософт изменит модель данных ценообразования для отправок приложений в Центре разработки для Windows. После реализации этого изменения ресурс **Sale** больше не будет поддерживаться, и вы временно не сможете получать и изменять данные по продажам для отправок приложений с помощью API отправки Магазина Windows. Мы будем обновлять API в будущем для создания нового способа программного доступа к информации о продажах для отправок приложений. Дополнительные сведения см. в разделе [Ресурс Pricing](#pricing-object).

Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|------|
|  name               |    string     |   Имя продажи.    |     
|  basePriceId               |   string      |  [Ценовая категория](#price-tiers), используемая для базовой цены продажи.    |     
|  startDate               |   string      |   Дата начала для продажи в формате ISO 8601.  |     
|  endDate               |   string      |  Дата окончания для продажи в формате ISO 8601.      |     
|  marketSpecificPricings               |   object      |   Словарь пар "ключ-значение", где каждый ключ представляет собой код страны ISO 3166-1 alpha-2 из двух букв, а каждое значение — [ценовую категорию](#price-tiers). Эти элементы представляют [особые цены на ваше приложение для определенных рынков](https://msdn.microsoft.com/windows/uwp/publish/define-pricing-and-market-selection#markets-and-custom-prices). Любые элементы этого словаря переопределяют базовую цену, заданную значением *basePriceId* для указанного рынка.    |


<span id="status-details-object" />
### Сведения о состоянии

Этот ресурс содержит дополнительные сведения о состоянии отправки. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|------|
|  errors               |    object     |   Массив объектов, содержащих сведения об ошибках для отправки. Дополнительные сведения см. далее в разделе [Сведения о состоянии](#status-detail-object).   |     
|  warnings               |   object      | Массив объектов, содержащих сведения о предупреждениях для отправки. Дополнительные сведения см. далее в разделе [Сведения о состоянии](#status-detail-object).     |
|  certificationReports               |     object    |   Массив объектов, которые обеспечивают доступ к данным отчета сертификации для отправки. В случае сбоя сертификации можно проверить эти отчеты для получения дополнительной информации. Дополнительные сведения см. далее в разделе [Отчет о сертификации](#certification-report-object).   |  


<span id="status-detail-object" />
### Сведения о состоянии

Этот ресурс содержит дополнительные сведения обо всех связанных ошибках и предупреждениях для отправки. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|------|
|  code               |    string     |   Строка, которая описывает тип ошибки или предупреждения. Дополнительные сведения см. в разделе [Код состояния отправки](#submission-status-code) ниже.   |     
|  details               |     string    |  Сообщение с дополнительными сведениями о проблеме.     |


<span id="application-package-object" />
### Пакет приложения

Этот ресурс содержит сведения о пакете приложения для отправки. В следующем примере показан формат этого ресурса.

```json
{
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
}
```

Этот ресурс содержит следующие значения.  

>**Примечание.**&nbsp;&nbsp;При вызове метода для [обновления отправки приложения](update-an-app-submission.md) в теле запроса требуются только значения *fileName*, *fileStatus*, *minimumDirectXVersion* и *minimumSystemRam* этого объекта. Остальные значения заполняются Центром разработки.

| Значение           | Тип    | Описание                   |
|-----------------|---------|------|
| fileName   |   string      |  Имя пакета.    |  
| fileStatus    | string    |  Состояние пакета. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>PendingUpload (Ожидает передачи)</li><li>Uploaded (Передан)</li><li>PendingDelete (Ожидает удаления)</li></ul>    |  
| id    |  string   |  Идентификатор, который уникально идентифицирует пакет. Это значение используется Центром разработки.   |     
| version    |  string   |  Версия пакета приложения. Дополнительные сведения см. в разделе [Нумерация версий пакета](https://msdn.microsoft.com/windows/uwp/publish/package-version-numbering).   |   
| architecture    |  string   |  Архитектура пакета (например, ARM).   |     
| languages    | array    |  Массив кодов языков, которые поддерживает приложение. Дополнительные сведения см. в разделе [Поддерживаемые языки](https://msdn.microsoft.com/windows/uwp/publish/supported-languages).    |     
| capabilities    |  array   |  Массив возможностей, необходимых для этого пакета. Дополнительные сведения о возможностях см. в разделе [Объявления возможностей приложения](https://msdn.microsoft.com/windows/uwp/packaging/app-capability-declarations).   |     
| minimumDirectXVersion    |  string   |  Минимальная версия DirectX, поддерживаемая пакетом приложения. Может задаваться только для приложений, предназначенных для Windows 8.x; игнорируется для приложений, предназначенных для других версий. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>DirectX93</li><li>DirectX100</li></ul>   |     
| minimumSystemRam    | string    |  Минимальный объем ОЗУ, необходимый для пакета приложения. Может задаваться только для приложений, предназначенных для Windows 8.x; игнорируется для приложений, предназначенных для других версий. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>Memory2GB</li></ul>   |       
| targetDeviceFamilies    | array    |  Массив строк, представляющих семейства устройств, для которых предназначен пакет. Это значение используется только для пакетов, предназначенных для Windows 10; для пакетов, предназначенных для предыдущих выпусков, это значение равно **None**. В настоящее время поддерживаются следующие строки семейств устройств для пакетов Windows 10, где *{0}* представляет собой строку версии Windows 10, например 10.0.10240.0, 10.0.10586.0 или 10.0.14393.0: <ul><li>Windows.Universal min version *{0}*</li><li>Windows.Desktop min version *{0}*</li><li>Windows.Mobile min version *{0}*</li><li>Windows.Xbox min version *{0}*</li><li>Windows.Holographic min version *{0}*</li></ul>   |    

<span/>

<span id="certification-report-object" />
### Отчет о сертификации

Этот ресурс предоставляет доступ к данным отчета о сертификации для отправки. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|------|
|     date            |    string     |  Дата и время создания отчета (в формате ISO 8601).    |
|     reportUrl            |    string     |  URL-адрес, по которому можно получить доступ к отчету.    |


<span id="package-delivery-options-object" />
### Объект параметров доставки пакета

Этот ресурс содержит постепенный выпуск пакета и обязательные параметры обновления для отправки. В следующем примере показан формат этого ресурса.

```json
{
  "packageDeliveryOptions": {
    "packageRollout": {
        "isPackageRollout": false,
        "packageRolloutPercentage": 0,
        "packageRolloutStatus": "PackageRolloutNotStarted",
        "fallbackSubmissionId": "0"
    },
    "isMandatoryUpdate": false,
    "mandatoryUpdateEffectiveDate": "1601-01-01T00:00:00.0000000Z"
  },
}
```

Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
| packageRollout   |   object      |  Содержит параметры постепенного выпуска пакета для отправки. Дополнительные сведения см. в разделе [Объект выпуска пакета](#package-rollout-object) ниже.    |  
| isMandatoryUpdate    | boolean    |  Указывает, следует ли рассматривать пакеты в этой отправке как обязательные для самоустанавливаемых обновлений приложений. Дополнительные сведения об обязательных пакетах самоустанавливаемых обновлений приложений см. в разделе [Загрузка и установка обновлений пакетов для приложения](../packaging/self-install-package-updates.md).    |  
| mandatoryUpdateEffectiveDate    |  date   |  Дата и время, когда пакеты в этой отправке станут обязательными, в формате ISO 8601 и по часовому поясу UTC.   |        

<span id="package-rollout-object" />
### Объект выпуска пакета

Этот ресурс содержит [параметры постепенного выпуска пакета](#manage-gradual-package-rollout) для отправки. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
| isPackageRollout   |   boolean      |  Указывает, включен ли постепенный выпуск пакета для этой отправки.    |  
| packageRolloutPercentage    | float    |  Процент пользователей, которые будут получать пакеты при постепенном выпуске.    |  
| packageRolloutStatus    |  string   |  Одна из следующих строк, указывающая состояние постепенного выпуска пакета: <ul><li>PackageRolloutNotStarted</li><li>PackageRolloutInProgress</li><li>PackageRolloutComplete</li><li>PackageRolloutStopped</li></ul>  |  
| fallbackSubmissionId    |  string   |  Идентификатор отправки, который будет поступать от пользователей, которые не получают пакеты постепенного выпуска.   |          

<span/>

## Перечисления

Эти методы используют следующие перечисления.


<span id="price-tiers" />
### Ценовые категории

Следующие значения представляют ценовые категории, доступные для отправки приложения.

| Значение           | Описание                                                                                                                                                                                                                          |
|-----------------|------|
|  Base               |   Ценовая категория не задана; используется базовая цена для приложения.      |     
|  NotAvailable              |   Приложение недоступно в указанном регионе.    |     
|  Free              |   Бесплатное приложение.    |    
|  Tier2 – Tier194               |   Tier2 представляет ценовую категорию 0,99 долл. США. Каждая дополнительная категория представляет дополнительное увеличение (1,29; 1,49 1,99 долл. США и т. д.).    |


<span id="enterprise-licensing" />
### Значения корпоративного лицензирования

Следующие значения представляют поведение корпоративного лицензирования для приложения. Дополнительные сведения об этих параметрах см. в разделе [Параметры корпоративного лицензирования](https://msdn.microsoft.com/windows/uwp/publish/organizational-licensing).

| Значение           |  Описание      |
|-----------------|---------------|
| None            |     Приложение недоступно организациям путем корпоративного лицензирования от Магазина (в режиме онлайн).         |     
| Online        |     Приложение доступно организациям путем корпоративного лицензирования от Магазина (в режиме онлайн).  |
| OnlineAndOffline | Приложение доступно организациям путем корпоративного лицензирования от Магазина (в режиме онлайн); кроме того, приложение доступно организациям путем лицензирования без подключения (в автономном режиме). |


<span id="submission-status-code" />
### Код состояния отправки

Следующие значения представляют код состояния отправки.

| Значение           |  Описание      |
|-----------------|---------------|
| None            |     Код не указан.         |     
| InvalidArchive        |     ZIP-архив, содержащий пакет, является недопустимым или имеет неизвестный формат архива.  |
| MissingFiles | В ZIP-архиве отсутствуют файлы, перечисленные в данных отправки, или они находятся в неправильном месте в архиве. |
| PackageValidationFailed | Один или несколько пакетов в вашей отправке не прошли проверку. |
| InvalidParameterValue | Один из параметров в теле запроса не является допустимым. |
| InvalidOperation | Операция, которую вы пытались выполнить, является недопустимой. |
| InvalidState | Операция, которую вы пытались выполнить, является недопустимой для текущего состояния тестового пакета. |
| ResourceNotFound | Не удалось найти указанный тестовый пакет. |
| ServiceError | Запрос не был успешно выполнен из-за внутренней ошибки службы. Попробуйте повторить запрос. |
| ListingOptOutWarning | Разработчик удалил описание из предыдущей отправки или не включил данные описания, которые поддерживаются пакетом. |
| ListingOptInWarning  | Разработчик добавил описание. |
| UpdateOnlyWarning | Разработчик пытается вставить какой-то элемент, который имеет только поддержку обновления. |
| Other  | Отправка находится в неизвестном состоянии или состоянии, не отнесенном ни к одной из категорий. |
| PackageValidationWarning | В процессе проверки пакета создано предупреждение. |

<span/>

## Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Получение данных приложения с помощью API отправки Магазина Windows](get-app-data.md)
* [Отправки приложений на информационной панели Центра разработки](https://msdn.microsoft.com/windows/uwp/publish/app-submissions)



<!--HONumber=Nov16_HO1-->


