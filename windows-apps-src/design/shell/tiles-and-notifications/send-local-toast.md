---
author: andrewleader
Description: Learn how to send a local toast notification and handle the user clicking the toast.
title: Отправка локального всплывающего уведомления
ms.assetid: E9AB7156-A29E-4ED7-B286-DA4A6E683638
label: Send a local toast notification
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, отправка всплывающих уведомлений, уведомления, отправка уведомлений, всплывающие уведомления, руководство, краткое руководство, начало работы, пример кода, пошаговое руководство
ms.localizationpriority: medium
ms.openlocfilehash: 3004b7041838656890b3a967e858dddc64c29ee5
ms.sourcegitcommit: c8f6866100a4b38fdda8394ea185b02d7af66411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2018
ms.locfileid: "3964254"
---
# <a name="send-a-local-toast-notification"></a>Отправка локального всплывающего уведомления


Всплывающее уведомление — это сообщение, которое приложение может создавать и доставлять пользователю, когда он или она в настоящее время не находится в вашем приложении. В этом руководстве описаны действия для создания, доставки и отображения всплывающего уведомления с новыми адаптивными шаблонами и интерактивными действиями Windows 10. Описанные действия демонстрируются на примере локальных уведомлений, которые реализуются проще всего.

> [!IMPORTANT]
> Для классических приложений (моста для классических приложений и классических приложений Win32) применяются другие действия для отправки уведомлений и обработки активации. См. документацию по [Классическим приложениям](toast-desktop-apps.md), чтобы узнать, как реализовать всплывающие уведомления.

Рассмотрим следующие действия:

### <a name="sending-a-toast"></a>Отправка всплывающего уведомления

* Создание визуальной части (текста и изображения) уведомления
* Добавление действий в уведомление
* Установка срока действия всплывающего уведомления
* Таким образом, вы можете заменить или удалить всплывающее уведомление позднее
* Отправка всплывающего уведомления с помощью локального API

### <a name="handling-activation"></a>Обработка активации

* Обработка активации при нажатии текста или кнопки
* Обработка активации переднего плана
* Обработка активации фона

> **Важные API-интерфейсы**: [класс ToastNotification](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotification), [класс ToastNotificationActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs)


## <a name="prerequisites"></a>Что вам понадобится

Чтобы полностью понять этот раздел, изучите инструкции ниже...

