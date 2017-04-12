---
author: mcleanbyron
ms.assetid: C7428551-4B31-4259-93CD-EE229007C4B8
description: "Используйте эти методы в API отправки Магазина Windows, чтобы управлять отправками для приложений, которые зарегистрированы в вашей учетной записи Центра разработки для Windows."
title: "Управление отправками приложений"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API отправки Магазина Windows, отправки приложений"
ms.openlocfilehash: f6f0342619f91190bf021842c3ac3b0c61964d25
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="manage-app-submissions"></a>Управление отправками приложений

API отправки Магазина Windows предоставляет методы, которые можно использовать для управления отправками ваших приложений, включая постепенные выпуски пакетов. Введение в API отправки Магазина Windows, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md).

>**Примечание.**&nbsp;&nbsp;Эти методы могут применяться только для учетных записей Центра разработки для Windows, у которых имеется разрешение на использование API отправки Магазина Windows. Это разрешение предоставляется учетным записям разработчиков поэтапно. В настоящий момент это разрешение предоставлено не всем учетным записям. Чтобы запросить ранний доступ, войдите в панель мониторинга Центра разработки, щелкните **Обратная связь** в нижней части панели, выберите пункт **API отправки** в разделе обратной связи и отправьте запрос. Когда для вашей учетной записи будет предоставлено разрешение, вы получите уведомление по электронной почте.

>**Важно!**&nbsp;&nbsp;При использовании API отправки Магазина Windows для создания отправки приложения обязательно вносите дополнительные изменения в отправку только с помощью API-интерфейса, а не через информационную панель Центра разработки. Если вы используете информационную панель для изменения отправки, изначально созданной с помощью API, вы более не сможете изменять или фиксировать эту отправку с помощью этого API. В некоторых случаях отправка может оставаться в состоянии ошибки, когда продолжить процесс отправки невозможно. В этом случае необходимо удалить отправку и создать новую.

<span id="methods-for-app-submissions" />
## <a name="methods-for-managing-app-submissions"></a>Методы для управления отправками приложений

Используйте следующие методы для получения, создания, обновления, фиксации и удаления отправки приложения. Для того чтобы вы могли использовать эти методы, приложение должно уже существовать в вашей учетной записи Центра разработки, и необходимо сначала создать одну отправку приложения на информационной панели. Дополнительные сведения см. в разделе [Необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites).

