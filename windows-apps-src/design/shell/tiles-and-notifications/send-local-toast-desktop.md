---
Description: Learn how Win32 C# apps can send local toast notifications and handle the user clicking the toast.
title: Отправка локального всплывающего уведомлений из классических приложений C#
ms.assetid: E9AB7156-A29E-4ED7-B286-DA4A6E683638
label: Send a local toast notification from desktop C# apps
template: detail.hbs
ms.date: 01/23/2018
ms.topic: article
keywords: windows 10, uwp, win32, настольный компьютер, всплывающие уведомления, отправка уведомления, отправка локального уведомления, мост для классических приложений, C#, c sharp
ms.localizationpriority: medium
ms.openlocfilehash: 2c32ba5928d3c272ef77a70790640346fb000762
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8217148"
---
# <a name="send-a-local-toast-notification-from-desktop-c-apps"></a>Отправка локального всплывающего уведомлений из классических приложений C#

Классические приложения (мост для классических приложений и классические приложения Win32) могут отправлять интерактивные всплывающие уведомления так же, как приложения универсальной платформы Windows (UWP). Однако существует несколько специальных действий для классических приложений, связанных с другими схемами активации и потенциальным отсутствием идентификатора пакета, если вы не используете мост для классических приложений.

> [!IMPORTANT]
> Если вы создаете приложение UWP, см. [документацию по UWP](send-local-toast.md). Сведения для других языков см. в разделе [C++ WRL для настольных компьютеров](send-local-toast-desktop-cpp-wrl.md).


## <a name="step-1-enable-the-windows-10-sdk"></a>Шаг 1. Включение Windows 10 SDK

Если вы не включили Windows 10 SDK для вашего приложения Win32, вам необходимо сделать это в первую очередь.

Щелкните проект правой кнопкой мыши и выберите пункт **Выгрузить проект**.

![Выгрузка проекта](images/win32-unload-project.png)

Затем снова щелкните проект правой кнопкой мыши и выберите **Изменить [имя_проекта].csproj**

![Изменение проекта](images/win32-edit-project.png)

Под существующим узлом `<TargetFrameworkVersion>` добавьте новый узел `<TargetPlatformVersion>`, указав дополнительную минимальную версию Windows 10. Фактически будет использоваться последний пакет SDK, установленной на компьютере разработки. Это просто указывает минимальную допустимую версию (и позволяет ссылаться на Windows SDK).

```xml
...
<TargetFrameworkVersion>...</TargetFrameworkVersion>
<TargetPlatformVersion>10.0.10240.0</TargetPlatformVersion>
...
```

Сохраните изменения и повторно загрузите проект.

![Повторная загрузка проекта](images/win32-reload-project.png)


## <a name="step-2-reference-the-apis"></a>Шаг 2. Ссылка на API-интерфейсы

Откройте диспетчер ссылок (щелкните проект правой кнопкой мыши и выберите **Добавить -> Ссылка**), выберите **Windows-> Core** и добавьте следующие ссылки:

* Windows.Data
* Windows.UI

![Диспетчеру ссылок](images/win32-add-windows-reference.png)


## <a name="step-3-copy-compat-library-code"></a>Шаг 3. Копирование кода библиотеки compat

