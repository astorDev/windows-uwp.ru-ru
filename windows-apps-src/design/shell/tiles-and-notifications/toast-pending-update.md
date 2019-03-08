---
Description: Сведения о создании многоэтапный взаимодействий в уведомлениях.
title: Всплывающее уведомление с ожидающей активацией обновления
label: Toast with pending update activation
template: detail.hbs
ms.date: 12/14/2017
ms.topic: article
keywords: windows 10, uwp, всплывающее уведомление, ожидающее обновления, pendingupdate, многоэтапное взаимодействие, многоэтапная интерактивность
ms.localizationpriority: medium
ms.openlocfilehash: b1574ee2913bd2889af204aae1089dc170df95b8
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57648559"
---
# <a name="toast-with-pending-update-activation"></a>Всплывающее уведомление с ожидающей активацией обновления

Вы можете использовать класс **PendingUpdate** для создания многоэтапного взаимодействия во всплывающих уведомлениях. Например, как показано ниже, вы можете создать ряд всплывающих уведомлений, в которой последующих всплывающих уведомлений зависит от ответов на предыдущие уведомления.

![Всплывающее уведомление с ожидающим обновлением](images/toast-pendingupdate.gif)

> [!IMPORTANT]
> **Требуется рабочий стол Fall Creators Update и 2.0.0 библиотека уведомлений**: Вам должна быть запущена локальная сборка 16299 или более поздней версии, чтобы просмотреть ожидающие операции обновления. Для назначения **PendingUpdate** кнопкам необходимо использовать [библиотеку уведомлений набора средств сообщества UWP из NuGet](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) версии 2.0.0 или более поздней. **PendingUpdate** поддерживается только на настольных компьютерах и игнорируется на других устройствах.


## <a name="prerequisites"></a>Предварительные условия

В этой статье предполагается, что вы знакомы со следующими темами..

- [Создав всплывающее содержимое](adaptive-interactive-toasts.md)
- [При отправке всплывающее уведомление и обработке активации фон](send-local-toast.md)


## <a name="overview"></a>Обзор

Для реализации всплывающего уведомления, использующего ожидающее обновление после активации...

1. На кнопках фоновой активации всплывающего уведомления укажите значение **AfterActivationBehavior** в свойстве **PendingUpdate**

2. Назначьте **Tag** (и при необходимости **Group**) при отправке всплывающего уведомления

3. Когда пользователь нажмет кнопку, фоновая задача будет активирована, а всплывающее уведомление останется на экране в состоянии ожидания обновления

4. В фоновой задаче отправьте новое всплывающее уведомление с новым содержимым, используя те же **Tag** и **Group**


## <a name="assign-pendingupdate"></a>Назначение PendingUpdate

На кнопках фоновой активации укажите значение **AfterActivationBehavior** в свойстве **PendingUpdate**. Обратите внимание, что это работает только для кнопок с типом **ActivationType** из **Background**.

```csharp
new ToastButton("Yes", "action=orderLunch")
{
    ActivationType = ToastActivationType.Background,

    ActivationOptions = new ToastActivationOptions()
    {
        AfterActivationBehavior = ToastAfterActivationBehavior.PendingUpdate
    }
}
```

```xml
<action
    content='Yes'
    arguments='action=orderLunch'
    activationType='background'
    afterActivationBehavior='pendingUpdate' />
```


## <a name="use-a-tag-on-the-notification"></a>Использование свойства Tag в уведомлении

Чтобы позднее заменить уведомление, нужно назначить **Tag** (и при необходимости **Group**) в уведомлении.

```csharp
// Create the notification
var notif = new ToastNotification(content.GetXml())
{
    Tag = "lunch"
};

// And show it
ToastNotificationManager.CreateToastNotifier().Show(notif);
```


## <a name="replace-the-toast-with-new-content"></a>Замена всплывающего уведомления новым содержимым

В ответ на нажатие кнопки пользователем активируется фоновая задача, и вам необходимо заменить всплывающее уведомление на новое содержимое. Для этого просто отправьте новое всплывающее уведомление с теми же значениями **Tag** и **Group**.

Мы настоятельно рекомендуем **отключить звук** при замене в ответ на нажатие кнопки, так как пользователь уже взаимодействует с вашим уведомлением.

```csharp
// Generate new content
ToastContent content = new ToastContent()
{
    ...

    // We disable audio on all subsequent toasts since they appear right after the user
    // clicked something, so the user's attention is already captured
    Audio = new ToastAudio() { Silent = true }
};

// Create the new notification
var notif = new ToastNotification(content.GetXml())
{
    Tag = "lunch"
};

// And replace the old one with this one
ToastNotificationManager.CreateToastNotifier().Show(notif);
```


## <a name="related-topics"></a>Статьи по теме

- [Полный образец кода на GitHub](https://github.com/WindowsNotifications/quickstart-toast-pending-update)
- [Отправить Локальная активация всплывающее уведомление и дескриптора](send-local-toast.md)
- [Содержимое документации всплывающее уведомление](adaptive-interactive-toasts.md)
- [Индикатор хода выполнения всплывающее уведомление](toast-progress-bar.md)