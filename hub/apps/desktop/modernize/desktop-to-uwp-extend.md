---
Description: Расширение классических приложений с помощью компонентов и элементов пользовательского интерфейса Windows
title: Расширение приложения с помощью компонентов и элементов пользовательского интерфейса Windows
ms.date: 06/08/2018
ms.topic: article
keywords: windows 10, uwp
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: a38f5fa7f3ef99f5970ec5d476fb65761aa39db4
ms.sourcegitcommit: cc108c791842789464c38a10e5d596c9bd878871
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2019
ms.locfileid: "75302588"
---
# <a name="extend-your-desktop-app-with-modern-uwp-components"></a>Расширение классического приложения с помощью современных компонентов UWP

Некоторые возможности Windows 10, такие как страницы с подходящим для сенсорного ввода интерфейсом, должны выполняться внутри современного контейнера приложения. Если вы хотите добавить эти возможности, расширьте свое классическое приложение, используя проекты UWP и компоненты среды выполнения Windows.

Во многих случаях можно вызывать API среды выполнения Windows непосредственно из классического приложения, поэтому перед прочтением этого руководства просмотрите раздел [Расширение для Windows 10](desktop-to-uwp-enhance.md).

> [!NOTE]
> Для функций, описанных в этой статье, требуется получить [идентификатор пакета](modernize-packaged-apps.md) для приложения путем [упаковки классического приложения в пакет MSIX](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root) или [предоставления идентификатора для приложения, используя разреженный пакет](grant-identity-to-nonpackaged-apps.md).

Если вы готовы, давайте начнем.

<a id="setup" />

## <a name="first-setup-your-solution"></a>Сначала настройте свое решение.

Добавьте в решение один или несколько проектов UWP и компонентов среды выполнения.

Начните с решения, которое содержит **проект упаковки приложений Windows** со ссылкой на классическое приложение.

На этом рисунке показан пример решения.

![Расширение начального проекта](images/desktop-to-uwp/extend-start-project.png)

Если ваше решение не содержит проект упаковки, см. руководство по [упаковке классического приложения с помощью Visual Studio](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net).

### <a name="configure-the-desktop-application"></a>Настройка классического приложения

Убедитесь, что в классическом приложении указаны ссылки на все файлы, необходимые для вызова API среды выполнения Windows.