<table>
<colgroup>
<col width="10%" />
<col width="30%" />
<col width="60%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Способ</th>
<th align="left">URI</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">GET</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}```</td>
<td align="left">[Получение существующей отправки приложения](get-an-app-submission.md)</td>
</tr>
<tr>
<td align="left">GET</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/status```</td>
<td align="left">[Получение состояния существующей отправки приложения](get-status-for-an-app-submission.md)</td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions```</td>
<td align="left">[Создание новой отправки приложения](create-an-app-submission.md)</td>
</tr>
<tr>
<td align="left">PUT</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}```</td>
<td align="left">[Обновление существующей отправки приложения](update-an-app-submission.md)</td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/commit```</td>
<td align="left">[Фиксация новой или обновленной отправки приложения](commit-an-app-submission.md)</td>
</tr>
<tr>
<td align="left">DELETE</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}```</td>
<td align="left">[Удаление отправки приложения](delete-an-app-submission.md)</td>
</tr>
</tbody>
</table>

<span id="create-an-app-submission">
### Создание отправки приложения

Чтобы создать отправку для приложения, выполните следующие действия.

1. Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.

  >**Примечание.**&nbsp;&nbsp;Убедитесь, что приложение уже содержит не менее одной завершенной отправки, для которой указаны сведения [возрастной категории](https://msdn.microsoft.com/windows/uwp/publish/age-ratings).

2. [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token). Этот маркер доступа необходимо передавать методам из API отправки Магазина Windows. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

3. [Создание отправки приложения](create-an-app-submission.md) путем выполнения следующего метода в API отправки Магазина Windows. Этот метод создает новую выполняющуюся отправку, которая является копией последней опубликованной отправки.

  > [!div class="tabbedCodeSnippets"]
  ``` syntax
  POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions
  ```

  Тело ответа содержит три элемента: идентификатор новой отправки, данные для новой отправки (в том числе все описания и информацию о ценах), а также URI подписанного URL-адреса (Shared Access Signature, SAS) для передачи любых пакетов приложений и изображений описания для отправки в хранилище BLOB-объектов Azure.

  >**Примечание.**&nbsp;&nbsp;URI SAS предоставляет доступ к защищенному ресурсу в хранилище Azure без необходимости в использовании ключей учетной записи. Основные сведения об URI SAS и их использовании с хранилищем BLOB-объектов Azure см. в разделах [Подписанные URL-адреса, часть 1. Общие сведения о модели SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1) и [Подписанные URL-адреса, часть 2. Создание и использование SAS с хранилищем BLOB-объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/).

4. При добавлении новых пакетов или изображений для отправки [подготовьте пакеты приложения](https://msdn.microsoft.com/windows/uwp/publish/app-package-requirements) и [подготовьте снимки экранов и изображения](https://msdn.microsoft.com/windows/uwp/publish/app-screenshots-and-images). Добавьте все эти файлы в ZIP-архив.

5. Проверьте данные отправки, внеся все необходимые для новой отправки изменения, затем выполните следующий метод для [обновления отправки приложения](update-an-app-submission.md).

  > [!div class="tabbedCodeSnippets"]
  ``` syntax
  PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}
  ```

  <span/>
  >**Примечание.**&nbsp;&nbsp;Если в отправку добавляются новые пакеты или изображения, обязательно обновите данные отправки, чтобы они ссылались на имя и относительный путь этих файлов в ZIP-архиве.

4. При добавлении новых пакетов или изображений для отправки выложите ZIP-архив в [хранилище BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage), используя URI SAS, полученный в теле ответа метода POST, вызов которого был выполнен ранее. Существуют разные библиотеки Azure, которые можно использовать в этих целях на различных платформах, включая следующие.

  * [Клиентская библиотека службы хранилища Azure для .NET](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs)
  * [Пакет SDK службы хранилища Azure для Java](https://docs.microsoft.com/azure/storage/storage-java-how-to-use-blob-storage)
  * [Пакет SDK службы хранилища Azure для Python](https://docs.microsoft.com/azure/storage/storage-python-how-to-use-blob-storage)

  <span/>

  В следующем фрагменте кода на C# показано, как передать ZIP-архив в хранилище BLOB-объектов Azure с помощью класса [CloudBlockBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx) клиентской библиотеки службы хранилища Azure для .NET. В этом примере кода предполагается, что ZIP-архив уже был записан в потоковый объект.

  > [!div class="tabbedCodeSnippets"]
  ```csharp
  string sasUrl = "https://productingestionbin1.blob.core.windows.net/ingestion/26920f66-b592-4439-9a9d-fb0f014902ec?sv=2014-02-14&sr=b&sig=usAN0kNFNnYE2tGQBI%2BARQWejX1Guiz7hdFtRhyK%2Bog%3D&se=2016-06-17T20:45:51Z&sp=rwl";
  Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob blockBob =
      new Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob(new System.Uri(sasUrl));
  await blockBob.UploadFromStreamAsync(stream);
  ```

5. [Подтвердите отправку приложения](commit-an-app-submission.md), выполнив следующий метод. Таким образом Центр разработки оповещается, что отправка готова и обновления требуется применить к вашей учетной записи.

  > [!div class="tabbedCodeSnippets"]
  ``` syntax
  POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/commit
  ```

6. Проверка состояния подтверждения путем выполнения следующего метода для [получения сведений о состоянии отправки приложения](get-status-for-an-app-submission.md).

  > [!div class="tabbedCodeSnippets"]
  ``` syntax
  GET https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/status
  ```

  Чтобы проверить состояние отправки, посмотрите значение поля *status* в теле ответа. Это значение должно измениться с **CommitStarted** на **PreProcessing**, если запрос выполнен успешно, или на **CommitFailed**, если в запросе возникли ошибки. Если имеются ошибки, поле *statusDetails* содержит дополнительные сведения об ошибке.

7. После успешного завершения подтверждения отправки она отправляется в Магазин для внедрения. Вы можете продолжать отслеживать ход выполнения отправки с помощью предыдущего метода или на информационной панели Центра разработки.

<span/>
### <a name="code-examples-for-managing-app-submissions"></a>Примеры кода для управления отправками приложений

Следующие статьи содержат подробные примеры кода на нескольких разных языках программирования, демонстрирующие процесс создания отправки приложения.

* [Примеры кода на C#](csharp-code-examples-for-the-windows-store-submission-api.md)
* [Примеры кода на Java](java-code-examples-for-the-windows-store-submission-api.md)
* [Примеры кода на Python](python-code-examples-for-the-windows-store-submission-api.md)

>**Примечание**&nbsp;&nbsp;Помимо вышеприведенных примеров кода мы также предоставляем модуль PowerShell с открытым исходным кодом, в котором поверх API отправки Магазина Windows реализован интерфейс командной строки. Этот модуль называется [StoreBroker](https://aka.ms/storebroker). Этот модуль можно использовать для управления приложением, тестируемой возможностью и отправками надстроек из командной строки, вместо того чтобы вызывать API отправки Магазина Windows напрямую. Кроме того, можно просмотреть дополнительные примеры вызова этого API, изучив исходный код. Модуль StoreBroker активно используется в корпорации Майкрософт в качестве основного способа отправки множества собственных приложений в Магазин. Дополнительные сведения см. на нашей [странице StoreBroker на сайте GitHub](https://aka.ms/storebroker).

<span id="manage-gradual-package-rollout">
## <a name="methods-for-managing-a-gradual-package-rollout"></a>Методы для управления постепенным выпуском пакета

Вы можете постепенно выполнять выпуск обновленных пакетов в отправке приложения для части пользователей вашего приложения в Windows 10. Это позволяет контролировать отзывы и аналитические данные для конкретных пакетов, чтобы вы могли убедиться в правильности обновления до его более широкого распространения. Можно изменить процент выпуска (или остановить обновление) для опубликованной отправки без необходимости создания новой отправки. Дополнительные сведения, включая инструкции по включению постепенного выпуска пакета и управлению им на информационной панели Центра разработки, см. в [этой статье](../publish/gradual-package-rollout.md).

Чтобы программным образом включить постепенный выпуск пакета для отправки приложения, выполните следующие действия используя методы в API отправки Магазина Windows.

  1. [Создайте отправку приложения](create-an-app-submission.md) или [получите существующую отправку приложения](get-an-app-submission.md).
  2. В данных ответа найдите ресурс [packageRollout](#package-rollout-object), задайте в поле *isPackageRollout* значение **true**, а в поле *packageRolloutPercentage* задайте процент пользователей вашего приложения, которые должны получить обновленные пакеты.
  3. Передайте обновленные данные отправки приложения методу [обновления отправки приложения](update-an-app-submission.md).

После включения постепенного выпуска пакета для отправки приложения можно использовать следующие методы, чтобы программным образом получать, обновлять, останавливать или завершать постепенный выпуск.

<table>
<colgroup>
<col width="10%" />
<col width="30%" />
<col width="60%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Метод</th>
<th align="left">URI</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">GET</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/packagerollout```</td>
<td align="left">[Получение сведений постепенного выпуска для отправки приложения](get-package-rollout-info-for-an-app-submission.md)</td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/updatepackagerolloutpercentage```</td>
<td align="left">[Обновление процента постепенного выпуска для отправки приложения](update-the-package-rollout-percentage-for-an-app-submission.md)</td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/haltpackagerollout```</td>
<td align="left">[Остановка постепенного выпуска для отправки приложения](halt-the-package-rollout-for-an-app-submission.md)</td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/finalizepackagerollout```</td>
<td align="left">[Завершение постепенного выпуска для отправки приложения](finalize-the-package-rollout-for-an-app-submission.md)</td>
</tr>
</tbody>
</table>

<span/>
## Ресурсы данных
Для управления отправками приложений методы API отправки Магазина Windows используют следующие ресурсы данных JSON.

<span id="app-submission-object" />
### Ресурс отправки приложения

Этот ресурс описывает отправку приложения.

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
            "description": "Main page",
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
  "friendlyName": "Submission 2"
}
```

