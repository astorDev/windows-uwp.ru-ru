---
ms.assetid: 82ab5fc9-3a7f-4d9e-9882-077ccfdd0ec9
title: Программирование пользовательского подключаемого модуля для портала устройств
description: Сведения о том, как создать приложение UWP, которое использует портал устройств Windows для размещения веб-страницы и предоставления диагностических сведений.
ms.date: 03/24/2017
ms.topic: article
keywords: Windows 10, UWP, портал устройств
ms.localizationpriority: medium
ms.openlocfilehash: 4881fe961979243849728d3f835c449e0f71f4b4
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75683847"
---
# <a name="write-a-custom-plugin-for-device-portal"></a>Программирование пользовательского подключаемого модуля для портала устройств

Сведения о том, как создать приложение UWP, которое использует портал устройств Windows для размещения веб-страницы и предоставления диагностических сведений.

Начиная с обновления Creators Update вы можете использовать портал устройств для размещения интерфейсов диагностики приложения. В этой статье рассматривается три обязательных элемента для создания DevicePortalProvider для приложения — изменения в appxmanifest, подключение приложения к службе портала устройств и обработка входящего запроса. Для начала работы мы предоставим пример приложения (ожидается в ближайшее время). 

## <a name="create-a-new-uwp-app-project"></a>Создание нового проекта приложения UWP
В этом руководстве для простоты мы включим все элементы в одно решение.