* Хорошее знание понятий и терминов всплывающих уведомлений. Дополнительные сведения см. в разделе [Обзор всплывающих уведомлений и центр уведомлений](https://blogs.msdn.microsoft.com/tiles_and_toasts/2015/07/08/toast-notification-and-action-center-overview-for-windows-10/).
* Знакомство с содержимым всплывающего уведомления Windows 10. Дополнительные сведения см. в [документации по содержимому всплывающего уведомления](adaptive-interactive-toasts.md).
* Проект приложения Windows 10 UWP

> [!NOTE]
> В отличие от Windows 8 или 8.1, вам больше не нужно объявлять в манифесте приложения, что ваше приложение способно отображать всплывающие уведомления. Все приложения способны отправлять и отображать всплывающие уведомления.

> [!NOTE]
> **Приложения для Windows 8 и 8.1**: этого используйте [документацию архива](https://msdn.microsoft.com/library/windows/apps/xaml/hh868254.aspx).


## <a name="install-nuget-packages"></a>Установка пакетов NuGet

Рекомендуется установить два следующих пакета NuGet в проект. Эти пакеты будут использовать наш пример кода. В конце статьи мы предоставим фрагменты кода «Ваниль», не используйте пакеты NuGet.

* [Microsoft.Toolkit.Uwp.Notifications](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/): создание всплывающих полезных данных через объекты вместо необработанных XML.
* [QueryString.NET](https://www.nuget.org/packages/QueryString.NET/): создание и анализ строки запроса с помощью C#


## <a name="add-namespace-declarations"></a>Добавление объявлений пространств имен

`Windows.UI.Notifications` включает интерфейсы API всплывающего уведомления.

```csharp
using Windows.UI.Notifications;
using Microsoft.Toolkit.Uwp.Notifications; // Notifications library
using Microsoft.QueryStringDotNET; // QueryString.NET
```


## <a name="send-a-toast"></a>Отправка всплывающего уведомления

В Windows 10 содержимое всплывающего уведомления описано с помощью адаптивного языка, который обеспечивает большую гибкость вида уведомления. Дополнительные сведения см. в [документации по содержимому всплывающего уведомления](adaptive-interactive-toasts.md).

### <a name="constructing-the-visual-part-of-the-content"></a>Создание визуальной части содержимого

Давайте начнем, создав визуальную часть содержимого, которая включает текст и изображения, которые необходимо добавить пользователя для просмотра.

Благодаря простой библиотеке уведомлений создание XML-содержимого является простым. Если не установить библиотеку уведомления из NuGet, необходимо создать XML-файл вручную, который оставляет место для ошибки.

> [!NOTE]
> В уведомлении можно использовать изображения из пакета приложения, локального хранилища приложения или из Интернета. Начиная с обновления Fall Creators Update размер веб-изображений может быть до 3 МБ для обычных подключений и до 1 МБ для лимитных подключений. На устройствах без Fall Creators Update размер веб-изображений не должен превышать 200 КБ.

```csharp
// In a real app, these would be initialized with actual data
string title = "Andrew sent you a picture";
string content = "Check this out, Happy Canyon in Utah!";
string image = "https://picsum.photos/360/202?image=883";
string logo = "ms-appdata:///local/Andrew.jpg";
 
// Construct the visuals of the toast
ToastVisual visual = new ToastVisual()
{
    BindingGeneric = new ToastBindingGeneric()
    {
        Children =
        {
            new AdaptiveText()
            {
                Text = title
            },
 
            new AdaptiveText()
            {
                Text = content
            },
 
            new AdaptiveImage()
            {
                Source = image
            }
        },
 
        AppLogoOverride = new ToastGenericAppLogo()
        {
            Source = logo,
            HintCrop = ToastGenericAppLogoCrop.Circle
        }
    }
};
```


### <a name="constructing-actions-part-of-the-content"></a>Создание действий части содержимого

Теперь добавим действия к содержимому.

В следующем примере мы добавили элемент ввода, который позволяет пользователю вводить текста, возвращаемый в приложение, когда пользователь нажимает одну из кнопок или щелкает всплывающее уведомление.

Затем мы добавили две кнопки, указав тип активации, содержимое и аргументы для каждой из них.
* **ActivationType** используется для указания того, как приложению необходимо активировать при выполнении этого действия пользователя. Вы можете запустить приложение на переднем плане, запустить фоновую задачу или запуск протокола другого приложения. Ваше приложение можете выбрать передний план или фоновый режим, но вы всегда будет получать вводимые пользователем данные и указанные вами аргументы, поэтому приложение может выполнить правильное действие, например отправить сообщение или открыть беседу.

```csharp
// In a real app, these would be initialized with actual data
int conversationId = 384928;
 
// Construct the actions for the toast (inputs and buttons)
ToastActionsCustom actions = new ToastActionsCustom()
{
    Inputs =
    {
        new ToastTextBox("tbReply")
        {
            PlaceholderContent = "Type a response"
        }
    },
 
    Buttons =
    {
        new ToastButton("Reply", new QueryString()
        {
            { "action", "reply" },
            { "conversationId", conversationId.ToString() }
 
        }.ToString())
        {
            ActivationType = ToastActivationType.Background,
            ImageUri = "Assets/Reply.png",
 
            // Reference the text box's ID in order to
            // place this button next to the text box
            TextBoxId = "tbReply"
        },
 
        new ToastButton("Like", new QueryString()
        {
            { "action", "like" },
            { "conversationId", conversationId.ToString() }
 
        }.ToString())
        {
            ActivationType = ToastActivationType.Background
        },
 
        new ToastButton("View", new QueryString()
        {
            { "action", "viewImage" },
            { "imageUrl", image }
 
        }.ToString())
    }
};
```


### <a name="combining-the-above-to-construct-the-full-content"></a>Комбинирование указанных выше элементов для создания всего содержимого

Создание содержимого теперь завершено, и мы можем использовать его для создания экземпляра объекта [**ToastNotification**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotification).

**Примечание**. Вы также можете предоставить тип активации внутри корневого элемента, чтобы указать, какой тип активации должен происходить, когда пользователь касается текста всплывающего уведомления. Как правило, коснувшись текста всплывающего уведомления, необходимо запустить приложение на переднем плане, чтобы создать согласованное взаимодействие с пользователем, но можно использовать другие типы активации для размещения вашего конкретного сценария, где это целесообразно для пользователя.

Вам всегда следует указывать значение свойства **Launch**, чтобы после касания пользователям тела всплывающего уведомления и запуска приложения оно понимало, какое содержимое должно отобразиться.

```csharp
// Now we can construct the final toast content
ToastContent toastContent = new ToastContent()
{
    Visual = visual,
    Actions = actions,
 
    // Arguments when the user taps body of toast
    Launch = new QueryString()
    {
        { "action", "viewConversation" },
        { "conversationId", conversationId.ToString() }
 
    }.ToString()
};
 
// And create the toast notification
var toast = new ToastNotification(toastContent.GetXml());
```


## <a name="set-an-expiration-time"></a>Задание времени окончания срока действия

В Windows 10 все всплывающие уведомления поступают в центр уведомлений после их закрытия или игнорируется пользователем, поэтому пользователь может просмотреть в уведомления, когда всплывающее окно исчезнет.

Тем не менее, если сообщение в уведомлении релевантно только для определенного периода времени, необходимо задать время окончания срока действия для всплывающего уведомления, пользователи не смогут увидеть устаревшие сведения из вашего приложения. Например, если повышение действует только 12 часов, задайте истечение срока действия, равный 12 часам. В следующем коде мы задаем время окончания срока действия, 2 дня.

> [!NOTE]
> По умолчанию и максимальное время окончания срока действия для локального всплывающего уведомления — 3 дня.

```csharp
toast.ExpirationTime = DateTime.Now.AddDays(2);
```


## <a name="provide-a-primary-key-for-your-toast"></a>Предоставить первичный ключ для всплывающего уведомления

Если вы хотите удалить или заменить уведомление, которое вы отправляете программным способом, необходимо использовать свойство Tag (и при необходимости свойство Group) для предоставления первичного ключа для уведомления. Затем можно использовать этот ключ в будущем для удаления или замены уведомления.

Чтобы просмотреть дополнительные сведения о замене или удалении уже доставленного всплывающего уведомления, см. раздел [краткое руководство: управление всплывающими уведомлениями в центре уведомлений (XAML)](https://msdn.microsoft.com/library/windows/apps/xaml/dn631260.aspx).

Tag и Group объединены в качестве составного первичного ключа. Группа — это более общий идентификатор, которой можно назначить группам, например "wallPosts", "сообщения", "friendRequests", и т. д. Выберите тег уникальной идентификации уведомления из группы. С помощью универсальной группы вы можете удалить все уведомления из группы с помощью [RemoveGroup API ](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_).

```csharp
toast.Tag = "18365";
toast.Group = "wallPosts";
```


## <a name="send-the-notification"></a>Отправка уведомления

После инициализации всплывающего уведомления, просто создайте объект [ToastNotifier](https://docs.microsoft.com/uwp/api/windows.ui.notifications.toastnotifier) и вызовите метод Show(), передав всплывающее уведомление.

```csharp
ToastNotificationManager.CreateToastNotifier().Show(toast);
```


## <a name="clear-your-notifications"></a>Очистка уведомлений

Приложения UWP несут ответственность за удаление и очистку уведомлений. При запуске приложения, мы не очистить автоматически уведомления.

Windows автоматически удалит уведомление, если пользователь явно нажмет уведомление.

Вот пример того, как приложение для обмена сообщениями должно делать...

1. Пользователь получает несколько всплывающих уведомлений о новых сообщениях беседы
2. Пользователь выбирает одно из этих всплывающих уведомлений, чтобы открыть диалог
3. Приложение открывает беседу, а затем удаляет все всплывающие уведомления для этой беседы (с помощью [RemoveGroup](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_) в группе приложения для этой беседы)
4. Центр уведомлений пользователя теперь правильно отражает состояние уведомления, так как слева в центре уведомлений нет устаревших уведомлений для этой беседы.

Подробные сведения о том, как очистить все уведомления или удалить определенные уведомления, см. в разделе [краткое руководство: управление всплывающими уведомлениями в центре уведомлений (XAML)](https://msdn.microsoft.com/library/windows/apps/xaml/dn631260.aspx).


## <a name="handling-activation"></a>Обработка активации

В Windows 10 когда пользователь нажимает всплывающее уведомление, могут активироваться приложения различными способами всплывающего уведомления...

* Активация переднего плана
* Фоновая активация

> [!NOTE]
> Если вы используете устаревшие шаблоны всплывающего уведомления из Windows 8.1, вместо **OnLaunched** будет вызываться **OnActivated**. Следующая документация относится только к современным уведомлениям Windows 10 для использования библиотеки уведомлений (или шаблон ToastGeneric при использовании необработанных XML).


### <a name="handling-foreground-activation"></a>Обработка активации переднего плана

В Windows 10, когда пользователь нажимает современное всплывающее уведомление (или кнопку на всплывающем уведомлении), вызывается **OnActivated** вместо **OnLaunched**, с новым типом активации — **ToastNotification**. Таким образом разработчик может легко различать активации всплывающего уведомления и выполнять задачи соответствующим образом.

В примере вы см. ниже, вы можете получить строка аргументов, указанные вами изначально в содержимого всплывающего уведомления. Также вы можете получить входные данные пользователя, указанное в текстовые поля и поля выбора.

> [!IMPORTANT]
> Вам необходимо инициализировать кадр и активировать окно вашего так же, как ваш код **OnLaunched**. **OnLaunched не вызывается, если пользователь нажимает на всплывающего уведомления**, даже если ваше приложение было закрыто и запуск в первый раз. Часто рекомендуется комбинировать **OnLaunched** и **OnActivated** в методе `OnLaunchedOrActivated`, так как такая же инициализация должна произойти в обоих.

```csharp
protected override void OnActivated(IActivatedEventArgs e)
{
    // Get the root frame
    Frame rootFrame = Window.Current.Content as Frame;
 
    // TODO: Initialize root frame just like in OnLaunched
 
    // Handle toast activation
    if (e is ToastNotificationActivatedEventArgs)
    {
        var toastActivationArgs = e as ToastNotificationActivatedEventArgs;
                 
        // Parse the query string (using QueryString.NET)
        QueryString args = QueryString.Parse(toastActivationArgs.Argument);
 
        // See what action is being requested 
        switch (args["action"])
        {
            // Open the image
            case "viewImage":
 
                // The URL retrieved from the toast args
                string imageUrl = args["imageUrl"];
 
                // If we're already viewing that image, do nothing
                if (rootFrame.Content is ImagePage && (rootFrame.Content as ImagePage).ImageUrl.Equals(imageUrl))
                    break;
 
                // Otherwise navigate to view it
                rootFrame.Navigate(typeof(ImagePage), imageUrl);
                break;
                             
 
            // Open the conversation
            case "viewConversation":
 
                // The conversation ID retrieved from the toast args
                int conversationId = int.Parse(args["conversationId"]);
 
                // If we're already viewing that conversation, do nothing
                if (rootFrame.Content is ConversationPage && (rootFrame.Content as ConversationPage).ConversationId == conversationId)
                    break;
 
                // Otherwise navigate to view it
                rootFrame.Navigate(typeof(ConversationPage), conversationId);
                break;
        }
 
        // If we're loading the app for the first time, place the main page on
        // the back stack so that user can go back after they've been
        // navigated to the specific page
        if (rootFrame.BackStack.Count == 0)
            rootFrame.BackStack.Add(new PageStackEntry(typeof(MainPage), null, null));
    }
 
    // TODO: Handle other types of activation
 
    // Ensure the current window is active
    Window.Current.Activate();
}
```


## <a name="handling-background-activation"></a>Обработка активации фона

При указании активации фона всплывающего уведомления (или кнопка внутри всплывающего уведомления), фоновая задача будет выполнен вместо активация приложения переднего плана.

Подробнее об использовании фоновых задач см. в разделе [Поддержка приложения с помощью фоновых задач](/launch-resume/support-your-app-with-background-tasks.md).

Если вы используете сборки 14393 или более поздней версии, можно использовать в процесс фоновой задачи, которые значительно упрощают вещи. Обратите внимание, что в процессе фоновые задачи не сможет запуститься в более старых версиях Windows. Мы будем использовать фоновую задачу в процессе в этом примере кода.

```csharp
const string taskName = "ToastBackgroundTask";

// If background task is already registered, do nothing
if (BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name.Equals(taskName)))
    return;

// Otherwise request access
BackgroundAccessStatus status = await BackgroundExecutionManager.RequestAccessAsync();

// Create the background task
BackgroundTaskBuilder builder = new BackgroundTaskBuilder()
{
    Name = taskName
};

// Assign the toast action trigger
builder.SetTrigger(new ToastNotificationActionTrigger());

// And register the task
BackgroundTaskRegistration registration = builder.Register();
```


Затем в App.xaml.cs, переопределите метод OnBackgroundActivated, вы можете получить предварительно определенных аргументов и пользовательского ввода, аналогично активации переднего плана.

```csharp
protected override async void OnBackgroundActivated(BackgroundActivatedEventArgs args)
{
    var deferral = args.TaskInstance.GetDeferral();
 
    switch (args.TaskInstance.Task.Name)
    {
        case "ToastBackgroundTask":
            var details = args.TaskInstance.TriggerDetails as ToastNotificationActionTriggerDetail;
            if (details != null)
            {
                string arguments = details.Argument;
                var userInput = details.UserInput;

                // Perform tasks
            }
            break;
    }
 
    deferral.Complete();
}
```



## <a name="plain-vanilla-code-snippets"></a>Фрагменты кода обычного «кода»

Если вы не используете библиотеки уведомления из NuGet, как показано ниже, чтобы создать [ToastNotification](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotification) вручную может создать XML-файлов.

```csharp
using Windows.UI.Notifications;
using Windows.Data.Xml.Dom;

// In a real app, these would be initialized with actual data
string title = "Andrew sent you a picture";
string content = "Check this out, Happy Canyon in Utah!";
string image = "http://blogs.msdn.com/cfs-filesystemfile.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-71-81-permanent/2727.happycanyon1_5B00_1_5D00_.jpg";
string logo = "ms-appdata:///local/Andrew.jpg";
 
// TODO: all values need to be XML escaped
 
// Construct the visuals of the toast
string toastVisual =
$@"<visual>
  <binding template='ToastGeneric'>
    <text>{title}</text>
    <text>{content}</text>
    <image src='{image}'/>
    <image src='{logo}' placement='appLogoOverride' hint-crop='circle'/>
  </binding>
</visual>";

// In a real app, these would be initialized with actual data
int conversationId = 384928;
 
// Generate the arguments we'll be passing in the toast
string argsReply = $"action=reply&conversationId={conversationId}";
string argsLike = $"action=like&conversationId={conversationId}";
string argsView = $"action=viewImage&imageUrl={Uri.EscapeDataString(image)}";
 
// TODO: all args need to be XML escaped
 
string toastActions =
$@"<actions>
 
  <input
      type='text'
      id='tbReply'
      placeHolderContent='Type a response'/>
 
  <action
      content='Reply'
      arguments='{argsReply}'
      activationType='background'
      imageUri='Assets/Reply.png'
      hint-inputId='tbReply'/>
 
  <action
      content='Like'
      arguments='{argsLike}'
      activationType='background'/>
 
  <action
      content='View'
      arguments='{argsView}'/>
 
</actions>";

// Now we can construct the final toast content
string argsLaunch = $"action=viewConversation&conversationId={conversationId}";
 
// TODO: all args need to be XML escaped
 
string toastXmlString =
$@"<toast launch='{argsLaunch}'>
    {toastVisual}
    {toastActions}
</toast>";
 
// Parse to XML
XmlDocument toastXml = new XmlDocument();
toastXml.LoadXml(toastXmlString);
 
// Generate toast
var toast = new ToastNotification(toastXml);
```


## <a name="resources"></a>Ресурсы

* [Полный пример кода на GitHub](https://github.com/WindowsNotifications/quickstart-sending-local-toast)
* [Документация по содержимому всплывающего уведомления](adaptive-interactive-toasts.md)
* [Класс ToastNotification](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotification)
* [Класс ToastNotificationActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs)
