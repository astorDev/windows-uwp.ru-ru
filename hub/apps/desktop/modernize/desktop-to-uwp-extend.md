---
Description: Расширение классических приложений с помощью компонентов и элементов пользовательского интерфейса Windows
title: Расширение классических приложений с помощью компонентов и элементов пользовательского интерфейса Windows
ms.date: 06/08/2018
ms.topic: article
keywords: windows 10, uwp
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: 2f1cc0e932a1dd413ed94e1fe5d9a3d1e6b0870c
ms.sourcegitcommit: d38e2f31c47434cd6dbbf8fe8d01c20b98fabf02
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70393578"
---
# <a name="extend-your-desktop-app-with-modern-uwp-components"></a>Расширение классического приложения с помощью современных компонентов UWP

Некоторые возможности Windows 10, такие как страницы с подходящим для сенсорного ввода интерфейсом, должны выполняться внутри современного контейнера приложения. Если вы хотите добавить эти возможности, расширьте классическое приложение с помощью проектов UWP и среда выполнения Windows компонентов.

Во многих случаях можно вызывать интерфейсы API среда выполнения Windows напрямую из приложения для настольных систем, поэтому перед изучением этого руководством см. статью [улучшение для Windows 10](desktop-to-uwp-enhance.md).

> [!NOTE]
> Для использования функций, описанных в этой статье, необходимо создать пакет приложения Windows для классического приложения. Если вы еще не сделали этого, см. раздел [Упаковка классических приложений](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root).

Если вы готовы, давайте начнем.

<a id="setup" />

## <a name="first-setup-your-solution"></a>Сначала настройте свое решение.

Добавьте один или несколько проектов UWP и компонентов среды выполнения в свое решение.

Начните с решения, которое содержит **проект упаковки приложения для Windows** со ссылкой на классическое приложение.

На этом рисунке показан пример решения.

![Расширение начального проекта](images/desktop-to-uwp/extend-start-project.png)

Если решение не содержит проект упаковки, см. раздел [Упаковка приложения для настольных систем с помощью Visual Studio](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net).

### <a name="configure-the-desktop-application"></a>Настройка классического приложения

Убедитесь, что классическое приложение содержит ссылки на файлы, необходимые для вызова среда выполнения Windows API.

