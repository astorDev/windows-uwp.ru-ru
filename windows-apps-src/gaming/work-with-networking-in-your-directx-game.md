---
title: Поддержка сети в играх
description: Узнайте, как разрабатывать и включать компоненты для работы в сети в игру DirectX.
ms.assetid: 212eee15-045c-8ba1-e274-4532b2120c55
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, игры, работа по сети, directx
ms.localizationpriority: medium
ms.openlocfilehash: 005dc30bc71d6d9087a3cc15880ffa3936d0a7f7
ms.sourcegitcommit: 22ed0d4edad5e6bab352e641cf86cf455cf83825
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133967"
---
# <a name="networking-for-games"></a>Поддержка сети в играх



Узнайте, как разрабатывать и включать компоненты для работы в сети в игру DirectX.

## <a name="concepts-at-a-glance"></a>Краткий обзор понятий


Разнообразные сетевые компоненты можно использовать в любых играх DirectX — от простых автономных до многопользовательских. Простейший пример использования сети — хранение имен пользователей и игровых результатов на центральном сервере.

Сетевые API необходимы в многопользовательских играх, которые используют инфраструктурную модель (клиент-сервер или одноранговое интернет-подключение), а также в играх с прямым (локальным одноранговым) подключением. В случае серверных многопользовательских игр центральный сервер обычно обрабатывает большинство игровых операций, а клиентское игровое приложение используется для ввода, отображения графики, воспроизведения звука и других функций. Скорость и задержка передачи по сети влияют на качество взаимодействия между игроками.

В играх с одноранговым подключением приложение каждого игрока обрабатывает вводимые данные и графику. В большинстве случаев игроки находятся близко друг к другу, поэтому задержка передачи по сети меньше, но проблема по-прежнему существует. Также возникает задача обнаружения одноранговых узлов и установления подключения.

В случае игр с одним участником центральный веб-сервер или служба часто используются для хранения имен пользователей, результатов и других данных. В таких играх скорость и задержка передачи по сети практически не представляют проблемы, так как они не влияют напрямую на игровые операции.

