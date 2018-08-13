---
author: PatrickFarley
ms.assetid: 82ab5fc9-3a7f-4d9e-9882-077ccfdd0ec9
title: Написание пользовательского подключаемого модуля для портала устройств
description: Узнайте, как создать приложение UWP, которое используется портал устройств Windows для размещения веб-страницы и предоставления диагностических сведений.
ms.author: pafarley
ms.date: 03/24/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 2aae0e634ea219ef602eafdb1516d1d42b9c7913
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "1026000"
---
# <a name="write-a-custom-plugin-for-device-portal"></a>Написание пользовательского подключаемого модуля для портала устройств

Узнайте, как создать приложение UWP, которое используется портал устройств Windows для размещения веб-страницы и предоставления диагностических сведений.

Начиная с обновления Creators Update, можно использовать портал устройств для размещения интерфейсов диагностики вашего приложения. В этой статье рассматривается три условия, необходимые для создания DevicePortalProvider для приложения — изменения appxmanifest, настройка подключения приложения к службе портала устройств и обработка входящего запроса. Для начала работы также предоставляется пример приложения (ожидается в ближайшее время). 

## <a name="create-a-new-uwp-app-project"></a>Создание нового проекта приложения UWP
В этом пособии для простоты мы создадим универсальное решение.

В Microsoft Visual Studio 2017 создайте новый проект приложения UWP. Выберите команду "Файл" > "Новый проект" и выберите "Шаблоны" > "Visual C#" > "Windows Universal" > "Пустое приложение (Windows Universal)". Назовите его "DevicePortalProvider". Это приложение будет содержать службу приложений. Убедитесь, что выбран пакет Creators Update SDK для поддержки.  Может потребоваться обновить Visual Studio или установить новый SDK — дополнительные сведения см. [здесь](https://blogs.windows.com/buildingapps/2017/04/05/updating-tooling-windows-10-creators-update/). 

## <a name="add-the-deviceportalprovider-extension-to-your-packageappxmanifest-file"></a>Добавление расширения devicePortalProvider в файл package.appxmanifest
Необходимо добавить код в файл *package.appxmanifest*, чтобы приложение стало более функциональным в качестве подключаемого модуля портала устройств. Сначала добавьте следующие определения пространства имен в верхней части файла. Кроме того, добавьте их к атрибуту `IgnorableNamespaces`.

```xml
<Package
    ... 
    xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
    xmlns:uap4="http://schemas.microsoft.com/appx/manifest/uap/windows10/4"
    IgnorableNamespaces="uap mp rescap uap4">
    ...
```

Чтобы объявить, что приложение является поставщиком портала устройств, следует создать службу приложений и новое расширение поставщика портала устройств, использующее его. Добавьте расширение windows.appService и расширение windows.devicePortalProvider к элементу `Extensions` в `Application`. Убедитесь, что атрибуты `AppServiceName` соответствуют в каждом расширении. Это указывает службе портала устройств, что эта служба приложения может быть запущена для обработки запросов в пространстве имен обработчика. 

```xml
...   
<Application 
    Id="App" 
    Executable="$targetnametoken$.exe"
    EntryPoint="DevicePortalProvider.App">
    ...
    <Extensions>
        <uap:Extension Category="windows.appService" EntryPoint="MySampleProvider.SampleProvider">
            <uap:AppService Name="com.sampleProvider.wdp" />
        </uap:Extension>
        <uap4:Extension Category="windows.devicePortalProvider">
            <uap4:DevicePortalProvider 
                DisplayName="My Device Portal Provider Sample App" 
                AppServiceName="com.sampleProvider.wdp" 
                HandlerRoute="/MyNamespace/api/" />
        </uap4:Extension>
    </Extensions>
</Application>
...
```

Атрибут `HandlerRoute` ссылается на пространство имен REST, заявленное приложением. Любой HTTP-запрос в этом пространстве имен (за которым неявно следует подстановочный знак), полученный службой портала устройств, будет отправляться в приложение для обработки. В этом случае любой успешно прошедший проверку подлинности HTTP-запрос к `<ip_address>/MyNamespace/api/*` будет отправлен в приложение. Конфликты между маршрутами обработчиков разрешаются при помощи проверки "наибольшего количества побед": выбирается тот маршрут, который совпадает с большим количеством запросов, это означает, что запрос к "/MyNamespace/api/foo" совпадет с поставщиком с "/MyNamespace/api", а не с "/MyNamespace".  

Для этой функции требуется две новые возможности. Их также следует добавить в файл *package.appxmanifest*.

```xml
...
<Capabilities>
    ...
    <Capability Name="privateNetworkClientServer" />
    <rescap:Capability Name="devicePortalProvider" />
</Capabilities>
...
```

> [!NOTE]
> Возможность "devicePortalProvider" ограничена ("rescap"), это означает, что необходимо получить предварительное разрешение из Магазина, чтобы приложение можно было там опубликовать. Однако это не помешает тестированию приложения локально путем загрузки неопубликованных приложений. Дополнительные сведения об ограниченных возможностях см. в разделе [Объявления возможностей приложения](https://msdn.microsoft.com/en-us/windows/uwp/packaging/app-capability-declarations#special-and-restricted-capabilities).

## <a name="set-up-your-background-task-and-winrt-component"></a>Настройка фоновых задач и компонентов WinRT
Для настройки подключения портала устройства приложение необходимо подключить к службе приложения через службу портала устройства с экземпляром портала устройства, запущенного в приложении. Для этого добавьте новый компонент WinRT в приложение с помощью класса, который реализует [**IBackgroundTask**](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.background.ibackgroundtask).

```csharp
namespace MySampleProvider {
    // Implementing a DevicePortalConnection in a background task
    public sealed class SampleProvider : IBackgroundTask {
        //...
    }
```

Убедитесь, что его имя совпадает с пространством имен и именем класса, заданными с помощью AppService EntryPoint ("MySampleProvider.SampleProvider"). Когда создается первый запрос к поставщику портала устройств, запрос будет скрыт в портале устройств, затем запустите фоновое задание приложения, вызовите его метод **Run** и передайте их в [**IBackgroundTaskInstance**](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.background.ibackgroundtaskinstance). Ваше приложение использует его для настройки экземпляра [**DevicePortalConnection**](https://docs.microsoft.com/en-us/uwp/api/windows.system.diagnostics.deviceportal.deviceportalconnection).

```csharp
// Implement background task handler with a DevicePortalConnection
public void Run(IBackgroundTaskInstance taskInstance) {
    // Take a deferral to allow the background task to continue executing 
    this.taskDeferral = taskInstance.GetDeferral();
    taskInstance.Canceled += TaskInstance_Canceled;

    // Create a DevicePortal client from an AppServiceConnection 
    var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
    var appServiceConnection = details.AppServiceConnection;
    this.devicePortalConnection = DevicePortalConnection.GetForAppServiceConnection(appServiceConnection);

    // Add Closed, RequestReceived handlers 
    devicePortalConnection.Closed += DevicePortalConnection_Closed;
    devicePortalConnection.RequestReceived += DevicePortalConnection_RequestReceived;
}
```

Существует два события, которые должны обрабатываться приложением для завершения цикла обработки запросов: **Closed**, когда служба портала устройств завершает работу, и [**RequestRecieved**](https://docs.microsoft.com/en-us/uwp/api/windows.system.diagnostics.deviceportal.deviceportalconnectionrequestreceivedeventargs), которое проверяет входящие HTTP-запросы и предоставляет основные функциональные возможности поставщика портала устройств. 

## <a name="handle-the-requestreceived-event"></a>Обработка события RequestReceived
Событие **RequestReceived** происходит один раз для каждого HTTP-запроса, который выполняется на маршруте обработчика, указанного для подключаемого модуля. Цикл обработки запросов для поставщиков портала устройств похож на аналогичный объект в NodeJS Express: объекты запроса и ответа предоставляются вместе с событием, а обработчик отвечает путем заполнения объекта запроса. Для поставщиков портала устройства событие **RequestReceived** и его обработчики используют объекты [**Windows.Web.Http.HttpRequestMessage**](https://docs.microsoft.com/en-us/uwp/api/windows.web.http.httprequestmessage) и [**HttpResponseMessage**](https://docs.microsoft.com/en-us/uwp/api/windows.web.http.httpresponsemessage).   

```csharp
// Sample RequestReceived echo handler: respond with an HTML page including the query and some additional process information. 
private void DevicePortalConnection_RequestReceived(DevicePortalConnection sender, DevicePortalConnectionRequestReceivedEventArgs args)
{
    var req = args.RequestMessage;
    var res = args.ResponseMessage;

    if (req.RequestUri.AbsolutePath.EndsWith("/echo"))
    {
        // construct an html response message
        string con = "<h1>" + req.RequestUri.AbsoluteUri + "</h1><br/>";
        var proc = Windows.System.Diagnostics.ProcessDiagnosticInfo.GetForCurrentProcess();
        con += String.Format("This process is consuming {0} bytes (Working Set)<br/>", proc.MemoryUsage.GetReport().WorkingSetSizeInBytes);
        con += String.Format("The process PID is {0}<br/>", proc.ProcessId);
        con += String.Format("The executable filename is {0}", proc.ExecutableFileName);
        res.Content = new HttpStringContent(con);
        res.Content.Headers.ContentType = new HttpMediaTypeHeaderValue("text/html");
        res.StatusCode = HttpStatusCode.Ok;            
    }
    //...
}
```

В этом примере обработчика запросов сначала мы выполняем запрос и получаем ответы объектов из параметра *args*, затем создаем строку с URL-адресом запроса и дополнительным форматированием HTML. Это добавляется в объект ответа в качестве экземпляра [**HttpStringContent**](https://docs.microsoft.com/en-us/uwp/api/windows.web.http.httpstringcontent). Другие классы [**IHttpContent**](https://docs.microsoft.com/en-us/uwp/api/windows.web.http.ihttpcontent), например для "String" и "Buffer", также разрешены.

Ответ затем настраивается в качестве ответа HTTP и приводится код состояния 200 (OK). Он должен преобразовываться должным образом в браузере, где был сделан исходный вызов. Обратите внимание, что когда возвращается обработчик события **RequestReceived**, агенту пользователя автоматически возвращается сообщение ответа: дополнительный метод "send" не требуется.

![ответное сообщение портала устройств](images/device-portal/plugin-response-message.png)

## <a name="providing-static-content"></a>Предоставление статического содержимого
Статическое содержимое может отображаться непосредственно из папки в пакете, что упрощает добавление пользовательского интерфейса для поставщика.  Самый простой способ отображения статического содержимого — это создание папки содержимого в проекте, который можно сопоставить с URL-адресом.

![папка статического содержимого портала устройств](images/device-portal/plugin-static-content.png)
 
Затем добавьте обработчик маршрута в обработчик события **RequestReceived**, который определяет маршруты статического содержимого и сопоставляет запрос соответствующим образом.  

```csharp
if (req.RequestUri.LocalPath.ToLower().Contains("/www/")) {
    var filePath = req.RequestUri.AbsolutePath.Replace('/', '\\').ToLower();
    filePath = filePath.Replace("\\backgroundprovider", "")
    try {
        var fileStream = Windows.ApplicationModel.Package.Current.InstalledLocation.OpenStreamForReadAsync(filePath).GetAwaiter().GetResult();
        res.StatusCode = HttpStatusCode.Ok;
        res.Content = new HttpStreamContent(fileStream.AsInputStream());
        res.Content.Headers.ContentType = new HttpMediaTypeHeaderValue("text/html");
    } catch(FileNotFoundException e) {
        string con = String.Format("<h1>{0} - not found</h1>\r\n", filePath);
        con += "Exception: " + e.ToString();
        res.Content = new HttpStringContent(con);
        res.StatusCode = HttpStatusCode.NotFound;
        res.Content.Headers.ContentType = new HttpMediaTypeHeaderValue("text/html");
    }
}
```
Убедитесь, что все файлы в папке содержимого помечены как "Содержимое" и для них установлены параметры "Копировать более позднюю версию" или "Всегда копировать" в меню свойств Visual Studio.  Это гарантирует, что файлы будут внутри пакета AppX при развертывании.  

![Настройка копирования файлов статического содержимого](images/device-portal/plugin-file-copying.png)

## <a name="using-existing-device-portal-resources-and-apis"></a>Использование ресурсов и API существующего портала устройств
Статическое содержимое, которое обслуживается поставщиком портала устройств, обслуживается тем же портом, что и основная служба портала устройств.  Это означает, что можно ссылаться на существующие JS и CSS, включенные в портал устройств, с помощью простых тегов `<link>` и `<script>` в HTML-коде. В целом, мы предлагаем использование *rest.js*, который создает все основные API REST портала устройств в удобном объекте webbRest и файл *common.css*, позволяющий создать стиль содержимого в соответствии с остальным интерфейсом пользователя портала устройств. Можно просмотреть пример этого на странице *index.html*, включенной в пример. *rest.js* используется для получения имени устройства и запущенных процессов от портала устройств. 

![Подключаемый модуль портала вывода устройства](images/device-portal/plugin-output.png)
 
Важно, что использование методов HttpPost/DeleteExpect200 в webbRest автоматически выполнит [обработку CSRF](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks), что позволит веб-странице вызывать API REST, которые изменяют состояние.  

> [!NOTE] 
> Статическое содержимое, включенное в портал устройства, не поставляется с гарантией защиты от критических изменений. Хотя интерфейсы API не должны изменятся часто, они могут изменяться, особенно в файлах *common.js* и *controls.js*, которые поставщик не должен использовать. 

## <a name="debugging-the-device-portal-connection"></a>Отладка подключения портала устройств
Для отладки фоновой задачи необходимо изменить способ запуска кода в Visual Studio. Выполните действия, описанные ниже, для отладки службы подключения приложения, чтобы проверить, как поставщик обрабатывает HTTP-запросы.

1.  В меню "Отладка" выберите "Свойства DevicePortalProvider". 
2.  На вкладке "Отладка" в разделе "Действие при запуске" выберите параметр "Не запускать, а отлаживать мой код при открытии".  
![Помещение подключаемого модуля в режиме отладки](images/device-portal/plugin-debug-mode.png)
3.  Установите точку останова в функции обработчика RequestReceived.
![Точка прерывания в обработчике requestreceived](images/device-portal/plugin-requestreceived-breakpoint.png)
> [!NOTE] 
> Убедитесь, что архитектура сборки точно соответствует архитектуре целевого объекта. При использовании 64-разрядного ПК развертывание необходимо выполнять с помощью сборки AMD64. 
4.  Нажмите F5 для развертывания приложения
5.  Отключите портал устройств, а затем снова включите его, чтобы он нашел ваше приложение (это необходимо только при изменении манифеста приложения — в остальное время можно просто повторно развертывать и пропускать этот шаг). 
6.  В браузере перейдите к пространству имен поставщика, также следует ввести точку останова.

## <a name="related-topics"></a>Еще по теме:
* [Обзор портала устройств Windows](device-portal.md)
* [Создание и использование службы приложения](https://docs.microsoft.com/en-us/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service)


