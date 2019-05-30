---
ms.assetid: 8D4AE532-22EF-4743-9555-A828B24B8F16
description: Эти методы можно используете в интерфейсе API отправки Microsoft Store для получения данных для приложений, зарегистрированных для учетной записи центра партнеров.
title: Получение данных приложения
ms.date: 02/28/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, данные приложения
ms.localizationpriority: medium
ms.openlocfilehash: cfbe8df46f51b41ccdd840f609caf2c593735e1f
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372138"
---
# <a name="get-app-data"></a>Получение данных приложения

Используйте следующие методы в интерфейсе API отправки Microsoft Store для получения данных для существующих приложений в вашей учетной записи центра партнеров. Введение в API отправки в Microsoft Store, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md).

Прежде чем использовать эти методы, приложение уже должен существовать в учетную запись центра партнеров. Для создания отправок для приложений и управления ими см. описания методов в разделе [Управление отправками приложений](manage-app-submissions.md).

| Метод | URI                                                                                             | Описание                                                 |
|------- |------------------------------------------------------------------------------------------------ |------------------------------------------------------------ |
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/applications`                                   | [Получение данных для всех приложений](get-all-apps.md)               |
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}`                   | [Получение данных для определенного приложения](get-an-app.md)                |
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/listinappproducts` | [Получение надстроек для приложения](get-add-ons-for-an-app.md)         |
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/listflights`       | [Получить пакет авиарейсов для приложения](get-flights-for-an-app.md) |

## <a name="prerequisites"></a>предварительные требования

Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store, прежде чем использовать любой из этих методов.

## <a name="data-resources"></a>Ресурсы данных

Методы API отправки в Microsoft Store для получения данных приложения используют следующие ресурсы данных JSON.

<span id="application_object" />

### <a name="application-resource"></a>Ресурс приложения

Этот ресурс представляет приложение, зарегистрированное в вашей учетной записи.

```json
{
  "id": "9NBLGGH4R315",
  "primaryName": "ApiTestApp",
  "packageFamilyName": "30481DevCenterAPITester.ApiTestAppForDevbox_ng6try80pwt52",
  "packageIdentityName": "30481DevCenterAPITester.ApiTestAppForDevbox",
  "publisherName": "CN=…",
  "firstPublishedDate": "1601-01-01T00:00:00Z",
  "lastPublishedApplicationSubmission": {
    "id": "1152921504621086517",
    "resourceLocation": "applications/9NBLGGH4R315/submissions/1152921504621086517"
  },
  "pendingApplicationSubmission": {
    "id": "1152921504621243487",
    "resourceLocation": "applications/9NBLGGH4R315/submissions/1152921504621243487"
  },
  "hasAdvancedListingPermission": true
}
```

