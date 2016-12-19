---
author: mcleanbyron
ms.assetid: 4BF9EF21-E9F0-49DB-81E4-062D6E68C8B1
description: "Используйте интерфейс analytics API Магазина Windows для программного извлечения аналитических данных по приложениям, зарегистрированным на вашу учетную запись или учетную запись вашей организации в Центре разработки для Windows."
title: "Доступ к аналитическим данным с помощью служб Магазина Windows"
translationtype: Human Translation
ms.sourcegitcommit: dcf4c263ff3fd8df846d1d5620ba31a9da7a5e6c
ms.openlocfilehash: 5ae5dcbe6684aa34a1428760cd5c7e9b8f599ebf

---

# <a name="access-analytics-data-using-windows-store-services"></a>Доступ к аналитическим данным с помощью служб Магазина Windows

Используйте *интерфейс analytics API Магазина Windows* для программного извлечения аналитических данных по приложениям, зарегистрированным на вашу учетную запись или учетную запись вашей организации в Центре разработки для Windows. Этот API позволяет извлекать данные о приобретении, ошибках, оценках и отзывов для приложения и надстройки (внутреннего продукта или IAP). Для проверки подлинности вызовов из приложения или службы в этом интерфейсе используется служба Azure Active Directory (Azure AD).

Далее описан весь процесс.

