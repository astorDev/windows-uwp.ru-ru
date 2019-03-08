---
Description: Узнайте, как закрепить вспомогательные плитки на панели задач.
title: ПИН-код вспомогательных плиток на панели задач
label: Pin secondary tiles to taskbar
template: detail.hbs
ms.date: 11/28/2018
ms.topic: article
keywords: Windows 10, uwp, закрепить на панели задач, вторичная Плитка закрепить вспомогательные плитки на панели задач, ярлык
ms.localizationpriority: medium
ms.openlocfilehash: 7ad322fe371b0e1f3605ffb4c29108a15bb28e0c
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57591979"
---
# <a name="pin-secondary-tiles-to-taskbar"></a>ПИН-код вспомогательных плиток на панели задач

Так же, как закрепления вспомогательные плитки на начальном, вы можете закрепить вспомогательные плитки на панели задач, предоставление пользователям быстрый доступ к содержимому в приложении.

<img alt="Taskbar pinning" src="../images/taskbar/pin-secondary-ui.png" width="972"/>

> [!IMPORTANT]
> **Ограниченный доступ к API**: Этот API — это функция ограниченный доступ. Чтобы использовать этот API, обратитесь в службу [ taskbarsecondarytile@microsoft.com ](mailto:taskbarsecondarytile@microsoft.com?Subject=Limited%20Access%20permission%20to%20use%20secondary%20tiles%20on%20taskbar).

> **Требуется обновление октября 2018**: Вы должны быть предназначены SDK 17763 и выполняться сборка 17763 или более поздней версии для закрепить на панели задач.


## <a name="guidance"></a>Руководство

Вторичная Плитка обеспечивает согласованное эффективный способ для пользователей, для прямого доступа к определенной области в приложении. Несмотря на то, что пользователь выберет ли «закрепить» вторичная Плитка на панели задач, также прикрепляемые областей в приложении определяются разработчиком. Дополнительные рекомендации см. в разделе [рекомендации вторичная Плитка](secondary-tiles-guidance.md).


## <a name="1-determine-if-api-exists-and-unlock-limited-access"></a>1. Определение наличия API и разблокировать ограниченным доступом

Старые устройства нет задач, закрепление API-интерфейсы (если целевой платформой является более старых версиях Windows 10). Таким образом не должен отображать кнопку закрепления на этих устройствах, которые не способны закрепления.