У этого ресурса есть следующие значения.

| Значение      | Тип   | Описание      |
|------------|--------|-------------------|
| id            | string  | Идентификатор отправки.  |
| applicationCategory           | строка  |   Строка, указывающая [категорию или подкатегорию](https://msdn.microsoft.com/windows/uwp/publish/category-and-subcategory-table) для вашего приложения. Категории и подкатегории объединяются в одну строку с помощью символа подчеркивания "_", например **BooksAndReference_EReader**.      |  
| pricing           |  Объект  | [Ресурс цены](#pricing-object), содержащий сведения о цене приложения.        |   
| visibility           |  Строка  |  Видимость приложения. Может принимать одно из следующих значений. <ul><li>Hidden (Скрыто)</li><li>Public (Общее)</li><li>Private (Частное)</li><li>NotSet (Не задано)</li></ul>       |   
| targetPublishMode           | Строка  | Режим публикации для отправки. Может принимать одно из следующих значений. <ul><li>Immediate (Незамедлительно)</li><li>Manual (Вручную)</li><li>SpecificDate (Указанная дата)</li></ul> |
| targetPublishDate           | строка  | Дата публикации отправки в формате ISO 8601, если для *targetPublishMode* задано значение SpecificDate.  |  
| listings           |   объект  |  Словарь пар "ключ-значение", где каждый ключ является кодом страны, а каждое значение— объектом [ресурса описания](#listing-object), содержащим данные описания приложения.       |   
| hardwarePreferences           |  массив  |   Массив строк, определяющих [предпочтения оборудования](https://msdn.microsoft.com/windows/uwp/publish/enter-app-properties#hardware_preferences) для приложения. Может принимать одно из следующих значений. <ul><li>Touch (Сенсорное устройство)</li><li>Keyboard (Клавиатура)</li><li>Mouse (Мышь)</li><li>Camera (Камера)</li><li>NfcHce</li><li>Nfc</li><li>BluetoothLE</li><li>Telephony (Телефония)</li></ul>     |   
| automaticBackupEnabled           |  логический  |   Указывает, может ли Windows включать данные этого приложения в автоматические резервные копии, записываемые в OneDrive. Подробные сведения см. в разделе [Объявления приложений](https://msdn.microsoft.com/windows/uwp/publish/app-declarations).   |   
| canInstallOnRemovableMedia           |  логический  |   Указывает, могут ли клиенты устанавливать приложение на съемный носитель. Подробные сведения см. в разделе [Объявления приложений](https://msdn.microsoft.com/windows/uwp/publish/app-declarations).     |   
| isGameDvrEnabled           |  логический |   Указывает, включена ли для приложения функция DVR для игр.    |   
| hasExternalInAppProducts           |     логический          |   Указывает, позволяет ли приложение пользователям делать покупки без использования коммерческой системы Магазина Windows. Подробные сведения см. в разделе [Объявления приложений](https://msdn.microsoft.com/windows/uwp/publish/app-declarations).     |   
| meetAccessibilityGuidelines           |    логический           |  Указывает, проверено ли приложение на соответствие рекомендациям по специальным возможностям. Подробные сведения см. в разделе [Объявления приложений](https://msdn.microsoft.com/windows/uwp/publish/app-declarations).      |   
| notesForCertification           |  строка  |   Содержит [заметки по сертификации](https://msdn.microsoft.com/windows/uwp/publish/notes-for-certification) приложения.    |    
| status           |   Строка  |  Состояние отправки. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>Canceled (Отменено)</li><li>PendingCommit (Ожидание фиксации)</li><li>CommitStarted (Фиксация запущена)</li><li>CommitFailed (Сбой фиксации)</li><li>PendingPublication (Ожидание публикации)</li><li>Publishing (Выполняется публикация)</li><li>Published (Опубликовано)</li><li>PublishFailed (Сбой публикации)</li><li>PreProcessing (Предварительная обработка)</li><li>PreProcessingFailed (Сбой предварительной обработки)</li><li>Certification (Сертификация)</li><li>CertificationFailed (Сбой сертификации)</li><li>Release (Выпуск)</li><li>ReleaseFailed (Сбой выпуска)</li></ul>      |    
| statusDetails           |   Объект  | [Ресурс сведений о состоянии](#status-details-object), который содержит дополнительные сведения о состоянии отправки, включая сведения об ошибках.       |    
| fileUploadUrl           |   Строка  | URI подписанного URL-адреса (SAS) для передачи пакетов для отправки. При добавлении новых пакетов или изображений для отправки выложите ZIP-архив, содержащий пакеты и изображения, по этому URI. Дополнительную информацию см. в разделе [Создание отправки приложения](#create-an-app-submission).       |    
| applicationPackages           |   Массив  | Массив [ресурсов пакета приложения](#application-package-object), предоставляющий сведения о каждом пакете в отправке |    
| packageDeliveryOptions    | Объект  | [Ресурс параметров доставки пакета](#package-delivery-options-object), который содержит постепенный выпуск пакета и обязательные параметры обновления для отправки.  |
| enterpriseLicensing           |  Строка  |  Одно из [значений, связанных с корпоративным лицензированием](#enterprise-licensing), задающих поведение приложения в отношении корпоративного лицензирования.  |    
| allowMicrosftDecideAppAvailabilityToFutureDeviceFamilies           |  логический   |  Указывает, разрешено ли Майкрософт [делать приложение доступным для будущих семействустройств Windows 10.](https://msdn.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability#windows-10-device-families).    |    
| allowTargetFutureDeviceFamilies           | object   |  Словарь пар "ключ-значение", в котором каждый ключ представляет собой [семейство устройств Windows 10](https://msdn.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability#windows-10-device-families), а каждое значение является логическим значением, которое указывает, разрешено ли нацеливание приложения на указанное семейство устройств.     |    
| friendlyName           |   строка  |  Понятное имя отправки для отображения на информационной панели Центра разработки. Это значение создается при создании отправки.       |  


<span id="listing-object" />
### <a name="listing-resource"></a>Ресурс описания

Этот ресурс содержит информацию описания для приложения. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание                  |
|-----------------|---------|------|
|  baseListing               |   object      |  Информация [базового описания](#base-listing-object) для приложения, которая определяет описание по умолчанию на всех платформах.   |     
|  platformOverrides               | object |   Словарь пар "ключ-значение", где каждый ключ является строкой, идентифицирующей платформу, для которой необходимо переопределить информацию в описании, а каждое значение является ресурсом [базового описания](#base-listing-object) (содержащим только значения от описания до заголовка), определяющим информацию для переопределения описания для указанной платформы. Ключи могут иметь следующие значения: <ul><li>Unknown (Неизвестно)</li><li>Windows80</li><li>Windows81</li><li>WindowsPhone71</li><li>WindowsPhone80</li><li>WindowsPhone81</li></ul>     |      |     

<span id="base-listing-object" />
### <a name="base-listing-resource"></a>Ресурс базового описания

Этот ресурс содержит базовую информацию описания для приложения. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание       |
|-----------------|---------|------|
|  copyrightAndTrademarkInfo                |   string      |  Необязательные [сведения об авторских правах и (или) товарных знаках](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#copyright-and-trademark-info).  |
|  keywords                |  array       |  Массив [ключевых слов](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#keywords), способствующих появлению вашего приложения в результатах поиска.    |
|  licenseTerms                |    string     | Необязательные [условия лицензионного соглашения](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#additional-license-terms) для вашего приложения.     |
|  privacyPolicy                |   string      |   URL-адрес [политики конфиденциальности](../publish/create-app-store-listings.md#privacy-policy) для вашего приложения.    |
|  supportContact                |   string      |  URL-адрес или адрес электронной почты для [контактных данных о поддержке](../publish/create-app-store-listings.md#support-contact-info) вашего приложения.     |
|  websiteUrl                |   string      |  URL-адрес [веб-страницы](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#website) вашего приложения.    |    
|  Описание               |    string     |   [Описание](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#description) для описания приложения.   |     
|  features               |    array     |  Массив размером до 20 строк со списком [функций](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#app-features) вашего приложения.     |
|  releaseNotes               |  string       |  [Заметки о выпуске](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#release-notes) для вашего приложения.    |
|  images               |   Массив      |  Массив ресурсов [изображений и значков](#image-object) для описания приложения.  |
|  recommendedHardware               |   Массив      |  Массив размером до 11 строк со списком [рекомендуемой конфигурации оборудования](https://msdn.microsoft.com/windows/uwp/publish/create-app-descriptions#recommended-hardware) для вашего приложения.     |
|  title               |     Строка    |   Название для описания приложения.   |  

<span id="image-object" />
### <a name="image-resource"></a>Ресурс изображений

Этот ресурс содержит данные изображений и значков для описания приложения. Дополнительные сведения об изображениях и значках для описания см. в разделе [Снимки экранов приложения и изображения](https://msdn.microsoft.com/windows/uwp/publish/app-screenshots-and-images). Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание           |
|-----------------|---------|------|
|  fileName               |    string     |   Имя файла изображения в ZIP-архиве, который был передан для отправки.    |     
|  fileStatus               |   string      |  Состояние файла изображения. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>PendingUpload (Ожидает передачи)</li><li>Uploaded (Передан)</li><li>PendingDelete (Ожидает удаления)</li></ul>   |
|  id  |  string  | Идентификатор изображения, указанный Центром разработки.  |
|  Описание  |  string  | Описание изображения.  |
|  imageType  |  string  | Одна из следующих строк, указывающих тип изображения: <ul><li>Unknown (Неизвестно)</li><li>Screenshot (Снимок экрана)</li><li>PromotionalArtwork414X180 (Рекламное изображение 414X180)</li><li>PromotionalArtwork846X468 (Рекламное изображение 846X468)</li><li>PromotionalArtwork558X756 (Рекламное изображение 558X756)</li><li>PromotionalArtwork414X468 (Рекламное изображение 414X468)</li><li>PromotionalArtwork558X558 (Рекламное изображение 558X558)</li><li>PromotionalArtwork2400X1200 (Рекламное изображение 2400X1200)</li><li>Icon (Значок)</li><li>WideIcon358X173 (Широкий значок 358X173)</li><li>BackgroundImage1000X800 (Фоновое изображение 1000X800)</li><li>SquareIcon358X358 (Фоновое изображение 358X358)</li><li>MobileScreenshot (Снимок экрана мобильного устройства)</li><li>XboxScreenshot (Снимок экрана Xbox)</li><li>SurfaceHubScreenshot (Снимок экрана SurfaceHub)</li><li>HoloLensScreenshot (Снимок экрана HoloLens)</li></ul>      |


<span id="pricing-object" />
### <a name="pricing-resource"></a>Ресурс цены

Этот ресурс содержит сведения о ценах для приложения. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
|  trialPeriod               |    string     |  Строка, указывающая срок действия пробной версии приложения. Может принимать одно из следующих значений. <ul><li>NoFreeTrial (Нет пробного периода)</li><li>OneDay (Один день)</li><li>TrialNeverExpires (Бессрочный пробный период)</li><li>SevenDays (7 дней)</li><li>FifteenDays (15 дней)</li><li>ThirtyDays (30 дней)</li></ul>    |
|  marketSpecificPricings               |    object     |  Словарь пар "ключ-значение", где каждый ключ представляет собой код страны ISO 3166-1 alpha-2 из двух букв, а каждое значение — [ценовую категорию](#price-tiers). Эти элементы представляют [особые цены на ваше приложение для определенных рынков](https://msdn.microsoft.com/windows/uwp/publish/define-pricing-and-market-selection#markets-and-custom-prices). Любые элементы этого словаря переопределяют базовую цену, заданную значением *priceId* для указанного рынка.      |     
|  sales               |   Массив      |  **Не рекомендуется**. Массив [ресурсов продажи](#sale-object), содержащих сведения о продажах для приложения.   |     
|  priceId               |   Строка      |  [Ценовая категория](#price-tiers), указывающая [базовую цену](https://msdn.microsoft.com/windows/uwp/publish/define-pricing-and-market-selection#base-price) для приложения.   |     
|  isAdvancedPricingModel               |   boolean (логическая)      |  Если **true**, ваша учетная запись разработчика имеет доступ к расширенному набору ценовых категорий от 0,99 долл. США до 1999,99 долл. США. Если **false**, ваша учетная запись разработчика имеет доступ к стандартному набору ценовых категорий от 0,99 долл. США до 999,99 долл. США. Дополнительные сведения о разных категориях см. в разделе [Ценовые категории](#price-tiers).<br/><br/>**Примечание**.&nbsp;&nbsp;Данное поле предназначено только для чтения.   |


<span id="sale-object" />
### <a name="sale-resource"></a>Ресурс продажи

Этот ресурс содержит сведения о продажах для приложения.

>**Важно!**&nbsp;&nbsp;Ресурс **продажи** больше не поддерживается, и в настоящее время невозможно получить или изменить данные продажи для отправки приложения с помощью API отправки в Магазин Windows.

   > * После вызова [метода GET для получения отправки приложения](get-an-app-submission.md) значение *sales* будет пустым. Вы можете продолжать использовать информационную панель Центра разработки для получения данных продаж для отправки приложения.
   > * При вызове [метода PUT для обновления отправки приложения](update-an-app-submission.md) сведения в значении *sales* будут игнорироваться. Вы можете продолжать использовать информационную панель Центра разработки для изменения данных продаж для отправки приложения.

> В дальнейшем мы обновим API отправки в Магазин Windows, чтобы предложить пользователям новый способ программного доступа к информации о продажах для отправок приложений.

Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание    |
|-----------------|---------|------|
|  name               |    string     |   Имя продажи.    |     
|  basePriceId               |   string      |  [Ценовая категория](#price-tiers), используемая для базовой цены продажи.    |     
|  startDate               |   string      |   Дата начала для продажи в формате ISO 8601.  |     
|  endDate               |   string      |  Дата окончания для продажи в формате ISO 8601.      |     
|  marketSpecificPricings               |   object      |   Словарь пар "ключ-значение", где каждый ключ представляет собой код страны ISO 3166-1 alpha-2 из двух букв, а каждое значение — [ценовую категорию](#price-tiers). Эти элементы представляют [особые цены на ваше приложение для определенных рынков](https://msdn.microsoft.com/windows/uwp/publish/define-pricing-and-market-selection#markets-and-custom-prices). Любые элементы этого словаря переопределяют базовую цену, заданную значением *basePriceId* для указанного рынка.    |


<span id="status-details-object" />
### <a name="status-details-resource"></a>Ресурс сведений о состоянии

Этот ресурс содержит дополнительные сведения о состоянии отправки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание         |
|-----------------|---------|------|
|  errors               |    Объект     |   Массив [ресурсов сведений о состоянии](#status-detail-object), который содержит информацию об ошибках для отправки.    |     
|  warnings               |   Объект      | Массив [ресурсов сведений о состоянии](#status-detail-object), который содержит информацию о предупреждениях для отправки.      |
|  certificationReports               |     Объект    |   Массив [ресурсов отчета сертификации](#certification-report-object), который предоставляет доступ к данным отчета о сертификации для отправки. В случае сбоя сертификации можно проверить эти отчеты для получения дополнительной информации.   |  


<span id="status-detail-object" />
### <a name="status-detail-resource"></a>Ресурс сведений о состоянии

Этот ресурс содержит дополнительные сведения обо всех связанных ошибках или предупреждениях для отправки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
|  code               |    Строка     |   [Код состояния отправки](#submission-status-code), описывающий тип ошибки или предупреждения.   |     
|  details               |     Строка    |  Сообщение с дополнительными сведениями о проблеме.     |


<span id="application-package-object" />
### <a name="application-package-resource"></a>Ресурс пакета приложения

Этот ресурс содержит сведения о пакете приложения для отправки.

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

У этого ресурса есть следующие значения.  

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
### <a name="certification-report-resource"></a>Ресурс отчета о сертификации

Этот ресурс предоставляет доступ к данным отчета о сертификации для отправки. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание             |
|-----------------|---------|------|
|     date            |    string     |  Дата и время создания отчета (в формате ISO 8601).    |
|     reportUrl            |    Строка     |  URL-адрес, по которому можно получить доступ к отчету.    |


<span id="package-delivery-options-object" />
### <a name="package-delivery-options-resource"></a>Ресурс параметров доставки пакета

Этот ресурс содержит постепенный выпуск пакета и обязательные параметры обновления для отправки.

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

У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
| packageRollout   |   Объект      |  [Ресурс выпуска пакета](#package-rollout-object), который содержит параметры постепенного выпуска пакета для отправки.   |  
| isMandatoryUpdate    | Логический    |  Указывает, следует ли рассматривать пакеты в этой отправке как обязательные для самоустанавливающихся обновлений приложений. Дополнительные сведения об обязательных пакетах самоустанавливаемых обновлений приложений см. в разделе [Загрузка и установка обновлений пакетов для приложения](../packaging/self-install-package-updates.md).    |  
| mandatoryUpdateEffectiveDate    |  date   |  Дата и время, когда пакеты в этой отправке станут обязательными, в формате ISO 8601 и по часовому поясу UTC.   |        

<span id="package-rollout-object" />
### <a name="package-rollout-resource"></a>Ресурс выпуска пакета

Этот ресурс содержит [параметры постепенного выпуска пакета](#manage-gradual-package-rollout) для отправки. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
| isPackageRollout   |   boolean      |  Указывает, включен ли постепенный выпуск пакета для этой отправки.    |  
| packageRolloutPercentage    | float    |  Процент пользователей, которые будут получать пакеты при постепенном выпуске.    |  
| packageRolloutStatus    |  string   |  Одна из следующих строк, указывающая состояние постепенного выпуска пакета: <ul><li>PackageRolloutNotStarted</li><li>PackageRolloutInProgress</li><li>PackageRolloutComplete</li><li>PackageRolloutStopped</li></ul>  |  
| fallbackSubmissionId    |  string   |  Идентификатор отправки, который будет поступать от пользователей, которые не получают пакеты постепенного выпуска.   |          

>**Примечание.**&nbsp;&nbsp;Значения *packageRolloutStatus* и *fallbackSubmissionId* назначаются Центром разработки, но не разработчиками. Если эти значения содержатся в теле запроса, они будут проигнорированы. 

<span/>

## <a name="enums"></a>Перечисления

Эти методы используют следующие перечисления.

<span id="price-tiers" />
### <a name="price-tiers"></a>Ценовые категории

Следующие значения представляют доступные ценовые категории в ресурсе [ценовой ресурс](#pricing-object) для отправки приложения.

| Значение           | Описание        |
|-----------------|------|
|  Base               |   Ценовая категория не задана; используется базовая цена для приложения.      |     
|  NotAvailable              |   Приложение недоступно в указанном регионе.    |     
|  Free              |   Бесплатное приложение.    |    
|  Tier*xxx*               |   Строка, указывающая ценовую категорию приложения в формате **Tier<em>xxxx</em>**. В настоящее время поддерживаются следующие ценовые категории.<br/><br/><ul><li>Если значение *isAdvancedPricingModel* [ценового ресурса](#pricing-object) — **true**, для вашей учетной записи доступны такие ценовые категории: **Tier1012** - **Tier1424**.</li><li>Если значение *isAdvancedPricingModel* [ценового ресурса](#pricing-object) — **false**, для вашей учетной записи доступны такие ценовые категории: **Tier2** - **Tier96**.</li></ul>Полную таблицу ценовых категорий, доступных для вашей учетной записи разработчика, включая цены для различных рынков, связанные с каждой категорией, можно найти на странице **Цены и доступность** для любой вашей отправки приложения в информационной панели Центра разработки, нажав ссылку **посмотреть таблицу** в разделе **Рынки и особые цены** (для некоторых учетных записей разработчика эта ссылка находится в разделе **Цены**).    |


<span id="enterprise-licensing" />
### <a name="enterprise-licensing-values"></a>Значения корпоративного лицензирования

Следующие значения представляют поведение корпоративного лицензирования для приложения. Дополнительные сведения об этих параметрах см. в разделе [Параметры корпоративного лицензирования](https://msdn.microsoft.com/windows/uwp/publish/organizational-licensing).

| Значение           |  Описание      |
|-----------------|---------------|
| None            |     Приложение недоступно организациям путем корпоративного лицензирования от Магазина (в режиме онлайн).         |     
| Online        |     Приложение доступно организациям путем корпоративного лицензирования от Магазина (в режиме онлайн).  |
| OnlineAndOffline | Приложение доступно организациям путем корпоративного лицензирования от Магазина (в режиме онлайн); кроме того, приложение доступно организациям путем лицензирования без подключения (в автономном режиме). |


<span id="submission-status-code" />
### <a name="submission-status-code"></a>Код состояния отправки

Следующие значения представляют код состояния отправки.

| Значение           |  Описание      |
|-----------------|---------------|
| None (Нет)            |     Код не указан.         |     
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

## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Получение данных приложения с помощью API отправки Магазина Windows](get-app-data.md)
* [Отправки приложений на информационной панели Центра разработки](https://msdn.microsoft.com/windows/uwp/publish/app-submissions)
