---
ms.assetid: 16D4C3B9-FC9B-46ED-9F87-1517E1B549FA
description: Используйте этот метод в интерфейсе API отправки Microsoft Store, чтобы удалить надстройку для приложения, зарегистрированный для вашей учетной записи центра партнеров.
title: Удаление надстройки
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, надстройка, удаление, продукт внутри приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: 5540dfbdc185eae405b2cdde7f18faee0efd0cac
ms.sourcegitcommit: 6a7dd4da2fc31ced7d1cdc6f7cf79c2e55dc5833
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58334625"
---
# <a name="delete-an-add-on"></a>Удаление надстройки

Используйте этот метод в интерфейсе API отправки Microsoft Store для удаления надстройки (также известная как app продукта или IAP) для приложения, зарегистрированный для вашей учетной записи центра партнеров.

## <a name="prerequisites"></a>Предварительные требования

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| DELETE    | `https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}` |


### <a name="request-header"></a>Заголовок запроса

| Header        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| id | строка | Обязательный. Код продукта в Магазине для надстройки, которую требуется удалить. Идентификатор Store доступен в центре партнеров.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.


### <a name="request-example"></a>Пример запроса

В следующем примере показано, как удалить надстройку.

```json
DELETE https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/9NBLGGH4TNMP HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В случае успеха этот метод возвращает пустой ответ.

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание                                                                                                                                                                           |
|--------|------------------|
| 400  | Недопустимый запрос. |
| 404  | Не удалось найти указанную надстройку.  |
| 409  | Указанная надстройка был найден, но его не удалось удалить в ее текущем состоянии или надстройка использует возможности центра партнеров, [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>См. также

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Получение всех надстроек](get-all-add-ons.md)
* [Получения надстройки](get-an-add-on.md)
* [Создать надстройку](create-an-add-on.md)
