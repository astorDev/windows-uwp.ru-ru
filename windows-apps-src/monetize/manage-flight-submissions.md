---
ms.assetid: 2A454057-FF14-40D2-8ED2-CEB5F27E0226
description: Используйте эти методы в API отправки Microsoft Store, чтобы управлять отправкой рейсов пакетов для приложений, зарегистрированных в учетной записи центра партнеров.
title: Управление отправкой тестового пакета
ms.date: 04/16/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, отправки тестируемых возможностей
ms.localizationpriority: medium
ms.openlocfilehash: 4e96f6d2495583fcee4d16e54a5c8a5e208fec27
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259255"
---
# <a name="manage-package-flight-submissions"></a>Управление отправкой тестового пакета

API отправки в Microsoft Store предоставляет методы, которые можно использовать для управления отправками тестового пакета для приложений, включая постепенные выпуски пакетов. Введение в API отправки в Microsoft Store, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md).

> [!IMPORTANT]
> Если для создания отправки пакета используется API отправки Microsoft Store, обязательно внесите дальнейшие изменения в отправку только с помощью API, а не из центра партнеров. Если вы используете информационную панель для изменения отправки, изначально созданной с помощью API, вы более не сможете изменять или фиксировать эту отправку с помощью этого API. В некоторых случаях отправка может оставаться в состоянии ошибки, когда продолжить процесс отправки невозможно. В этом случае необходимо удалить отправку и создать новую.

<span id="methods-for-package-flight-submissions" />

## <a name="methods-for-managing-package-flight-submissions"></a>Методы управления отправками тестового пакета

Используйте следующие методы для получения, создания, обновления, фиксации и удаления отправки тестового пакета. Перед использованием этих методов рейс должен уже существовать в центре партнеров. Вы можете создать перелет пакета [в центре партнеров](https://docs.microsoft.com/windows/uwp/publish/package-flights) или с помощью методов API отправки Microsoft Store, описанных в разделе [Управление рейсами пакетов](manage-flights.md).

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
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}</td>
<td align="left"><a href="get-a-flight-submission.md">Получение существующей отправки рейса пакета</a></td>
</tr>
<tr>
<td align="left">GET</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/status</td>
<td align="left"><a href="get-status-for-a-flight-submission.md">Получение состояния текущей отправки пакета</a></td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions</td>
<td align="left"><a href="create-a-flight-submission.md">Создание нового пакета передача рейса</a></td>
</tr>
<tr>
<td align="left">PUT</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}</td>
<td align="left"><a href="update-a-flight-submission.md">Обновление существующей отправки рейса пакета</a></td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/commit</td>
<td align="left"><a href="commit-a-flight-submission.md">Фиксация новой или обновленной отправки пакета</a></td>
</tr>
<tr>
<td align="left">DELETE</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}</td>
<td align="left"><a href="delete-a-flight-submission.md">Удаление пакета передача рейса</a></td>
</tr>
</tbody>
</table>

<span id="create-a-package-flight-submission">

## <a name="create-a-package-flight-submission"></a>Создание отправки тестового пакета

Чтобы создать отправку для тестового пакета, выполните следующие действия.

1. Если вы еще не сделали этого, выполните предварительные требования, описанные в статье [Создание и управление отправкой с помощью Microsoft Store Services](create-and-manage-submissions-using-windows-store-services.md), включая связывание приложения Azure AD с учетной записью центра партнеров и получение идентификатора и ключа клиента. Это необходимо сделать только один раз; после получения идентификатора клиента и ключа с их помощью можно в любой момент создать новый маркер доступа Azure AD.  

2. [Получение токена доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token). Этот маркер доступа необходимо передавать методам из API отправки в Microsoft Store. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

