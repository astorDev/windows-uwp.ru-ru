---
title: inventoryItem (JSON)
assetID: 446cca28-b2d3-1b84-f973-94065519b391
permalink: en-us/docs/xboxlive/rest/json-inventoryitem.html
description: " inventoryItem (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 240e527258923cff146009810c190e401e0574d0
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57628499"
---
# <a name="inventoryitem-json"></a>inventoryItem (JSON)
Этот элемент инвентаризации core представляет стандартный элемент, на котором может быть предоставлено право.
<a id="ID4EN"></a>


## <a name="inventoryitem"></a>inventoryItem

Объект inventoryItem имеет следующей спецификации.

| Участник| Тип| Описание|
| --- | --- | --- |
| URL-адрес| Строка| Уникальный идентификатор для этого конкретному элементу.|
| ItemType| Строка| Тип элемента. Текущие значения <ul><li><b>Неизвестный</b></li><li><b>Игра</b></li><li><b>Фильма</b></li><li> <b>TVShow</b></li><li><b>MusicVideo</b></li><li><b>GameTrial</b></li><li><b>ViralVideo</b></li><li><b>TVEpisode</b></li><li><b>TVSeason</b></li><li><b>TVSeries</b></li><li><b>VideoPreview</b></li><li><b>Плакат</b></li><li><b>Подкаст</b></li><li><b>Image</b></li><li><b>Иллюстрация</b></li><li><b>ArtistPicture</b></li><li><b>GameContent</b></li><li><b>GameDemo</b></li><li><b>Темы</b></li><li><b>XboxOriginalGame</b></li><li><b>GamerTile</b></li><li><b>ArcadeGame</b></li><li><b>GameConsumable</b></li><li><b>Альбом</b></li><li><b>AlbumDisc</b></li><li><b>AlbumArt</b></li><li><b>GameVideo</b></li><li><b>BackgroundArt</b></li><li><b>TVTrailer</b></li><li><b>GameTrailer</b></li><li><b>VideoShort</b></li><li><b>пакета</b></li><li><b>XnaCommunityGame</b></li><li><b>Рекламные</b></li><li><b>MovieTrailer</b></li><li><b>SlideshowPreviewImage</b></li><li><b>ServerBackedGames</b></li><li><b>Marketplace</b></li><li><b>AvatarItem</b></li><li><b>LiveApp</b></li><li><b>WebGame</b></li><li><b>MobileGame</b></li><li><b>MobilePdlc</b></li><li><b>MobileConsumable</b></li><li><b>Приложение</b></li><li><b>MetroGame</b></li><li><b>MetroGameContent</b></li><li><b>MetroGameConsumable</b></li><li><b>GameLayer</b></li><li><b>GameActivity</b></li><li><b>GameV2</b></li><li><b>SubscriptionV2</b></li><li><b>Подписка</b><br/><br/> **Примечание.** Игры обозначаются **GameV2**, являются расходных **GameConsumable**, и является устойчивой DLC **GameContent**. |
  | Контейнеры | Строка | Это набор «контейнеры», содержащих этот элемент. Элементы, которые принадлежат к конкретному контейнеру можно запрашивать пользователя инвентаризации. Эти контейнеры определяются в том случае, когда элемент добавляется в перечень после покупки. |
  | получить | DateTime | Дата и время, когда элемент был добавлен инвентаризации пользователя. |
  | startDate | DateTime | Дата и время стали или будут доступны для использования этого элемента. |
  | endDate | DateTime | Дата и время стали или станут непригодными для использования этого элемента. |
  | Состояние | Строка | Состояние элемента. Допустимые значения: **включено**, **Suspended**, **истек**, **Canceled**, **Renewed**.  |
  | trial, | Логическое значение | Обязательный. Значение true, если это право использования пробной версии; в противном случае — значение false. Если приобрести пробную версию назначение и затем приобрести полную версию, вы получите оба. |
  | trialTimeRemaining | TimeSpan | Допускает значения NULL. Сколько времени остается включенной пробного периода в минутах. |
  | Машинные команды | Массив | Если элементы обрабатывается, это содержит встроенный представление уникальный идентификатор (ссылка) элемента готовых к использованию инвентаризации, а также ее текущее количество. |

<a id="ID4EMAAC"></a>


## <a name="sample-json-syntax"></a>Образец синтаксиса JSON


```json
inventoryItem {
  "url": string,
  "itemType": "Music" | "Video" | "Game" | "AvatarItem" | "Subscription" | "DLC" | "Consumable" | ...,
  "obtained": DateTime,
  "beginDate": DateTime,
  "endDate": DateTime,
  "state": "Unavailable" | "Available" | "Suspended" | "Expired",
  "trial": true,
  "trialTimeRemaining":"23:12:14",
  ("consumable": {"url": string, "quantity": int})
}

```


<a id="ID4EVAAC"></a>


## <a name="consumable-inventory-item"></a>Элемент готовых к использованию инвентаризации

Готовых к использованию сущностей представляет минимальный набор свойств для элемента, готовых к использованию.

| Участник| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| URL-адрес| Строка| Уникальный идентификатор для конкретному элементу готовых к использованию.|
| quantity| 32-разрядное знаковое целое число| Текущее количество этот элемент инвентаризации.|


```json
consumableInventoryItem {
  "url": string,
  "quantity": int
}

```


<a id="ID4E4BAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4E6BAC"></a>


##### <a name="parent"></a>Parent

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)


<a id="ID4EJCAC"></a>


##### <a name="reference"></a>Справочные материалы

[/Users/Me/Inventory](../uri/marketplace/uri-inventory.md)

 [/ инвентаризации/расходных / {itemID}](../uri/marketplace/uri-inventoryconsumablesitemurl.md)

 [/Inventory/ {itemID}](../uri/marketplace/uri-inventoryitemurl.md)
