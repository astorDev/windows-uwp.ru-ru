---
author: Xansky
description: Используйте этот метод в API отправки в Microsoft Store для получения сведений о выпуске пакета в составе отправки тестового пакета.
title: Получение сведений о выпуске для отправки тестируемой возможности
ms.author: mhopkins
ms.date: 04/17/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, API отправки в Microsoft Store, выпуск пакета, отправка тестируемой возможности
ms.assetid: 397f1b99-2be7-4f65-bcf1-9433a3d496ad
ms.localizationpriority: medium
ms.openlocfilehash: 9dd9a318ffd80d32f6370709c4ca3463ae6d8cc7
ms.sourcegitcommit: e16c9845b52d5bd43fc02bbe92296a9682d96926
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "4955070"
---
# <a name="get-rollout-info-for-a-flight-submission"></a>Получение сведений о выпуске для отправки тестируемой возможности


Используйте этот метод в API отправки в Microsoft Store для получения сведений о [выпуске пакета](../publish/gradual-package-rollout.md) в составе отправки тестового пакета. Подробнее о процессе создания отправки тестового пакета с помощью API отправки в Microsoft Store см. в разделе [Управление отправкой тестового пакета](manage-flight-submissions.md).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте отправку тестового пакета для приложения в учетной записи центра разработки. Это можно сделать на информационной панели центра разработки или с помощью метода [создания отправки тестового пакета](create-a-flight-submission.md).

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание заголовка и параметров запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/packagerollout   ``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | string | Обязательный. Код продукта в Магазине для приложения, содержащего отправку тестового пакета, с информацией о выпуске пакета, которую требуется получить. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| flightId | string | Обязательный. Код продукта в Магазине для тестового пакета, содержащего отправку с информацией о выпуске пакета, которую требуется получить. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight.md) и [получение тестовых пакетов для приложения](get-flights-for-an-app.md). Для тестового пакета, который был создан в информационной панели центра разработки, этот код также доступен по URL-адресу страницы тестового пакета на информационной панели.    |
| submissionId | string | Обязательный. Идентификатор отправки с информацией о выпуске пакета, которую требуется получить. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight-submission.md). Для отправки, которая была создана в информационной панели центра разработки, этот код также доступен по URL-адресу страницы отправки на информационной панели.   |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

Следующий пример демонстрирует получение сведений о выпуске пакета для отправки тестового пакета.

```
GET https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243649/packagerollout HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере показано тело ответа JSON, позволяющего успешно выполнить вызов этого метода для отправки тестового пакета с поддержкой постепенного выпуска пакета. Подробнее о значениях в тексте ответа см. в описании [ресурса "Выпуск пакета"](manage-flight-submissions.md#package-rollout-object).

```json
{
    "isPackageRollout": true,
    "packageRolloutPercentage": 25.0,
    "packageRolloutStatus": "PackageRolloutInProgress",
    "fallbackSubmissionId": "1212922684621243058"
}
```

Если отправка тестового пакета не поддерживает постепенный выпуск пакета, возвращается следующее тело ответа.

```json
{
    "isPackageRollout": false,
    "packageRolloutPercentage": 0.0,
    "packageRolloutStatus": "PackageRolloutNotStarted",
    "fallbackSubmissionId": "0"
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 404  | Не удалось найти отправку тестового пакета. |
| 409  | Отправка тестового пакета не принадлежит указанному тестовому пакету, или приложение использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки в Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>Статьи по теме

* [Постепенный выпуск пакета](../publish/gradual-package-rollout.md)
* [Управление отправками тестовых пакетов с помощью API отправки в Microsoft Store](manage-flight-submissions.md)
* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
