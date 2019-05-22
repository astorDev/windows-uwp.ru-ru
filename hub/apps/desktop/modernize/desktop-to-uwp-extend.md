---
Description: Расширение классических приложений с помощью компонентов и элементов пользовательского интерфейса Windows
title: Расширение классических приложений с помощью компонентов и элементов пользовательского интерфейса Windows
ms.date: 06/08/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: ff87990341b3936b5f2e02b8ff8b119a23188e92
ms.sourcegitcommit: f0f933d5cf0be734373a7b03e338e65000cc3d80
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65984434"
---
# <a name="extend-your-desktop-app-with-modern-uwp-components"></a>Расширить свое приложение с помощью современных компоненты UWP

Некоторые возможности Windows 10, такие как страницы с подходящим для сенсорного ввода интерфейсом, должны выполняться внутри современного контейнера приложения. Если вы хотите добавить эти возможности, расширьте свое классическое приложение, используя проекты UWP и компоненты среды выполнения Windows.

Во многих случаях можно вызвать API среды выполнения Windows непосредственно из своего настольного приложения, поэтому перед Ознакомьтесь в этом руководстве, см. в разделе [улучшения для Windows 10](desktop-to-uwp-enhance.md).

> [!NOTE]
> Функции, описанные в этой статье требуется создать пакет приложения Windows для своего настольного приложения. Если вы еще не сделали этого, см. в разделе [пакет приложений для настольных компьютеров](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root).

Если вы готовы, давайте начнем.

<a id="setup" />

## <a name="first-setup-your-solution"></a>Сначала настройте свое решение.

Добавьте один или несколько проектов UWP и компонентов среды выполнения в свое решение.

Начните с решения, которое содержит **проект упаковки приложения для Windows** со ссылкой на классическое приложение.

На этом рисунке показан пример решения.

![Расширение начального проекта](images/desktop-to-uwp/extend-start-project.png)

Если решение не содержит проект упаковки, см. в разделе [упаковки классического приложения с помощью Visual Studio](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net).

### <a name="configure-the-desktop-application"></a>Настройка классического приложения

Убедитесь, что приложение рабочего стола имеет ссылки на файлы, необходимые для вызова API среды выполнения Windows.

