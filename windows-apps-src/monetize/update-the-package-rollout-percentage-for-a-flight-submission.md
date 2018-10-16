---
author: Xansky
description: Используйте этот метод в API отправки Microsoft Store, чтобы изменить процент выпуска пакета для отправки тестового пакета.
title: Обновление процента выпуска для отправки тестируемой возможности
ms.author: mhopkins
ms.date: 04/17/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, API отправки Microsoft Store, выпуск пакета, отправка тестируемой возможности, обновление, процент
ms.assetid: ee9aa223-e945-4c11-b430-1f4b1e559743
ms.localizationpriority: medium
ms.openlocfilehash: 6cd360f4c4aac0e628699e52534ee3e2f42e49d4
ms.sourcegitcommit: 106aec1e59ba41aae2ac00f909b81bf7121a6ef1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "4623783"
---
# <a name="update-the-rollout-percentage-for-a-flight-submission"></a>Обновление процента выпуска для отправки тестируемой возможности


Используйте этот метод в API отправки Microsoft Store, чтобы [изменить процент выпуска](../publish/gradual-package-rollout.md#setting-the-rollout-percentage) для отправки тестового пакета. Подробнее о процессе создания отправки тестового пакета с помощью API отправки в Microsoft Store см. в разделе [Управление отправкой тестового пакета](manage-flight-submissions.md).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте отправку для приложения в учетной записи Центра разработки. Это можно сделать на информационной панели Центра разработки или с помощью метода [Создание отправки приложения](create-an-app-submission.md).
* Включите постепенный выпуск пакета для отправки. Это можно сделать на [информационной панели Центра разработки](../publish/gradual-package-rollout.md) или [с помощью API отправки в Microsoft Store](manage-flight-submissions.md#manage-gradual-package-rollout).

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание заголовка и параметров запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| POST   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/updatepackagerolloutpercentage``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | string | Обязательный. Код продукта в Магазине для приложения, содержащего отправку тестового пакета, для которого требуется изменить процент выпуска пакета. Подробнее о коде продукта в Магазине см. в статье [Просмотр сведений об идентификации приложений](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| flightId | string | Обязательный. Идентификатор тестового пакета, содержащего отправку с процентом выпуска пакета, который требуется изменить. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight.md) и [получение тестовых пакетов для приложения](get-flights-for-an-app.md). Для тестового пакета, который был создан в информационной панели центра разработки, этот код также доступен по URL-адресу страницы тестового пакета на информационной панели.  |
| submissionId | string | Обязательный. Идентификатор отправки с процентом выпуска пакета, который требуется изменить. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight-submission.md). Для отправки, которая была создана в информационной панели центра разработки, этот код также доступен по URL-адресу страницы отправки на информационной панели.  |
| percentage  |  float  |  Обязательный. Процент пользователей, которые получат постепенно выпускаемый пакет.  |


### <a name="request-body"></a>Текст запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

В следующем примере показано, как изменить процент выпуска пакета для отправки тестового пакета.

```
POST https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243680/updatepackagerolloutpercentage?percentage=25 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. Подробнее о значениях в тексте ответа см. в описании [ресурса «Выпуск пакета»](manage-flight-submissions.md#package-rollout-object).

```json
{
    "isPackageRollout": true,
    "packageRolloutPercentage": 25.0,
    "packageRolloutStatus": "PackageRolloutInProgress",
    "fallbackSubmissionId": "1212922684621243058"
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 404  | Не удалось найти отправку тестового пакета. |
| 409  | Этот код указывает на одну из следующих ошибок.<br/><br/><ul><li>Отправка не находится в допустимом состоянии для операции постепенного выпуска (перед вызовом этого метода отправка должна быть опубликована, и значение [packageRolloutStatus](manage-flight-submissions.md#package-rollout-object) должно быть равно **PackageRolloutInProgress**).</li><li>Отправка не относится к указанному приложению.</li><li>Приложение использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки в Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported).</li></ul> |   


## <a name="related-topics"></a>Статьи по теме

* [Постепенный выпуск пакета](../publish/gradual-package-rollout.md)
* [Управление отправками тестовых пакетов с помощью API отправки в Microsoft Store](manage-flight-submissions.md)
* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
