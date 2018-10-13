---
author: andrewleader
Description: Learn how to pin a secondary tile from your UWP app.
title: Закрепление вспомогательных плиток
label: Pin secondary tiles
template: detail.hbs
ms.author: wdg-dev-content
ms.date: 05/25/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, вспомогательные плитки, закрепить, закрепление, краткое руководство, пример кода, пример, secondarytile
ms.localizationpriority: medium
ms.openlocfilehash: 437d149e22f035fdd0cb1f5251a114b6dd4765e4
ms.sourcegitcommit: d10fb9eb5f75f2d10e1c543a177402b50fe4019e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2018
ms.locfileid: "4570145"
---
# <a name="pin-secondary-tiles"></a>Закрепление вспомогательных плиток


В этом разделе описано, как создать вспомогательную плитку для приложения UWP и закрепить ее в меню "Пуск".

![Снимок экрана: вспомогательные плитки](images/secondarytiles.png)

Дополнительные сведения о вспомогательных плитках см. в разделе [Обзор вспомогательных плиток](secondary-tiles.md).


## <a name="add-namespace"></a>Добавление пространства имен

Пространство имен Windows.UI.StartScreen содержит класс SecondaryTile.

```csharp
using Windows.UI.StartScreen;
```


## <a name="initialize-the-secondary-tile"></a>Инициализация вспомогательной плитки

Вспомогательные плитки состоят из нескольких ключевых компонентов.

* **TileId**: уникальный идентификатор, который позволяет идентифицировать плитку среди других вспомогательных плиток.
* **DisplayName**: имя, которые должно отображаться на плитке.
* **Arguments**: аргументы, которые требуется передать в приложение, когда пользователь щелкает плитку.
* **Square150x150Logo**: необходимый логотип, отображаемый на плитке среднего размера (и преобразуемый в плитку меньшего размера, если небольшой логотип не предоставляется).

Вам **НЕОБХОДИМО** предоставить инициализированные значения всех указанных выше свойств, в противном случае возникнет исключение.

Существует множество конструкторов, которые можно использовать, но конструктор, который принимает tileId, displayName, arguments, square150x150Logo и desiredSize поможет убедиться, что заданы все необходимые свойства.

```csharp
// Construct a unique tile ID, which you will need to use later for updating the tile
string tileId = "City" + zipCode;

// Use a display name you like
string displayName = cityName;

// Provide all the required info in arguments so that when user
// clicks your tile, you can navigate them to the correct content
string arguments = "action=viewCity&zipCode=" + zipCode;

// Initialize the tile with required arguments
SecondaryTile tile = new SecondaryTile(
    tileId,
    displayName,
    arguments,
    new Uri("ms-appx:///Assets/CityTiles/Square150x150Logo.png"),
    TileSize.Default);
```


## <a name="optional-add-support-for-larger-tile-sizes"></a>Необязательно: добавьте поддержку крупных плиток

Если вы собираетесь показывать расширенные уведомления на вспомогательной плитке, вам, скорее всего, потребуется разрешить пользователю увеличить или уменьшать размер плитки, чтобы просматривать больше или меньше содержимого.

Чтобы включить широкий и крупный размер плитки, необходимо предоставить объекты *Wide310x150Logo* и *Square310x310Logo*. Кроме того, если это возможно, вам следует предоставить объект *Square71x71Logo* для маленькой плитки (в противном будет уменьшено изображение Square150x150Logo).

Вы также можете предоставить уникальный логотип *Square44x44Logo*, который отображается в правом нижнем углу при наличии уведомления. Если этот объект не предоставить, будет использоваться объект *Square44x44Logo* из основной плитки.

```csharp
// Enable wide and large tile sizes
tile.VisualElements.Wide310x150Logo = new Uri("ms-appx:///Assets/CityTiles/Wide310x150Logo.png");
tile.VisualElements.Square310x310Logo = new Uri("ms-appx:///Assets/CityTiles/Square310x310Logo.png");

// Add a small size logo for better looking small tile
tile.VisualElements.Square71x71Logo = new Uri("ms-appx:///Assets/CityTiles/Square71x71Logo.png");

// Add a unique corner logo for the secondary tile
tile.VisualElements.Square44x44Logo = new Uri("ms-appx:///Assets/CityTiles/Square44x44Logo.png");
```


## <a name="optional-enable-showing-the-display-name"></a>Необязательно: включите показ отображаемого имени

