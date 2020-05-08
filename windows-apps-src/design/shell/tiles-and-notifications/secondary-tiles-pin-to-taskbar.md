---
Description: Сведения о закреплении дополнительных плиток на панели задач.
title: Закрепление дополнительных плиток на панели задач
label: Pin secondary tiles to taskbar
template: detail.hbs
ms.date: 11/28/2018
ms.topic: article
keywords: Windows 10, UWP, закрепление на панели задач, вторичная плитка, закрепление дополнительных плиток на панели задач, ярлык
ms.localizationpriority: medium
ms.openlocfilehash: 5d4041faf2fbc729291da902e66be1e0979f9d97
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82971019"
---
# <a name="pin-secondary-tiles-to-taskbar"></a>Закрепление дополнительных плиток на панели задач

Так же, как закрепление вторичных плиток для начала, можно закрепить дополнительные плитки на панели задач, предоставляя пользователям быстрый доступ к содержимому в приложении.

<img alt="Taskbar pinning" src="../images/taskbar/pin-secondary-ui.png" width="972"/>

> [!IMPORTANT]
> **API с ограниченным доступом**: Этот API является функцией ограниченного доступа. Чтобы использовать этот API, обратитесь по [taskbarsecondarytile@microsoft.com](mailto:taskbarsecondarytile@microsoft.com?Subject=Limited%20Access%20permission%20to%20use%20secondary%20tiles%20on%20taskbar)адресу.

> **Требуется обновление за октябрь 2018**: необходимо выбрать пакет SDK 17763 и запустить сборку 17763 или более позднюю версию для закрепления на панели задач.


## <a name="guidance"></a>Руководство

Вторичная плитка предоставляет пользователям единообразный и эффективный способ прямого доступа к конкретным областям внутри приложения. Хотя пользователь выбирает, следует ли «закрепить» дополнительную плитку на панели задач, области также прикрепляемые в приложении определяются разработчиком. Дополнительные рекомендации см. в [руководстве по дополнительным плиткам](secondary-tiles-guidance.md).


## <a name="1-determine-if-api-exists-and-unlock-limited-access"></a>1. Определите, существует ли API, и разблокируйте ограниченный доступ.

У старых устройств нет API-интерфейсов, закреплять панель задач (при использовании более ранних версий Windows 10). Поэтому не следует отображать на этих устройствах кнопку закрепления, которая не поддерживает закрепление.

