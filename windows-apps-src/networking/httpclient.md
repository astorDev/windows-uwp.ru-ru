---
description: Используйте HttpClient и остаток API пространства имен Windows.Web.Http для отправки и получения данных по протоколам HTTP 2.0 и HTTP 1.1.
title: HttpClient
ms.assetid: EC9820D3-3A46-474F-8A01-AE1C27442750
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: dd4b8c137d65339701b40027bb3230162e2c2456
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57620479"
---
# <a name="httpclient"></a>HttpClient

**Важные API**

-   [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639)
-   [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692)
-   [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631)

Используйте [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) и остаток API пространства имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) для отправки и получения данных по протоколам HTTP 2.0 и HTTP 1.1.

## <a name="overview-of-httpclient-and-the-windowswebhttp-namespace"></a>Обзор HttpClient и пространства имен Windows.Web.Http

Классы в пространстве имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) и связанных с ним пространствах имен [**Windows.Web.Http.Headers**](https://msdn.microsoft.com/library/windows/apps/dn252713) и [**Windows.Web.Http.Filters**](https://msdn.microsoft.com/library/windows/apps/dn298623) предоставляют программный интерфейс для приложений UWP, действующих как клиент HTTP, для выполнения базовых запросов GET или реализации более сложных функций HTTP, перечисленных ниже.

-   Методы для распространенных команд (**DELETE**, **GET**, **PUT** и **POST**). Отправка каждого из этих запросов представляет собой асинхронную операцию.

-   поддержка распространенных параметров и схем проверки подлинности;

-   доступ к сведениям о передаче по протоколу SSL;

-   возможность включать пользовательские фильтры в расширенные приложения;

-   возможность получать, задавать и удалять файлы cookie;

-   информация о ходе выполнения HTTP-запроса доступна для асинхронных методов.

Класс [**Windows.Web.Http.HttpRequestMessage**](https://msdn.microsoft.com/library/windows/apps/dn279617) представляет сообщение HTTP-запроса, отправляемое объектом [**Windows.Web.Http.HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639). Класс [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631) представляет сообщение HTTP-ответа, полученного в ответ на HTTP-запрос. Сообщения HTTP определены IETF в [RFC 2616](https://go.microsoft.com/fwlink/p/?linkid=241642).

Пространство имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) представляет содержимое HTTP в виде основного текста и заголовков содержимого, включая файлы cookie. Содержимое HTTP может связываться с HTTP-запросом или HTTP-ответом. В пространстве имен **Windows.Web.Http** доступно несколько классов для представления содержимого HTTP.

-   [**HttpBufferContent**](https://msdn.microsoft.com/library/windows/apps/dn298625). Содержимое в виде буфера
-   [**HttpFormUrlEncodedContent**](https://msdn.microsoft.com/library/windows/apps/dn298685). Содержимое как кортежи "имя-значение", закодированные типом MIME **application/x-www-form-urlencoded**
-   [**HttpMultipartContent**](https://msdn.microsoft.com/library/windows/apps/dn298708). Содержимое в виде **составного /\***  тип MIME.
-   [**HttpMultipartFormDataContent**](https://msdn.microsoft.com/library/windows/apps/dn279596). Содержимое, закодированное как тип MIME **multipart/form-data**.
-   [**HttpStreamContent**](https://msdn.microsoft.com/library/windows/apps/dn279649). Содержимое как поток (внутренний тип, который используется HTTP-методом GET для получения данных и HTTP-методом POST для отправки данных)
-   [**HttpStringContent**](https://msdn.microsoft.com/library/windows/apps/dn279661). Содержимое в виде строки.
-   [**IHttpContent** ](https://msdn.microsoft.com/library/windows/apps/dn279684) -базовый интерфейс для разработчикам создавать свои собственные объекты содержимого

Фрагмент кода в разделе "Отправка простого запроса GET через HTTP" использует класс [**HttpStringContent**](https://msdn.microsoft.com/library/windows/apps/dn279661) для представления ответа HTTP из запроса GET HTTP в виде строки.

Пространство имен [**Windows.Web.Http.Headers**](https://msdn.microsoft.com/library/windows/apps/dn252713) поддерживает создание заголовков HTTP и файлов cookie, которые затем связываются в качестве свойств с объектами [**HttpRequestMessage**](https://msdn.microsoft.com/library/windows/apps/dn279617) и [**HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631).

## <a name="send-a-simple-get-request-over-http"></a>Отправка простого запроса GET через HTTP

Как упоминалось ранее в этой статье, пространство имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) позволяет приложениям UWP отправлять запросы GET. В следующем фрагменте кода показано, как отправить запрос GET к http://www.contoso.com с помощью [ **Windows.Web.Http.HttpClient** ](https://msdn.microsoft.com/library/windows/apps/dn298639) класс и [  **Windows.Web.Http.HttpResponseMessage** ](https://msdn.microsoft.com/library/windows/apps/dn279631) класс для чтения ответа из запроса GET.

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

## <a name="post-binary-data-over-http"></a>Учет двоичных данных по протоколу HTTP

[C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis) приведенном ниже примере кода показано, используя данные формы и запрос POST для отправки небольшой объем двоичных данных как передачу файла на веб-сервере. Код использует [ **HttpBufferContent** ](/uwp/api/windows.web.http.httpbuffercontent) класс для представления двоичных данных и [ **HttpMultipartFormDataContent** ](/uwp/api/windows.web.http.httpmultipartformdatacontent) класс представления форм с несколькими частями данных.

> [!NOTE]
> Вызов **получить** (как показано в следующем примере кода) не подходит для потока пользовательского интерфейса. Правильный способ использования в этом случае, см. в разделе [параллелизма и асинхронные операции с использованием C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/concurrency).

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

    Windows::Web::Http::HttpClient httpClient;

    Uri requestUri{ L"https://www.contoso.com/post" };

    Windows::Web::Http::HttpMultipartFormDataContent postContent;
    Windows::Web::Http::Headers::HttpContentDispositionHeaderValue disposition{ L"form-data" };
    postContent.Headers().ContentDisposition(disposition);
    // The 'name' directive contains the name of the form field representing the data.
    disposition.Name(L"fileForUpload");
    // Here, the 'filename' directive is used to indicate to the server a file name
    // to use to save the uploaded data.
    disposition.FileName(L"file.dat");

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

    postContent.Add(binaryContent); // Add the binary data content as a part of the form data content.

    // Send the POST request asynchronously, and retrieve the response as a string.
    Windows::Web::Http::HttpResponseMessage httpResponseMessage;
    std::wstring httpResponseBody;

    try
    {
        // Send the POST request.
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

Чтобы ПЕРЕДАТЬ содержимое реальных двоичных файлов (а не явных двоичные данные, используемые выше), вы найдете его проще в использовании [HttpStreamContent](/uwp/api/windows.web.http.httpstreamcontent) объекта. Конструирует и, в качестве аргумента в его конструктор, передайте значение, возвращенное из вызова [StorageFile.OpenReadAsync](/uwp/api/windows.storage.storagefile.openreadasync). Этот метод возвращает поток для данных в двоичном файле.

Кроме того, если вы загружаете больших файлов (размером более около 10 МБ), затем мы рекомендуем использовать среды выполнения Windows [фоновой передачи](/uwp/api/windows.networking.backgroundtransfer) API-интерфейсы.

## <a name="exceptions-in-windowswebhttp"></a>Исключения в Windows.Web.Http

Исключение создается, если конструктору для объекта [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) передается неправильная строка универсального кода ресурса (URI).

**.NET:**   [ **Windows.Foundation.Uri** ](https://msdn.microsoft.com/library/windows/apps/br225998) тип отображается как [ **System.Uri** ](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) в C# и VISUAL BASIC.

В C# и Visual Basic можно избежать этой ошибки, используя класс [**System.Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) из платформы .NET 4.5 и один из методов [**System.Uri.TryCreate**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.trycreate.aspx), чтобы перед составлением URI проверить строку, полученную от пользователя приложения.

В C++ нет метода для преобразования строки в код URI. Если пользователь вводит в приложение данные [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998), конструктор должен находиться в блоке try/catch. Если вызывается исключение, приложение может уведомить об этом пользователя и запросить новое имя узла.

В пространстве имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) отсутствует удобная функция для обработки исключений. Поэтому приложение, использующее класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) и другие классы из этого пространства имен, должно использовать значение **HRESULT**.

В приложениях с помощью .NET Framework 4.5 в C#, VB.NET, [System.Exception](https://msdn.microsoft.com/library/system.exception.aspx) представляет ошибку во время выполнения приложения при возникновении исключения. Свойство [System.Exception.HResult](https://msdn.microsoft.com/library/system.exception.hresult.aspx) возвращает значение **HRESULT**, назначенное определенному исключению. Свойство [System.Exception.Message](https://msdn.microsoft.com/library/system.exception.message.aspx) возвращает сообщение с описанием исключения. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Приложение может фильтровать полученные данные по определенному значению перечисления **HRESULT**, чтобы действовать в зависимости от причины исключения.

В приложениях на управляемом C++ объект [Platform::Exception](https://msdn.microsoft.com/library/windows/apps/hh755825.aspx) представляет ошибку во время выполнения приложения, когда возникает исключение. Свойство [Platform::Exception::HResult](https://msdn.microsoft.com/library/windows/apps/hh763371.aspx) возвращает значение **HRESULT**, назначенное определенному исключению. Свойство [Platform::Exception::Message](https://msdn.microsoft.com/library/windows/apps/hh763375.aspx) возвращает строку, которая предоставляется системой и связывается со значением **HRESULT**. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Приложение может фильтровать полученные данные по определенному значению перечисления **HRESULT**, чтобы действовать в зависимости от причины исключения.

Для большинства ошибок проверки параметра **HRESULT** возвращаемый **E\_INVALIDARG**. Для некоторых вызовов недопустимый метод **HRESULT** возвращаемый **E\_НЕДОПУСТИМЫЙ\_МЕТОД\_вызвать**.

