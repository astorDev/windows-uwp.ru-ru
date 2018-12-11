---
Description: Learn how to pin secondary tiles to taskbar.
title: Закрепление вспомогательных плиток на панели задач
label: Pin secondary tiles to taskbar
template: detail.hbs
ms.date: 11/28/2018
ms.topic: article
keywords: Windows 10, uwp, закрепить на панели задач, вспомогательные плитки, закрепить вспомогательные плитки на панели задач, ярлык
ms.localizationpriority: medium
ms.openlocfilehash: 7ad322fe371b0e1f3605ffb4c29108a15bb28e0c
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8886728"
---
# <a name="pin-secondary-tiles-to-taskbar"></a>Закрепление вспомогательных плиток на панели задач

Так же, как закрепление вспомогательных плиток в меню "Пуск", могут закреплять вспомогательные плитки на панели задач, что дает пользователям быстрый доступ к содержимому в вашем приложении.

<img alt="Taskbar pinning" src="../images/taskbar/pin-secondary-ui.png" width="972"/>

> [!IMPORTANT]
> **Ограниченный доступ к API**: этот API — это функция ограниченного доступа. Чтобы использовать этот API, обратитесь к [taskbarsecondarytile@microsoft.com](mailto:taskbarsecondarytile@microsoft.com?Subject=Limited%20Access%20permission%20to%20use%20secondary%20tiles%20on%20taskbar).

> **Требует октября 2018 г. обновление**: 17763 необходим пакет SDK и использовать сборку 17763 или более поздней версии для закрепить на панели задач.


## <a name="guidance"></a>Руководство

Вспомогательная Плитка представляет собой единообразный и эффективный способ предоставить пользователям прямой доступ к определенным областям приложения. Несмотря на то, что пользователь может выбрать «закреплять» вспомогательную плитку на панели задач или нет, можно закрепить области в приложении, определяются разработчика. Дополнительные рекомендации см. [руководство по вспомогательным плиткам](secondary-tiles-guidance.md).


## <a name="1-determine-if-api-exists-and-unlock-limited-access"></a>1. Определите, существует ли API и разблокировать ограниченный доступ

Более старых устройствах не отображать панель задач, закрепление API-интерфейсы (Если вы хотите использовать более ранние версии Windows 10). Таким образом не следует отображать кнопку PIN-код на этих устройствах, которые не поддерживают закрепление.