Кроме того, эта функция заблокирована в ограниченном доступе. Чтобы получить доступ, обратитесь в корпорацию Майкрософт. Вызовы API в **[таскбарманажер. рекуестпинсекондаритилеасинк](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.requestpinsecondarytileasync#Windows_UI_Shell_TaskbarManager_RequestPinSecondaryTileAsync_Windows_UI_StartScreen_SecondaryTile_)**, **[таскбарманажер. иссекондаритилепиннедасинк](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.issecondarytilepinnedasync)** и **[TaskbarManager. TryUnpinSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.tryunpinsecondarytileasync)** завершатся ошибкой с исключением "отказано в доступе". Приложениям запрещено использовать этот API без разрешения, и определение API может измениться в любое время.

Используйте метод [апиинформатион. исмесодпресент](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation.ismethodpresent#Windows_Foundation_Metadata_ApiInformation_IsMethodPresent_System_String_System_String_) , чтобы определить наличие API-интерфейсов. А затем используйте API **[лимитедакцессфеатурес](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures)** , чтобы попытаться разблокировать API.

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


## <a name="2-get-the-taskbarmanager-instance"></a>2. получение экземпляра Таскбарманажер

Приложения Windows могут работать на различных устройствах. не все из них поддерживают панель задач. Сейчас ее поддерживают только настольные компьютеры. Кроме того, может возникнуть присутствие панели задач. Чтобы проверить, имеется ли в данный момент панель задач, вызовите метод **[таскбарманажер. методом по умолчанию](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.getdefault)** и убедитесь, что возвращаемый экземпляр имеет значение, не равное NULL. Не отображать кнопку с закреплениями, если панель задач отсутствует.

Мы рекомендуем держать экземпляр на время выполнения одной операции, например закрепления, а затем заменяя новый экземпляр, в следующий раз, когда потребуется выполнить еще одну операцию.

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


## <a name="3-check-whether-your-tile-is-currently-pinned-to-the-taskbar"></a>3. Проверьте, закреплена ли плитка на панели задач

Если плитка уже закреплена, вместо нее должна отобразиться кнопка "Открепить". Вы можете использовать метод **[иссекондаритилепиннедасинк](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.issecondarytilepinnedasync)** для проверки того, закреплена ли плитка в данный момент (пользователи могут открепить ее в любое время). В этом методе вы передаете **тилеид** плитки, которую вы хотите закрепить.

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


## <a name="4-check-whether-pinning-is-allowed"></a>4. Проверьте, разрешено ли закрепление

Закрепление на панели задач можно отключить с помощью групповая политика. Свойство [таскбарманажер. испиннингалловед](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.ispinningallowed) позволяет проверить, разрешено ли закрепление.

Когда пользователь нажимает кнопку "закрепить", следует проверить это свойство и, если оно ложно, появится диалоговое окно сообщения, информирующее пользователя о том, что закрепление не разрешено на этом компьютере.

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


## <a name="5-construct-and-pin-your-tile"></a>5. Создание и закрепление плитки

Пользователь щелкнул кнопку закрепить, и вы определили, что у вас есть интерфейсы API, есть панель задач, а закрепление разрешено... время на закрепление!

Во-первых, создайте вторичную плитку так же, как при закрепления для запуска. Дополнительные сведения о дополнительных свойствах плитки см. в статье [Закрепление дополнительных плиток для запуска](secondary-tiles-pinning.md). Однако при закреплении на панели задач в дополнение к ранее необходимым свойствам также требуется Square44x44Logo (это логотип, используемый панелью задач). В противном случае будет создаваться исключение.

Затем передайте плитку в метод **[рекуестпинсекондаритилеасинк](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.requestpinsecondarytileasync)** . Так как это ограниченный доступ, при этом не отображается диалоговое окно подтверждения и не требуется поток пользовательского интерфейса. Но в будущем, когда эта возможность открывается за пределами ограниченного доступа, вызывающие объекты, не использующие ограниченный доступ, получат диалоговое окно и должны использовать поток пользовательского интерфейса.

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

Этот метод возвращает логическое значение, указывающее, закреплена ли плитка на панели задач. Если плитка уже закреплена, метод обновляет существующую плитку и возвращает значение true. Если закрепление не разрешено или панель задач не поддерживается, метод возвращает значение false.


## <a name="enumerate-tiles"></a>Перечисление плиток

Чтобы просмотреть все созданные плитки, которые все еще закреплены в любом месте ("Пуск", "панель задач" или "оба"), используйте **[финдалласинк](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.findallasync)**. Затем можно проверить, закреплены ли эти плитки на панели задач и/или в начале. Если поверхность не поддерживается, эти методы возвращают значение false.

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

Чтобы обновить уже закрепленную плитку, можно использовать метод [**секондаритиле. UpdateAsync**](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.updateasync) , как описано в разделе [Обновление вторичной плитки](secondary-tiles-pinning.md#updating-a-secondary-tile).


## <a name="unpin-a-tile"></a>Открепить плитку

Приложение должно предоставить кнопку "Открепить", если плитка в данный момент закреплена. Чтобы открепить плитку, просто вызовите **[трюнпинсекондаритилеасинк](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.tryunpinsecondarytileasync)**, передав **тилеид** вспомогательной плитки, которую вы хотите отменить.

Этот метод возвращает логическое значение, указывающее, будет ли плитка перестает быть закрепленной на панели задач. Если плитка не была закреплена на первом месте, это также возвращает значение true. Если функция закрепления не разрешена, возвращается значение false.

Если плитка закреплена только на панели задач, это приведет к удалению плитки, так как она больше не закреплена в любом месте.

```csharp
var taskbarManager = TaskbarManager.GetDefault();
if (taskbarManager != null)
{
    bool isUnpinned = await taskbarManager.TryUnpinSecondaryTileAsync("myTileId");
}
```


## <a name="delete-a-tile"></a>Удаление плитки

Если вы хотите открепить плитку из любой области (Start, панель задач), используйте метод **[рекуестделетеасинк](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.requestdeleteasync)** .

Это подходит для случаев, когда содержимое, которое пользователь закрепляет, больше не применяется. Например, если приложение позволяет закрепить записную книжку для запуска и панели задач, после чего пользователь удаляет записную книжку, следует просто удалить плитку, связанную с записной книжкой.

```csharp
// Initialize a secondary tile with the same tile ID you want removed.
// Or, locate it with FindAllAsync()
SecondaryTile toBeDeleted = new SecondaryTile(tileId);

// And then delete the tile
await toBeDeleted.RequestDeleteAsync();
```


## <a name="unpin-only-from-start"></a>Открепить только от начала

Если вы хотите открепить вторичную плитку от начала, оставляя ее на панели задач, можно вызвать метод **[стартскринманажер. триремовесекондаритилеасинк](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.startscreenmanager.tryremovesecondarytileasync)** . Таким образом, плитка будет удалена, если она больше не закреплена на других поверхностях.

Этот метод возвращает логическое значение, указывающее, будет ли плитка больше не закреплена для запуска. Если плитка не была закреплена на первом месте, это также возвращает значение true. Если функция unpinning не разрешена или не поддерживается, возвращается значение false.

```csharp
await StartScreenManager.GetDefault().TryRemoveSecondaryTileAsync("myTileId");
```


## <a name="resources"></a>Ресурсы

* [Класс TaskbarManager](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager)
* [Закрепите дополнительные плитки для запуска](secondary-tiles-pinning.md)
