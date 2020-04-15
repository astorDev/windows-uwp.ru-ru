---
Description: Узнайте, как запланировать появление локального всплывающего уведомления в дальнейшем.
title: Запланировать всплывающее уведомление
label: Schedule a toast notification
template: detail.hbs
ms.date: 04/09/2020
ms.topic: article
keywords: Windows 10, UWP, запланированное всплывающее уведомление, счедуледтоастнотификатион, практическое руководство, начало работы, пример кода, пошаговое руководство
ms.localizationpriority: medium
ms.openlocfilehash: 07339cf793bdada51f79d70d9e9e6b6d4a41851b
ms.sourcegitcommit: 017f2f1492f3220da0fae8b4c99de7206a185dff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81386433"
---
# <a name="schedule-a-toast-notification"></a>Запланировать всплывающее уведомление

Запланированные всплывающие уведомления позволяют запланировать появление уведомлений позже, независимо от того, выполняется ли в это время приложение. Это полезно для таких сценариев, как отображение напоминаний или других задач для пользователя, когда время и содержимое уведомления известны заранее.

Обратите внимание, что запланированные всплывающие уведомления имеют окно доставки в течение 5 минут. Если компьютер отключен в течение запланированного времени доставки и остается отключенным дольше 5 минут, то уведомление будет "удалено", как больше не относится к пользователю. Если вам требуется гарантированная доставка уведомлений независимо от времени отключения компьютера, рекомендуется использовать фоновую задачу с триггером времени, как показано в [этом примере кода](https://github.com/WindowsNotifications/quickstart-snoozable-toasts-even-if-computer-is-off).

> [!IMPORTANT]
> Настольные приложения (как MSIX, так и различающиеся пакеты и классическая Win32) немного отличаются от действий по отправке уведомлений и обработке активации. Выполните приведенные ниже инструкции, но замените `ToastNotificationManager` классом `DesktopNotificationManagerCompat` из документации по [приложениям для настольных приложений](toast-desktop-apps.md) .

> **Важные API**: [класс счедуледтоастнотификатион](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ScheduledToastNotification)


## <a name="prerequisites"></a>Предварительные требования

Чтобы полностью понять этот раздел, изучите инструкции ниже...

* Хорошее знание понятий и терминов всплывающих уведомлений. Дополнительные сведения см. в статье [Обзор центра уведомлений и действий](https://blogs.msdn.microsoft.com/tiles_and_toasts/2015/07/08/toast-notification-and-action-center-overview-for-windows-10/).
* Знакомство с содержимым всплывающего уведомления Windows 10. Дополнительные сведения см. в [документации по содержимому всплывающего уведомления](adaptive-interactive-toasts.md).
* Проект приложения Windows 10 UWP


## <a name="install-nuget-packages"></a>Установка пакетов NuGet

Рекомендуется установить два следующих пакета NuGet в проект. Эти пакеты будут использовать наш пример кода.

* [Microsoft.Toolkit.Uwp.Notifications](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/): создание всплывающих полезных данных через объекты вместо необработанных XML.
* [QueryString.NET](https://www.nuget.org/packages/QueryString.NET/): создание и анализ строки запроса с помощью C#


## <a name="add-namespace-declarations"></a>Добавление объявлений пространств имен

`Windows.UI.Notifications` включает API-интерфейсы всплывающих уведомлений.

```csharp
using Windows.UI.Notifications;
using Microsoft.Toolkit.Uwp.Notifications; // Notifications library
using Microsoft.QueryStringDotNET; // QueryString.NET
```


## <a name="construct-the-toast-content"></a>Создание содержимого всплывающего уведомления

В Windows 10 содержимое всплывающего уведомления описано с помощью адаптивного языка, который обеспечивает большую гибкость вида уведомления. Дополнительные сведения см. в [документации по содержимому всплывающего уведомления](adaptive-interactive-toasts.md).

Благодаря библиотеке уведомлений создание XML-содержимого выполняется очень просто. Если не установить библиотеку уведомления из NuGet, необходимо создать XML-файл вручную, который оставляет место для ошибки.

Вам всегда следует указывать значение свойства **Launch**, чтобы после касания пользователям тела всплывающего уведомления и запуска приложения оно понимало, какое содержимое должно отобразиться.

```csharp
// In a real app, these would be initialized with actual data
string title = "ASTR 170B1";
string content = "You have 3 items due today!";

// Now we can construct the final toast content
ToastContent toastContent = new ToastContent()
{
    Visual = new ToastVisual()
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
                }
            }
        }
    },
 
    // Arguments when the user taps body of toast
    Launch = new QueryString()
    {
        { "action", "viewClass" },
        { "classId", "3910938180" }
 
    }.ToString()
};
```

## <a name="create-the-scheduled-toast"></a>Создание запланированного уведомления

После инициализации содержимого всплывающего уведомления создайте новый [счедуледтоастнотификатион](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ScheduledToastNotification) и передайте его в XML-файл и укажите время доставки уведомления.

```csharp
// Create the scheduled notification
var toast = new ScheduledToastNotification(toastContent.GetXml(), DateTime.Now.AddSeconds(5));
```


## <a name="provide-a-primary-key-for-your-toast"></a>Предоставить первичный ключ для всплывающего уведомления

Если вы хотите программно отменить, удалить или заменить запланированное уведомление, необходимо использовать свойство Tag (и, при необходимости, свойство Group), чтобы предоставить первичный ключ для уведомления. Затем этот первичный ключ можно использовать в будущем для отмены, удаления или замены уведомления.

Чтобы просмотреть дополнительные сведения о замене или удалении уже доставленного всплывающего уведомления, см. раздел [краткое руководство: управление всплывающими уведомлениями в центре уведомлений (XAML)](https://docs.microsoft.com/previous-versions/windows/apps/dn631260(v=win.10)).

Tag и Group объединены в качестве составного первичного ключа. Группа — это более общий идентификатор, которой можно назначить группам, например "wallPosts", "сообщения", "friendRequests", и т. д. Выберите тег уникальной идентификации уведомления из группы. С помощью универсальной группы вы можете удалить все уведомления из группы с помощью [RemoveGroup API ](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_).

```csharp
toast.Tag = "18365";
toast.Group = "ASTR 170B1";
```


## <a name="schedule-the-notification"></a>Запланировать уведомление

Наконец, создайте [тоастнотифиер](https://docs.microsoft.com/uwp/api/windows.ui.notifications.toastnotifier) и вызовите аддтосчедуле (), передав запланированное всплывающее уведомление.

```csharp
// And your scheduled toast to the schedule
ToastNotificationManager.CreateToastNotifier().AddToSchedule(toast);
```


## <a name="cancel-scheduled-notifications"></a>Отменить запланированные уведомления

Чтобы отменить запланированное уведомление, сначала необходимо получить список всех запланированных уведомлений.

Затем найдите запланированное всплывающее уведомление, соответствующее тегу (и, при необходимости, группе), который вы указали ранее, и вызовите Ремовефромсчедуле ().

```csharp
// Create the toast notifier
ToastNotifier notifier = ToastNotificationManager.CreateToastNotifier();

// Get the list of scheduled toasts that haven't appeared yet
IReadOnlyList<ScheduledToastNotification> scheduledToasts = notifier.GetScheduledToastNotifications();

// Find our scheduled toast we want to cancel
var toRemove = scheduledToasts.FirstOrDefault(i => i.Tag == "18365" && i.Group == "ASTR 170B1");
if (toRemove != null)
{
    // And remove it from the schedule
    notifier.RemoveFromSchedule(toRemove);
}
```


## <a name="activation-handling"></a>Обработка активации

Дополнительные сведения об обработке активации см. в документации по [отправке локального уведомления](send-local-toast.md) . Активация запланированного всплывающего уведомления обрабатывается так же, как и активация локального всплывающего уведомления.


## <a name="adding-actions-inputs-and-more"></a>Добавление действий, входных данных и т. д.

Дополнительные сведения о таких разделах, как действия и входные данные, см. в документации по [отправке локального уведомления](send-local-toast.md) . Действия и входные данные работают одинаково в локальных уведомлениях, как это делается в запланированных уведомлениях.


## <a name="resources"></a>Ресурсы

* [Документация по содержимому всплывающего уведомления](adaptive-interactive-toasts.md)
* [Класс Счедуледтоастнотификатион](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ScheduledToastNotification)