Кроме того эта функция заблокирована в группе с ограниченным доступом. Чтобы получить доступ, обратитесь в Майкрософт. Вызовы API  **[TaskbarManager.RequestPinSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.requestpinsecondarytileasync#Windows_UI_Shell_TaskbarManager_RequestPinSecondaryTileAsync_Windows_UI_StartScreen_SecondaryTile_)**,  **[TaskbarManager.IsSecondaryTilePinnedAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.issecondarytilepinnedasync)** и **[TaskbarManager.TryUnpinSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.tryunpinsecondarytileasync)** завершится ошибкой с исключением отказано в доступе. Приложения не разрешается использовать этот API без разрешения и определение API может измениться в любое время.

Используйте [ApiInformation.IsMethodPresent](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation.ismethodpresent#Windows_Foundation_Metadata_ApiInformation_IsMethodPresent_System_String_System_String_) метод, чтобы определить, присутствуют ли API-интерфейсы. А затем с помощью **[LimitedAccessFeatures](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures)** API попробовать разблокировки API.

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

Приложения UWP могут работать на различных устройствах, и не все из них поддерживают панель задач. Сейчас ее поддерживают только настольные компьютеры. Кроме того наличие панели задач могут поступать и перейти. Чтобы проверить, является ли в данный момент задач, вызовите **[TaskbarManager.GetDefault](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.getdefault)** метод и проверьте, что возвращенный экземпляр не равен null. Не отображать кнопку ПИН-код, если отсутствует панели задач.

Мы рекомендуем напряженной на экземпляр, в течение одной операции, такие как закрепление, а затем перетащив новый экземпляр в следующий раз, необходимо выполнить другую операцию.

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


## <a name="3-check-whether-your-tile-is-currently-pinned-to-the-taskbar"></a>3. Проверьте, закреплен ли в настоящее время плитки на панели задач

Если элемент уже закреплен, можно отображать кнопку изъять вместо этого. Можно использовать **[IsSecondaryTilePinnedAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.issecondarytilepinnedasync)** метод для проверки ли в настоящее время закреплена плитки (пользователи можете открепить его в любое время). В этом методе вы передаете **идентификатор TileId** закрепленные плитки, которые вы хотите узнать.

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


## <a name="4-check-whether-pinning-is-allowed"></a>4. Проверьте, разрешено ли закрепления

Закрепление на панели задач можно отключить с помощью групповой политики. [TaskbarManager.IsPinningAllowed](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.ispinningallowed) свойство позволяет проверить, разрешен ли закрепления.

При нажатии кнопки ПИН-код, необходимо проверить это свойство, и если он имеет значение false, должно отобразиться диалоговое окно сообщения, сообщая пользователю, что закрепление на этом компьютере не допускается.

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


## <a name="5-construct-and-pin-your-tile"></a>5. Создавать и закреплять плитки

Пользователь нажал на кнопку закрепления, и вы определили, присутствуют интерфейсы API, присутствует панель задач, что закрепление допускается... время для ПИН-кода!

Во-первых создания вторичной плитки, так же, как при закреплении начало. Дополнительные сведения о свойствах вторичной плитке путем чтения [ПИН-код вспомогательные плитки на начальном](secondary-tiles-pinning.md). Тем не менее при закреплении на панели задач, в дополнение к ранее обязательных свойств Square44x44Logo (это логотип, используемые на панели задач) также является обязательным. В противном случае будет создано исключение.

Затем передайте плитку, чтобы **[RequestPinSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.requestpinsecondarytileasync)** метод. Так как это выполняется с ограниченным доступом, это не отобразит диалоговое окно подтверждения и не требует поток пользовательского интерфейса. Но в будущем это открытием за пределы ограниченным доступом, вызывающие объекты не использования ограниченный доступ будет получен диалог и обязательно использовать в потоке пользовательского интерфейса.

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

Этот метод возвращает логическое значение, указывающее, закреплен ли теперь плитки на панели задач. Если Плитка была закреплена, метод обновляет существующий элемент и возвращает значение true. Если закрепление не разрешено или панели задач не поддерживается, метод возвращает значение false.


## <a name="enumerate-tiles"></a>Перечислить плитки

Чтобы увидеть все плитки, которые вы создали и по-прежнему являются закрепленные где-нибудь (Start, задач или оба), используйте  **[FindAllAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.findallasync)**. Впоследствии можно проверить ли эти плитки закреплены на панели задач и/или запуска. Если области не поддерживается, эти методы возвращают значение false.

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


## <a name="update-a-tile"></a>Обновления плитки

Чтобы обновить уже закрепленной плитки, можно использовать [ **SecondaryTile.UpdateAsync** ](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.updateasync) метод, как описано в разделе [обновление вторичная Плитка](secondary-tiles-pinning.md#updating-a-secondary-tile).


## <a name="unpin-a-tile"></a>Открепить плитки

Приложения должны предоставлять кнопка изъять, если элемент закреплен в настоящее время. Чтобы открепить плитку, просто вызовите  **[TryUnpinSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager.tryunpinsecondarytileasync)**, передавая **идентификатор TileId** вторичной плитки хотелось бы отменено.

Этот метод возвращает логическое значение, указывающее, закреплен ли больше не плитки на панели задач. Если элемент не был закреплен в первую очередь, также возвращает значение true. Если после освобождения не был разрешен, возвращается false.

Если Плитка была только закреплен на панели задач, так как он больше не закреплен в любом месте будут удалены плитки.

```csharp
var taskbarManager = TaskbarManager.GetDefault();
if (taskbarManager != null)
{
    bool isUnpinned = await taskbarManager.TryUnpinSecondaryTileAsync("myTileId");
}
```


## <a name="delete-a-tile"></a>Удаление плитки

Если вы хотите отменить закрепление плитки из любого расположения (запуск задач), используйте **[RequestDeleteAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.secondarytile.requestdeleteasync)** метод.

Такой вариант подходит для случаев, где содержимое закрепленные пользователь больше не применяется. Например если приложение позволяет закреплять записной книжки для запуска и задач, а затем пользователь удаляет записную книжку, следует просто удалить плитки, связанные с записной книжкой.

```csharp
// Initialize a secondary tile with the same tile ID you want removed.
// Or, locate it with FindAllAsync()
SecondaryTile toBeDeleted = new SecondaryTile(tileId);

// And then delete the tile
await toBeDeleted.RequestDeleteAsync();
```


## <a name="unpin-only-from-start"></a>Открепить только от начального экрана

Если вы только хотите открепить вторичная Плитка с начала, оставив ее на панели задач, можно вызвать **[StartScreenManager.TryRemoveSecondaryTileAsync](https://docs.microsoft.com/uwp/api/windows.ui.startscreen.startscreenmanager.tryremovesecondarytileasync)** метод. Если больше не будет закреплена на любой другой поверхности аналогичным образом будут удалены плитки.

Этот метод возвращает логическое значение, указывающее, закреплен ли больше не плитки на начальном. Если элемент не был закреплен в первую очередь, также возвращает значение true. Если после освобождения Недозволенный или запуска не поддерживается, он возвращает значение false.

```csharp
await StartScreenManager.GetDefault().TryRemoveSecondaryTileAsync("myTileId");
```


## <a name="resources"></a>Ресурсы

* [Класс TaskbarManager](https://docs.microsoft.com/uwp/api/windows.ui.shell.taskbarmanager)
* [Вспомогательные плитки ПИН-код для начала](secondary-tiles-pinning.md)