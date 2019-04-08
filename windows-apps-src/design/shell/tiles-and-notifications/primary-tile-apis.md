---
Description: Вы можете программно закрепить главную плитку приложения в меню "Пуск" таким же способом, как и дополнительные плитки. Вы можете проверить, закреплена ли плитка в данный момент.
title: API основной плитки
label: Primary tile API's
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp, StartScreenManager, закрепление основной плитки, api основной плитки, проверка закрепления плитки, живая плитка
ms.localizationpriority: medium
ms.openlocfilehash: 04d7c66b358a3a465522ad3b56d8ae926358ae57
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57596199"
---
# <a name="primary-tile-apis"></a>API для работы с основными плитками
 

Интерфейсы API основной плитки позволяют проверить, закреплено ли ваше приложение в меню "Пуск" и закрепить основную плитку вашего приложения.

> [!IMPORTANT]
> **Требуется Creators Update**: Вы должны быть предназначены SDK 15063 и выполняться сборка 15063 или более поздней версии, чтобы с помощью основного элемента API-интерфейсы.

> **Важные API**: [**Класс StartScreenManager**](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.startscreenmanager), [ContainsAppListEntryAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.startscreenmanager#Windows_UI_StartScreen_StartScreenManager_ContainsAppListEntryAsync_Windows_ApplicationModel_Core_AppListEntry_), [RequestAddAppListEntryAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.startscreenmanager#Windows_UI_StartScreen_StartScreenManager_RequestAddAppListEntryAsync_Windows_ApplicationModel_Core_AppListEntry_)


## <a name="when-to-use-primary-tile-apis"></a>Когда следует использовать API-интерфейсы основной плитки

Вы приложили много усилий для проектирования удобного интерфейса основной плитки своего приложения, и теперь вы имеете возможность запрашивать у пользователя, чтобы закрепить ее в меню "Пуск". Но прежде чем перейти в код, при создании интерфейса необходимо иметь в виду следующие факторы.

* **Проектируйте** плавные и легко исчезающие элементы в приложении с помощью четкого вызова действия "Pin Live Tile".
* **Ясно** опишите значение живой плитки приложения, перед тем как просить пользователя закрепить ее.
* **Не** просите пользователя закрепить плитку приложения, если плитка уже закреплена или устройство не поддерживает ее (дополнительные сведения ниже).
* **Не** просите пользователя много раз закрепить плитку вашего приложения (ему, скорее всего, это не понравится).
* **Не** вызывайте API закрепления без явного взаимодействия с пользователем или при сворачивании либо неоткрытии приложения.


## <a name="checking-whether-the-apis-exist"></a>Проверка на наличие API

Если ваше приложение поддерживает более ранние версии Windows 10, необходимо проверить, доступны ли эти API-интерфейсы основной плитки. Вы можете сделать это с помощью ApiInformation. Если интерфейсы API основной плитки недоступны, исключите любые вызовы API.

```csharp
if (ApiInformation.IsTypePresent("Windows.UI.StartScreen.StartScreenManager"))
{
    // Primary tile API's supported!
}
else
{
    // Older version of Windows, no primary tile API's
}
```


## <a name="check-if-start-supports-your-app"></a>Узнайте, поддерживает ли меню "Пуск" приложение

В зависимости от текущего меню "Пуск" и типа приложения, закрепление приложения на в текущем меню "Пуск" может не поддерживаться. Только для настольные и мобильные устройства поддерживают закрепление основной плитки на начальном экране. Таким образом, перед отображением пользовательского интерфейса закрепления или выполнением любого закрепления, необходимо сначала убедиться, что ваше приложение поддерживается для текущего начального экрана. Если оно не поддерживается, не просите пользователя закрепить плитку.

```csharp
// Get your own app list entry
// (which is always the first app list entry assuming you are not a multi-app package)
AppListEntry entry = (await Package.Current.GetAppListEntriesAsync())[0];

// Check if Start supports your app
bool isSupported = StartScreenManager.GetDefault().SupportsAppListEntry(entry);
```


## <a name="check-whether-youre-currently-pinned"></a>Проверка закрепления в настоящее время

Чтобы узнать, закреплена ли основная плитка в настоящее время в меню "Пуск", используйте метод [ContainsAppListEntryAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.startscreenmanager#Windows_UI_StartScreen_StartScreenManager_ContainsAppListEntryAsync_Windows_ApplicationModel_Core_AppListEntry_).

```csharp
// Get your own app list entry
AppListEntry entry = (await Package.Current.GetAppListEntriesAsync())[0];

// Check if your app is currently pinned
bool isPinned = await StartScreenManager.GetDefault().ContainsAppListEntryAsync(entry);
```


##  <a name="pin-your-primary-tile"></a>Закрепление основной плитки

Если основная плитка не закреплена, и ваша плитка поддерживается меню "Пуск", вы можете показать совет пользователям относительно закрепления основной плитки.

> [!NOTE]
> Необходимо вызвать этот API из потока пользовательского интерфейса, пока приложение находится на переднем плане, и этот API следует вызывать только после намеренно пользователь запросил закрепить плитку первичного (например, после щелчка ссылки Да для вашей подсказки о возможности закрепления плитки).

Если пользователь нажимает кнопку, чтобы закрепить основную плитку, вы вызываете метод [RequestAddAppListEntryAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.startscreenmanager#Windows_UI_StartScreen_StartScreenManager_RequestAddAppListEntryAsync_Windows_ApplicationModel_Core_AppListEntry_) для запроса относительно закрепления плитки на начальном экране. Отобразится диалоговое окно подтверждения, чтобы пользователь подтвердил, что он хочет закрепить плитку на начальном экране.

Возвращается логическое значение, указывающее закреплена ли плитка на начальном экране. Если плитка уже закреплена, немедленно вернется значение true без отображения диалогового окна для пользователя. Если пользователь нажмет кнопку "Нет" в диалоговом окне или закрепление плитки на начальном экране не поддерживается, будет возвращено значение false. В противном случае, пользователь нажал кнопку "Да" и плитка была закреплена. API вернет значение true.

```csharp
// Get your own app list entry
AppListEntry entry = (await Package.Current.GetAppListEntriesAsync())[0];

// And pin it to Start
bool isPinned = await StartScreenManager.GetDefault().RequestAddAppListEntryAsync(entry);
```


## <a name="resources"></a>Ресурсы

* [Полный образец кода на GitHub](https://github.com/WindowsNotifications/quickstart-pin-primary-tile)
* [Закрепить на панели задач](../pin-to-taskbar.md)
* [Плитки, индикаторы событий и уведомления](index.md)
* [Документация по адаптивной плитку](create-adaptive-tiles.md)
