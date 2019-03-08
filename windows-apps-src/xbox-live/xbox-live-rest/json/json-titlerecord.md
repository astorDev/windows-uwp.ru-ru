---
title: TitleRecord (JSON)
assetID: 8e1bd699-e408-67c8-31da-2d968adfbc21
permalink: en-us/docs/xboxlive/rest/json-titlerecord.html
description: " TitleRecord (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 89baf7e9a737428d492246f1647a561a4a8170cf
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57603989"
---
# <a name="titlerecord-json"></a>TitleRecord (JSON)
Сведения, включая его имя и метку времени последнего изменения. 
<a id="ID4EN"></a>

 
## <a name="titlerecord"></a>TitleRecord
 
TitleRecord должен содержать DeviceRecord или LastSeenRecord, но не может одновременно содержать.
 
Объект TitleRecord имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| id| 32-разрядного целого числа без знака| TitleId записи.| 
| name| Строка| Локализованное имя заголовка.| 
| активность| [ActivityRecord](json-activityrecord.md)| Действие пользователя в заголовке. Возвращается, только если глубина «все».| 
| lastModified| DateTime| Метка времени UTC, время последнего обновления записи.| 
| Размещение| Строка| Размещение приложения в пользовательском интерфейсе. Существует несколько возможностей «заливка», «full», «прикрепленного» или «фон». По умолчанию используется «full» для устройств без возможности для размещения приложений.| 
| Состояние| Строка| Состояние заголовка. Может быть «активно» или «неактивно» (по умолчанию). Заголовок устанавливает состояние на основе критериев, свои собственные действия и бездействия.| 
  
<a id="ID4E6C"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
      id:"12341234",
      name:"Contoso 5",
      state:"active",
      placement:"fill",
      timestamp:"2012-09-17T07:15:23.4930000",
      activity:
      {
        richPresence:"Team Deathmatch on Nirvana"
      }
    }
    
```

  
<a id="ID4EID"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EKD"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4EUD"></a>

 
##### <a name="reference"></a>Справочные материалы 

[POST (/users/xuid({xuid})/devices/current/titles/current)](../uri/presence/uri-usersxuiddevicescurrenttitlescurrentpost.md)

   