---
author: stevewhims
description: Используйте HttpClient и остаток API пространства имен Windows.Web.Http для отправки и получения данных по протоколам HTTP2.0 и HTTP1.1.
title: HttpClient
ms.assetid: EC9820D3-3A46-474F-8A01-AE1C27442750
ms.author: stwhi
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c874c690826dfa74b8dcb2312204cd549db3db2b
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "5824017"
---
# <a name="httpclient"></a>HttpClient


**Важные API**

-   [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639)
-   [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692)
-   [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631)

Используйте [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) и остаток API пространства имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) для отправки и получения данных по протоколам HTTP2.0 и HTTP1.1.

## <a name="overview-of-httpclient-and-the-windowswebhttp-namespace"></a>Обзор HttpClient и пространства имен Windows.Web.Http

Классы в пространстве имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) и связанных с ним пространствах имен [**Windows.Web.Http.Headers**](https://msdn.microsoft.com/library/windows/apps/dn252713) и [**Windows.Web.Http.Filters**](https://msdn.microsoft.com/library/windows/apps/dn298623) предоставляют программный интерфейс для приложений UWP, действующих как клиент HTTP, для выполнения базовых запросов GET или реализации более сложных функций HTTP, перечисленных ниже.

-   Методы для распространенных команд (**DELETE**, **GET**, **PUT** и **POST**). Отправка каждого из этих запросов представляет собой асинхронную операцию.

-   поддержка распространенных параметров и схем проверки подлинности;

-   доступ к сведениям о передаче по протоколу SSL;

-   возможность включать пользовательские фильтры в расширенные приложения;

-   возможность получать, задавать и удалять файлы cookie;

-   информация о ходе выполнения HTTP-запроса доступна для асинхронных методов.

Класс [**Windows.Web.Http.HttpRequestMessage**](https://msdn.microsoft.com/library/windows/apps/dn279617) представляет сообщение HTTP-запроса, отправляемое объектом [**Windows.Web.Http.HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639). Класс [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631) представляет сообщение HTTP-ответа, полученного в ответ на HTTP-запрос. Сообщения HTTP определены IETF в [RFC 2616](http://go.microsoft.com/fwlink/p/?linkid=241642).

Пространство имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) представляет содержимое HTTP в виде основного текста и заголовков содержимого, включая файлы cookie. Содержимое HTTP может связываться с HTTP-запросом или HTTP-ответом. В пространстве имен **Windows.Web.Http** доступно несколько классов для представления содержимого HTTP.

-   [**HttpBufferContent**](https://msdn.microsoft.com/library/windows/apps/dn298625). Содержимое в виде буфера
-   [**HttpFormUrlEncodedContent**](https://msdn.microsoft.com/library/windows/apps/dn298685). Содержимое как кортежи "имя-значение", закодированные типом MIME **application/x-www-form-urlencoded**
-   [**HttpMultipartContent**](https://msdn.microsoft.com/library/windows/apps/dn298708). Содержимое в форме типа MIME **multipart/\***.
-   [**HttpMultipartFormDataContent**](https://msdn.microsoft.com/library/windows/apps/dn279596). Содержимое, закодированное как тип MIME **multipart/form-data**.
-   [**HttpStreamContent**](https://msdn.microsoft.com/library/windows/apps/dn279649). Содержимое как поток (внутренний тип, который используется HTTP-методом GET для получения данных и HTTP-методом POST для отправки данных)
-   [**HttpStringContent**](https://msdn.microsoft.com/library/windows/apps/dn279661). Содержимое в виде строки.
-   [**IHttpContent**](https://msdn.microsoft.com/library/windows/apps/dn279684)— это базовый интерфейс для разработчиков, создающих собственные объекты содержимого

Фрагмент кода в разделе "Отправка простого запроса GET через HTTP" использует класс [**HttpStringContent**](https://msdn.microsoft.com/library/windows/apps/dn279661) для представления ответа HTTP из запроса GET HTTP в виде строки.

Пространство имен [**Windows.Web.Http.Headers**](https://msdn.microsoft.com/library/windows/apps/dn252713) поддерживает создание заголовков HTTP и файлов cookie, которые затем связываются в качестве свойств с объектами [**HttpRequestMessage**](https://msdn.microsoft.com/library/windows/apps/dn279617) и [**HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631).

## <a name="send-a-simple-get-request-over-http"></a>Отправка простого запроса GET через HTTP

Как упоминалось ранее в этой статье, пространство имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) позволяет приложениям UWP отправлять запросы GET. В следующем фрагменте кода показано, как отправлять запрос GET для http://www.contoso.com с помощью класса [**Windows.Web.Http.HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) и класса [**Windows.Web.Http.HttpResponseMessage**](https://msdn.microsoft.com/library/windows/apps/dn279631) для чтения ответа из запроса GET.

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

## <a name="exceptions-in-windowswebhttp"></a>Исключения в Windows.Web.Http

Исключение создается, если конструктору для объекта [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) передается неправильная строка универсального кода ресурса (URI).

**.NET:** тип [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998) отображается как [**System.Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) в C# и VB.

В C# и Visual Basic можно избежать этой ошибки, используя класс [**System.Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) из платформы .NET 4.5 и один из методов [**System.Uri.TryCreate**](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.trycreate.aspx), чтобы перед составлением URI проверить строку, полученную от пользователя приложения.

В C++ нет метода для преобразования строки в код URI. Если пользователь вводит в приложение данные [**Windows.Foundation.Uri**](https://msdn.microsoft.com/library/windows/apps/br225998), конструктор должен находиться в блоке try/catch. Если вызывается исключение, приложение может уведомить об этом пользователя и запросить новое имя узла.

В пространстве имен [**Windows.Web.Http**](https://msdn.microsoft.com/library/windows/apps/dn279692) отсутствует удобная функция для обработки исключений. Поэтому приложение, использующее класс [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) и другие классы из этого пространства имен, должно использовать значение **HRESULT**.

В приложениях с помощью Framework4.5 .NET в C#, VB.NET, [System.Exception](http://msdn.microsoft.com/library/system.exception.aspx) представляет ошибку во время выполнения приложения при возникновении исключения. Свойство [System.Exception.HResult](http://msdn.microsoft.com/library/system.exception.hresult.aspx) возвращает значение **HRESULT**, назначенное определенному исключению. Свойство [System.Exception.Message](http://msdn.microsoft.com/library/system.exception.message.aspx) возвращает сообщение с описанием исключения. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Приложение может фильтровать полученные данные по определенному значению перечисления **HRESULT**, чтобы действовать в зависимости от причины исключения.

В приложениях на управляемом C++ объект [Platform::Exception](http://msdn.microsoft.com/library/windows/apps/hh755825.aspx) представляет ошибку во время выполнения приложения, когда возникает исключение. Свойство [Platform::Exception::HResult](http://msdn.microsoft.com/library/windows/apps/hh763371.aspx) возвращает значение **HRESULT**, назначенное определенному исключению. Свойство [Platform::Exception::Message](http://msdn.microsoft.com/library/windows/apps/hh763375.aspx) возвращает строку, которая предоставляется системой и связывается со значением **HRESULT**. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Приложение может фильтровать полученные данные по определенному значению перечисления **HRESULT**, чтобы действовать в зависимости от причины исключения.

Для многих ошибок при проверке параметров возвращаемым **HRESULT** является **E\_INVALIDARG**. Для некоторых непредусмотренных вызовов методов возвращаемым **HRESULT** является **E\_ILLEGAL\_METHOD\_CALL**.

