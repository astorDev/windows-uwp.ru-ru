---
author: DelfCo
description: "Для связи с другими устройствами в качестве разработчика приложений универсальной платформы для Windows (UWP) можно использовать как Windows.Networking.Sockets, так и Winsock."
title: "Сокеты"
ms.assetid: 23B10A3C-E33F-4CD6-92CB-0FFB491472D6
ms.sourcegitcommit: 4557fa59d377edc2ae5bf5a9be63516d152949bb
ms.openlocfilehash: 432d9849335c537836fd23a4cd95c79c51bc881d

---

# Сокеты

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**Windows.Networking.Sockets**](https://msdn.microsoft.com/library/windows/apps/br226960)
-   [Winsock](https://msdn.microsoft.com/library/windows/desktop/ms740673)

Для связи с другими устройствами в качестве разработчика приложений универсальной платформы для Windows (UWP) можно использовать как [**Windows.Networking.Sockets**](https://msdn.microsoft.com/library/windows/apps/br226960), так и [Winsock](https://msdn.microsoft.com/library/windows/desktop/ms737523). В этом разделе представлено подробное руководство по использованию пространства имен **Windows.Networking.Sockets** для выполнения сетевых операций.

>
>            **Примечание.** В рамках [сетевой изоляции](https://msdn.microsoft.com/library/windows/apps/hh770532.aspx) система запрещает установку соединений сокетов или WinSock между двумя приложениями UWP, работающими на одном компьютере, через локальный петлевой адрес (127.0.0.0) либо путем явного указания локального IP-адреса. Это значит, что использовать сокеты для связи между двумя приложений UWP нельзя. UWP предоставляет другие механизмы связи между приложениями. Дополнительные сведения см. в разделе [Связь между приложениями](https://msdn.microsoft.com/windows/uwp/app-to-app/index).

## Базовые операции сокетов TCP

Для постоянных подключений сокет TCP обеспечивает в сети низкоуровневую двунаправленную передачу данных. Сокеты TCP широко используются большинством сетевых протоколов в Интернете. В этом разделе показано, как активировать приложение UWP для отправки и получения данных с сокетом потока TCP с помощью классов [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) и [**StreamSocketListener**](https://msdn.microsoft.com/library/windows/apps/br226906) как части пространства имен [**Windows.Networking.Sockets**](https://msdn.microsoft.com/library/windows/apps/br226960). В целях данного раздела мы будем создавать очень простое приложение, функционирующее как эхо-сервер и клиент, чтобы продемонстрировать основные операции TCP.

**Создание эхо-сервера TCP**

В следующем примере кода показано, как создать объект [**StreamSocketListener**](https://msdn.microsoft.com/library/windows/apps/br226906) и запустить прослушивание для входящих соединений TCP.

```csharp
try
{
    //Create a StreamSocketListener to start listening for TCP connections.
    Windows.Networking.Sockets.StreamSocketListener socketListener = new Windows.Networking.Sockets.StreamSocketListener();

    //Hook up an event handler to call when connections are received.
    socketListener.ConnectionReceived += SocketListener_ConnectionReceived;

    //Start listening for incoming TCP connections on the specified port. You can specify any port that' s not currently in use.
    await socketListener.BindServiceNameAsync("1337");
}
catch (Exception e)
{
    //Handle exception.
}
```

В следующем примере кода реализован обработчик событий SocketListener\_ConnectionReceived, который был прикреплен к событию [**StreamSocketListener.ConnectionReceived**](https://msdn.microsoft.com/library/windows/apps/hh701494) в примере выше. Этот обработчик событий вызывается классом [**StreamSocketListener**](https://msdn.microsoft.com/library/windows/apps/br226906) каждый раз, когда удаленный клиент устанавливает соединение с эхо-сервером.

```csharp
private async void SocketListener_ConnectionReceived(Windows.Networking.Sockets.StreamSocketListener sender, 
    Windows.Networking.Sockets.StreamSocketListenerConnectionReceivedEventArgs args)
{
    //Read line from the remote client.
    Stream inStream = args.Socket.InputStream.AsStreamForRead();
    StreamReader reader = new StreamReader(inStream);
    string request = await reader.ReadLineAsync();
    
    //Send the line back to the remote client.
    Stream outStream = args.Socket.OutputStream.AsStreamForWrite();
    StreamWriter writer = new StreamWriter(outStream);
    await writer.WriteLineAsync(request);
    await writer.FlushAsync();
}
```

**Создание эхо-клиента TCP**

В следующем примере кода показано, как создать объект [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882), установить соединение с удаленным сервером, отправить запрос и получить ответ.

```csharp
try
{
    //Create the StreamSocket and establish a connection to the echo server.
    Windows.Networking.Sockets.StreamSocket socket = new Windows.Networking.Sockets.StreamSocket();
    
    //The server hostname that we will be establishing a connection to. We will be running the server and client locally,
    //so we will use localhost as the hostname.
    Windows.Networking.HostName serverHost = new Windows.Networking.HostName("localhost");
    
    //Every protocol typically has a standard port number. For example HTTP is typically 80, FTP is 20 and 21, etc.
    //For the echo server/client application we will use a random port 1337.
    string serverPort = "1337";
    await socket.ConnectAsync(serverHost, serverPort);

    //Write data to the echo server.
    Stream streamOut = socket.OutputStream.AsStreamForWrite();
    StreamWriter writer = new StreamWriter(streamOut);
    string request = "test";
    await writer.WriteLineAsync(request);
    await writer.FlushAsync();

    //Read data from the echo server.
    Stream streamIn = socket.InputStream.AsStreamForRead();
    StreamReader reader = new StreamReader(streamIn);
    string response = await reader.ReadLineAsync();
}
catch (Exception e)
{
    //Handle exception here.            
}
```

## Базовые операции сокетов UDP

Сокет UDP обеспечивает в сети низкоуровневую двунаправленную передачу данных в любом направлении для сетевого соединения, не требующую установленного соединения. Поскольку сокеты UDP не поддерживают соединение на обеих конечных точках, они предоставляют быстрый и простой способ сетевого подключения между удаленными компьютерами. Однако сокеты UDP не обеспечивают целостность сетевых пакетов или их передачу удаленному адресату. Примеры приложений, использующих сокеты UDP: локальные клиенты обнаружения сети и локальные клиенты чатов. В этом разделе демонстрируется использование класса [**DatagramSocket**](https://msdn.microsoft.com/library/windows/apps/br241319) для отправки и получения сообщений UDP путем создания простого эхо-сервера и клиента.

**Создание эхо-сервера UDP**

В следующем примере кода показано, как создать объект [**DatagramSocket**](https://msdn.microsoft.com/library/windows/apps/br241319) и привязать его к определенному порту, чтобы можно было ожидать получения входящих сообщений UDP.

```csharp
Windows.Networking.Sockets.DatagramSocket socket = new Windows.Networking.Sockets.DatagramSocket();

socket.MessageReceived += Socket_MessageReceived;

//You can use any port that is not currently in use already on the machine.
string serverPort = "1337";

//Bind the socket to the serverPort so that we can start listening for UDP messages from the UDP echo client.
await socket.BindServiceNameAsync(serverPort);
```

В следующем примере кода реализован обработчик событий **Socket\_MessageReceived** для чтения сообщения, которое было получено от клиента, и обратной отправки того же сообщения.

```csharp
private async void Socket_MessageReceived(Windows.Networking.Sockets.DatagramSocket sender, Windows.Networking.Sockets.DatagramSocketMessageReceivedEventArgs args)
{
    //Read the message that was received from the UDP echo client.
    Stream streamIn = args.GetDataStream().AsStreamForRead();
    StreamReader reader = new StreamReader(streamIn);
    string message = await reader.ReadLineAsync();

    //Create a new socket to send the same message back to the UDP echo client.
    Windows.Networking.Sockets.DatagramSocket socket = new Windows.Networking.Sockets.DatagramSocket();
    
    //Use a separate port number for the UDP echo client because both will be unning on the same machine.
    string clientPort = "1338"
    Stream streamOut = (await socket.GetOutputStreamAsync(args.RemoteAddress, clientPort)).AsStreamForWrite();
    StreamWriter writer = new StreamWriter(streamOut);
    await writer.WriteLineAsync(message);
    await writer.FlushAsync();
}
```

**Создание эхо-клиента UDP**

В следующем примере кода показано, как создать объект [**DatagramSocket**](https://msdn.microsoft.com/library/windows/apps/br241319) и привязать его к определенному порту, чтобы можно было ожидать получения входящих сообщений UDP и отправлять сообщение UDP эхо-серверу UDP.

```csharp
private async void testUdpSocketServer()
{
    Windows.Networking.Sockets.DatagramSocket socket = new Windows.Networking.Sockets.DatagramSocket();
    
    socket.MessageReceived += Socket_MessageReceived;
    
    //You can use any port that is not currently in use already on the machine. We will be using two separate and random 
    //ports for the client and server because both the will be running on the same machine.
    string serverPort = "1337";
    string clientPort = "1338";
    
    //Because we will be running the client and server on the same machine, we will use localhost as the hostname.
    Windows.Networking.HostName serverHost = new Windows.Networking.HostName("localhost");
    
    //Bind the socket to the clientPort so that we can start listening for UDP messages from the UDP echo server.
    await socket.BindServiceNameAsync(clientPort);
                
    //Write a message to the UDP echo server.
    Stream streamOut = (await socket.GetOutputStreamAsync(serverHost, serverPort)).AsStreamForWrite();
    StreamWriter writer = new StreamWriter(streamOut);
    string message = "Hello, world!";
    await writer.WriteLineAsync(message);
    await writer.FlushAsync();
}
```

В следующем примере кода реализован обработчик событий **Socket\_MessageReceived** для чтения сообщения, полученного от эхо-сервера UDP.

```csharp
private async void Socket_MessageReceived(Windows.Networking.Sockets.DatagramSocket sender, 
    Windows.Networking.Sockets.DatagramSocketMessageReceivedEventArgs args)
{
    //Read the message that was received from the UDP echo server.
    Stream streamIn = args.GetDataStream().AsStreamForRead();
    StreamReader reader = new StreamReader(streamIn);
    string message = await reader.ReadLineAsync();
}
```

## Фоновые операции и посредник сокета

Если ваше приложение получает подключения или данные по сокетам, вы должны быть готовы к выполнению этих операций надлежащим образом в то время, пока приложение находится в фоновом режиме. Для этого используется посредник сокетов. Подробнее о том, как использовать брокер сокетов, см. в разделе [Сетевые подключения в фоновом режиме](network-communications-in-the-background.md).

## Пакетные отправки

Начиная с Windows10, Windows.Networking.Sockets поддерживает пакетные отправки–способ отправки нескольких буферов с гораздо более низкими издержками, связанными с переключением контекста, чем при отправке каждого из буферов отдельно. Это особенно полезно, если ваше приложение выполняет VoIP, VPN или другие задачи, предполагающие максимально эффективное перемещение больших объемов данных.

Каждый вызов WriteAsync на сокете активирует переход ядра к сетевому стеку. Если приложение записывает одновременно много буферов, при каждой операции записи происходит отдельный переход ядра, что приводит к значительным издержкам. Новый шаблон пакетных отправок оптимизирует частоту переходов ядра. Эта функция в настоящее время ограничена и включает только [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) и подключенные экземпляры [**DatagramSocket**](https://msdn.microsoft.com/library/windows/apps/br241319).

Ниже приведен пример неоптимальной отправки приложением большого количества буферов.

```csharp
// Send a set of packets inefficiently, one packet at a time.
// This is not recommended if you have many packets to send
IList<IBuffer> packetsToSend = PreparePackets();
var outputStream = stream.OutputStream;

foreach (IBuffer packet in packetsToSend)
{
    // Incurs kernel transition overhead for each packet
    await outputStream.WriteAsync(packet);
}
```

В этом примере показан более эффективный способ отправки большого количества буферов. Поскольку этот метод предполагает использование функций, свойственных только языку C#, он доступен исключительно программистам C#. В данном примере показано, как при одновременной отправке нескольких пакетов система получает возможность выполнять пакетные отправки, что позволяет оптимизировать переходы ядра и тем самым повысить производительность.

```csharp
// More efficient way to send packets.
// This way enables the system to do batched sends
IList<IBuffer> packetsToSend = PreparePackets();
var outputStream = stream.OutputStream;

int i = 0;
Task[] pendingTasks = new Tast[packetsToSend.Count];
foreach (IBuffer packet in packetsToSend)
{
    // track all pending writes as tasks, but do not wait on one before peforming the next
    pendingTasks[i++] = outputStream.WriteAsync(packet).AsTask();
    // Do not modify any buffer' s contents until the pending writes are complete.
}
// Now, wait for all of the pending writes to complete
await Task.WaitAll(pendingTasks);
```

В этом примере показан еще один способ отправки большого количества буферов, совместимый с функцией пакетной отправки. А поскольку данный способ не предполагает использование функций, присущих C#, он может применяться для всех языков (хотя здесь он показан на примере C#). В данном случае используется измененное поведение в члене **OutputStream** классов [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) и [**DatagramSocket**](https://msdn.microsoft.com/library/windows/apps/br241319), появившихся в Windows10.

```csharp
// More efficient way to send packets in Windows 10, using the new behavior of OutputStream.FlushAsync().
int i = 0;
IList<IBuffer> packetsToSend = PreparePackets();
var outputStream = socket.OutputStream;

var pendingWrites = new IAsyncOperationWithProgress<uint,uint> [packetsToSend.Count];

foreach (IBuffer packet in packetsToSend)
{
    pendingWrites[i++] = outputStream.WriteAsync(packet);
    // Do not modify any buffer' s contents until the pending writes are complete.
}

// Wait for all pending writes to complete. This step enables batched sends on the output stream.
await outputStream.FlushAsync();
```

В предыдущих версиях Windows значение **FlushAsync** возвращалось немедленно; при этом некоторые операции в потоке могли быть еще не завершены. В Windows10 поведение изменилось. 
            Теперь **FlushAsync** гарантированно возвращается только после завершения всех операций выходного потока.

Существуют некоторые важные ограничения, связанные с использованием пакетных записей в вашем коде.

-   Вы не можете изменять содержимое экземпляров **IBuffer**, написанных до завершения асинхронной записи.
-   Шаблон **FlushAsync** работает только на **StreamSocket.OutputStream** и **DatagramSocket.OutputStream**.
-   Шаблон **FlushAsync** работает только в Windows10 и более поздних версиях.
-   В других случаях следует использовать **Task.WaitAll** вместо шаблона **FlushAsync**.

## Совместное использование порта для DatagramSocket

Windows10 представляет новое свойство [**DatagramSocketControl**](https://msdn.microsoft.com/library/windows/apps/hh701190), [**MulticastOnly**](https://msdn.microsoft.com/library/windows/apps/dn895368), позволяющее указывать, что рассматриваемый сокет **DatagramSocket** может сосуществовать с другими многоадресными сокетами Win32 или WinRT, привязанными к тому же адресу/порту.

## Предоставление сертификата клиента с классом StreamSocket

Класс [**Windows.Networking.StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) поддерживает возможность использования протокола SSL/TLS для проверки подлинности сервера, к которому обращается приложение. В определенных случаях приложение также должно пройти проверку подлинности на сервере с помощью сертификата клиента TLS. В Windows10 можно предоставить сертификат клиента на объект [**StreamSocket.Control**](https://msdn.microsoft.com/library/windows/apps/br226893) (необходимо задать перед началом подтверждения TLS). Если сервер запрашивает сертификат клиента, Windows ответит, воспользовавшись предоставленным сертификатом.

Фрагмент кода, демонстрирующий данную операцию:

```csharp
var socket = new StreamSocket();
Windows.Security.Cryptography.Certificates.Certificate certificate = await GetClientCert();
socket.Control.ClientCertificate = certificate;
await socket.ConnectAsync(destination, SocketProtectionLevel.Tls12);
```

## Исключения в Windows.Networking.Sockets

Конструктор класса [**HostName**](https://msdn.microsoft.com/library/windows/apps/br207113), используемый с сокетами, может вызвать исключение, если переданная строка не является допустимым именем узла (содержит символы, которые не разрешено использовать в имени узла). Если пользователь вводит в приложение данные **HostName**, конструктор должен находиться в блоке try/catch. Если вызывается исключение, приложение может уведомить об этом пользователя и запросить новое имя узла.

Пространство имен [**Windows.Networking.Sockets**](https://msdn.microsoft.com/library/windows/apps/br226960) содержит удобные вспомогательные методы и перечисления для обработки ошибок, возникающих при использовании сокетов и WebSocket. Это удобно, чтобы по-разному обрабатывать в приложении различные сетевые исключения.

Ошибка, обнаруженная в операции [**DatagramSocket**](https://msdn.microsoft.com/library/windows/apps/br241319), [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) или [**StreamSocketListener**](https://msdn.microsoft.com/library/windows/apps/br226906), возвращается в виде значения **HRESULT**. Метод [**SocketError.GetStatus**](https://msdn.microsoft.com/library/windows/apps/hh701462) преобразует сетевую ошибку, произошедшую в операции с сокетом, в значение перечисления [**SocketErrorStatus**](https://msdn.microsoft.com/library/windows/apps/hh701457). Большинство значений перечисления **SocketErrorStatus** соответствуют ошибке, возвращаемой стандартной операцией с сокетами Windows. Приложение может фильтровать полученные данные по определенным значениям перечисления **SocketErrorStatus**, чтобы действовать в зависимости от причины исключения.

Ошибка, обнаруженная в операции [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842) или [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923), возвращается в виде значения **HRESULT**. Метод [**WebSocketError.GetStatus**](https://msdn.microsoft.com/library/windows/apps/hh701529) преобразует сетевую ошибку, произошедшую в операции с WebSocket, в значение перечисления [**WebErrorStatus**](https://msdn.microsoft.com/library/windows/apps/hh747818). Большинство значений перечисления **WebErrorStatus** соответствуют ошибке, возвращаемой стандартной операцией клиента HTTP. Приложение может фильтровать полученные данные по определенному значению перечисления **WebErrorStatus**, чтобы по-разному действовать в зависимости от причины исключения.

Для ошибок при проверке параметров приложение может использовать также **HRESULT** из исключения, чтобы получить более подробные сведения об ошибке, которая вызвала исключение. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Для многих ошибок при проверке параметров возвращаемым **HRESULT** является **E\_INVALIDARG**.

## Winsock API

Кроме того, в приложении UWP можно использовать [Winsock](https://msdn.microsoft.com/library/windows/desktop/ms740673). Поддерживаемый Winsock API основывается на API Windows Phone 8.1 Microsoft Silverlight и продолжает поддерживать большую часть типов, свойств и методов (удалены некоторые API, которые считаются устаревшими). Дополнительную информацию о программировании Winsock можно найти [здесь](https://msdn.microsoft.com/library/windows/desktop/ms740673).





<!--HONumber=Jun16_HO4-->


