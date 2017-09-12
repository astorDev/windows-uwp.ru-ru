---
author: normesta
Description: "Расширение классических приложений с помощью компонентов и элементов пользовательского интерфейса Windows"
Search.Product: eADQiWindows 10XVcnh
title: "Расширение классических приложений с помощью компонентов и элементов пользовательского интерфейса Windows"
ms.author: normesta
ms.date: 07/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: fce6076f07957b50e83cf80e8d12350630b99456
ms.sourcegitcommit: ba0d20f6fad75ce98c25ceead78aab6661250571
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2017
---
# <a name="extend-your-desktop-application-with-modern-uwp-components"></a>Расширение классических приложений с помощью современных компонентов UWP

Некоторые возможности Windows 10, такие как страницы с подходящим для сенсорного ввода интерфейсом, должны выполняться внутри современного контейнера приложения. Если вы хотите добавить эти возможности, расширьте свое классическое приложение, используя компоненты UWP.

Во многих случаях можно вызывать API-интерфейсы UWP непосредственно из классического приложения, поэтому перед прочтением этого руководства просмотрите раздел [Расширение для Windows 10](desktop-to-uwp-enhance.md).

>[!NOTE]
>В этом руководстве предполагается, что вы создали пакет приложения Windows для своего классического приложения с помощью моста для классических приложений. Если вы еще не сделали этого, см. раздел [Мост для классических приложений](desktop-to-uwp-root.md).

Если вы готовы, давайте начнем.

## <a name="show-a-modern-xaml-ui"></a>Отображение современного пользовательского интерфейса XAML

В рамках разработки вы можете внедрить в свое классическое приложение современные интерфейсы пользователя на основе XAML. Эти интерфейсы пользователя естественным образом адаптируются под различные размеры и разрешение экрана и поддерживают современные интерактивные модели, например сенсорный и рукописный ввод.

Например, с помощью небольшого количества разметки XAML вы можете предоставить пользователям мощные функции визуализации, связанные с картой.

На рисунке показано приложение VB6, которое открывает современный пользовательский интерфейс на основе XAML, содержащий элементы управления картой.

![адаптивный макет](images\desktop-to-uwp\extend-xaml-ui.png)

### <a name="have-a-closer-look-at-this-app"></a>Рассмотрим это приложение подробнее