У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание       |
|-----------------|---------|---------------------|
| id            | строка  | Код продукта в Магазине для приложения. Подробнее о коде продукта в Магазине см. в статье [Просмотр сведений об идентификации приложений](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details).   |
| primaryName   | строка  | Основное имя приложения.      |
| packageFamilyName | строка  | Имя семейства пакетов для приложения.      |
| packageIdentityName          | строка  | Имя идентификации пакета для приложения.                       |
| publisherName       | строка  | Идентификатор издателя Windows, который связан с приложением. Это соответствует **пакета/удостоверений/издатель** значение, которое появляется на [удостоверения приложения](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details) страницу приложения в центре партнеров.       |
| firstPublishedDate      | строка  | Дата первой публикации приложения в формате ISO 8601.   |
| lastPublishedApplicationSubmission       | Объект | [Ресурс отправки](#submission_object), который предоставляет сведения о последней опубликованной отправке для приложения.    |
| pendingApplicationSubmission        | Объект  |  [Ресурс отправки](#submission_object), который предоставляет сведения о текущей ожидающей отправке для приложения.   |   
| hasAdvancedListingPermission        | Логический  |  Указывает, можно ли настроить [gamingOptions](manage-app-submissions.md#gaming-options-object) или [trailers](manage-app-submissions.md#trailer-object) для отправки для приложения. Это значение равно true для отправок, созданных после мая 2017 г. |  |


<span id="add-on-object" />

### <a name="add-on-resource"></a>Ресурс надстройки

Этот ресурс содержит сведения о надстройке.

```json
{
    "inAppProductId": "9WZDNCRD7DLK"
}
```

У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание         |
|-----------------|---------|----------------------|
| inAppProductId            | строка  | Код продукта в Магазине для этой надстройки. Это значение предоставляется Магазином. Пример кода продукта в Магазине: 9NBLGGH4TNMP.   |


<span id="flight-object" />

### <a name="flight-resource"></a>Тестовый ресурс

Этот ресурс содержит сведения о тестовом пакете для приложения.

```json
{
    "flightId": "7bfc11d5-f710-47c5-8a98-e04bb5aad310",
    "friendlyName": "myflight",
    "lastPublishedFlightSubmission": {
        "id": "1152921504621086517",
        "resourceLocation": "flights/7bfc11d5-f710-47c5-8a98-e04bb5aad310/submissions/1152921504621086517"
    },
    "pendingFlightSubmission": {
        "id": "1152921504621215786",
        "resourceLocation": "flights/7bfc11d5-f710-47c5-8a98-e04bb5aad310/submissions/1152921504621215786"
    },
    "groupIds": [
        "1152921504606962205"
    ],
    "rankHigherThan": "Non-flighted submission"
}
```

У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание           |
|-----------------|---------|------------------------|
| flightId            | строка  | Идентификатор для тестового пакета. Это значение предоставляется путем центра партнеров.  |
| friendlyName           | строка  | Имя тестового пакета, указанное разработчиком.   |
| lastPublishedFlightSubmission       | Объект | [Ресурс отправки](#submission_object), который предоставляет сведения о последней опубликованной отправке для тестового пакета.   |
| pendingFlightSubmission        | Объект  |  [Ресурс отправки](#submission_object), который предоставляет сведения о текущей ожидающей отправке для тестового пакета.  |    
| groupIds           | Массив  | Массив строк, содержащий идентификаторы тестовых групп, которые связаны с тестовым пакетом. Дополнительные сведения о тестовых группах см. в разделе [Тестовые пакеты](https://docs.microsoft.com/windows/uwp/publish/package-flights).   |
| rankHigherThan           | строка  | Понятное имя тестового пакета, приоритет которого на единицу ниже приоритета текущего тестового пакета. Дополнительные сведения о задании приоритетов тестовых групп см. в разделе [Тестовые пакеты](https://docs.microsoft.com/windows/uwp/publish/package-flights).  |


<span id="submission_object" />

### <a name="submission-resource"></a>Ресурс отправки

Этот ресурс содержит сведения об отправке. В следующем примере показан формат этого ресурса.

```json
{
  "pendingApplicationSubmission": {
    "id": "1152921504621243487",
    "resourceLocation": "applications/9WZDNCRD9MMD/submissions/1152921504621243487"
  }
}
```

У этого ресурса есть следующие значения.

| Значение              | Тип   | Описание               |
|--------------------|--------|---------------------------|
| id                 | строка | Идентификатор отправки. |
| resourceLocation   | строка | Относительный путь, который можно добавить к базовому URI запроса ```https://manage.devcenter.microsoft.com/v1.0/my/```, чтобы получить полные данные для отправки. |

 
## <a name="related-topics"></a>См. также

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправки приложений, с помощью API отправки Microsoft Store](manage-app-submissions.md)
* [Получить все приложения](get-all-apps.md)
* [Получить приложения](get-an-app.md)
* [Получение надстроек для приложения](get-add-ons-for-an-app.md)
* [Получить пакет авиарейсов для приложения](get-flights-for-an-app.md)
