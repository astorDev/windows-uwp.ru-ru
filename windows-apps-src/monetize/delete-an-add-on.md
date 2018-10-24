---
author: Xansky
ms.assetid: 16D4C3B9-FC9B-46ED-9F87-1517E1B549FA
description: Используйте этот метод в API отправки в Microsoft Store для удаления надстройки для приложения, которое зарегистрировано в вашей учетной записи Центра разработки для Windows.
title: Удаление надстройки
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, API отправки в Microsoft Store, надстройка, удаление, продукт внутри приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: ff108f3f7f09aa737f6a955d9cb91810f6047f8c
ms.sourcegitcommit: 4b97117d3aff38db89d560502a3c372f12bb6ed5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "5441304"
---
# <a name="delete-an-add-on"></a>Удаление надстройки

Используйте этот метод в API отправки в Microsoft Store для удаления надстройки (также называется внутренним продуктом приложения или IAP) для приложения, которое зарегистрировано в вашей учетной записи центра разработки для Windows.

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| УДАЛЕНИЕ    | ```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| id | string | Обязательный. Код продукта в Магазине для надстройки, которую требуется удалить. Код продукта в Магазине отображается на информационной панели Центра разработки.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.


### <a name="request-example"></a>Пример запроса

В следующем примере показано, как удалить надстройку.

```
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
| 409  | Указанная надстройка найдена, однако ее не удалось удалить в текущем состоянии или она использует функцию панели мониторинга Центра разработки, которую [в настоящее время API отправки в Microsoft Store не поддерживает](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Получение всех надстроек](get-all-add-ons.md)
* [Получение надстройки](get-an-add-on.md)
* [Создание надстройки](create-an-add-on.md)
