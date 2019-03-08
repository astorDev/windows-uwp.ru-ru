---
title: GameResult (JSON)
assetID: 43d863c0-2179-ae46-5d4a-2f08cd44b667
permalink: en-us/docs/xboxlive/rest/json-gameresult.html
description: " GameResult (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: b408b1aaae5e6f54958a016575c4a2c37765f1e9
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57602339"
---
# <a name="gameresult-json"></a>GameResult (JSON)
Объект JSON, представляющий данные, которые описывают результаты игр сеанса. 
<a id="ID4EN"></a>

  
 
Объект GameResult JSON имеет следующие члены.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| blob| Массив 8-разрядных целых чисел без знака| Пользовательские результирующие title конкретных данных.| 
| Результат| Строка| Результат игрока участие в сеансе игр. Допустимые значения: «Win», «Потери» или «Привязать». | 
| оценка| 64-разрядное знаковое целое число| Оценка получения проигрывателя в сеансе игр.| 
| время| 64-разрядное знаковое целое число| Игрока время для игр сеанса.| 
| xuid| 64-разрядного целого числа без знака| Идентификатор пользователя Xbox проигрывателя, к которому применяются результаты.| 
  
<a id="ID4EPC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
   "xuid": 2533274790412952,
   "outcome": "Win",
   "score": 100
}
    
```

  
<a id="ID4EYC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E1C"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   