Для этого см. раздел [Настройка проекта](desktop-to-uwp-enhance.md#set-up-your-project) .

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

### <a name="optional-add-a-windows-runtime-component"></a>Используемых Добавление компонента среда выполнения Windows

Для выполнения некоторых сценариев необходимо добавить код в компонент среда выполнения Windows.

![служба приложения компонента среды выполнения](images/desktop-to-uwp/add-runtime-component.png)

Затем в проекте UWP добавьте ссылку на компонент среды выполнения. Решение будет выглядеть следующим образом.

![Ссылка на компонент среды выполнения](images/desktop-to-uwp/runtime-component-reference.png)

### <a name="build-your-solution"></a>Создание решения

Выполните сборку решения, чтобы убедиться, что ошибки не отображаются. При возникновении ошибок откройте **Configuration Manager** и убедитесь, что проекты предназначены для той же платформы.

![Диспетчер конфигурации](images/desktop-to-uwp/config-manager.png)

Рассмотрим некоторые действия, которые можно выполнять с проектами UWP и компонентами среды выполнения.

## <a name="show-a-modern-xaml-ui"></a>Отображение современного пользовательского интерфейса XAML

В рамках разработки вы можете внедрить в свое классическое приложение современные интерфейсы пользователя на основе XAML. Эти интерфейсы пользователя естественным образом адаптируются под различные размеры и разрешение экрана и поддерживают современные интерактивные модели, например сенсорный и рукописный ввод.

Например, с помощью небольшого количества разметки XAML вы можете предоставить пользователям мощные функции визуализации, связанные с картой.

На рисунке показано приложение Windows Forms, которое открывает современный пользовательский интерфейс на основе XAML, содержащий элементы управления картой.

![адаптивный макет](images/desktop-to-uwp/extend-xaml-ui.png)

>[!NOTE]
>В этом примере показан пользовательский интерфейс XAML путем добавления проекта UWP в решение. Это стабильный поддерживаемый подход к отображению пользовательских интерфейсов XAML в классическом приложении. Альтернативой этому подходу является добавление элементов управления типа UWP XAML непосредственно в классическое приложение с помощью кода XAML. В настоящее время в качестве предварительной версии для разработчиков доступны все острова XAML. Несмотря на то что вы можете опробовать их в своем собственном прототипном коде сейчас, мы не рекомендуем использовать их в производственном коде в настоящее время. В будущих выпусках Windows эти API и элементы управления будут продолжать работать и стабилизироваться. Дополнительные сведения о островах XAML см. [в статье элементы управления UWP в классических приложениях](xaml-islands.md) .

### <a name="the-design-pattern"></a>Шаблон проектирования

Для отображения пользовательского интерфейса на основе XAML выполните следующие действия.

:one: [Настройка решения](#solution-setup)

:two: [Создание пользовательского интерфейса XAML](#xaml-UI)

:three: [Добавление расширения протокола в проект UWP](#add-a-protocol-extension)

:four: [Запуск приложения UWP из классического приложения](#start)

:five: [В проекте UWP отобразите нужную страницу.](#parse)

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

В **Обозреватель решений**откройте файл **Package. appxmanifest** проекта упаковки в решении и добавьте это расширение.

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

Во-первых, из классического приложения создайте [Uri](https://docs.microsoft.com/dotnet/api/system.uri?redirectedfrom=MSDN), включающий имя протокола и все параметры, которые необходимо передать в приложение UWP. Затем вызовите метод [LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync).

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

В коде, расположенном на странице XAML, переопределите ``OnNavigatedTo`` метод для использования параметров, переданных на страницу. В этом случае мы будем использовать широту и долготу, переданные на эту страницу, чтобы показать местоположение на карте.

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

Например, пользователи могут выбрать ваше приложение для совместного использования изображений Microsoft погранично, приложения Photos. Вот пример приложения WPF с такой возможностью.

![получатель данных](images/desktop-to-uwp/share-target.png).

Полный пример см. [здесь](https://github.com/Microsoft/Windows-Packaging-Samples/tree/master/ShareTarget)

### <a name="the-design-pattern"></a>Шаблон проектирования

Чтобы сделать ваше приложение получателем данных, выполните следующие действия.

:one: [Добавление расширения общего доступа](#share-extension)

:two: [Переопределение обработчика событий Оншаретаржетактиватед](#override)

:three: [Добавление расширений рабочего стола в проект UWP](#desktop-extensions)

:four: [Добавление расширения процесса полного доверия](#full-trust)

:five: [Изменение классического приложения для получения общего файла](#modify-desktop)

<a id="share-extension" />

Следующие шаги  

### <a name="add-a-share-target-extension"></a>Добавление расширения получателя данных

В **Обозреватель решений**откройте файл **Package. appxmanifest** проекта упаковки в решении и добавьте расширение целевого объекта Share.

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

Укажите имя исполняемого файла, созданного проектом UWP, и имя класса точки входа. В этой разметке предполагается, что имя исполняемого файла для `ShareTarget.exe`приложения UWP —.

Также необходимо указать типы файлов, которые могут быть переданы вашему приложению. В этом примере мы делаем для классического приложения [WPF фотосторедемо](https://github.com/Microsoft/WPF-Samples/tree/master/Sample%20Applications/PhotoStoreDemo) общий ресурс для точечных рисунков, поэтому мы `Bitmap` указываем для поддерживаемого типа файлов.

<a id="override" />

### <a name="override-the-onsharetargetactivated-event-handler"></a>Переопределение обработчика событий Оншаретаржетактиватед

Переопределите обработчик событий **оншаретаржетактиватед** в классе **app** проекта UWP.

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

В этом коде мы сохраняем изображение, совместно используемое пользователем, в папку с локальным хранилищем приложений. Позже мы изменим классическое приложение для извлечения изображений из этой же папки. Классическое приложение может сделать это, так как оно включено в тот же пакет, что и приложение UWP.

<a id="desktop-extensions" />

### <a name="add-desktop-extensions-to-the-uwp-project"></a>Добавление расширений рабочего стола в проект UWP

Добавьте **расширения классических приложений Windows для расширения UWP** в проект приложения UWP.

![расширение Desktop](images/desktop-to-uwp/desktop-extensions.png)

<a id="full-trust" />

### <a name="add-the-full-trust-process-extension"></a>Добавление расширения процесса полного доверия

В **Обозреватель решений**откройте файл **Package. appxmanifest** проекта упаковки в решении, а затем добавьте расширение процесса полного доверия рядом с расширением целевого объекта Share, который вы добавили ранее.

```xml
<Extensions>
  ...
      <desktop:Extension Category="windows.fullTrustProcess" Executable="PhotoStoreDemo\PhotoStoreDemo.exe" />
  ...
</Extensions>  
```

Это расширение позволит приложению UWP запустить классическое приложение, к которому требуется предоставить общий доступ к файлу. В качестве примера мы будем называть исполняемый объект приложения [WPF фотосторедемо](https://github.com/Microsoft/WPF-Samples/tree/master/Sample%20Applications/PhotoStoreDemo) Desktop.

<a id="modify-desktop" />

### <a name="modify-the-desktop-application-to-get-the-shared-file"></a>Изменение классического приложения для получения общего файла

Измените настольное приложение, чтобы найти и обработать общий файл. В этом примере приложение UWP сохранило общий файл в папке Local App Data. Поэтому мы изменим приложение [WPF фотосторедемо](https://github.com/Microsoft/WPF-Samples/tree/master/Sample%20Applications/PhotoStoreDemo) Desktop для извлечения фотографий из этой папки.

```csharp
Photos.Path = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
```

Для экземпляров классических приложений, которые уже открыты пользователем, можно также обрабатывать событие [FileSystemWatcher](https://docs.microsoft.com/dotnet/api/system.io.filesystemwatcher?view=netframework-4.7.2) и передавать путь к расположению файла. Таким образом, все открытые экземпляры настольного приложения будут показывать общую фотографию.

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

Ниже приведен пример приложения WPF, которое регистрирует фоновую задачу.

![фоновая задача](images/desktop-to-uwp/sample-background-task.png)

Эта задача создает запрос http и измеряет время, необходимое, чтобы запрос вернул отклик. Ваши задачи наверняка будут более интересными, но это отличный пример для изучения базового механизма фоновой задачи.

Полный пример см. [здесь](https://github.com/Microsoft/Windows-Packaging-Samples/tree/master/BGTask).

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

В конструкторе манифеста откройте файл **Package. appxmanifest** проекта упаковки в решении.

На вкладке **Объявления** добавьте объявление **Фоновые задачи**.

![Вариант фоновой задачи](images/desktop-to-uwp/background-task-option.png)

Затем выберите требуемые свойства. В нашем примере используется свойство **Timer**.

![Свойство Timer](images/desktop-to-uwp/timer-property.png)

Укажите полное имя класса в компоненте среда выполнения Windows, который реализует фоновую задачу.

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

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Отправьте отзыв или получите предложения по функциям**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)
