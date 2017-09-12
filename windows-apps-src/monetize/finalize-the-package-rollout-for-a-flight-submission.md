---
author: mcleanbyron
description: "Используйте этот метод в API отправки для Магазина Windows, чтобы завершить выпуск пакета с целью отправки тестового пакета."
title: "Завершение выпуска для отправки тестируемой возможности"
ms.author: mcleans
ms.date: 08/03/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API отправки для Магазина Windows, выпуск пакета, отправка тестируемой возможности, завершение"
ms.assetid: e4a645f6-1f00-4af5-80d6-d2ee179acc8a
ms.openlocfilehash: 949c7fbcc7cdab891b1d1fd50a63fb0e8ed8490b
ms.sourcegitcommit: a8e7dc247196eee79b67aaae2b2a4496c54ce253
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/04/2017
---
# <a name="finalize-the-rollout-for-a-flight-submission"></a>Завершение выпуска для отправки тестируемой возможности


Используйте этот метод в API отправки для Магазина Windows, чтобы [завершить выпуск пакета](../publish/gradual-package-rollout.md#completing-the-rollout) с целью отправки тестового пакета. Дополнительные сведения о процессе создания отправки тестового пакета с помощью API отправки Магазина Windows см. в разделе [Управление отправкой тестового пакета](manage-flight-submissions.md).


## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте отправку для приложения в учетной записи Центра разработки. Это можно сделать на информационной панели Центра разработки или с помощью метода [Создание отправки приложения](create-an-app-submission.md).
* Включите постепенный выпуск пакета для отправки. Это можно сделать на [информационной панели Центра разработки](../publish/gradual-package-rollout.md) или [с помощью API отправки Магазина Windows](manage-flight-submissions.md#manage-gradual-package-rollout).

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание заголовка и параметров запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| POST   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/finalizepackagerollout``` |

<span/>
 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/>

### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | Строка | Обязательный. Код продукта в Магазине для приложения, содержащего отправку тестового пакета, для которого требуется завершить выпуск пакета. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| flightId | Строка | Обязательный. Код продукта в Магазине для тестового пакета, содержащего отправку с выпуском пакета, который требуется завершить. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight.md) и [получение тестовых пакетов для приложения](get-flights-for-an-app.md).  |
| submissionId | Строка | Обязательный. Идентификатор отправки с выпуском пакета, который необходимо завершить. Этот идентификатор отображается на информационной панели Центра обработки, а также добавляется в данные ответов для запросов на [создание отправки тестового пакета](create-a-flight-submission.md).  |

<span/>

### <a name="request-body"></a>Текст запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

Следующий пример демонстрирует завершение выпуска пакета для отправки тестового пакета.

```
POST https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243680/finalizepackagerollout HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. Подробнее о значениях в тексте ответа см. в описании [ресурса «Выпуск пакета»](manage-flight-submissions.md#package-rollout-object).

```json
{
    "isPackageRollout": true,
    "packageRolloutPercentage": 100.0,
    "packageRolloutStatus": "PackageRolloutComplete",
    "fallbackSubmissionId": "1212922684621243058"
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 404  | Не удалось найти отправку тестового пакета. |
| 409  | Этот код указывает на одну из следующих ошибок.<br/><br/><ul><li>Отправка не находится в допустимом состоянии для операции постепенного выпуска (перед вызовом этого метода отправка должна быть опубликована, и значение [packageRolloutStatus](manage-flight-submissions.md#package-rollout-object) должно быть равно **PackageRolloutInProgress**).</li><li>Отправка не относится к указанному приложению.</li><li>Приложение использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки Магазина Windows](create-and-manage-submissions-using-windows-store-services.md#not_supported).</li></ul> |   

<span/>


## <a name="related-topics"></a>Статьи по теме

* [Постепенный выпуск пакета](../publish/gradual-package-rollout.md)
* [Управление отправками тестовых пакетов с помощью API отправки Магазина Windows](manage-flight-submissions.md)
* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
