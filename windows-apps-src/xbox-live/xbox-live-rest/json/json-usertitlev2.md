---
title: UserTitle (JSON)
assetID: 3e5767af-5704-8463-696b-42a7d2a1e231
permalink: en-us/docs/xboxlive/rest/json-usertitlev2.html
description: " UserTitle (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 33901a5bde25fd17072c2b45d587a33209424378
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57623029"
---
# <a name="usertitle-json"></a>UserTitle (JSON)
Содержит название данные пользователя. 
<a id="ID4EN"></a>

 
## <a name="usertitle"></a>UserTitle
 
Объект UserTitle имеет следующей спецификации. Все свойства являются обязательными.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| lastUnlock| DateTime| Время последнего заработанные достижений.| 
| titleId| 32-разрядного целого числа без знака| Уникальный идентификатор для заголовка.| 
| titleVersion| Строка| Версия заголовка.| 
| serviceConfigId| Строка| Идентификатор набора конфигурации основной службы, связанных с заголовком.| 
| titleType| Строка| Тип заголовка.| 
| Платформы| Строка| Поддерживаемые платформы.| 
| name| Строка| Название этого текста. Максимальная длина 22.| 
| earnedAchievements| 32-разрядного целого числа без знака| Количество достижений, полученные за title, включая разблокирован достижений и успешного выполнения задачи.| 
| currentGamerscore| 32-разрядного целого числа без знака| Общее достижениями этот пользователь получил в этом заголовке.| 
| maxGamerscore| 32-разрядного целого числа без знака| Общее возможных достижениями для этого заголовка.| 
  
<a id="ID4EFE"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EHE"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   