---
author: Xansky
ms.assetid: 5BD650D2-AA26-4DE9-8243-374FDB7D932B
description: Используйте этот метод в API отправки Microsoft Store для создания надстройки для приложения, которое зарегистрировано в вашей учетной записи PartnerCenter.
title: Создание надстройки
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, API отправки Microsoft Store, создание надстройки, внутренний продукт приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: d262a86c4a177095015c3f1391b19f1a7719d0a4
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6187696"
---
# <a name="create-an-add-on"></a>Создание надстройки

Используйте этот метод в API отправки Microsoft Store для создания надстройки (также называется внутренним продуктом приложения или IAP) для приложения, которое зарегистрировано в вашей учетной записи центра партнеров.

> [!NOTE]
> При использовании этого метода выполняется создание надстройки без отправок. Руководство по созданию отправки для надстройки см. в описании методов в разделе [Управление отправками надстроек](manage-add-on-submissions.md).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| POST    | ```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-body"></a>Текст запроса

Текст запроса содержит следующие параметры.

|  Параметр  |  Тип  |  Описание  |  Обязательный.  |
|------|------|------|------|
|  applicationIds  |  Массив  |  Массив, содержащий код продукта в Магазине для приложения, с которым связана эта надстройка. Этот массив поддерживает только один элемент.   |  Да  |
|  productId  |  Строка  |  Код продукта этой надстройки. Этот идентификатор можно использовать в коде для установки ссылки на надстройку. Дополнительные сведения см. в разделе [Установка типа и кода продукта](https://msdn.microsoft.com/windows/uwp/publish/set-your-iap-product-id).  |  Да  |
|  productType  |  Строка  |  Тип продукта этой надстройки. Поддерживаются следующие значения: **Длительного пользования** и **Потребляемый**.  |  Да  |


### <a name="request-example"></a>Пример запроса

В следующем примере кода показано, как создать новую надстройку потребляемого типа для приложения.

```syntax
POST https://manage.devcenter.microsoft.com/v1.0/my/inappproducts HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1Q...
Content-Type: application/json
{
    "applicationIds": [  "9NBLGGH4R315"  ],
    "productId": "my-new-add-on",
    "productType": "Consumable",
}
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON, обеспечивающий успешный вызов этого метода. Дополнительные сведения о значениях в тексте ответа см. в разделе [Ресурс надстройки](manage-add-ons.md#add-on-object).

```json
{
  "applications": {
    "value": [
      {
        "id": "9NBLGGH4R315",
        "resourceLocation": "applications/9NBLGGH4R315"
      }
    ],
    "totalCount": 1
  },
  "id": "9NBLGGH4TNMP",
  "productId": "my-new-add-on",
  "productType": "Consumable",
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание                                                                                                                                                                           |
|--------|------------------|
| 400  | Недопустимый запрос. |
| 409  | Не удалось создать надстройку из-за его текущего состояния или надстройка использует компонент центра партнеров, [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>Статьи по теме

* [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправками надстроек](manage-add-on-submissions.md)
* [Получение всех надстроек](get-all-add-ons.md)
* [Получение надстройки](get-an-add-on.md)
* [Удаление надстройки](delete-an-add-on.md)
