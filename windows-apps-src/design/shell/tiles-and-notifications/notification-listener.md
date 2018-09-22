---
author: andrewleader
Description: Learn how to use Notification Listener to access all of the user's notifications.
title: Прослушиватель уведомлений
ms.assetid: E9AB7156-A29E-4ED7-B286-DA4A6E683638
label: Chaseable tiles
template: detail.hbs
ms.author: mijacobs
ms.date: 06/13/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, прослушиватель уведомлений, usernotificationlistener, документация, уведомления о доступе
ms.localizationpriority: medium
ms.openlocfilehash: f4d8cb9ef7589bd8f0c56586ab8fcfec7c1f01e3
ms.sourcegitcommit: a160b91a554f8352de963d9fa37f7df89f8a0e23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2018
ms.locfileid: "4129333"
---
# <a name="notification-listener-access-all-notifications"></a>Прослушиватель уведомлений: доступ ко всем уведомлениям

Прослушиватель уведомлений предоставляет доступ к уведомлениям пользователя. Смарт-часы и другие носимые устройства могут использовать прослушиватель уведомлений для отправки уведомлений с телефона на носимое устройство. Приложения автоматизации для дома могут использовать прослушиватель уведомлений для выполнения конкретных действий при получении уведомлений, например заставить свет мигать, когда вам кто-то звонит.call. 

> [!IMPORTANT]
> **Требуется юбилейное обновление**. Обратите внимание, что для использования прослушивателя уведомлений необходим пакет SDK 14393 и сборка 14393 или более поздней версии.


> **Важные API-интерфейсы**: [класс UserNotificationListener](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.Management.UserNotificationListener), [класс UserNotificationChangedTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.UserNotificationChangedTrigger)


## <a name="enable-the-listener-by-adding-the-user-notification-capability"></a>Включите прослушиватель, добавив функцию пользовательских уведомлений 

Чтобы использовать прослушиватель уведомлений, необходимо добавить в манифест приложения функцию прослушивателя пользовательских уведомлений.

1. В Visual Studio в проводнике решений дважды щелкните файл `Package.appxmanifest`, чтобы открыть конструктор манифестов.
2. Откройте вкладку "Возможности".
3. Проверьте функцию **Прослушиватель пользовательских уведомлений**.


## <a name="check-whether-the-listener-is-supported"></a>Проверьте, поддерживается ли прослушиватель

