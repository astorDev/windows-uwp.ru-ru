---
author: normesta
Description: Extend your desktop application with Windows UIs and components
Search.Product: eADQiWindows 10XVcnh
title: Расширение классических приложений с помощью компонентов и элементов пользовательского интерфейса Windows
ms.author: normesta
ms.date: 03/22/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: ef20366092a5f284c39f4e43d4412c69b60f12fa
ms.sourcegitcommit: 6618517dc0a4e4100af06e6d27fac133d317e545
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "1691333"
---
# <a name="extend-your-desktop-application-with-modern-uwp-components"></a>Расширение классических приложений с помощью современных компонентов UWP

Некоторые возможности Windows 10, такие как страницы с подходящим для сенсорного ввода интерфейсом, должны выполняться внутри современного контейнера приложения. Если вы хотите добавить эти возможности, расширьте свое классическое приложение, используя проекты UWP и компоненты среды выполнения Windows.

Во многих случаях можно вызывать API-интерфейсы UWP непосредственно из классического приложения, поэтому перед прочтением этого руководства просмотрите раздел [Расширение для Windows 10](desktop-to-uwp-enhance.md).

>[!NOTE]
>В этом руководстве предполагается, что вы создали пакет приложения для Windows для своего классического приложения с помощью моста для классических приложений. Если вы еще не сделали этого, см. раздел [Мост для классических приложений](desktop-to-uwp-root.md).

Если вы готовы, давайте начнем.

## <a name="first-setup-your-solution"></a>Сначала настройте свое решение.

Добавьте один или несколько проектов UWP и компонентов среды выполнения в свое решение.

Начните с решения, которое содержит **проект упаковки приложения для Windows** со ссылкой на классическое приложение.

На этом рисунке показан пример решения.

![Расширение начального проекта](images/desktop-to-uwp/extend-start-project.png)

Если ваше решение не содержит пакетный проект, обратитесь к разделу [Упаковка приложения с помощью Visual Studio](desktop-to-uwp-packaging-dot-net.md).

### <a name="add-a-uwp-project"></a>Добавление проекта UWP

Добавьте проект **Пустое приложение (универсальная платформа Windows)** в свое решение.

Там вы сможете создать современный пользовательский интерфейс XAML или использовать API, которые выполняются только в процессе UWP.

![Проект UWP](images/desktop-to-uwp/add-uwp-project-to-solution.png)

В проекте упаковки щелкните правой кнопкой узел **Приложения** и выберите **Добавить ссылку**.

![Добавление ссылки на проект UWP](images/desktop-to-uwp/add-uwp-project-reference.png)

Затем добавьте ссылку на проект UWP.

![Добавление ссылки на проект UWP](images/desktop-to-uwp/choose-uwp-project.png)

Решение будет выглядеть следующим образом.

![Решение с проектом UWP](images/desktop-to-uwp/uwp-project-reference.png)

### <a name="optional-add-a-windows-runtime-component"></a>(Дополнительно) Создайте компонент среды выполнения Windows

Для выполнения некоторых сценариев потребуется добавить код в компонент среды выполнения Windows.

![служба приложения компонента среды выполнения](images/desktop-to-uwp/add-runtime-component.png)

Затем в проекте UWP добавьте ссылку на компонент среды выполнения. Решение будет выглядеть следующим образом.

![Ссылка на компонент среды выполнения](images/desktop-to-uwp/runtime-component-reference.png)

Рассмотрим некоторые действия, которые можно выполнять с проектами UWP и компонентами среды выполнения.

## <a name="show-a-modern-xaml-ui"></a>Отображение современного пользовательского интерфейса XAML

В рамках разработки вы можете внедрить в свое классическое приложение современные интерфейсы пользователя на основе XAML. Эти интерфейсы пользователя естественным образом адаптируются под различные размеры и разрешение экрана и поддерживают современные интерактивные модели, например сенсорный и рукописный ввод.

Например, с помощью небольшого количества разметки XAML вы можете предоставить пользователям мощные функции визуализации, связанные с картой.

На рисунке показано приложение VB6, которое открывает современный пользовательский интерфейс на основе XAML, содержащий элементы управления картой.

