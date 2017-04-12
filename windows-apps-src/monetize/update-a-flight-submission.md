---
author: mcleanbyron
ms.assetid: 24C5F796-5FB8-4B5D-B428-C3154B3098BD
description: "Используйте этот метод в API отправки Магазина Windows для обновления существующей отправки тестового пакета."
title: "Обновление отправки тестового пакета"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API отправки Магазина Windows, отправка тестируемой возможности, обновление"
ms.openlocfilehash: 89ef86cdf3243322f3d8725e40ef13bf43f31a8e
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="update-a-package-flight-submission"></a>Обновление отправки тестового пакета


Используйте этот метод в API отправки Магазина Windows для обновления существующей отправки тестового пакета. После успешного обновления отправки с помощью этого метода необходимо [зафиксировать отправку](commit-a-flight-submission.md) для проверки и публикации.

Дополнительные сведения об использовании этого метода в процессе создания отправки тестового пакета с помощью API отправки Магазина Windows см. в разделе [Управление отправками тестовых пакетов](manage-flight-submissions.md).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте отправку тестового пакета для приложения в учетной записи центра разработки. Это можно сделать на информационной панели центра разработки или с помощью метода [создания отправки тестового пакета](create-a-flight-submission.md).

>**Примечание.**&nbsp;&nbsp;Этот метод можно использовать только для учетных записей Центра разработки для Windows, у которых есть разрешение на использование API отправки Магазина Windows. Такое разрешение имеется не у всех учетных записей.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| PUT    | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions{submissionId}``` |

<span/>
 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательное. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/>

### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | Строка | Обязательный. Код продукта в Магазине для приложения, отправку тестового пакета которого необходимо обновить. Подробнее о коде продукта в Магазине см. в статье[Просмотр сведений об идентификации приложений](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| flightId | Строка | Обязательный. Идентификатор тестового пакета, отправку которого необходимо обновить. Этот идентификатор отображается на информационной панели Центра разработки, а также включается в данные ответов для запросов на [создание тестового пакета](create-a-flight.md) и [получение тестовых пакетов для приложения](get-flights-for-an-app.md).  |
| submissionId | Строка | Обязательный. Идентификатор отправки для обновления. Этот идентификатор отображается на информационной панели Центра разработки, а также включается в данные ответов для запросов на [создание отправки тестового пакета](create-a-flight-submission.md).  |

<span/>

### <a name="request-body"></a>Текст запроса

Текст запроса содержит следующие параметры.

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| flightPackages           | массив  | Содержит объекты, предоставляющие сведения о каждом пакете в отправке. Дополнительные сведения о значениях в тексте ответа см. в разделе [Ресурс тестового пакета](manage-flight-submissions.md#flight-package-object). При вызове этого метода для обновления отправки приложения в теле запроса должны присутствовать только значения *fileName*, *fileStatus*, *minimumDirectXVersion* и *minimumSystemRam* этих объектов. Остальные значения заполняются Центром разработки. |
| packageDeliveryOptions    | объект  | Содержит параметры постепенного выпуска пакета и обязательного обновления для отправки. Подробнее см. в разделе [Объект параметров доставки пакета](manage-flight-submissions.md#package-delivery-options-object).  |
| targetPublishMode           | Строка  | Режим публикации для отправки. Может принимать одно из следующих значений. <ul><li>Immediate (Незамедлительно)</li><li>Manual (Вручную)</li><li>SpecificDate (Указанная дата)</li></ul> |
| targetPublishDate           | строка  | Дата публикации отправки в формате ISO 8601, если для *targetPublishMode* задано значение SpecificDate.  |
| notesForCertification           | строка  |  Предоставляет дополнительные сведения для тест-инженеров сертификации, такие как учетные данные тестовой учетной записи и действия, которые требуется выполнить для доступа к функциям их проверки. Дополнительные сведения см. в разделе [Заметки по сертификации](https://msdn.microsoft.com/windows/uwp/publish/notes-for-certification). |

<span/>

### <a name="request-example"></a>Пример запроса

В следующем примере показано, как обновить отправку тестового пакета для приложения.

```json
PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243649 HTTP/1.1
Authorization: Bearer <your access token>
Content-Type: application/json
{
  "flightPackages": [
    {
      "fileName": "newPackage.appx",
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
  "targetPublishMode": "Immediate",
  "targetPublishDate": "",
  "notesForCertification": "No special steps are required for certification of this app."
}
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. В теле ответа содержатся сведения об обновленной отправке. Дополнительные сведения о значениях в тексте ответа см. в разделе [Ресурс отправки тестового пакета](manage-flight-submissions.md#flight-submission-object).

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

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 400  | Не удалось обновить отправку тестового пакета. Недопустимый запрос. |
| 409  | Не удалось обновить отправку тестового пакета из-за текущего состояния приложения или в связи с тем, что приложение использует компонент информационной панели Центра разработки, [который в настоящее время не поддерживается API отправки Магазина Windows](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   

<span/>


## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправками тестового пакета](manage-flight-submissions.md)
* [Получение отправки тестового пакета](get-a-flight-submission.md)
* [Создание отправки тестового пакета](create-a-flight-submission.md)
* [Подтверждение отправки тестового пакета](commit-a-flight-submission.md)
* [Удаление отправки тестового пакета](delete-a-flight-submission.md)
* [Получение состояния отправки тестового пакета](get-status-for-a-flight-submission.md)
