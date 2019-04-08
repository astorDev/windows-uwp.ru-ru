---
description: Используйте этот метод в API отправки в Microsoft Store, чтобы завершить выпуск пакета для отправки приложения.
title: Завершение выпуска для отправки приложения
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, выпуск пакета, отправка приложения, завершение
ms.assetid: c7dd39e6-5162-455a-b03b-1ed76bffcf6e
ms.localizationpriority: medium
ms.openlocfilehash: c8fe211268190ac269018a6bd47acb4b824d2075
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57607889"
---
# <a name="finalize-the-rollout-for-an-app-submission"></a>Завершение выпуска для отправки приложения


Используйте этот метод в API отправки в Microsoft Store, чтобы [завершить выпуск пакета](../publish/gradual-package-rollout.md#completing-the-rollout) для отправки приложения. Подробнее о процессе создания отправки приложения с помощью API отправки в Microsoft Store см. в статье [Управление отправками приложений](manage-app-submissions.md).

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.
* Создайте такие данные для приложения в учетную запись центра партнеров. Это можно сделать в центре партнеров, или это можно сделать с помощью [создать Отправка приложения](create-an-app-submission.md) метод.
* Включите постепенный выпуск пакета для отправки. Это можно сделать [в центре партнеров](../publish/gradual-package-rollout.md), или это можно сделать [с помощью API отправки Microsoft Store](manage-app-submissions.md#manage-gradual-package-rollout).

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание заголовка и параметров запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| POST   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/finalizepackagerollout``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | Строка | Обязательный. Код продукта в Магазине для приложения, содержащего отправку с выпуском пакета, который требуется завершить. Подробнее о коде продукта в Магазине см. в статье [Просмотр сведений об идентификации приложений](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| submissionId | Строка | Обязательный. Идентификатор отправки с выпуском пакета, который необходимо завершить. Этот идентификатор добавляется в данные ответов для запросов на [создание отправки приложения](create-an-app-submission.md). Для отправки, который был создан в центре партнеров этот идентификатор также доступна в URL-АДРЕСЕ для отправки страницы в центре партнеров.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

Следующий пример демонстрирует завершение выпуска пакета для отправки тестового пакета.

```
POST https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/submissions/1152921504621243680/finalizepackagerollout HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. Подробнее о значениях в тексте ответа см. в описании [ресурса «Выпуск пакета»](manage-app-submissions.md#package-rollout-object).

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
| 404  | Не удалось найти отправку. |
| 409  | Этот код указывает на одну из следующих ошибок.<br/><br/><ul><li>Отправка не находится в допустимом состоянии для операции постепенного выпуска (перед вызовом этого метода отправка должна быть опубликована, и значение [packageRolloutStatus](manage-app-submissions.md#package-rollout-object) должно быть равно **PackageRolloutInProgress**).</li><li>Отправка не относится к указанному приложению.</li><li>Приложение использует функцию DevPartner Center, которая [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported).</li></ul> |   


## <a name="related-topics"></a>Статьи по теме

* [Постепенное пакета развертывания](../publish/gradual-package-rollout.md)
* [Управление отправки приложений, с помощью API отправки Microsoft Store](manage-app-submissions.md)
* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
