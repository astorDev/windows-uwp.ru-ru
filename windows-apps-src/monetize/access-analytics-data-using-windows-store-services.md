---
author: mcleanbyron
ms.assetid: 4BF9EF21-E9F0-49DB-81E4-062D6E68C8B1
description: Используйте API аналитики для Microsoft Store для программного извлечения аналитических данных по приложениям, зарегистрированным на вашу учетную запись или учетную запись вашей организации в Центре разработки для Windows.
title: Доступ к аналитическим данным с помощью служб Магазина
ms.author: mcleans
ms.date: 06/04/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, службы Store, API аналитики дляMicrosoft Store
ms.localizationpriority: medium
ms.openlocfilehash: 26bed64053e8de9a42ac01ed3262c7b0f41d1d42
ms.sourcegitcommit: 5dda01da4702cbc49c799c750efe0e430b699502
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2018
ms.locfileid: "4111506"
---
# <a name="access-analytics-data-using-store-services"></a>Доступ к аналитическим данным с помощью служб Магазина

Используйте *API аналитики для Microsoft Store* для программного извлечения аналитических данных по приложениям, зарегистрированным на вашу учетную запись или учетную запись вашей организации в Центре разработки для Windows. Этот API позволяет извлекать данные о приобретении, ошибках, оценках и отзывов для приложения и надстройки (внутреннего продукта или IAP). Для проверки подлинности вызовов из приложения или службы в этом интерфейсе используется служба Azure Active Directory (Azure AD).

Далее описан весь процесс.

