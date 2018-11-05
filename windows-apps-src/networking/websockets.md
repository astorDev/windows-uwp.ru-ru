---
author: stevewhims
description: Протокол WebSockets предоставляет механизм быстрого и безопасного двустороннего взаимодействия между клиентом и сервером через HTTP(S) и поддерживает и UTF-8, и двоичные сообщения.
title: WebSockets
ms.assetid: EAA9CB3E-6A3A-4C13-9636-CCD3DE46E7E2
ms.author: stwhi
ms.date: 06/04/2018
ms.topic: article
keywords: windows 10, uwp, сеть, websocket, messagewebsocket, streamwebsocket
ms.localizationpriority: medium
ms.openlocfilehash: eaba8d7b87d9e2762d13bd86629ee4a996e2d5e8
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6039438"
---
# <a name="websockets"></a>WebSockets
Протокол WebSockets предоставляет механизм быстрого и безопасного двустороннего взаимодействия между клиентом и сервером через HTTP(S) и поддерживает и UTF-8, и двоичные сообщения.

В [протоколе WebSocket](http://tools.ietf.org/html/rfc6455) данные немедленно передаются через полнодуплексное односокетное подключение, при этом сообщения могут отправляться и приниматься с обеих конечных точек в реальном времени. WebSocket отлично подходит для использования в многопользовательских играх (в режиме реального времени и в тех, где необходимо соблюдать очередь), мгновенные уведомления социальных сетей, отображение свежей информации о курсе акций или погоде, а также в других приложениях, для которых необходима безопасная и быстрая передача данных.

Чтобы установить соединение WebSocket, клиент и сервер обмениваются специальным подтверждением, основанным на протоколе HTTP. В случае успешного выполнения протокол прикладного уровня "обновляется" с HTTP до WebSocket, используя ранее установленное соединение TCP. После этого HTTP больше не задействован. Данные могут быть отправлены или получены с использованием протокола WebSocket любой конечной точкой в любое время до завершения подключения WebSocket.

**Примечание**. Клиент не может использовать WebSocket для передачи данных, если сервер не использует протокол WebSocket. Если сервер не поддерживает WebSocket, вам придется использовать другой метод передачи данных.

Универсальная платформа Windows (UWP) поддерживает использование WebSocket как клиентами, так и серверами. Пространство имен [**Windows.Networking.Sockets**](/uwp/api/windows.networking.sockets) определяет два класса WebSocket для использования клиентами&mdash;[**MessageWebSocket**](/uwp/api/windows.networking.sockets.messagewebsocket) и [**StreamWebSocket**](/uwp/api/windows.networking.sockets.streamwebsocket). Вот сравнение этих двух классов WebSocket.

| [MessageWebSocket](/uwp/api/windows.networking.sockets.messagewebsocket) | [StreamWebSocket](/uwp/api/windows.networking.sockets.streamwebsocket) |
| - | - |
| Все сообщение WebSocket считывается или записывается за одну операцию. | Части сообщения могут считываться в ходе каждой из операций чтения. |
| Подходит, если сообщения не очень большие. | Подходит для передачи очень больших файлов (например, фото или видео). |
| Поддерживает двоичные сообщения и сообщения в кодировке UTF-8. | Поддерживает только двоичные сообщения. |
| Аналогичны [UDP или сокету датаграмм](sockets.md#build-a-basic-udp-socket-client-and-server) (предназначены для частых, небольших сообщений), однако обладают надежностью, гарантированным порядком пакетов и контролем перегрузки, присущими TCP. | Аналогичны [TCP или сокету потока](sockets.md#build-a-basic-tcp-socket-client-and-server). |

## <a name="secure-your-connection-with-tlsssl"></a>Защита подключения с помощью TLS/SSL
В большинстве случаев необходимо использовать безопасное соединение WebSocket, чтобы зашифровать отправляемые и получаемые данные. Это также увеличивает шансы успешного подключения, так как многие посредники, такие как брандмауэры и прокси-серверы, отклоняют незашифрованные подключения WebSocket. [Протокол WebSocket](https://tools.ietf.org/html/rfc6455#section-3) определяет эти две схемы URI.

| Схема URI | Назначение |
| - | - |
| wss: | Используется для защищенных соединений, которые должны быть зашифрованы. |
| ws: | Используется для незашифрованных соединений. |

Чтобы зашифровать соединение WebSocket, воспользуйтесь схемой URI `wss:`. Вот пример.

```csharp
protected override async void OnNavigatedTo(NavigationEventArgs e)
{
    var webSocket = new Windows.Networking.Sockets.MessageWebSocket();
    await webSocket.ConnectAsync(new Uri("wss://www.contoso.com/mywebservice"));
}
```

```cppwinrt
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Networking.Sockets.h>
#include <winrt/Windows.UI.Xaml.Navigation.h>
#include <sstream>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::UI::Xaml::Navigation;
...
IAsyncAction OnNavigatedTo(NavigationEventArgs /* e */)
{
    Windows::Networking::Sockets::MessageWebSocket webSocket;
    co_await webSocket.ConnectAsync(Uri{ L"wss://www.contoso.com/mywebservice" });
}
```

## <a name="use-messagewebsocket-to-connect"></a>Использование сокета MessageWebSocket для подключения
[**MessageWebSocket**](/uwp/api/windows.networking.sockets.messagewebsocket) позволяет одной операцией прочитать/записать все сообщение WebSocket. Следовательно, его можно использовать, если сообщения небольшие. Этот класс поддерживает двоичные сообщения и сообщения в кодировке UTF-8.

В примере кода ниже эхо-сервер WebSocket.org&mdash; это служба, которая возвращает отправителю любое отправленное ей сообщение.

```csharp
private Windows.Networking.Sockets.MessageWebSocket messageWebSocket;

protected override void OnNavigatedTo(NavigationEventArgs e)
{
    this.messageWebSocket = new Windows.Networking.Sockets.MessageWebSocket();

    // In this example, we send/receive a string, so we need to set the MessageType to Utf8.
    this.messageWebSocket.Control.MessageType = Windows.Networking.Sockets.SocketMessageType.Utf8;

    this.messageWebSocket.MessageReceived += WebSocket_MessageReceived;
    this.messageWebSocket.Closed += WebSocket_Closed;

    try
    {
        Task connectTask = this.messageWebSocket.ConnectAsync(new Uri("wss://echo.websocket.org")).AsTask();
        connectTask.ContinueWith(_ => this.SendMessageUsingMessageWebSocketAsync("Hello, World!"));
    }
    catch (Exception ex)
    {
        Windows.Web.WebErrorStatus webErrorStatus = Windows.Networking.Sockets.WebSocketError.GetStatus(ex.GetBaseException().HResult);
        // Add additional code here to handle exceptions.
    }
}

private async Task SendMessageUsingMessageWebSocketAsync(string message)
{
    using (var dataWriter = new DataWriter(this.messageWebSocket.OutputStream))
    {
        dataWriter.WriteString(message);
        await dataWriter.StoreAsync();
        dataWriter.DetachStream();
    }
    Debug.WriteLine("Sending message using MessageWebSocket: " + message);
}

private void WebSocket_MessageReceived(Windows.Networking.Sockets.MessageWebSocket sender, Windows.Networking.Sockets.MessageWebSocketMessageReceivedEventArgs args)
{
    try
    {
        using (DataReader dataReader = args.GetDataReader())
        {
            dataReader.UnicodeEncoding = Windows.Storage.Streams.UnicodeEncoding.Utf8;
            string message = dataReader.ReadString(dataReader.UnconsumedBufferLength);
            Debug.WriteLine("Message received from MessageWebSocket: " + message);
            this.messageWebSocket.Dispose();
        }
    }
    catch (Exception ex)
    {
        Windows.Web.WebErrorStatus webErrorStatus = Windows.Networking.Sockets.WebSocketError.GetStatus(ex.GetBaseException().HResult);
        // Add additional code here to handle exceptions.
    }
}

private void WebSocket_Closed(Windows.Networking.Sockets.IWebSocket sender, Windows.Networking.Sockets.WebSocketClosedEventArgs args)
{
    Debug.WriteLine("WebSocket_Closed; Code: " + args.Code + ", Reason: \"" + args.Reason + "\"");
    // Add additional code here to handle the WebSocket being closed.
}
```

```cppwinrt
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Networking.Sockets.h>
#include <winrt/Windows.Storage.Streams.h>
#include <winrt/Windows.UI.Xaml.Navigation.h>
#include <sstream>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Storage::Streams;
using namespace Windows::UI::Xaml::Navigation;
...
private:
    Windows::Networking::Sockets::MessageWebSocket m_messageWebSocket;
    winrt::event_token m_messageReceivedEventToken;
    winrt::event_token m_closedEventToken;

public:
    IAsyncAction OnNavigatedTo(NavigationEventArgs /* e */)
    {
        // In this example, we send/receive a string, so we need to set the MessageType to Utf8.
        m_messageWebSocket.Control().MessageType(Windows::Networking::Sockets::SocketMessageType::Utf8);

        m_messageReceivedEventToken = m_messageWebSocket.MessageReceived({ this, &MessageWebSocketPage::OnWebSocketMessageReceived });
        m_closedEventToken = m_messageWebSocket.Closed({ this, &MessageWebSocketPage::OnWebSocketClosed });

        try
        {
            co_await m_messageWebSocket.ConnectAsync(Uri{ L"wss://echo.websocket.org" });
            SendMessageUsingMessageWebSocketAsync(L"Hello, World!");
        }
        catch (winrt::hresult_error const& ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus{ Windows::Networking::Sockets::WebSocketError::GetStatus(ex.to_abi()) };
            // Add additional code here to handle exceptions.
        }
    }

private:
    IAsyncAction SendMessageUsingMessageWebSocketAsync(std::wstring message)
    {
        DataWriter dataWriter{ m_messageWebSocket.OutputStream() };
        dataWriter.WriteString(message);

        co_await dataWriter.StoreAsync();
        dataWriter.DetachStream();
        std::wstringstream wstringstream;
        wstringstream << L"Sending message using MessageWebSocket: " << message.c_str() << std::endl;
        ::OutputDebugString(wstringstream.str().c_str());
    }

    void OnWebSocketMessageReceived(Windows::Networking::Sockets::MessageWebSocket const& /* sender */, Windows::Networking::Sockets::MessageWebSocketMessageReceivedEventArgs const& args)
    {
        try
        {
            DataReader dataReader{ args.GetDataReader() };

            dataReader.UnicodeEncoding(Windows::Storage::Streams::UnicodeEncoding::Utf8);
            auto message = dataReader.ReadString(dataReader.UnconsumedBufferLength());
            std::wstringstream wstringstream;
            wstringstream << L"Message received from MessageWebSocket: " << message.c_str() << std::endl;
            ::OutputDebugString(wstringstream.str().c_str());
            m_messageWebSocket.Close(1000, L"");
        }
        catch (winrt::hresult_error const& ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus{ Windows::Networking::Sockets::WebSocketError::GetStatus(ex.to_abi()) };
            // Add additional code here to handle exceptions.
        }
    }

    void OnWebSocketClosed(Windows::Networking::Sockets::IWebSocket const& /* sender */, Windows::Networking::Sockets::WebSocketClosedEventArgs const& args)
    {
        std::wstringstream wstringstream;
        wstringstream << L"WebSocket_Closed; Code: " << args.Code() << ", Reason: \"" << args.Reason().c_str() << "\"" << std::endl;
        ::OutputDebugString(wstringstream.str().c_str());
        // Add additional code here to handle the WebSocket being closed.
    }
```

```cpp
#include <ppltasks.h>
#include <sstream>
...
using namespace Windows::Foundation;
using namespace Windows::Storage::Streams;
using namespace Windows::UI::Xaml::Navigation;
...
private:
    Windows::Networking::Sockets::MessageWebSocket^ messageWebSocket;

protected:
    virtual void OnNavigatedTo(NavigationEventArgs^ e) override
    {
        this->messageWebSocket = ref new Windows::Networking::Sockets::MessageWebSocket();

        // In this example, we send/receive a string, so we need to set the MessageType to Utf8.
        this->messageWebSocket->Control->MessageType = Windows::Networking::Sockets::SocketMessageType::Utf8;

        this->messageWebSocket->MessageReceived += ref new TypedEventHandler<Windows::Networking::Sockets::MessageWebSocket^, Windows::Networking::Sockets::MessageWebSocketMessageReceivedEventArgs^>(this, &MessageWebSocketPage::WebSocket_MessageReceived);
        this->messageWebSocket->Closed += ref new TypedEventHandler<Windows::Networking::Sockets::IWebSocket^, Windows::Networking::Sockets::WebSocketClosedEventArgs^>(this, &MessageWebSocketPage::WebSocket_Closed);

        try
        {
            auto connectTask = Concurrency::create_task(this->messageWebSocket->ConnectAsync(ref new Uri(L"wss://echo.websocket.org")));
            connectTask.then([this] { this->SendMessageUsingMessageWebSocketAsync(L"Hello, World!"); });
        }
        catch (Platform::Exception^ ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus = Windows::Networking::Sockets::WebSocketError::GetStatus(ex->HResult);
            // Add additional code here to handle exceptions.
        }
    }

private:
    void SendMessageUsingMessageWebSocketAsync(Platform::String^ message)
    {
        auto dataWriter = ref new DataWriter(this->messageWebSocket->OutputStream);
        dataWriter->WriteString(message);

        Concurrency::create_task(dataWriter->StoreAsync()).then(
            [=](unsigned int)
        {
            dataWriter->DetachStream();
            std::wstringstream wstringstream;
            wstringstream << L"Sending message using MessageWebSocket: " << message->Data() << std::endl;
            ::OutputDebugString(wstringstream.str().c_str());
        });
    }

    void WebSocket_MessageReceived(Windows::Networking::Sockets::MessageWebSocket^ sender, Windows::Networking::Sockets::MessageWebSocketMessageReceivedEventArgs^ args)
    {
        try
        {
            DataReader^ dataReader = args->GetDataReader();

            dataReader->UnicodeEncoding = Windows::Storage::Streams::UnicodeEncoding::Utf8;
            Platform::String^ message = dataReader->ReadString(dataReader->UnconsumedBufferLength);
            std::wstringstream wstringstream;
            wstringstream << L"Message received from MessageWebSocket: " << message->Data() << std::endl;
            ::OutputDebugString(wstringstream.str().c_str());
            this->messageWebSocket->Close(1000, L"");
        }
        catch (Platform::Exception^ ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus = Windows::Networking::Sockets::WebSocketError::GetStatus(ex->HResult);
            // Add additional code here to handle exceptions.
        }
    }

    void WebSocket_Closed(Windows::Networking::Sockets::IWebSocket^ sender, Windows::Networking::Sockets::WebSocketClosedEventArgs^ args)
    {
        std::wstringstream wstringstream;
        wstringstream << L"WebSocket_Closed; Code: " << args->Code << ", Reason: \"" << args->Reason->Data() << "\"" << std::endl;
        ::OutputDebugString(wstringstream.str().c_str());
        // Add additional code here to handle the WebSocket being closed.
    }
```

### <a name="handle-the-messagewebsocketmessagereceived-and-messagewebsocketclosed-events"></a>Обработка событий MessageWebSocket.MessageReceived и MessageWebSocket.Closed
Как показано в приведенном выше примере, до установки подключения и отправки данных с помощью **MessageWebSocket** необходимо подписаться на события [**MessageWebSocket.MessageReceived**](/uwp/api/windows.networking.sockets.messagewebsocket.MessageReceived) и [**MessageWebSocket.Closed**](/uwp/api/windows.networking.sockets.messagewebsocket.Closed).
 
Событие **MessageReceived** создается при получении данных. Доступ к данным осуществляется через [**MessageWebSocketMessageReceivedEventArgs**](/uwp/api/windows.networking.sockets.messagewebsocketmessagereceivedeventargs). Событие **Closed** создается, когда клиент или сервер закрывает сокет.
 
### <a name="send-data-on-a-messagewebsocket"></a>Отправка данных в MessageWebSocket
После установки подключения можно отправлять данные на сервер. Это можно сделать с помощью свойства [**MessageWebSocket.OutputStream**](https://docs.microsoft.com/en-us/uwp/api/Windows.Networking.Sockets.MessageWebSocket.OutputStream) и [**DataWriter**](/uwp/api/windows.storage.streams.datawriter) для записи данных. 

**Примечание**. **DataWriter** становится владельцем потока вывода. Когда **DataWriter** выходит за пределы области, если к нему прикреплен поток вывода, **DataWriter** освобождает поток вывода. После этого все последующие попытки использовать поток вывода будут неудачными со значением HRESULT 0x80000013. Однако можно вызвать метод [**DataWriter.DetachStream**](/uwp/api/windows.storage.streams.datawriter.DetachStream), чтобы отсоединить поток вывода от **DataWriter** и вернуть владение потоком событию **MessageWebSocket**.

## <a name="use-streamwebsocket-to-connect"></a>Использование StreamWebSocket для подключения
[**StreamWebSocket**](/uwp/api/windows.networking.sockets.streamwebsocket) позволяет считывать части сообщения при каждой операции чтения. Следовательно, он подходит для передачи очень больших файлов (например, фото или видео). Этот класс поддерживает только двоичные сообщения.

В примере кода ниже эхо-сервер WebSocket.org&mdash; это служба, которая возвращает отправителю любое отправленное ей сообщение.

```csharp
private Windows.Networking.Sockets.StreamWebSocket streamWebSocket;

protected override void OnNavigatedTo(NavigationEventArgs e)
{
    this.streamWebSocket = new Windows.Networking.Sockets.StreamWebSocket();

    this.streamWebSocket.Closed += WebSocket_Closed;

    try
    {
        Task connectTask = this.streamWebSocket.ConnectAsync(new Uri("wss://echo.websocket.org")).AsTask();

        connectTask.ContinueWith(_ =>
        {
            Task.Run(() => this.ReceiveMessageUsingStreamWebSocket());
            Task.Run(() => this.SendMessageUsingStreamWebSocket(new byte[] { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09 }));
        });
    }
    catch (Exception ex)
    {
        Windows.Web.WebErrorStatus webErrorStatus = Windows.Networking.Sockets.WebSocketError.GetStatus(ex.GetBaseException().HResult);
        // Add code here to handle exceptions.
    }
}

private async void ReceiveMessageUsingStreamWebSocket()
{
    try
    {
        using (var dataReader = new DataReader(this.streamWebSocket.InputStream))
        {
            dataReader.InputStreamOptions = InputStreamOptions.Partial;
            await dataReader.LoadAsync(256);
            byte[] message = new byte[dataReader.UnconsumedBufferLength];
            dataReader.ReadBytes(message);
            Debug.WriteLine("Data received from StreamWebSocket: " + message.Length + " bytes");
        }
        this.streamWebSocket.Dispose();
    }
    catch (Exception ex)
    {
        Windows.Web.WebErrorStatus webErrorStatus = Windows.Networking.Sockets.WebSocketError.GetStatus(ex.GetBaseException().HResult);
        // Add code here to handle exceptions.
    }
}

private async void SendMessageUsingStreamWebSocket(byte[] message)
{
    try
    {
        using (var dataWriter = new DataWriter(this.streamWebSocket.OutputStream))
        {
            dataWriter.WriteBytes(message);
            await dataWriter.StoreAsync();
            dataWriter.DetachStream();
        }
        Debug.WriteLine("Sending data using StreamWebSocket: " + message.Length.ToString() + " bytes");
    }
    catch (Exception ex)
    {
        Windows.Web.WebErrorStatus webErrorStatus = Windows.Networking.Sockets.WebSocketError.GetStatus(ex.GetBaseException().HResult);
        // Add code here to handle exceptions.
    }
}

private void WebSocket_Closed(Windows.Networking.Sockets.IWebSocket sender, Windows.Networking.Sockets.WebSocketClosedEventArgs args)
{
    Debug.WriteLine("WebSocket_Closed; Code: " + args.Code + ", Reason: \"" + args.Reason + "\"");
    // Add additional code here to handle the WebSocket being closed.
}
```

```cppwinrt
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Networking.Sockets.h>
#include <winrt/Windows.Storage.Streams.h>
#include <winrt/Windows.UI.Xaml.Navigation.h>
#include <sstream>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Storage::Streams;
using namespace Windows::UI::Xaml::Navigation;
...
private:
    Windows::Networking::Sockets::StreamWebSocket m_streamWebSocket;
    winrt::event_token m_closedEventToken;

public:
    IAsyncAction OnNavigatedTo(NavigationEventArgs /* e */)
    {
        m_closedEventToken = m_streamWebSocket.Closed({ this, &StreamWebSocketPage::OnWebSocketClosed });

        try
        {
            co_await m_streamWebSocket.ConnectAsync(Uri{ L"wss://echo.websocket.org" });
            ReceiveMessageUsingStreamWebSocket();
            SendMessageUsingStreamWebSocket({ 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09 });
        }
        catch (winrt::hresult_error const& ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus{ Windows::Networking::Sockets::WebSocketError::GetStatus(ex.to_abi()) };
            // Add additional code here to handle exceptions.
        }
    }

private:
    IAsyncAction SendMessageUsingStreamWebSocket(std::vector< byte > message)
    {
        try
        {
            DataWriter dataWriter{ m_streamWebSocket.OutputStream() };
            dataWriter.WriteBytes(message);

            co_await dataWriter.StoreAsync();
            dataWriter.DetachStream();
            std::wstringstream wstringstream;
            wstringstream << L"Sending data using StreamWebSocket: " << message.size() << L" bytes" << std::endl;
            ::OutputDebugString(wstringstream.str().c_str());
        }
        catch (winrt::hresult_error const& ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus{ Windows::Networking::Sockets::WebSocketError::GetStatus(ex.to_abi()) };
            // Add additional code here to handle exceptions.
        }
    }

    IAsyncAction ReceiveMessageUsingStreamWebSocket()
    {
        try
        {
            DataReader dataReader{ m_streamWebSocket.InputStream() };
            dataReader.InputStreamOptions(InputStreamOptions::Partial);

            unsigned int bytesLoaded = co_await dataReader.LoadAsync(256);
            std::vector< byte > message(bytesLoaded);
            dataReader.ReadBytes(message);
            std::wstringstream wstringstream;
            wstringstream << L"Data received from StreamWebSocket: " << message.size() << " bytes" << std::endl;
            ::OutputDebugString(wstringstream.str().c_str());
            m_streamWebSocket.Close(1000, L"");
        }
        catch (winrt::hresult_error const& ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus{ Windows::Networking::Sockets::WebSocketError::GetStatus(ex.to_abi()) };
            // Add additional code here to handle exceptions.
        }
    }

    void OnWebSocketClosed(Windows::Networking::Sockets::IWebSocket const&, Windows::Networking::Sockets::WebSocketClosedEventArgs const& args)
    {
        std::wstringstream wstringstream;
        wstringstream << L"WebSocket_Closed; Code: " << args.Code() << ", Reason: \"" << args.Reason().c_str() << "\"" << std::endl;
        ::OutputDebugString(wstringstream.str().c_str());
        // Add additional code here to handle the WebSocket being closed.
    }
```

```cpp
#include <ppltasks.h>
#include <sstream>
...
using namespace Windows::Foundation;
using namespace Windows::Storage::Streams;
using namespace Windows::UI::Xaml::Navigation;
...
private:
    Windows::Networking::Sockets::StreamWebSocket^ streamWebSocket;

protected:
    virtual void OnNavigatedTo(NavigationEventArgs^ e) override
    {
        this->streamWebSocket = ref new Windows::Networking::Sockets::StreamWebSocket();

        this->streamWebSocket->Closed += ref new TypedEventHandler<Windows::Networking::Sockets::IWebSocket^, Windows::Networking::Sockets::WebSocketClosedEventArgs^>(this, &StreamWebSocketPage::WebSocket_Closed);

        try
        {
            auto connectTask = Concurrency::create_task(this->streamWebSocket->ConnectAsync(ref new Uri(L"wss://echo.websocket.org")));

            connectTask.then(
                [=]
            {
                this->ReceiveMessageUsingStreamWebSocket();
                this->SendMessageUsingStreamWebSocket(ref new Platform::Array< byte >{ 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09 });
            });
        }
        catch (Platform::Exception^ ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus = Windows::Networking::Sockets::WebSocketError::GetStatus(ex->HResult);
            // Add additional code here to handle exceptions.
        }
    }

private:
    void SendMessageUsingStreamWebSocket(const Platform::Array< byte >^ message)
    {
        try
        {
            auto dataWriter = ref new DataWriter(this->streamWebSocket->OutputStream);
            dataWriter->WriteBytes(message);

            Concurrency::create_task(dataWriter->StoreAsync()).then(
                [=](Concurrency::task< unsigned int >) // task< unsigned int > instead of unsigned int in order to handle any exceptions thrown in StoreAsync().
            {
                dataWriter->DetachStream();
                std::wstringstream wstringstream;
                wstringstream << L"Sending data using StreamWebSocket: " << message->Length << L" bytes" << std::endl;
                ::OutputDebugString(wstringstream.str().c_str());
            });
        }
        catch (Platform::Exception^ ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus = Windows::Networking::Sockets::WebSocketError::GetStatus(ex->HResult);
            // Add additional code here to handle exceptions.
        }
    }

    void ReceiveMessageUsingStreamWebSocket()
    {
        try
        {
            DataReader^ dataReader = ref new DataReader(this->streamWebSocket->InputStream);
            dataReader->InputStreamOptions = InputStreamOptions::Partial;

            Concurrency::create_task(dataReader->LoadAsync(256)).then(
                [=](unsigned int bytesLoaded)
            {
                auto message = ref new Platform::Array< byte >(bytesLoaded);
                dataReader->ReadBytes(message);
                std::wstringstream wstringstream;
                wstringstream << L"Data received from StreamWebSocket: " << message->Length << " bytes" << std::endl;
                ::OutputDebugString(wstringstream.str().c_str());
                this->streamWebSocket->Close(1000, L"");
            });
        }
        catch (Platform::Exception^ ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus = Windows::Networking::Sockets::WebSocketError::GetStatus(ex->HResult);
            // Add additional code here to handle exceptions.
        }
    }

    void WebSocket_Closed(Windows::Networking::Sockets::IWebSocket^ sender, Windows::Networking::Sockets::WebSocketClosedEventArgs^ args)
    {
        std::wstringstream wstringstream;
        wstringstream << L"WebSocket_Closed; Code: " << args->Code << ", Reason: \"" << args->Reason->Data() << "\"" << std::endl;
        ::OutputDebugString(wstringstream.str().c_str());
        // Add additional code here to handle the WebSocket being closed.
    }
```

### <a name="handle-the-streamwebsocketclosed-event"></a>Обработка события StreamWebSocket.Closed
Прежде чем устанавливать подключение и отправлять данные с помощью **StreamWebSocket**, необходимо подписаться на событие [**StreamWebSocket.Closed**](/uwp/api/windows.networking.sockets.streamwebsocket.Closed). Событие **Closed** создается, когда клиент или сервер закрывает сокет.
 
### <a name="send-data-on-a-streamwebsocket"></a>Отправка данных в StreamWebSocket
После установки подключения можно отправлять данные на сервер. Это можно сделать с помощью свойства [**StreamWebSocket.OutputStream**](https://docs.microsoft.com/en-us/uwp/api/Windows.Networking.Sockets.StreamWebSocket.OutputStream) и [**DataWriter**](/uwp/api/windows.storage.streams.datawriter) для записи данных.

**Примечание**. Если вы хотите записать больше данных в один сокет, не забудьте вызвать метод [**DataWriter.DetachStream**](/uwp/api/windows.storage.streams.datawriter.DetachStream), чтобы отсоединить поток вывода от **DataWriter** до того, как **DataWriter** выйдет за пределы области. В результате владельцем потока становится **MessageWebSocket**.

### <a name="receive-data-on-a-streamwebsocket"></a>Получение данных в StreamWebSocket
Используйте свойство [**StreamWebSocket.InputStream**](https://docs.microsoft.com/en-us/uwp/api/Windows.Networking.Sockets.StreamWebSocket.InputStream) и [**DataReader**](/uwp/api/windows.storage.streams.datareader) для чтения данных.

## <a name="advanced-options-for-messagewebsocket-and-streamwebsocket"></a>Дополнительные параметры для MessageWebSocket и StreamWebSocket
Прежде чем устанавливать подключение, можно задать дополнительные параметры в сокете, задав свойства в [**MessageWebSocketControl**](/uwp/api/windows.networking.sockets.messagewebsocketcontrol) или [**StreamWebSocketControl**](/uwp/api/windows.networking.sockets.streamwebsocketcontrol). Вы осуществляете доступ к экземпляру этих классов из самого объекта сокета через его свойство [**MessageWebSocket.Control**](/uwp/api/windows.networking.sockets.messagewebsocket.control) или [**StreamWebSocket.Control**](/uwp/api/windows.networking.sockets.streamwebsocket.control) в зависимости от ситуации.

Вот пример использования **StreamWebSocket**. Такая же схема применяется к **MessageWebSocket**.

```csharp
var streamWebSocket = new Windows.Networking.Sockets.StreamWebSocket();

// By default, the Nagle algorithm is not used. This overrides that, and causes it to be used.
streamWebSocket.Control.NoDelay = false;

await streamWebSocket.ConnectAsync(new Uri("wss://echo.websocket.org"));
```

```cppwinrt
Windows::Networking::Sockets::StreamWebSocket streamWebSocket;

// By default, the Nagle algorithm is not used. This overrides that, and causes it to be used.
streamWebSocket.Control().NoDelay(false);

auto connectAsyncAction = streamWebSocket.ConnectAsync(Uri{ L"wss://echo.websocket.org" });
```

```cpp
auto streamWebSocket = ref new Windows::Networking::Sockets::StreamWebSocket();

// By default, the Nagle algorithm is not used. This overrides that, and causes it to be used.
streamWebSocket->Control->NoDelay = false;

auto connectTask = Concurrency::create_task(streamWebSocket->ConnectAsync(ref new Uri(L"wss://echo.websocket.org")));
```

**Примечание**. Не пытайтесь изменить свойство элемента управления *после* вызова **ConnectAsync**. Единственное исключение из этого правила— [MessageWebSocketControl.MessageType](/uwp/api/windows.networking.sockets.messagewebsocketcontrol.MessageType).

## <a name="websocket-information-classes"></a>Классы информации WebSocket
[**MessageWebSocket**](/uwp/api/windows.networking.sockets.messagewebsocket) и [**StreamWebSocket**](/uwp/api/windows.networking.sockets.streamwebsocket) имеют соответствующий класс, который обеспечивает дополнительную информацию об объекте.

[**MessageWebSocketInformation**](/uwp/api/windows.networking.sockets.messagewebsocketinformation) предоставляет информацию о **MessageWebSocket**, и вы извлекаете его экземпляр с помощью свойства [**MessageWebSocket.Information**](/uwp/api/windows.networking.sockets.messagewebsocket.Information).

[**StreamWebSocketInformation**](/uwp/api/Windows.Networking.Sockets.StreamWebSocketInformation) предоставляет информацию о **StreamWebSocket**, и вы извлекаете его экземпляр с помощью свойства  [**StreamWebSocket.Information**](/uwp/api/Windows.Networking.Sockets.StreamWebSocket.Information).

Обратите внимание, что свойства в этих классах информации доступны только для чтения, но их можно использовать для извлечения информации в любой момент в течение срока жизни объекта веб-сокета.

## <a name="handling-exceptions"></a>Обработка исключений
Ошибка, обнаруженная в операции [**MessageWebSocket**](/uwp/api/Windows.Networking.Sockets.MessageWebSocket) или [**StreamWebSocket**](/uwp/api/Windows.Networking.Sockets.StreamWebSocket), возвращается в виде значения **HRESULT**. Можно передать значение **HRESULT** методу [**WebSocketError.GetStatus**](/uwp/api/windows.networking.sockets.websocketerror.getstatus), чтобы преобразовать его в значение перечисления [**WebErrorStatus**](/uwp/api/Windows.Web.WebErrorStatus).

Большинство значений перечисления **WebErrorStatus** соответствуют ошибке, возвращаемой стандартной операцией клиента HTTP. Ваше приложение может включать значения перечисления **WebErrorStatus**, чтобы по-разному действовать в зависимости от причины исключения.

Для ошибок при проверке параметров можно использовать также **HRESULT** из исключения, чтобы получить более подробные сведения об ошибке. Возможные значения **HRESULT** перечислены в `Winerror.h`, который находится в вашей установке SDK (например, в папке `C:\Program Files (x86)\Windows Kits\10\Include\<VERSION>\shared`). Для многих ошибок при проверке параметров возвращаемым **HRESULT** является **E_INVALIDARG**.

## <a name="setting-timeouts-on-websocket-operations"></a>Назначение времени ожидания при выполнении операций с WebSocket
Классы **MessageWebSocket** и **StreamWebSocket** используют внутреннюю системную службу для отправки запросов клиента WebSocket и получения откликов от сервера. Время ожидания, используемое для операции подключения WebSocket, по умолчанию составляет 60секунд. Если HTTP-сервер, поддерживающий WebSocket, не отвечает или не может ответить на запрос подключения WebSocket (он временно недоступен или блокируется в результате отказа сети), внутренняя системная служба ожидает заданные по умолчанию 60секунд, а затем возвращает ошибку. Эта ошибка вызывает исключение в методе WebSocket **ConnectAsync**. Время ожидания, используемое по умолчанию для операций отправки и получения после установления подключения WebSocket, составляет 30секунд.

Если запрос имени HTTP-сервера в URI возвращает несколько IP-адресов, то перед завершением с ошибкой внутренняя системная служба проверяет до 5 IP-адресов для сайта, по умолчанию ожидая ответа по каждому адресу в течение 60секунд. Следовательно, ваше приложение может ждать несколько минут, пытаясь подключиться к нескольким IP-адресам, прежде чем обработает исключение. Пользователю в этом случае может показаться, что приложение не работает. 

Чтобы ускорить реакцию приложения и свести эти проблемы к минимуму, можно задать более короткое время ожидания для запросов на подключение. Время ожидания задается одинаково для **MessageWebSocket** и **StreamWebSocket**.

```csharp
private Windows.Networking.Sockets.MessageWebSocket messageWebSocket;

protected override void OnNavigatedTo(NavigationEventArgs e)
{
    this.messageWebSocket = new Windows.Networking.Sockets.MessageWebSocket();

    try
    {
        var cancellationTokenSource = new CancellationTokenSource();
        var connectTask = this.messageWebSocket.ConnectAsync(new Uri("wss://echo.websocket.org")).AsTask(cancellationTokenSource.Token);

        // Cancel connectTask after 5 seconds.
        cancellationTokenSource.CancelAfter(TimeSpan.FromMilliseconds(5000));

        connectTask.ContinueWith((antecedent) =>
        {
            if (antecedent.Status == TaskStatus.RanToCompletion)
            {
                // connectTask ran to completion, so we know that the MessageWebSocket is connected.
                // Add additional code here to use the MessageWebSocket.
            }
            else
            {
                // connectTask timed out, or faulted.
            }
        });
    }
    catch (Exception ex)
    {
        Windows.Web.WebErrorStatus webErrorStatus = Windows.Networking.Sockets.WebSocketError.GetStatus(ex.GetBaseException().HResult);
        // Add additional code here to handle exceptions.
    }
}
```

```cppwinrt
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Networking.Sockets.h>
#include <winrt/Windows.UI.Xaml.Navigation.h>
#include <sstream>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::UI::Xaml::Navigation;
...
private:
    Windows::Networking::Sockets::MessageWebSocket m_messageWebSocket;

    IAsyncAction TimeoutAsync()
    {
        // Return control to the caller, and resume again to complete the async action after the timeout period.
        // 5 seconds, in this example.
        co_await(std::chrono::seconds{ 5 });
    }

public:
    IAsyncAction OnNavigatedTo(NavigationEventArgs /* e */)
    {
        try
        {
            // Return control to the caller, and then immediately resume on a thread pool thread.
            co_await winrt::resume_background();

            auto connectAsyncAction = m_messageWebSocket.ConnectAsync(Uri{ L"wss://echo.websocket.org" });

            TimeoutAsync().Completed([connectAsyncAction](IAsyncAction const& sender, AsyncStatus const)
            {
                // TimeoutAsync completes after the timeout period. After that period, it's safe
                // to cancel the ConnectAsync action even if it has already completed.
                connectAsyncAction.Cancel();
            });

            try
            {
                // Block until the ConnectAsync action completes or is canceled.
                connectAsyncAction.get();
            }
            catch (winrt::hresult_error const& ex)
            {
                std::wstringstream wstringstream;
                wstringstream << L"ConnectAsync threw an exception: " << ex.message().c_str() << std::endl;
                ::OutputDebugString(wstringstream.str().c_str());
            }

            if (connectAsyncAction.Status() == AsyncStatus::Completed)
            {
                // connectTask ran to completion, so we know that the MessageWebSocket is connected.
                // Add additional code here to use the MessageWebSocket.
            }
            else
            {
                // connectTask did not run to completion.
            }
        }
        catch (winrt::hresult_error const& ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus{ Windows::Networking::Sockets::WebSocketError::GetStatus(ex.to_abi()) };
            // Add additional code here to handle exceptions.
        }
    }
```

```cpp
#include <agents.h>
#include <ppltasks.h>
#include <sstream>
...
using namespace Windows::Foundation;
using namespace Windows::Storage::Streams;
using namespace Windows::UI::Xaml::Navigation;
...
private:
    Windows::Networking::Sockets::MessageWebSocket^ messageWebSocket;

protected:
    virtual void OnNavigatedTo(NavigationEventArgs^ e) override
    {
        this->messageWebSocket = ref new Windows::Networking::Sockets::MessageWebSocket();

        try
        {
            Concurrency::cancellation_token_source cancellationTokenSource;
            Concurrency::cancellation_token cancellationToken = cancellationTokenSource.get_token();

            auto connectTask = Concurrency::create_task(this->messageWebSocket->ConnectAsync(ref new Uri(L"wss://echo.websocket.org")), cancellationToken);

            // This continuation task returns true should connectTask run to completion.
            Concurrency::task< bool > taskRanToCompletion = connectTask.then([](void)
            {
                return true;
            });

            // This task returns false after the specified timeout. 5 seconds, in this example.
            Concurrency::task< bool > taskTimedout = Concurrency::create_task([]() -> bool
            {
                Concurrency::task_completion_event< void > taskCompletionEvent;

                // A call object that sets the task completion event.
                auto call = std::make_shared< Concurrency::call< int > >([taskCompletionEvent](int)
                {
                    taskCompletionEvent.set();
                });

                // A non-repeating timer that calls the call object when the timer fires.
                auto nonRepeatingTimer = std::make_shared< Concurrency::timer < int > >(5000, 0, call.get(), false);
                nonRepeatingTimer->start();

                // A task that completes after the completion event is set.
                Concurrency::task< void > taskWaitForCompletionEvent(taskCompletionEvent);

                return taskWaitForCompletionEvent.then([]() {return false; }).get();
            });

            (taskRanToCompletion || taskTimedout).then([this, cancellationTokenSource](bool connectTaskRanToCompletion)
            {
                if (connectTaskRanToCompletion)
                {
                    // connectTask ran to completion, so we know that the MessageWebSocket is connected.
                    // Add additional code here to use the MessageWebSocket.
                }
                else
                {
                    // taskTimedout ran to completion, so we should cancel connectTask via the cancellation_token_source.
                    cancellationTokenSource.cancel();
                }
            });
        }
        catch (Platform::Exception^ ex)
        {
            Windows::Web::WebErrorStatus webErrorStatus = Windows::Networking::Sockets::WebSocketError::GetStatus(ex->HResult);
            // Add additional code here to handle exceptions.
        }
    }
```

## <a name="important-apis"></a>Важные API
* [DataReader](/uwp/api/Windows.Storage.Streams.DataReader)
* [DataWriter](/uwp/api/Windows.Storage.Streams.DataWriter)
* [DataWriter.DetachStream](/uwp/api/windows.storage.streams.datawriter.DetachStream)
* [MessageWebSocket](/uwp/api/windows.networking.sockets.messagewebsocket)
* [MessageWebSocket.Closed](/uwp/api/Windows.Networking.Sockets.MessageWebSocket.Closed)
* [MessageWebSocket.ConnectAsync](/uwp/api/windows.networking.sockets.messagewebsocket.connectasync)
* [MessageWebSocket.Control](/uwp/api/windows.networking.sockets.messagewebsocket.control)
* [MessageWebSocket.Information](/uwp/api/Windows.Networking.Sockets.MessageWebSocket.Information)
* [MessageWebSocket.MessageReceived](/uwp/api/Windows.Networking.Sockets.MessageWebSocket.MessageReceived)
* [MessageWebSocket.OutputStream](https://docs.microsoft.com/en-us/uwp/api/Windows.Networking.Sockets.MessageWebSocket.OutputStream)
* [MessageWebSocketControl](/uwp/api/Windows.Networking.Sockets.MessageWebSocketControl)
* [MessageWebSocketControl.MessageType](/uwp/api/Windows.Networking.Sockets.MessageWebSocketControl.MessageType)
* [MessageWebSocketInformation](/uwp/api/Windows.Networking.Sockets.MessageWebSocketInformation)
* [MessageWebSocketMessageReceivedEventArgs](/uwp/api/Windows.Networking.Sockets.MessageWebSocketMessageReceivedEventArgs)
* [SocketMessageType](/uwp/api/windows.networking.sockets.socketmessagetype)
* [StreamWebSocket](/uwp/api/Windows.Networking.Sockets.StreamWebSocket)
* [StreamWebSocket.Closed](/uwp/api/Windows.Networking.Sockets.StreamWebSocket.Closed)
* [StreamSocket.ConnectAsync](/uwp/api/windows.networking.sockets.streamsocket.connectasync)
* [StreamWebSocket.Control](/uwp/api/windows.networking.sockets.streamwebsocket.control)
* [StreamWebSocket.Information](/uwp/api/windows.networking.sockets.streamwebsocket.Information)
* [StreamWebSocket.InputStream](https://docs.microsoft.com/en-us/uwp/api/Windows.Networking.Sockets.StreamWebSocket.InputStream)
* [StreamWebSocket.OutputStream](https://docs.microsoft.com/en-us/uwp/api/Windows.Networking.Sockets.StreamWebSocket.OutputStream)
* [StreamWebSocketControl](/uwp/api/Windows.Networking.Sockets.StreamWebSocketControl)
* [StreamWebSocketInformation](/uwp/api/Windows.Networking.Sockets.StreamWebSocketInformation)
* [WebErrorStatus](/uwp/api/Windows.Web.WebErrorStatus) 
* [WebSocketError.GetStatus](/uwp/api/windows.networking.sockets.websocketerror.getstatus)
* [Windows.Networking.Sockets](/uwp/api/Windows.Networking.Sockets)

## <a name="related-topics"></a>Статьи по теме
* [Протокол WebSocket](http://tools.ietf.org/html/rfc6455)
* [Сокеты](sockets.md)

## <a name="samples"></a>Примеры
* [Пример WebSocket](http://go.microsoft.com/fwlink/p/?LinkId=620623)