Скопируйте [файл DesktopNotificationManagerCompat.cs из GitHub](https://raw.githubusercontent.com/WindowsNotifications/desktop-toasts/master/CS/DesktopToastsApp/DesktopNotificationManagerCompat.cs) в свой проект. Библиотека compat значительно упрощает уведомления для классических приложений. Для выполнения следующих инструкций требуется библиотека compat.


## <a name="step-4-implement-the-activator"></a>Шаг 4. Реализация активатора

Таким образом, когда пользователь щелкает всплывающее уведомление, ваше приложение может сделать что-то необходимо реализовать обработчик для активации всплывающего уведомления. Это необходимо, чтобы всплывающее уведомление сохранялось в центре уведомлений (так как всплывающее уведомление можно щелкнуть после закрытия вашего приложения). Этот класс можно разместить в любом месте в проекте.

Разверните класс **NotificationActivator**, добавьте три атрибута, приведенные ниже, и создайте уникальный идентификатор GUID CLSID для вашего приложения с помощью одного из множества веб-генераторов GUID. С помощью идентификатора CLSID (идентификатор класса) центр уведомлений определяет, какой класс активировать, используя COM.

```csharp
// The GUID CLSID must be unique to your app. Create a new GUID if copying this code.
[ClassInterface(ClassInterfaceType.None)]
[ComSourceInterfaces(typeof(INotificationActivationCallback))]
[Guid("replaced-with-your-guid-C173E6ADF0C3"), ComVisible(true)]
public class MyNotificationActivator : NotificationActivator
{
    public override void OnActivated(string invokedArgs, NotificationUserInput userInput, string appUserModelId)
    {
        // TODO: Handle activation
    }
}
```


## <a name="step-5-register-with-notification-platform"></a>Шаг 5. Регистрация на платформе уведомлений

Затем вам необходимо зарегистрироваться на платформе уведомлений. Требуется выполнить различные действия, которые зависят от того, используете ли вы мост для классических приложений или Win32. Если вы поддерживаете оба варианта, необходимо выполнить оба шага (тем не менее нет необходимости в разветвлении кода, так как наша библиотека делает это автоматически).


### <a name="desktop-bridge"></a>Мост для классических приложений

Если вы используете мост для классических приложений (или поддерживаете оба варианта) в файл **Package.appxmanifest** добавьте:

1. Объявление для **xmlns:com**
2. Объявление для **xmlns:desktop**
3. В атрибуте **IgnorableNamespaces** : **com** и **desktop**
4. **com:Extension** для COM-активатора с использованием GUID из шага 4. Не забудьте добавить `Arguments="-ToastActivated"`, чтобы знать, что запуск произошел из всплывающего уведомления
5. **desktop:Extension** для **windows.toastNotificationActivation**, чтобы объявить CLSID активатора уведомления (GUID из шага 4).

**Package.appxmanifest**

```xml
<Package
  ...
  xmlns:com="http://schemas.microsoft.com/appx/manifest/com/windows10"
  xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10"
  IgnorableNamespaces="... com desktop">
  ...
  <Applications>
    <Application>
      ...
      <Extensions>

        <!--Register COM CLSID LocalServer32 registry key-->
        <com:Extension Category="windows.comServer">
          <com:ComServer>
            <com:ExeServer Executable="YourProject\YourProject.exe" Arguments="-ToastActivated" DisplayName="Toast activator">
              <com:Class Id="replaced-with-your-guid-C173E6ADF0C3" DisplayName="Toast activator"/>
            </com:ExeServer>
          </com:ComServer>
        </com:Extension>

        <!--Specify which CLSID to activate when toast clicked-->
        <desktop:Extension Category="windows.toastNotificationActivation">
          <desktop:ToastNotificationActivation ToastActivatorCLSID="replaced-with-your-guid-C173E6ADF0C3" /> 
        </desktop:Extension>

      </Extensions>
    </Application>
  </Applications>
 </Package>
```


### <a name="classic-win32"></a>Классическое приложение Win32

Если вы используете классическое приложение Win32 (или поддерживать оба варианта), вам необходимо объявить идентификатор модели пользователя приложения (AUMID), а также CLSID всплывающего уведомления (идентификатор GUID из шага 4) для ярлыка приложения в меню "Пуск".

Выберите уникальный AUMID для идентификации приложения Win32. Обычно он указывается в формате [Имя_компании].[Имя_приложения], но вам необходимо убедиться, что он является уникальным для всех приложений (вы можете добавьте в конец ряд цифр).

#### <a name="step-51-wix-installer"></a>Шаг 5.1. Установщик WiX

Если вы используете WiX в качестве установщика, измените файл **Product.wxs**, чтобы добавить два свойства ярлыка в ярлык меню "Пуск", как показано ниже. Убедитесь, что ваш идентификатор GUID из шага 4 заключен в `{}`, как показано ниже.

**Product.wxs**

```xml
<Shortcut Id="ApplicationStartMenuShortcut" Name="Wix Sample" Description="Wix Sample" Target="[INSTALLFOLDER]WixSample.exe" WorkingDirectory="INSTALLFOLDER">
                    
    <!--AUMID-->
    <ShortcutProperty Key="System.AppUserModel.ID" Value="YourCompany.YourApp"/>
    
    <!--COM CLSID-->
    <ShortcutProperty Key="System.AppUserModel.ToastActivatorCLSID" Value="{replaced-with-your-guid-C173E6ADF0C3}"/>
    
</Shortcut>
```

> [!IMPORTANT]
> Чтобы фактически использовать уведомления, необходимо установить приложение с помощью установщика перед отладкой в обычном режиме, чтобы ярлык в меню "Пуск" с вашими AUMID и CLSID существовал. После создания ярлыка в меню "Пуск" вы можете выполнить отладку, нажав клавишу F5 в Visual Studio.


#### <a name="step-52-register-aumid-and-com-server"></a>Шаг 5.2. Регистрация AUMID и COM-сервера

Затем, независимо от установщика, в коде запуска приложения (перед вызовом API-интерфейсов уведомлений) вызовите метод **RegisterAumidAndComServer**, указав класс активатора из шага 4 и идентификатор AUMID, который используется выше.

```csharp
// Register AUMID and COM server (for Desktop Bridge apps, this no-ops)
DesktopNotificationManagerCompat.RegisterAumidAndComServer<MyNotificationActivator>("YourCompany.YourApp");
```

Если вы поддерживаете мост для классических приложений и классический режим Win32, вы можете вызывать этот метод в любом случае. Если вы используете мост для классических приложений, этот метод просто немедленно возвращается. Разветвление кода не требуется.

Этот метод позволяет вызвать API-интерфейсы библиотеки compat для отправки уведомлений и управления ими, не указывая постоянно AUMID. Он также вставляет раздел реестра LocalServer32 для COM-сервера.


## <a name="step-6-register-com-activator"></a>Шаг 6. Регистрация активатора COM

Для моста для классических приложений и для классических приложений Win32 необходимо зарегистрировать тип активатора уведомлений, чтобы вы могли обрабатывать активацию всплывающих уведомлений.

В коде запуска приложения вызовите следующий метод **RegisterActivator**, передав в реализации класс **NotificationActivator** созданный на шаге 4. Это необходимо, чтобы вы получали активацию всплывающих уведомлений.

```csharp
// Register COM server and activator type
DesktopNotificationManagerCompat.RegisterActivator<MyNotificationActivator>();
```


## <a name="step-7-send-a-notification"></a>Шаг 7. Отправка уведомления

Отправка уведомлений осуществляется так же, как для приложений UWP, за исключением того, что вы будете использовать класс **DesktopNotificationManagerCompat** для создания **ToastNotifier**. Библиотека compat автоматически осуществляет разветвление кода для моста для классических приложений и Win32. Для классических приложений Win32 библиотека compat кэширует AUMID, указанный при вызове **RegisterAumidAndComServer**, чтобы вам не нужно было думать о том, следует ли предоставлять AUMID или нет.

> [!NOTE]
> Установите [библиотеку уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/), чтобы создавать уведомления с помощью C#, как показано ниже, вместо того чтобы использовать необработанный XML.

Убедитесь, что вы используете привязку **ToastContent**, как показано ниже, (или шаблон ToastGeneric, если вы создаете XML), так как предыдущие шаблоны всплывающих уведомлений для Windows 8.1 не активируют ваш активатор уведомлений COM, созданный на шаге 4.

> [!IMPORTANT]
> HTTP-изображения поддерживаются только в приложениях моста для классических приложений с поддержкой Интернета в манифесте. Классические приложения Win32 не поддерживают образы HTTP-изображения, вам необходимо скачать изображение и добавить их в локальные данные приложения и ссылаться на него локально.

```csharp
// Construct the visuals of the toast (using Notifications library)
ToastContent toastContent = new ToastContent()
{
    // Arguments when the user taps body of toast
    Launch = "action=viewConversation&conversationId=5",

    Visual = new ToastVisual()
    {
        BindingGeneric = new ToastBindingGeneric()
        {
            Children =
            {
                new AdaptiveText()
                {
                    Text = "Hello world!"
                }
            }
        }
    }
};

// Create the XML document (BE SURE TO REFERENCE WINDOWS.DATA.XML.DOM)
var doc = new XmlDocument();
doc.LoadXml(toastContent.GetContent());

// And create the toast notification
var toast = new ToastNotification(doc);

// And then show it
DesktopNotificationManagerCompat.CreateToastNotifier().Show(toast);
```

> [!IMPORTANT]
> Классические приложения Win32 не могут использовать устаревшие шаблоны всплывающих уведомлений (например, ToastText02). При указании COM CLSID активация традиционных шаблонов завершится ошибкой. Необходимо использовать шаблоны ToastGeneric для Windows 10, как показано выше.


## <a name="step-8-handling-activation"></a>Шаг 8. Обработка активации

Когда пользователь щелкает всплывающее уведомление, вызывается метод **OnActivated** класса **NotificationActivator**.

Внутри метода OnActivated можно проанализировать аргументы, указанные во всплывающем уведомлении, и получить данные, введенные или выбранные пользователем, и затем активировать приложение соответствующим образом.

> [!NOTE]
> Метод **OnActivated** не вызывается в потоке пользовательского интерфейса. Если вы хотите выполнить операции потока пользовательского интерфейса, необходимо вызвать `Application.Current.Dispatcher.Invoke(callback)`.

```csharp
// The GUID must be unique to your app. Create a new GUID if copying this code.
[ClassInterface(ClassInterfaceType.None)]
[ComSourceInterfaces(typeof(INotificationActivationCallback))]
[Guid("replaced-with-your-guid-C173E6ADF0C3"), ComVisible(true)]
public class MyNotificationActivator : NotificationActivator
{
    public override void OnActivated(string invokedArgs, NotificationUserInput userInput, string appUserModelId)
    {
        Application.Current.Dispatcher.Invoke(delegate
        {
            // Tapping on the top-level header launches with empty args
            if (arguments.Length == 0)
            {
                // Perform a normal launch
                OpenWindowIfNeeded();
                return;
            }

            // Parse the query string (using NuGet package QueryString.NET)
            QueryString args = QueryString.Parse(invokedArgs);

            // See what action is being requested 
            switch (args["action"])
            {
                // Open the image
                case "viewImage":

                    // The URL retrieved from the toast args
                    string imageUrl = args["imageUrl"];

                    // Make sure we have a window open and in foreground
                    OpenWindowIfNeeded();

                    // And then show the image
                    (App.Current.Windows[0] as MainWindow).ShowImage(imageUrl);

                    break;

                // Background: Quick reply to the conversation
                case "reply":

                    // Get the response the user typed
                    string msg = userInput["tbReply"];

                    // And send this message
                    SendMessage(msg);

                    // If there's no windows open, exit the app
                    if (App.Current.Windows.Count == 0)
                    {
                        Application.Current.Shutdown();
                    }

                    break;
            }
        });
    }

    private void OpenWindowIfNeeded()
    {
        // Make sure we have a window open (in case user clicked toast while app closed)
        if (App.Current.Windows.Count == 0)
        {
            new MainWindow().Show();
        }

        // Activate the window, bringing it to focus
        App.Current.Windows[0].Activate();

        // And make sure to maximize the window too, in case it was currently minimized
        App.Current.Windows[0].WindowState = WindowState.Normal;
    }
}
```

Для поддержки запуска, когда ваше приложение закрыто, в файле `App.xaml.cs`, необходимо переопределить метод **OnStartup** (для приложений WPF), чтобы понять, происходит ли запуск из всплывающего уведомления или нет. При запуске из всплывающего уведомления, будет присутствовать аргумент "-ToastActivated". В этом случае вам следует остановить любой обычный код активации запуска и разрешить вашу обработку кода запуска **OnActivated**.

```csharp
protected override async void OnStartup(StartupEventArgs e)
{
    // Register AUMID, COM server, and activator
    DesktopNotificationManagerCompat.RegisterAumidAndComServer<MyNotificationActivator>("YourCompany.YourApp");
    DesktopNotificationManagerCompat.RegisterActivator<MyNotificationActivator>();

    // If launched from a toast
    if (e.Args.Contains("-ToastActivated"))
    {
        // Our NotificationActivator code will run after this completes,
        // and will show a window if necessary.
    }

    else
    {
        // Show the window
        // In App.xaml, be sure to remove the StartupUri so that a window doesn't
        // get created by default, since we're creating windows ourselves (and sometimes we
        // don't want to create a window if handling a background activation).
        new MainWindow().Show();
    }

    base.OnStartup(e);
}
```


### <a name="activation-sequence-of-events"></a>Последовательность событий активации

Для WPF последовательность активации выглядит следующим образом...

Если приложение уже запущено:

1. Вызывается метод **OnActivated** в вашем **NotificationActivator**

Если приложение не запущено:

1. **OnStartup** в `App.xaml.cs` вызывается с **Args** со значением "-ToastActivated"
2. Вызывается метод **OnActivated** в вашем **NotificationActivator**


### <a name="foreground-vs-background-activation"></a>Активация на переднем плане или в фоне
Для классических приложений активации на переднем плане и в фоне обрабатывается одинаково: вызывается ваш активатор COM. Код вашего приложения определяет, следует ли отобразить окно или просто выполнить определенные действия, а затем завершить работу. Таким образом, указав **ActivationType** как **Background** в содержимом всплывающего уведомления, вы ничего не поменяете.


## <a name="step-9-remove-and-manage-notifications"></a>Шаг 9. Удаление уведомлений и управление ими

Удаление уведомлений и управление ими осуществляется так же, как и для приложений UWP. Однако мы рекомендуем использовать библиотеку compat для получения **DesktopNotificationHistoryCompat**, чтобы не предоставлять AUMID, если вы используете классический режим Win32.

```csharp
// Remove the toast with tag "Message2"
DesktopNotificationManagerCompat.History.Remove("Message2");

// Clear all toasts
DesktopNotificationManagerCompat.History.Clear();
```


## <a name="step-10-deploying-and-debugging"></a>Шаг 10. Развертывание и отладка

Сведения о развертывании и отладке приложения моста для классических приложений см. в разделе [Запуск, отладка и тестирование упакованного классического приложения](/porting/desktop-to-uwp-debug.md).

Для развертывания и отладки классического приложения Win32 необходимо установить приложение с помощью установщика перед отладкой в обычном режиме, чтобы ярлык в меню "Пуск" с вашими AUMID и CLSID существовал. После создания ярлыка в меню "Пуск" вы можете выполнить отладку, нажав клавишу F5 в Visual Studio.

Если ваши уведомления просто не отображаются в классическом приложении Win32 (и исключения не вызываются), это, скорее всего, означает, что ярлык в меню "Пуск" отсутствует (установите приложение с помощью установщика), или AUMID, используемый в коде, не соответствует AUMID в ярлыке меню "Пуск".

Если ваши уведомления отображаются, но не сохраняются в центре уведомлений (исчезают после закрытия всплывающего окна), активатор COM реализован неправильно.

Если вы установили как версию для моста для классических приложений, так версию для Win32, обратите внимание, что приложение моста для классических приложений будет иметь приоритет при обработке активации всплывающих уведомлений. Это означает, что всплывающие уведомления из классического приложения Win32 по-прежнему запускают приложение моста для классических приложений при нажатии. При удалении приложения моста для классических приложений активация будет обрабатываться классическим приложением Win32.


## <a name="known-issues"></a>Известные проблемы

**ИСПРАВЛЕНО. Приложение не получает фокус после щелчка всплывающего уведомления**: в сборках 15063 и более ранних версиях права переднего плана не передавались приложению при активации COM-сервера. Поэтому приложение просто мигало при попытке переместить его на передний план. В то время решения этой проблемы не существовало. Мы исправили это сборках 16299 и более поздних версиях.


## <a name="resources"></a>Ресурсы

* [Полный пример кода на GitHub](https://github.com/WindowsNotifications/desktop-toasts)
* [Всплывающие уведомления из классических приложений](toast-desktop-apps.md)
* [Документация по содержимому всплывающего уведомления](adaptive-interactive-toasts.md)

