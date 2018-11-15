---
author: stevewhims
description: Ниже приведены действия, которые необходимо выполнить для любого приложения, подключаемого к сети.
title: Основы работы с сетями
ms.assetid: 1F47D33B-6F00-4F74-A52D-538851FD38BE
ms.author: stwhi
ms.date: 06/01/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 50ac9fcf984fa6c4ebad7e480ebfc2d002256e26
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "6669471"
---
# <a name="networking-basics"></a>Основы работы в сети
Ниже приведены действия, которые необходимо выполнить для любого приложения, подключаемого к сети.

## <a name="capabilities"></a>Возможности
Для работы в сети необходимо добавить соответствующие элементы возможности в манифест приложения. Если в манифесте приложения не указана возможность подключения к сети, то приложение не сможет подключаться к сети, а все попытки подключиться к сети будут неудачными.

Ниже перечислены наиболее часто используемые возможности подключения к сети.

| Возможность | Описание |
|------------|-------------|
| **internetClient** | Предоставляет исходящий доступ к Интернету и сетям в общественных местах, таких как аэропорт или кафе. Программы, которым требуется доступ в Интернет, должны использовать эту возможность. |
| **internetClientServer** | Предоставляет приложению входящий и исходящий доступ к сети из Интернета и сетей в общественных местах, таких как аэропорт или кафе. |
| **privateNetworkClientServer** | Предоставляет приложению входящий и исходящий доступ к сети в доверенных местах пользователя, таких как дом или рабочее место. |

Существуют и другие возможности, которые могут быть необходимы для вашего приложения при определенных обстоятельствах.

| Возможность | Описание |
|------------|-------------|
| **enterpriseAuthentication** | Позволяет приложению подключаться к сетевым ресурсам, для которых требуются учетные данные домена. Для этой характеристики необходимо, чтобы администратор домена включил такую функциональность для всех приложений. В качестве примера можно привести приложение, которое получает данные с серверов SharePoint в частной интрасети. <br/> С этой характеристикой ваши учетные данные могут быть использованы для доступа к сетевым ресурсам в сети, для которой требуются учетные данные. Приложение с этой характеристикой может выполнять вход в сеть под вашим именем. <br/> Эта возможность не требуется, если нужно разрешить приложению доступ к Интернету через прокси-сервер, выполняющий проверку подлинности. |
| **proximity** | Требуется для близкого взаимодействия с устройствами, расположенными недалеко от компьютера. Близкое взаимодействие в ближайшей зоне может быть использовано для отправки или соединения с приложением на находящемся рядом устройстве. <br/> Эта возможность предоставляет приложению доступ к сети для подключения к расположенному недалеко устройству, если пользователь согласен отправить или принять приглашение. |
| **sharedUserCertificates** | Предоставляет приложению доступ к сертификатам программного обеспечения и оборудования (например, сертификатам смарт-карт). Когда эта возможность вызывается во время выполнения, пользователь должен совершить действие (например, вставить карту или выбрать сертификат). <br/> В приложении с этой характеристикой для идентификации применяются сертификаты программного обеспечения и оборудования или смарт-карта. Эта характеристика может быть использована для идентификации работодателем, банком или государственными службами. |