:heavy_check_mark: [Посмотреть видео](https://mva.microsoft.com/en-US/training-courses/developers-guide-to-the-desktop-bridge-17373/Demo-Add-a-XAML-UI-and-Toast-Notification-to-a-VB6-Application-OsJHC7WhD_8006218965)

:heavy_check_mark: [Получить приложение](https://www.microsoft.com/en-us/store/p/vb6-app-with-xaml-sample/9n191ncxf2f6)

:heavy_check_mark: [Просмотреть код](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/VB6withXaml)

### <a name="the-design-pattern"></a>Шаблон проектирования

Для отображения пользовательского интерфейса на основе XAML выполните следующие действия.

:one: [Добавьте проект UWP в свое решение](#project)

:two: [Добавьте расширение протокола в этот проект](#protocol)

:three: [Запустите приложение UWP из классического приложения](#start)

:four: [В проекте UWP отобразите требуемую страницу](#parse)

<span id="project" />
### <a name="add-a-uwp-project"></a>Добавление проекта UWP

Добавьте проект **Пустое приложение (универсальная платформа Windows)** в свое решение.

<span id="protocol" />
### <a name="add-a-protocol-extension"></a>Добавление расширение протокола

В **Обозревателе решений** откройте файл **package.appxmanifest** этого проекта и добавьте расширение.

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

![вкладка объявления](images\desktop-to-uwp\protocol-properties.png)



> [!NOTE]
> Элементы управления картой скачивают данные из Интернета, поэтому, если вы используете такой элемент, необходимо также добавить возможность internet client в манифест.

<span id="start" />
### <a name="start-the-uwp-app"></a>Запуск приложения UWP

Во-первых, создайте [Uri](https://msdn.microsoft.com/library/system.uri.aspx), включающий имя протокола и все параметры, которые необходимо передать в приложение UWP. Затем вызовите метод [LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher#Windows_System_Launcher_LaunchUriAsync_Windows_Foundation_Uri_).

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

<span id="parse" />
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

![адаптивный макет](images\desktop-to-uwp\winforms-app-service.png)

### <a name="have-a-closer-look-at-this-app"></a>Рассмотрим это приложение подробнее

:heavy_check_mark: [Посмотреть видео](https://mva.microsoft.com/en-US/training-courses/developers-guide-to-the-desktop-bridge-17373/Demo-Expose-an-AppService-from-a-Windows-Forms-Data-Application-GiqNS7WhD_706218965)

:heavy_check_mark: [Получить приложение](https://www.microsoft.com/en-us/store/p/winforms-appservice/9p7d9b6nk5tn)

:heavy_check_mark: [Просмотреть код](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WinformsAppService)

### <a name="the-design-pattern"></a>Шаблон проектирования

Для предоставления службы выполните следующие действия.

:one: [Создайте компонент среды выполнения Windows](#component)

:two: [Добавьте расширение службы приложения](#extension)

:three: [Реализуйте службу приложения](#appservice)

:four: [Протестируйте службу приложения](#test)

<span id="component" />

### <a name="add-a-windows-runtime-component"></a>Добавление компонента среды выполнения Windows

Добавьте проект **Компонент среды выполнения Windows (универсальная платформа Windows)** в ваше решение.

Затем добавьте ссылку на проект этого компонента среды выполнения в свой проект упаковки UWP.

<span id="extension" />
### <a name="add-an-app-service-extension"></a>Добавление расширения службы приложения

В **Обозревателе решений** откройте файл **package.appxmanifest** вашего проекта упаковки и добавьте расширение службы приложения.

```xml
<Extensions>
      <uap:Extension
          Category="windows.appService"
          EntryPoint="MyAppService.AppServiceTask">
        <uap:AppService Name="com.microsoft.samples.winforms" />
      </uap:Extension>
    </Extensions>    
```

Задайте службе приложения имя и укажите имя класса точки входа. Это класс, который вы будете использовать для реализации службы приложения.

<span id="appservice" />
### <a name="implement-the-app-service"></a>Реализация службы приложения

Здесь вы будете проверять и обрабатывать запросы из других приложений.

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

<span id="test" />
### <a name="test-the-app-service"></a>Тестирование службы приложения

Протестируйте службу путем вызова из другого приложения.

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
        string result = "";
 
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

![получатель данных](images\desktop-to-uwp\share-target.png)

### <a name="have-a-closer-look-at-this-app"></a>Рассмотрим это приложение подробнее

:heavy_check_mark: [Посмотреть видео](https://mva.microsoft.com/en-US/training-courses/developers-guide-to-the-desktop-bridge-17373/Demo-Make-a-WPF-Application-a-Share-Target-xd6Fu6WhD_8406218965)

:heavy_check_mark: [Получить приложение](https://www.microsoft.com/en-us/store/p/wpf-app-as-sharetarget/9pjcjljlck37)

:heavy_check_mark: [Просмотреть код](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WPFasShareTarget)

### <a name="the-design-pattern"></a>Шаблон проектирования

Чтобы сделать ваше приложение получателем данных, выполните следующие действия.

:one: [Добавьте проект UWP в свое решение](#project2)

:two: [Добавьте расширение получателя данных](#share-extension)

:three: [Переопределите обработчик событий OnNavigatedTo](#override)

<span id="project2" />
### <a name="add-a-uwp-project-to-your-solution"></a>Добавление проекта UWP в решение

Добавьте проект **Пустое приложение (универсальная платформа Windows)** в свое решение.

<span id="share-extension" />
### <a name="add-a-share-target-extension"></a>Добавление расширения получателя данных

В **Обозревателе решений** откройте файл **package.appxmanifest** этого проекта и добавьте расширение.

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

<span id="override" />
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

## <a name="support-and-feedback"></a>Поддержка и отзывы

**Поиск ответов на конкретные вопросы**

Наша команда следит за этими [тегами StackOverflow](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge).

**Оставьте отзыв или предложите новые возможности для реализации**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)

**Оставьте отзыв об этой статье**

Используйте раздел комментариев ниже.
