---
title: /users/xuid({xuid})/scids/{scid}/stats/{statname)/people/{all|favorite}
assetID: 0983dad0-59b7-45b7-505d-603e341fe0cc
permalink: en-us/docs/xboxlive/rest/uri-usersxuidscidstatnamepeople.html
description: " /users/xuid({xuid})/scids/{scid}/stats/{statname)/people/{all|favorite}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 85a6470a64ceef3b154384d1ca859fb28733aad3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57632579"
---
# <a name="usersxuidxuidscidsscidstatsstatnamepeopleallfavorite"></a>/users/xuid({xuid})/scids/{scid}/stats/{statname)/people/{all|favorite}
Обращается к социальных сетей (рангом) "список лидеров".
Доменом для таких URI является `leaderboards.xboxlive.com`.

  * [Параметры URI](#ID4EV)

<a id="ID4EV"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| xuid| Строка| Идентификатор пользователя.|
| scid| Строка| Идентификатор конфигурации службы, содержащего ресурс к которому выполняется доступ.|
| statname| Строка| Уникальный идентификатор пользователя stat ресурса, к которому выполняется доступ.|
| все\|Избранные| перечисление| Следует ли для ранжирования stat значения (показатели) для всех известных контактов текущего пользователя или только тех контактов, которые обозначены как избранные людей с этого пользователя.|

<a id="ID4EOC"></a>


## <a name="valid-methods"></a>Допустимые методы

[GET (/ users/xuid ({xuid}) {scid} /scids/ /stats/ {statname) /people/ {все\|избранные})](uri-usersxuidscidstatnamepeopleget.md)

&nbsp;&nbsp;Возвращает список лидеров социальных сетей, ранжирование stat значения (показатели) для всех известных контактов текущего пользователя или только тех контактов, которые обозначены как избранные людей с этого пользователя.

<a id="ID4EYC"></a>


## <a name="see-also"></a>См. также

<a id="ID4E1C"></a>


##### <a name="parent"></a>Parent

[Идентификаторы URI списки лидеров](atoc-reference-leaderboard.md)