Чтобы сделать это, см. в разделе [проект](desktop-to-uwp-enhance.md#set-up-your-project) раздел.

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

### <a name="build-your-solution"></a>Построение решения

Соберите решение, чтобы убедиться, что ошибки не отображаются. Если возникнут ошибки, откройте **Configuration Manager** и убедитесь, что ваши проекты предназначены для той же платформе.

![Configuration manager](images/desktop-to-uwp/config-manager.png)

Рассмотрим некоторые действия, которые можно выполнять с проектами UWP и компонентами среды выполнения.

## <a name="show-a-modern-xaml-ui"></a>Отображение современного пользовательского интерфейса XAML

В рамках разработки вы можете внедрить в свое классическое приложение современные интерфейсы пользователя на основе XAML. Эти интерфейсы пользователя естественным образом адаптируются под различные размеры и разрешение экрана и поддерживают современные интерактивные модели, например сенсорный и рукописный ввод.

Например, с помощью небольшого количества разметки XAML вы можете предоставить пользователям мощные функции визуализации, связанные с картой.

На рисунке показано приложение Windows Forms, которое открывает современный пользовательский интерфейс на основе XAML, содержащий элементы управления картой.

![адаптивный макет](images/desktop-to-uwp/extend-xaml-ui.png)

>[!NOTE]
>Этот пример пользовательского интерфейса XAML путем добавления в решение проекта универсальной платформы Windows. Это стабильная способ отображения пользовательских интерфейсов XAML в настольном приложении. Вместо этого подхода — для добавления элементов управления универсальной платформы Windows XAML напрямую своего настольного приложения с помощью островам XAML. О-ва XAML в настоящее время доступны в виде developer preview. Несмотря на то, что мы рекомендуем вам опробовать их в собственном коде прототип сейчас, не рекомендуется использовать их в рабочем коде в данный момент. Эти API-интерфейсы и элементы управления будут продолжать с развитием и стабилизации в будущих выпусках Windows. Дополнительные сведения о XAML о-ва, см. в разделе [элементов управления универсальной платформы Windows в настольных приложениях](xaml-islands.md)

### <a name="the-design-pattern"></a>Шаблон проектирования

Для отображения пользовательского интерфейса на основе XAML выполните следующие действия.

: один: [Настройка решения](#solution-setup)

: два: [Создание пользовательского интерфейса XAML](#xaml-UI)

: три: [Добавьте расширение протокола в проект универсальной платформы Windows](#add-a-protocol-extension)

: четыре: [Запустите приложение универсальной платформы Windows из настольных приложений](#start)

: пять: [В проекте UWP отображение страницы, вы должны](#parse)

<a id="solution-setup" />

### <a name="setup-your-solution"></a>Настройка решения

Общие рекомендации по настройке решения см. в разделе [Настройка решения](#setup) в начале данного руководства.

Решение будет выглядеть следующим образом.

![Решение пользовательского интерфейса на XAML](images/desktop-to-uwp/xaml-ui-solution.png)

В этом примере проект Windows Forms называется **Landmarks**, а проект UWP, который содержит пользовательский интерфейс на XAML, называется **MapUI**.

<a id="xaml-UI" />

### <a name="create-a-xaml-ui"></a>Создание пользовательского интерфейса на XAML

Добавьте пользовательский интерфейс на XAML в свой проект UWP Вот код XAML для простой карты.

```xml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" Margin="12,20,12,14">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto"/>
        <ColumnDefinition Width="*"/>
    </Grid.ColumnDefinitions>
    <maps:MapControl x:Name="myMap" Grid.Column="0" Width="500" Height="500"
                     ZoomLevel="{Binding ElementName=zoomSlider,Path=Value, Mode=TwoWay}"
                     Heading="{Binding ElementName=headingSlider,Path=Value, Mode=TwoWay}"
                     DesiredPitch="{Binding ElementName=desiredPitchSlider,Path=Value, Mode=TwoWay}"
                     HorizontalAlignment="Left"
                     MapServiceToken="<Your Key Goes Here" />
    <Grid Grid.Column="1" Margin="12">
        <StackPanel>
            <Slider Minimum="1" Maximum="20" Header="ZoomLevel" Name="zoomSlider" Value="17.5"/>
            <Slider Minimum="0" Maximum="360" Header="Heading" Name="headingSlider" Value="0"/>
            <Slider Minimum="0" Maximum="64" Header=" DesiredPitch" Name="desiredPitchSlider" Value="32"/>
        </StackPanel>
    </Grid>
</Grid>
```

### <a name="add-a-protocol-extension"></a>Добавление расширение протокола

В **обозревателе решений**откройте **package.appxmanifest** упаковка проекта в решении и добавления этого расширения.

```xml
<Extensions>
  <uap:Extension Category="windows.protocol" Executable="MapUI.exe" EntryPoint="MapUI.App">
    <uap:Protocol Name="xamluidemo" />
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

```csharp

private void Statue_Of_Liberty_Click(object sender, EventArgs e)
{
    ShowMap(40.689247, -74.044502);
}

private async void ShowMap(double lat, double lon)
{
    string str = "xamluidemo://";

    Uri uri = new Uri(str + "location?lat=" +
        lat.ToString() + "&?lon=" + lon.ToString());

    var success = await Windows.System.Launcher.LaunchUriAsync(uri);

}
```

<a id="parse" />

### <a name="parse-parameters-and-show-a-page"></a>Анализ параметров и показ страницы

В классе **App** своего проекта UWP переопределите обработчик событий **OnActivated**. Если приложение активируется вашим протоколом, выполните анализ параметров, а затем откройте требуемую страницу.

```csharp
protected override void OnActivated(Windows.ApplicationModel.Activation.IActivatedEventArgs e)
{
    if (e.Kind == ActivationKind.Protocol)
    {
        ProtocolActivatedEventArgs protocolArgs = (ProtocolActivatedEventArgs)e;
        Uri uri = protocolArgs.Uri;
        if (uri.Scheme == "xamluidemo")
        {
            Frame rootFrame = new Frame();
            Window.Current.Content = rootFrame;
            rootFrame.Navigate(typeof(MainPage), uri.Query);
            Window.Current.Activate();
        }
    }
}
```

В коде XAML страницы, переопределить ``OnNavigatedTo`` передан метод использовать параметры на странице. В этом случае мы будем использовать широту и долготу, переданные на эту страницу, чтобы показать местоположение на карте.

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
 {
     if (e.Parameter != null)
     {
         WwwFormUrlDecoder decoder = new WwwFormUrlDecoder(e.Parameter.ToString());

         double lat = Convert.ToDouble(decoder[0].Value);
         double lon = Convert.ToDouble(decoder[1].Value);

         BasicGeoposition pos = new BasicGeoposition();

         pos.Latitude = lat;
         pos.Longitude = lon;

         myMap.Center = new Geopoint(pos);

         myMap.Style = MapStyle.Aerial3D;

     }

     base.OnNavigatedTo(e);
 }
```

## <a name="making-your-desktop-application-a-share-target"></a>Присвоение классическому приложению роли получателя данных

Вы можете сделать свое классическое приложение получателем данных, чтобы пользователи могли легко передавать ему данные из других приложений, поддерживающих общий доступ, например изображения.

Например пользователям выбрать приложения для совместного использования изображений из браузера Microsoft Edge, приложение "фотографии". Ниже приведен пример приложения WPF, поддерживает такую возможность.

![получатель данных](images/desktop-to-uwp/share-target.png).

Полный пример см. в разделе [здесь](https://github.com/Microsoft/Windows-Packaging-Samples/tree/master/ShareTarget)

### <a name="the-design-pattern"></a>Шаблон проектирования

Чтобы сделать ваше приложение получателем данных, выполните следующие действия.

: один: [Добавьте расширение целевого общего доступа](#share-extension)

: два: [Переопределите обработчик событий OnShareTargetActivated](#override)

: три: [Добавьте в проект UWP расширения рабочего стола](#desktop-extensions)

: четыре: [Добавьте расширение процесс полного доверия](#full-trust)

: пять: [Изменить приложение рабочего стола, чтобы получить общий файл](#modify-desktop)

<a id="share-extension" />

Следующие действия  

### <a name="add-a-share-target-extension"></a>Добавление расширения получателя данных

В **обозревателе решений**откройте **package.appxmanifest** файл пакета проекта в решении и добавьте расширение целевого общего ресурса.

```xml
<Extensions>
      <uap:Extension
          Category="windows.shareTarget"
          Executable="ShareTarget.exe"
          EntryPoint="App">
        <uap:ShareTarget>
          <uap:SupportedFileTypes>
            <uap:SupportsAnyFileType />
          </uap:SupportedFileTypes>
          <uap:DataFormat>Bitmap</uap:DataFormat>
        </uap:ShareTarget>
      </uap:Extension>
</Extensions>  
```

Укажите имя исполняемого файла, созданного проектом UWP, и имя класса точки входа. Эта разметка предполагает, что имя исполняемого файла для приложения UWP `ShareTarget.exe`.

Также необходимо указать типы файлов, которые могут быть переданы вашему приложению. В этом примере мы выполняем [WPF PhotoStoreDemo](https://github.com/Microsoft/WPF-Samples/tree/master/Sample%20Applications/PhotoStoreDemo) классического приложения, общего целевого объекта для растровых изображений, мы указываем `Bitmap` для поддерживаемый тип файлов.

<a id="override" />

### <a name="override-the-onsharetargetactivated-event-handler"></a>Переопределите обработчик событий OnShareTargetActivated

Переопределить **OnShareTargetActivated** обработчик событий в **приложения** класс проекта универсальной платформы Windows.

Этот обработчик событий вызывается, когда пользователи выбирают ваше приложение в качестве получателя файлов.

```csharp

protected override void OnShareTargetActivated(ShareTargetActivatedEventArgs args)
{
    shareWithDesktopApplication(args.ShareOperation);
}

private async void shareWithDesktopApplication(ShareOperation shareOperation)
{
    if (shareOperation.Data.Contains(StandardDataFormats.StorageItems))
    {
        var items = await shareOperation.Data.GetStorageItemsAsync();
        StorageFile file = items[0] as StorageFile;
        IRandomAccessStreamWithContentType stream = await file.OpenReadAsync();

        await file.CopyAsync(ApplicationData.Current.LocalFolder);
            shareOperation.ReportCompleted();

        await FullTrustProcessLauncher.LaunchFullTrustProcessForCurrentAppAsync();
    }
}
```

В этом коде мы сохраняем изображения, которое используется совместно с пользователем в папку локального хранилища приложения. Позже мы изменим классического приложения для получения образов из этой папки. Классическое приложение можно сделать, так как он включен в том же пакете, что и приложение универсальной платформы Windows.

<a id="desktop-extensions" />

### <a name="add-desktop-extensions-to-the-uwp-project"></a>Добавьте в проект UWP расширения рабочего стола

Добавить **расширения рабочего стола Windows для UWP** расширения в проект приложения универсальной платформы Windows.

![Расширение рабочего стола](images/desktop-to-uwp/desktop-extensions.png)

<a id="full-trust" />

### <a name="add-the-full-trust-process-extension"></a>Добавьте расширение процесс полного доверия

В **обозревателе решений**откройте **package.appxmanifest** упаковка проекта в решении, а затем добавьте расширение процесс полного доверия, рядом с расширением целевой общей папки, добавьте это ранее для файла.

```xml
<Extensions>
  ...
      <desktop:Extension Category="windows.fullTrustProcess" Executable="PhotoStoreDemo\PhotoStoreDemo.exe" />
  ...
</Extensions>  
```

Это расширение будет включить запуск классического приложения, к которому вы хотите совместное использование файла приложения универсальной платформы Windows. В примере, мы называем к исполняемому файлу [WPF PhotoStoreDemo](https://github.com/Microsoft/WPF-Samples/tree/master/Sample%20Applications/PhotoStoreDemo) настольного приложения.

<a id="modify-desktop" />

### <a name="modify-the-desktop-application-to-get-the-shared-file"></a>Изменить приложение рабочего стола, чтобы получить общий файл

Изменение приложения для настольных компьютеров, поиск и обработку общий файл. В этом примере приложения UWP хранятся общий файл в папке данных локального приложения. Таким образом, мы изменим [WPF PhotoStoreDemo](https://github.com/Microsoft/WPF-Samples/tree/master/Sample%20Applications/PhotoStoreDemo) классическое приложение для фотографий по запросу из этой папки.

```csharp
Photos.Path = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
```

Для экземпляров настольного приложения, которые уже откройте пользователем, мы может также обрабатывать [FileSystemWatcher](https://docs.microsoft.com/dotnet/api/system.io.filesystemwatcher?view=netframework-4.7.2) событий и передайте в него путь к расположению файлов. Таким образом все открытые экземпляры настольного приложения будет отображаться общий фотографии.

```csharp
...

   FileSystemWatcher watcher = new FileSystemWatcher(Photos.Path);

...

private void Watcher_Created(object sender, FileSystemEventArgs e)
{
    // new file got created, adding it to the list
    Dispatcher.BeginInvoke(System.Windows.Threading.DispatcherPriority.Normal, new Action(() =>
    {
        if (File.Exists(e.FullPath))
        {
            ImageFile item = new ImageFile(e.FullPath);
            Photos.Insert(0, item);
            PhotoListBox.SelectedIndex = 0;
            CurrentPhoto.Source = (BitmapSource)item.Image;
        }
    }));
}

```

## <a name="create-a-background-task"></a>Создание фоновой задачи

Добавьте фоновую задачу для выполнения кода, даже если приложение приостановлено. Фоновые задачи отлично подходят для небольших задач, не требующих участия пользователя. Например, ваша задача может скачивать почту, показывать всплывающее уведомление о входящем сообщении в чате или реагировать на изменение системного условия.

Ниже приведен пример приложения WPF, которое регистрирует в фоновом режиме.

![фоновая задача](images/desktop-to-uwp/sample-background-task.png)

Эта задача создает запрос http и измеряет время, необходимое, чтобы запрос вернул отклик. Ваши задачи наверняка будут более интересными, но это отличный пример для изучения базового механизма фоновой задачи.

Полный пример см. в разделе [здесь](https://github.com/Microsoft/Windows-Packaging-Samples/tree/master/BGTask).

### <a name="the-design-pattern"></a>Шаблон проектирования

Чтобы создать фоновую службу, выполните следующие действия:

: один: [Реализуйте фоновой задачи](#implement-task)

: два: [Настройка фоновой задачи](#configure-background-task)

: три: [Зарегистрировать фоновую задачу](#register-background-task)

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

Откройте в конструкторе манифеста **package.appxmanifest** упаковка проекта в решении.

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

**Найдите ответы на ваши вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Отправить отзыв или предложения по функциям**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)
