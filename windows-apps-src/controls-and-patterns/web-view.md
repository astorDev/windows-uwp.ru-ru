---
Description: Элемент управления представлением веб-страницы внедряет в приложение представление, которое отображает содержимое веб-страницы с помощью механизма визуализации Microsoft Edge. В элементе управления представлением веб-страницы могут появляться и работать гиперссылки.
title: Руководство по представлению веб-страницы
ms.assetid: D3CFD438-F9D6-4B72-AF1D-16EF2DFC1BB1
label: Руководство по представлению веб-страницы
template: detail.hbs
---

# Представление веб-страницы

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Элемент управления представлением веб-страницы внедряет в приложение представление, которое отображает содержимое веб-страницы с помощью механизма визуализации Microsoft Edge. В элементе управления представлением веб-страницы могут появляться и работать гиперссылки.

**Важные API**

-   [**WebView class**](https://msdn.microsoft.com/library/windows/apps/br227702)

## Выбор правильного элемента управления

Используйте элемент управления представлением веб-страницы для отображения содержимого HTML-страниц расширенного формата с удаленного веб-сервера, динамически генерируемого кода или файлов содержимого в пакете приложения. Содержимое расширенного формата может также содержать код сценария и обеспечивать связь между этим сценарием и кодом вашего приложения.

## Создание представления веб-страницы

**Изменение внешнего вида представления веб-страницы**

[**WebView**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.aspx) не относится к подклассу [**Control**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.aspx), поэтому у него нет шаблона элемента управления. Но можно задать различные свойства для управления некоторыми визуальными аспектами представления веб-страницы.
- Чтобы ограничить область отображения, задайте свойства [**Width**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.width.aspx) и [**Height**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.height.aspx). 
- Для преобразования, масштабирования, наклона и поворота представления веб-страницы используйте свойство [**RenderTransform**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.rendertransform.aspx).
- Для управления прозрачностью представления веб-страницы задайте свойство [**Opacity**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.opacity.aspx).
- Для определения цвета, который необходимо использовать в качестве фона веб-страницы, если содержимое HTML не определяет цвет, задайте свойство [**DefaultBackgroundColor**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.defaultbackgroundcolor.aspx). 

**Получение заголовка веб-страницы**

Получить заголовок документа HTML, отображаемого в представлении веб-страницы в настоящий момент, можно с помощью свойства [**DocumentTitle**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.documenttitle.aspx). 

**События ввода и порядок вкладок**

Хотя WebView не относится к подклассу "Control", он получает фокус ввода с клавиатуры и участвует в порядке вкладок. Он предоставляет метод [**Focus**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.focus.aspx), а также события [**GotFocus**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.gotfocus.aspx) и [**LostFocus**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.lostfocus.aspx), но у него отсутствуют свойства, связанные с вкладками. Его положение в последовательности вкладок такое же, как его положение в порядке документа XAML. Последовательность вкладок включает все элементы в содержимом представления веб-страницы, которые могут получать фокус ввода. 

Как показано в таблице событий на странице класса [**WebView**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.aspx), представление веб-страницы не поддерживает большинство событий пользовательского ввода, унаследованных от [**UIElement**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.aspx), таких как [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.keydown.aspx), [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.keyup.aspx) и [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.pointerpressed.aspx). Вместо этого вы можете использовать [**InvokeScriptAsync**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.invokescriptasync.aspx) с функцией JavaScript **eval**, чтобы использовать обработчики событий HTML и **window.external.notify** из обработчика событий HTML для уведомления приложения с помощью [**WebView.ScriptNotify**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.scriptnotify.aspx).

### Переход к содержимому

Представление веб-страницы предоставляет несколько интерфейсов API для базовой навигации: [**GoBack**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.goback.aspx), [**GoForward**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.goforward.aspx), [**Stop**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.stop.aspx), [**Refresh**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.refresh.aspx), [**CanGoBack**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.cangoback.aspx) и [**CanGoForward**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.cangoforward.aspx). Вы можете использовать их, чтобы добавить в приложение типовые возможности веб-серфинга. 

Задайте в коде XAML свойство [**Source**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.source.aspx), чтобы настроить начальное содержимое представления веб-страницы. Анализатор XAML автоматически преобразует строку в [**Uri**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.foundation.uri.aspx). 

```xaml
<!-- Source file is on the web. -->
<WebView x:Name="webView1" Source="http://www.contoso.com"/>

<!-- Source file is in local storage. -->
<WebView x:Name="webView2" Source="ms-appdata:///local/intro/welcome.html"/>

<!-- Source file is in the app package. -->
<WebView x:Name="webView3" Source="ms-appx-web:///help/about.html"/>
```

Свойство "Source" можно задать в коде, но вместо этого для загрузки содержимого в код обычно предпочтительнее использовать один из методов **Navigate**. 

Для загрузки веб-содержимого используйте метод [**Navigate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.navigate.aspx) с **Uri**, который использует схему http или https. 

```csharp
webView1.Navigate("http://www.contoso.com");
```

Для перехода к URI с запросом POST и заголовками HTTP, используйте метод [**NavigateWithHttpRequestMessage**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.navigatewithhttprequestmessage.aspx). Этот метод поддерживает только [**HttpMethod.Post**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.web.http.httpmethod.post.aspx) и [**HttpMethod.Get**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.web.http.httpmethod.get.aspx) для значения свойства [**HttpRequestMessage.Method**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.web.http.httprequestmessage.method.aspx). 

Чтобы загрузить несжатое и незашифрованное содержимое хранилищ данных [**LocalFolder**]() или [**TemporaryFolder**]() вашего приложения, используйте метод **Navigate** с **Uri**, использующим [схему ms-appdata](). Для поддержки представлений веб-страницы для этой схемы необходимо поместить содержимое во вложенную папку локальной или временной папки. Это позволяет переходить к таким URI, как ms-appdata:///local/*folder*/*file*.html и ms-appdata:///temp/*folder*/*file*.html. (Подробнее о загрузке сжатых или зашифрованных файлов см. в разделе [**NavigateToLocalStreamUri**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.navigatetolocalstreamuri.aspx).) 

Каждая из этих вложенных папок первого уровня изолирована от содержимого других вложенных папок первого уровня. Например, вы можете перейти к ms-appdata:///temp/folder1/file.html, но в этом файле не может быть ссылки на ms-appdata:///temp/folder2/file.html. Тем не менее можно в пакете приложения сделать ссылку на содержимое HTML с помощью **схемы ms-appx-web** и на веб-содержимое с помощью схем URI **http** и **https**.

```csharp
webView1.Navigate("ms-appdata:///local/intro/welcome.html");
```

Для загрузки содержимого из пакета приложения используйте метод **Navigate** с **Uri**, который использует [** схему ms-appx-web**](https://msdn.microsoft.com/library/windows/apps/xaml/jj655406.aspx#ms_appx_web). 

```csharp
webView1.Navigate("ms-appx-web:///help/about.html");
```

Локальное содержимое можно загрузить с помощью пользовательского сопоставителя, используя метод [**NavigateToLocalStreamUri**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.navigatetolocalstreamuri.aspx). Это позволяет выполнять сложные сценарии, например скачивание и кэширование веб-содержимого для использования в автономном режиме или извлечение содержимого из сжатого файла.

### Реакция на события навигации

Элемент управления представлением веб-страницы имеет несколько событий, которые можно использовать, чтобы реагировать на состояния навигации и загрузки содержимого. Для корневого содержимого представления веб-страницы события происходят в следующем порядке: [**NavigationStarting**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.navigationstarting.aspx), [**ContentLoading**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.contentloading.aspx), [**DOMContentLoaded**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.domcontentloaded.aspx), [**NavigationCompleted**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.navigationcompleted.aspx)


**NavigationStarting** — происходит перед тем, как представление веб-страницы переходит к новому содержимому. Можно отменить навигацию в обработчике для этого события, задав для свойства WebViewNavigationStartingEventArgs.Cancel значение "true". 

```csharp
webView1.NavigationStarting += webView1_NavigationStarting;

private void webView1_NavigationStarting(object sender, WebViewNavigationStartingEventArgs args)
{
    // Cancel navigation if URL is not allowed. (Implemetation of IsAllowedUri not shown.)
    if (!IsAllowedUri(args.Uri))
        args.Cancel = true;
}
```

**ContentLoading** — происходит, когда представление веб-страницы начинает загружать новое содержимое. 

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

**DOMContentLoaded** — происходит, когда представление веб-страницы заканчивает анализировать текущее содержимое HTML. 

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

**NavigationCompleted** — происходит, когда представление веб-страницы заканчивает загружать текущее содержимое или при сбое навигации. Чтобы определить результат навигации, проверьте свойства [**IsSuccess**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewnavigationcompletedeventargs.issuccess.aspx) и [**WebErrorStatus**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewnavigationcompletedeventargs.weberrorstatus.aspx) класса [**WebViewNavigationCompletedEventArgs**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewnavigationcompletedeventargs.aspx). 

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
- [**FrameNavigationStarting**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.framenavigationstarting.aspx) — происходит перед тем, как кадр в представлении веб-страницы перейдет к новому содержимому. 
- [**FrameContentLoading**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.framecontentloading.aspx) — происходит, когда кадр в представлении веб-страницы начнет загрузку нового содержимого. 
- [**FrameDOMContentLoaded**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.framedomcontentloaded.aspx) — происходит, когда кадр в представлении веб-страницы заканчивает анализ текущего содержимого HTML. 
- [**FrameNavigationCompleted**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.framenavigationcompleted.aspx) — происходит, когда кадр в представлении веб-страницы заканчивает загружать свое содержимое. 

### Реакция на потенциальные проблемы

Вы можете реагировать на потенциальные проблемы с содержимым, такие как длинные выполняемые сценарии, содержимое, которое представление веб-страницы не может загрузить, и предупреждения о небезопасном содержимом. 

Ваше приложение может перестать отвечать на действия пользователя, пока выполняются сценарии. Пока представление веб-страницы выполняет сценарий JavaScript, периодически происходит событие [**LongRunningScriptDetected**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.longrunningscriptdetected.aspx), которое предоставляет возможность прервать сценарий. Чтобы определить, как долго выполняется сценарий, проверьте свойство [**ExecutionTime**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewlongrunningscriptdetectedeventargs.executiontime.aspx) элемента [**WebViewLongRunningScriptDetectedEventArgs**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewlongrunningscriptdetectedeventargs.aspx). Чтобы остановить сценарий, задайте свойству аргументов событий [**StopPageScriptExecution**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewlongrunningscriptdetectedeventargs.stoppagescriptexecution.aspx) значение **true**. Остановленный сценарий не будет выполняться снова, пока он не будет перезагружен во время последующего перехода представления веб-страницы. 

Элемент управления представлением веб-страницы не может размещать произвольные типы файлов. При выполнении попытки загрузить содержимое, которое представление веб-страницы не может размещать, происходит событие [**UnviewableContentIdentified**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.unviewablecontentidentified.aspx). Вы можете обработать такое событие и уведомить пользователя или использовать класс [**Launcher**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.system.launcher.aspx), чтобы перенаправить файл во внешний браузер или другое приложение.

Аналогичным образом событие [**UnsupportedUriSchemeIdentified**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.unsupportedurischemeidentified.aspx) происходит, когда неподдерживаемая схема URI (например, fbconnect:// или mailto://) вызывается в веб-содержимом. Вы можете обработать это событие для выполнения специального действия, а не разрешать системному обработчику по умолчанию запускать этот URI.

Событие [**UnsafeContentWarningDisplayingevent**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.unsafecontentwarningdisplaying.aspx) возникает, когда представление веб-страницы отображает страницу предупреждения для содержимого, которое фильтр SmartScreen определил как небезопасное. Если пользователь решает продолжить навигацию, последующий переход на страницу не будет отображать предупреждение или вызывать событие.

### Обработка особых случаев для содержимого представления веб-страницы

Вы можете использовать свойство [**ContainsFullScreenElement**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.containsfullscreenelement.aspx) и событие [**ContainsFullScreenElementChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.containsfullscreenelementchanged.aspx), чтобы определять, реагировать и включать полноэкранный интерфейс в веб-содержимом, например воспроизведение видео в полноэкранном режиме. Например, можно использовать событие ContainsFullScreenElementChanged, чтобы изменить размер представления веб-страницы таким образом, чтобы оно полностью заняло представление приложения. Это событие также, как показано в следующем примере, можно использовать для перевода приложения из режима окна в полноэкранный режим, если требуется полноэкранное веб-приложение.

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

Событие [**NewWindowRequested**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.newwindowrequested.aspx) можно использовать для обработки случаев, в которых размещаемое веб-содержимое отправляет запрос на отображение нового окна, например всплывающего окна. Вы можете использовать другой элемент управления WebView, чтобы отобразить содержимого этого окна.

Используйте событие [**PermissionRequested**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.permissionrequested.aspx), чтобы включить веб-функции, которым требуются специальные возможности. Сейчас эти функции включают в себя географическое положение, хранилище IndexedDB, а также звук и видеоизображение пользователя (например, с микрофона или веб-камеры). Если ваше приложение получает доступ к данным о местоположении пользователя или его мультимедиа, вы обязаны объявить эту возможность в манифесте приложения. Например, приложению, использующему географическое положение, нужны следующие объявления возможностей как минимум в файле Package.appxmanifest.

```xml
  <Capabilities>
    <Capability Name="internetClient" />
    <DeviceCapability Name="location" />
  </Capabilities>
```

Для включения этих функций, кроме обработки события [**PermissionRequested**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.permissionrequested.aspx) приложением, пользователь должен будет утвердительно ответить на стандартные системные диалоги для приложений, запрашивающих разрешение на доступ к сведениям о местоположении или мультимедиа.

Приведем пример включения приложением географического положения на карте из коллекции Bing.

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

Если вашему приложению требуется пользовательский ввод или другие асинхронные операции, чтобы ответить на запрос разрешения, используйте метод [**Defer**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewpermissionrequest.defer.aspx) функции [**WebViewPermissionRequest**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewpermissionrequest.aspx), чтобы создать событие [**WebViewDeferredPermissionRequest**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewdeferredpermissionrequest.aspx), которое может быть выполнено позднее. Подробнее: [**WebViewPermissionRequest.Defer**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewpermissionrequest.defer.aspx). 

Если требуется, чтобы пользователи безопасно выходили с веб-сайта, размещенного в представлении веб-страницы, или в других случаях, в которых важна безопасность, вызовите статический метод [**ClearTemporaryWebDataAsync**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.cleartemporarywebdataasync.aspx) для очистки всего локально кэшированного содержимого из сеанса представления веб-страницы. Это предотвратит доступ злоумышленников к конфиденциальным данным. 

### Взаимодействие с содержимым представления веб-страницы

Вы можете взаимодействовать с содержимым представления веб-страницы с помощью метода [**InvokeScriptAsync**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.invokescriptasync.aspx), чтобы вызвать или внедрить в него сценарий, и события [**ScriptNotify**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.scriptnotify.aspx), чтобы получить сведения от содержимого представления веб-страницы.

Чтобы вызвать сценарий JavaScript внутри представления веб-страницы, используйте метод [**InvokeScriptAsync**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.invokescriptasync.aspx). Вызванный сценарий может возвращать только строковые значения. 

Например, если содержимое представления веб-страницы с именем `webView1` содержит функцию с именем `setDate`, это составит три параметра, которые вы можете вызвать следующим образом. 

```csharp
string[] args = {"January", "1", "2000"};
string returnValue = await webView1.InvokeScriptAsync("setDate", args);
```


Можно использовать **InvokeScriptAsync** с функцией JavaScript **eval**, чтобы внедрить содержимое в веб-страницу.

В следующем примере текст текстового поля XAML (`nameTextBox.Text`) написан в элементе div в странице HTML, размещенной в `webView1`. 

```csharp
private async void Button_Click(object sender, RoutedEventArgs e)
{
    string functionString = String.Format("document.getElementById('nameDiv').innerText = 'Hello, {0}';", nameTextBox.Text);
    await webView1.InvokeScriptAsync("eval", new string[] { functionString });
}
```

Сценарии в содержимом представления веб-страницы могут использовать событие **window.external.notify** с параметром строки для отправки сведений в ваше приложение. Для получения этих сообщений обработайте событие [**ScriptNotify**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.scriptnotify.aspx). 

Чтобы разрешить внешней веб-странице запускать событие **ScriptNotify** при вызове "window.external.notify", необходимо включить URI этой страницы в раздел **ApplicationContentUriRules** манифеста приложения. (Это можно сделать в Microsoft Visual Studio на вкладке "URI содержимого" конструктора Package.appxmanifest.) URI в этом списке должны использовать HTTPS и могут содержать знаки подстановки для поддоменов (например, `https://*.microsoft.com`), но не могут содержать знаки подстановки для доменов (например, `https://*.com` и `https://*.*`). Требования манифеста не применяются к содержимому, которое поступает из пакета приложения, использует URI ms-local-stream:// или загружается с помощью метода [**NavigateToString**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.navigatetostring.aspx). 

### Доступ к среде выполнения Windows в представлении веб-страницы

Метод [**AddWebAllowedObject**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.addweballowedobject.aspx) можно использовать для внедрения экземпляра собственного класса из компонента среды выполнения Windows в контекст JavaScript представления веб-страницы. Это дает полный доступ к собственным методам, свойствам и событиям этого объекта в содержимом JavaScript данного представления веб-страницы. Этот класс должен быть помечен атрибутом [**AllowForWeb**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.foundation.metadata.allowforwebattribute.aspx). 

Например, данный код внедряет экземпляр `MyClass`, импортированный из компонента среды выполнения Windows, в представление веб-страницы.

```csharp
private void webView_NavigationStarting(WebView sender, WebViewNavigationStartingEventArgs args) 
{ 
    if (args.Uri.Host == "www.contoso.com")  
    { 
        webView.AddWebAllowedObject("nativeObject", new MyClass()); 
    } 
}
```

Подробнее см. в разделе [**WebView.AddWebAllowedObject**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.addweballowedobject.aspx). 

Кроме того, доверенное содержимое JavaScript в представлении веб-страницы может получить разрешение на прямой доступ к API среды выполнения Windows. Это предоставляет мощные собственные функции для веб-приложений, размещенных в представлении веб-страницы. Чтобы реализовать эту функцию, URI для доверенного содержимого должен быть добавлен в список разрешенных URI в ApplicationContentUriRules приложения в Package.appxmanifest, при этом для WindowsRuntimeAccess должно быть специально установлено значение "all". 

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

### Параметры для размещения веб-содержимого

Вы можете использовать свойство [**WebView.Settings**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.settings.aspx) (типа [**WebViewSettings**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewsettings.aspx)), чтобы включать или отключать JavaScript и IndexedDB. Например, если вы используете представление веб-страницы для отображения исключительно статического содержимого, возможно, вы захотите отключить JavaScript для повышения производительности.

### Захват содержимого представления веб-страницы

Чтобы включить общий доступ к содержимому представления веб-страницы для других приложений, используйте метод [**CaptureSelectedContentToDataPackageAsync**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.captureselectedcontenttodatapackageasync.aspx), который возвращает выбранное содержимое в виде [**DataPackage**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.applicationmodel.datatransfer.datapackage.aspx). Это асинхронный метод, поэтому вы должны использовать задержку, чтобы предотвратить возврат вашего обработчика событий [**DataRequested**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.applicationmodel.datatransfer.datatransfermanager.datarequested.aspx) до завершения асинхронного вызова. 

Чтобы получить изображение предпросмотра текущего содержимого представления веб-страницы, используйте метод [**CapturePreviewToStreamAsync**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.capturepreviewtostreamasync.aspx). Этот метод создает образ текущей страницы и записывает его в указанный поток. 

### Поведение потоков

По умолчанию содержимое представления веб-страницы размещается в потоке пользовательского интерфейса на устройствах в семействе настольных устройств и вне потока пользовательского интерфейса на всех других устройствах. Статическое свойство [**WebView.DefaultExecutionMode**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webview.defaultexecutionmode.aspx) можно использовать для запроса поведения потока по умолчанию для текущего клиента. При необходимости можно использовать конструктор [**WebView(WebViewExecutionMode)**](https://msdn.microsoft.com/library/windows/apps/xaml/dn932036.aspx), чтобы определить это поведение. 

> **Примечание.**  При размещении содержимого в потоке пользовательского интерфейса на мобильных устройствах могут возникнуть проблемы с производительностью, поэтому убедитесь, что после изменения DefaultExecutionMode вы проверили работу приложения на всех целевых устройствах.

Представление веб-страницы, в котором размещается содержимое вне потока пользовательского интерфейса, несовместимо с родительскими элементами управления, которым требуются жесты для распространения вверх от элемента управления представления веб-страницы к родительскому элементу, например [**FlipView**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.flipview.aspx), [**ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.aspx) и другие связанные элементы управления. Эти элементы управления не смогут получать жесты, вызванные в представлении веб-страницы вне потока. Кроме того, вывод веб-содержимого вне потока не поддерживается прямо. Вместо этого необходимо выводить элемент с заполнением [**WebViewBrush**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.webviewbrush.aspx).

## <span id="Recommendations"></span><span id="recommendations"></span><span id="RECOMMENDATIONS"></span>Рекомендации


-   Убедитесь, что загруженный веб-сайт имеет правильный формат при отображении на данном устройстве, а также использует цвета, шрифтовое оформление и навигацию в соответствии с вашим приложением.
-   Поля ввода должны иметь правильный размер. Пользователи могут не понять, что у них есть возможность масштабирования для ввода текста.
-   Если представление веб-страницы не соответствует вашему приложению, введите альтернативные элементы управления или придумайте собственные способы решения соответствующих задач. Если представление веб-страницы соответствует вашему приложению, пользователи будут воспринимать его как должное.

\[Эта статья содержит сведения, относящиеся к приложениям универсальной платформы Windows (UWP) и Windows 10. Для получения инструкций по Windows 8.1, скачайте [рекомендации для Windows 8.1 в формате PDF](https://go.microsoft.com/fwlink/p/?linkid=258743).\]

## <span id="related_topics"></span>Связанные разделы

* [**WebView class**](https://msdn.microsoft.com/library/windows/apps/br227702)
 

 






<!--HONumber=Mar16_HO1-->


