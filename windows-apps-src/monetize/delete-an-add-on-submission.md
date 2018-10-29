---
author: Xansky
ms.assetid: D677E126-C3D6-46B6-87A5-6237EBEDF1A9
description: Используйте этот метод в API отправки в Microsoft Store для удаления существующей отправки надстройки.
title: Удаление отправки надстройки
ms.author: mhopkins
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, отправка надстройки, удаление, продукт внутри приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: 4c5d9e905f6b3d8acffc53e943d946f6ac7c3c68
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5740018"
---
# <a name="delete-an-add-on-submission"></a>Удаление отправки надстройки

Используйте этот метод в API отправки в Microsoft Store, чтобы удалить существующую отправку надстройки (также называется продуктом внутри приложения или IAP).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| УДАЛЕНИЕ    | ```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}/submissions/{submissionId}``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| inAppProductId | string | Обязательный. Код продукта в Магазине надстройки, содержащей отправку, которую требуется удалить. Код продукта в Магазине отображается на информационной панели Центра разработки.  |
| submissionId | string | Обязательный. Идентификатор отправки для удаления. Этот идентификатор добавляется в данные ответов для запросов на [создание отправки надстройки](create-an-add-on-submission.md). Для отправки, которая была создана в информационной панели центра разработки, этот код также доступен по URL-адресу страницы отправки на информационной панели.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.


### <a name="request-example"></a>Пример запроса

В следующем примере показано, как удалить отправку надстройки.

```
DELETE https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/9NBLGGH4TNMP/submissions/1152921504621230023 HTTP/1.1
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
| 409  | Указанная отправка найдена, однако ее не удалось удалить в текущем состоянии или надстройка использует функцию панели мониторинга Центра разработки, которую [в настоящее время API отправки в Microsoft Store не поддерживает](create-and-manage-submissions-using-windows-store-services.md#not_supported). |


## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Получение отправки надстройки](get-an-add-on-submission.md)
* [Создание отправки надстройки](create-an-add-on-submission.md)
* [Подтверждение отправки надстройки](commit-an-add-on-submission.md)
* [Обновление отправки надстройки](update-an-add-on-submission.md)
* [Получение состоянии отправки надстройки](get-status-for-an-add-on-submission.md)
