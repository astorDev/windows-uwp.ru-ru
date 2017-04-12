---
author: mcleanbyron
ms.assetid: DAF92881-6AF6-44C7-B466-215F5226AE04
description: "Используйте этот метод в API отправки Магазина Windows для получения информации о конкретном приложении, которое зарегистрировано в вашей учетной записи Центра разработки для Windows."
title: "Получение приложения"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API отправки Магазина Windows, приложение"
ms.openlocfilehash: 6087bf65ac297b12f92a9082db4e6a12e93130ef
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="get-an-app"></a>Получение приложения




Используйте этот метод в API отправки Магазина Windows для получения информации о конкретном приложении, которое зарегистрировано в вашей учетной записи Центра разработки для Windows.

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.

>**Примечание.**&nbsp;&nbsp;Этот метод можно использовать только для учетных записей Центра разработки для Windows, у которых есть разрешение на использование API отправки Магазина Windows. Такое разрешение имеется не у всех учетных записей.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}``` |

<span/>
 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательное. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/>

### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | Строка | Обязательный. Код продукта в Магазине для приложения, которое требуется получить. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |

<span/>

### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

<span/>

### <a name="request-example"></a>Пример запроса

В следующем примере показано, как получить сведения о приложении с кодом продукта в Магазине 9WZDNCRD91MD.

```
GET https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлено тело ответа JSON в случае успешного вызова этого метода. Дополнительные сведения о значениях в теле ответа см. в разделе [Ресурс приложения](get-app-data.md#application_object).

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
  }
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 404  | Не удалось найти указанное приложение. |
| 409  | Приложение использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки Магазина Windows](create-and-manage-submissions-using-windows-store-services.md#not_supported).  |

<span/>

## <a name="related-topics"></a>Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
* [Получение всех приложений](get-all-apps.md)
* [Получение тестовых пакетов для приложения](get-flights-for-an-app.md)
* [Получение надстроек для приложения](get-add-ons-for-an-app.md)
