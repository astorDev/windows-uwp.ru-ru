---
title: /users/xuid({xuid})/achievements/{scid}/{achievementid}
assetID: 656a6d63-1a11-b0a5-63d2-2b010abd62e7
permalink: en-us/docs/xboxlive/rest/uri-usersxuidachievementsscidachievementid.html
description: " /users/xuid({xuid})/achievements/{scid}/{achievementid}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 00c577f60b67f15f75c47b5e737ca12819695110
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57599449"
---
# <a name="usersxuidxuidachievementsscidachievementid"></a>/users/xuid({xuid})/achievements/{scid}/{achievementid}
Возвращает сведения о достижении, включая настроенные метаданные и данные конкретного пользователя. 

> [!NOTE] 
> Поддерживается только для платформы. 

 
Доменом для таких URI является `achievements.xboxlive.com`.
 
  * [Параметры URI](#ID4E2)
 
<a id="ID4E2"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | 
| xuid| 64-разрядного целого числа без знака| Xbox пользователя идентификатор (XUID) пользователя, ресурсом которого осуществляется. Должно соответствовать XUID пользователя, прошедшего проверку подлинности.| 
| scid| Код GUID| Уникальный идентификатор конфигурации службы, достижение которого осуществляется доступ.| 
| achievementid| 32-разрядного целого числа без знака| (В пределах указанного SCID) идентификатор достижения, к которому выполняется обращение.| 
  
<a id="ID4EMC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[GET (/users/xuid({xuid})/achievements/{scid}/{achievementid})](uri-usersxuidachievementsscidachievementidget.md)

&nbsp;&nbsp;Получение сведений о достижения.
 
<a id="ID4EWC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EYC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI достижений](atoc-reference-achievementsv2.md)

   