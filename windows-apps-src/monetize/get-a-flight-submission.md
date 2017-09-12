---
author: mcleanbyron
ms.assetid: A0DFF26B-FE06-459B-ABDC-3EA4FEB7A21E
description: "Используйте этот метод в API отправки Магазина Windows, чтобы получить данные для существующей отправки тестового пакета."
title: "Получение отправки тестового пакета"
ms.author: mcleans
ms.date: 08/03/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API отправки Магазина Windows, отправка тестируемой возможности"
ms.openlocfilehash: 3187c91b0ddd17bee73755273bc1b0531ddddb8e
ms.sourcegitcommit: a8e7dc247196eee79b67aaae2b2a4496c54ce253
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/04/2017
---
# <a name="get-a-package-flight-submission"></a>Получение отправки тестового пакета




Используйте этот метод в API отправки Магазина Windows, чтобы получить данные для существующей отправки тестового пакета. Дополнительные сведения о процессе создания отправки тестового пакета с помощью API отправки Магазина Windows см. в разделе [Управление отправкой тестового пакета](manage-flight-submissions.md).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте отправку тестового пакета для приложения в учетной записи центра разработки. Это можно сделать на информационной панели центра разработки или с помощью метода [создания отправки тестового пакета](create-a-flight-submission.md).

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions{submissionId}``` |

<span/>
 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/>

### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | Строка | Обязательный. Код продукта в Магазине, принадлежащий приложению, содержащему отправку тестового пакета, который требуется получить. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| flightId | Строка | Обязательный. Идентификатор тестового пакета, содержащего отправку, которую требуется получить. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight.md) и [получение тестовых пакетов для приложения](get-flights-for-an-app.md).  |
| submissionId | Строка | Обязательный. Идентификатор отправки, которую требуется получить. Этот идентификатор отображается на информационной панели Центра обработки, а также добавляется в данные ответов для запросов на [создание отправки тестового пакета](create-a-flight-submission.md).  |

<span/>

### <a name="request-body"></a>Текст запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

В приведенном ниже примере показано, как получить отправку тестового пакета для приложения с кодом продукта в Магазине 9WZDNCRD91MD.

```
POST https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243649 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлено тело ответа JSON в случае успешного вызова этого метода. В теле ответа содержатся сведения об указанной отправке. Дополнительные сведения о значениях в теле ответа см. в разделе [Ресурс отправки тестового пакета](manage-flight-submissions.md#flight-submission-object).

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
| 404  | Не удалось найти отправку тестового пакета. |
| 409  | Отправка тестового пакета не принадлежит указанному тестовому пакету, или приложение использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки Магазина Windows](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   

<span/>


## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправкой тестового пакета](manage-flight-submissions.md)
* [Создание отправки тестового пакета](create-a-flight-submission.md)
* [Подтверждение отправки тестового пакета](commit-a-flight-submission.md)
* [Обновление отправки тестового пакета](update-a-flight-submission.md)
* [Удаление отправки тестового пакета](delete-a-flight-submission.md)
