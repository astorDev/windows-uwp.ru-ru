---
ms.assetid: 2A454057-FF14-40D2-8ED2-CEB5F27E0226
description: Используйте эти методы в API отправки Microsoft Store, чтобы управлять отправкой рейсов пакетов для приложений, зарегистрированных в учетной записи центра партнеров.
title: Управление отправкой рейсов пакетов
ms.date: 04/16/2018
ms.topic: article
keywords: API отправки для Windows 10, UWP, Microsoft Store, отправка рейсов
ms.localizationpriority: medium
ms.openlocfilehash: 50596fdadae2ac4a0625687e7c8acaf985ccfaa7
ms.sourcegitcommit: f561efbda5c1d47b85601d91d70d86c5332bbf8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690378"
---
# <a name="manage-package-flight-submissions"></a>Управление отправкой рейсов пакетов

API отправки Microsoft Store предоставляет методы, которые можно использовать для управления отправкой рейсов пакетов для приложений, включая постепенное развертывания пакетов. Общие сведения об API отправки Microsoft Store, включая предварительные требования для использования API, см. в статье [Создание и управление отправкой с помощью служб Microsoft Store Services](create-and-manage-submissions-using-windows-store-services.md).

> [!IMPORTANT]
> Если для создания отправки пакета используется API отправки Microsoft Store, обязательно внесите дальнейшие изменения в отправку только с помощью API, а не из центра партнеров. Если вы используете панель мониторинга для изменения отправки, изначально созданной с помощью API, вы больше не сможете изменить или зафиксировать эту отправку с помощью API. В некоторых случаях отправка может остаться в состоянии ошибки, когда оно не может продолжаться в процессе отправки. Если это происходит, необходимо удалить отправку и создать новую отправку.

<span id="methods-for-package-flight-submissions" />

## <a name="methods-for-managing-package-flight-submissions"></a>Методы управления отправкой рейсов пакетов

Используйте следующие методы для получения, создания, обновления, фиксации или удаления пакета передачи авиабилетов. Перед использованием этих методов рейс должен уже существовать в центре партнеров. Вы можете создать перелет пакета [в центре партнеров](https://docs.microsoft.com/windows/uwp/publish/package-flights) или с помощью методов API отправки Microsoft Store, описанных в разделе [Управление рейсами пакетов](manage-flights.md).

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
<th align="left">Description (Описание)</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">ПОЛУЧЕНИЕ</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}</td>
<td align="left"><a href="get-a-flight-submission.md">Получение существующей отправки рейса пакета</a></td>
</tr>
<tr>
<td align="left">ПОЛУЧЕНИЕ</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/status</td>
<td align="left"><a href="get-status-for-a-flight-submission.md">Получение состояния текущей отправки пакета</a></td>
</tr>
<tr>
<td align="left">ПУБЛИКАЦИЯ</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions</td>
<td align="left"><a href="create-a-flight-submission.md">Создание нового пакета передача рейса</a></td>
</tr>
<tr>
<td align="left">ОТПРАВКА</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}</td>
<td align="left"><a href="update-a-flight-submission.md">Обновление существующей отправки рейса пакета</a></td>
</tr>
<tr>
<td align="left">ПУБЛИКАЦИЯ</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/commit</td>
<td align="left"><a href="commit-a-flight-submission.md">Фиксация новой или обновленной отправки пакета</a></td>
</tr>
<tr>
<td align="left">УДАЛИТЬ</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}</td>
<td align="left"><a href="delete-a-flight-submission.md">Удаление пакета передача рейса</a></td>
</tr>
</tbody>
</table>

<span id="create-a-package-flight-submission">

## <a name="create-a-package-flight-submission"></a>Создание пакета отправки авиабилетов

Чтобы создать отправку для пакета, выполните следующую процедуру.

1. Если вы еще не сделали этого, выполните предварительные требования, описанные в статье [Создание и управление отправкой с помощью Microsoft Store Services](create-and-manage-submissions-using-windows-store-services.md), включая связывание приложения Azure AD с учетной записью центра партнеров и получение идентификатора и ключа клиента. Это необходимо сделать только один раз. После получения идентификатора и ключа клиента их можно повторно использовать в любой момент, когда потребуется создать новый маркер доступа Azure AD.  

