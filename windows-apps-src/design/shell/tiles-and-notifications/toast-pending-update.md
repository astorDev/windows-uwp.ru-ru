---
author: andrewleader
Description: Learn how to create multi-step interactions in your notifications.
title: Всплывающее уведомление с ожидающей активацией обновления
label: Toast with pending update activation
template: detail.hbs
ms.author: mijacobs
ms.date: 12/14/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, всплывающее уведомление, ожидающее обновления, pendingupdate, многоэтапное взаимодействие, многоэтапная интерактивность
ms.localizationpriority: medium
ms.openlocfilehash: f5efccbb73758d0e6541e59812801c22a22c87b5
ms.sourcegitcommit: c4d3115348c8b54fcc92aae8e18fdabc3deb301d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2018
ms.locfileid: "5410652"
---
# <a name="toast-with-pending-update-activation"></a>Всплывающее уведомление с ожидающей активацией обновления

Вы можете использовать класс **PendingUpdate** для создания многоэтапного взаимодействия во всплывающих уведомлениях. Например, как показано ниже, вы можете создать ряд всплывающих уведомлений, в которой последующих всплывающих уведомлений зависит от ответов на предыдущие уведомления.

![Всплывающее уведомление с ожидающим обновлением](images/toast-pendingupdate.gif)

> [!IMPORTANT]
> **Требуется обновление Desktop Fall Creators Update и библиотека уведомлений версии 2.0.0**: вам необходимо использовать сборку для настольного компьютера 16299 или более поздней версии, чтобы увидеть ожидающие обновления. Для назначения **PendingUpdate** кнопкам необходимо использовать [библиотеку уведомлений набора средств сообщества UWP из NuGet](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) версии 2.0.0 или более поздней. **PendingUpdate** поддерживается только на настольных компьютерах и игнорируется на других устройствах.


## <a name="prerequisites"></a>Что вам понадобится

В этой статье предполагается, что вы знакомы со следующими темами..

- [Создание содержимое всплывающего уведомления](adaptive-interactive-toasts.md)
- [Отправка всплывающего уведомления и обработка активации фона](send-local-toast.md)


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

- [Полный пример кода на GitHub](https://github.com/WindowsNotifications/quickstart-toast-pending-update)
- [Отправка локального всплывающего уведомления и обработка активации](send-local-toast.md)
- [Документация по содержимому всплывающего уведомления](adaptive-interactive-toasts.md)
- [Индикатор хода выполнения всплывающего уведомления](toast-progress-bar.md)