![адаптивный макет](images/desktop-to-uwp/extend-xaml-ui.png)

### <a name="have-a-closer-look-at-this-app"></a>Рассмотрим это приложение подробнее

:heavy_check_mark: [Получить приложение](https://www.microsoft.com/en-us/store/p/vb6-app-with-xaml-sample/9n191ncxf2f6)

:heavy_check_mark: [Просмотреть код](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/VB6withXaml)

### <a name="the-design-pattern"></a>Шаблон проектирования

Для отображения пользовательского интерфейса на основе XAML выполните следующие действия.

:one: [Добавьте расширение протокола в этот проект](#protocol)

:two: [Запустите приложение UWP из классического приложения](#start)

:three: [В проекте UWP отобразите требуемую страницу](#parse)

<a id="protocol" />

### <a name="add-a-protocol-extension"></a>Добавление расширение протокола

В **Обозревателе решений** откройте файл **package.appxmanifest** проекта UWP в вашем решении и добавьте это расширение.

```xml
<Extensions>
      <uap:Extension
          Category="windows.protocol"
          Executable="MapUI.exe"
          EntryPoint=" MapUI.App">
        <uap:Protocol Name="desktopbridgemapsample" />
      </uap:Extension>
    </Extensions>     
```

Задайте имя протокола, введите имя исполняемого файла, созданного проектом UWP, и имя класса точки входа.

Также можно открыть **package.appxmanifest** в конструкторе, выбрать вкладку **Объявления** и затем добавить сюда расширение.

![вкладка объявления](images/desktop-to-uwp/protocol-properties.png)



> [!NOTE]
> Элементы управления картой скачивают данные из Интернета, поэтому, если вы используете такой элемент, необходимо также добавить возможность internet client в манифест.

<a id="start" />

### <a name="start-the-uwp-app"></a>Запуск приложения UWP

Во-первых, из классического приложения создайте [Uri](https://msdn.microsoft.com/library/system.uri.aspx), включающий имя протокола и все параметры, которые необходимо передать в приложение UWP. Затем вызовите метод [LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync).

Вот простой пример на языке C#.

```csharp

private async void showMap(double lat, double lon)
{
    string str = "desktopbridgemapsample://";

    Uri uri = new Uri(str + "location?lat=" +
        lat.ToString() + "&?lon=" + lon.ToString());

    var success = await Windows.System.Launcher.LaunchUriAsync(uri);

    if (success)
    {
        // URI launched
    }
    else
    {
        // URI launch failed
    }
}
```
В нашем примере мы делаем нечто более сложное. Мы заключаем вызов в функцию взаимодействия, которую можно вызывать в VB6, с именем ``LaunchMap``. Эта функция написана с использованием C++.

Вот блок кода VB.

```VB
Private Declare Function LaunchMap Lib "UWPWrappers.dll" _
  (ByVal lat As Double, ByVal lon As Double) As Boolean
 
Private Sub EiffelTower_Click()
    LaunchMap 48.858222, 2.2945
End Sub
```

Вот функция на C++.

```C++

DllExport bool __stdcall LaunchMap(double lat, double lon)
{
  try
  {
    String ^str = ref new String(L"desktopbridgemapsample://");
    Uri ^uri = ref new Uri(
      str + L"location?lat=" + lat.ToString() + L"&?lon=" + lon.ToString());
 
    // now launch the UWP component
    Launcher::LaunchUriAsync(uri);
  }
  catch (Exception^ ex) { return false; }
  return true;
}

```

<a id="parse" />

### <a name="parse-parameters-and-show-a-page"></a>Анализ параметров и показ страницы

В классе **App** своего проекта UWP переопределите обработчик событий **OnActivated**. Если приложение активируется вашим протоколом, выполните анализ параметров, а затем откройте требуемую страницу.

```C++
void App::OnActivated(Windows::ApplicationModel::Activation::IActivatedEventArgs^ e)
{
  if (e->Kind == ActivationKind::Protocol)
  {
    ProtocolActivatedEventArgs^ protocolArgs = (ProtocolActivatedEventArgs^)e;
    Uri ^uri = protocolArgs->Uri;
    if (uri->SchemeName == "desktopbridgemapsample")
    {
      Frame ^rootFrame = ref new Frame();
      Window::Current->Content = rootFrame;
      rootFrame->Navigate(TypeName(MainPage::typeid), uri->Query);
      Window::Current->Activate();
    }
  }
}
```

### <a name="similar-samples"></a>Аналогичные примеры

[Пример Northwind: полный пример для пользовательского интерфейса UWA и устаревшего кода Win32](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/NorthwindSample)

[Пример Northwind: приложение UWP подключается к SQL Server](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SQLServer)

## <a name="provide-services-to-other-apps"></a>Предоставление служб другим приложениям

Вы добавляете службу, которую могут использовать другие приложения. Например, можно добавить службу, которая предоставляет другим приложениям управляемый доступ к базе данных вашего приложения. Путем реализации фоновой задачи приложения могут связаться со службой, даже если ваше классическое приложение не выполняется.

Вот пример, в котором это демонстрируется.

![адаптивный макет](images/desktop-to-uwp/winforms-app-service.png)

### <a name="have-a-closer-look-at-this-app"></a>Рассмотрим это приложение подробнее

:heavy_check_mark: [Получить приложение](https://www.microsoft.com/en-us/store/p/winforms-appservice/9p7d9b6nk5tn)

:heavy_check_mark: [Просмотреть код](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WinformsAppService)

### <a name="the-design-pattern"></a>Шаблон проектирования

Для предоставления службы выполните следующие действия.

:one: [Реализуйте службу приложения](#appservice)

:two: [Добавьте расширение службы приложения](#extension)

:three: [Протестируйте службу приложения](#test)

<a id="appservice" />

### <a name="implement-the-app-service"></a>Реализация службы приложения

Здесь вы будете проверять и обрабатывать запросы из других приложений. Добавьте этот код в компонент среды выполнения Windows в вашем решении.

```csharp
public sealed class AppServiceTask : IBackgroundTask
{
    private BackgroundTaskDeferral backgroundTaskDeferral;
 
    public void Run(IBackgroundTaskInstance taskInstance)
    {
        this.backgroundTaskDeferral = taskInstance.GetDeferral();
        taskInstance.Canceled += OnTaskCanceled;
        var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
        details.AppServiceConnection.RequestReceived += OnRequestReceived;
    }
 
    private async void OnRequestReceived(AppServiceConnection sender,
                                         AppServiceRequestReceivedEventArgs args)
    {
        var messageDeferral = args.GetDeferral();
        ValueSet message = args.Request.Message;
        string id = message["ID"] as string;
        ValueSet returnData = DataBase.GetData(id);
        await args.Request.SendResponseAsync(returnData);
        messageDeferral.Complete();
    }
 
 
    private void OnTaskCanceled(IBackgroundTaskInstance sender,
                                BackgroundTaskCancellationReason reason)
    {
        if (this.backgroundTaskDeferral != null)
        {
            this.backgroundTaskDeferral.Complete();
        }
    }
}
```

<a id="extension" />

### <a name="add-an-app-service-extension-to-the-uwp-project"></a>Добавление в проект UWP расширения службы приложений

Откройте файл **package.appxmanifest** проекта UWP и добавьте расширение службы приложения в элемент ``<Application>``.

```xml
<Extensions>
      <uap:Extension
          Category="windows.appService"
          EntryPoint="AppServiceComponent.AppServiceTask">
        <uap:AppService Name="com.microsoft.samples.winforms" />
      </uap:Extension>
    </Extensions>    
```
Задайте службе приложения имя и укажите имя класса точки входа. Это класс, в котором вы реализовали эту службу.

<a id="test" />

### <a name="test-the-app-service"></a>Тестирование службы приложения

Протестируйте службу путем вызова из другого приложения. Этот код может представлять классическое приложение, например приложение Windows Forms или другое приложение UWP.

> [!NOTE]
> Этот код работает, только если правильно задать свойство ``PackageFamilyName`` класса ``AppServiceConnection``. Это имя можно получить, вызвав метод ``Windows.ApplicationModel.Package.Current.Id.FamilyName`` в контексте проекта UWP. См. раздел [Создание и использование службы приложения](https://docs.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service).

```csharp
private async void button_Click(object sender, RoutedEventArgs e)
{
    AppServiceConnection dataService = new AppServiceConnection();
    dataService.AppServiceName = "com.microsoft.samples.winforms";
    dataService.PackageFamilyName = "Microsoft.SDKSamples.WinformWithAppService";
 
    var status = await dataService.OpenAsync();
    if (status == AppServiceConnectionStatus.Success)
    {
        string id = int.Parse(textBox.Text);
        var message = new ValueSet();
        message.Add("ID", id);
        AppServiceResponse response = await dataService.SendMessageAsync(message);
 
        if (response.Status == AppServiceResponseStatus.Success)
        {
            if (response.Message["Status"] as string == "OK")
            {
                DisplayResult(response.Message["Result"]);
            }
        }
    }
}
```

Дополнительные сведения о службах приложения можно найти здесь: [Создание и использование службы приложения](https://docs.microsoft.com/windows/uwp/launch-resume/how-to-create-and-consume-an-app-service).

### <a name="similar-samples"></a>Аналогичные примеры

[Пример моста службы приложения](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/AppServiceBridgeSample)

[Пример моста службы приложения с приложением для win32 на C++](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/AppServiceBridgeSample_C%2B%2B)

[Приложение MFC, которое получает push-уведомления](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/MFCwithPush)


## <a name="making-your-desktop-application-a-share-target"></a>Присвоение классическому приложению роли получателя данных

Вы можете сделать свое классическое приложение получателем данных, чтобы пользователи могли легко передавать ему данные из других приложений, поддерживающих общий доступ, например изображения.

Пользователи могут выбрать ваше приложение, чтобы передать ему изображения из Microsoft Edge или приложения "Фотографии". Вот пример приложения WPF, поддерживающего такую возможность.

![получатель данных](images/desktop-to-uwp/share-target.png)

### <a name="have-a-closer-look-at-this-app"></a>Рассмотрим это приложение подробнее

:heavy_check_mark: [Получить приложение](https://www.microsoft.com/en-us/store/p/wpf-app-as-sharetarget/9pjcjljlck37)

:heavy_check_mark: [Просмотреть код](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WPFasShareTarget)

### <a name="the-design-pattern"></a>Шаблон проектирования

Чтобы сделать ваше приложение получателем данных, выполните следующие действия.

:one: [Добавьте расширение получателя данных](#share-extension)

:two: [Переопределение обработчика событий OnNavigatedTo](#override)

<a id="share-extension" />

### <a name="add-a-share-target-extension"></a>Добавление расширения получателя данных

В **Обозревателе решений** откройте файл **package.appxmanifest** проекта UWP в вашем решении и добавьте это расширение.

```xml
<Extensions>
      <uap:Extension
          Category="windows.shareTarget"
          Executable="ShareTarget.exe"
          EntryPoint="ShareTarget.App">
        <uap:ShareTarget>
          <uap:SupportedFileTypes>
            <uap:SupportsAnyFileType />
          </uap:SupportedFileTypes>
          <uap:DataFormat>Bitmap</uap:DataFormat>
        </uap:ShareTarget>
      </uap:Extension>
</Extensions>  
```

Укажите имя исполняемого файла, созданного проектом UWP, и имя класса точки входа. Также необходимо указать типы файлов, которые могут быть переданы вашему приложению.

<a id="override" />

### <a name="override-the-onnavigatedto-event-handler"></a>Переопределение обработчика событий OnNavigatedTo

В классе **App** своего проекта UWP переопределите обработчик событий **OnNavigatedTo**.

Этот обработчик событий вызывается, когда пользователи выбирают ваше приложение в качестве получателя файлов.

```csharp
protected override async void OnNavigatedTo(NavigationEventArgs e)
{
  this.shareOperation = (ShareOperation)e.Parameter;
  if (this.shareOperation.Data.Contains(StandardDataFormats.StorageItems))
  {
      this.sharedStorageItems =
        await this.shareOperation.Data.GetStorageItemsAsync();
       
      foreach (StorageFile item in this.sharedStorageItems)
      {
          ProcessSharedFile(item);
      }
  }
}
```

## <a name="create-a-background-task"></a>Создание фоновой задачи

Добавьте фоновую задачу для выполнения кода, даже если приложение приостановлено. Фоновые задачи отлично подходят для небольших задач, не требующих участия пользователя. Например, ваша задача может скачивать почту, показывать всплывающее уведомление о входящем сообщении в чате или реагировать на изменение системного условия.

Вот пример приложения WPF, которое регистрирует фоновую задачу.

![фоновая задача](images/desktop-to-uwp/sample-background-task.png)

Эта задача создает запрос http и измеряет время, необходимое, чтобы запрос вернул отклик. Ваши задачи наверняка будут более интересными, но это отличный пример для изучения базового механизма фоновой задачи.

### <a name="have-a-closer-look-at-this-app"></a>Рассмотрим это приложение подробнее

:heavy_check_mark: [Просмотреть код](https://github.com/Microsoft/Windows-Packaging-Samples/tree/master/BGTask)

### <a name="the-design-pattern"></a>Шаблон проектирования

Чтобы создать фоновую службу, выполните следующие действия:

:one: [Реализация фоновой задачи](#implement-task)

:two: [Настройка фоновой задачи](#configure-background-task)

:three: [Регистрация фоновой задачи](#register-background-task)

<a id="implement-task" />

### <a name="implement-the-background-task"></a>Реализация фоновой задачи

Реализуйте фоновую задачу, добавив код в проект компонентов среды выполнения Windows.

```csharp
public sealed class SiteVerifier : IBackgroundTask
{
    public async void Run(IBackgroundTaskInstance taskInstance)
    {

        taskInstance.Canceled += TaskInstance_Canceled;
        BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
        var msg = await MeasureRequestTime();
        ShowToast(msg);
        deferral.Complete();
    }

    private async Task<string> MeasureRequestTime()
    {
        string msg;
        try
        {
            var url = ApplicationData.Current.LocalSettings.Values["UrlToVerify"] as string;
            var http = new HttpClient();
            Stopwatch clock = Stopwatch.StartNew();
            var response = await http.GetAsync(new Uri(url));
            response.EnsureSuccessStatusCode();
            var elapsed = clock.ElapsedMilliseconds;
            clock.Stop();
            msg = $"{url} took {elapsed.ToString()} ms";
        }
        catch (Exception ex)
        {
            msg = ex.Message;
        }
        return msg;
    }
```

<a id="configure-background-task" />

### <a name="configure-the-background-task"></a>Настройка фоновой задачи

В конструкторе манифестов откройте файл **package.appxmanifest** проекта UWP в своем решении.

На вкладке **Объявления** добавьте объявление **Фоновые задачи**.

![Вариант фоновой задачи](images/desktop-to-uwp/background-task-option.png)

Затем выберите требуемые свойства. В нашем примере используется свойство **Timer**.

![Свойство Timer](images/desktop-to-uwp/timer-property.png)

Укажите полное имя класса в компоненте среды выполнения Windows, который реализует фоновую задачу.

![Свойство Timer](images/desktop-to-uwp/background-task-entry-point.png)

<a id="register-background-task" />

### <a name="register-the-background-task"></a>Регистрация фоновой задачи

Добавьте код в проект классического приложения, регистрирующий фоновую задачу.

```csharp
public void RegisterBackgroundTask(String triggerName)
{
    var current = BackgroundTaskRegistration.AllTasks
        .Where(b => b.Value.Name == triggerName).FirstOrDefault().Value;

    if (current is null)
    {
        BackgroundTaskBuilder builder = new BackgroundTaskBuilder();
        builder.Name = triggerName;
        builder.SetTrigger(new MaintenanceTrigger(15, false));
        builder.TaskEntryPoint = "HttpPing.SiteVerifier";
        builder.Register();
        System.Diagnostics.Debug.WriteLine("BGTask registered:" + triggerName);
    }
    else
    {
        System.Diagnostics.Debug.WriteLine("Task already:" + triggerName);
    }
}
```
## <a name="support-and-feedback"></a>Поддержка и отзывы

**Поиск ответов на вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Оставьте отзыв или предложите новые возможности для реализации**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)