2. [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token). Этот маркер доступа необходимо передать методам в API отправки Microsoft Store. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до истечения срока действия. По истечении срока действия маркера можно получить новый.

3. [Создайте пакет отправки рейса](create-a-flight-submission.md) , выполнив следующий метод в API отправки Microsoft Store. Этот метод создает новую выполняющуюся отправку, которая представляет собой копию последней опубликованной отправки.

    ```json
    POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions
    ```

    Текст ответа содержит ресурс [отправки рейсов](#flight-submission-object) , который включает идентификатор новой отправки, URI подписанного URL-адрес (SAS) для отправки пакетов, отправляемых в хранилище BLOB-объектов Azure, и данные для новой отправки (включая все списки и сведения о ценах.

    > [!NOTE]
    > URI SAS обеспечивает доступ к защищенному ресурсу в службе хранилища Azure, не требуя ключей учетной записи. Общие сведения об URI SAS и их использовании с хранилищем BLOB-объектов Azure см. в разделе подписанные URL-адреса [, часть 1. Основные сведения о модели SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) и подписанных URL [-адресах. часть 2. Создание и использование SAS с хранилищем больших двоичных объектов](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/).

4. Если вы добавляете новые пакеты для отправки, [Подготовьте пакеты](https://docs.microsoft.com/windows/uwp/publish/app-package-requirements) и добавьте их в ZIP-архив.

5. Измените данные [отправки рейса](#flight-submission-object) , указав необходимые изменения для новой отправки, и выполните следующий метод, чтобы [Обновить отправку рейса пакета](update-a-flight-submission.md).

    ```json
    PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}
    ```
      > [!NOTE]
      > При добавлении новых пакетов для отправки убедитесь, что вы обновляете данные отправки, чтобы они ссылались на имя и относительный путь этих файлов в ZIP-архиве.

4. Если вы добавляете новые пакеты для отправки, отправьте ZIP-архив в [хранилище BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage) с помощью URI SAS, предоставленного в тексте ответа метода POST, который был вызван ранее. Существуют различные библиотеки Azure, которые можно использовать для этого на различных платформах, в том числе:

    * [Клиентская библиотека хранилища Azure для .NET](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-blobs)
    * [пакет SDK службы хранилища Azure для Java](https://docs.microsoft.com/azure/storage/storage-java-how-to-use-blob-storage)
    * [Пакет SDK службы хранилища Azure для Python](https://docs.microsoft.com/azure/storage/storage-python-how-to-use-blob-storage)

    В следующем C# примере кода показано, как передать ZIP-архив в хранилище больших двоичных объектов Azure с помощью класса [CloudBlockBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob) в клиентской библиотеке службы хранилища Azure для .NET. В этом примере предполагается, что ZIP-архив уже записан в объект потока.

    ```csharp
    string sasUrl = "https://productingestionbin1.blob.core.windows.net/ingestion/26920f66-b592-4439-9a9d-fb0f014902ec?sv=2014-02-14&sr=b&sig=usAN0kNFNnYE2tGQBI%2BARQWejX1Guiz7hdFtRhyK%2Bog%3D&se=2016-06-17T20:45:51Z&sp=rwl";
    Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob blockBob =
        new Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob(new System.Uri(sasUrl));
    await blockBob.UploadFromStreamAsync(stream);
    ```

5. [Зафиксируйте отправку рейса пакета](commit-a-flight-submission.md) , выполнив следующий метод. В результате вы получите оповещение об этом центре партнеров, и теперь ваши обновления должны быть применены к вашей учетной записи.

    ```json
    POST https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/commit
    ```

6. Проверьте состояние фиксации, выполнив следующий метод, чтобы [получить состояние отправки рейса пакета](get-status-for-a-flight-submission.md).

    ```json
    GET https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/status
    ```

    Чтобы подтвердить состояние отправки, проверьте значение *состояния* в тексте ответа. Это значение должно измениться с **коммитстартед** на **предварительную обработку** , если запрос выполнен, или **коммитфаилед** , если в запросе возникли ошибки. При наличии ошибок в поле *статусдетаилс* содержатся дополнительные сведения об ошибке.

7. После успешного завершения фиксации отправка отправляется в хранилище для приема. Вы можете продолжить отслеживать ход отправки, используя предыдущий метод или посетив центр партнеров.

<span/>

## <a name="code-examples"></a>Примеры кода

В следующих статьях приведены подробные примеры кода, демонстрирующие создание пакета передачи авиабилетов на нескольких разных языках программирования:

* [C#примеры кода](csharp-code-examples-for-the-windows-store-submission-api.md)
* [Примеры кода Java](java-code-examples-for-the-windows-store-submission-api.md)
* [Примеры кода Python](python-code-examples-for-the-windows-store-submission-api.md)

## <a name="storebroker-powershell-module"></a>Модуль PowerShell Стореброкер

В качестве альтернативы непосредственному вызову API-интерфейса отправки Microsoft Store мы также предоставляем модуль PowerShell с открытым исходным кодом, который реализует интерфейс командной строки поверх API. Этот модуль называется [стореброкер](https://aka.ms/storebroker). Этот модуль можно использовать для управления отправкой приложения, рейса и надстройки из командной строки вместо прямого вызова API-интерфейса отправки Microsoft Store или можно просто просмотреть источник, чтобы просмотреть дополнительные примеры вызова этого API. Модуль Стореброкер активно используется в корпорации Майкрософт в качестве основного способа, которым в магазин передаются многие приложения из первого производителя.

Дополнительные сведения см. [на странице нашей стореброкер на сайте GitHub](https://aka.ms/storebroker).

<span id="manage-gradual-package-rollout">

## <a name="manage-a-gradual-package-rollout-for-a-package-flight-submission"></a>Управление постепенной выгрузкой пакетов для отправки на печать пакета

Вы можете постепенно развернуть обновленные пакеты в пакете подготовки к передаче в процентах от клиентов приложения в Windows 10. Это позволяет отслеживать Отзывы и аналитические данные о конкретных пакетах, чтобы убедиться, что вы уверены в этом обновлении, прежде чем использовать их более широко. Можно изменить процент развертывания (или остановить обновление) для опубликованной отправки без создания новой отправки. Дополнительные сведения, в том числе инструкции по включению постепенного развертывания пакетов и управлению ими в центре партнеров, см. в [этой статье](../publish/gradual-package-rollout.md).

Чтобы программно включить постепенное развертывание пакета для отправки рейса пакета, выполните эту процедуру с помощью методов в API отправки Microsoft Store:

  1. [Создание пакета для отправки авиабилетов](create-a-flight-submission.md) или [Получение пакета передачи](get-a-flight-submission.md).
  2. В данных ответа выберите ресурс [паккажероллаут](#package-rollout-object) , задайте для поля *испаккажероллаут* значение true, а для поля *паккажероллаутперцентаже* — процент клиентов приложения, которые должны получить обновленные пакеты.
  3. Передайте обновленные данные отправки рейса пакета в метод [отправки пакета обновления](update-a-flight-submission.md) .

После включения постепенного развертывания пакета для отправки рейса пакета можно использовать следующие методы для программного получения, обновления, остановки или завершения постепенного развертывания.

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
<th align="left">Description (Описание)</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">ПОЛУЧЕНИЕ</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/packagerollout</td>
<td align="left"><a href="get-package-rollout-info-for-a-flight-submission.md">Получение сведений об постепенной отправке пакета</a></td>
</tr>
<tr>
<td align="left">ПУБЛИКАЦИЯ</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/updatepackagerolloutpercentage</td>
<td align="left"><a href="update-the-package-rollout-percentage-for-a-flight-submission.md">Обновить процент постепенного развертывания для отправки рейса пакета</a></td>
</tr>
<tr>
<td align="left">ПУБЛИКАЦИЯ</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/haltpackagerollout</td>
<td align="left"><a href="halt-the-package-rollout-for-a-flight-submission.md">Прекращение постепенного развертывания для отправки пакета</a></td>
</tr>
<tr>
<td align="left">ПУБЛИКАЦИЯ</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/finalizepackagerollout</td>
<td align="left"><a href="finalize-the-package-rollout-for-a-flight-submission.md">Завершение постепенного развертывания для отправки пакета</a></td>
</tr>
</tbody>
</table>

<span/>

## <a name="data-resources"></a>Ресурсы данных

Методы API-интерфейса отправки Microsoft Store для управления отправкой рейсов пакетов используют следующие ресурсы данных JSON.

<span id="flight-submission-object" />

### <a name="flight-submission-resource"></a>Ресурс отправки рейса

Этот ресурс описывает отправку рейса пакета.

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

Этот ресурс имеет следующие значения.

| Значение      | Тип   | Description (Описание)              |
|------------|--------|------------------------------|
| id            | строка  | Идентификатор отправки.  |
| флигхтид           | строка  |  Идентификатор пакета, с которым связана отправка.  |  
| status           | строка  | Состояние отправки. Он может принимать одно из перечисленных ниже значений. <ul><li>None</li><li>Canceled</li><li>пендингкоммит</li><li>коммитстартед</li><li>коммитфаилед</li><li>пендингпубликатион</li><li>Публикация</li><li>Опубликовано</li><li>публишфаилед</li><li>Предварительной обработки</li><li>препроцессингфаилед</li><li>Сертификация</li><li>цертификатионфаилед</li><li>Release</li><li>релеасефаилед</li></ul>   |
| статусдетаилс           | object  |  [Ресурс сведений о состоянии](#status-details-object) , содержащий дополнительные сведения о состоянии отправки, включая сведения об ошибках.  |
| флигхтпаккажес           | array  | Содержит [ресурсы пакета рейсов](#flight-package-object) , содержащие сведения о каждом пакете в отправке.   |
| паккажеделиверйоптионс    | object  | [Ресурс параметров доставки пакета](#package-delivery-options-object) , который содержит постепенное развертывание пакета и обязательные параметры обновления для отправки.   |
| филеуплоадурл           | строка  | URI подписанного URL-адрес (SAS) для передачи пакетов для отправки. При добавлении новых пакетов для отправки отправьте ZIP-архив, содержащий пакеты, в этот универсальный код ресурса (URI). Дополнительные сведения см. [в разделе Создание пакета подготовки к передаче](#create-a-package-flight-submission).  |
| таржетпублишмоде           | строка  | Режим публикации для отправки. Он может принимать одно из перечисленных ниже значений. <ul><li>Немедленно</li><li>Руководство</li><li>спеЦификдате</li></ul> |
| таржетпублишдате           | строка  | Дата публикации для отправки в формате ISO 8601, если для *таржетпублишмоде* задано значение спеЦификдате.  |
| нотесфорцертификатион           | строка  |  Предоставляет дополнительные сведения для тестировщиков сертификации, таких как проверка учетных данных учетной записи и действия для доступа и проверки компонентов. Дополнительные сведения см. в разделе [Примечания для сертификации](https://docs.microsoft.com/windows/uwp/publish/notes-for-certification). |

<span id="status-details-object" />

### <a name="status-details-resource"></a>Ресурс сведений о состоянии

Этот ресурс содержит дополнительные сведения о состоянии отправки. Этот ресурс имеет следующие значения.

| Значение           | Тип    | Description (Описание)                   |
|-----------------|---------|------|
|  errors               |    object     |   Массив [подробных ресурсов состояния](#status-detail-object) , содержащих сведения об ошибке для отправки.   |     
|  Предупреждения               |   object      | Массив [подробных ресурсов состояния](#status-detail-object) , содержащих сведения о предупреждениях для отправки.     |
|  цертификатионрепортс               |     object    |   Массив [ресурсов отчета о сертификации](#certification-report-object) , предоставляющий доступ к данным отчета о сертификации для отправки. Вы можете просмотреть эти отчеты, чтобы получить дополнительные сведения в случае сбоя сертификации.    |  


<span id="status-detail-object" />

### <a name="status-detail-resource"></a>Подробный ресурс состояния

Этот ресурс содержит дополнительные сведения о любых связанных ошибках или предупреждениях для отправки. Этот ресурс имеет следующие значения.

| Значение           | Тип    | Description (Описание)       |
|-----------------|---------|------|
|  Код               |    строка     |   [Код состояния отправки](#submission-status-code) , описывающий тип ошибки или предупреждения. |  
|  сведения               |     строка    |  Сообщение с дополнительными сведениями о данной ошибке.     |


<span id="certification-report-object" />

### <a name="certification-report-resource"></a>Ресурс отчета о сертификации

Этот ресурс предоставляет доступ к данным отчета о сертификации для отправки. Этот ресурс имеет следующие значения.

| Значение           | Тип    | Description (Описание)         |
|-----------------|---------|------|
|     дата            |    строка     |  Дата и время создания отчета в формате ISO 8601.    |
|     репортурл            |    строка     |  URL-адрес, по которому можно получить доступ к отчету.    |


<span id="flight-package-object" />

### <a name="flight-package-resource"></a>Ресурс пакета рейсов

Этот ресурс предоставляет сведения о пакете в отправке.

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

Этот ресурс имеет следующие значения.

> [!NOTE]
> При вызове метода [Update пакета передачи рейса](update-a-flight-submission.md) в тексте запроса требуются только значения *filename*, *филестатус*, *минимумдиректксверсион*и *минимумсистемрам* этого объекта. Другие значения заполняются центром партнеров.

| Значение           | Тип    | Description (Описание)              |
|-----------------|---------|------|
| fileName   |   строка      |  Имя пакета.    |  
| филестатус    | строка    |  Состояние пакета. Он может принимать одно из перечисленных ниже значений. <ul><li>None</li><li>пендингуплоад</li><li>Отправка</li><li>PendingDelete</li></ul>    |  
| id    |  строка   |  Идентификатор, который однозначно идентифицирует пакет. Это значение используется в центре партнеров.   |     
| версия    |  строка   |  Версия пакета приложения. Дополнительные сведения см. в разделе [Нумерация версий пакета](https://docs.microsoft.com/windows/uwp/publish/package-version-numbering).   |   
| архитектура    |  строка   |  Архитектура пакета приложения (например, ARM).   |     
| языки    | array    |  Массив кодов языков для языков, поддерживаемых приложением. Дополнительные сведения см. в разделе [Поддерживаемые языки](https://docs.microsoft.com/windows/uwp/publish/supported-languages).    |     
| capabilities    |  array   |  Массив возможностей, необходимых для пакета. Дополнительные сведения о возможностях см. в статье [объявления возможностей приложений](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).   |     
| минимумдиректксверсион    |  строка   |  Минимальная версия DirectX, поддерживаемая пакетом приложения. Это значение можно задать только для приложений, предназначенных для Windows 8. x; он игнорируется для приложений, предназначенных для других версий. Он может принимать одно из перечисленных ниже значений. <ul><li>None</li><li>DirectX93</li><li>DirectX100</li></ul>   |     
| минимумсистемрам    | строка    |  Минимальный объем ОЗУ, необходимый для пакета приложения. Это значение можно задать только для приложений, предназначенных для Windows 8. x; он игнорируется для приложений, предназначенных для других версий. Он может принимать одно из перечисленных ниже значений. <ul><li>None</li><li>Memory2GB</li></ul>   |    


<span id="package-delivery-options-object" />

### <a name="package-delivery-options-resource"></a>Ресурс параметров доставки пакета

Этот ресурс содержит постепенное развертывание пакета и обязательные параметры обновления для отправки.

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

Этот ресурс имеет следующие значения.

| Значение           | Тип    | Description (Описание)        |
|-----------------|---------|------|
| паккажероллаут   |   object      |   [Ресурс выпуска пакета](#package-rollout-object) , содержащий параметры постепенного развертывания пакета для отправки.    |  
| исмандаторюпдате    | Логическое    |  Указывает, следует ли обрабатывать пакеты в этой отправке как обязательные для самостоятельной установки обновлений приложения. Дополнительные сведения о обязательных пакетах для самостоятельной установки обновлений приложений см. [в статье скачивание и установка обновлений пакетов для приложения](../packaging/self-install-package-updates.md).    |  
| мандаторюпдатиффективедате    |  дата   |  Дата и время, когда пакеты в этой отправке становятся обязательными, в формате ISO 8601 и часовом поясе UTC.   |        

<span id="package-rollout-object" />

### <a name="package-rollout-resource"></a>Ресурс развертывания пакета

Этот ресурс содержит параметры постепенного [развертывания пакета](#manage-gradual-package-rollout) для отправки. Этот ресурс имеет следующие значения.

| Значение           | Тип    | Description (Описание)        |
|-----------------|---------|------|
| испаккажероллаут   |   Логическое      |  Указывает, включен ли постепенный выпуск пакетов для отправки.    |  
| паккажероллаутперцентаже    | float;    |  Процент пользователей, которые будут принимать пакеты в постепенном выходе.    |  
| паккажероллаутстатус    |  строка   |  Одна из следующих строк, указывающая состояние развертывания постепенного пакета: <ul><li>паккажероллаутнотстартед</li><li>паккажероллаутинпрогресс</li><li>паккажероллауткомплете</li><li>паккажероллаутстоппед</li></ul>  |  
| фаллбакксубмиссионид    |  строка   |  Идентификатор отправки, который будет получен клиентами, не получившими постепенных пакетов развертывания.   |          

> [!NOTE]
> Значения *паккажероллаутстатус* и *Фаллбакксубмиссионид* назначаются центром партнеров и не предназначены для установки разработчиком. Если включить эти значения в текст запроса, эти значения будут проигнорированы.

<span/>

## <a name="enums"></a>Перечисления

Эти методы используют следующие перечисления.

<span id="submission-status-code" />

### <a name="submission-status-code"></a>Код состояния отправки

Следующие коды представляют состояние отправки.

| Код           |  Description (Описание)      |
|-----------------|---------------|
|  None            |     Код не указан.         |     
|      инвалидарчиве        |     ZIP-архив, содержащий пакет, недопустим или имеет неизвестный формат архива.  |
| MissingFiles | В ZIP-архиве отсутствуют все файлы, указанные в данных отправки, или они находятся в неправильном месте в архиве. |
| паккажевалидатионфаилед | Не удалось проверить один или несколько пакетов в отправлении. |
| инвалидпараметервалуе | Один из параметров в тексте запроса недопустим. |
| InvalidOperation | Предпринята недопустимая операция. |
| инвалидстате | Предпринятая операция недопустима для текущего состояния рейса пакета. |
| ResourceNotFound | Не удалось найти указанный рейс пакета. |
| ServiceError | Произошла внутренняя ошибка службы, препятствующая выполнению запроса. Повторите запрос. |
| листингоптаутварнинг | Разработчик удалил список из предыдущей отправки или не включал сведения о листинге, поддерживаемые пакетом. |
| листингоптинварнинг  | Разработчик добавил список. |
| упдатеонливарнинг | Разработчик пытается вставить что-то, которое поддерживает только обновление. |
| Другие  | Отправка находится в неизвестном или неклассифицированном состоянии. |
| паккажевалидатионварнинг | Процесс проверки пакета привел к появлению предупреждения. |

<span/>

## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Microsoft Store Services](create-and-manage-submissions-using-windows-store-services.md)
* [Управление рейсами пакетов с помощью API отправки Microsoft Store](manage-flights.md)
* [Получение пакета передачи рейса](get-a-flight-submission.md)
* [Создание пакета отправки авиабилетов](create-a-flight-submission.md)
* [Обновление отправки рейса пакета](update-a-flight-submission.md)
* [Фиксация отправки пакета](commit-a-flight-submission.md)
* [Удаление пакета передача рейса](delete-a-flight-submission.md)
* [Получение состояния отправки рейса пакета](get-status-for-a-flight-submission.md)
