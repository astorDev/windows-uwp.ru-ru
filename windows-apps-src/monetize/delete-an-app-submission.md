---
ms.assetid: 96C090C1-88F8-42E7-AED1-AFA9031E952B
description: Используйте этот метод в API отправки в Microsoft Store для удаления существующей отправки приложения.
title: Удаление отправки приложения
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, отправка приложения, удаление
ms.localizationpriority: medium
ms.openlocfilehash: 5d582f79d57fb8b3648d8c872f700d998a2fec1a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57603419"
---
# <a name="delete-an-app-submission"></a>Удаление отправки приложения

Используйте этот метод в API отправки в Microsoft Store для удаления существующей отправки приложения.

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| DELETE    | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | Строка | Обязательный. Код продукта в Магазине приложения, содержащего отправку, которую требуется удалить. Подробнее о коде продукта в Магазине см. в статье [Просмотр сведений об идентификации приложений](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| submissionId | Строка | Обязательный. Идентификатор отправки для удаления. Этот идентификатор добавляется в данные ответов для запросов на [создание отправки приложения](create-an-app-submission.md). Для отправки, который был создан в центре партнеров этот идентификатор также доступна в URL-АДРЕСЕ для отправки страницы в центре партнеров.  |


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
| 409  | Обнаружен указанный отправки, но его не удалось удалить в ее текущем состоянии или приложение использует функцию центра партнеров, которая [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |


## <a name="related-topics"></a>Статьи по теме

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Получить Отправка приложения](get-an-app-submission.md)
* [Создание, отправка приложения](create-an-app-submission.md)
* [Зафиксировать Отправка приложения](commit-an-app-submission.md)
* [Обновление приложения отправки](update-an-app-submission.md)
* [Получить состояние отправки приложения](get-status-for-an-app-submission.md)
