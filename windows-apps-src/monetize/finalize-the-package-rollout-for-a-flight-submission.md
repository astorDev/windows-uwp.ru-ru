---
description: Используйте этот метод в API отправки в Microsoft Store, чтобы завершить выпуск пакета с целью отправки тестового пакета.
title: Завершение выпуска для отправки тестируемой возможности
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, выпуск пакета, отправка тестируемой возможности, завершение
ms.assetid: e4a645f6-1f00-4af5-80d6-d2ee179acc8a
ms.localizationpriority: medium
ms.openlocfilehash: c7a2d0c2f5ae938d10c1b82bd74da447b510afe7
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371793"
---
# <a name="finalize-the-rollout-for-a-flight-submission"></a>Завершение выпуска для отправки тестируемой возможности


Используйте этот метод в API отправки в Microsoft Store, чтобы [завершить выпуск пакета](../publish/gradual-package-rollout.md#completing-the-rollout) с целью отправки тестового пакета. Подробнее о процессе создания отправки тестового пакета с помощью API отправки в Microsoft Store см. в разделе [Управление отправкой тестового пакета](manage-flight-submissions.md).


## <a name="prerequisites"></a>предварительные требования

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.
* Создание отправки для приложения в центре партнеров. Это можно сделать в центре партнеров, или это можно сделать с помощью [создать Отправка приложения](create-an-app-submission.md) метод.
* Включите постепенный выпуск пакета для отправки. Это можно сделать [центра партнеров](../publish/gradual-package-rollout.md), или это можно сделать [с помощью API отправки Microsoft Store](manage-flight-submissions.md#manage-gradual-package-rollout).

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание заголовка и параметров запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| ПОМЕСТИТЬ   | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/finalizepackagerollout` |


### <a name="request-header"></a>Заголовок запроса

| Header        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | строка | Обязательный. Код продукта в Магазине для приложения, содержащего отправку тестового пакета, для которого требуется завершить выпуск пакета. Подробнее о коде продукта в Магазине см. в статье [Просмотр сведений об идентификации приложений](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| flightId | строка | Обязательный. Код продукта в Магазине для тестового пакета, содержащего отправку тестового пакета, для которого требуется завершить выпуск пакета. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight.md) и [получение тестовых пакетов для приложения](get-flights-for-an-app.md). Для рейсов, который был создан в центре партнеров этот идентификатор также доступен в URL-адрес для страницы рейсов в центре партнеров. |
| submissionId | строка | Обязательный. Идентификатор отправки с выпуском пакета, который необходимо завершить. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight-submission.md). Для отправки, который был создан в центре партнеров этот идентификатор также доступна в URL-АДРЕСЕ для отправки страницы в центре партнеров.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

Следующий пример демонстрирует завершение выпуска пакета для отправки тестового пакета.

```json
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
| 409  | Этот код указывает на одну из следующих ошибок.<br/><br/><ul><li>Отправка не находится в допустимом состоянии для операции постепенного выпуска (перед вызовом этого метода отправка должна быть опубликована, и значение [packageRolloutStatus](manage-flight-submissions.md#package-rollout-object) должно быть равно **PackageRolloutInProgress**).</li><li>Отправка не относится к указанному приложению.</li><li>Приложение использует функцию центра партнеров, которая [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported).</li></ul> |   


## <a name="related-topics"></a>См. также

* [Постепенное пакета развертывания](../publish/gradual-package-rollout.md)
* [Управление отправкой пакета рейсов, с помощью API отправки Microsoft Store](manage-flight-submissions.md)
* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