В Microsoft Visual Studio 2019 создайте новый проект приложения UWP. Щелкните элементы "Файл" > "Новый" > "Проект", выберите шаблон "Пустое приложение (Windows Universal) для C#" и нажмите кнопку "Далее". Откроется диалоговое окно настройки нового проекта. Присвойте проекту имя DevicePortalProvider и щелкните "Создать". Это приложение будет содержать службу приложений. Убедитесь, что выбрана поддержка версии Windows 10 Creators Update (10.0, сборка 15063).  Может потребоваться обновить Visual Studio или установить новый пакет SDK. Дополнительные сведения см. [здесь](https://blogs.windows.com/buildingapps/2017/04/05/updating-tooling-windows-10-creators-update/). 

## <a name="add-the-deviceportalprovider-extension-to-your-packageappxmanifest-file"></a>Добавление расширения devicePortalProvider в файл package.appxmanifest
Необходимо добавить код в файл *package.appxmanifest*, чтобы приложение могло работать в качестве подключаемого модуля для портала устройств. Сначала добавьте следующие определения пространств имен в верхней части файла. Кроме того, добавьте их в атрибут `IgnorableNamespaces`.

```xml
<Package
    ... 
    xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
    xmlns:uap4="http://schemas.microsoft.com/appx/manifest/uap/windows10/4"
    IgnorableNamespaces="uap mp rescap uap4">
    ...
```

Чтобы объявить, что приложение является поставщиком портала устройств, следует создать службу приложений и новое расширение поставщика портала устройств, использующее его. Добавьте расширение windows.appService и расширение windows.devicePortalProvider в элемент `Extensions` в `Application`. Убедитесь, что атрибуты `AppServiceName` в каждом расширении совпадают. Так мы сообщим службе портала устройств, что эта служба приложения может быть запущена для обработки запросов в пространстве имен обработчика. 

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

Атрибут `HandlerRoute` ссылается на пространство имен REST, заявленное приложением. Любой HTTP-запрос в этом пространстве имен (за которым неявно следует подстановочный знак), полученный службой портала устройств, будет отправляться в приложение для обработки. В нашем примере любой успешно прошедший проверку подлинности HTTP-запрос к `<ip_address>/MyNamespace/api/*` будет отправлен в это приложение. Конфликты между маршрутами обработчиков разрешаются при помощи проверки "наибольшего количества побед": выбирается тот маршрут, который совпадает с большим количеством запросов, это означает, что запрос к "/MyNamespace/api/foo" будет передан поставщику с маршрутом "/MyNamespace/api", а не "/MyNamespace".  

Для этой функции требуются две новые возможности. Их также следует добавить в файл *package.appxmanifest*.

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
> Возможность devicePortalProvider ограничена ("rescap"), то есть вам нужно получить предварительное разрешение в Store, чтобы опубликовать в нем приложение. Однако это не помешает локально тестировать приложение, загрузив его как неопубликованное. Дополнительные сведения об ограниченных возможностях см. в статье [Объявления возможностей приложения](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).

## <a name="set-up-your-background-task-and-winrt-component"></a>Настройка фоновой задачи и компонента WinRT
Чтобы настроить подключение портала устройства, приложение необходимо подключить к службе приложения через службу портала устройства с экземпляром портала устройства, запущенного в приложении. Для этого добавьте новый компонент WinRT в приложение с помощью класса, который реализует [**IBackgroundTask**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask).

```csharp
namespace MySampleProvider {
    // Implementing a DevicePortalConnection in a background task
    public sealed class SampleProvider : IBackgroundTask {
        //...
    }
```

Убедитесь, что его имя совпадает с пространством имен и именем класса, заданными с помощью AppService EntryPoint ("MySampleProvider.SampleProvider"). Когда вы выполните первый запрос к поставщику портала устройств, портал устройств спрячет этот запрос, затем запустит фоновое задание приложения, вызовет его метод **Run** и передаст ему параметр [**IBackgroundTaskInstance**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtaskinstance). Ваше приложение использует его для настройки экземпляра [**DevicePortalConnection**](https://docs.microsoft.com/uwp/api/windows.system.diagnostics.deviceportal.deviceportalconnection).

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

Приложение должно уметь обрабатывать два события, чтобы поддерживать весь цикл обработки запроса. **Closed** вызывается, когда служба портала устройств завершает работу, а [**RequestRecieved**](https://docs.microsoft.com/uwp/api/windows.system.diagnostics.deviceportal.deviceportalconnectionrequestreceivedeventargs) проверяет входящие HTTP-запросы и выполняет основные функции поставщика портала устройств. 

## <a name="handle-the-requestreceived-event"></a>Обработка события RequestReceived
Событие **RequestReceived** происходит один раз для каждого HTTP-запроса, выполняемого в маршруте обработчика для подключаемого модуля. Цикл обработки запросов для поставщиков портала устройств выполняется так же, как в NodeJS Express: объекты запроса и ответа предоставляются вместе с событием, а обработчик отвечает путем заполнения объекта запроса. Для поставщиков портала устройств событие **RequestReceived** и его обработчики используют объекты [**Windows.Web.Http.HttpRequestMessage**](https://docs.microsoft.com/uwp/api/windows.web.http.httprequestmessage) и [**HttpResponseMessage**](https://docs.microsoft.com/uwp/api/windows.web.http.httpresponsemessage).   

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

В этом примере обработчика запросов мы извлекаем объекты запроса и ответа из параметра *args*, а затем создаем строку с URL-адресом запроса и дополнительным форматированием HTML. Она добавляется в объект ответа в качестве экземпляра [**HttpStringContent**](https://docs.microsoft.com/uwp/api/windows.web.http.httpstringcontent). Другие классы [**IHttpContent**](https://docs.microsoft.com/uwp/api/windows.web.http.ihttpcontent), например для String и Buffer, также допустимы.

Ответ получает тип ответа HTTP и код состояния 200 (OK). Он должен правильно отображаться в браузере, из которого был сделан исходный вызов. Обратите внимание, что при возврате обработчика события **RequestReceived** агенту пользователя автоматически возвращается ответное сообщение, то есть дополнительный метод отправки не требуется.

![ответное сообщение портала устройств](images/device-portal/plugin-response-message.png)

## <a name="providing-static-content"></a>Предоставление статического содержимого
Статическое содержимое может передаваться непосредственно из папки в пакете, что упрощает добавление пользовательского интерфейса для поставщика.  Самый простой способ отображения статического содержимого — это создание папки содержимого в проекте, который можно сопоставить с URL-адресом.

![папка статического содержимого портала устройств](images/device-portal/plugin-static-content.png)
 
Затем добавьте обработчик маршрутов в обработчик события **RequestReceived**, который определяет маршруты статического содержимого и сопоставляет запрос соответствующим образом.  

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
Убедитесь, что все файлы в папке содержимого помечены как "Содержимое" и для них установлены параметры "Копировать более позднюю версию" или "Всегда копировать" в меню свойств Visual Studio.  Это гарантирует, что при развертывании файлы будут помещены в пакет AppX.  

![настройка копирования файлов статического содержимого](images/device-portal/plugin-file-copying.png)

## <a name="using-existing-device-portal-resources-and-apis"></a>Использование ресурсов и API существующего портала устройств
Статическое содержимое, которое обслуживается поставщиком портала устройств, доступно через тот же порт, что и основная служба портала устройств.  Это означает, что вы можете ссылаться на существующие JS- и CSS-файлы, относящиеся к порталу устройств, используя простые теги `<link>` и `<script>` в HTML-коде. В целом, мы рекомендуем использовать файл *rest.js*, который объединяет все основные REST API портала устройств в удобном объекте webbRest, и файл *common.css*, позволяющий создать стиль содержимого в соответствии с интерфейсом пользователя портала устройств. Вариант такой реализации можно изучить на странице *index.html*, включенной в наш пример. Здесь *rest.js* используется для получения имени устройства и запуска процессов на портале устройств. 

![выходные данные подключаемого модуля устройства](images/device-portal/plugin-output.png)
 
Важно, что при использовании методов HttpPost/DeleteExpect200 в webbRest [обработка CSRF](https://docs.microsoft.com/aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) выполняется автоматически, что позволяет веб-странице вызывать REST API, которые изменяют состояние.  

> [!NOTE] 
> Статическое содержимое, относящиеся к порталу устройств, не имеет гарантий защиты от критических изменений. Интерфейсы API могут изменяться, хотя и не очень часто. Это в особенности относится к файлам *common.js* и *controls.js*, которые поставщик не должен использовать. 

## <a name="debugging-the-device-portal-connection"></a>Отладка подключения к порталу устройств
Для отладки фоновой задачи вам нужно изменить способ запуска кода в Visual Studio. Выполните действия, описанные ниже, для отладки подключения службы приложения, чтобы проверить, как поставщик обрабатывает HTTP-запросы.

1.  В меню "Отладка" выберите "Свойства DevicePortalProvider". 
2.  На вкладке "Отладка" в разделе "Действие при запуске" выберите параметр "Не запускать, а отлаживать мой код при открытии".  
![помещение подключаемого модуля в режим отладки](images/device-portal/plugin-debug-mode.png)
3.  Установите точку останова в функции обработчика RequestReceived.
![Точка останова в обработчике requestreceived](images/device-portal/plugin-requestreceived-breakpoint.png)
> [!NOTE] 
> Убедитесь, что архитектура сборки точно соответствует архитектуре целевого объекта. Если вы используете 64-разрядный компьютер, развертывание необходимо выполнять с помощью сборки AMD64. 
4.  Нажмите F5, чтобы развернуть приложение.
5.  Отключите портал устройств, а затем снова включите его, чтобы он нашел ваше приложение (это необходимо только после изменения манифеста приложения, а в остальных случаях можно просто развертывать приложение, пропустив этот шаг). 
6.  В браузере перейдите к пространству имен поставщика, это должно привести к срабатыванию точки останова.

## <a name="related-topics"></a>Связанные темы
* [Обзор портала устройств Windows](device-portal.md)
* [Создание и использование службы приложений](https://docs.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service)