## <a name="communicating-when-your-app-is-not-in-the-foreground"></a>Взаимодействовать, когда приложение выполняется не на переднем плане
[Поддержка приложения с помощью фоновых задач](https://msdn.microsoft.com/library/windows/apps/mt299103) содержит общие сведения об использовании фоновых задач для выполнения работы, когда приложение не находится на переднем плане. Если точнее, ваш код должен выполнить специальные действия, чтобы получать уведомление, когда он не является текущим приложением переднего плана и по сети поступают данные для него. Вы использовали триггеры канала управления для этой цели в Windows8 и по-прежнему поддерживаются в Windows10. Все сведения об использовании триггеров канала управления доступны [**здесь**](https://msdn.microsoft.com/library/windows/apps/hh701032). Новая технология в Windows10 имеет лучшую функциональность с более низкой нагрузкой для некоторых сценариев, например сокетами потока с поддержкой push-уведомлений: брокер сокета и триггеры активности сокетов.

Если ваше приложение использует [**DatagramSocket**](https://msdn.microsoft.com/library/windows/apps/br241319), [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) или [**StreamSocketListener**](https://msdn.microsoft.com/library/windows/apps/br226906), то оно может передать право собственности на открытый сокет брокеру сокета, предоставляемому системой, а затем покинуть передний план или даже завершить работу. Если подключение устанавливается на передаваемом сокете или трафик поступает на этот сокет, то активируется приложение или его назначенная фоновая задача. Если приложение не запущено, оно запускается. Затем брокер сокетов уведомляет приложение о поступлении нового трафика с помощью [**SocketActivityTrigger**](https://msdn.microsoft.com/library/windows/apps/dn806009). Приложение исправляет сокет от брокера сокетов и обрабатывает трафик на сокете. Это значит, что ваше приложение использует значительно меньше системных ресурсов, когда оно активно не обрабатывает сетевой трафик.

Брокер сокетов может заменить триггеры канала управления, где это применимо, потому что он обеспечивает такую же функциональность, но обладает меньшими ограничениями и меньшим объемом используемой памяти. Брокер сокетов может использоваться приложениями, которые не являются приложениями на экране блокировки, и он используется на телефонах аналогично другим устройствам. Для активации брокером сокета необязательно запускать приложения при поступлении трафика. Брокер сокета поддерживает прослушивание на сокетах TCP, которые не поддерживают триггеры канала управления.

### <a name="choosing-a-network-trigger"></a>Выбор сетевого триггера
Существуют определенные сценарии, когда любой тип триггера будет соответствующим. При выборе типа триггера для использования в своем приложении не забывайте следующие рекомендации.

-   Если вы используете [**IXMLHTTPRequest2**](https://msdn.microsoft.com/library/windows/desktop/hh831151), [**System.Net.Http.HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639) или [System.Net.Http.HttpClientHandler](http://go.microsoft.com/fwlink/p/?linkid=241638), вы должны использовать [**ControlChannelTrigger**](https://msdn.microsoft.com/library/windows/apps/hh701032).
-   Если вы используете **StreamSockets** с поддержкой push-уведомлений, можно использовать триггеры канала управления, но лучше всего использовать [**SocketActivityTrigger**](https://msdn.microsoft.com/library/windows/apps/dn806009). Последний вариант позволяет системе освободить память и снизить энергопотребление, когда подключение активно не используется.
-   Если вы хотите сократить используемый объем памяти приложения, когда приложение активно не обслуживает запросы сети, отдайте предпочтение [**SocketActivityTrigger**](https://msdn.microsoft.com/library/windows/apps/dn806009), если это возможно.
-   Если вы хотите, чтобы ваше приложение могло получать данные, когда система находится в режиме ожидания с подключением, используйте [**SocketActivityTrigger**](https://msdn.microsoft.com/library/windows/apps/dn806009).

Дополнительные сведения и примеры использования брокера сокетов см. в разделе [Сетевые подключения в фоновом режиме](network-communications-in-the-background.md).

## <a name="secured-connections"></a>Безопасные подключения
SSL и его преемник TLS— это протоколы шифрования, разработанные для проверки подлинности и шифрования сетевых подключений. Они предназначены для предотвращения перехвата и незаконного изменения данных, отправляемых и получаемых по сети. Для обмена данными по этим протоколам используется модель клиент-сервер. Эти протоколы также используют цифровые сертификаты и центры сертификации для проверки подлинности сервера.

### <a name="creating-secure-socket-connections"></a>Создание защищенных соединений сокетов
Объект [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) можно настроить так, чтобы использовать протокол SSL/TLS для обмена данными между клиентом и сервером. Эта поддержка протокола SSL/TLS ограничена использованием объекта **StreamSocket** в качестве клиента при согласовании SSL/TLS. Невозможно использовать протокол SSL/TLS с объектом **StreamSocket**, созданным [**StreamSocketListener**](https://msdn.microsoft.com/library/windows/apps/br226906), когда принимаются входящие сообщения, так как согласование SSL и TLS в качестве сервера не реализовано классом **StreamSocket**.

Есть два способа защитить подключение [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) с помощью протокола SSL/TLS:

-   [**ConnectAsync**](https://msdn.microsoft.com/library/windows/apps/hh701504) — устанавливает начальное подключение к сетевой службе и сразу согласовывает использование протокола SSL/TLS для всех подключений.
-   [**UpgradeToSslAsync**](https://msdn.microsoft.com/library/windows/apps/br226922) — сначала подключается к сетевой службе без использования шифрования. Приложение может отправлять и получать данные. Затем обновляет подключение, чтобы использовать протокол SSL/TLS для всех дальнейших подключений.

SocketProtectionLevel указывает необходимый уровень защиты сокета, который требуется приложению, чтобы установить или обновить подключение. Однако полученный уровень защиты установленного соединения определяется в процессе согласования между двумя конечными точками подключения. Результатом может быть менее безопасный уровень защиты, чем указанный, если другая конечная точка запрашивает более низкий уровень. 

 После успешного выполнения асинхронной операции можно получить запрошенный уровень защиты, используемый в вызове [**ConnectAsync**](https://msdn.microsoft.com/library/windows/apps/hh701504) или [**UpgradeToSslAsync**](https://msdn.microsoft.com/library/windows/apps/br226922) с помощью свойства [**StreamSocketinformation.ProtectionLevel**](https://msdn.microsoft.com/library/windows/apps/hh967868). Однако это не отражает фактический уровень защиты соединения.

> [!NOTE]
> Примечание. Код не должен зависеть от использования определенного уровня защиты или предположения, что заданный уровень безопасности используется по умолчанию. Среда безопасности постоянно изменяется. Протоколы и уровни защиты по умолчанию будут также изменяться со временем, чтобы не допустить использование протоколов с известными слабыми местами. Значения по умолчанию могут отличаться в зависимости от конфигурации отдельного компьютера или установленного программного обеспечения и примененных исправлений. Если поведение приложения зависит от использования определенного уровня безопасности, необходимо явно указать этот уровень и убедиться, что этот уровень действительно используется в установленном подключении.

### <a name="use-connectasync"></a>Использование ConnectAsync
[**ConnectAsync**](https://msdn.microsoft.com/library/windows/apps/hh701504) можно использовать для установки начального подключения к сетевой службе и немедленного согласования использования протокола SSL/TLS для всех подключений. Существует два метода **ConnectAsync**, поддерживающих передачу параметра *protectionLevel*:

-   [**ConnectAsync(EndpointPair, SocketProtectionLevel)**](https://msdn.microsoft.com/library/windows/apps/hh701511) — начинает асинхронную операцию с объектом [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882), чтобы подключиться к удаленному месту назначения в сети, указанному как объект [**EndpointPair**](https://msdn.microsoft.com/library/windows/apps/hh700953) и [**SocketProtectionLevel**](https://msdn.microsoft.com/library/windows/apps/br226880).
-   [**ConnectAsync(HostName, String, SocketProtectionLevel)**](https://msdn.microsoft.com/library/windows/apps/br226916) — начинает асинхронную операцию с объектом [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882), чтобы подключиться к удаленному месту назначения, указанному как имя удаленного узла, имя удаленной службы и [**SocketProtectionLevel**](https://msdn.microsoft.com/library/windows/apps/br226880).

Если параметр *protectionLevel* задан как **Windows.Networking.Sockets.SocketProtectionLevel.Ssl** при вызове любого из упомянутых выше методов [**ConnectAsync**](https://msdn.microsoft.com/library/windows/apps/hh701504), то [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) должен использовать протокол SSL/TLS для шифрования. Это значение требует применения шифрования и не допускает шифр NULL.

Применяется та же обычная последовательность с одним из этих методов [**ConnectAsync**](https://msdn.microsoft.com/library/windows/apps/hh701504).

-   Создайте объект [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882).
-   Если для сокета необходим дополнительный параметр, используйте свойство [**StreamSocket.Control**](https://msdn.microsoft.com/library/windows/apps/br226917), чтобы получить экземпляр [**StreamSocketControl**](https://msdn.microsoft.com/library/windows/apps/br226893), связанный с объектом [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882). Задайте свойство в **StreamSocketControl**.
-   Вызовите один из упомянутых выше методов [**ConnectAsync**](https://msdn.microsoft.com/library/windows/apps/hh701504), чтобы начать операцию подключения к удаленному месту назначения, и сразу согласуйте использование протокола SSL/TLS.
-   Фактически согласованную с помощью [**ConnectAsync**](https://msdn.microsoft.com/library/windows/apps/hh701504) надежность протокола SSL можно определить с помощью свойства [**StreamSocketinformation.ProtectionLevel**](https://msdn.microsoft.com/library/windows/apps/hh967868) после успешного завершения асинхронной операции.

В следующем примере создается [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882), а также выполняется попытка установить подключение к сетевой службе и сразу согласовать использование протокола SSL/TLS. Если согласование выполнено успешно, все сетевые подключения, использующие **StreamSocket**, между клиентом и сетевым сервером будут зашифрованы.

```csharp
using Windows.Networking;
using Windows.Networking.Sockets;

    // Define some variables and set values
    StreamSocket clientSocket = new StreamSocket();
     
    HostName serverHost = new HostName("www.contoso.com");
    string serverServiceName = "https";
    
    // For simplicity, the sample omits implementation of the
    // NotifyUser method used to display status and error messages 
    
    // Try to connect to contoso using HTTPS (port 443)
    try {

        // Call ConnectAsync method with SSL
        await clientSocket.ConnectAsync(serverHost, serverServiceName, SocketProtectionLevel.Ssl);

        NotifyUser("Connected");
    }
    catch (Exception exception) {
        // If this is an unknown status it means that the error is fatal and retry will likely fail.
        if (SocketError.GetStatus(exception.HResult) == SocketErrorStatus.Unknown) {
            throw;
        }
        
        NotifyUser("Connect failed with error: " + exception.Message);
        // Could retry the connection, but for this simple example
        // just close the socket.
        
        clientSocket.Dispose();
        clientSocket = null; 
    }
           
    // Add code to send and receive data using the clientSocket
    // and then close the clientSocket
```

```cppwinrt
#include <winrt/Windows.Networking.Sockets.h>

using namespace winrt;
...
    // Define some variables, and set values.
    Windows::Networking::Sockets::StreamSocket clientSocket;

    Windows::Networking::HostName serverHost{ L"www.contoso.com" };
    winrt::hstring serverServiceName{ L"https" };

    // For simplicity, the sample omits implementation of the
    // NotifyUser method used to display status and error messages.

    // Try to connect to the server using HTTPS and SSL (port 443).
    try
    {
        co_await clientSocket.ConnectAsync(serverHost, serverServiceName, Windows::Networking::Sockets::SocketProtectionLevel::Tls12);
        NotifyUser(L"Connected");
    }
    catch (winrt::hresult_error const& exception)
    {
        NotifyUser(L"Connect failed with error: " + exception.message());
        clientSocket = nullptr;
    }
    // Add code to send and receive data using the clientSocket,
    // then set the clientSocket to nullptr when done to close it.
```

```cpp
using Windows::Networking;
using Windows::Networking::Sockets;

    // Define some variables and set values
    StreamSocket^ clientSocket = new ref StreamSocket();
 
    HostName^ serverHost = new ref HostName("www.contoso.com");
    String serverServiceName = "https";

    // For simplicity, the sample omits implementation of the
    // NotifyUser method used to display status and error messages 

    // Try to connect to the server using HTTPS and SSL (port 443)
    task<void>(clientSocket->ConnectAsync(serverHost, serverServiceName, SocketProtectionLevel::SSL)).then([this] (task<void> previousTask) {
        try
        {
            // Try getting all exceptions from the continuation chain above this point.
            previousTask.Get();
            NotifyUser("Connected");
        }
        catch (Exception^ exception)
        {
            NotifyUser("Connect failed with error: " + exception->Message);
            
            clientSocket.Close();
            clientSocket = null;
        }
    });
    // Add code to send and receive data using the clientSocket
    // Then close the clientSocket when done
```

### <a name="use-upgradetosslasync"></a>Использование UpgradeToSslAsync
Если код использует [**UpgradeToSslAsync**](https://msdn.microsoft.com/library/windows/apps/br226922), сначала он устанавливает подключение к сетевой службе без шифрования. Приложение может отправлять и получать данные. Затем подключение обновляется, чтобы использовать протокол SSL/TLS для всех дальнейших подключений.

Метод [**UpgradeToSslAsync**](https://msdn.microsoft.com/library/windows/apps/br226922) принимает два параметра. Параметр *protectionLevel* обозначает нужный уровень защиты. Параметр *validationHostName* — это имя удаленного места назначения в сети, которое используется для проверки при обновлении до протокола SSL. Как правило, *validationHostName* содержит то же имя узла, которое приложение использовало для начального установления подключения. Если параметр *protectionLevel* задан как **Windows.System.Socket.SocketProtectionLevel.Ssl** при вызове **UpgradeToSslAsync**, то [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) должен использовать протокол SSL/TLS для шифрования в будущих подключениях через сокет. Это значение требует применения шифрования и не допускает шифр NULL.

Обычная последовательность, которая должна использоваться с методом [**UpgradeToSslAsync**](https://msdn.microsoft.com/library/windows/apps/br226922), описана ниже:

-   Создайте объект [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882).
-   Если для сокета необходим дополнительный параметр, используйте свойство [**StreamSocket.Control**](https://msdn.microsoft.com/library/windows/apps/br226917), чтобы получить экземпляр [**StreamSocketControl**](https://msdn.microsoft.com/library/windows/apps/br226893), связанный с объектом [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882). Задайте свойство в **StreamSocketControl**.
-   Если нужно отправить и получить какие-либо данные без шифрования, сделайте это сейчас.
-   Вызовите метод [**UpgradeToSslAsync**](https://msdn.microsoft.com/library/windows/apps/br226922), чтобы начать обновление подключения для использования протокола SSL/TLS.
-   Фактически согласованную с помощью [**UpgradeToSslAsync**](https://msdn.microsoft.com/library/windows/apps/br226922) надежность протокола SSL можно определить с помощью свойства [**StreamSocketinformation.ProtectionLevel**](https://msdn.microsoft.com/library/windows/apps/hh967868) после успешного завершения асинхронной операции.

В следующем примере создается [**StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882), выполняется попытка установить подключение к сетевой службе, отправляются некоторые исходные данные, а затем согласовывается использование протокола SSL/TLS. Если согласование выполнено успешно, все сетевые подключения, использующие **StreamSocket**, между клиентом и сетевым сервером будут зашифрованы.

```csharp
using Windows.Networking;
using Windows.Networking.Sockets;
using Windows.Storage.Streams;

    // Define some variables and set values
    StreamSocket clientSocket = new StreamSocket();
 
    HostName serverHost = new HostName("www.contoso.com");
    string serverServiceName = "http";

    // For simplicity, the sample omits implementation of the
    // NotifyUser method used to display status and error messages 

    // Try to connect to contoso using HTTP (port 80)
    try {
        // Call ConnectAsync method with a plain socket
        await clientSocket.ConnectAsync(serverHost, serverServiceName, SocketProtectionLevel.PlainSocket);

        NotifyUser("Connected");

    }
    catch (Exception exception) {
        // If this is an unknown status it means that the error is fatal and retry will likely fail.
        if (SocketError.GetStatus(exception.HResult) == SocketErrorStatus.Unknown) {
            throw;
        }

        NotifyUser("Connect failed with error: " + exception.Message, NotifyType.ErrorMessage);
        // Could retry the connection, but for this simple example
        // just close the socket.

        clientSocket.Dispose();
        clientSocket = null; 
        return;
    }

    // Now try to send some data
    DataWriter writer = new DataWriter(clientSocket.OutputStream);
    string hello = "Hello, World! ☺ ";
    Int32 len = (int) writer.MeasureString(hello); // Gets the UTF-8 string length.
    writer.WriteInt32(len);
    writer.WriteString(hello);
    NotifyUser("Client: sending hello");

    try {
        // Call StoreAsync method to store the hello message
        await writer.StoreAsync();

        NotifyUser("Client: sent data");

        writer.DetachStream(); // Detach stream, if not, DataWriter destructor will close it.
    }
    catch (Exception exception) {
        NotifyUser("Store failed with error: " + exception.Message);
        // Could retry the store, but for this simple example
            // just close the socket.

            clientSocket.Dispose();
            clientSocket = null; 
            return;
    }

    // Now upgrade the client to use SSL
    try {
        // Try to upgrade to SSL
        await clientSocket.UpgradeToSslAsync(SocketProtectionLevel.Ssl, serverHost);

        NotifyUser("Client: upgrade to SSL completed");
           
        // Add code to send and receive data 
        // The close clientSocket when done
    }
    catch (Exception exception) {
        // If this is an unknown status it means that the error is fatal and retry will likely fail.
        if (SocketError.GetStatus(exception.HResult) == SocketErrorStatus.Unknown) {
            throw;
        }

        NotifyUser("Upgrade to SSL failed with error: " + exception.Message);

        clientSocket.Dispose();
        clientSocket = null; 
        return;
    }
```

```cppwinrt
#include <winrt/Windows.Networking.Sockets.h>
#include <winrt/Windows.Storage.Streams.h>

using namespace winrt;
using namespace Windows::Storage::Streams;
...
    // Define some variables, and set values.
    Windows::Networking::Sockets::StreamSocket clientSocket;

    Windows::Networking::HostName serverHost{ L"www.contoso.com" };
    winrt::hstring serverServiceName{ L"https" };

    // For simplicity, the sample omits implementation of the
    // NotifyUser method used to display status and error messages. 

    // Try to connect to the server using HTTP (port 80).
    try
    {
        co_await clientSocket.ConnectAsync(serverHost, serverServiceName, Windows::Networking::Sockets::SocketProtectionLevel::PlainSocket);
        NotifyUser(L"Connected");
    }
    catch (winrt::hresult_error const& exception)
    {
        NotifyUser(L"Connect failed with error: " + exception.message());
        clientSocket = nullptr;
    }

    // Now, try to send some data.
    DataWriter writer{ clientSocket.OutputStream() };
    winrt::hstring hello{ L"Hello, World! ☺ " };
    uint32_t len{ writer.MeasureString(hello) }; // Gets the size of the string, in bytes.
    writer.WriteInt32(len);
    writer.WriteString(hello);
    NotifyUser(L"Client: sending hello");

    try
    {
        co_await writer.StoreAsync();
        NotifyUser(L"Client: sent hello");

        writer.DetachStream(); // Detach the stream when you want to continue using it; otherwise, the DataWriter destructor closes it.
    }
    catch (winrt::hresult_error const& exception)
    {
        NotifyUser(L"Store failed with error: " + exception.message());
        // We could retry the store operation. But, for this simple example, just close the socket by setting it to nullptr.
        clientSocket = nullptr;
        co_return;
    }

    // Now, upgrade the client to use SSL.
    try
    {
        co_await clientSocket.UpgradeToSslAsync(Windows::Networking::Sockets::SocketProtectionLevel::Tls12, serverHost);
        NotifyUser(L"Client: upgrade to SSL completed");

        // Add code to send and receive data using the clientSocket,
        // then set the clientSocket to nullptr when done to close it.
    }
    catch (winrt::hresult_error const& exception)
    {
        // If this is an unknown status, then the error is fatal and retry will likely fail.
        Windows::Networking::Sockets::SocketErrorStatus socketErrorStatus{ Windows::Networking::Sockets::SocketError::GetStatus(exception.to_abi()) };
        if (socketErrorStatus == Windows::Networking::Sockets::SocketErrorStatus::Unknown)
        {
            throw;
        }

        NotifyUser(L"Upgrade to SSL failed with error: " + exception.message());
        // We could retry the store operation. But for this simple example, just close the socket by setting it to nullptr.
        clientSocket = nullptr;
        co_return;
    }
```

```cpp
using Windows::Networking;
using Windows::Networking::Sockets;
using Windows::Storage::Streams;

    // Define some variables and set values
    StreamSocket^ clientSocket = new ref StreamSocket();
 
    Hostname^ serverHost = new ref HostName("www.contoso.com");
    String serverServiceName = "http";

    // For simplicity, the sample omits implementation of the
    // NotifyUser method used to display status and error messages 

    // Try to connect to contoso using HTTP (port 80)
    task<void>(clientSocket->ConnectAsync(serverHost, serverServiceName, SocketProtectionLevel::PlainSocket)).then([this] (task<void> previousTask) {
        try
        {
            // Try getting all exceptions from the continuation chain above this point.
            previousTask.Get();
            NotifyUser("Connected");
        }
        catch (Exception^ exception)
        {
            NotifyUser("Connect failed with error: " + exception->Message);
 
            clientSocket->Close();
            clientSocket = null;
        }
    });
       
    // Now try to send some data
    DataWriter^ writer = new ref DataWriter(clientSocket.OutputStream);
    String hello = "Hello, World! ☺ ";
    Int32 len = (int) writer->MeasureString(hello); // Gets the UTF-8 string length.
    writer->writeInt32(len);
    writer->writeString(hello);
    NotifyUser("Client: sending hello");

    task<void>(writer->StoreAsync()).then([this] (task<void> previousTask) {
        try {
            // Try getting all exceptions from the continuation chain above this point.
            previousTask.Get();

            NotifyUser("Client: sent hello");

            writer->DetachStream(); // Detach stream, if not, DataWriter destructor will close it.
       }
       catch (Exception^ exception) {
               NotifyUser("Store failed with error: " + exception->Message);
               // Could retry the store, but for this simple example
               // just close the socket.
 
               clientSocket->Close();
               clientSocket = null;
               return
       }
    });

    // Now upgrade the client to use SSL
    task<void>(clientSocket->UpgradeToSslAsync(clientSocket.SocketProtectionLevel.Ssl, serverHost)).then([this] (task<void> previousTask) {
        try {
            // Try getting all exceptions from the continuation chain above this point.
            previousTask.Get();

           NotifyUser("Client: upgrade to SSL completed");
           
           // Add code to send and receive data 
           // Then close clientSocket when done
        }
        catch (Exception^ exception) {
            // If this is an unknown status it means that the error is fatal and retry will likely fail.
            if (SocketError.GetStatus(exception.HResult) == SocketErrorStatus.Unknown) {
                throw;
            }

            NotifyUser("Upgrade to SSL failed with error: " + exception.Message);

            clientSocket->Close();
            clientSocket = null; 
            return;
        }
    });
```

### <a name="creating-secure-websocket-connections"></a>Создание защищенных соединений WebSocket
Подобно традиционным соединениям сокетов, для приложений UWP соединения WebSocket можно шифровать с помощью протоколов TLS/SSL при использовании компонентов [**StreamWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226923) и [**MessageWebSocket**](https://msdn.microsoft.com/library/windows/apps/br226842). В большинстве случаев необходимо использовать безопасное подключение WebSocket. Это увеличит шансы удачного подключения, поскольку многие прокси-серверы отклоняют незашифрованные подключения WebSocket.

Примеры создания и обновления защищенных соединений сокетов до сетевых служб см. в разделе [Защита соединений WebSocket с помощью TLS/SSL](https://msdn.microsoft.com/library/windows/apps/xaml/hh994399).

Кроме шифрования TLS/SSL для завершения исходного подтверждения серверу может потребоваться значение заголовка **Sec-WebSocket-Protocol**. Это значение, представляемое свойствами [**StreamWebSocketInformation.Protocol**](https://msdn.microsoft.com/library/windows/apps/hh701514) и [**MessageWebSocketInformation.Protocol**](https://msdn.microsoft.com/library/windows/apps/hh701358), указывает версию протокола соединения и позволяет серверу правильно интерпретировать открытое подтверждение и обмениваемые впоследствии данные. Если на основе данной информации о протоколе в какой-то момент времени серверу не удается безопасно интерпретировать входящие данные, такое соединение может быть закрыто.

Если в исходном запросе от клиента этого значения нет или предоставленное значение не соответствует ожидаемому сервером, в случае ошибки подтверждения WebSocket ожидаемое значение отправляется сервером клиенту.

## <a name="authentication"></a>Проверка подлинности
Как предоставить учетные данные для проверки подлинности при подключении через сеть.

### <a name="providing-a-client-certificate-with-the-streamsocket-class"></a>Предоставление сертификата клиента с классом StreamSocket
Класс [**Windows.Networking.StreamSocket**](https://msdn.microsoft.com/library/windows/apps/br226882) поддерживает возможность использования протокола SSL/TLS для проверки подлинности сервера, к которому обращается приложение. В определенных случаях приложение также должно пройти проверку подлинности на сервере с помощью сертификата клиента TLS. В Windows10 вы можете предоставить сертификат клиента в объекте [**StreamSocket.Control**](https://msdn.microsoft.com/library/windows/apps/br226893) (необходимо задать перед началом подтверждения TLS). Если сервер запрашивает сертификат клиента, Windows ответит, воспользовавшись предоставленным сертификатом.

Фрагмент кода, демонстрирующий данную операцию:

```csharp
var socket = new StreamSocket();
Windows.Security.Cryptography.Certificates.Certificate certificate = await GetClientCert();
socket.Control.ClientCertificate = certificate;
await socket.ConnectAsync(destination, SocketProtectionLevel.Tls12);
```

### <a name="providing-authentication-credentials-to-a-web-service"></a>Предоставление веб-службе учетных данных для проверки подлинности
Сетевые API, позволяющие приложениям взаимодействовать с защищенными веб-службами, предоставляют собственные методы инициализации клиента или формирования заголовка запроса с учетными данными для проверки подлинности на сервере и прокси-сервере. Для каждого метода задается объект [**PasswordCredential**](https://msdn.microsoft.com/library/windows/apps/br227061), указывающий имя пользователя, пароль и ресурс, для которого используются эти учетные данные. Ниже представлена таблица соответствия этих API.

| **WebSockets** | [**MessageWebSocketControl.ServerCredential**](https://msdn.microsoft.com/library/windows/apps/br226848) |
|-------------------------|----------------------------------------------------------------------------------------------------------|
|  | [**MessageWebSocketControl.ProxyCredential**](https://msdn.microsoft.com/library/windows/apps/br226847) |
|  | [**StreamWebSocketControl.ServerCredential**](https://msdn.microsoft.com/library/windows/apps/br226928) |
|  | [**StreamWebSocketControl.ProxyCredential**](https://msdn.microsoft.com/library/windows/apps/br226927) |
| **Передача в фоновом режиме** | [**BackgroundDownloader.ServerCredential**](https://msdn.microsoft.com/library/windows/apps/hh701076) |
|  | [**BackgroundDownloader.ProxyCredential**](https://msdn.microsoft.com/library/windows/apps/hh701068) |
|  | [**BackgroundUploader.ServerCredential**](https://msdn.microsoft.com/library/windows/apps/hh701184) |
|  | [**BackgroundUploader.ProxyCredential**](https://msdn.microsoft.com/library/windows/apps/hh701178) |
| **Синдикация** | [**SyndicationClient(PasswordCredential)**](https://msdn.microsoft.com/library/windows/apps/hh702355) |
|  | [**SyndicationClient.ServerCredential**](https://msdn.microsoft.com/library/windows/apps/br243461) |
|  | [**SyndicationClient.ProxyCredential**](https://msdn.microsoft.com/library/windows/apps/br243459) |
| **AtomPub** | [**AtomPubClient(PasswordCredential)**](https://msdn.microsoft.com/library/windows/apps/hh702262) |
|  | [**AtomPubClient.ServerCredential**](https://msdn.microsoft.com/library/windows/apps/br243428) |
|  | [**AtomPubClient.ProxyCredential**](https://msdn.microsoft.com/library/windows/apps/br243423) |

## <a name="handling-network-exceptions"></a>Обработка сетевых исключений
В большинстве областей программирования исключение указывает на существенную проблему или ошибку, вызванную неполадкой в программе. В сетевом программировании существует дополнительный источник исключений: сама сеть и характер сетевых подключений. Сетевые подключения по сути ненадежны и сопровождаются непредвиденными ошибками. Для каждого из способов использования сети приложением необходимо сохранять некоторые сведения о состоянии; и код вашего приложения должно обрабатывать сетевые исключения, обновляя эти сведения о состоянии и инициируя соответствующую логику, чтобы ваше приложение повторно установило подключение или попыталось решить проблему со связью.

Когда в универсальных приложениях для Windows отображается исключение, обработчик исключений может получить подробную информацию о причине исключения, чтобы разобраться в проблеме и принять необходимые меры.

В каждой языковой проекции поддерживается метод для доступа к подробной информации. Исключение проецируется в виде значения **HRESULT** в универсальных приложениях для Windows. *Winerror.h* включает файл, содержащий большой список возможных значений **HRESULT**, в том числе сетевые ошибки.

Сетевые API поддерживают различные методы получения такой подробной информации о причине исключения.

-   Некоторые API предоставляют вспомогательный метод, который преобразовывает значение **HRESULT** из исключения в значение перечисления.
-   Другие API предоставляют метод для получения фактического значения **HRESULT**.

## <a name="related-topics"></a>Ссылки по теме
* [Улучшения сетевых API в Windows10](http://blogs.windows.com/buildingapps/2015/07/02/networking-api-improvements-in-windows-10/)
