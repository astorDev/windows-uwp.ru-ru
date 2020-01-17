---
Description: Элемент управления представлением веб-страницы позволяет внедрить в приложение представление, в котором отображается веб-содержимое с помощью механизма визуализации Microsoft Edge. В этом элементе можно реализовать отображение и работу гиперссылок.
title: Представление веб-страницы
ms.assetid: D3CFD438-F9D6-4B72-AF1D-16EF2DFC1BB1
label: Web view
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: de7a430248841722aedd960cd485ea24499fdd00
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684276"
---
# <a name="web-view"></a>Представление веб-страницы

Элемент управления представлением веб-страницы позволяет внедрить в приложение представление, в котором отображается веб-содержимое с помощью механизма визуализации Microsoft Edge. В этом элементе можно реализовать отображение и работу гиперссылок.

> **Важные API**: [Класс WebView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebView)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте элемент управления представлением веб-страницы для отображения форматированного содержимого HTML-страниц с удаленного веб-сервера, динамически генерируемого кода или файлов содержимого в пакете приложения. Форматированное содержимое может также содержать код скрипта и обеспечивать связь между этим сценарием и кодом вашего приложения.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">XAML Controls Gallery</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/WebView">открыть это приложение и увидеть WebView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-a-web-view"></a>Создание представления веб-страницы

**Изменение внешнего вида представления веб-страницы**

[WebView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebView) не относится к подклассу [Control](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control), поэтому у него нет шаблона элемента управления. Но вы можете указывать различные свойства для управления некоторыми визуальными аспектами представления веб-страницы.
- Чтобы ограничить область отображения, задайте свойства [Width](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.width) и [Height](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.height). 
- Для преобразования, масштабирования, наклона и поворота представления веб-страницы используйте свойство [RenderTransform](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform).
- Для управления прозрачностью представления веб-страницы задайте свойство [Opacity](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.opacity).
- Для определения цвета, который необходимо использовать в качестве фона веб-страницы, если содержимое HTML не определяет цвет, задайте свойство [DefaultBackgroundColor](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.defaultbackgroundcolor). 

**Получение заголовка веб-страницы**

Получить заголовок документа HTML, отображаемого в представлении веб-страницы в настоящий момент, можно с помощью свойства [DocumentTitle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.documenttitle). 

**События ввода и порядок вкладок**

Хотя элемент WebView не относится к подклассу Control, он получает фокус ввода с клавиатуры и участвует в порядке вкладок. Он предоставляет метод [Focus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.focus), а также события [GotFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.gotfocus) и [LostFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.lostfocus), но у него отсутствуют свойства, связанные с вкладками. Его положение в последовательности вкладок такое же, как его положение в порядке документов XAML. Последовательность вкладок включает все элементы в содержимом представления веб-страницы, которые могут получать фокус ввода. 

Как показано в таблице "События" на странице класса [WebView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebView), представление веб-страницы не поддерживает большинство событий пользовательского ввода, унаследованных от [UIElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement), таких как [KeyDown](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.keydown), [KeyUp](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.keyup) и [PointerPressed](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed). Вместо этого вы можете использовать [InvokeScriptAsync](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.invokescriptasync) с функцией JavaScript **eval**, чтобы использовать обработчики событий HTML и **window.external.notify** из обработчика событий HTML для уведомления приложения с помощью [WebView.ScriptNotify](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.scriptnotify).

### <a name="navigating-to-content"></a>Переход к содержимому

Веб-представление предоставляет несколько API для базовой навигации: [GoBack](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.goback), [GoForward](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.goforward), [Stop](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.stop), [Refresh](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.refresh), [CanGoBack](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.cangoback) и [CanGoForward](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.cangoforward). Вы можете использовать их, чтобы добавить в приложение стандартные возможности просмотра веб-страниц. 

