---
title: LastSeenRecord (JSON)
assetID: 6a93202c-801c-03c6-8386-6acd0f366780
permalink: en-us/docs/xboxlive/rest/json-lastseenrecord.html
description: " LastSeenRecord (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: e06de31cabaedb68ed57d3d4f2ff30614ceb6317
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57613379"
---
# <a name="lastseenrecord-json"></a>LastSeenRecord (JSON)
Сведения о при прежнее пользователя, доступных в том случае, когда у пользователя есть нет допустимых DeviceRecord системы. 
<a id="ID4EN"></a>

 
## <a name="lastseenrecord"></a>LastSeenRecord
 
Объект LastSeenRecord имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| deviceType| Строка| Тип устройства, на котором пользователь выполнял последнего отсутствует.| 
| titleId| 32-разрядного целого числа без знака| Идентификатор заголовка, на котором пользователь выполнял последнего отсутствует.| 
| имя_шаблона| Строка| Имя заголовка, на котором пользователь выполнял последнего отсутствует.| 
| Метка времени| DateTime| Метка времени UTC, указывающее, когда пользователь был последний присутствует.| 
  
<a id="ID4EHC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
  deviceType:W8,    
  titleId:"23452345",
  titleName:"My Awesome Game",
  timestamp:"2012-09-17T07:15:23.4930000"
}
    
```

  
<a id="ID4EQC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ESC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4E5C"></a>

 
##### <a name="reference"></a>Справочные материалы   