---
author: mijacobs
Description: You can programmatically pin your app to the taskbar,  bnd you can check if it's currently pinned.
title: Закрепление приложения на панели задач
template: detail.hbs
ms.author: mijacobs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, панель задач, диспетчер панели задач, закрепление на панели задач, основная плитка
ms.localizationpriority: medium
ms.openlocfilehash: 47fcd1f9d090c49ecbd49e05696b33f789973160
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "7167220"
---
# <a name="pin-your-app-to-the-taskbar"></a>Закрепление приложения на панели задач

Вы можете программно закрепить приложение на панели задач так же, как [приложение в меню "Пуск"](tiles-and-notifications/primary-tile-apis.md). Вы также можете проверить, закреплено ли приложение в данный момент и допускает ли панель задач закрепление. 

![Панель задач](images/taskbar/taskbar.png)

> [!IMPORTANT]
> **Требуется обновление Fall Creators Update**. Обратите внимание, что для использования API панели задач необходим пакет SDK 16299 и сборка 16299 или более поздней версии.

> **Важные API-интерфейсы**: [класс TaskbarManager](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager) 


## <a name="when-should-you-ask-the-user-to-pin-your-app-to-the-taskbar"></a>Когда следует попросить пользователя закрепить приложение на панели задач? 

[Класс TaskbarManager](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager) позволяет попросить пользователя закрепить приложение на панели задач, при этом пользователь должен утвердить запрос. Вы приложили много усилий для проектирования удобного приложения, и теперь вы можете попросить пользователя закрепить его на панели задач. Но прежде чем перейти в код, при создании интерфейса необходимо иметь в виду следующие факторы.

* **Проектируйте** плавные и легко исчезающие элементы в приложении с помощью четкого вызова действия "Закрепить на панели задач". Не используйте диалоговые окна и всплывающие элементы для этой цели. 
* **Ясно** опишите значение приложения, перед тем как просить пользователя закрепить его.
* **Не** просите пользователя закрепить приложение, если плитка уже закреплена или устройство не поддерживает ее. (В этой статье объясняется, как определить, поддерживается ли закрепление.)
* **Не** просите пользователей много раз закрепить ваше приложение (им, скорее всего, это не понравится).
* **Не** вызывайте API закрепления без явного взаимодействия с пользователем или при сворачивании либо неоткрытии приложения.


## <a name="1-check-whether-the-required-apis-exist"></a>1. Проверьте, существует ли требуемые API.

Если ваше приложение поддерживает более ранние версии Windows 10, необходимо проверить, доступен ли класс TaskbarManager. Вы можете использовать [метод ApiInformation.IsTypePresent](https://docs.microsoft.com/en-us/uwp/api/windows.foundation.metadata.apiinformation#Windows_Foundation_Metadata_ApiInformation_IsTypePresent_System_String_) для этой проверки. Если класс TaskbarManager недоступен, исключите любые вызовы API.

```csharp
if (ApiInformation.IsTypePresent("Windows.UI.Shell.TaskbarManager"))
{
    // Taskbar APIs exist!
}

else
{
    // Older version of Windows, no taskbar APIs
}
```


## <a name="2-check-whether-taskbar-is-present-and-allows-pinning"></a>2. Проверьте, присутствует ли панель задач и допускает ли она закрепление.

Приложения UWP могут работать на различных устройствах, и не все из них поддерживают панель задач. Сейчас ее поддерживают только настольные компьютеры. 

Даже если панель задач доступна, групповая политика на компьютере пользователя может запрещать закрепление элементов на панели задач. Поэтому прежде чем пытаться закрепить приложение, необходимо проверить, поддерживается ли закрепление на панели задач. [Свойство TaskbarManager.IsPinningAllowed](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.IsPinningAllowed) возвращает значение true, если панель задач присутствует и разрешает закрепление элементов. 

```csharp
// Check if taskbar allows pinning (Group Policy can disable it, or some device families don't have taskbar)
bool isPinningAllowed = TaskbarManager.GetDefault().IsPinningAllowed;
```

> [!NOTE]
> Если вы не собираетесь закреплять приложение на панели задач и просто хотите узнать, доступна ли панель задач, используйте [свойство TaskbarManager.IsSupported](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.IsSupported).


## <a name="3-check-whether-your-app-is-currently-pinned-to-the-taskbar"></a>3. Проверьте, не закреплено ли ваше приложение на панели задач в данный момент.

Очевидно, что нет смысла просить пользователя закрепить приложение на панели задач, если оно уже закреплено. Вы можете использовать [метод TaskbarManager.IsCurrentAppPinnedAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.IsCurrentAppPinnedAsync), чтобы проверить, закреплено ли приложение, перед тем как обращаться к пользователю.

```csharp
// Check whether your app is currently pinned
bool isPinned = await TaskbarManager.GetDefault().IsCurrentAppPinnedAsync();

if (isPinned)
{
    // The app is already pinned--no point in asking to pin it again!
}
else 
{
    //The app is not pinned. 
}
```


##  <a name="4-pin-your-app"></a>4. Закрепите ваше приложение.

Если панель задач присутствует, закрепление разрешено и приложение в настоящее время не закреплено, вы можете показать подсказку, чтобы сообщить пользователям, что они могут закрепить ваше приложение. Например, можно отобразить значок закрепления в интерфейсе, который пользователь может щелкнуть. 

Если пользователь щелкнет элемент закрепления в интерфейсе, вы вызовите [метод TaskbarManager.RequestPinCurrentAppAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.RequestPinCurrentAppAsync). После этого появится диалоговое окно, где пользователю будет предложено подтвердить закрепление приложения на панели задач.

> [!IMPORTANT]
> Метод необходимо вызывать из потока пользовательского интерфейса переднего плана, в противном случае будет вызвано исключение.

```csharp
// Request to be pinned to the taskbar
bool isPinned = await TaskbarManager.GetDefault().RequestPinCurrentAppAsync();
```

![Диалоговое окно закрепления](images/taskbar/pin-dialog.png)

Этот метод возвращает логическое значение, которое указывает, закреплено ли теперь приложение на панели задач. Если приложение уже было закреплено, метод возвращает значение true без отображения диалогового окна для пользователя. Если пользователь нажмет кнопку "Нет" в диалоговом окне или закрепление приложения на панели задач не поддерживается, метод возвращает значение false. В противном случае, пользователь нажал кнопку "Да" и приложение было закреплено, API вернет значение true.


## <a name="resources"></a>Ресурсы

* [Полный пример кода на GitHub](https://github.com/WindowsNotifications/quickstart-pin-to-taskbar)
* [Класс TaskbarManager](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager)
* [Закрепление приложения в меню "Пуск"](tiles-and-notifications/primary-tile-apis.md)