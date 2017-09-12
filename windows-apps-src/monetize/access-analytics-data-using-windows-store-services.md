---
author: mcleanbyron
ms.assetid: 4BF9EF21-E9F0-49DB-81E4-062D6E68C8B1
description: "Используйте интерфейс analytics API Магазина Windows для программного извлечения аналитических данных по приложениям, зарегистрированным на вашу учетную запись или учетную запись вашей организации в Центре разработки для Windows."
title: "Доступ к аналитическим данным с помощью служб Магазина"
ms.author: mcleans
ms.date: 08/03/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, uwp, службы Магазина, API аналитики для Магазина Windows"
ms.openlocfilehash: f739676d02ae5af4c3960fdde6461779c1533885
ms.sourcegitcommit: 2b436dc5e5681b8884e0531ee303f851a3e3ccf2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2017
---
# <a name="access-analytics-data-using-store-services"></a>Доступ к аналитическим данным с помощью служб Магазина

Используйте *интерфейс analytics API Магазина Windows* для программного извлечения аналитических данных по приложениям, зарегистрированным на вашу учетную запись или учетную запись вашей организации в Центре разработки для Windows. Этот API позволяет извлекать данные о приобретении, ошибках, оценках и отзывов для приложения и надстройки (внутреннего продукта или IAP). Для проверки подлинности вызовов из приложения или службы в этом интерфейсе используется служба Azure Active Directory (Azure AD).

Далее описан весь процесс.

