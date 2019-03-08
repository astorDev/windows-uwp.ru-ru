---
title: UserSettings (JSON)
assetID: 17c030cb-05e0-f78e-5ab1-cdbd8b801ceb
permalink: en-us/docs/xboxlive/rest/json-usersettings.html
description: " UserSettings (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 5451c59ab608105677a657ade41154bd2b622f5e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655049"
---
# <a name="usersettings-json"></a>UserSettings (JSON)
Возвращает параметры для текущего пользователя, прошедшего проверку подлинности. 
<a id="ID4EN"></a>

 
## <a name="usersettings"></a>Параметры пользователя
 
Объект UserSettings имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| id| 32-разрядного целого числа без знака| Идентификатор параметра.| 
| Источник| 32-разрядного целого числа без знака| Представляет источник параметра. | 
| titleId| 32-разрядного целого числа без знака| Идентификатор заголовок, связанный с параметром. | 
| value| Массив 8-разрядное целое число без знака| Представляет значение параметра. Получение параметров клиентов необходимо понимать формат представления, чтобы иметь возможность считывать данные. | 
  
<a id="ID4EJC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
   "id":268697600,
   "source":1,
   "titleId":1297287259,
   "value":"CAAAAA=="
}
    
```

  
<a id="ID4ESC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EUC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   