1.  Убедитесь, что вы выполнили все [необходимые условия](#prerequisites).
2.  Перед вызовом метода в API аналитики для Магазина Windows [получите маркер доступа Azure AD](#obtain-an-azure-ad-access-token). После получения маркера доступа у вас будет 60 минут, чтобы использовать его в вызовах к API аналитики для Магазина Windows до окончания срока действия маркера. После истечения срока действия маркера можно сформировать новый маркер.
3.  [Вызов интерфейса analytics API Магазина Windows](#call-the-windows-store-analytics-api).

<span id="prerequisites" />
## <a name="step-1-complete-prerequisites-for-using-the-windows-store-analytics-api"></a>Шаг 1. Выполнение необходимых условий для использования API аналитики для Магазина Windows

Перед тем как начать писать код для вызова API аналитики для Магазина Windows, убедитесь, что вы выполнили следующие необходимые условия.

* У вас (или у вашей организации) должен быть каталог Azure AD, а также разрешение [глобального администратора](http://go.microsoft.com/fwlink/?LinkId=746654) для этого каталога. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть Azure Active Directory. В противном случае можно [создать новый каталог Azure AD в Центре разработки](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users) без дополнительной платы.

* Вам необходимо привязать приложение Azure AD к учетной записи Центра разработки, получить идентификатор клиента для приложения и идентификатор клиента и создать ключ. Приложение Azure AD представляет собой приложение или службу, из которой отправляются вызовы в интерфейс analytics API Магазина Windows. Чтобы получить маркер доступа Azure AD, который вы передадите в API, необходимо иметь в наличии идентификатор владельца, идентификатор клиента и ключ.

  >**Примечание.**&nbsp;&nbsp;Эту операцию необходимо выполнить только один раз. После того как вы получите идентификатор владельца, идентификатор клиента и ключ, их можно будет использовать повторно в любое время для создания нового маркера доступа Azure AD.

Чтобы привязать приложение Azure AD к учетной записи Центра разработки и получить необходимые значения, выполните следующие действия.

1.  В Центре разработки перейдите в раздел **Параметры учетной записи**, щелкните **Управление пользователями** и привяжите учетную запись своей организации в Центре разработки к каталогу Azure AD вашей организации. Подробные инструкции см. в разделе [Управление учетными записями пользователей](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users).

2.  На странице **Управление пользователями** нажмите кнопку **Добавить приложения Azure AD**, добавьте приложение Azure AD, представляющее собой приложение или службу, которые вы будете использовать для доступа к аналитическим данным вашей учетной записи Центра разработки, и назначьте ему роль **Менеджер**. Если приложение уже есть в каталоге Azure AD, вы можете выбрать его на странице **Добавление приложений Azure AD**, чтобы добавить его к учетной записи Центра разработки. Также можно создать приложение Azure AD на странице **Добавление приложений Azure AD**. Дополнительные сведения см. в разделе [Добавление приложений Azure Active Directory и управление ими](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users#add-and-manage-azure-ad-applications).

3.  Вернитесь на страницу **Управление пользователями**, щелкните имя приложения Azure AD, чтобы перейти к параметрам приложения и скопируйте **идентификатора владельца** и **идентификатор клиента**.

4. Нажмите кнопку **Добавить новый ключ**. На следующем экране скопируйте **ключ**. Вы не сможете получить доступ к этой информации после закрытия страницы. Дополнительные сведения об управлении ключами см. в разделе [Добавление приложений Azure Active Directory и управление ими](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users#add-and-manage-azure-ad-applications).

<span id="obtain-an-azure-ad-access-token" />
## <a name="step-2-obtain-an-azure-ad-access-token"></a>Шаг 2. Получение маркера доступа Azure AD

Перед тем как можно будет вызвать любой из методов в API аналитики для Магазина Windows, сначала необходимо получить маркер доступа Azure AD и передать его в заголовок **Авторизация** каждого метода в API. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера вы можете обновить его, чтобы дальше использовать в последующих вызовах к API.

Для получения маркера доступа следуйте инструкциям в разделе [Вызовы между службами с помощью учетных данных клиентов](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-service-to-service/), чтобы отправить HTTP-запрос POST в конечную точку ```https://login.microsoftonline.com/<tenant_id>/oauth2/token```. Ниже приведен пример запроса.

```
POST https://login.microsoftonline.com/<your_tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource=https://manage.devcenter.microsoft.com
```

Для параметров *tenant\_id*, *client\_id* и *client\_secret* укажите идентификатор владельца, идентификатор клиента и ключ вашего приложения, которые вы получили из Центра разработки после выполнения инструкций в предыдущем разделе. Для параметра *resource* укажите следующий универсальный код ресурса (URI): ```https://manage.devcenter.microsoft.com```.

После истечения срока действия маркера доступа вы можете обновить его, следуя инструкциям, приведенным [здесь](https://azure.microsoft.com/documentation/articles/active-directory-protocols-oauth-code/#refreshing-the-access-tokens).

<span id="call-the-windows-store-analytics-api" />
## <a name="step-3-call-the-windows-store-analytics-api"></a>Шаг 3. Вызов интерфейса API аналитики Магазина Windows

После получения маркера доступа Azure AD вы можете вызвать интерфейс analytics API Магазина Windows. Сведения о синтаксисе каждого метода см. в следующих статьях. Необходимо передать токен доступа в заголовок **Authorization** каждого метода.

* [Получение сведений о покупках приложения](get-app-acquisitions.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Получение сведений об ошибке в приложении](get-details-for-an-error-in-your-app.md)
* [Получение трассировки стека при возникновении ошибки в приложении](get-the-stack-trace-for-an-error-in-your-app.md)
* [Получение сведений об оценках приложения](get-app-ratings.md)
* [Получение отзывов о приложении](get-app-reviews.md)
* [Получение данных об эффективности рекламы](get-ad-performance-data.md)
* [Получение данных об эффективности рекламной кампании](get-ad-campaign-performance-data.md)

## <a name="code-example"></a>Пример кода


В следующем примере кода показано, как получить маркер доступа Azure AD и вызвать интерфейс analytics API Магазина Windows из консольного приложения C#. Чтобы использовать этот пример кода, назначьте переменным *tenantId*, *clientId*, *clientSecret* и *appID* соответствующие вашему сценарию значения. В этом примере для десериализации данных JSON, возвращаемых интерфейсом analytics API Магазина Windows, требуется [пакет Json.NET](http://www.newtonsoft.com/json).

```CSharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace TestAnalyticsAPI
{
    class Program
    {
        static void Main(string[] args)
        {
            string tenantId = "<your tenant ID>";
            string clientId = "<your client ID>";
            string clientSecret = "<your secret>";

            string scope = "https://manage.devcenter.microsoft.com";

            // Retrieve an Azure AD access token
            string accessToken = GetClientCredentialAccessToken(
                    tenantId,
                    clientId,
                    clientSecret,
                    scope).Result;

            // This is your app's Store ID. This ID is available on
            // the App identity page of the Dev Center dashboard.
            string appID = "<your app's Store ID>";

            DateTime startDate = DateTime.Parse("08-01-2015");
            DateTime endDate = DateTime.Parse("11-01-2015");
            int pageSize = 1000;
            int startPageIndex = 0;

            // Call the Windows Store analytics API
            CallAnalyticsAPI(accessToken, appID, startDate, endDate, pageSize, startPageIndex);

            Console.Read();
        }

        private static void CallAnalyticsAPI(string accessToken, string appID, DateTime startDate, DateTime endDate, int top, int skip)
        {
            string requestURI;

            // Get app acquisitions
            requestURI = string.Format(
                "https://manage.devcenter.microsoft.com/v1.0/my/analytics/appacquisitions?applicationId={0}&startDate={1}&endDate={2}&top={3}&skip={4}",
                appID, startDate, endDate, top, skip);

            //// Get add-on acquisitions
            //requestURI = string.Format(
            //    "https://manage.devcenter.microsoft.com/v1.0/my/analytics/inappacquisitions?applicationId={0}&startDate={1}&endDate={2}&top={3}&skip={4}",
            //    appID, startDate, endDate, top, skip);

            //// Get app failures
            //requestURI = string.Format(
            //    "https://manage.devcenter.microsoft.com/v1.0/my/analytics/failurehits?applicationId={0}&startDate={1}&endDate={2}&top={3}&skip={4}",
            //    appID, startDate, endDate, top, skip);

            //// Get app ratings
            //requestURI = string.Format(
            //    "https://manage.devcenter.microsoft.com/v1.0/my/analytics/ratings?applicationId={0}&startDate={1}&endDate={2}top={3}&skip={4}",
            //    appID, startDate, endDate, top, skip);

            //// Get app reviews
            //requestURI = string.Format(
            //    "https://manage.devcenter.microsoft.com/v1.0/my/analytics/reviews?applicationId={0}&startDate={1}&endDate={2}&top={3}&skip={4}",
            //    appID, startDate, endDate, top, skip);

            HttpRequestMessage requestMessage = new HttpRequestMessage(HttpMethod.Get, requestURI);
            requestMessage.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            WebRequestHandler handler = new WebRequestHandler();
            HttpClient httpClient = new HttpClient(handler);

            HttpResponseMessage response = httpClient.SendAsync(requestMessage).Result;

            Console.WriteLine(response);
            Console.WriteLine(response.Content.ReadAsStringAsync().Result);

            response.Dispose();
        }

        public static async Task<string> GetClientCredentialAccessToken(string tenantId, string clientId, string clientSecret, string scope)
        {
            string tokenEndpointFormat = "https://login.microsoftonline.com/{0}/oauth2/token";
            string tokenEndpoint = string.Format(tokenEndpointFormat, tenantId);

            dynamic result;
            using (HttpClient client = new HttpClient())
            {
                string tokenUrl = tokenEndpoint;
                using (
                    HttpRequestMessage request = new HttpRequestMessage(
                        HttpMethod.Post,
                        tokenUrl))
                {
                    string content =
                        string.Format(
                            "grant_type=client_credentials&client_id={0}&client_secret={1}&resource={2}",
                            clientId,
                            clientSecret,
                            scope);

                    request.Content = new StringContent(content, Encoding.UTF8, "application/x-www-form-urlencoded");

                    using (HttpResponseMessage response = await client.SendAsync(request))
                    {
                        string responseContent = await response.Content.ReadAsStringAsync();
                        result = JsonConvert.DeserializeObject(responseContent);
                    }
                }
            }

            return result.access_token;
        }
    }
}
```

## <a name="error-responses"></a>Ошибочные ответы


Интерфейс analytics API Магазина Windows возвращает ошибочные ответы в объекте JSON, который содержит коды ошибок и сообщения. В следующем примере демонстрируется ошибочный ответ, вызванный недопустимым параметром.

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

## <a name="related-topics"></a>Связанные статьи

* [Получение сведений о покупках приложения](get-app-acquisitions.md)
* [Получение сведений о покупках надстройки](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Получение сведений об ошибке в приложении](get-details-for-an-error-in-your-app.md)
* [Получение трассировки стека при возникновении ошибки в приложении](get-the-stack-trace-for-an-error-in-your-app.md)
* [Получение сведений об оценках приложения](get-app-ratings.md)
* [Получение отзывов о приложении](get-app-reviews.md)
* [Получение данных об эффективности рекламы](get-ad-performance-data.md)
* [Получение данных об эффективности рекламной кампании](get-ad-campaign-performance-data.md)
 



<!--HONumber=Dec16_HO1-->