Задайте в коде XAML свойство [Source](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.source), чтобы настроить начальное содержимое представления веб-страницы. Анализатор XAML автоматически преобразует строку в [Uri](https://docs.microsoft.com/uwp/api/Windows.Foundation.Uri). 

```xaml
<!-- Source file is on the web. -->
<WebView x:Name="webView1" Source="http://www.contoso.com"/>

<!-- Source file is in local storage. -->
<WebView x:Name="webView2" Source="ms-appdata:///local/intro/welcome.html"/>

<!-- Source file is in the app package. -->
<WebView x:Name="webView3" Source="ms-appx-web:///help/about.html"/>
```

Свойство Source можно задать в коде, но вместо него для загрузки содержимого в код предпочтительнее использовать один из методов **Navigate**. 

Для загрузки веб-содержимого используйте метод [Navigate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.navigate) с **Uri**, который использует схему http или https. 

```csharp
webView1.Navigate("http://www.contoso.com");
```

Для перехода к URI с запросом POST и заголовками HTTP, используйте метод [NavigateWithHttpRequestMessage](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.navigatewithhttprequestmessage). Этот метод поддерживает только [HttpMethod.Post](https://docs.microsoft.com/uwp/api/windows.web.http.httpmethod.post) и [HttpMethod.Get](https://docs.microsoft.com/uwp/api/windows.web.http.httpmethod.get) для значения свойства [HttpRequestMessage.Method](https://docs.microsoft.com/uwp/api/windows.web.http.httprequestmessage.method). 

Чтобы загрузить несжатое и незашифрованное содержимое хранилищ данных [LocalFolder](/uwp/api/windows.storage.applicationdata.localfolder) или [TemporaryFolder](/uwp/api/windows.storage.applicationdata.temporaryfolder) вашего приложения, используйте метод **Navigate** с **Uri**, использующим [схему ms-appdata](/windows/uwp/app-resources/uri-schemes). Для поддержки представлений веб-страницы в этой схеме необходимо поместить содержимое во вложенную папку локальной или временной папки. Это позволяет переходить к таким URI, как ms-appdata:///local/*folder*/*file*.html и ms-appdata:///temp/*folder*/*file*.html. (Подробнее о загрузке сжатых или зашифрованных файлов см. в разделе [NavigateToLocalStreamUri](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.navigatetolocalstreamuri).) 

Каждая из этих вложенных папок первого уровня изолирована от содержимого других вложенных папок первого уровня. Например, вы можете перейти к ms-appdata:///temp/folder1/file.html, но в этом файле не может быть ссылки на ms-appdata:///temp/folder2/file.html. Но в пакете приложения можно создать ссылку на HTML-содержимое с помощью **схемы ms-appx-web** и на веб-содержимое с помощью схем URI **http** и **https**.

```csharp
webView1.Navigate("ms-appdata:///local/intro/welcome.html");
```

Для загрузки содержимого из пакета приложения используйте метод **Navigate** с **Uri**, который использует [схему ms-appx-web](https://docs.microsoft.com/previous-versions/windows/apps/jj655406(v=win.10)). 

```csharp
webView1.Navigate("ms-appx-web:///help/about.html");
```

Локальное содержимое можно загрузить с помощью пользовательского сопоставителя, используя метод [NavigateToLocalStreamUri](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.navigatetolocalstreamuri). Это позволяет выполнять сложные сценарии, например скачивание и кэширование веб-содержимого для использования в автономном режиме или извлечение содержимого из сжатого файла.

### <a name="responding-to-navigation-events"></a>Реакция на события навигации

Элемент управления представлением веб-страницы имеет несколько событий, позволяющих реализовать реакцию на состояния навигации и загрузки содержимого. Для содержимого корневого веб-представления события происходят в следующем порядке: [NavigationStarting](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.navigationstarting), [ContentLoading](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.contentloading), [DOMContentLoaded](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.domcontentloaded), [NavigationCompleted](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.navigationcompleted)


**NavigationStarting** происходит перед тем, как в веб-представлении выполняется переход к новому содержимому. Можно отменить навигацию в обработчике для этого события, задав для свойства WebViewNavigationStartingEventArgs.Cancel значение true. 

```csharp
webView1.NavigationStarting += webView1_NavigationStarting;

private void webView1_NavigationStarting(object sender, WebViewNavigationStartingEventArgs args)
{
    // Cancel navigation if URL is not allowed. (Implemetation of IsAllowedUri not shown.)
    if (!IsAllowedUri(args.Uri))
        args.Cancel = true;
}
```

**ContentLoading** происходит, когда в представлении веб-страницы начинается загрузка нового содержимого. 

```csharp
webView1.ContentLoading += webView1_ContentLoading;

private void webView1_ContentLoading(WebView sender, WebViewContentLoadingEventArgs args)
{
    // Show status.
    if (args.Uri != null)
    {
        statusTextBlock.Text = "Loading content for " + args.Uri.ToString();
    }
}
```

**DOMContentLoaded** происходит, когда в представлении веб-страницы заканчивается анализ текущего HTML-содержимого. 

```csharp
webView1.DOMContentLoaded += webView1_DOMContentLoaded;

private void webView1_DOMContentLoaded(WebView sender, WebViewDOMContentLoadedEventArgs args)
{
    // Show status.
    if (args.Uri != null)
    {
        statusTextBlock.Text = "Content for " + args.Uri.ToString() + " has finished loading";
    }
}
```

**NavigationCompleted** происходит, когда в представлении веб-страницы заканчивается загрузка текущего содержимое или происходит сбой навигации. Чтобы определить результат навигации, проверьте свойства [IsSuccess](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webviewnavigationcompletedeventargs.issuccess) и [WebErrorStatus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webviewnavigationcompletedeventargs.weberrorstatus) класса [WebViewNavigationCompletedEventArgs](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewNavigationCompletedEventArgs). 

```csharp
webView1.NavigationCompleted += webView1_NavigationCompleted;

private void webView1_NavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
{
    if (args.IsSuccess == true)
    {
        statusTextBlock.Text = "Navigation to " + args.Uri.ToString() + " completed successfully.";
    }
    else
    {
        statusTextBlock.Text = "Navigation to: " + args.Uri.ToString() + 
                               " failed with error " + args.WebErrorStatus.ToString();
    }
}
```

Подобные события происходят в таком же порядке для каждого элемента **iframe** в содержимом представления веб-страницы. 
- [FrameNavigationStarting](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.framenavigationstarting) — происходит перед тем, как кадр в представлении веб-страницы перейдет к новому содержимому. 
- [FrameContentLoading](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.framecontentloading) — происходит, когда кадр в представлении веб-страницы начнет загрузку нового содержимого. 
- [FrameDOMContentLoaded](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.framedomcontentloaded) — происходит, когда кадр в представлении веб-страницы заканчивает анализ текущего содержимого HTML. 
- [FrameNavigationCompleted](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.framenavigationcompleted) — происходит, когда кадр в представлении веб-страницы заканчивает загружать свое содержимое. 

### <a name="responding-to-potential-problems"></a>Реакция на потенциальные проблемы

Вы можете реагировать на потенциальные проблемы с содержимым, такие как долгое выполнение скриптов, невозможность загрузить содержимое в представление веб-страницы или предупреждения о небезопасном содержимом. 

Ваше приложение может перестать отвечать на действия пользователя, когда выполняются скрипты. Пока представление веб-страницы выполняет сценарий JavaScript, периодически происходит событие [LongRunningScriptDetected](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.longrunningscriptdetected), которое предоставляет возможность прервать сценарий. Чтобы определить, как долго выполняется сценарий, проверьте свойство [ExecutionTime](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webviewlongrunningscriptdetectedeventargs.executiontime) элемента [WebViewLongRunningScriptDetectedEventArgs](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewLongRunningScriptDetectedEventArgs). Чтобы остановить сценарий, задайте свойству аргументов событий [StopPageScriptExecution](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webviewlongrunningscriptdetectedeventargs.stoppagescriptexecution) значение **true**. Остановленный скрипт не будет выполняться снова, пока он не будет перезагружен во время последующего перехода в представлении веб-страницы. 

Элемент управления представлением веб-страницы не позволяет размещать произвольные типы файлов. При попытке загрузить содержимое, которое представление веб-страницы не может размещать, происходит событие [UnviewableContentIdentified](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.unviewablecontentidentified). Вы можете обработать такое событие и уведомить пользователя или использовать класс [Launcher](https://docs.microsoft.com/uwp/api/Windows.System.Launcher), чтобы перенаправить файл во внешний браузер или другое приложение.

Аналогично событие [UnsupportedUriSchemeIdentified](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.unsupportedurischemeidentified) происходит, когда неподдерживаемая схема URI (например, fbconnect:// или mailto://) вызывается в веб-содержимом. Вы можете обработать это событие для выполнения специального действия, а не разрешать системному обработчику по умолчанию запускать этот URI.

Событие [UnsafeContentWarningDisplayingevent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.unsafecontentwarningdisplaying) возникает, когда представление веб-страницы отображает страницу предупреждения для содержимого, которое фильтр SmartScreen определил как небезопасное. Если пользователь решает продолжить навигацию, при последующем переходе на страницу не будет отображаться предупреждение или вызываться событие.

### <a name="handling-special-cases-for-web-view-content"></a>Особые случаи с содержимым представления веб-страницы

Свойство [ContainsFullScreenElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.containsfullscreenelement) и событие [ContainsFullScreenElementChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.containsfullscreenelementchanged) можно использовать для обнаружения, ответа и включения полноэкранного режима в веб-содержимом, например, воспроизведение видео в полноэкранном режиме. Например, можно использовать событие ContainsFullScreenElementChanged, чтобы изменить размер представления веб-страницы таким образом, чтобы оно полностью заняло представление приложения. Это событие также, как показано в следующем примере, можно использовать для перевода приложения из режима окна в полноэкранный режим, если требуется полноэкранное веб-приложение.

```csharp
// Assume webView is defined in XAML
webView.ContainsFullScreenElementChanged += webView_ContainsFullScreenElementChanged;

private void webView_ContainsFullScreenElementChanged(WebView sender, object args)
{
    var applicationView = ApplicationView.GetForCurrentView();

    if (sender.ContainsFullScreenElement)
    {
        applicationView.TryEnterFullScreenMode();
    }
    else if (applicationView.IsFullScreenMode)
    {
        applicationView.ExitFullScreenMode();
    }
}
```

Событие [NewWindowRequested](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.newwindowrequested) можно использовать для обработки случаев, в которых размещаемое веб-содержимое отправляет запрос на отображение нового окна, например, всплывающего окна. Вы можете использовать другой элемент управления WebView для отображения содержимого этого окна.

Используйте событие [PermissionRequested](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.permissionrequested), чтобы включить веб-функции, которые требуют специальных возможностей. Сейчас эти функции включают в себя геолокацию, хранилище IndexedDB, а также аудио- и видеофайлы пользователя (например, с микрофона или веб-камеры). Если ваше приложение получает доступ к данным о местоположении пользователя или его мультимедиа, вы обязаны объявить эту возможность в манифесте приложения. Например, приложению, использующему геолокацию, нужны следующие объявления возможностей как минимум в файле Package.appxmanifest.

```xml
  <Capabilities>
    <Capability Name="internetClient" />
    <DeviceCapability Name="location" />
  </Capabilities>
```

В дополнение к приложению, обрабатывающему событие [PermissionRequested](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.permissionrequested), пользователь должен утвердительно ответить на стандартные системные диалоги для приложений, запрашивающих местоположение или возможности мультимедиа, чтобы включить эти функции.

Приведем пример включения приложением геолокации на карте из коллекции Bing.

```csharp
// Assume webView is defined in XAML
webView.PermissionRequested += webView_PermissionRequested;

private void webView_PermissionRequested(WebView sender, WebViewPermissionRequestedEventArgs args)
{
    if (args.PermissionRequest.PermissionType == WebViewPermissionType.Geolocation &&
        args.PermissionRequest.Uri.Host == "www.bing.com")
    {
        args.PermissionRequest.Allow();
    }
}
```

Если вашему приложению требуется пользовательский ввод или другие асинхронные операции, чтобы ответить на запрос разрешения, используйте метод [Defer](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webviewpermissionrequest.defer) функции [WebViewPermissionRequest](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewPermissionRequest), чтобы создать событие [WebViewDeferredPermissionRequest](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewDeferredPermissionRequest), которое может быть выполнено позднее. См. в разделе [WebViewPermissionRequest.Defer](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webviewpermissionrequest.defer). 

Если пользователям необходимо безопасно выйти из веб-сайта, размещенного в веб-представлении, или в других случаях, когда важна безопасность, вызовите статический метод [ClearTeilitaryWebDataAsync](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.cleartemporarywebdataasync) для очистки всего локально кэшированного контента из сеанса представления веб-страницы. Это предотвратит доступ злоумышленников к конфиденциальным данным. 

### <a name="interacting-with-web-view-content"></a>Взаимодействие с содержимым представления веб-страницы

Взаимодействовать с содержимым веб-представления можно используя метод [InvokeScriptAsync](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.invokescriptasync) для вызова или внедрения сценария в содержимое веб-представления, а также событие [ScriptNotify](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.scriptnotify) для получения сведений от содержимого представления веб-страницы.

Чтобы вызвать JavaScript внутри представления веб-страницы, используйте метод [InvokeScriptAsync](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.invokescriptasync). Вызванный скрипт может возвращать только строковые значения. 

Например, если в содержимом представления веб-страницы с именем `webView1` присутствует функция `setDate`, принимающая три параметра, можете вызвать ее следующим образом. 

```csharp
string[] args = {"January", "1", "2000"};
string returnValue = await webView1.InvokeScriptAsync("setDate", args);
```


Можно использовать **InvokeScriptAsync** с функцией JavaScript **eval**, чтобы внедрить содержимое в веб-страницу.

В следующем примере текст текстового поля XAML (`nameTextBox.Text`) написан в элементе div на странице HTML, размещенной в `webView1`. 

```csharp
private async void Button_Click(object sender, RoutedEventArgs e)
{
    string functionString = String.Format("document.getElementById('nameDiv').innerText = 'Hello, {0}';", nameTextBox.Text);
    await webView1.InvokeScriptAsync("eval", new string[] { functionString });
}
```

Скрипты в содержимом представления веб-страницы могут использовать событие **window.external.notify** с параметром строки для отправки сведений в ваше приложение. Для получения этих сообщений обработайте событие [ScriptNotify](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.scriptnotify). 

Чтобы разрешить внешней веб-странице активировать событие **ScriptNotify** при вызове window.external.notify, необходимо добавить URI этой страницы в раздел **ApplicationContentUriRules** манифеста приложения. (Это можно сделать в Microsoft Visual Studio на вкладке URI содержимого конструктора Package.appxmanifest.) URI в этом списке необходимо использовать протокол HTTPS и могут содержать подстановочные знаки поддомена (например, `https://*.microsoft.com`), но они не могут содержать подстановочные знаки домена (например, `https://*.com` и `https://*.*`). Требования манифеста не применяются к содержимому, которое поступает из пакета приложения, использует URI ms-local-stream:// или загружается с помощью метода [NavigateToString](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.navigatetostring). 

### <a name="accessing-the-windows-runtime-in-a-web-view"></a>Доступ к среде выполнения Windows в представлении веб-страницы

Метод [AddWebAllowedObject](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.addweballowedobject) можно использовать для внедрения экземпляра собственного класса из компонента среды выполнения Windows в контекст JavaScript представления веб-страницы. Это дает полный доступ к собственным методам, свойствам и событиям этого объекта в содержимом JavaScript этого представления веб-страницы. Этот класс должен быть помечен атрибутом [AllowForWeb](https://docs.microsoft.com/uwp/api/Windows.Foundation.Metadata.AllowForWebAttribute). 

Например, этот код внедряет экземпляр `MyClass`, импортированный из компонента среды выполнения Windows, в представление веб-страницы.

```csharp
private void webView_NavigationStarting(WebView sender, WebViewNavigationStartingEventArgs args) 
{ 
    if (args.Uri.Host == "www.contoso.com")  
    { 
        webView.AddWebAllowedObject("nativeObject", new MyClass()); 
    } 
}
```

Дополнительные сведения см. в разделе [WebView.AddWebAllowedObject](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.addweballowedobject). 

Кроме того, доверенное содержимое JavaScript в представлении веб-страницы может получить разрешение на прямой доступ к API среды выполнения Windows. Это предоставляет мощные собственные функции для веб-приложений, размещенных в представлении веб-страницы. Чтобы реализовать эту функцию, URI для доверенного содержимого нужно добавить в список разрешенных URI в ApplicationContentUriRules приложения в Package.appxmanifest, при этом для WindowsRuntimeAccess должно быть специально установлено значение all. 

В этом примере показан раздел манифеста приложения. В нем локальному URI дается доступ к среде выполнения Windows. 

```csharp
  <Applications>
    <Application Id="App"
      ...

      <uap:ApplicationContentUriRules>
        <uap:Rule Match="ms-appx-web:///Web/App.html" WindowsRuntimeAccess="all" Type="include"/>
      </uap:ApplicationContentUriRules>
    </Application>
  </Applications>
```

### <a name="options-for-web-content-hosting"></a>Параметры для размещения веб-содержимого

Свойство [WebView.Settings](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.settings) (типа [WebViewSettings](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewSettings)) можно использовать, чтобы управлять включением JavaScript и IndexedDB. Например, если вы используете представление веб-страницы для отображения исключительно статического содержимого, возможно, вы захотите отключить JavaScript для повышения производительности.

### <a name="capturing-web-view-content"></a>Захват содержимого представления веб-страницы

Чтобы включить общий доступ к содержимому представления веб-страницы для других приложений, используйте метод [CaptureSelectedContentToDataPackageAsync](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.captureselectedcontenttodatapackageasync), который возвращает выбранное содержимое в виде [DataPackage](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.DataPackage). Это асинхронный метод, поэтому следует использовать задержку, чтобы предотвратить возврат обработчика событий [DataRequested](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datatransfermanager.datarequested) до завершения асинхронного вызова. 

Чтобы получить изображение предварительного просмотра текущего содержимого представления веб-страницы, используйте метод [CapturePreviewToStreamAsync](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.capturepreviewtostreamasync). Этот метод создает образ с текущим содержимым и записывает его в указанный поток. 

### <a name="threading-behavior"></a>Поведение потоков

По умолчанию содержимое представления веб-страницы размещается в потоке пользовательского интерфейса на устройствах в семействе компьютеров и вне потока пользовательского интерфейса на всех других устройствах. Статическое свойство [WebView.DefaultExecutionMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.defaultexecutionmode) можно использовать для запроса поведения потока по умолчанию для текущего клиента. При необходимости можно использовать конструктор [WebView(WebViewExecutionMode)](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.-ctor#Windows_UI_Xaml_Controls_WebView__ctor_Windows_UI_Xaml_Controls_WebViewExecutionMode_), чтобы переопределить это поведение. 

> **Примечание**&nbsp;&nbsp;При размещении содержимого в потоке пользовательского интерфейса на мобильных устройствах могут возникнуть проблемы с производительностью, поэтому обязательно проверяйте все целевые устройства при изменении DefaultExecutionMode.

Представление веб-страницы, в котором размещается содержимое вне потока пользовательского интерфейса, несовместимо с родительскими элементами управления, которым требуются жесты для распространения от элемента управления представления веб-страницы к родительскому элементу, например, [FlipView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.FlipView), [ScrollViewer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) и другие связанные элементы управления. Эти элементы управления не смогут получать жесты, вызванные в представлении веб-страницы вне потока. Кроме того, вывод веб-содержимого вне потока напрямую не поддерживается. Вместо этого необходимо выводить элемент с заполнением [WebViewBrush](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebViewBrush).

## <a name="recommendations"></a>Рекомендации


-   Убедитесь, что загруженный веб-сайт имеет правильный формат для отображения на устройстве, а его цвета, шрифтовое оформление и навигация соответствуют вашему приложению.
-   Поля ввода должны иметь правильный размер. Пользователи могут не понять, что они могут увеличить изображение поля для ввода текста.
-   Если представление веб-страницы не соответствует вашему приложению, введите альтернативные элементы управления или придумайте собственные способы решения соответствующих задач. Если представление веб-страницы соответствует вашему приложению, пользователи будут воспринимать их как единую среду.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-topics"></a>Связанные темы

- [Класс WebView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebView)