1.  Убедитесь, что вы выполнили все [необходимые условия](#prerequisites).
2.  Перед вызовом метода в API аналитики дляМагазина Windows [получите маркер доступа Azure AD](#obtain-an-azure-ad-access-token). После получения маркера доступа у вас будет 60минут, чтобы использовать его в вызовах к API аналитики дляМагазина Windows до окончания срока действия маркера. После истечения срока действия маркера можно сформировать новый маркер.
3.  [Вызов интерфейса analytics API Магазина Windows](#call-the-windows-store-analytics-api).

<span id="prerequisites" />
## <a name="step-1-complete-prerequisites-for-using-the-windows-store-analytics-api"></a>Шаг 1. Выполнение необходимых условий для использования API аналитики дляМагазина Windows

Перед тем как начать писать код для вызова API аналитики дляМагазина Windows, убедитесь, что вы выполнили следующие необходимые условия.

* У вас (или у вашей организации) должен быть каталог Azure AD, а также разрешение [глобального администратора](http://go.microsoft.com/fwlink/?LinkId=746654) для этого каталога. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть Azure Active Directory. В противном случае можно [создать новый каталог Azure AD в Центре разработки](../publish/associate-azure-ad-with-dev-center.md#create-a-brand-new-azure-ad-to-associate-with-your-dev-center-account) без дополнительной платы.

* Вам необходимо привязать приложение Azure AD к учетной записи Центра разработки, получить идентификатор клиента для приложения и идентификатор клиента и создать ключ. Приложение Azure AD представляет собой приложение или службу, из которой отправляются вызовы в интерфейс analytics API Магазина Windows. Чтобы получить маркер доступа Azure AD, который вы передадите в API, необходимо иметь в наличии идентификатор владельца, идентификатор клиента и ключ.
    > [!NOTE]
    > Эту операцию необходимо выполнить только один раз. После того как вы получите идентификатор владельца, идентификатор клиента и ключ, их можно будет использовать повторно в любое время для создания нового маркера доступа Azure AD.

Чтобы привязать приложение Azure AD к учетной записи Центра разработки и получить необходимые значения, выполните следующие действия.

1.  В Центре разработки перейдите в раздел **Параметры учетной записи**, щелкните **Управление пользователями** и [привяжите учетную запись своей организации в Центре разработки к каталогу Azure AD вашей организации](../publish/associate-azure-ad-with-dev-center.md).

2.  На странице **Управление пользователями** нажмите кнопку **Добавить приложения Azure AD**, добавьте приложение Azure AD, представляющее собой приложение или службу, которые вы будете использовать для доступа к аналитическим данным вашей учетной записи Центра разработки, и назначьте ему роль **Менеджер**. Если приложение уже есть в каталоге Azure AD, вы можете выбрать его на странице **Добавление приложений Azure AD**, чтобы добавить его к учетной записи Центра разработки. Также можно создать приложение Azure AD на странице **Добавление приложений Azure AD**. Дополнительные сведения см. в разделе [Добавление приложений Azure AD к учетной записи в Центре разработки](../publish/add-users-groups-and-azure-ad-applications.md#azure-ad-applications).

3.  Вернитесь на страницу **Управление пользователями**, щелкните имя приложения Azure AD, чтобы перейти к параметрам приложения и скопируйте **идентификатора владельца** и **идентификатор клиента**.

4. Нажмите кнопку **Добавить новый ключ**. На следующем экране скопируйте **ключ**. Вы не сможете получить доступ к этой информации после закрытия страницы. Дополнительные сведения см. в разделе [Управление ключами для приложения Azure AD](../publish/add-users-groups-and-azure-ad-applications.md#manage-keys).

<span id="obtain-an-azure-ad-access-token" />
## <a name="step-2-obtain-an-azure-ad-access-token"></a>Шаг 2. Получение маркера доступа Azure AD

Перед тем как можно будет вызвать любой из методов в API аналитики дляМагазина Windows, сначала необходимо получить маркер доступа Azure AD и передать его в заголовок **Авторизация** каждого метода в API. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера вы можете обновить его, чтобы дальше использовать в последующих вызовах к API.

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
## <a name="step-3-call-the-windows-store-analytics-api"></a>Шаг 3. Вызов интерфейса API аналитики Магазина Windows

После получения маркера доступа Azure AD вы можете вызвать интерфейс analytics API Магазина Windows. Сведения о синтаксисе каждого метода см. в следующих статьях. Необходимо передать маркер доступа в заголовок **Authorization** каждого метода.

| Сценарий       | Методы      |
|---------------|--------------------|
| Приобретение, преобразования и установка |  <ul><li>[Получение сведений о покупках приложения](get-app-acquisitions.md)</li><li>[Получение данных воронки приобретений приложения](get-acquisition-funnel-data.md)</li><li>[Получение преобразований приложения по каналу](get-app-conversions-by-channel.md)</li><li>[Получение сведений о приобретениях надстройки](get-in-app-acquisitions.md)</li><li>[Получение преобразований надстройки по каналу](get-add-on-conversions-by-channel.md)</li><li>[Получение сведений об установках приложения](get-app-installs.md)</li></ul> |
| Ошибки приложения | <ul><li>[Получение данных отчетов об ошибках](get-error-reporting-data.md)</li><li>[Получение сведений об ошибке в приложении](get-details-for-an-error-in-your-app.md)</li><li>[Получение трассировки стека при возникновении ошибки в приложении](get-the-stack-trace-for-an-error-in-your-app.md)</li></ul> |
| Оценки и отзывы | <ul><li>[Получение сведений об оценках приложения](get-app-ratings.md)</li><li>[Получение отзывов о приложении](get-app-reviews.md)</li></ul> |
| Реклама в приложении и рекламные кампании | <ul><li>[Получение данных об эффективности рекламы](get-ad-performance-data.md)</li><li>[Получение данных об эффективности рекламной кампании](get-ad-campaign-performance-data.md)</li></ul> |

Для учетных записей разработчиков, которые связаны с [программой Центра разработки оборудования для Windows](https://msdn.microsoft.com/windows/hardware/drivers/dashboard/get-started-with-the-hardware-dashboard), доступны следующие дополнительные методы.

| Сценарий       | Методы      |
|---------------|--------------------|
| Ошибки в драйверах для Windows 10 (для независимых поставщиков оборудования) |  <ul><li>[Получение данных системы отчетов об ошибках для драйверов для Windows10](get-error-reporting-data-for-windows-10-drivers.md)</li><li>[Получение сведений об ошибке драйвера для Windows10](get-details-for-a-windows-10-driver-error.md)</li><li>[Скачивание CAB-файла для ошибки драйвера для Windows10](download-the-cab-file-for-a-windows-10-driver-error.md)</li></ul> |
| Ошибки в драйверах для Windows7 или Windows8.x (для независимых поставщиков оборудования) |  <ul><li>[Получение данных системы отчетов для драйверов для Windows7 и Windows8.x](get-error-reporting-data-for-windows-7-and-windows-8.x-drivers.md)</li><li>[Получение сведений об ошибке драйвера для Windows7 или Windows 8.x](get-details-for-a-windows-7-or-windows-8.x-driver-error.md)</li><li>[Скачивание CAB-файла для ошибки драйвера для Windows7 или Windows8.x](download-the-cab-file-for-a-windows-7-or-windows-8.x-driver-error.md)</li></ul> |
| Ошибки оборудования (для изготовителей оборудования) |  <ul><li>[Получение данных системы отчетов об ошибках оборудования OEM](get-oem-hardware-error-reporting-data.md)</li><li>[Получение сведений об ошибке оборудования OEM](get-details-for-an-oem-hardware-error.md)</li><li>[Скачивание CAB-файла для ошибки оборудования OEM](download-the-cab-file-for-an-oem-hardware-error.md)</li></ul> |

## <a name="code-example"></a>Пример кода

В следующем примере кода показано, как получить маркер доступа Azure AD и вызвать интерфейс analytics API Магазина Windows из консольного приложения C#. Чтобы использовать этот пример кода, назначьте переменным *tenantId*, *clientId*, *clientSecret* и *appID* соответствующие вашему сценарию значения. В этом примере для десериализации данных JSON, возвращаемых интерфейсом analytics API Магазина Windows, требуется [пакет Json.NET](http://www.newtonsoft.com/json).

> [!div class="tabbedCodeSnippets"]
[!code-cs[AnalyticsApi](./code/StoreServicesExamples_Analytics/cs/Program.cs#AnalyticsApiExample)]

## <a name="error-responses"></a>Ошибочные ответы

API аналитики для Магазина Windows возвращает ошибочные ответы в объекте JSON, который содержит коды ошибок и сообщения. В следующем примере демонстрируется ошибочный ответ, вызванный недопустимым параметром.

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
