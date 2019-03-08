---
title: Reward (JSON)
assetID: d1c92e8a-afbc-22c5-c0b5-6063963f8c4d
permalink: en-us/docs/xboxlive/rest/json-reward.html
description: " Reward (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 1d58d34263e7e0e90091c41c1df4fd5e078f5055
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57607499"
---
# <a name="reward-json"></a>Reward (JSON)
Теперь, связанные с достижением.
<a id="ID4EN"></a>


## <a name="reward"></a>Reward

Теперь объект имеет следующей спецификации.

| Участник| Тип| Описание|
| --- | --- | --- |
| name| Строка| Имя пользовательского интерфейса Reward.|
| Описание| Строка| Описание пользовательского интерфейса Reward.|
| value| Строка| Значение Reward.|
| type| Перечисление RewardType| Тип Reward: <ul><li>Недопустимое значение (0): Был настроен тип reward неизвестные и не поддерживается.</li><li>Игровые достижения (1): Преимущество добавляет точки достижениями игрока.</li><li>inApp (2): Преимущество определяется и доставляется по названию.</li><li>Искусство (3): Преимущество является цифровыми активами.</li></ul> | 
| ValueType| Перечисление ProgressValueDataType| Тип значения. См. в разделе [требование (JSON)](json-requirement.md) Дополнительные сведения.|

<a id="ID4EBD"></a>


## <a name="sample-json-syntax"></a>Образец синтаксиса JSON


```json
{
  "name":null,
  "description":null,
  "value":"10",
  "type":"Gamerscore",
  "valueType":"Int"
}

```


<a id="ID4EKD"></a>


## <a name="see-also"></a>См. также

<a id="ID4EMD"></a>


##### <a name="parent"></a>Parent

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)