Кроме того эта функция заблокирован в разделе ограниченным доступом. Чтобы получить доступ, обратитесь в корпорацию Майкрософт. Вызовы API **[TaskbarManager.RequestPinSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.requestpinsecondarytileasync#Windows_UI_Shell_TaskbarManager_RequestPinSecondaryTileAsync_Windows_UI_StartScreen_SecondaryTile_)**, **[TaskbarManager.IsSecondaryTilePinnedAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.issecondarytilepinnedasync)** и **[TaskbarManager.TryUnpinSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.tryunpinsecondarytileasync)** приведет к сбою с исключение отказано в доступе. Приложения не могут использовать этот API, без разрешения и определение API может измениться в любой момент.

Используйте метод [ApiInformation.IsMethodPresent](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation.ismethodpresent#Windows_Foundation_Metadata_ApiInformation_IsMethodPresent_System_String_System_String_) , чтобы определить, присутствуют ли API-интерфейсы. А затем воспользоваться **[LimitedAccessFeatures](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures)** API разблокирование API.

```csharp
if (ApiInformation.IsMethodPresent("Windows.UI.Shell.TaskbarManager", "RequestPinSecondaryTileAsync"))
{
    // API present!
    // Unlock the pin to taskbar feature
    var result = LimitedAccessFeatures.TryUnlockFeature(
        "com.microsoft.windows.secondarytilemanagement",
        "<tokenFromMicrosoft>",
        "<publisher> has registered their use of com.microsoft.windows.secondarytilemanagement with Microsoft and agrees to the terms of use.");

    // If unlock succeeded
    if ((result.Status == LimitedAccessFeatureStatus.Available) ||
        (result.Status == LimitedAccessFeatureStatus.AvailableWithoutToken))
    {
        // Continue
    }
    else
    {
        // Don't show pin to taskbar button or call any of the below APIs
    }
}

else
{
    // Don't show pin to taskbar button or call any of the below APIs
}
```


## <a name="2-get-the-taskbarmanager-instance"></a>2. Получите экземпляр TaskbarManager

Приложения UWP могут работать на различных устройствах, и не все из них поддерживают панель задач. Сейчас ее поддерживают только настольные компьютеры. Кроме того наличие панели задач могут поступать и перейти. Чтобы проверить, является ли панель задач в данный момент, вызовите метод **[TaskbarManager.GetDefault](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.getdefault)** и проверьте, что экземпляр, возвращаемый не равно null. Не отображать кнопку PIN-код, если отсутствует панели задач.

Мы рекомендуем удерживать на экземпляр в течение одной операции, например закрепления, а затем перетаскивая новый экземпляр в следующий раз, необходимо сделать другой операции.

```csharp
TaskbarManager taskbarManager = TaskbarManager.GetDefault();

if (taskbarManager != null)
{
    // Continue
}
else
{
    // Taskbar not present, don't display a pin button
}
```


## <a name="3-check-whether-your-tile-is-currently-pinned-to-the-taskbar"></a>3. Проверьте, закреплена ли в данный момент плитки на панели задач

Если Плитка уже закреплена, вместо этого следует Показать "открепить". Чтобы проверить, закреплена ли Плитка в данный момент можно использовать метод **[IsSecondaryTilePinnedAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.issecondarytilepinnedasync)** (пользователи могут открепить его в любое время). При использовании этого метода вы передаете **TileId** плитки, чтобы узнать, закреплена.

```csharp
if (await taskbarManager.IsSecondaryTilePinnedAsync("myTileId"))
{
    // The tile is already pinned. Display the unpin button.
}

else 
{
    // The tile is not pinned. Display the pin button.
}
```


## <a name="4-check-whether-pinning-is-allowed"></a>4. Проверьте, разрешен ли закрепление

Закрепление на панели задач можно отключить с помощью групповой политики. Свойство [TaskbarManager.IsPinningAllowed](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.ispinningallowed) позволяет проверить, разрешен ли закрепление.

Когда пользователь нажимает кнопку PIN-код, необходимо проверить это свойство, и если оно является "false", должно отображаться диалоговое окно сообщения, информирующее пользователя, закрепление не поддерживается на этом компьютере.

```csharp
TaskbarManager taskbarManager = TaskbarManager.GetDefault();
if (taskbarManager == null)
{
    // Display message dialog informing user that taskbar is no longer present, and then hide the button
}

else if (taskbarManager.IsPinningAllowed == false)
{
    // Display message dialog informing user pinning is not allowed on this machine
}

else
{
    // Continue pinning
}
```


## <a name="5-construct-and-pin-your-tile"></a>5. Создание, и закрепить плитки

Пользователь нажал кнопку PIN-код, и вы определили, что API-интерфейсы присутствуют, панель задач присутствует и закрепление разрешено … до PIN-код!

Во-первых создайте сокет вспомогательной плитке, так же, как при закреплении меню "Пуск". Подробнее о свойствах вспомогательной плитки, читая [Закрепление вспомогательных плиток в меню "Пуск"](secondary-tiles-pinning.md). Тем не менее при закреплении на панели задач, в дополнение к ранее необходимые свойства, Square44x44Logo (это логотип, используемых задач) также является обязательным. В противном случае будет создано исключение.

Затем передайте методу **[RequestPinSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.requestpinsecondarytileasync)** плитки. Так как это в разделе ограниченным доступом, это не будет отображать диалоговое окно подтверждения и не требует потока пользовательского интерфейса. Однако в будущем при это открывается за пределы ограниченным доступом, вызывающие методы использования ограниченным доступом не будет получать диалогового окна и необходимо использовать поток пользовательского интерфейса.

```csharp
// Initialize the tile (all properties below are required)
SecondaryTile tile = new SecondaryTile("myTileId");
tile.DisplayName = "PowerPoint 2016 (Remote)";
tile.Arguments = "app=powerpoint";
tile.VisualElements.Square44x44Logo = new Uri("ms-appdata:///AppIcons/PowerPoint_Square44x44Logo.png");
tile.VisualElements.Square150x150Logo = new Uri("ms-appdata:///AppIcons/PowerPoint_Square150x150Logo.png");

// Pin it to the taskbar
bool isPinned = await taskbarManager.RequestPinSecondaryTileAsync(tile);
```

Этот метод возвращает логическое значение, указывающее, закреплен ли теперь плитки на панели задач. Если Плитка уже закреплена, метод обновляет существующую плитку и возвращает значение true. Если закрепление не поддерживается или задач не поддерживается, метод возвращает значение false.


## <a name="enumerate-tiles"></a>Перечисление плитки

Чтобы просмотреть все плитки, которые вы создали и по-прежнему закреплены участок (меню "Пуск", панели задач или оба), используйте **[FindAllAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.findallasync)**. Впоследствии можно проверить закреплено ли эти плитки на панели задач и меню "Пуск". Если поверхности не поддерживается, эти методы возвращают значение false.

```csharp
var taskbarManager = TaskbarManager.GetDefault();
var startScreenManager = StartScreenManager.GetDefault();

// Look through all tiles
foreach (SecondaryTile tile in await SecondaryTile.FindAllAsync())
{
    if (taskbarManager != null && await taskbarManager.IsSecondaryTilePinnedAsync(tile.TileId))
    {
        // Tile is pinned to the taskbar
    }

    if (startScreenManager != null && await startScreenManager.ContainsSecondaryTileAsync(tile.TileId))
    {
        // Tile is pinned to Start
    }
}
```


## <a name="update-a-tile"></a>Обновление плитки

Чтобы обновить уже закрепления плитки, можно использовать метод [**SecondaryTile.UpdateAsync**](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.updateasync) , как описано в [обновление вспомогательной плитки](secondary-tiles-pinning.md#updating-a-secondary-tile).


## <a name="unpin-a-tile"></a>Откреплять плитки

Ваше приложение должно предоставлять "открепить", закреплена ли Плитка в данный момент. Чтобы открепить плитки, просто вызовите метод **[TryUnpinSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.tryunpinsecondarytileasync)**, передав **TileId** из вспомогательной плитки, которые хотели бы открепил.

Этот метод возвращает логическое значение, указывающее, закреплен ли больше не плитки на панели задач. Если Плитка не закреплена в первую очередь, также возвращает значение true. Если откреплять не был включен, этот метод возвращает значение false.

Если Плитка только закреплена на панели задач, так как он больше не закреплено в любом будут удалены плитки.

```csharp
var taskbarManager = TaskbarManager.GetDefault();
if (taskbarManager != null)
{
    bool isUnpinned = await taskbarManager.TryUnpinSecondaryTileAsync("myTileId");
}
```


## <a name="delete-a-tile"></a>Удаление плитка

Если вы хотите откреплять плитки из любой точки (меню "Пуск", панели задач), используйте метод **[RequestDeleteAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.requestdeleteasync)** .

Это подходит для случаев, когда содержимое, закрепленные пользователем требование больше не применяется. Например если приложение позволяет закрепить ноутбук к меню "Пуск" и панели задач, а затем пользователь удаляет разделе "заметки", необходимо просто удалить плитки, связанные с заметками.

```csharp
// Initialize a secondary tile with the same tile ID you want removed.
// Or, locate it with FindAllAsync()
SecondaryTile toBeDeleted = new SecondaryTile(tileId);

// And then delete the tile
await toBeDeleted.RequestDeleteAsync();
```


## <a name="unpin-only-from-start"></a>Открепить только из меню "Пуск"

Если вы хотите только открепить вспомогательную плитку из меню "Пуск", сохраняя при этом его на панели задач, можно вызвать метод **[StartScreenManager.TryRemoveSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.startscreenmanager.tryremovesecondarytileasync)** . Если он больше не закреплен на других поверхностях аналогично удаление плитки.

Этот метод возвращает логическое значение, указывающее, закреплен ли больше не плитку в меню "Пуск". Если Плитка не закреплена в первую очередь, также возвращает значение true. Если откреплять не было разрешено или меню "Пуск" не поддерживается, этот метод возвращает значение false.

```csharp
await StartScreenManager.GetDefault().TryRemoveSecondaryTileAsync("myTileId");
```


## <a name="resources"></a>Ресурсы

* [Класс TaskbarManager](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager)
* [Закрепление вспомогательных плиток в меню "Пуск"](secondary-tiles-pinning.md)