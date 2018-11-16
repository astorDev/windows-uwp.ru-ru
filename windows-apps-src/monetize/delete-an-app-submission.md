---
author: Xansky
ms.assetid: 96C090C1-88F8-42E7-AED1-AFA9031E952B
description: Используйте этот метод в API отправки в Microsoft Store для удаления существующей отправки приложения.
title: Удаление отправки приложения
ms.author: mhopkins
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, отправка приложения, удаление
ms.localizationpriority: medium
ms.openlocfilehash: da2533eb8b6e45e4426a1d25931638466547e01b
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6967998"
---
# <a name="delete-an-app-submission"></a>Удаление отправки приложения

Используйте этот метод в API отправки в Microsoft Store для удаления существующей отправки приложения.

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| УДАЛЕНИЕ    | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | string | Обязательный. Код продукта в Магазине приложения, содержащего отправку, которую требуется удалить. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| submissionId | string | Обязательный. Идентификатор отправки для удаления. Этот идентификатор добавляется в данные ответов для запросов на [создание отправки приложения](create-an-app-submission.md). Для отправки, которая была создана в центре партнеров этот идентификатор также доступен по URL-адресу страницы отправки в центр партнеров.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.


### <a name="request-example"></a>Пример запроса

В следующем примере показано, как удалить отправку приложения.

```
DELETE https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/submissions/1152921504621243610 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В случае успеха этот метод возвращает пустой ответ.

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 400  | Недопустимые параметры запроса. |
| 404  | Не удалось найти указанную отправку. |
| 409  | Указанная Отправка найдена, однако ее не удалось удалить в текущем состоянии или приложение использует компонент центра партнеров, [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |


## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Получение отправки приложения](get-an-app-submission.md)
* [Создание отправки приложения](create-an-app-submission.md)
* [Подтверждение отправки приложения](commit-an-app-submission.md)
* [Обновление отправки приложения](update-an-app-submission.md)
* [Получение состояния отправки приложения](get-status-for-an-app-submission.md)
