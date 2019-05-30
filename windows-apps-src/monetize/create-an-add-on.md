---
ms.assetid: 5BD650D2-AA26-4DE9-8243-374FDB7D932B
description: Используйте этот метод в интерфейсе API отправки Microsoft Store, чтобы создать надстройку для приложения, зарегистрированный для вашей учетной записи PartnerCenter.
title: Создание надстройки
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, API отправки Microsoft Store, создание надстройки, внутренний продукт приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: 00eb1a865631ce51cfa065d27ed00b44c66a6757
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371259"
---
# <a name="create-an-add-on"></a>Создание надстройки

Используйте этот метод в интерфейсе API отправки Microsoft Store для создания надстройки (также известная как app продукта или IAP) для приложения, зарегистрированный для вашей учетной записи центра партнеров.

> [!NOTE]
> При использовании этого метода выполняется создание надстройки без отправок. Руководство по созданию отправки для надстройки см. в описании методов в разделе [Управление отправками надстроек](manage-add-on-submissions.md).

## <a name="prerequisites"></a>предварительные требования

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| ПОМЕСТИТЬ    | `https://manage.devcenter.microsoft.com/v1.0/my/inappproducts` |


### <a name="request-header"></a>Заголовок запроса

| Header        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-body"></a>Тело запроса

Текст запроса содержит следующие параметры.

|  Параметр  |  Тип  |  Описание  |  Обязательно  |
|------|------|------|------|
|  applicationIds  |  Массив  |  Массив, содержащий код продукта в Магазине для приложения, с которым связана эта надстройка. Этот массив поддерживает только один элемент.   |  Да  |
|  productId  |  строка  |  Код продукта этой надстройки. Этот идентификатор можно использовать в коде для установки ссылки на надстройку. Дополнительные сведения см. в разделе [Установка типа и кода продукта](https://docs.microsoft.com/windows/uwp/publish/set-your-iap-product-id).  |  Да  |
|  productType  |  строка  |  Тип продукта этой надстройки. Поддерживаются следующие значения: **Устойчивые** и **готовых к использованию**.  |  Да  |


### <a name="request-example"></a>Пример запроса

В следующем примере кода показано, как создать новую надстройку потребляемого типа для приложения.

```json
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

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. Дополнительные сведения о значениях в теле ответа см. в разделе [Ресурс надстройки](manage-add-ons.md#add-on-object).

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
| 409  | Не удалось создать надстройку из-за текущего состояния или надстройка использует возможности центра партнеров, [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>См. также

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправкой надстройки](manage-add-on-submissions.md)
* [Получение всех надстроек](get-all-add-ons.md)
* [Получения надстройки](get-an-add-on.md)
* [Удалить надстройку](delete-an-add-on.md)
