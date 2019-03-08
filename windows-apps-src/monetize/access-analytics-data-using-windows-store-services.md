---
ms.assetid: 4BF9EF21-E9F0-49DB-81E4-062D6E68C8B1
description: Используйте API анализа Microsoft Store для программного извлечения данных аналитики для приложений, зарегистрированных для организации с вашего разрешения или разрешения '' s учетной записи центра партнеров Windows.
title: Доступ к аналитическим данным с помощью служб Магазина
ms.date: 06/04/2018
ms.topic: article
keywords: windows 10, uwp, службы Store, API аналитики для Microsoft Store
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 72e0941bb42a2a507af652758432ce51212c1042
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57592659"
---
# <a name="access-analytics-data-using-store-services"></a>Доступ к аналитическим данным с помощью служб Магазина

Используйте *API анализа Microsoft Store* Чтобы программно получить данные аналитики для приложений, которые регистрируются для вашего разрешения или разрешения центра партнеров Windows учетной записи организации. Этот API позволяет извлекать данные о приобретении, ошибках, оценках и отзывов для приложения и надстройки (внутреннего продукта или IAP). Для проверки подлинности вызовов из приложения или службы в этом интерфейсе используется служба Azure Active Directory (Azure AD).

Далее описан весь процесс.

1.  Убедитесь, что вы выполнили все [необходимые условия](#prerequisites).
2.  Перед вызовом метода в API аналитики для Microsoft Store[получите маркер доступа Azure AD](#obtain-an-azure-ad-access-token). После получения маркера доступа у вас будет 60 минут, чтобы использовать его в вызовах к API аналитики для Microsoft Store до окончания срока действия маркера. После истечения срока действия маркера можно сформировать новый маркер.
3.  [Вызов API аналитики для Microsoft Store](#call-the-windows-store-analytics-api).

<span id="prerequisites" />

## <a name="step-1-complete-prerequisites-for-using-the-microsoft-store-analytics-api"></a>Шаг 1. Выполните предварительные требования для использования API анализа Microsoft Store

Перед тем как начать писать код для вызова API аналитики для Microsoft Store, убедитесь, что вы выполнили следующие необходимые условия.

* У вас (или у вашей организации) должен быть каталог Azure AD, а также разрешение [глобального администратора](https://go.microsoft.com/fwlink/?LinkId=746654) для этого каталога. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть Azure Active Directory. В противном случае вы можете [создайте новый Azure AD в центре партнеров](../publish/associate-azure-ad-with-partner-center.md#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account) без дополнительной платы.

* Их необходимо связать приложение Azure AD с помощью учетной записи центра партнеров, получить идентификатор клиента и идентификатор клиента для приложения и создать ключ. Приложение Azure AD представляет собой приложение или службу, из которой отправляются вызовы в API аналитики для Microsoft Store. Чтобы получить маркер доступа Azure AD, который вы передадите в API, необходимо иметь в наличии идентификатор владельца, идентификатор клиента и ключ.
    > [!NOTE]
    > Эту операцию необходимо выполнить только один раз. После того как вы получите идентификатор владельца, идентификатор клиента и ключ, их можно будет использовать повторно в любое время для создания нового маркера доступа Azure AD.

Чтобы связать приложение Azure AD с помощью учетной записи центра партнеров и получить необходимые значения:

1.  В центре партнеров [связать учетную запись центра партнеров организации в каталоге Azure AD вашей организации](../publish/associate-azure-ad-with-partner-center.md).

2.  Далее из **пользователей** странице в **параметры учетной записи** раздел центра партнеров, [добавить это приложение Azure AD](../publish/add-users-groups-and-azure-ad-applications.md#add-azure-ad-applications-to-your-partner-center-account) , представляющий приложение или служба, которая будет использоваться для доступ к данным аналитики для учетной записи центра партнеров. Обязательно назначьте этому приложению роль **Менеджер**. Если приложение не существует в каталоге Azure AD вы можете [создайте приложение Azure AD в центре партнеров](../publish/add-users-groups-and-azure-ad-applications.md#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account).

3.  Вернитесь на страницу **Пользователи**, щелкните имя приложения Azure AD, чтобы перейти к параметрам приложения, и скопируйте значения **идентификатора владельца** и **идентификатора клиента**.

4. Нажмите кнопку **Добавить новый ключ**. На следующем экране скопируйте **ключ**. Вы не сможете получить доступ к этой информации после закрытия страницы. Дополнительные сведения см. в разделе [Управление ключами для приложения Azure AD](../publish/add-users-groups-and-azure-ad-applications.md#manage-keys).

<span id="obtain-an-azure-ad-access-token" />

## <a name="step-2-obtain-an-azure-ad-access-token"></a>Шаг 2. Получение токена доступа Azure AD

Перед тем как можно будет вызвать любой из методов в API аналитики для Microsoft Store, сначала необходимо получить маркер доступа Azure AD и передать его в заголовок **Авторизация** каждого метода в API. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия маркера вы можете обновить его, чтобы дальше использовать в последующих вызовах к API.

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

Для *клиента\_идентификатор* в URI, POST и *клиента\_идентификатор* и *клиента\_секрет* параметры, укажите клиента Идентификатор, идентификатор клиента и ключ для вашего приложения, который вы получили из центра партнеров в предыдущем разделе. Для параметра *resource* укажите ```https://manage.devcenter.microsoft.com```.

После истечения срока действия маркера доступа вы можете обновить его, следуя инструкциям, приведенным [здесь](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-code/#refreshing-the-access-tokens).

<span id="call-the-windows-store-analytics-api" />

## <a name="step-3-call-the-microsoft-store-analytics-api"></a>Шаг 3. Вызовите API анализа Microsoft Store

После получения маркера доступа Azure AD вы можете вызвать API аналитики для Microsoft Store. Необходимо передать токен доступа в заголовок **Authorization** каждого метода.

### <a name="methods-for-uwp-apps"></a>Методы для приложений UWP

Следующие методы analytics доступны для приложений универсальной платформы Windows в центре партнеров.

| Сценарий       | Методы      |
|---------------|--------------------|
| Ввод в эксплуатацию, преобразования, Установка и использование |  <ul><li>[Получить приобретения приложений](get-app-acquisitions.md)</li><li>[Получить данные воронки приобретения приложений](get-acquisition-funnel-data.md)</li><li>[Получить приложение преобразования каналом](get-app-conversions-by-channel.md)</li><li>[Получить приобретение надстройки](get-in-app-acquisitions.md)</li><li>[Получить подписку приобретение надстройки](get-subscription-acquisitions.md)</li><li>[Получить надстройку преобразования каналом](get-add-on-conversions-by-channel.md)</li><li>[Получить установки приложений](get-app-installs.md)</li><li>[Получение ежедневного использования приложения](get-app-usage-daily.md)</li><li>[Получение ежемесячного использования приложения](get-app-usage-monthly.md)</li></ul> |
| Ошибки приложения | <ul><li>[Получить данные отчетов об ошибках](get-error-reporting-data.md)</li><li>[Подробные сведения для ошибки в приложении](get-details-for-an-error-in-your-app.md)</li><li>[Получить трассировку стека для ошибки в приложении](get-the-stack-trace-for-an-error-in-your-app.md)</li><li>[Загрузите CAB-файл для ошибки в приложении](download-the-cab-file-for-an-error-in-your-app.md)</li></ul> |
| Дополнительная информация | <ul><li>[Получение данных insights для приложения](get-insights-data-for-your-app.md)</li></ul>  |
| Оценки и отзывы | <ul><li>[Получение оценок приложения](get-app-ratings.md)</li><li>[Получение проверки приложения](get-app-reviews.md)</li></ul> |
| Реклама в приложении и рекламные кампании | <ul><li>[Получить данные о производительности ad](get-ad-performance-data.md)</li><li>[Получить данные о производительности ad кампании](get-ad-campaign-performance-data.md)</li></ul> |

### <a name="methods-for-desktop-applications"></a>Методы для классических приложений

Для учетных записей разработчиков, которые связаны с [программой для разработчиков классических приложений для Windows](https://msdn.microsoft.com/library/windows/desktop/mt826504), доступны следующие методы аналитики.

| Сценарий       | Методы      |
|---------------|--------------------|
| Установки |  <ul><li>[Получить установку классических приложений](get-desktop-app-installs.md)</li></ul> |
| Блоки |  <ul><li>[Получение обновления блоков для своего настольного приложения](get-desktop-block-data.md)</li><li>[Возвращает сведения о блокировки для своего настольного приложения](get-desktop-block-data-details.md)</li></ul> |
| Ошибки приложений. |  <ul><li>[Получить данные для своего настольного приложения отчетов об ошибках](get-desktop-application-error-reporting-data.md)</li><li>[Подробные сведения для ошибки в приложения для настольных компьютеров](get-details-for-an-error-in-your-desktop-application.md)</li><li>[Получить трассировку стека для ошибки в приложения для настольных компьютеров](get-the-stack-trace-for-an-error-in-your-desktop-application.md)</li><li>[Загрузите CAB-файл для ошибки в приложения для настольных компьютеров](download-the-cab-file-for-an-error-in-your-desktop-application.md)</li></ul> |
| Дополнительная информация | <ul><li>[Получение данных insights для своего настольного приложения](get-insights-data-for-your-desktop-app.md)</li></ul>  |

### <a name="methods-for-xbox-live-services"></a>Методы для служб Xbox Live

Доступны следующие дополнительные методы для использования учетными записями разработчиков в играх, использующих [службы Xbox Live](../xbox-live/developer-program-overview.md).

| Сценарий       | Методы      |
|---------------|--------------------|
| Общая аналитика |  <ul><li>[Получить аналитические данные Xbox Live](get-xbox-live-analytics.md)</li><li>[Получение данных достижения Xbox Live](get-xbox-live-achievements-data.md)</li><li>[Получение данных одновременное использование Xbox Live](get-xbox-live-concurrent-usage-data.md)</li></ul> |
| Аналитика работоспособности |  <ul><li>[Получение данных работоспособности Xbox Live](get-xbox-live-health-data.md)</li></ul> |
| Аналитика сообщества |  <ul><li>[Получение данных концентратора-игр Xbox Live](get-xbox-live-game-hub-data.md)</li><li>[Получение данных клуба Xbox Live](get-xbox-live-club-data.md)</li><li>[Получение данных в многопользовательской, Xbox Live](get-xbox-live-multiplayer-data.md)</li></ul>  |

### <a name="methods-for-xbox-one-games"></a>Методы для игр Xbox One

Следующие дополнительные методы доступны для использования с учетными записями разработчика в службе Xbox One игры, которые были приняты через портал разработчиков Xbox (XDP) доступны на панели мониторинга аналитики XDP.

| Сценарий       | Методы      |
|---------------|--------------------|
| Приобретения |  <ul><li>[Получить Xbox One игр приобретений](get-xbox-one-game-acquisitions.md)</li><li>[Получить Xbox One приобретение надстройки](get-xbox-one-add-on-acquisitions.md)</li></ul> |
| Ошибки |  <ul><li>[Получить данные отчетов об ошибках для Xbox One игр](get-error-reporting-data-for-your-xbox-one-game.md)</li><li>[Подробные сведения для ошибки в Xbox One игр](get-details-for-an-error-in-your-xbox-one-game.md)</li><li>[Получить трассировку стека для ошибки в Xbox One игр](get-the-stack-trace-for-an-error-in-your-xbox-one-game.md)</li><li>[Загрузите CAB-файл для ошибки в игре для Xbox One](download-the-cab-file-for-an-error-in-your-xbox-one-game.md)</li></ul> |

### <a name="methods-for-hardware-and-drivers"></a>Методы для оборудования и драйверов

Учетные записи разработчика, которые принадлежат [программы панели мониторинга Windows hardware](https://msdn.microsoft.com/windows/hardware/drivers/dashboard/get-started-with-the-hardware-dashboard) имеют доступ к дополнительный набор методов для извлечения данных аналитики для оборудования и драйверов. Дополнительные сведения см. в разделе [панель мониторинга оборудования API](https://docs.microsoft.com/windows-hardware/drivers/dashboard/dashboard-api).

## <a name="code-example"></a>Пример кода

В следующем примере кода показано, как получить маркер доступа Azure AD и вызвать API аналитики для Microsoft Store из консольного приложения C#. Чтобы использовать этот пример кода, назначьте переменным *tenantId*, *clientId*, *clientSecret* и *appID* соответствующие вашему сценарию значения. В этом примере для десериализации данных JSON, возвращаемых API аналитики для Microsoft Store, требуется [пакет Json.NET](https://www.newtonsoft.com/json).

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
