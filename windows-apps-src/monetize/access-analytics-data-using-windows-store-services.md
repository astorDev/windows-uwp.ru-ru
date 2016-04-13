---
ms.assetid: 4BF9EF21-E9F0-49DB-81E4-062D6E68C8B1
description: Используйте интерфейс analytics API Магазина Windows для программного извлечения аналитических данных по приложениям, зарегистрированным на вашу учетную запись или учетную запись вашей организации в Центре разработки для Windows.
title: Доступ к аналитическим данным с помощью служб Магазина Windows
---

# Доступ к аналитическим данным с помощью служб Магазина Windows


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Используйте *интерфейс analytics API Магазина Windows* для программного извлечения аналитических данных по приложениям, зарегистрированным на вашу учетную запись или учетную запись вашей организации в Центре разработки для Windows. Этот интерфейс позволяет извлекать данные по приобретению приложений и IAP, ошибкам, оценкам приложений и отзывам. Для проверки подлинности вызовов из приложения или службы в этом интерфейсе используется служба Azure Active Directory (Azure AD).

## Необходимые условия для использования интерфейса analytics API Магазина Windows


-   У вас (или вашей организации) должна быть служба Azure AD Directory. Если вы уже используете Office 365 или другие бизнес-службы Майкрософт, то у вас уже есть Azure Active Directory. В противном случае можно [скачать ее бесплатно](http://go.microsoft.com/fwlink/p/?LinkId=703757).
-   Необходимо иметь [учетную запись пользователя](https://azure.microsoft.com/documentation/articles/active-directory-create-users/) в каталоге Azure AD, который вы хотите связать со своей учетной записью в Центре разработки для Windows.

## Использование интерфейса analytics API Магазина Windows


Прежде чем использовать интерфейс analytics API Магазина Windows, необходимо привязать приложение Azure AD к учетной записи Центра разработки и получить токен доступа Azure AD. Приложение Azure AD представляет собой приложение или службу, из которой отправляются вызовы в интерфейс analytics API Магазина Windows. Получив токен доступа, можно направлять вызовы в интерфейс analytics API Магазина Windows из приложения или службы.

Далее представлено пошаговое описание процесса.

1.  [Привязка приложения Azure AD к своей учетной записи в Центре разработки для Windows](#associate-an-azure-ad-application-with-your-windows-dev-center-account).
2.  [Получение токена доступа Azure AD](#obtain-an-azure-ad-access-token).
3.  [Вызов интерфейса analytics API Магазина Windows](#call-the-windows-store-analytics-api).


### Привязка приложения Azure AD к своей учетной записи в Центре разработки для Windows

1.  В Центре разработки перейдите в раздел **Параметры учетной записи**, щелкните **Управление пользователями** и привяжите учетную запись своей организации в Центре разработки к каталогу Azure AD вашей организации. Подробные инструкции: [Управление учетными записями пользователей](https://msdn.microsoft.com/library/windows/apps/mt489008). При необходимости можно добавить других пользователей из каталога Azure AD организации, чтобы они получили доступ к вашей учетной записи в Центре разработки.

    **Примечание** Связать с каталогом Azure Active Directory можно только одну учетную запись в Центре разработки. Аналогичным образом, только один каталог Azure Active Directory можно связать с учетной записью Центра разработки. Установив эту связь, вы не сможете ее удалить без помощи службы поддержки.

     

2.  На странице **Управление пользователями** нажмите кнопку **Добавить приложения Azure AD**, добавьте приложение Azure AD, которое вы будете использовать для доступа к аналитическим данным вашей учетной записи Центра разработки, и назначьте ему роль **Менеджер**. Если приложение уже есть в каталоге Azure AD, вы можете выбрать его на странице **Добавление приложений Azure AD**, чтобы добавить его к учетной записи Центра разработки. В противном случае можно создать приложение Azure AD на странице **Добавление приложений Azure AD**. Дополнительные сведения об управлении приложениями Azure AD см. в разделе [Управление учетными записями пользователей](https://msdn.microsoft.com/library/windows/apps/mt489008).

3.  Вернитесь на страницу **Управления пользователями**, щелкните имя вашего приложения в Azure AD, чтобы перейти к его параметрам, и нажмите кнопку **Добавить новый ключ**. На следующем экране скопируйте значения полей **Код клиента** и **Ключ**. Дополнительные сведения об управлении приложениями Azure AD см. в разделе [Управление учетными записями пользователей](https://msdn.microsoft.com/library/windows/apps/mt489008). Идентификатор клиента и ключ необходимы, чтобы получить маркер доступа Azure AD для вызова analytics API Магазина Windows. Вы не сможете получить доступ к этой информации после закрытия страницы.


### Получение токена доступа Azure AD

После привязки приложения Azure AD к учетной записи в Центре разработки и получения кода клиента и ключа для приложения эти данные можно использовать, чтобы получить токен доступа Azure AD. Токен доступа необходим для вызова любых методов интерфейса analytics API Магазина Windows.

Для получения токена доступа следуйте инструкциям в разделе [Вызовы между службами с помощью учетных данных клиентов](https://msdn.microsoft.com/library/azure/dn645543.aspx), чтобы отправить HTTP-запрос POST на следующую конечную точку Azure AD.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

-   Чтобы получить идентификатор клиента, выполните вход на [портал управления Azure Management Portal](http://manage.windowsazure.com/), перейдите в раздел **Active Directory** и щелкните каталог, который вы ранее привязали к своей учетной записи в Центре разработки. Идентификатор клиента для этого каталога является частью URL-адреса этой страницы, как показано в строке *your\_tenant\_ID* (см. пример ниже).

  ```
  https://manage.windowsazure.com/@<your_tenant_name>#Workspaces/ActiveDirectoryExtension/Directory/<your_tenant_ID>/directoryQuickStart
  ```

-   Для параметров *client\_id* и *client\_secret* укажите идентификатор клиента и ключ для вашего приложения, который вы ранее получили в Центре разработки.
-   Для параметра *resource* укажите следующий универсальный код ресурса (URI): ```https://manage.devcenter.microsoft.com```.


### Вызов интерфейса analytics API Магазина Windows

После получения маркера доступа Azure AD вы можете вызвать интерфейс analytics API Магазина Windows. Сведения о синтаксисе каждого метода см. в следующих статьях. Необходимо передать токен доступа в заголовок **Authorization** каждого метода.

-   [Получение сведений о приобретении приложений](get-app-acquisitions.md)
-   [Получение сведений о приобретении IAP](get-in-app-acquisitions.md)
-   [Получение данных отчетов об ошибках](get-error-reporting-data.md)
-   [Получение оценок приложений](get-app-ratings.md)
-   [Получение отзывов о приложениях](get-app-reviews.md)

## Пример кода


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

            // This is your app's product ID. This ID is embedded in the app's listing link
            // on the App identity page of the Dev Center dashboard.
            string appID = "<your app's product ID>";

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

            //// Get IAP acquisitions
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

## Ошибочные ответы


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

## Связанные темы

* [Получение сведений о приобретении приложений](get-app-acquisitions.md)
* [Получение сведений о приобретении IAP](get-in-app-acquisitions.md)
* [Получение данных отчетов об ошибках](get-error-reporting-data.md)
* [Получение оценок приложений](get-app-ratings.md)
* [Получение отзывов о приложении](get-app-reviews.md)
 


<!--HONumber=Mar16_HO3-->