Если ваше приложение поддерживает более ранние версии Windows 10, необходимо использовать для проверки [класс ApiInformation](https://docs.microsoft.com/uwp/api/Windows.Foundation.Metadata.ApiInformation).  Если прослушиватель не поддерживается, не отправляйте никакие вызовы в API-интерфейсы прослушивателя.

```csharp
if (ApiInformation.IsTypePresent("Windows.UI.Notifications.Management.UserNotificationListener"))
{
    // Listener supported!
}
 
else
{
    // Older version of Windows, no Listener
}
```


## <a name="requesting-access-to-the-listener"></a>Запрос доступа к прослушивателю

Поскольку прослушиватель разрешает доступ к уведомлениям пользователя, пользователи обязаны предоставить приложению разрешение на доступ к уведомлениям. Во время первого запуска приложения необходимо запросить доступ на использование прослушивателя уведомлений. При желании можно продемонстрировать предварительный пользовательский интерфейс, поясняющий, почему приложению требуется доступ к уведомлениям пользователя, прежде чем можно вызвать метод [RequestAccessAsync](https://docs.microsoft.com/uwp/api/windows.ui.notifications.management.usernotificationlistener.RequestAccessAsync), чтобы пользователь понял, почему они должны разрешить доступ.

```csharp
// Get the listener
UserNotificationListener listener = UserNotificationListener.Current;
 
// And request access to the user's notifications (must be called from UI thread)
UserNotificationListenerAccessStatus accessStatus = await listener.RequestAccessAsync();
 
switch (accessStatus)
{
    // This means the user has granted access.
    case UserNotificationListenerAccessStatus.Allowed:
 
        // Yay! Proceed as normal
        break;
 
    // This means the user has denied access.
    // Any further calls to RequestAccessAsync will instantly
    // return Denied. The user must go to the Windows settings
    // and manually allow access.
    case UserNotificationListenerAccessStatus.Denied:
 
        // Show UI explaining that listener features will not
        // work until user allows access.
        break;
 
    // This means the user closed the prompt without
    // selecting either allow or deny. Further calls to
    // RequestAccessAsync will show the dialog again.
    case UserNotificationListenerAccessStatus.Unspecified:
 
        // Show UI that allows the user to bring up the prompt again
        break;
}
```

Пользователь может отозвать этот доступ в любой момент в настройках Windows. Следовательно, приложение должно всегда проверять статус доступа, используя метод [GetAccessStatus](https://docs.microsoft.com/uwp/api/windows.ui.notifications.management.usernotificationlistener.GetAccessStatus), прежде чем выполнять код для прослушивателя уведомлений. Если пользователь отзывает доступ, API-вызовы будут автоматически завершаться сбоем, исключение создаваться не будет (например, API для получения всех уведомлений просто вернет пустой список).


## <a name="access-the-users-notifications"></a>Доступ к уведомлениям пользователя

Прослушиватель уведомлений позволяет получить список текущих уведомлений пользователя. Просто вызовите метод [GetNotificationsAsync](https://docs.microsoft.com/uwp/api/windows.ui.notifications.management.usernotificationlistener.GetNotificationsAsync) и задайте тип уведомлений, которые требуется получить (в настоящее время поддерживается только один тип уведомлений— всплывающие уведомления).

```csharp
// Get the toast notifications
IReadOnlyList<UserNotification> notifs = await listener.GetNotificationsAsync(NotificationKinds.Toast);
```


## <a name="displaying-the-notifications"></a>Отображение уведомлений

Каждое уведомление представляется в виде класса [UserNotification](https://docs.microsoft.com/uwp/api/windows.ui.notifications.usernotification), который предоставляет информацию о приложении, из которого поступает уведомление, времени создания уведомления, идентификаторе уведомления и самом уведомлении.

```csharp
public sealed class UserNotification
{
    public AppInfo AppInfo { get; }
    public DateTimeOffset CreationTime { get; }
    public uint Id { get; }
    public Notification Notification { get; }
}
```

Свойство [AppInfo](https://docs.microsoft.com/uwp/api/windows.ui.notifications.usernotification.AppInfo) предоставляет информацию, необходимую для отображения уведомления.

> [!NOTE]
> Рекомендуется окружить весь код для обработки одного уведомления в блок try/catch, если непредвиденное исключение произойдет при захвате отдельного уведомления. Не следует отказываться от отображения всех остальных уведомлений из-за проблемы с одним из них.

```csharp
// Select the first notification
UserNotification notif = notifs[0];
 
// Get the app's display name
string appDisplayName = notif.AppInfo.DisplayInfo.DisplayName;
 
// Get the app's logo
BitmapImage appLogo = new BitmapImage();
RandomAccessStreamReference appLogoStream = notif.AppInfo.DisplayInfo.GetLogo(new Size(16, 16));
await appLogo.SetSourceAsync(await appLogoStream.OpenReadAsync());
```

Содержимое самого уведомления, например текст уведомления, содержится в свойстве [Уведомление](https://docs.microsoft.com/uwp/api/windows.ui.notifications.usernotification.Notification). Это свойство содержит визуальную часть уведомления. (Если вы знакомы с отправкой уведомлений в Windows, вы заметите, что свойства [Visual](https://docs.microsoft.com/uwp/api/windows.ui.notifications.notification.Visual) и [Visual.Bindings](https://docs.microsoft.com/uwp/api/windows.ui.notifications.notificationvisual.Bindings) в объекте [Notification](https://docs.microsoft.com/uwp/api/windows.ui.notifications.notification) соответствуют тому, что отправляют разработчики при отображении уведомления).

Нужно искать всплывающую привязку (чтобы избежать ошибок в коде, необходимо убедиться, что привязка не равна null). Можно получить текстовые элементы из привязки. Можно отобразить столько текстовых элементов, сколько нужно. (В идеале необходимо отобразить их все.) Текстовые элементы можно обрабатывать иначе: например, первый как текст заголовка, а последующие— как текст основной части.

```csharp
// Get the toast binding, if present
NotificationBinding toastBinding = notif.Notification.Visual.GetBinding(KnownNotificationBindings.ToastGeneric);
 
if (toastBinding != null)
{
    // And then get the text elements from the toast binding
    IReadOnlyList<AdaptiveNotificationText> textElements = toastBinding.GetTextElements();
 
    // Treat the first text element as the title text
    string titleText = textElements.FirstOrDefault()?.Text;
 
    // We'll treat all subsequent text elements as body text,
    // joining them together via newlines.
    string bodyText = string.Join("\n", textElements.Skip(1).Select(t => t.Text));
}
```


## <a name="remove-a-specific-notification"></a>Удаление конкретного уведомления

Если носимое устройство или служба позволяет пользователю скрыть уведомления, можно удалить фактическое уведомление, чтобы пользователь позже не увидел его на телефоне или на ПК. Просто укажите ИД уведомления (из объекта [UserNotification](https://docs.microsoft.com/uwp/api/windows.ui.notifications.usernotification)), которое необходимо удалить: 

```csharp
// Remove the notification
listener.RemoveNotification(notifId);
```


## <a name="clear-all-notifications"></a>Удалить все уведомления

Метод [UserNotificationListener.ClearNotifications](https://docs.microsoft.com/uwp/api/windows.ui.notifications.management.usernotificationlistener.ClearNotifications) удаляет все уведомления пользователя. Используйте этот метод с осторожностью. Удалять все уведомления нужно лишь в том случае, если ваше носимое устройство или служба отображает ВСЕ уведомления. Если носимое устройство или служба отображает только определенные уведомления, то когда пользователь нажимает кнопку "Очистить уведомления", он ожидает, что удалены будут лишь некоторые уведомления; если вызвать метод [ClearNotifications](https://docs.microsoft.com/uwp/api/windows.ui.notifications.management.usernotificationlistener.ClearNotifications), удалятся все уведомления, включая те, которые не отображались на носимом устройстве или в службе.

```csharp
// Clear all notifications. Use with caution.
listener.ClearNotifications();
```


## <a name="background-task-for-notification-addeddismissed"></a>Фоновая задача для добавленного/скрытого уведомления

Стандартный способ включить прослушивание уведомлений в приложении— настроить фоновую задачу, чтобы вы знали, когда приложение было добавлено или скрыто, независимо от того, работает ли приложение в настоящее время.

Благодаря [однопроцессной модели](../../../launch-resume/create-and-register-an-inproc-background-task.md), добавленной в юбилейном обновлении, добавить фоновые задачи относительно просто. Получив от приложения доступ к прослушивателю уведомлений и разрешение на выполнение фоновых задач, просто зарегистрируйте новую фоновую задачу в коде основного приложения и задайте класс [UserNotificationChangedTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.usernotificationchangedtrigger) с помощью [типа всплывающего уведомления](https://docs.microsoft.com/uwp/api/windows.ui.notifications.notificationkinds).

```csharp
// TODO: Request/check Listener access via UserNotificationListener.Current.RequestAccessAsync
 
// TODO: Request/check background task access via BackgroundExecutionManager.RequestAccessAsync
 
// If background task isn't registered yet
if (!BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name.Equals("UserNotificationChanged")))
{
    // Specify the background task
    var builder = new BackgroundTaskBuilder()
    {
        Name = "UserNotificationChanged"
    };
 
    // Set the trigger for Listener, listening to Toast Notifications
    builder.SetTrigger(new UserNotificationChangedTrigger(NotificationKinds.Toast));
 
    // Register the task
    builder.Register();
}
```

Затем в файле App.xaml.cs переопределите метод [OnBackgroundActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.OnBackgroundActivated), если вы еще не сделали этого, и используйте оператор-переключатель в имени задачи, чтобы определить, какой из многочисленных триггеров фоновых задач был вызван.

```csharp
protected override async void OnBackgroundActivated(BackgroundActivatedEventArgs args)
{
    var deferral = args.TaskInstance.GetDeferral();
 
    switch (args.TaskInstance.Task.Name)
    {
        case "UserNotificationChanged":
            // Call your own method to process the new/removed notifications
            // The next section of documentation discusses this code
            await MyWearableHelpers.SyncNotifications();
            break;
    }
 
    deferral.Complete();
}
```

Фоновая задача— это просто напоминание, не предоставляющей никакой информации о том, какое уведомление было добавлено или удалено. При вызове фоновой задачи необходимо синхронизировать уведомления на носимом устройстве, чтобы они отражали уведомления на платформе. В этом случае если фоновая задача завершится сбоем, уведомления на носимом устройстве можно будет восстановить при следующем выполнении фоновой задачи.

`SyncNotifications` это реализуемый метод; в следующем разделе рассказано, как. 


## <a name="determining-which-notifications-were-added-and-removed"></a>Определение добавленных и удаленных уведомлений

В вашем методе `SyncNotifications` необходимо вычислить разницу между текущей коллекцией уведомлений и уведомлениями на платформе, чтобы определить, какие уведомления были добавлены или удалены (синхронизируя уведомления с носимым устройством).

```csharp
// Get all the current notifications from the platform
IReadOnlyList<UserNotification> userNotifications = await listener.GetNotificationsAsync(NotificationKinds.Toast);
 
// Obtain the notifications that our wearable currently has displayed
IList<uint> wearableNotificationIds = GetNotificationsOnWearable();
 
// Copy the currently displayed into a list of notification ID's to be removed
var toBeRemoved = new List<uint>(wearableNotificationIds);
 
// For each notification in the platform
foreach (UserNotification userNotification in userNotifications)
{
    // If we've already displayed this notification
    if (wearableNotificationIds.Contains(userNotification.Id))
    {
        // We want to KEEP it displayed, so take it out of the list
        // of notifications to remove.
        toBeRemoved.Remove(userNotification.Id);
    }
 
    // Othwerise it's a new notification
    else
    {
        // Display it on the Wearable
        SendNotificationToWearable(userNotification);
    }
}
 
// Now our toBeRemoved list only contains notification ID's that no longer exist in the platform.
// So we will remove all those notifications from the wearable.
foreach (uint id in toBeRemoved)
{
    RemoveNotificationFromWearable(id);
}
```


## <a name="foreground-event-for-notification-addeddismissed"></a>Событие переднего плана для добавленных/удаленных уведомлений

> [!IMPORTANT] 
> Известная проблема: событие переднего плана будут зацикливание ЦП в последних версиях Windows и ранее данные не подошли до этого. НЕ используйте событие переднего плана. В ближайшие обновления для Windows мы будет устранить эту проблему.

Вместо использования событие переднего плана, используйте код, показанный ранее для фоновой задачи [модель с одним процессом](../../../launch-resume/create-and-register-an-inproc-background-task.md) . Фоновая задача также позволяют получать уведомления об изменении обоих во время работы приложения скрытые или не выполняется.

```csharp
// Subscribe to foreground event (DON'T USE THIS)
listener.NotificationChanged += Listener_NotificationChanged;
 
private void Listener_NotificationChanged(UserNotificationListener sender, UserNotificationChangedEventArgs args)
{
    // NOTE: This event WILL CAUSE CPU LOOPS, DO NOT USE. Use the background task instead.
}
```


## <a name="how-to-fix-delays-in-the-background-task"></a>Как устранить задержки в фоновой задаче

При тестировании приложения можно заметить, что фоновая задача иногда откладывается и не запускается несколько минут. Чтобы решить эту проблему, пользователь должен перейти в настройки системы -> Системные -> Батарея -> Использование заряда батареи конкретными приложениями, найти приложение в списке, выбрать его и изменить для него значение на "Всегда разрешена работа в фоновом режиме". После этого фоновая задача будет всегда запускаться в течение секунды после получения уведомления.