Состояние сети может измениться в любой момент, поэтому любая игра, использующая сетевые API, должна обрабатывать сетевые исключения. Подробнее об обработке сетевых исключений см. в разделе [Основы работы с сетями](https://docs.microsoft.com/windows/uwp/networking/networking-basics).

Широко распространенные брандмауэры и веб-прокси могут влиять на возможность использования сетевых компонентов. Игру, использующую сеть, нужно должным образом подготовить к взаимодействию с брандмауэрами и прокси-серверами.

В случае мобильных устройств важно следить за доступностью сетевых ресурсов и правильно реагировать, находясь в зоне действия сети с лимитным тарифным планом, где тарифы на роуминг и передачу данных могут быть высоки.

Сетевая изоляция является частью модели безопасности приложения, которая используется в Windows. Windows активно обнаруживает границы сети и применяет ограничения сетевого доступа для сетевой изоляции. Для определения уровня доступа к сетевым ресурсам приложения должны объявить возможности сетевой изоляции. В противном случае приложение не получит доступа к сетевым ресурсам. Подробнее о том, как Windows применяет сетевую изоляцию для приложений см. в разделе [Настройка возможностей сетевой изоляции](https://docs.microsoft.com/previous-versions/windows/apps/hh770532(v=win.10)).

## <a name="design-considerations"></a>Рекомендации по проектированию


В играх DirectX можно использовать разнообразные сетевые API. Поэтому важно выбрать именно тот, который подходит для ваших задач. Windows поддерживает широкий спектр сетевых API, с помощью которых приложение может взаимодействовать с другими компьютерами и устройствами либо через Интернет, либо через частные сети. Сначала нужно определить, какие сетевые компоненты требуются вашему приложению.

Вот некоторые наиболее популярные сетевые API для игр.

-   TCP и сокеты — обеспечивает надежное подключение. Используйте протокол TCP для игровых операций, не требующих обеспечения безопасности. Протокол TCP обеспечивает простое масштабирование сервера, поэтому он обычно используется в играх на базе инфраструктурной модели (клиент-сервер или одноранговое интернет-подключение). TCP также можно использовать для игр с прямым (локальным одноранговым) подключением по Wi-Fi Direct и Bluetooth. Как правило, TCP используется для перемещения игровых объектов, взаимодействия с персонажами, поддержки текстового чата и других операций. Класс [**стреамсоккет**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket) предоставляет сокет TCP, который можно использовать в Microsoft Store играх. Класс **StreamSocket** используется вместе со связанными классами в пространстве имен [**Windows::Networking::Sockets**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets).
-   TCP и сокеты с использованием SSL — обеспечивает надежное подключение, предотвращающее перехват. Используйте подключения TCP с SSL для игровых операций, для которых требуется безопасность. Шифрование и передача служебных данных SSL увеличивают задержку и снижают производительность, поэтому этот вариант используется, только если требуется безопасность. TCP с SSL, как правило, используется для входа в игру, покупки и продажи ресурсов, создания игровых персонажей и управления ими. Класс [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket) предоставляет TCP-сокет с поддержкой SSL.
-   UDP и сокеты — обеспечивают ненадежную передачу данных по сети с небольшим объемом служебных данных. Протокол UDP используется для игровых операций, в которых требуется малая задержка и допустима небольшая потеря пакетов. Он часто применяется для игр с боевыми действиями, шутеров и лабиринтов, сетевого звукового воспроизведения и голосового чата. Класс [**датаграмсоккет**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.DatagramSocket) предоставляет сокет UDP, который можно использовать в Microsoft Store играх. Класс **DatagramSocket** используется вместе со связанными классами в пространстве имен [**Windows::Networking::Sockets**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets).
-   HTTP-клиент — обеспечивает надежное подключение к HTTP-серверам. Наиболее распространенный сетевой сценарий — доступ к веб-сайту для извлечения или сохранения информации. Простым примером может быть игра, использующая веб-сайт для хранения данных о пользователях и результаты игры. В сочетании с SSL для обеспечения безопасности HTTP-клиент можно использовать для входа в игру, покупки и продажи ресурсов, создания игровых персонажей и управления ими. Класс [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) предоставляет современный API клиента HTTP для использования в Microsoft Store играх. Класс **HttpClient** используется вместе со связанными классами в пространстве имен [**Windows::Web::Http**](https://docs.microsoft.com/uwp/api/Windows.Web.Http).

## <a name="handling-network-exceptions-in-your-directx-game"></a>Обработка сетевых исключений в игре DirectX


Сетевое исключение, возникающее в игре DirectX, указывает на серьезную проблему или ошибку. При использовании сетевых API исключения могут возникать по многим причинам. Часто причиной исключения являются изменения в сетевых подключениях или другие сетевые проблемы удаленного узла или сервера.

Ниже приводятся некоторые причины исключений, возникающих при использовании сетевых API:

-   ввод недопустимого или содержащего ошибки имя узла или URI;
-   ошибки разрешения имен при поиске имени узла или URI;
-   потеря или изменение сетевого подключения;
-   ошибки сетевого подключения с использованием сокетов или клиентских API HTTP;
-   ошибки сетевого сервера или удаленных конечных точек;
-   другие сетевые ошибки.

Исключения из-за сетевых ошибок (например, потеря или изменение подключения, ошибки подключения и сбои сервера) могут возникать в любое время. В результате таких ошибок создаются исключения. Если ваше приложение не обрабатывает исключение, то среда выполнения может завершить работу приложения.

При вызове большинства асинхронных сетевых методов вам следует написать код для обработки исключений. Иногда, когда возникает исключение, можно снова вызвать сетевой метод и устранить проблему. В других случаях приложение должно быть готово продолжить работу без подключения к сети, используя данные из кэша.

Приложения универсальной платформы Windows (UWP) обычно вызывают одно исключение. Обработчик исключений может получить подробную информацию о причине исключения, чтобы разобраться в проблеме и принять необходимые меры.

При возникновении исключений в игре с DirectX, являющейся приложением UWP, можно получить значение **HRESULT** причины ошибки. *Winerror.h* включает файл, содержащий большой список возможных значений **HRESULT**, в том числе сетевые ошибки.

Сетевые API поддерживают различные методы получения такой подробной информации о причине исключения.

-   Метод получения значения **HRESULT** ошибки, которая вызвала исключение. Возможный список потенциальных значений **HRESULT** огромен и не определен. Значение **HRESULT** можно получить с помощью любых сетевых API.
-   Вспомогательный метод, преобразующий значение **HRESULT** в значение перечисления. Список возможных значений перечисления определен и относительно краток. Для классов сокетов в [**Windows::Networking::Sockets**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets) доступен вспомогательный метод.

### <a name="exceptions-in-windowsnetworkingsockets"></a>Исключения в Windows.Networking.Sockets

Конструктор класса [**HostName**](https://docs.microsoft.com/uwp/api/Windows.Networking.HostName), используемый с сокетами, может вызвать исключение, если переданная строка не является допустимым именем узла (содержит символы, которые не разрешено использовать в имени узла). Если пользователь вводит в приложение данные **HostName** для однорангового подключения к игре, конструктор должен находиться в блоке try/catch. Если вызывается исключение, приложение может уведомить об этом пользователя и запросить новое имя узла.

Добавление кода для проверки строки имени узла, введенного пользователем

```cpp

    // Define some variables at the class level.
    Windows::Networking::HostName^ remoteHost;

    bool isHostnameFromUser = false;
    bool isHostnameValid = false;

    ///...

    // If the value of 'remoteHostname' is set by the user in a control as input 
    // and is therefore untrusted input and could contain errors. 
    // If we can't create a valid hostname, we notify the user in statusText 
    // about the incorrect input.

    String ^hostString = remoteHostname;

    try 
    {
        remoteHost = ref new Windows::Networking:Host(hostString);
        isHostnameValid = true;
    }
    catch (InvalidArgumentException ^ex)
    {
        statusText->Text = "You entered a bad hostname, please re-enter a valid hostname.";
        return;
    }

    isHostnameFromUser = true;


    // ... Continue with code to execute with a valid hostname.
```

Пространство имен [**Windows.Networking.Sockets**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets) содержит удобные вспомогательные методы и перечисления для обработки ошибок, возникающих при использовании сокетов. Это удобно, чтобы по-разному обрабатывать в приложении различные сетевые исключения.

Ошибка, обнаруженная в операции [**DatagramSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.DatagramSocket), [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket) или [**StreamSocketListener**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocketListener), может привести к вызову исключения. Причиной исключения является значение ошибки, представленное как значение **HRESULT**. Метод [**SocketError.GetStatus**](https://docs.microsoft.com/uwp/api/windows.networking.sockets.socketerror.getstatus) преобразует сетевую ошибку, произошедшую в операции с сокетом, в значение перечисления [**SocketErrorStatus**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.SocketErrorStatus). Большинство значений перечисления **SocketErrorStatus** соответствуют ошибке, возвращаемой стандартной операцией с сокетами Windows. Приложение может фильтровать полученные данные по определенным значениям перечисления **SocketErrorStatus**, чтобы действовать в зависимости от причины исключения.

Для ошибок при проверке параметров приложение может использовать также **HRESULT** из исключения, чтобы получить более подробные сведения об ошибке, которая вызвала исключение. Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Для большинства ошибок проверки параметров возвращается значение **HRESULT** , равное **E \_ INVALIDARG**.

Добавление кода для обработки исключений при попытке подключения через сокет потока

```cpp
using namespace Windows::Networking;
using namespace Windows::Networking::Sockets;

    
    // Define some more variables at the class level.

    bool isSocketConnected = false
    bool retrySocketConnect = false;

    // The number of times we have tried to connect the socket.
    unsigned int retryConnectCount = 0;

    // The maximum number of times to retry a connect operation.
    unsigned int maxRetryConnectCount = 5; 
    ///...

    // We pass in a valid remoteHost and serviceName parameter.
    // The hostname can contain a name or an IP address.
    // The servicename can contain a string or a TCP port number.

    StreamSocket ^ socket = ref new StreamSocket();
    SocketErrorStatus errorStatus; 
    HResult hr;

    // Save the socket, so any subsequent steps can use it.
    CoreApplication::Properties->Insert("clientSocket", socket);

    // Connect to the remote server. 
    create_task(socket->ConnectAsync(
            remoteHost,
            serviceName,
            SocketProtectionLevel::PlainSocket)).then([this] (task<void> previousTask)
    {
        try
        {
            // Try getting all exceptions from the continuation chain above this point.
            previousTask.get();

            isSocketConnected = true;
            // Mark the socket as connected. We do not really care about the value of the property, but the mere 
            // existence of  it means that we are connected.
            CoreApplication::Properties->Insert("connected", nullptr);
        }
        catch (Exception^ ex)
        {
            hr = ex.HResult;
            errorStatus = SocketStatus::GetStatus(hr); 
            if (errorStatus != Unknown)
            {
                                                                switch (errorStatus) 
                   {
                    case HostNotFound:
                        // If the hostname is from the user, this may indicate a bad input.
                        // Set a flag to ask the user to re-enter the hostname.
                        isHostnameValid = false;
                        return;
                        break;
                    case ConnectionRefused:
                        // The server might be temporarily busy.
                        retrySocketConnect = true;
                        return;
                        break; 
                    case NetworkIsUnreachable: 
                        // This could be a connectivity issue.
                        retrySocketConnect = true;
                        break;
                    case UnreachableHost: 
                        // This could be a connectivity issue.
                        retrySocketConnect = true;
                        break;
                    case NetworkIsDown: 
                        // This could be a connectivity issue.
                        retrySocketConnect = true;
                        break;
                    // Handle other errors. 
                    default: 
                        // The connection failed and no options are available.
                        // Try to use cached data if it is available. 
                        // You may want to tell the user that the connect failed.
                        break;
                }
                }
                else 
                {
                    // Received an Hresult that is not mapped to an enum.
                    // This could be a connectivity issue.
                    retrySocketConnect = true;
                }
            }
        });
    }

```

### <a name="exceptions-in-windowswebhttp"></a>Исключения в Windows.Web.Http

Конструктор класса [**Windows::Foundation::Uri**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri), используемый с [**Windows::Web::Http::HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient), может вызвать исключение, если переданная строка не является допустимым URI (содержит символы, которые не разрешено использовать в URI). В C++ нет метода для преобразования строки в код URI. Если пользователь вводит в приложение данные **Windows::Foundation::Uri**, конструктор должен находиться в блоке try/catch. Если вызывается исключение, приложение может уведомить об этом пользователя и запросить новый URI.

Приложение также должно проверить, что схема URI соответствует протоколу HTTP или HTTPS, так как это единственные схемы, поддерживаемые [**Windows::Web::Http::HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient).

Добавление кода для проверки введенной пользователем строки для URI

```cpp

    // Define some variables at the class level.
    Windows::Foundation::Uri^ resourceUri;

    bool isUriFromUser = false;
    bool isUriValid = false;

    ///...

    // If the value of 'inputUri' is set by the user in a control as input 
    // and is therefore untrusted input and could contain errors. 
    // If we can't create a valid hostname, we notify the user in statusText 
    // about the incorrect input.

    String ^uriString = inputUri;

    try 
    {
        isUriValid = false;
        resourceUri = ref new Windows::Foundation:Uri(uriString);

        if (resourceUri->SchemeName != "http" && resourceUri->SchemeName != "https")
        {
            statusText->Text = "Only 'http' and 'https' schemes supported. Please re-enter URI";
            return;
        }
        isUriValid = true;
    }
    catch (InvalidArgumentException ^ex)
    {
        statusText->Text = "You entered a bad URI, please re-enter Uri to continue.";
        return;
    }

    isUriFromUser = true;


    // ... Continue with code to execute with a valid URI.
```

В пространстве имен [**Windows::Web::Http**](https://docs.microsoft.com/uwp/api/windows.web.http) отсутствует удобная функция. Таким образом, приложение, использующее [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) и другие классы в этом пространстве имен, должно использовать значение **HRESULT** .

В приложениях на C++ объект [**Platform::Exception**](https://docs.microsoft.com/cpp/cppcx/platform-exception-class) представляет ошибку во время выполнения приложения, когда возникает исключение. Свойство [**Platform:: Exception:: HRESULT**](https://docs.microsoft.com/cpp/cppcx/platform-exception-class#hresult) возвращает **значение HRESULT** , назначенное конкретному исключению. Свойство [**Platform:: Exception:: Message**](https://docs.microsoft.com/cpp/cppcx/platform-exception-class#message) Возвращает предоставленную системой строку, связанную со значением **HRESULT** . Возможные значения **HRESULT** перечислены в файле заголовка *Winerror.h*. Приложение может фильтровать полученные данные по определенному значению перечисления **HRESULT**, чтобы действовать в зависимости от причины исключения.

Для большинства ошибок проверки параметров возвращается значение **HRESULT** , равное **E \_ INVALIDARG**. Для некоторых непредусмотренных вызовов методов возвращаемым значением **HRESULT** будет **E\_ILLEGAL\_METHOD\_CALL**.

Добавление кода для обработки исключений при попытке использовать [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient) для подключения к HTTP-серверу

```cpp
using namespace Windows::Foundation;
using namespace Windows::Web::Http;
    
    // Define some more variables at the class level.

    bool isHttpClientConnected = false
    bool retryHttpClient = false;

    // The number of times we have tried to connect the socket
    unsigned int retryConnectCount = 0;

    // The maximum number of times to retry a connect operation.
    unsigned int maxRetryConnectCount = 5; 
    ///...

    // We pass in a valid resourceUri parameter.
    // The URI must contain a scheme and a name or an IP address.

    HttpClient ^ httpClient = ref new HttpClient();
    HResult hr;

    // Save the httpClient, so any subsequent steps can use it.
    CoreApplication::Properties->Insert("httpClient", httpClient);

    // Send a GET request to the HTTP server. 
    create_task(httpClient->GetAsync(resourceUri)).then([this] (task<void> previousTask)
    {
        try
        {
            // Try getting all exceptions from the continuation chain above this point.
            previousTask.get();

            isHttpClientConnected = true;
            // Mark the HttClient as connected. We do not really care about the value of the property, but the mere 
            // existence of  it means that we are connected.
            CoreApplication::Properties->Insert("connected", nullptr);
        }
        catch (Exception^ ex)
        {
            hr = ex.HResult;
                                                switch (errorStatus) 
               {
                case WININET_E_NAME_NOT_RESOLVED:
                    // If the Uri is from the user, this may indicate a bad input.
                    // Set a flag to ask user to re-enter the Uri.
                    isUriValid = false;
                    return;
                    break;
                case WININET_E_CANNOT_CONNECT:
                    // The server might be temporarily busy.
                    retryHttpClientConnect = true;
                    return;
                    break; 
                case WININET_E_CONNECTION_ABORTED: 
                    // This could be a connectivity issue.
                    retryHttpClientConnect = true;
                    break;
                case WININET_E_CONNECTION_RESET: 
                    // This could be a connectivity issue.
                    retryHttpClientConnect = true;
                    break;
                case INET_E_RESOURCE_NOT_FOUND: 
                    // The server cannot locate the resource specified in the uri.
                    // If the Uri is from user, this may indicate a bad input.
                    // Set a flag to ask the user to re-enter the Uri
                    isUriValid = false;
                    return;
                    break;
                // Handle other errors. 
                default: 
                    // The connection failed and no options are available.
                    // Try to use cached data if it is available. 
                    // You may want to tell the user that the connect failed.
                    break;
            }
            else 
            {
                // Received an Hresult that is not mapped to an enum.
                // This could be a connectivity issue.
                retrySocketConnect = true;
            }
        }
    });
    

```

## <a name="related-topics"></a>См. также


**Другие ресурсы**

* [Подключение с помощью сокета датаграмм](https://docs.microsoft.com/previous-versions/windows/apps/jj635238(v=win.10))
* [Подключение к сетевому ресурсу с помощью сокета потока](https://docs.microsoft.com/previous-versions/windows/apps/jj150599(v=win.10))
* [Подключение к сетевым службам](https://docs.microsoft.com/previous-versions/windows/apps/hh452976(v=win.10))
* [Подключение к веб-службам](https://docs.microsoft.com/previous-versions/windows/apps/hh761504(v=win.10))
* [Основы работы с сетями](https://docs.microsoft.com/windows/uwp/networking/networking-basics)
* [Настройка характеристик сетевой изоляции](https://docs.microsoft.com/previous-versions/windows/apps/hh770532(v=win.10))
* [Использование интерфейса замыкания на себя и отладка сетевой изоляции](https://docs.microsoft.com/previous-versions/windows/apps/hh780593(v=win.10))

**Справочник**

* [**датаграмсоккет**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.DatagramSocket)
* [**HttpClient**](https://docs.microsoft.com/uwp/api/Windows.Web.Http.HttpClient)
* [**StreamSocket**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets.StreamSocket)
* [**Windows:: Web:: http**](https://docs.microsoft.com/uwp/api/Windows.Web.Http)
* [**Windows:: сети:: сокеты**](https://docs.microsoft.com/uwp/api/Windows.Networking.Sockets)

**Примеры**

* [Образец DatagramSocket](https://github.com/microsoft/VCSamples/tree/master/VC2012Samples/Windows%208%20samples/C%2B%2B/Windows%208%20app%20samples/ControlChannelTrigger%20StreamSocket%20sample%20(Windows%208))
* [Пример HttpClient](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/master/Official%20Windows%20Platform%20Sample/HttpClient%20sample)
* [Образец Proximity](https://github.com/microsoft/VCSamples/tree/master/VC2012Samples/Windows%208%20samples/C%2B%2B/Windows%208%20app%20samples/Proximity%20sample%20(Windows%208))
* [StreamSocket sample](https://code.msdn.microsoft.com/windowsapps/StreamSocket-Sample-8c573931)
