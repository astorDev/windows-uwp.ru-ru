---
author: mcleanbyron
ms.assetid: CD866083-EB7F-4389-A907-FC43DC2FCB5E
description: "Используйте этот метод в API отправки Магазина Windows для создания новой отправки тестового пакета для приложения, которое зарегистрировано в вашей учетной записи Центра разработки для Windows."
title: "Создание отправки тестового пакета"
ms.author: mcleans
ms.date: 08/03/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API отправки Магазина Windows, создание отправки тестируемой возможности"
ms.openlocfilehash: f9d83f6e43e42fdaaf3f5242b461a989e074a9b5
ms.sourcegitcommit: a8e7dc247196eee79b67aaae2b2a4496c54ce253
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/04/2017
---
# <a name="create-a-package-flight-submission"></a>Создание отправки тестового пакета

Используйте этот метод в API отправки Магазина Windows для создания новой отправки тестового пакета для приложения. После успешного создания новой отправки с помощью этого метода [обновите отправку](update-a-flight-submission.md), чтобы внести любые необходимые изменения в данные отправки, а затем [зафиксируйте отправку](commit-a-flight-submission.md) для внедрения и публикации.

Дополнительные сведения об использовании этого метода в процессе создания отправки тестового пакета с помощью API отправки Магазина Windows см. в разделе [Управление отправками тестовых пакетов](manage-flight-submissions.md).

>**Примечание.**&nbsp;&nbsp;При использовании этого метода выполняется создание отправки для имеющегося тестового пакета. Чтобы создать тестовый пакет, используйте метод [создания тестового пакета](create-a-flight.md).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте тестовый пакет для приложения в учетной записи Центра разработки. Это можно сделать на панели мониторинга Центра разработки или с помощью метода [создания тестового пакета](create-a-flight.md).

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| POST    | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions``` |

<span/>
 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/>

### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | Строка | Обязательный. Код продукта в Магазине для приложения, отправку тестового пакета которого необходимо создать. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| flightId | Строка | Обязательный. Идентификатор тестового пакета, для которого необходимо добавить отправку. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight.md) и [получение тестовых пакетов для приложения](get-flights-for-an-app.md).  |

<span/>

### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

В примере кода ниже показано, как создать новую отправку тестового пакета для приложения с кодом продукта 9WZDNCRD91MD в Магазине.

```
POST https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON, обеспечивающий успешный вызов этого метода. В тексте ответа содержатся сведения о новой отправке. Дополнительные сведения о значениях в тексте ответа см. в разделе [Ресурс отправки тестового пакета](manage-flight-submissions.md#flight-submission-object).

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
| 400  | Не удалось создать отправку тестового пакета. Недопустимый запрос. |
| 409  | Не удалось создать отправку тестового пакета из-за текущего состояния приложения или в связи с тем, что приложение использует компонент панели мониторинга Центра разработки, [который в настоящее время не поддерживается API отправки Магазина Windows](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   

<span/>

## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправками тестового пакета](manage-flight-submissions.md)
* [Получение отправки тестового пакета](get-a-flight-submission.md)
* [Подтверждение отправки тестового пакета](commit-a-flight-submission.md)
* [Обновление отправки тестового пакета](update-a-flight-submission.md)
* [Удаление отправки тестового пакета](delete-a-flight-submission.md)
* [Получение состояния отправки тестового пакета](get-status-for-a-flight-submission.md)