1.  Убедитесь, что вы выполнили все [необходимые условия](#prerequisites).
2.  Перед вызовом метода в API аналитики для Microsoft Store[получите маркер доступа Azure AD](#obtain-an-azure-ad-access-token). После получения маркера доступа у вас будет 60минут, чтобы использовать его в вызовах к API аналитики для Microsoft Store до окончания срока действия маркера. После истечения срока действия маркера можно сформировать новый маркер.
3.  [Вызов API аналитики дляMicrosoft Store](#call-the-windows-store-analytics-api).

<span id="prerequisites" />

## <a name="step-1-complete-prerequisites-for-using-the-microsoft-store-analytics-api"></a>Шаг 1. Выполнение необходимых условий для использования API аналитики дляMicrosoft Store

Перед тем как начать писать код для вызова API аналитики для Microsoft Store, убедитесь, что вы выполнили следующие необходимые условия.

* У вас (или у вашей организации) должен быть каталог Azure AD, а также разрешение [глобального администратора](http://go.microsoft.com/fwlink/?LinkId=746654) для этого каталога. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть Azure Active Directory. В противном случае можно [создать новый каталог Azure AD в Центре разработки](../publish/associate-azure-ad-with-dev-center.md#create-a-brand-new-azure-ad-to-associate-with-your-dev-center-account) без дополнительной платы.

* Вам необходимо привязать приложение Azure AD к учетной записи Центра разработки, получить идентификатор клиента для приложения и идентификатор клиента и создать ключ. Приложение Azure AD представляет собой приложение или службу, из которой отправляются вызовы в API аналитики дляMicrosoft Store. Чтобы получить маркер доступа Azure AD, который вы передадите в API, необходимо иметь в наличии идентификатор владельца, идентификатор клиента и ключ.
    > [!NOTE]
    > Эту операцию необходимо выполнить только один раз. После того как вы получите идентификатор владельца, идентификатор клиента и ключ, их можно будет использовать повторно в любое время для создания нового маркера доступа Azure AD.

Чтобы привязать приложение Azure AD к учетной записи Центра разработки и получить необходимые значения, выполните следующие действия.

1.  В Центре разработки [привяжите учетную запись своей организации к каталогу Azure AD организации](../publish/associate-azure-ad-with-dev-center.md).

2.  Затем на странице **Пользователи** в разделе **Параметры учетной записи** Центра разработки [добавьте приложение Azure AD](../publish/add-users-groups-and-azure-ad-applications.md#add-azure-ad-applications-to-your-dev-center-account), представляющее собой приложение или службу, которые вы будете использовать для доступа к аналитическим данным вашей учетной записи Центра разработки. Обязательно назначьте этому приложению роль **Менеджер**. Если приложение еще не существует в каталоге Azure AD, вы можете [создать новое приложение Azure AD в Центре разработки](../publish/add-users-groups-and-azure-ad-applications.md#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-dev-center-account).

3.  Вернитесь на страницу **Пользователи**, щелкните имя приложения Azure AD, чтобы перейти к параметрам приложения, и скопируйте значения **идентификатора владельца** и **идентификатора клиента**.

4. Нажмите кнопку **Добавить новый ключ**. На следующем экране скопируйте **ключ**. Вы не сможете получить доступ к этой информации после закрытия страницы. Дополнительные сведения см. в разделе [Управление ключами для приложения Azure AD](../publish/add-users-groups-and-azure-ad-applications.md#manage-keys).

<span id="obtain-an-azure-ad-access-token" />

## <a name="step-2-obtain-an-azure-ad-access-token"></a>Шаг 2. Получение маркера доступа Azure AD

Перед тем как можно будет вызвать любой из методов в API аналитики дляMicrosoft Store, сначала необходимо получить маркер доступа Azure AD и передать его в заголовок **Авторизация** каждого метода в API. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера вы можете обновить его, чтобы дальше использовать в последующих вызовах к API.

Для получения маркера доступа следуйте инструкциям в разделе [Вызовы между службами с помощью учетных данных клиентов](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/), чтобы отправить HTTP-запрос POST в конечную точку ```https://login.microsoftonline.com/<tenant_id>/oauth2/token```. Ниже приведен пример запроса.

```
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource=https://manage.devcenter.microsoft.com
```

Для значения *tenant\_id* в POST URI и параметров *client\_id* и *client\_secret* укажите идентификатор владельца, идентификатор клиента и ключ вашего приложения, который был получен в Центре разработки, как описано в предыдущем разделе. Для параметра *resource* укажите ```https://manage.devcenter.microsoft.com```.

После истечения срока действия токена доступа вы можете обновить его, следуя инструкциям, приведенным [здесь](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-code/#refreshing-the-access-tokens).

<span id="call-the-windows-store-analytics-api" />

## <a name="step-3-call-the-microsoft-store-analytics-api"></a>Шаг 3. Вызов API аналитики для Microsoft Store

После получения маркера доступа Azure AD вы можете вызвать API аналитики дляMicrosoft Store. Необходимо передать токен доступа в заголовок **Авторизация** каждого метода.

### <a name="methods-for-uwp-apps"></a>Методы для приложений UWP

Следующие методы аналитики доступны для приложений UWP в центре разработки.

| Сценарий       | Методы      |
|---------------|--------------------|
| Приобретение, преобразования, установки и использования |  <ul><li>[Получение сведений о покупках приложения](get-app-acquisitions.md)</li><li>[Получение данных воронки приобретений приложения](get-acquisition-funnel-data.md)</li><li>[Получение преобразований приложения по каналу](get-app-conversions-by-channel.md)</li><li>[Получение сведений о приобретениях надстройки](get-in-app-acquisitions.md)</li><li>[Получение сведений о приобретениях надстройки с подпиской](get-subscription-acquisitions.md)</li><li>[Получение конверсий надстройки по каналу](get-add-on-conversions-by-channel.md)</li><li>[Получение сведений об установках приложения](get-app-installs.md)</li><li>[Получение ежедневных использование приложений](get-app-usage-daily.md)</li><li>[Получение ежемесячные использование приложений](get-app-usage-monthly.md)</li></ul> |
| Ошибки приложения | <ul><li>[Получение данных отчетов об ошибках](get-error-reporting-data.md)</li><li>[Получение сведений об ошибке в приложении](get-details-for-an-error-in-your-app.md)</li><li>[Получение трассировки стека при возникновении ошибки в приложении](get-the-stack-trace-for-an-error-in-your-app.md)</li><li>[Скачивание CAB-файла для ошибки в приложении](download-the-cab-file-for-an-error-in-your-app.md)</li></ul> |
| Аналитика | <ul><li>[Получение данных о для вашего приложения](get-insights-data-for-your-app.md)</li></ul>  |
| Оценки и отзывы | <ul><li>[Получение сведений об оценках приложения](get-app-ratings.md)</li><li>[Получение отзывов о приложении](get-app-reviews.md)</li></ul> |
| Реклама в приложении и рекламные кампании | <ul><li>[Получение данных об эффективности рекламы](get-ad-performance-data.md)</li><li>[Получение данных об эффективности рекламной кампании](get-ad-campaign-performance-data.md)</li></ul> |

### <a name="methods-for-desktop-applications"></a>Методы для классических приложений

Для учетных записей разработчиков, которые связаны с [программой для разработчиков классических приложений для Windows](https://msdn.microsoft.com/library/windows/desktop/mt826504), доступны следующие методы аналитики.

| Сценарий       | Методы      |
|---------------|--------------------|
| Установки |  <ul><li>[Получение установок классического приложения](get-desktop-app-installs.md)</li></ul> |
| Блоки |  <ul><li>[Получить обновления блоков для классического приложения](get-desktop-block-data.md)</li><li>[Получение сведений блок обновления для классического приложения](get-desktop-block-data-details.md)</li></ul> |
| Ошибки приложений |  <ul><li>[Получение данных отчетов об ошибках для классического приложения](get-desktop-application-error-reporting-data.md)</li><li>[Получение сведений об ошибке в классическом приложении](get-details-for-an-error-in-your-desktop-application.md)</li><li>[Получение трассировки стека при возникновении ошибки в классическом приложении](get-the-stack-trace-for-an-error-in-your-desktop-application.md)</li><li>[Скачивание CAB-файла для ошибки в классическом приложении](download-the-cab-file-for-an-error-in-your-desktop-application.md)</li></ul> |
| Аналитика | <ul><li>[Получение данных о для классического приложения](get-insights-data-for-your-desktop-app.md)</li></ul>  |

### <a name="methods-for-xbox-live-services"></a>Методы для служб Xbox Live

Доступны следующие дополнительные методы для использования учетными записями разработчиков в играх, использующих [службы Xbox Live](../xbox-live/developer-program-overview.md).

| Сценарий       | Методы      |
|---------------|--------------------|
| Общая аналитика |  <ul><li>[Получение аналитических данных Xbox Live](get-xbox-live-analytics.md)</li><li>[Получение данных о достижениях в Xbox Live](get-xbox-live-achievements-data.md)</li><li>[Получение данных параллельного использования Xbox Live](get-xbox-live-concurrent-usage-data.md)</li></ul> |
| Аналитика работоспособности |  <ul><li>[Получение данных работоспособности в Xbox Live](get-xbox-live-health-data.md)</li></ul> |
| Аналитика сообщества |  <ul><li>[Получение данных центра игр в Xbox Live](get-xbox-live-game-hub-data.md)</li><li>[Получение данных клуба в Xbox Live](get-xbox-live-club-data.md)</li><li>[Получение многопользовательских данных в Xbox Live](get-xbox-live-multiplayer-data.md)</li></ul>  |

### <a name="methods-for-xbox-one-games"></a>Методы для игр Xbox One

Следующие дополнительные методы доступны для использования учетными записями разработчиков в играх Xbox One, которые добавлены через портал разработчика Xbox (XDP) и доступны на информационной панели центра разработки аналитики XDP.

| Сценарий       | Методы      |
|---------------|--------------------|
| Приобретения |  <ul><li>[Получение сведений о покупках игр на Xbox One](get-xbox-one-game-acquisitions.md)</li></ul> |

### <a name="methods-for-hardware-and-drivers"></a>Методы для оборудования и драйверов

Учетных записей разработчиков, которые связаны с [программой центра разработки оборудования для Windows](https://msdn.microsoft.com/windows/hardware/drivers/dashboard/get-started-with-the-hardware-dashboard) иметь доступ к дополнительный набор методов для извлечения аналитических данных по оборудования и драйверов. Дополнительные сведения см. в разделе [панели мониторинга оборудования API](https://docs.microsoft.com/windows-hardware/drivers/dashboard/dashboard-api).

## <a name="code-example"></a>Пример кода

В следующем примере кода показано, как получить маркер доступа Azure AD и вызвать API аналитики для Microsoft Store из консольного приложения C#. Чтобы использовать этот пример кода, назначьте переменным *tenantId*, *clientId*, *clientSecret* и *appID* соответствующие вашему сценарию значения. В этом примере для десериализации данных JSON, возвращаемых API аналитики дляMicrosoft Store, требуется [пакет Json.NET](http://www.newtonsoft.com/json).

> [!div class="tabbedCodeSnippets"]
[!code-cs[AnalyticsApi](./code/StoreServicesExamples_Analytics/cs/Program.cs#AnalyticsApiExample)]

## <a name="error-responses"></a>Ошибочные ответы

API аналитики для Microsoft Store возвращает ошибочные ответы в объекте JSON, который содержит коды ошибок и сообщения. В следующем примере демонстрируется ошибочный ответ, вызванный недопустимым параметром.

```json
{
    "code":"BadRequest",
    "data":[],
    "details":[],
    "innererror":{
        "code":"InvalidQueryParameters",
        "data":[
            "top parameter cannot be more than 10000"
        ],
        "details":[],
        "message":"One or More Query Parameters has invalid values.",
        "source":"AnalyticsAPI"
    },
    "message":"The calling client sent a bad request to the service.",
    "source":"AnalyticsAPI"
}
```