По умолчанию отображаемое имя не будет показано. Чтобы показать отображаемое имя на средней, широкой и крупной плитке, добавьте следующий код.

```csharp
// Show the display name on all sizes
tile.VisualElements.ShowNameOnSquare150x150Logo = true;
tile.VisualElements.ShowNameOnWide310x150Logo = true;
tile.VisualElements.ShowNameOnSquare310x310Logo = true;
```


## <a name="optional-3d-secondary-tiles"></a>Необязательно: трехмерные вспомогательные плитки
Вы можете улучшить вспомогательную плитку для Windows Mixed Reality, добавив трехмерные ресурсы. Пользователи могут размещать трехмерные плитки непосредственно на главной странице Windows Mixed Reality вместо меню "Пуск" при использовании приложения в среде смешанной реальности. Например, вы можете создавать 360-градусные фотосферы, которые привязаны непосредственно к приложению для просмотра панорам, или можете разрешить пользователям размещать трехмерную модель стула из каталога мебели, которая открывает страницу сведений о цене и цветах этого объекта, если он выбран. Сведения о начале работы см. в [документации для разработчиков смешанной реальности](https://developer.microsoft.com/windows/mixed-reality/implementing_3d_deep_links_for_your_app_in_the_windows_mixed_reality_home).



## <a name="pin-the-secondary-tile"></a>Закрепление вспомогательной плитки

Наконец, отправьте запрос, чтобы закрепить плитку. Обратите внимание, что этот метод следует вызывать из потока пользовательского интерфейса. На рабочем столе отобразится диалоговое окно, предлагающее пользователю подтвердить закрепление плитки.

> [!IMPORTANT]
> Если вы создаете классическое приложение для Windows с помощью моста для классических приложений, сначала необходимо выполнить дополнительный шаг, как описано в разделе [Закрепление из классического приложения](secondary-tiles-desktop-pinning.md).

```csharp
// Pin the tile
bool isPinned = await tile.RequestCreateAsync();

// TODO: Update UI to reflect whether user can now either unpin or pin
```


## <a name="check-if-a-secondary-tile-exists"></a>Проверьте, существует ли вспомогательная плитка

Если пользователь посещает страницу в приложении, которая уже закреплена в меню "Пуск", потребуется показать кнопку "Открепить".

Поэтому при отображаемой кнопки необходимо сначала проверить, закреплена ли вспомогательная плитка в данный момент.

```csharp
// Check if the secondary tile is pinned
bool isPinned = SecondaryTile.Exists(tileId);

// TODO: Update UI to reflect whether user can either unpin or pin
```


## <a name="unpinning-a-secondary-tile"></a>Открепление вспомогательной плитки

Если плитка уже закреплена и пользователь нажимает кнопку "Открепить", необходимо открепить (удалить) плитку.

```csharp
// Initialize a secondary tile with the same tile ID you want removed
SecondaryTile toBeDeleted = new SecondaryTile(tileId);

// And then unpin the tile
await toBeDeleted.RequestDeleteAsync();
```


## <a name="updating-a-secondary-tile"></a>Обновление вспомогательной плитки

Если вам необходимо обновить логотипы, отображаемое имя или другие элементы на вспомогательной плитке, вы можете использовать метод *RequestUpdateAsync*.

```csharp
// Initialize a secondary tile with the same tile ID you want to update
SecondaryTile tile = new SecondaryTile(tileId);

// Assign ALL properties, including ones you aren't changing

// And then update it
await tile.UpdateAsync();
```


## <a name="enumerating-all-pinned-secondary-tiles"></a>Перечисление всех закрепленных вспомогательных плиток

Если вам нужно найти все плитки, закрепленные пользователем, вместо *SecondaryTile.Exists* можно также использовать функцию *SecondaryTile.FindAllAsync()*.

```csharp
// Get all secondary tiles
var tiles = await SecondaryTile.FindAllAsync();
```


## <a name="send-a-tile-notification"></a>Отправка уведомления на плитке

Сведения о том, как показывать расширенное содержимое на плитке с помощью уведомлений, см. в разделе [Отправка локального уведомления на плитке](sending-a-local-tile-notification.md).


## <a name="related"></a>Статьи по теме

* [Обзор вспомогательных плиток](secondary-tiles.md)
* [Руководство по вспомогательным плиткам](secondary-tiles-guidance.md)
* [Ресурсы плиток](app-assets.md)
* [Документация по содержимому плиток](create-adaptive-tiles.md)
* [Отправка локального уведомления на плитке](sending-a-local-tile-notification.md)
