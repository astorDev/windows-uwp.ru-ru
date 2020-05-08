---
Description: Узнайте, как приложения Win32 C# могут отправлять уведомления с помощью уведомлений и управлять пользовательским всплывающим уведомлением.
title: Отправка локального всплывающего уведомлений из классических приложений C#
ms.assetid: E9AB7156-A29E-4ED7-B286-DA4A6E683638
label: Send a local toast notification from desktop C# apps
template: detail.hbs
ms.date: 01/23/2018
ms.topic: article
keywords: Windows 10, UWP, Win32, Настольный, всплывающее уведомление, отправка всплывающего уведомления, отправка локального уведомления, Desktop Bridge, msix, разреженные пакеты, C#, C Sharp, всплывающее уведомление, WPF
ms.localizationpriority: medium
ms.openlocfilehash: f177660ce6e367caf69de849839a94472f5343fb
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82968289"
---
# <a name="send-a-local-toast-notification-from-desktop-c-apps"></a>Отправка локального всплывающего уведомлений из классических приложений C#

Классические приложения (включая Упакованные приложения [MSIX](https://docs.microsoft.com/windows/msix/desktop/source-code-overview) , приложения, использующие [разреженные пакеты](https://docs.microsoft.com/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps) для получения удостоверений пакетов и классические приложения Win32 без пакетов) могут отправлять Интерактивные всплывающие уведомления так же, как приложения Windows. Однако существует несколько специальных действий для классических приложений из-за различных схем активации и потенциального отсутствия удостоверения пакета, если вы не используете MSIX или разреженные пакеты.

> [!IMPORTANT]
> Если вы создаете приложение UWP, см. [документацию по UWP](send-local-toast.md). Сведения для других языков см. в разделе [C++ WRL для настольных компьютеров](send-local-toast-desktop-cpp-wrl.md).


## <a name="step-1-enable-the-windows-runtime-apis"></a>Шаг 1. Включение API-интерфейсов среда выполнения Windows

Если вы не ссылались на интерфейсы API среда выполнения Windows из приложения Win32, то сначала необходимо сделать это.

Просто установите `Microsoft.Windows.SDK.Contracts` [пакет NuGet](https://www.nuget.org/packages/Microsoft.Windows.SDK.Contracts) в проекте. Дополнительные сведения о [включении среда выполнения Windowsных API-интерфейсов](https://docs.microsoft.com/windows/apps/desktop/modernize/desktop-to-uwp-enhance)см. здесь.


## <a name="step-2-copy-compat-library-code"></a>Шаг 2. Копирование кода библиотеки compat

Скопируйте [файл DesktopNotificationManagerCompat.cs из GitHub](https://raw.githubusercontent.com/WindowsNotifications/desktop-toasts/master/CS/DesktopToastsApp/DesktopNotificationManagerCompat.cs) в свой проект. Библиотека compat значительно упрощает уведомления для классических приложений. Для выполнения следующих инструкций требуется библиотека compat.


## <a name="step-3-implement-the-activator"></a>Шаг 3. Реализация активатора

Необходимо реализовать обработчик для активации всплывающих уведомлений, чтобы при щелчке всплывающего уведомления ваше приложение мог выполнить какие-либо действия. Это необходимо, чтобы всплывающее уведомление сохранялось в центре уведомлений (так как всплывающее уведомление можно щелкнуть после закрытия вашего приложения). Этот класс можно разместить в любом месте в проекте.

Создайте новый класс **минотификатионактиватор** и расширьте класс **нотификатионактиватор** . Добавьте перечисленные ниже три атрибута и создайте уникальный идентификатор CLSID для приложения с помощью одного из многих генераторов GUID в сети. С помощью идентификатора CLSID (идентификатор класса) центр уведомлений определяет, какой класс активировать, используя COM.

**MyNotificationActivator.CS** (создание этого файла)

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


## <a name="step-4-register-with-notification-platform"></a>Шаг 4. Регистрация на платформе уведомлений

Затем вам необходимо зарегистрироваться на платформе уведомлений. В зависимости от того, используете ли вы MSIX или разреженные пакеты или классический Win32, можно выполнить различные действия. Если вы поддерживаете оба варианта, необходимо выполнить оба шага (тем не менее нет необходимости в разветвлении кода, так как наша библиотека делает это автоматически).


### <a name="msixsparse-packages"></a>MSIX/разреженные пакеты

Если вы используете [MSIX](https://docs.microsoft.com/windows/msix/desktop/source-code-overview) или [разреженный пакет](https://docs.microsoft.com/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps) (или, если они поддерживаются), в **Package. appxmanifest**добавьте:

1. Объявление для **xmlns:com**
2. Объявление для **xmlns:desktop**
3. В атрибуте **IgnorableNamespaces** : **com** и **desktop**
4. **com:Extension** для COM-активатора с использованием GUID из шага 4. Не забудьте добавить `Arguments="-ToastActivated"`, чтобы знать, что запуск произошел из всплывающего уведомления
5. **Desktop: расширение** для **Windows. тоастнотификатионактиватион** , чтобы объявить идентификатор CLSID для активатора всплывающего уведомления (GUID из шага #3).

**Package.appxmanifest**

```xml
<!--Add these namespaces-->
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

Если вы используете классическую программу Win32 (или поддерживает оба варианта), необходимо объявить идентификатор модели пользователя приложения (AUMID) и CLSID активатора всплывающих уведомлений (GUID из шага #3) на ярлыке приложения в меню Пуск.

Выберите уникальный AUMID для идентификации приложения Win32. Обычно он указывается в формате [Имя_компании].[Имя_приложения], но вам необходимо убедиться, что он является уникальным для всех приложений (вы можете добавьте в конец ряд цифр).

#### <a name="step-41-wix-installer"></a>Шаг 4,1. установщик WiX

Если вы используете WiX в качестве установщика, измените файл **Product.wxs**, чтобы добавить два свойства ярлыка в ярлык меню "Пуск", как показано ниже. Убедитесь, что идентификатор GUID из шага #3 будет заключен в `{}` , как показано ниже.

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


#### <a name="step-42-register-aumid-and-com-server"></a>Шаг 4,2. Регистрация AUMID и сервера COM

Затем, независимо от установщика, в коде запуска приложения (перед вызовом любых API-интерфейсов уведомлений) вызовите метод **регистераумидандкомсервер** , указав класс активатора уведомлений на шаге #3 и AUMID, который использовался выше.

```csharp
// Register AUMID and COM server (for MSIX/sparse package apps, this no-ops)
DesktopNotificationManagerCompat.RegisterAumidAndComServer<MyNotificationActivator>("YourCompany.YourApp");
```

Если вы поддерживаете как MSIX, так и разреженный пакет и классический Win32, вы можете вызывать этот метод независимо. Если вы работаете в MSIX или разреженном пакете, этот метод просто возвращает значение немедленно. Разветвление кода не требуется.

Этот метод позволяет вызвать API-интерфейсы библиотеки compat для отправки уведомлений и управления ими, не указывая постоянно AUMID. Он также вставляет раздел реестра LocalServer32 для COM-сервера.


## <a name="step-5-register-com-activator"></a>Шаг 5. Регистрация активатора COM

Для MSIX и разреженного пакета и классических приложений Win32 необходимо зарегистрировать тип активатора уведомлений, чтобы можно было выполнять активацию уведомлений.

В коде запуска приложения вызовите следующий метод **регистерактиватор** , передав реализацию класса **нотификатионактиватор** , созданного на шаге #3. Это необходимо, чтобы вы получали активацию всплывающих уведомлений.

```csharp
// Register COM server and activator type
DesktopNotificationManagerCompat.RegisterActivator<MyNotificationActivator>();
```


## <a name="step-6-send-a-notification"></a>Шаг 6. Отправка уведомления

Отправка уведомлений осуществляется так же, как для приложений UWP, за исключением того, что вы будете использовать класс **DesktopNotificationManagerCompat** для создания **ToastNotifier**. Библиотека совместимости автоматически обрабатывает различия между MSIX/разреженным пакетом и классическим Win32, поэтому не нужно развилкировать код. Для классических приложений Win32 библиотека compat кэширует AUMID, указанный при вызове **RegisterAumidAndComServer**, чтобы вам не нужно было думать о том, следует ли предоставлять AUMID или нет.

> [!NOTE]
> Установите [библиотеку уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/), чтобы создавать уведомления с помощью C#, как показано ниже, вместо того чтобы использовать необработанный XML.

Убедитесь, что вы используете **тоастконтент** , показанный ниже (или шаблон тоастженерик, если вы создаете XML), так как шаблоны всплывающих уведомлений прежних версий Windows 8.1 не активируют активатор для создания com-уведомлений, созданный на шаге #3.

> [!IMPORTANT]
> Образы HTTP поддерживаются только в приложениях MSIX или разреженных пакетов, имеющих возможности Интернета в манифесте. Классические приложения Win32 не поддерживают образы HTTP-изображения, вам необходимо скачать изображение и добавить их в локальные данные приложения и ссылаться на него локально.

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


## <a name="step-7-handling-activation"></a>Шаг 7. Обработка активации

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
Для классических приложений активации на переднем плане и в фоне обрабатывается одинаково: вызывается ваш активатор COM. Код вашего приложения определяет, следует ли отобразить окно или просто выполнить определенные действия, а затем завершить работу. Таким образом, указание **Активатионтипе** **фона** в содержимом всплывающих уведомлений не влияет на поведение.


## <a name="step-8-remove-and-manage-notifications"></a>Шаг 8. Удаление уведомлений и управление ими

Удаление уведомлений и управление ими осуществляется так же, как и для приложений UWP. Однако мы рекомендуем использовать библиотеку compat для получения **DesktopNotificationHistoryCompat**, чтобы не предоставлять AUMID, если вы используете классический режим Win32.

```csharp
// Remove the toast with tag "Message2"
DesktopNotificationManagerCompat.History.Remove("Message2");

// Clear all toasts
DesktopNotificationManagerCompat.History.Clear();
```


## <a name="step-9-deploying-and-debugging"></a>Шаг 9. Развертывание и отладка

Сведения о развертывании и отладке приложения MSIX см. в разделе [Запуск, отладка и тестирование упакованного классического приложения](/windows/uwp/porting/desktop-to-uwp-debug).

Для развертывания и отладки классического приложения Win32 необходимо установить приложение с помощью установщика перед отладкой в обычном режиме, чтобы ярлык в меню "Пуск" с вашими AUMID и CLSID существовал. После создания ярлыка в меню "Пуск" вы можете выполнить отладку, нажав клавишу F5 в Visual Studio.

Если ваши уведомления просто не отображаются в классическом приложении Win32 (и исключения не вызываются), это, скорее всего, означает, что ярлык в меню "Пуск" отсутствует (установите приложение с помощью установщика), или AUMID, используемый в коде, не соответствует AUMID в ярлыке меню "Пуск".

Если ваши уведомления отображаются, но не сохраняются в центре уведомлений (исчезают после закрытия всплывающего окна), активатор COM реализован неправильно.

Если вы установили пакет MSIX/SPARSE и классическое приложение Win32, обратите внимание, что приложение MSIX/sparse пакет заменит классическое приложение Win32 при обработке активации всплывающих уведомлений. Это означает, что при нажатии всплывающих уведомлений из классического приложения Win32 по-прежнему будет запускаться MSIX/разреженное приложение пакета. При удалении приложения MSIX или разреженного пакета все активации будут возвращены в классическое приложение Win32.


## <a name="known-issues"></a>Известные проблемы

**ИСПРАВЛЕНО. Приложение не получает фокус после щелчка всплывающего уведомления**: в сборках 15063 и более ранних версиях права переднего плана не передавались приложению при активации COM-сервера. Поэтому приложение просто мигало при попытке переместить его на передний план. В то время решения этой проблемы не существовало. Мы исправили это сборках 16299 и более поздних версиях.


## <a name="resources"></a>Ресурсы

* [Полный пример кода на GitHub](https://github.com/WindowsNotifications/desktop-toasts)
* [Всплывающие уведомления из классических приложений](toast-desktop-apps.md)
* [Документация по содержимому всплывающего уведомления](adaptive-interactive-toasts.md)

