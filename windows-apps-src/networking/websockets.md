---
author: DelfCo
description: "Протокол WebSockets определяет механизм быстрого и безопасного двустороннего взаимодействия между клиентом и сервером через HTTP(S)."
title: WebSockets
ms.assetid: EAA9CB3E-6A3A-4C13-9636-CCD3DE46E7E2
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: ff2429e1e9ea56c414978c126497551b1e1864b8

---

# WebSockets

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842)
-   [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923)

Протокол WebSockets определяет механизм быстрого и безопасного двустороннего взаимодействия между клиентом и сервером через HTTP(S).

В [протоколе WebSocket](http://tools.ietf.org/html/rfc6455) данные немедленно передаются через полнодуплексное односокетное подключение, при этом сообщения могут отправляться и приниматься с обеих конечных точек в реальном времени. Соединения WebSocket отлично подходят для использования в играх в реальном времени, предусматривающих мгновенные уведомления социальных сетей и отображение свежей информации (статистика игры), для чего требуется безопасная и быстрая передача данных. Разработчики универсальной платформы для Windows (UWP) могут использовать классы [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842) и [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923) для подключения к серверам, которые поддерживают протокол Websocket.

| MessageWebSocket                                                         | StreamWebSocket                                                                               |
|--------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| Подходит для обычных сценариев, в которых сообщения не являются слишком большими.   | Подходит для сценариев, в которых передаются большие файлы (например, фото или видео). |
| Включает уведомление о том, что сообщение WebSocket полностью получено. | Позволяет считывать части сообщения при каждой операции чтения.                             |
| Поддерживает двоичные сообщения и сообщения в кодировке UTF-8.                                 | Поддерживает только двоичные сообщения.                                                                |
| Аналогичен UDP-сокету или сокету датаграммы.                                     | Аналогичен TCP-сокету или сокету потока.                                                            |

В большинстве случаев необходимо использовать безопасное соединение WebSocket для шифрования отправляемых и получаемых данных. Это также увеличивает шансы успешного подключения, т.к. многие прокси-серверы отклоняют незашифрованные соединения WebSocket. Протокол WebSocket определяет две следующие схемы URI.

-   ws– используется для незашифрованных соединений;
-   wss– используется для защищенных соединений, которые должны быть зашифрованы.

Для шифрования подключения WebSocket используйте wss: схема URI, например, `wss://www.contoso.com/mywebservice`.

## Использование MessageWebSocket

Метод [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842) позволяет считывать части сообщения при каждой операции чтения. **MessageWebSocket** обычно используется в сценариях с не очень большими сообщениями. Поддерживаются двоичные файлы и кодировка UTF-8.

Код в этом разделе создает новый [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842), подключается к серверу WebSocket и отправляет на него данные. После успешного установления подключения приложение ожидает вызова события [**MessageWebSocket.MessageReceived**](https://msdn.microsoft.com/library/windows/apps/br241358),
которое свидетельствует о получении данных.

В этом примере используется сервер вывода на экран страницы WebSocket.org (служба, которая просто возвращает отправителю любую отправленную ей строку). Благодаря использованию указателя протокола «wss:» в данном примере применяется безопасное подключение для отправки и получения сообщений.

> [!div class="tabbedCodeSnippets"]
> ```cpp
> void Game::InitWebSockets()
> {
>     // Create a new web socket
>     m_messageWebSocket = ref new MessageWebSocket();
> 
>     // Set the message type to UTF-8
>     m_messageWebSocket->Control->MessageType = Windows::Networking::Sockets::SocketMessageType::Utf8;
> 
>     // Register callbacks for notifications of interest
>     m_messageWebSocket->MessageReceived += 
>        ref new TypedEventHandler<MessageWebSocket^, MessageWebSocketMessageReceivedEventArgs^>(this, &Game::WebSocketMessageReceived);
>     m_messageWebSocket->Closed += ref new TypedEventHandler<IWebSocket^, WebSocketClosedEventArgs^>(this, &Game::WebSocketClosed);
> 
>     // This test code uses the websocket.org echo service to illustrate sending a string and receiving the echoed string back
>     // Note that wss: makes this an encrypted connection.
>     m_serverUri = ref new Uri("wss://echo.websocket.org");
> 
>     // Establish the connection, and set m_socketConnected on success
>     create_task(m_messageWebSocket->ConnectAsync(m_serverUri)).then([this] (task<void> previousTask)
>     {
>         try
>         {
>             // Try getting all exceptions from the continuation chain above this point.
>             previousTask.get();
> 
>             // websocket connected. update state variable
>             m_socketConnected = true;
>             OutputDebugString(L"Successfully initialized websockets\n");
>         }
>         catch (Platform::COMException^ exception)
>         {
>             // Add code here to handle any exceptions
>             // HandleException(exception);
> 
>         }
>     });
> }
> ```
> ```cs
> MessageWebSocket webSock = new MessageWebSocket();
> 
> //In this case we will be sending/receiving a string so we need to set the MessageType to Utf8.
> webSock.Control.MessageType = SocketMessageType.Utf8;
> 
> //Add the MessageReceived event handler.
> webSock.MessageReceived += WebSock_MessageReceived;
> 
> //Add the Closed event handler.
> webSock.Closed += WebSock_Closed;
> 
> Uri serverUri = new Uri("wss://echo.websocket.org");
> 
> try
> {
>     //Connect to the server.
>     await webSock.ConnectAsync(serverUri);
> 
>     //Send a message to the server.
>     await WebSock_SendMessage(webSock, "Hello, world!");
> }
> catch (Exception ex)
> {
>     //Add code here to handle any exceptions
> }
> ```

После инициализации подключения WebSocket ваш код должен осуществить следующие действия для правильного выполнения отправки и получения данных.

### Регистрация обратного вызова для события MessageWebSocket.MessageReceived

Перед установкой подключения и отправкой данных с помощью WebSocket ваше приложение должно зарегистрировать обратный вызов события, чтобы получить уведомление о получении данных. При возникновении события [**MessageWebSocket.MessageReceived**](https://msdn.microsoft.com/library/windows/apps/br241358) вызывается зарегистрированный обратный вызов, который получает данные из [**MessageWebSocketMessageReceivedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br226852). В этом примере предполагается, что сообщение отправляется в формате UTF-8.

В следующем примере функция получает строку от подключенного сервера WebSocket и выводит ее в окне вывода отладчика.

> [!div class="tabbedCodeSnippets"]
>```cpp
>void Game::WebSocketMessageReceived(MessageWebSocket^ sender, MessageWebSocketMessageReceivedEventArgs^ args)
>{
>    DataReader^ messageReader = args->GetDataReader();
>    messageReader->UnicodeEncoding = Windows::Storage::Streams::UnicodeEncoding::Utf8;
>
>    String^ readString = messageReader->ReadString(messageReader->UnconsumedBufferLength);
>    // Data has been read and is now available from the readString variable.
>    swprintf(m_debugBuffer, 511, L"WebSocket Message received: %s\n", readString->Data());
>    OutputDebugString(m_debugBuffer);
>}
>```
>```csharp
>//The MessageReceived event handler.
>private void WebSock_MessageReceived(MessageWebSocket sender, MessageWebSocketMessageReceivedEventArgs args)
>{
>    DataReader messageReader = args.GetDataReader();
>    messageReader.UnicodeEncoding = UnicodeEncoding.Utf8;
>    string messageString = messageReader.ReadString(messageReader.UnconsumedBufferLength);
>
>    //Add code here to do something with the string that is received.
>}
>```

###  Регистрация события обратного вызова MessageWebSocket.Closed

Перед установлением подключения и отправкой данных с помощью WebSocket ваше приложение должно зарегистрировать обратный вызов события, чтобы получить уведомление, когда сервер WebSocket закроет WebSocket. Когда происходит событие [**MessageWebSocket.Closed**](https://msdn.microsoft.com/library/windows/apps/hh701364),
вызывается зарегистрированный обратный вызов для указания того, что подключение было закрыто сервером WebSocket.

> [!div class="tabbedCodeSnippets"]
>```cpp
>void Game::WebSocketClosed(IWebSocket^ sender, WebSocketClosedEventArgs^ args)
>{
>    // The method may be triggered remotely by the server sending unsolicited close frame or locally by Close()/delete operator.
>    // This method assumes we saved the connected WebSocket to a variable called m_messageWebSocket
>    if (m_messageWebSocket != nullptr)
>    {
>        delete m_messageWebSocket;
>        m_messageWebSocket = nullptr;
>        OutputDebugString(L"Socket was closed\n");
>    }
>    m_socketConnected = false;
> }
>```
>```csharp
>//The Closed event handler
>private void WebSock_Closed(IWebSocket sender, WebSocketClosedEventArgs args)
>{
>    //Add code here to do something when the connection is closed locally or by the server
>}
>```

###  Отправка сообщения на WebSocket

После установления подключения клиент WebSocket может отправлять данные на сервер. Метод [**DataWriter.StoreAsync**](https://msdn.microsoft.com/library/windows/apps/br208171) возвращает параметр, который отображается в виде целого числа без знака. Это меняет способ определения задачи по отправке сообщения по отношению к задаче по установлению подключения.

**Примечание.** При создании нового объекта DataWriter с помощью OutputStream сокета MessageWebSocket объект DataWriter берет под контроль OutputStream и при выходе DataWriter за пределы области отменяет распределение Outputstream. В результате все последующие попытки использовать OutputStream будут неудачными со значением HRESULT 0x80000013. Чтобы избежать отмены распределения OutputStream, данный код вызывает метод DetachStream DataWriter, который возвращает контроль над потоком объекту WebSocket.

Следующая функция отправляет данную строку к подключенному WebSocket и выводит сообщения проверки в окне вывода отладчика.

> [!div class="tabbedCodeSnippets"]
>```cpp
>void Game::SendWebSocketMessage(Windows::Networking::Sockets::MessageWebSocket^ sendingSocket, Platform::String^ message)
>{
>    if (m_socketConnected)
>    {
>        // WebSocket is connected, so send a message
>        m_messageWriter = ref new DataWriter(sendingSocket->OutputStream);
>
>        m_messageWriter->WriteString(message);
>
>        // Send the data as one complete message
>        create_task(m_messageWriter->StoreAsync()).then([this] (unsigned int)
>        {
>            // Send Completed
>            m_messageWriter->DetachStream();    // give the stream back to m_messageWebSocket
>            OutputDebugString(L"Sent websocket message\n");
>        })
>            .then([this] (task<void>> previousTask)
>        {
>            try
>            {
>                // Try getting all exceptions from the continuation chain above this point.
>                previousTask.get();
>            }
>            catch (Platform::COMException ^ex)
>            {
>                // Add code to handle the exception
>                // HandleException(exception);
>            }
>        });
>    }
>}
>```
>```csharp
>//Send a message to the server.
>private async Task WebSock_SendMessage(MessageWebSocket webSock, string message)
>{
>    DataWriter messageWriter = new DataWriter(webSock.OutputStream);
>    messageWriter.WriteString(message);
>    await messageWriter.StoreAsync();
>}
>```

## Использование расширенных элементов управления с подключениями WebSockets

[**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842) и [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923) соответствуют одной и той же модели использования расширенных элементов управления. Основные классы, соответствующие каждому из названных выше классов, являются связанными классами для доступа к расширенным элементам управления.

[**MessageWebSocketControl**](https://msdn.microsoft.com/library/windows/apps/br226843) предоставляет данные элемента управления сокетами для объекта [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842).
[**StreamWebSocketControl**](https://msdn.microsoft.com/library/windows/apps/br226924) предоставляет данные элемента управления сокетами для объекта [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923).
Основная модель использования расширенных элементов управления является одинаковой для обоих типов протоколов WebSocket. В следующем рассуждении [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923) применяется в качестве примера, но такой же процесс можно использовать и для [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842).

1.  Создайте объект [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923).
2.  Используйте свойство [**StreamWebSocket.Control**](https://msdn.microsoft.com/library/windows/apps/br226934), чтобы извлечь экземпляр [**StreamWebSocketControl**](https://msdn.microsoft.com/library/windows/apps/br226924), связанный с этим объектом [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923).
3.  Получите или задайте свойства экземпляра [**StreamWebSocketControl**](https://msdn.microsoft.com/library/windows/apps/br226924) для получения или установки конкретных расширенных элементов управления.

Как [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923), так и [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842) определяют требования ко времени настройки расширенных элементов управления.

-   Для всех расширенных элементов управления [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923) приложение всегда должно устанавливать свойство перед осуществлением операции подключения. Ввиду этого требования рекомендуется задавать все свойства элемента управления сразу после создания объекта **StreamWebSocket** . Не пытайтесь установить свойство элемента управления после вызова метода [**StreamWebSocket.ConnectAsync**](https://msdn.microsoft.com/library/windows/apps/br226933).
-   Для всех расширенных элементов управления [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842), кроме типа сообщения, необходимо устанавливать свойства перед выполнением операции подключения. Рекомендуется устанавливать все свойства элемента управления сразу после создания объекта **MessageWebSocket** . Не пытайтесь изменить свойства элементов управления, кроме типа сообщений, после вызова [**MessageWebSocket.ConnectAsync**](https://msdn.microsoft.com/library/windows/apps/br226859).

## Классы информации WebSocket

[**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842) и [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923) имеют соответствующий класс, который обеспечивает дополнительную информацию об экземпляре WebSocket.

[**MessageWebSocketInformation**](https://msdn.microsoft.com/library/windows/apps/br226849) предоставляет информацию о [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842), и вы можете извлечь экземпляр информационного класса с помощью свойства [**MessageWebSocket.Information**](https://msdn.microsoft.com/library/windows/apps/br226861).

[**StreamWebSocketInformation**](https://msdn.microsoft.com/library/windows/apps/br226929) предоставляет информацию о [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923), и вы можете извлечь экземпляр информационного класса с помощью свойства [**StreamWebSocket.Information**](https://msdn.microsoft.com/library/windows/apps/br226935).

Обратите внимание, что все свойства в обоих классах информации приведены только для чтения, а также что текущие сведения можно извлечь в любое время, пока существует объект веб-сокета.

## Обработка сетевых исключений

Ошибка, обнаруженная в операции [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842) или [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923), возвращается в виде значения **HRESULT**. Метод [**WebSocketError.GetStatus**](https://msdn.microsoft.com/library/windows/apps/hh701529) преобразует сетевую ошибку, произошедшую в операции с WebSocket, в значение перечисления [**WebErrorStatus**](https://msdn.microsoft.com/library/windows/apps/hh747818). Большинство значений перечисления **WebErrorStatus** соответствуют ошибке, возвращаемой стандартной операцией клиента HTTP. Ваше приложение может фильтровать полученные данные по определенному значению перечисления **WebErrorStatus**, чтобы по-разному действовать в зависимости от причины исключения.

Для ошибок при проверке параметров приложение может использовать также **HRESULT** из исключения, чтобы получить более подробные сведения об ошибке, которая вызвала исключение. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Для многих ошибок при проверке параметров возвращаемым **HRESULT** является **E\_INVALIDARG**.

## Назначение времени ожидания при выполнении операций с WebSocket

Классы MessageWebSocket и StreamWebSocket используют внутреннюю системную службу для отправки запросов клиента WebSocket и получения откликов от сервера. Время ожидания, используемое для операции подключения WebSocket, по умолчанию составляет 60секунд. Если HTTP-сервер, поддерживающий WebSocket, временно недоступен либо блокируется в результате отказа сети, и сервер не отвечает или не может ответить на запрос подключения WebSocket, внутренняя системная служба ожидает заданные по умолчанию 60секунд, а затем возвращает ошибку, которая приводит к созданию исключения по методу WebSocket ConnectAsync. Если запрос имени HTTP-сервера в URI возвращает несколько IP-адресов, то перед завершением с ошибкой внутренняя системная служба проверяет до 5 IP-адресов для сайта, по умолчанию ожидая ответа по каждому адресу в течение 60секунд. Приложение, создающее запрос подключения WebSocket, может ожидать несколько минут, повторяя попытки соединения с несколькими IP-адресами, а затем возвращает ошибку и создает исключение. Пользователю в этом случае может показаться, что приложение не работает. Время ожидания, используемое по умолчанию для операций отправки и получения после установления подключения WebSocket, составляет 30секунд.

Чтобы уменьшить влияние таких проблем и ускорить ответ приложения, можно задать более короткое время ожидания отклика на запросы соединения, чтобы операция завершалась с ошибкой раньше, чем при параметрах по умолчанию.

Время ожидания задается одинаково для StreamWebSockets и MessageWebSockets. В следующем примере показано, как задавать время ожидания для StreamWebSocket; для MessageWebSocket выполняется аналогичный процесс.

1.  Используя таймер, создайте задачу, которая выполняется после заданного времени ожидания.
2.  Создайте задачу для операции с WebSocket с cancellation\_token\_source для возможности отмены.
3.  Если задача с заданным временем ожидания завершится до операции подключения WebSocket, отмените задачу для операции WebSocket.

В следующем примере создается одна задача, которая завершается после указанной задержки, а затем создается вторая задача, которая отменяется после указанной задержки. Эти классы можно использовать с StreamWebSocket и MessageWebSocket при попытке установить подключение для установки определенного времени ожидания. В примере использования вызывается метод StreamWebSocket.ConnectAsync в задаче с cancellation\_token\_source с возможностью отмены. Если вначале истекает время ожидания, cancellation\_token\_source применяется для отмены задачи для выполнения операции подключения WebSocket.

```cpp
    #include <agents.h>
    #include <ppl.h>
    #include <ppltasks.h>

    using namespace concurrency;
    using namespace std;

    // Creates a task that completes after the specified delay.
    task<void> complete_after(unsigned int timeout)
    {
        // A task completion event that is set when a timer fires.
        task_completion_event<void> tce;

        // Create a non-repeating timer.
        shared_ptr<timer<int>> fire_once(new timer<int>(timeout, 0, nullptr, false));
        
        // Create a call object that sets the completion event after the timer fires.
        shared_ptr<call<int>> callback(new call<int>([tce](int)
        {
            tce.set();
        }));

        // Connect the timer to the callback and start the timer.
        fire_once->link_target(callback.get());
        fire_once->start();

        // Create a task that completes after the completion event is set.
        task<void> event_set(tce);

        // Create a continuation task that cleans up resources and
        // and return that continuation task.
        return event_set.then([callback, fire_once]()
        {
        });
    }

    // Cancels the provided task after the specifed delay, if the task
    // did not complete.
    template<typename T>
    task<T> cancel_after_timeout(task<T> t, cancellation_token_source cts, unsigned int timeout)
    {
        // Create a task that returns true after the specified task completes.
        task<bool> success_task = t.then([](T)
        {
            return true;
        });
        // Create a task that returns false after the specified timeout.
        task<bool> failure_task = complete_after(timeout).then([]
        {
            return false;
        });

        // Create a continuation task that cancels the overall task  
        // if the timeout task finishes first. 
        return (failure_task || success_task).then([t, cts](bool success)
        {
            if (!success)
            {
                // Set the cancellation token. The task that is passed as the 
                // t parameter should respond to the cancellation and stop 
                // as soon as it can.
                cts.cancel();
            }
 
            // Return the original task.
            return t;
        });
    }
```




<!--HONumber=Aug16_HO3-->


