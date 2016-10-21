---
author: mcleanbyron
ms.assetid: 2A454057-FF14-40D2-8ED2-CEB5F27E0226
description: "Используйте эти методы в API отправки Магазина Windows для управления отправками тестовых пакетов для приложений, которые зарегистрированы в вашей учетной записи Центра разработки для Windows."
title: "Управление отправками тестовых пакетов с помощью API отправки Магазина Windows"
translationtype: Human Translation
ms.sourcegitcommit: 5f975d0a99539292e1ce91ca09dbd5fac11c4a49
ms.openlocfilehash: 18d28495b80101cf5cfe53869b0f5cd3d61b50c9

---

# Управление отправками тестовых пакетов с помощью API отправки Магазина Windows




Используйте указанные ниже методы в API отправки Магазина Windows для управления отправками тестовых пакетов для приложений, которые зарегистрированы в вашей учетной записи Центра разработки для Windows. Введение в API отправки Магазина Windows, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md).

>**Примечание.**&nbsp;&nbsp;Эти методы могут применяться только для учетных записей Центра разработки для Windows, у которых имеется разрешение на использование API отправки Магазина Windows. Такое разрешение имеется не у всех учетных записей. Чтобы можно было использовать эти методы для создания отправок и управления отправками для тестового пакета, этот тестовый пакет должен существовать в учетной записи Центра разработки. Тестовый пакет можно создать [с помощью информационной панели Центра разработки](https://msdn.microsoft.com/windows/uwp/publish/package-flights) или с помощью методов API отправки Магазина Windows, которые описываются в разделе [Управление тестовыми пакетами](manage-flights.md).

| Метод        | URI    | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| GET | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}``` | Получение данных для существующей отправки тестового пакета. Дополнительные сведения см. в разделе [Получение отправки тестового пакета](get-a-flight-submission.md). |
| GET | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/status``` | Получение состояния существующей отправки тестового пакета. Дополнительные сведения см. в разделе [Получение состояния отправки тестового пакета](get-status-for-a-flight-submission.md). |
| POST | ```https://manage.devcenter.microsoft.com/v1.0/applications/{applicationId}/flights/{flightId}/submissions``` | Создание новой отправки тестового пакета для приложения, которое зарегистрировано в вашей учетной записи Центра разработки для Windows. Дополнительные сведения см. в разделе [Создание отправки тестового пакета](create-a-flight-submission.md). |
| POST | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/commit``` | Подтверждение новой или обновленной отправки тестового пакета в Центр разработки для Windows. Дополнительные сведения см. в разделе [Подтверждение отправки тестового пакета](commit-a-flight-submission.md). |
| PUT | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}``` | Обновление существующей отправки тестового пакета. Дополнительные сведения см. в разделе [Обновление отправки тестового пакета](update-a-flight-submission.md). |
| DELETE | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}``` | Удаление отправки тестового пакета. Дополнительные сведения см. в разделе [Удаление отправки тестового пакета](delete-a-flight-submission.md). |

<span id="create-a-package-flight-submission">
## Создание отправки тестового пакета

Чтобы создать отправку для тестового пакета, выполните следующие действия.

1. Если это еще не сделано, выполните необходимые условия, описанные в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md), включая привязку приложения Azure AD к учетной записи Центра разработки для Windows и получение идентификатора клиента и ключа. Это необходимо сделать только один раз; после получения идентификатора клиента и ключа с их помощью можно в любой момент создать новый маркер доступа Azure AD.  

2. [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token). Этот маркер доступа необходимо передавать методам из API отправки Магазина Windows. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

3. Выполните следующий метод в API отправки Магазина Windows. Этот метод создает новую выполняющуюся отправку, которая является копией последней опубликованной отправки. Дополнительные сведения см. в разделе [Создание отправки тестового пакета](create-a-flight-submission.md).

  ```
  POST https://manage.devcenter.microsoft.com/v1.0/my/applications{applicationId}/flights/{flightId}/submissions
  ```

  Тело ответа содержит три элемента: идентификатор новой отправки, данные для новой отправки (в том числе все описания и информацию о ценах), а также URI подписанного URL-адреса (Shared Access Signature, SAS) для передачи любых пакетов для отправки. Дополнительные сведения о SAS см. в разделе [Подписанные URL-адреса, часть 1. Общие сведения о модели SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

4. Если добавляются новые пакеты для отправки, [подготовьте пакеты](https://msdn.microsoft.com/windows/uwp/publish/app-package-requirements) и добавьте их в ZIP-архив.

5. Обновите данные отправки, внеся все необходимые для новой отправки изменения, затем выполните следующий метод для обновления отправки. Дополнительные сведения см. в разделе [Обновление отправки тестового пакета](update-a-flight-submission.md).

  ```
  PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}
  ```

  >**Примечание.**&nbsp;&nbsp;Если в отправку добавляются новые пакеты, обязательно обновите данные отправки, чтобы они ссылались на имя и относительный путь этих файлов в ZIP-архиве.

4. При добавлении новых пакетов для отправки передайте ZIP-архив по URI SAS, полученному в теле ответа метода POST, который вызывался на шаге 2. Дополнительные сведения см. в разделе [Подписанные URL-адреса, часть 2. Создание и использование SAS с хранилищем BLOB-объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/).

  В следующем фрагменте кода показано, как передать архив с помощью класса [CloudBlockBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx) клиентской библиотеки службы хранилища Azure для .NET.

  ```csharp
  string sasUrl = "https://productingestionbin1.blob.core.windows.net/ingestion/26920f66-b592-4439-9a9d-fb0f014902ec?sv=2014-02-14&sr=b&sig=usAN0kNFNnYE2tGQBI%2BARQWejX1Guiz7hdFtRhyK%2Bog%3D&se=2016-06-17T20:45:51Z&sp=rwl";

  Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob blockBob =
      new Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob(new System.Uri(sasUrl));
  await blockBob.UploadFromStreamAsync(stream);
  ```

5. Подтвердите отправку, выполнив следующий метод. Таким образом Центр разработки оповещается, что отправка готова и обновления требуется применить к вашей учетной записи. Дополнительные сведения см. в разделе [Подтверждение отправки тестового пакета](commit-a-flight-submission.md).

  ```
  POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/commit
  ```

6. Проверьте состояние подтверждения, выполнив следующий метод. Дополнительные сведения см. в разделе [Получение состояния отправки тестового пакета](get-status-for-a-flight-submission.md).

  ```
  GET https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/status
  ```

  Чтобы проверить состояние отправки, посмотрите значение поля *status* в теле ответа. Это значение должно измениться с **CommitStarted** на **PreProcessing**, если запрос выполнен успешно, или на **CommitFailed**, если в запросе возникли ошибки. Если имеются ошибки, поле *statusDetails* содержит дополнительные сведения об ошибке.

7. После успешного завершения подтверждения отправки она отправляется в Магазин для внедрения. Вы можете продолжать отслеживать ход выполнения отправки с помощью предыдущего метода или на информационной панели Центра разработки.

## Ресурсы

В этих методах используются следующие ресурсы данных.

<span id="flight-submission-object" />
### Отправка тестового пакета

Этот ресурс представляет отправку для тестового пакета. В следующем примере показан формат этого ресурса.

```json
{
  "id": "1152921504621243649",
  "flightId": "cd2e368a-0da5-4026-9f34-0e7934bc6f23",
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [],
    "warnings": [],
    "certificationReports": []
  },
  "flightPackages": [
    {
      "fileName": "newPackage.appx",
      "fileStatus": "PendingUpload",
      "id": "",
      "version": "1.0.0.0",
      "languages": ["en-us"],
      "capabilities": [],
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None"
    }
  ],
  "fileUploadUrl": "https://productingestionbin1.blob.core.windows.net/ingestion/8b389577-5d5e-4cbe-a744-1ff2e97a9eb8?sv=2014-02-14&sr=b&sig=wgMCQPjPDkuuxNLkeG35rfHaMToebCxBNMPw7WABdXU%3D&se=2016-06-17T21:29:44Z&sp=rwl",
  "targetPublishMode": "Immediate",
  "targetPublishDate": "",
  "notesForCertification": "No special steps are required for certification of this app."
}
```

Этот ресурс содержит следующие значения.

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id            | string  | Идентификатор для отправки.  |
| flightId           | string  |  Идентификатор тестового пакета, с которым связана отправка.  |  
| status           | string  | Состояние отправки. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>Canceled (Отменено)</li><li>PendingCommit (Ожидание фиксации)</li><li>CommitStarted (Фиксация запущена)</li><li>CommitFailed (Сбой фиксации)</li><li>PendingPublication (Ожидание публикации)</li><li>Publishing (Выполняется публикация)</li><li>Published (Опубликовано)</li><li>PublishFailed (Сбой публикации)</li><li>PreProcessing (Предварительная обработка)</li><li>PreProcessingFailed (Сбой предварительной обработки)</li><li>Certification (Сертификация)</li><li>CertificationFailed (Сбой сертификации)</li><li>Release (Выпуск)</li><li>ReleaseFailed (Сбой выпуска)</li></ul>   |
| statusDetails           | object  |  Содержит дополнительные сведения о состоянии отправки, включая сведения об ошибках. Дополнительные сведения см. далее в разделе [Сведения о состоянии](#status-details-object). |
| flightPackages           | array  | Содержит объекты, предоставляющие сведения о каждом пакете в отправке. Дополнительные сведения см. в разделе [Тестовый пакет](#flight-package-object) ниже.  |
| fileUploadUrl           | string  | URI подписанного URL-адреса (SAS) для передачи пакетов для отправки. При добавлении новых пакетов для отправки выложите ZIP-архив, содержащий пакеты, по этому URI. Дополнительные сведения см. в разделе [Создание отправки тестового пакета](#create-a-package-flight-submission).  |
| targetPublishMode           | string  | Режим публикации для отправки. Может принимать одно из следующих значений. <ul><li>Immediate (Незамедлительно)</li><li>Manual (Вручную)</li><li>SpecificDate (Указанная дата)</li></ul> |
| targetPublishDate           | string  | Дата публикации отправки в формате ISO 8601, если для *targetPublishMode* задано значение SpecificDate.  |
| notesForCertification           | string  |  Предоставляет дополнительные сведения для тест-инженеров сертификации, такие как учетные данные тестовой учетной записи и действия, которые требуется выполнить для доступа к функциям их проверки. Дополнительные сведения см. в разделе [Заметки по сертификации](https://msdn.microsoft.com/windows/uwp/publish/notes-for-certification). |

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


<span id="certification-report-object" />
### Отчет о сертификации

Этот ресурс предоставляет доступ к данным отчета о сертификации для отправки. Этот ресурс содержит следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|------|
|     date            |    string     |  Дата и время создания отчета (в формате ISO 8601).    |
|     reportUrl            |    string     |  URL-адрес, по которому можно получить доступ к отчету.    |


<span id="flight-package-object" />
### Тестовый пакет

Этот ресурс содержит сведения о пакете в отправке. В следующем примере показан формат этого ресурса.

```json
{
  "flightPackages": [
    {
      "fileName": "newPackage.appx",
      "fileStatus": "PendingUpload",
      "id": "",
      "version": "1.0.0.0",
      "languages": ["en-us"],
      "capabilities": [],
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None"
    }
  ],
}
```

Этот ресурс содержит следующие значения.

>**Примечание.**&nbsp;&nbsp;При вызове метода для [обновления отправки тестового пакета](update-a-flight-submission.md) в теле запроса требуются только значения *fileName*, *fileStatus*, *minimumDirectXVersion* и *minimumSystemRam* этого объекта. Остальные значения заполняются Центром разработки.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|------|
| fileName   |   string      |  Имя пакета.    |  
| fileStatus    | string    |  Состояние пакета. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>PendingUpload (Ожидает передачи)</li><li>Uploaded (Передан)</li><li>PendingDelete (Ожидает удаления)</li></ul>    |  
| id    |  string   |  Идентификатор, который уникально идентифицирует пакет. Это значение используется Центром разработки.   |     
| version    |  string   |  Версия пакета приложения. Дополнительные сведения см. в разделе [Нумерация версий пакета](https://msdn.microsoft.com/windows/uwp/publish/package-version-numbering).   |   
| architecture    |  string   |  Архитектура пакета приложения (например, ARM).   |     
| languages    | array    |  Массив кодов языков, которые поддерживает приложение. Дополнительные сведения см. в разделе [Поддерживаемые языки](https://msdn.microsoft.com/windows/uwp/publish/supported-languages).    |     
| capabilities    |  array   |  Массив возможностей, необходимых для этого пакета. Дополнительные сведения о возможностях см. в разделе [Объявления возможностей приложения](https://msdn.microsoft.com/windows/uwp/packaging/app-capability-declarations).   |     
| minimumDirectXVersion    |  string   |  Минимальная версия DirectX, поддерживаемая пакетом приложения. Может задаваться только для приложений, предназначенных для Windows 8.x; игнорируется для приложений, предназначенных для других версий. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>DirectX93</li><li>DirectX100</li></ul>   |     
| minimumSystemRam    | string    |  Минимальный объем ОЗУ, необходимый для пакета приложения. Может задаваться только для приложений, предназначенных для Windows 8.x; игнорируется для приложений, предназначенных для других версий. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>Memory2GB</li></ul>   |    

<span/>

## Перечисления

Эти методы используют следующие перечисления.

<span id="submission-status-code" />
### Код состояния отправки

Следующие коды представляют состояние отправки.

| Код           |  Описание      |
|-----------------|---------------|
|  None (Нет)            |     Код не указан.         |     
|      InvalidArchive        |     ZIP-архив, содержащий пакет, является недопустимым или имеет неизвестный формат архива.  |
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
* [Управление тестовыми пакетами с помощью API отправки Магазина Windows](manage-flights.md)
* [Получение отправки тестового пакета](get-a-flight-submission.md)
* [Создание отправки тестового пакета](create-a-flight-submission.md)
* [Обновление отправки тестового пакета](update-a-flight-submission.md)
* [Подтверждение отправки тестового пакета](commit-a-flight-submission.md)
* [Удаление отправки тестового пакета](delete-a-flight-submission.md)
* [Получение состояния отправки тестового пакета](get-status-for-a-flight-submission.md)



<!--HONumber=Aug16_HO5-->