3. [Создание отправки тестового пакета](create-a-flight-submission.md) путем выполнения следующего метода в API отправки в Microsoft Store. Этот метод создает новую выполняющуюся отправку, которая является копией последней опубликованной отправки.

    ```json
    POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions
    ```

    Тело ответа содержит ресурс [отправка тестового пакета](#flight-submission-object), включающий в себя идентификатор новой отправки, URI подписанного URL-адреса (Shared Access Signature, SAS) для передачи любых пакетов приложений и изображений описания для отправки в хранилище BLOB-объектов Azure, а также данные для новой отправки (в том числе все описания и информацию о ценах).

    > [!NOTE]
    > URI SAS предоставляет доступ к защищенному ресурсу в хранилище Azure без необходимости в использовании ключей учетной записи. Основные сведения об URI SAS и их использовании с хранилищем BLOB-объектов Azure см. в разделах [Подписанные URL-адреса, часть 1. Общие сведения о модели SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) и [Подписанные URL-адреса, часть 2. Создание и использование SAS с хранилищем BLOB-объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/).

4. При добавлении новых пакетов для отправки [подготовьте пакеты](https://docs.microsoft.com/windows/uwp/publish/app-package-requirements) и добавьте их в ZIP-архив.

5. Проверьте данные [отправки тестового пакета](#flight-submission-object), внеся все необходимые для новой отправки изменения, затем выполните следующий метод для [обновления отправки тестового пакета](update-a-flight-submission.md).

    ```json
    PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}
    ```
      > [!NOTE]
      > Если в отправку добавляются новые пакеты, обязательно обновите данные отправки, чтобы они ссылались на имя и относительный путь этих файлов в ZIP-архиве.

4. При добавлении новых пакетов для отправки выложите ZIP-архив в [хранилище BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage), используя URI SAS, полученный в теле ответа метода POST, вызов которого был выполнен ранее. Существуют разные библиотеки Azure, которые можно использовать в этих целях на различных платформах, включая следующие.

    * [Клиентская библиотека службы хранилища Azure для .NET](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs)
    * [Пакет SDK службы хранилища Azure для Java](https://docs.microsoft.com/azure/storage/storage-java-how-to-use-blob-storage)
    * [Пакет SDK службы хранилища Azure для Python](https://docs.microsoft.com/azure/storage/storage-python-how-to-use-blob-storage)

    В следующем фрагменте кода на C# показано, как передать ZIP-архив в хранилище BLOB-объектов Azure с помощью класса [CloudBlockBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob) клиентской библиотеки службы хранилища Azure для .NET. В этом примере кода предполагается, что ZIP-архив уже был записан в потоковый объект.

    ```csharp
    string sasUrl = "https://productingestionbin1.blob.core.windows.net/ingestion/26920f66-b592-4439-9a9d-fb0f014902ec?sv=2014-02-14&sr=b&sig=usAN0kNFNnYE2tGQBI%2BARQWejX1Guiz7hdFtRhyK%2Bog%3D&se=2016-06-17T20:45:51Z&sp=rwl";
    Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob blockBob =
        new Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob(new System.Uri(sasUrl));
    await blockBob.UploadFromStreamAsync(stream);
    ```

5. [Подтвердите отправку тестового пакета](commit-a-flight-submission.md), выполнив следующий метод. В результате вы получите оповещение об этом центре партнеров, и теперь ваши обновления должны быть применены к вашей учетной записи.

    ```json
    POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/commit
    ```

6. Проверка состояния подтверждения путем выполнения следующего метода для [получения сведений о состоянии отправки тестового пакета](get-status-for-a-flight-submission.md).

    ```json
    GET https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/status
    ```

    Чтобы проверить состояние отправки, посмотрите значение поля *status* в теле ответа. Это значение должно измениться с **CommitStarted** на **PreProcessing**, если запрос выполнен успешно, или на **CommitFailed**, если в запросе возникли ошибки. Если имеются ошибки, поле *statusDetails* содержит дополнительные сведения об ошибке.

7. После успешного завершения подтверждения отправки она отправляется в Магазин для внедрения. Вы можете продолжить отслеживать ход отправки, используя предыдущий метод или посетив центр партнеров.

<span/>

## <a name="code-examples"></a>Примеры кода

Следующие статьи содержат подробные примеры кода на нескольких разных языках программирования, демонстрирующие процесс создания отправки тестового пакета.

* [C#примеры кода](csharp-code-examples-for-the-windows-store-submission-api.md)
* [Примеры кода Java](java-code-examples-for-the-windows-store-submission-api.md)
* [Примеры кода Python](python-code-examples-for-the-windows-store-submission-api.md)

## <a name="storebroker-powershell-module"></a>Модуль StoreBroker PowerShell

В качестве альтернативы прямому вызову API отправки в Microsoft Store также предоставляется модуль PowerShell с открытым исходным кодом, в котором поверх API реализован интерфейс командной строки. Этот модуль называется [StoreBroker](https://github.com/Microsoft/StoreBroker). Этот модуль можно использовать для управления приложением, тестируемой возможностью и отправками надстроек из командной строки, вместо того чтобы вызывать API отправки в Microsoft Store напрямую. Кроме того, можно просмотреть дополнительные примеры вызова этого API, изучив исходный код. Модуль StoreBroker активно используется Microsoft в качестве основного способа отправки многих собственных приложений компании в Магазин.

Дополнительные сведения см. на нашей [странице StoreBroker на сайте GitHub](https://github.com/Microsoft/StoreBroker).

<span id="manage-gradual-package-rollout">

## <a name="manage-a-gradual-package-rollout-for-a-package-flight-submission"></a>Управление постепенным выпуском пакета для отправки тестового пакета

Вы можете постепенно выполнять выпуск обновленных пакетов в отправке тестового пакета для части пользователей вашего приложения в Windows 10. Это позволяет контролировать отзывы и аналитические данные для конкретных пакетов, чтобы вы могли убедиться в правильности обновления до его более широкого распространения. Можно изменить процент выпуска (или остановить обновление) для опубликованной отправки без необходимости создания новой отправки. Дополнительные сведения, в том числе инструкции по включению постепенного развертывания пакетов и управлению ими в центре партнеров, см. в [этой статье](../publish/gradual-package-rollout.md).

Чтобы программным образом включить постепенный выпуск пакета для отправки тестового пакета, выполните следующие действия, используя методы в API отправки в Microsoft Store.

  1. [Создайте отправку тестового пакета](create-a-flight-submission.md) или [получите отправку тестового пакета](get-a-flight-submission.md).
  2. В данных ответа найдите ресурс [packageRollout](#package-rollout-object), задайте в поле *isPackageRollout* значение true, а в поле *packageRolloutPercentage* задайте процент пользователей вашего приложения, которые должны получить обновленные пакеты.
  3. Передайте обновленные данные отправки тестового пакета методу [обновления отправки тестового пакета](update-a-flight-submission.md).

После включения постепенного выпуска пакета для отправки тестового пакета можно использовать следующие методы, чтобы программным образом получать, обновлять, останавливать или завершать постепенный выпуск.

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
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/packagerollout</td>
<td align="left"><a href="get-package-rollout-info-for-a-flight-submission.md">Получение сведений об постепенной отправке пакета</a></td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/updatepackagerolloutpercentage</td>
<td align="left"><a href="update-the-package-rollout-percentage-for-a-flight-submission.md">Обновить процент постепенного развертывания для отправки рейса пакета</a></td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/haltpackagerollout</td>
<td align="left"><a href="halt-the-package-rollout-for-a-flight-submission.md">Прекращение постепенного развертывания для отправки пакета</a></td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/finalizepackagerollout</td>
<td align="left"><a href="finalize-the-package-rollout-for-a-flight-submission.md">Завершение постепенного развертывания для отправки пакета</a></td>
</tr>
</tbody>
</table>

<span/>

## <a name="data-resources"></a>Ресурсы данных

Для управления отправками тестовых пакетов методы API отправки в Microsoft Store используют следующие ресурсы данных JSON.

<span id="flight-submission-object" />

### <a name="flight-submission-resource"></a>Ресурс отправки тестируемой возможности

Этот ресурс описывает отправку тестового пакета.

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
  "fileUploadUrl": "https://productingestionbin1.blob.core.windows.net/ingestion/8b389577-5d5e-4cbe-a744-1ff2e97a9eb8?sv=2014-02-14&sr=b&sig=wgMCQPjPDkuuxNLkeG35rfHaMToebCxBNMPw7WABdXU%3D&se=2016-06-17T21:29:44Z&sp=rwl",
  "targetPublishMode": "Immediate",
  "targetPublishDate": "",
  "notesForCertification": "No special steps are required for certification of this app."
}
```

У этого ресурса есть следующие значения.

| Значение      | Тип   | Описание              |
|------------|--------|------------------------------|
| id            | Строка  | Идентификатор для отправки.  |
| flightId           | Строка  |  Идентификатор тестового пакета, с которым связана отправка.  |  
| status           | Строка  | Состояние отправки. Может принимать одно из следующих значений. <ul><li>None</li><li>Canceled (Отменено)</li><li>PendingCommit (Ожидание фиксации)</li><li>CommitStarted (Фиксация запущена)</li><li>CommitFailed (Сбой фиксации)</li><li>PendingPublication (Ожидание публикации)</li><li>Publishing (Выполняется публикация)</li><li>Published (Опубликовано)</li><li>PublishFailed (Сбой публикации)</li><li>PreProcessing (Предварительная обработка)</li><li>PreProcessingFailed (Сбой предварительной обработки)</li><li>Certification (Сертификация)</li><li>CertificationFailed (Сбой сертификации)</li><li>Выпуск</li><li>ReleaseFailed (Сбой выпуска)</li></ul>   |
| statusDetails           | object  |  [Ресурс сведений о состоянии](#status-details-object), который содержит дополнительные сведения о состоянии отправки, включая сведения об ошибках.  |
| flightPackages           | Массив  | Содержит [ресурсы тестового пакета](#flight-package-object), которые предоставляют сведения о каждом пакете в отправке.   |
| packageDeliveryOptions    | object  | [Ресурс параметров доставки пакета](#package-delivery-options-object), который содержит постепенный выпуск пакета и обязательные параметры обновления для отправки.   |
| fileUploadUrl           | Строка  | URI подписанного URL-адреса (SAS) для передачи пакетов для отправки. При добавлении новых пакетов для отправки выложите ZIP-архив, содержащий пакеты, по этому URI. Дополнительные сведения см. в разделе [Создание отправки тестового пакета](#create-a-package-flight-submission).  |
| targetPublishMode           | Строка  | Режим публикации для отправки. Может принимать одно из следующих значений. <ul><li>Immediate (Незамедлительно)</li><li>Manual (Вручную)</li><li>SpecificDate (Указанная дата)</li></ul> |
| targetPublishDate           | Строка  | Дата публикации отправки в формате ISO 8601, если для *targetPublishMode* задано значение SpecificDate.  |
| notesForCertification           | Строка  |  Предоставляет дополнительные сведения для тест-инженеров сертификации, такие как учетные данные тестовой учетной записи и действия, которые требуется выполнить для доступа к функциям их проверки. Дополнительные сведения см. в разделе [Заметки по сертификации](https://docs.microsoft.com/windows/uwp/publish/notes-for-certification). |

<span id="status-details-object" />

### <a name="status-details-resource"></a>Ресурс сведений о состоянии

Этот ресурс содержит дополнительные сведения о состоянии отправки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание                   |
|-----------------|---------|------|
|  errors               |    object     |   Массив [ресурсов сведений о состоянии](#status-detail-object), который содержит информацию об ошибках для отправки.   |     
|  warnings               |   object      | Массив [ресурсов сведений о состоянии](#status-detail-object), который содержит информацию о предупреждениях для отправки.     |
|  certificationReports               |     object    |   Массив [ресурсов отчета сертификации](#certification-report-object), который предоставляет доступ к данным отчета о сертификации для отправки. В случае сбоя сертификации можно проверить эти отчеты для получения дополнительной информации.    |  


<span id="status-detail-object" />

### <a name="status-detail-resource"></a>Ресурс сведений о состоянии

Этот ресурс содержит дополнительные сведения обо всех связанных ошибках или предупреждениях для отправки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание       |
|-----------------|---------|------|
|  code               |    Строка     |   [Код состояния отправки](#submission-status-code), описывающий тип ошибки или предупреждения. |  
|  details               |     Строка    |  Сообщение с дополнительными сведениями о проблеме.     |


<span id="certification-report-object" />

### <a name="certification-report-resource"></a>Ресурс отчета о сертификации

Этот ресурс предоставляет доступ к данным отчета о сертификации для отправки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание         |
|-----------------|---------|------|
|     date            |    Строка     |  Дата и время создания отчета в формате ISO 8601.    |
|     reportUrl            |    Строка     |  URL-адрес, по которому можно получить доступ к отчету.    |


<span id="flight-package-object" />

### <a name="flight-package-resource"></a>Ресурс тестового пакета

Этот ресурс содержит сведения о пакете в отправке.

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

У этого ресурса есть следующие значения.

> [!NOTE]
> При вызове метода для [обновления отправки тестового пакета](update-a-flight-submission.md) в тексте запроса требуются только значения *fileName*, *fileStatus*, *minimumDirectXVersion* и *minimumSystemRam* этого объекта. Другие значения заполняются центром партнеров.

| Значение           | Тип    | Описание              |
|-----------------|---------|------|
| fileName   |   Строка      |  Имя пакета.    |  
| fileStatus    | Строка    |  Состояние пакета. Может принимать одно из следующих значений. <ul><li>None</li><li>PendingUpload (Ожидает передачи)</li><li>Uploaded (Передан)</li><li>PendingDelete (Ожидает удаления)</li></ul>    |  
| id    |  Строка   |  Идентификатор, который уникально идентифицирует пакет. Это значение используется в центре партнеров.   |     
| version    |  Строка   |  Версия пакета приложения. Дополнительные сведения см. в разделе [Нумерация версий пакета](https://docs.microsoft.com/windows/uwp/publish/package-version-numbering).   |   
| architecture    |  Строка   |  Архитектура пакета приложения (например, ARM).   |     
| languages    | Массив    |  Массив кодов языков, которые поддерживает приложение. Дополнительные сведения см. в разделе [Поддерживаемые языки](https://docs.microsoft.com/windows/uwp/publish/supported-languages).    |     
| capabilities    |  Массив   |  Массив возможностей, необходимых для этого пакета. Дополнительные сведения о возможностях см. в разделе [Объявления возможностей приложения](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).   |     
| minimumDirectXVersion    |  Строка   |  Минимальная версия DirectX, поддерживаемая пакетом приложения. Может задаваться только для приложений, предназначенных для Windows 8.x; игнорируется для приложений, предназначенных для других версий. Может принимать одно из следующих значений. <ul><li>None</li><li>DirectX93</li><li>DirectX100</li></ul>   |     
| minimumSystemRam    | Строка    |  Минимальный объем ОЗУ, необходимый для пакета приложения. Может задаваться только для приложений, предназначенных для Windows 8.x; игнорируется для приложений, предназначенных для других версий. Может принимать одно из следующих значений. <ul><li>None</li><li>Memory2GB</li></ul>   |    


<span id="package-delivery-options-object" />

### <a name="package-delivery-options-resource"></a>Ресурс параметров доставки пакета

Этот ресурс содержит постепенный выпуск пакета и обязательные параметры обновления для отправки.

```json
{
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
}
```

У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
| packageRollout   |   object      |   [Ресурс выпуска пакета](#package-rollout-object), который содержит параметры постепенного выпуска пакета для отправки.    |  
| isMandatoryUpdate    | Логический    |  Указывает, следует ли рассматривать пакеты в этой отправке как обязательные для самоустанавливающихся обновлений приложений. Дополнительные сведения об обязательных пакетах самоустанавливаемых обновлений приложений см. в разделе [Загрузка и установка обновлений пакетов для приложения](../packaging/self-install-package-updates.md).    |  
| mandatoryUpdateEffectiveDate    |  date   |  Дата и время, когда пакеты в этой отправке станут обязательными, в формате ISO 8601 и по часовому поясу UTC.   |        

<span id="package-rollout-object" />

### <a name="package-rollout-resource"></a>Ресурс выпуска пакета

Этот ресурс содержит [параметры постепенного выпуска пакета](#manage-gradual-package-rollout) для отправки. У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|------|
| isPackageRollout   |   Логический      |  Указывает, включен ли постепенный выпуск пакета для этой отправки.    |  
| packageRolloutPercentage    | float    |  Процент пользователей, которые будут получать пакеты при постепенном выпуске.    |  
| packageRolloutStatus    |  Строка   |  Одна из следующих строк, указывающая состояние постепенного выпуска пакета: <ul><li>PackageRolloutNotStarted</li><li>PackageRolloutInProgress</li><li>PackageRolloutComplete</li><li>PackageRolloutStopped</li></ul>  |  
| fallbackSubmissionId    |  Строка   |  Идентификатор отправки, который будет поступать от пользователей, которые не получают пакеты постепенного выпуска.   |          

> [!NOTE]
> Значения *паккажероллаутстатус* и *Фаллбакксубмиссионид* назначаются центром партнеров и не предназначены для установки разработчиком. Если эти значения содержатся в теле запроса, они будут проигнорированы.

<span/>

## <a name="enums"></a>перечислениям;

Эти методы используют следующие перечисления.

<span id="submission-status-code" />

### <a name="submission-status-code"></a>Код состояния отправки

Следующие коды представляют состояние отправки.

| Код           |  Описание      |
|-----------------|---------------|
|  None            |     Код не указан.         |     
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
| Другое  | Отправка находится в неизвестном состоянии или состоянии, не отнесенном ни к одной из категорий. |
| PackageValidationWarning | В процессе проверки пакета создано предупреждение. |

<span/>

## <a name="related-topics"></a>См. также

* [Создание отправок и управление ими с помощью служб Microsoft Store Services](create-and-manage-submissions-using-windows-store-services.md)
* [Управление рейсами пакетов с помощью API отправки Microsoft Store](manage-flights.md)
* [Получение пакета передачи рейса](get-a-flight-submission.md)
* [Создание пакета отправки авиабилетов](create-a-flight-submission.md)
* [Обновление отправки рейса пакета](update-a-flight-submission.md)
* [Фиксация отправки пакета](commit-a-flight-submission.md)
* [Удаление пакета передача рейса](delete-a-flight-submission.md)
* [Получение состояния отправки рейса пакета](get-status-for-a-flight-submission.md)
