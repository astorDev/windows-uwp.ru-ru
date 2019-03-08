---
title: PresenceRecord (JSON)
assetID: 414e6ef5-f7bd-70d0-7386-7aa1c3a56e21
permalink: en-us/docs/xboxlive/rest/json-presencerecord.html
description: " PresenceRecord (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 6d531352c4336e00c93a91e7c945602ab69695f2
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57622589"
---
# <a name="presencerecord-json"></a>PresenceRecord (JSON)
Данные о присутствии отдельного пользователя.
<a id="ID4EN"></a>


## <a name="presencerecord"></a>PresenceRecord

Объект PresenceRecord имеет следующей спецификации.

| Участник| Тип| Описание|
| --- | --- | --- |
| xuid| Строка| Xbox пользователя идентификатор (XUID) целевого пользователя. Наличие данных, предоставленных — для этого пользователя.|
| устройств| Массив [DeviceRecord](json-devicerecord.md)| Список записей устройства пользователя.|
| Состояние| Строка| Действие пользователя в Xbox LIVE. Возможные значения: <ul><li>Онлайн: Пользователь имеет по крайней мере одно устройство записи.</li><li>Сейчас: Пользователь является со знаком в Xbox LIVE, но не активен, в названии ни одного.</li><li>Автономный режим: Пользователь не присутствует на любом устройстве.</li></ul> | 
| lastSeen| [LastSeenRecord](json-lastseenrecord.md)| Сведения о последней просмотренной доступна только в случае, когда у пользователя есть нет допустимых DeviceRecords. Если объект был удален из кэша, его данных может быть не возвращено, так как отсутствует постоянное хранилище.|

<a id="ID4E2C"></a>


## <a name="sample-json-syntax"></a>Образец синтаксиса JSON


```json
{
  xuid:"0123456789",
  state:"online",
  devices:
  [{
    type:"D",
    titles:
    [{
      id:"12341234",
      name:"Contoso 5",
      state:"active",
      placement:"fill",
      timestamp:"2012-09-17T07:15:23.4930000",
      activity:
      {
        richPresence:"Team Deathmatch on Nirvana"
      }
    },
    {
      id:"12341235",
      name:"Contoso Waypoint",
      timestamp:"2012-09-17T07:15:23.4930000",
      placement:"snapped",
      state:"active",
      activity:
      {
        richPresence:"Using radar"
      }
    }]
  },
  {
    type:"W8",
    titles:
    [{
      id:"23452345",
      name:"Contoso Gamehelp",
      state:"active",
      placement:"full",
      timestamp:"2012-09-17T07:15:23.4930000",
      activity:
      {
        richPresence:"Nirvana page"
      }
    }]
  }]
}

```


<a id="ID4EED"></a>


## <a name="see-also"></a>См. также

<a id="ID4EGD"></a>


##### <a name="parent"></a>Parent

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)


<a id="ID4EQD"></a>


##### <a name="reference"></a>Справочные материалы

[POST (/ users/пакетной службы)](../uri/presence/uri-usersbatchpost.md)

 [Получение (/ users/me)](../uri/presence/uri-usersmeget.md)

 [DELETE (/users/xuid({xuid})/devices/current/titles/current)](../uri/presence/uri-usersxuiddevicescurrenttitlescurrentdelete.md)

 [GET (/users/xuid({xuid}))](../uri/presence/uri-usersxuidget.md)