Для этого выполните инструкции по [настройке проекта](desktop-to-uwp-enhance.md#set-up-your-project).

### <a name="add-a-uwp-project"></a>Добавление проекта UWP

Добавьте в решение проект **Пустое приложение (универсальная платформа Windows)** .

Там вы сможете создать современный пользовательский интерфейс XAML или использовать API, которые выполняются только в процессе UWP.

![Проект UWP](images/desktop-to-uwp/add-uwp-project-to-solution.png)

В проекте упаковки щелкните правой кнопкой узел **Приложения** и выберите команду **Добавить ссылку**.

![Ссылка на проект UWP](images/desktop-to-uwp/add-uwp-project-reference.png)

Затем добавьте ссылку на проект UWP.

![Ссылка на проект UWP](images/desktop-to-uwp/choose-uwp-project.png)

Решение будет выглядеть следующим образом:

![Решение с проектом UWP](images/desktop-to-uwp/uwp-project-reference.png)

### <a name="optional-add-a-windows-runtime-component"></a>(Необязательно) Создайте компонент среды выполнения Windows

Для некоторых сценариев потребуется добавить код в компонент среды выполнения Windows.

![Служба приложений компонента среды выполнения](images/desktop-to-uwp/add-runtime-component.png)

Затем в проекте UWP добавьте ссылку на компонент среды выполнения. Решение будет выглядеть следующим образом:

![Ссылка на компонент среды выполнения](images/desktop-to-uwp/runtime-component-reference.png)

### <a name="build-your-solution"></a>Сборка решения

Выполните сборку решения и убедитесь, что ошибок нет. При возникновении ошибок откройте **Диспетчер конфигурации** и убедитесь, что проекты предназначены для той же платформы.

![Диспетчер конфигурации](images/desktop-to-uwp/config-manager.png)

Рассмотрим некоторые действия, которые можно выполнять с проектами UWP и компонентами среды выполнения.

## <a name="show-a-modern-xaml-ui"></a>Отображение современного пользовательского интерфейса XAML

В потоке разработки можно внедрять в классическое приложение современные интерфейсы пользователя на основе XAML. Эти интерфейсы пользователя естественным образом адаптируются под различные размеры и разрешение экрана и поддерживают современные интерактивные модели, в том числе сенсорный и рукописный ввод.

Например, с помощью небольшого количества разметки XAML вы можете предоставить пользователям мощные функции визуализации, связанные с картой.

На рисунке показано приложение Windows Forms, которое открывает современный пользовательский интерфейс на основе XAML, содержащий элементы управления картой.

![Адаптивный макет](images/desktop-to-uwp/extend-xaml-ui.png)

>[!NOTE]
>В этом примере показан пользовательский интерфейс XAML, полученный добавлением проекта UWP в решение. Это стабильный поддерживаемый подход к отображению пользовательских интерфейсов XAML в классическом приложении. Альтернативный вариант — добавление элементов управления XAML UWP непосредственно в классическое приложение с помощью XAML Islands. XAML Islands сейчас предоставляется в предварительной версии для разработчиков. Несмотря на то что вы можете опробовать их в своем собственном прототипном коде сейчас, мы не рекомендуем использовать их в производственном коде в настоящее время. В будущих выпусках Windows эти API и элементы управления будут дорабатываться и совершенствоваться. См. сведения о XAML Islands в руководстве по [элементам управления UWP в классических приложениях](xaml-islands.md).

### <a name="the-design-pattern"></a>Шаблон проектирования

Для отображения пользовательского интерфейса на основе XAML выполните следующие действия:

:one: [Настройка решения](#solution-setup)

:two: [Создание пользовательского интерфейса XAML](#xaml-UI)

:three: [Добавление расширения протокола в проект UWP](#add-a-protocol-extension)

:four: [Запуск приложения UWP из классического приложения](#start)

:five: [Отображение нужной страницы в проекте UWP](#parse)

<a id="solution-setup" />

### <a name="setup-your-solution"></a>Настройка решения

См. инструкции по [настройке решения](#setup) в начале этого руководства.

Сейчас ваше решение выглядит примерно так:

![Решение пользовательского интерфейса XAML](images/desktop-to-uwp/xaml-ui-solution.png)

В этом примере проект Windows Forms называется **Landmarks**, а проект UWP, который содержит пользовательский интерфейс XAML, называется **MapUI**.

<a id="xaml-UI" />

### <a name="create-a-xaml-ui"></a>Создание пользовательского интерфейса XAML

Добавьте пользовательский интерфейс XAML в проект UWP Вот код XAML для простой карты.

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

### <a name="add-a-protocol-extension"></a>Добавление расширения протокола

В **обозревателе решений** откройте файл **package.appxmanifest** проекта упаковки из нужного решения и добавьте это расширение.

```xml
<Extensions>
  <uap:Extension Category="windows.protocol" Executable="MapUI.exe" EntryPoint="MapUI.App">
    <uap:Protocol Name="xamluidemo" />
  </uap:Extension>
</Extensions>
```

Задайте имя протокола, введите имя исполняемого файла, созданного проектом UWP, и имя класса для точки входа.

Также можно открыть **package.appxmanifest** в конструкторе, выбрать вкладку **Объявления** и затем добавить сюда расширение.

![Вкладка объявлений](images/desktop-to-uwp/protocol-properties.png)

> [!NOTE]
> Элементы управления картой скачивают данные из Интернета, следовательно, если вы используете такой элемент, необходимо также добавить в манифест возможность internet client.

<a id="start" />

### <a name="start-the-uwp-app"></a>Запуск приложения UWP

Во-первых, из классического приложения создайте [URI](https://docs.microsoft.com/dotnet/api/system.uri), включающий имя протокола и все параметры, которые необходимо передать в приложение UWP. Затем вызовите метод [LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync).

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

### <a name="parse-parameters-and-show-a-page"></a>Анализ параметров и отображение страницы

В классе **App** проекта UWP переопределите обработчик событий **OnActivated**. Если приложение активируется вашим протоколом, выполните анализ параметров и откройте требуемую страницу.

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

В коде, который создает эту страницу XAML, переопределите метод ``OnNavigatedTo`` для использования параметров, переданных на страницу. В нашем примере мы будем использовать данные широты и долготы, переданные на страницу, чтобы показать расположение на карте.

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

Вы можете назначить классическое приложение получателем данных, чтобы пользователи могли легко передавать ему данные из других приложений с поддержкой передачи данных, например изображений.

Пользователи смогут выбрать ваше приложение, чтобы передать изображения из Microsoft Edge или приложения "Фотографии". Вот пример приложения WPF, которое поддерживает такую возможность.

![Получатель данных](images/desktop-to-uwp/share-target.png).

Полный пример см. [здесь](https://github.com/Microsoft/Windows-Packaging-Samples/tree/master/ShareTarget).

### <a name="the-design-pattern"></a>Шаблон проектирования

Чтобы сделать ваше приложение получателем данных, выполните следующие действия:

:one: [Добавление расширения получателя данных](#share-extension)

:two: [Переопределение обработчика событий OnShareTargetActivated](#override)

:three: [Добавление расширения классического приложения в проект UWP](#desktop-extensions)

:four: [Добавление расширения процесса полного доверия](#full-trust)

:five: [Изменение классического приложения для получения переданного файла](#modify-desktop)

<a id="share-extension" />

Дальнейшие шаги  

### <a name="add-a-share-target-extension"></a>Добавление расширения получателя данных

В **обозревателе решений** откройте файл **package.appxmanifest** проекта упаковки из нужного решения и добавьте расширение получателя данных.

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

Укажите имя исполняемого файла, созданного проектом UWP, и имя класса точки входа. В этой разметке предполагается, что исполняемый файл для приложения UWP называется `ShareTarget.exe`.

Также необходимо указать типы файлов, которые могут быть переданы приложению. В нашем примере создается приложение [WPF PhotoStoreDemo](https://github.com/Microsoft/WPF-Samples/tree/master/Sample%20Applications/PhotoStoreDemo), которое выступает в роли получателя растровых изображений, поэтому мы определим `Bitmap` для поддерживаемого типа файлов.

<a id="override" />

### <a name="override-the-onsharetargetactivated-event-handler"></a>Переопределение обработчика событий OnShareTargetActivated

Переопределите обработчик событий **OnShareTargetActivated** в классе **App** проекта UWP.

Этот обработчик событий вызывается, когда пользователи выбирают приложение в качестве получателя файлов.

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

В этом коде мы сохраняем переданное пользователем изображение в папку локального хранилища приложений. Позже мы изменим классическое приложение, чтобы оно извлекало изображения из этой папки. Классическое приложение может это сделать, так как включено в тот же пакет, что и приложение UWP.

<a id="desktop-extensions" />

### <a name="add-desktop-extensions-to-the-uwp-project"></a>Добавление расширения классического приложения в проект UWP

Добавьте **расширения рабочего стола Windows для UWP** в проект приложения UWP.

![Расширение для классических приложений](images/desktop-to-uwp/desktop-extensions.png)

<a id="full-trust" />

### <a name="add-the-full-trust-process-extension"></a>Добавление расширения процесса полного доверия

В **обозревателе решений** откройте файл **package.appxmanifest** проекта упаковки из нужного решения и добавьте расширение процесса полного доверия рядом с тем расширением получателя данных, которое вы уже добавили в этот файл.

```xml
<Extensions>
  ...
      <desktop:Extension Category="windows.fullTrustProcess" Executable="PhotoStoreDemo\PhotoStoreDemo.exe" />
  ...
</Extensions>  
```

Это расширение позволит приложению UWP запускать классическое приложение, которому передается файл. В нашем примере это исполняемый файл классического приложения [WPF PhotoStoreDemo](https://github.com/Microsoft/WPF-Samples/tree/master/Sample%20Applications/PhotoStoreDemo).

<a id="modify-desktop" />

### <a name="modify-the-desktop-application-to-get-the-shared-file"></a>Изменение классического приложения для получения переданного файла

Измените классическое приложение так, чтобы оно находило и обрабатывало переданный файл. В нашем примере приложение UWP сохранило переданный файл в локальной папке с данными приложения. Поэтому мы настроим в приложении [WPF PhotoStoreDemo](https://github.com/Microsoft/WPF-Samples/tree/master/Sample%20Applications/PhotoStoreDemo) получение фотографий из этой папки.

```csharp
Photos.Path = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
```

Для экземпляров классического приложения, которые уже открыты пользователем, можно также обрабатывать событие [FileSystemWatcher](https://docs.microsoft.com/dotnet/api/system.io.filesystemwatcher) и передавать путь к расположению файла. Это позволит отобразить переданную фотографию во всех открытых экземплярах классического приложения.

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

Вы можете добавить фоновую задачу для выполнения кода, даже если приложение приостановлено. Фоновые задачи отлично подходят для небольших задач, не требующих участия пользователя. Например, ваша задача может скачивать почту, показывать всплывающее уведомление о входящем сообщении в чате или реагировать на изменение системного условия.

Вот пример приложения WPF, которое регистрирует фоновую задачу.

![Фоновая задача](images/desktop-to-uwp/sample-background-task.png)

Эта задача создает запрос HTTP и измеряет время получения ответа на этот запрос. Ваши задачи наверняка будут более интересными, но это отличный пример для изучения базового механизма фоновой задачи.

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

В конструкторе манифестов откройте файл **package.appxmanifest** проекта упаковки в своем решении.

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

## <a name="find-answers-to-your-questions"></a>Получение ответов на вопросы

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).
