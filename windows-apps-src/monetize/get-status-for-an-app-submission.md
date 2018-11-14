---
author: Xansky
ms.assetid: 039B8810-5C9E-4DB9-A6AF-33E7401311FF
description: Используйте этот метод в API отправки в Microsoft Store для получения сведений о состоянии отправки приложения.
title: Получение состояния отправки приложения
ms.author: mhopkins
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, отправка приложения, состояние
ms.localizationpriority: medium
ms.openlocfilehash: f92911d431579f9408b02680abf1d49f17740903
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6198131"
---
# <a name="get-the-status-of-an-app-submission"></a>Получение состояния отправки приложения

Используйте этот метод в API отправки в Microsoft Store для получения сведений о состоянии отправки приложения. Подробнее о процессе создания отправки приложения с помощью API отправки в Microsoft Store см. в статье [Управление отправками приложений](manage-app-submissions.md).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/status``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | string | Обязательный. Код продукта в Магазине для приложения, содержащего отправку, для которой требуется получить сведения о состоянии. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| submissionId | string | Обязательный. Идентификатор отправки, для которой требуется получить сведения о состоянии. Этот идентификатор добавляется в данные ответов для запросов на [создание отправки приложения](create-an-app-submission.md). Для отправки, которая была создана в центре партнеров этот идентификатор также доступен по URL-адресу страницы отправки в центр партнеров.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

В приведенном ниже примере показано, как получить сведения о состоянии отправки приложения.

```
GET https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/submissions/1152921504621243610/status HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлено тело ответа JSON в случае успешного вызова этого метода. В теле ответа содержатся сведения об указанной отправке. Дополнительные сведения о значениях, которые могут содержаться в теле ответа, см. в следующих разделах.

```json
{
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [],
    "warnings": [],
    "certificationReports": []
  },
}
```

### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| status           | string  | Состояние отправки. Может принимать одно из следующих значений. <ul><li>None (Нет)</li><li>Canceled (Отменено)</li><li>PendingCommit (Ожидание фиксации)</li><li>CommitStarted (Фиксация запущена)</li><li>CommitFailed (Сбой фиксации)</li><li>PendingPublication (Ожидание публикации)</li><li>Publishing (Выполняется публикация)</li><li>Published (Опубликовано)</li><li>PublishFailed (Сбой публикации)</li><li>PreProcessing (Предварительная обработка)</li><li>PreProcessingFailed (Сбой предварительной обработки)</li><li>Certification (Сертификация)</li><li>CertificationFailed (Сбой сертификации)</li><li>Release (Выпуск)</li><li>ReleaseFailed (Сбой выпуска)</li></ul>   |
| statusDetails           | object  |  Содержит дополнительные сведения о состоянии отправки, включая сведения об ошибках. Дополнительные сведения см. в разделе [Ресурс сведений о состоянии](manage-app-submissions.md#status-details-object). |


## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 404  | Не удалось найти отправку. |
| 409  | Приложение использует компонент центра партнеров, [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported).  |


## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Получение отправки приложения](get-an-app-submission.md)
* [Создание отправки приложения](create-an-app-submission.md)
* [Подтверждение отправки приложения](commit-an-app-submission.md)
* [Обновление отправки приложения](update-an-app-submission.md)
* [Удаление отправки приложения](delete-an-app-submission.md)
