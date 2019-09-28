---
description: Используйте HttpClient и остаток API пространства имен Windows.Web.Http для отправки и получения данных по протоколам HTTP 2.0 и HTTP 1.1.
title: HttpClient
ms.assetid: EC9820D3-3A46-474F-8A01-AE1C27442750
ms.date: 06/05/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8dd1fce4e8a5c18af93df32b12ce8b20c8bc69f9
ms.sourcegitcommit: a20457776064c95a74804f519993f36b87df911e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71340567"
---
# <a name="httpclient"></a>HttpClient

**Важные API**

-   [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient)
-   [**Windows.Web.Http**](https://docs.microsoft.com/uwp/api/Windows.Web.Http)
-   [**Windows.Web.Http.HttpResponseMessage**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpResponseMessage)

Используйте [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) и остаток API пространства имен [**Windows.Web.Http**](https://docs.microsoft.com/uwp/api/Windows.Web.Http) для отправки и получения данных по протоколам HTTP 2.0 и HTTP 1.1.

## <a name="overview-of-httpclient-and-the-windowswebhttp-namespace"></a>Обзор HttpClient и пространства имен Windows.Web.Http

Классы в пространстве имен [**Windows.Web.Http**](https://docs.microsoft.com/uwp/api/Windows.Web.Http) и связанных с ним пространствах имен [**Windows.Web.Http.Headers**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.Headers) и [**Windows.Web.Http.Filters**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.Filters) предоставляют программный интерфейс для приложений UWP, действующих как клиент HTTP, для выполнения базовых запросов GET или реализации более сложных функций HTTP, перечисленных ниже.

-   Методы для распространенных команд (**DELETE**, **GET**, **PUT** и **POST**). Отправка каждого из этих запросов представляет собой асинхронную операцию.

-   поддержка распространенных параметров и схем проверки подлинности;

-   доступ к сведениям о передаче по протоколу SSL;

-   возможность включать пользовательские фильтры в расширенные приложения;

-   возможность получать, задавать и удалять файлы cookie;

-   информация о ходе выполнения HTTP-запроса доступна для асинхронных методов.

Класс [**Windows.Web.Http.HttpRequestMessage**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpRequestMessage) представляет сообщение HTTP-запроса, отправляемое объектом [**Windows.Web.Http.HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient). Класс [**Windows.Web.Http.HttpResponseMessage**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpResponseMessage) представляет сообщение HTTP-ответа, полученного в ответ на HTTP-запрос. Сообщения HTTP определены IETF в [RFC 2616](https://go.microsoft.com/fwlink/p/?linkid=241642).

Пространство имен [**Windows.Web.Http**](https://docs.microsoft.com/uwp/api/Windows.Web.Http) представляет содержимое HTTP в виде основного текста и заголовков содержимого, включая файлы cookie. Содержимое HTTP может связываться с HTTP-запросом или HTTP-ответом. В пространстве имен **Windows.Web.Http** доступно несколько классов для представления содержимого HTTP.

-   [**HttpBufferContent**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpBufferContent). Содержимое в виде буфера
-   [**HttpFormUrlEncodedContent**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpFormUrlEncodedContent). Содержимое как кортежи "имя-значение", закодированные типом MIME **application/x-www-form-urlencoded**
-   [**HttpMultipartContent**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpMultipartContent). Содержимое в виде типа MIME **multipart/\*** .
-   [**HttpMultipartFormDataContent**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpMultipartFormDataContent). Содержимое, закодированное как тип MIME **multipart/form-data**.
-   [**HttpStreamContent**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpStreamContent). Содержимое как поток (внутренний тип, который используется HTTP-методом GET для получения данных и HTTP-методом POST для отправки данных)
-   [**HttpStringContent**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpStringContent). Содержимое в виде строки.
-   [**IHttpContent**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.IHttpContent) — базовый интерфейс для разработчиков, создающих собственные объекты содержимого.

Фрагмент кода в разделе "Отправка простого запроса GET через HTTP" использует класс [**HttpStringContent**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpStringContent) для представления ответа HTTP из запроса GET HTTP в виде строки.

Пространство имен [**Windows.Web.Http.Headers**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.Headers) поддерживает создание заголовков HTTP и файлов cookie, которые затем связываются в качестве свойств с объектами [**HttpRequestMessage**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpRequestMessage) и [**HttpResponseMessage**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpResponseMessage).

## <a name="send-a-simple-get-request-over-http"></a>Отправка простого запроса GET через HTTP

Как упоминалось ранее в этой статье, пространство имен [**Windows.Web.Http**](https://docs.microsoft.com/uwp/api/Windows.Web.Http) позволяет приложениям UWP отправлять запросы GET. Следующий фрагмент кода демонстрирует, как отправлять запрос GET на адрес http:\//www.contoso.com с помощью класса [**Windows.Web.Http.HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) и класса [**Windows.Web.Http.HttpResponseMessage**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpResponseMessage) для чтения ответа из запроса GET.

```csharp
//Create an HTTP client object
Windows.Web.Http.HttpClient httpClient = new Windows.Web.Http.HttpClient();

//Add a user-agent header to the GET request. 
var headers = httpClient.DefaultRequestHeaders;

//The safe way to add a header value is to use the TryParseAdd method and verify the return value is true,
//especially if the header value is coming from user input.
string header = "ie";
if (!headers.UserAgent.TryParseAdd(header))
{
    throw new Exception("Invalid header value: " + header);
}

header = "Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)";
if (!headers.UserAgent.TryParseAdd(header))
{
    throw new Exception("Invalid header value: " + header);
}

Uri requestUri = new Uri("http://www.contoso.com");

//Send the GET request asynchronously and retrieve the response as a string.
Windows.Web.Http.HttpResponseMessage httpResponse = new Windows.Web.Http.HttpResponseMessage();
string httpResponseBody = "";

try
{
    //Send the GET request
    httpResponse = await httpClient.GetAsync(requestUri);
    httpResponse.EnsureSuccessStatusCode();
    httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
}
catch (Exception ex)
{
    httpResponseBody = "Error: " + ex.HResult.ToString("X") + " Message: " + ex.Message;
}
```

```cppwinrt
// pch.h
#pragma once
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Http.Headers.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"
#include <iostream>
using namespace winrt;
using namespace Windows::Foundation;

int main()
{
    init_apartment();

    // Create an HttpClient object.
    Windows::Web::Http::HttpClient httpClient;

    // Add a user-agent header to the GET request.
    auto headers{ httpClient.DefaultRequestHeaders() };

    // The safe way to add a header value is to use the TryParseAdd method, and verify the return value is true.
    // This is especially important if the header value is coming from user input.
    std::wstring header{ L"ie" };
    if (!headers.UserAgent().TryParseAdd(header))
    {
        throw L"Invalid header value: " + header;
    }

    header = L"Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)";
    if (!headers.UserAgent().TryParseAdd(header))
    {
        throw L"Invalid header value: " + header;
    }

    Uri requestUri{ L"http://www.contoso.com" };

    // Send the GET request asynchronously, and retrieve the response as a string.
    Windows::Web::Http::HttpResponseMessage httpResponseMessage;
    std::wstring httpResponseBody;

    try
    {
        // Send the GET request.
        httpResponseMessage = httpClient.GetAsync(requestUri).get();
        httpResponseMessage.EnsureSuccessStatusCode();
        httpResponseBody = httpResponseMessage.Content().ReadAsStringAsync().get();
    }
    catch (winrt::hresult_error const& ex)
    {
        httpResponseBody = ex.message();
    }
    std::wcout << httpResponseBody;
}
```

## <a name="post-binary-data-over-http"></a>Использование запроса POST для отправки двоичных данных по HTTP

Пример кода [C++/WinRT](/windows/uwp/cpp-and-winrt-apis) ниже демонстрирует использование данных формы и запроса POST для отправки небольшого объема двоичных данных в виде файла, передаваемого на веб-сервер. В этом коде класс [**HttpBufferContent**](/uwp/api/windows.web.http.httpbuffercontent) представляет двоичные данные, а класс [**HttpMultipartFormDataContent**](/uwp/api/windows.web.http.httpmultipartformdatacontent) — данные составной формы.

> [!NOTE]
> Использовать вызов **get** (как показано в примере кода ниже) недопустимо для потока пользовательского интерфейса. В таком случае используйте способ, описанный в статье [Concurrency and asynchronous operations with C++/WinRT](/windows/uwp/cpp-and-winrt-apis/concurrency) (Параллелизм и синхронные операций с помощью C++/WinRT).

```cppwinrt
// pch.h
#pragma once
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Security.Cryptography.h>
#include <winrt/Windows.Storage.Streams.h>
#include <winrt/Windows.Web.Http.Headers.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"
#include <iostream>
#include <sstream>
using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Storage::Streams;

int main()
{
    init_apartment();

    auto buffer{
        Windows::Security::Cryptography::CryptographicBuffer::ConvertStringToBinary(
            L"A sentence of text to encode into binary to serve as sample data.",
            Windows::Security::Cryptography::BinaryStringEncoding::Utf8
        )
    };
    Windows::Web::Http::HttpBufferContent binaryContent{ buffer };
    // You can use the 'image/jpeg' content type to represent any binary data;
    // it's not necessarily an image file.
    binaryContent.Headers().Append(L"Content-Type", L"image/jpeg");

    Windows::Web::Http::Headers::HttpContentDispositionHeaderValue disposition{ L"form-data" };
    binaryContent.Headers().ContentDisposition(disposition);
    // The 'name' directive contains the name of the form field representing the data.
    disposition.Name(L"fileForUpload");
    // Here, the 'filename' directive is used to indicate to the server a file name
    // to use to save the uploaded data.
    disposition.FileName(L"file.dat");

    Windows::Web::Http::HttpMultipartFormDataContent postContent;
    postContent.Add(binaryContent); // Add the binary data content as a part of the form data content.

    // Send the POST request asynchronously, and retrieve the response as a string.
    Windows::Web::Http::HttpResponseMessage httpResponseMessage;
    std::wstring httpResponseBody;

    try
    {
        // Send the POST request.
        Uri requestUri{ L"https://www.contoso.com/post" };
        Windows::Web::Http::HttpClient httpClient;
        httpResponseMessage = httpClient.PostAsync(requestUri, postContent).get();
        httpResponseMessage.EnsureSuccessStatusCode();
        httpResponseBody = httpResponseMessage.Content().ReadAsStringAsync().get();
    }
    catch (winrt::hresult_error const& ex)
    {
        httpResponseBody = ex.message();
    }
    std::wcout << httpResponseBody;
}
```

Чтобы передать содержимое двоичного файла (а не явные двоичные данные, как это было сделано выше) с помощью запроса POST, воспользуйтесь объектом [HttpStreamContent](/uwp/api/windows.web.http.httpstreamcontent). Создайте его и передайте значение, возвращенное вызовом, в виде аргумента для конструктора в метод [StorageFile.OpenReadAsync](/uwp/api/windows.storage.storagefile.openreadasync). Этот метод возвращает поток для данных в вашем двоичном файле.

Кроме того, если вы передаете файл большого размера (больше 10 МБ), мы рекомендуем использовать интерфейсы API среды выполнения Windows для [передачи в фоновом режиме](/uwp/api/windows.networking.backgroundtransfer).

## <a name="post-json-data-over-http"></a>Передача данных JSON с помощью запроса POST по HTTP

В следующем примере данные JSON передаются на конечную точку, а затем выводится текст ответа.

```cs
using System;
using System.Diagnostics;
using System.Threading.Tasks;
using Windows.Storage.Streams;
using Windows.Web.Http;

private async Task TryPostJsonAsync()
{
    try
    {
        // Construct the HttpClient and Uri. This endpoint is for test purposes only.
        HttpClient httpClient = new HttpClient();
        Uri uri = new Uri("https://www.contoso.com/post");

        // Construct the JSON to post.
        HttpStringContent content = new HttpStringContent(
            "{ \"firstName\": \"Eliot\" }",
            UnicodeEncoding.Utf8,
            "application/json");

        // Post the JSON and wait for a response.
        HttpResponseMessage httpResponseMessage = await httpClient.PostAsync(
            uri,
            content);

        // Make sure the post succeeded, and write out the response.
        httpResponseMessage.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponseMessage.Content.ReadAsStringAsync();
        Debug.WriteLine(httpResponseBody);
    }
    catch (Exception ex)
    {
        // Write out any exceptions.
        Debug.WriteLine(ex);
    }
}
```

## <a name="exceptions-in-windowswebhttp"></a>Исключения в Windows.Web.Http

Исключение создается, если конструктору для объекта [**Windows.Foundation.Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri) передается неправильная строка универсального кода ресурса (URI).

**.NET:**   тип [**Windows.Foundation.Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri) отображается в C# и VB как [**System.Uri**](https://docs.microsoft.com/dotnet/api/system.uri).

В C# и Visual Basic можно избежать этой ошибки, используя класс [**System.Uri**](https://docs.microsoft.com/dotnet/api/system.uri) из платформы .NET 4.5 и один из методов [**System.Uri.TryCreate**](https://docs.microsoft.com/dotnet/api/system.uri.trycreate#overloads), чтобы перед составлением URI проверить строку, полученную от пользователя приложения.

В C++ нет метода для преобразования строки в код URI. Если пользователь вводит в приложение данные [**Windows.Foundation.Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri), конструктор должен находиться в блоке try/catch. Если вызывается исключение, приложение может уведомить об этом пользователя и запросить новое имя узла.

В пространстве имен [**Windows.Web.Http**](https://docs.microsoft.com/uwp/api/Windows.Web.Http) отсутствует удобная функция для обработки исключений. Поэтому приложение, использующее класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) и другие классы из этого пространства имен, должно использовать значение **HRESULT**.

В приложениях, использующих .NET Framework 4.5 и написанных на C# или VB.NET, объект [System.Exception](https://docs.microsoft.com/dotnet/api/system.exception) представляет ошибку во время выполнения приложения, когда возникает исключение. Свойство [System.Exception.HResult](https://docs.microsoft.com/dotnet/api/system.exception.hresult#System_Exception_HResult) возвращает значение **HRESULT**, назначенное определенному исключению. Свойство [System.Exception.Message](https://docs.microsoft.com/dotnet/api/system.exception.message#System_Exception_Message) возвращает сообщение с описанием исключения. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Приложение может фильтровать полученные данные по определенному значению перечисления **HRESULT**, чтобы действовать в зависимости от причины исключения.

В приложениях на управляемом C++ объект [Platform::Exception](https://docs.microsoft.com/cpp/cppcx/platform-exception-class) представляет ошибку во время выполнения приложения, когда возникает исключение. Свойство [Platform::Exception::HResult](https://docs.microsoft.com/cpp/cppcx/platform-exception-class#hresult) возвращает значение **HRESULT**, назначенное определенному исключению. Свойство [Platform::Exception::Message](https://docs.microsoft.com/cpp/cppcx/platform-exception-class#message) возвращает строку, которая предоставляется системой и связывается со значением **HRESULT**. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Приложение может фильтровать полученные данные по определенному значению перечисления **HRESULT**, чтобы действовать в зависимости от причины исключения.

Для многих ошибок при проверке параметров **HRESULT** возвращает значение **E\_INVALIDARG**. Для некоторых непредусмотренных вызовов методов возвращаемым значением **HRESULT** будет **E\_ILLEGAL\_METHOD\_CALL**.

