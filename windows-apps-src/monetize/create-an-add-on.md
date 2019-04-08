---
ms.assetid: 5BD650D2-AA26-4DE9-8243-374FDB7D932B
description: Используйте этот метод в интерфейсе API отправки Microsoft Store, чтобы создать надстройку для приложения, зарегистрированный для вашей учетной записи PartnerCenter.
title: Создание надстройки
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, API отправки Microsoft Store, создание надстройки, внутренний продукт приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: 8465dc7a42961a20fcd33ba8d43c71e2d73727ff
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651039"
---
# <a name="create-an-add-on"></a>Создание надстройки

Используйте этот метод в интерфейсе API отправки Microsoft Store для создания надстройки (также известная как app продукта или IAP) для приложения, зарегистрированный для вашей учетной записи центра партнеров.

> [!NOTE]
> При использовании этого метода выполняется создание надстройки без отправок. Руководство по созданию отправки для надстройки см. в описании методов в разделе [Управление отправками надстроек](manage-add-on-submissions.md).

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| POST    | ```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-body"></a>Тело запроса

Текст запроса содержит следующие параметры.

|  Параметр  |  Тип  |  Описание  |  Обязательно  |
|------|------|------|------|
|  applicationIds  |  Массив  |  Массив, содержащий код продукта в Магазине для приложения, с которым связана эта надстройка. Этот массив поддерживает только один элемент.   |  Да  |
|  productId  |  Строка  |  Код продукта этой надстройки. Этот идентификатор можно использовать в коде для установки ссылки на надстройку. Дополнительные сведения см. в разделе [Установка типа и кода продукта](https://msdn.microsoft.com/windows/uwp/publish/set-your-iap-product-id).  |  Да  |
|  productType  |  Строка  |  Тип продукта этой надстройки. Поддерживаются следующие значения: **Устойчивые** и **готовых к использованию**.  |  Да  |


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


## <a name="related-topics"></a>Статьи по теме

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправкой надстройки](manage-add-on-submissions.md)
* [Получение всех надстроек](get-all-add-ons.md)
* [Получения надстройки](get-an-add-on.md)
* [Удалить надстройку](delete-an-add-on.md)
