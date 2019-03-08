---
title: Обзор типов данных
assetID: c154a6fa-e7b2-4652-f6fc-f946f74480e9
permalink: en-us/docs/xboxlive/rest/datatypeoverview.html
description: " Обзор типов данных"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 62932a921d51a988a5533d7ee08f4968bb67a29d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57659659"
---
# <a name="data-type-overview"></a>Обзор типов данных
 
Xbox Live Services используются различные типы данных, связанные с удостоверениями и проверки подлинности. Здесь представлен обзор этих типов.
 
| Тип| Описание| 
| --- | --- | 
| тег игрока| Экран уникальное и понятное имя для пользователя.| 
| Проигрыватель| Объект JSON, содержащий XUID и его тег игрока, также индекс игрока в сеансе (или «рабочих мест»), ли игрок по-прежнему участвует в сеанс и небольших BLOB-объектов настраиваемых данных пользователя.| 
| профиль| Сведения о пользователе через адресов URI профиля и методов HTTP, обычно UserSettings пользователя, но также возможно, включающий gamercard, игрока, XUID и т. д.| 
| настройка| Один из параметров определенного заголовка в объекте UserSettings.| 
| UserClaims| Простой объект JSON, содержащий XUID пользователя и его тег игрока.| 
| Параметры пользователя| Объект JSON, содержащий коллекцию параметров, относящихся к title или настройки для текущего пользователя, прошедшего проверку подлинности. Параметры пользователя может содержать произвольные данные, возможно, связанные с действиями в игре.| 
| XUID| Идентификатор пользователя Xbox для пользователя, уникальный длинное целое без знака. Не должна быть в удобное для восприятия.| 
 
<a id="ID4E6D"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EBE"></a>

 
##### <a name="parent"></a>Parent  

[Дополнительная справка](atoc-xboxlivews-reference-additional.md)

  
<a id="ID4ENE"></a>

 
##### <a name="reference--player-jsonjsonjson-playermd"></a>Справочник по [Player (JSON)](../json/json-player.md)

 [UserClaims (JSON)](../json/json-userclaims.md)

 [UserSettings (JSON)](../json/json-usersettings.md)

   