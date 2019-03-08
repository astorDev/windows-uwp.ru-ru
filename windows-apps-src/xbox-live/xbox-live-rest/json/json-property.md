---
title: Property (JSON)
assetID: 93de547e-d936-6fcc-92cb-e4dd284dd609
permalink: en-us/docs/xboxlive/rest/json-property.html
description: " Property (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 7e2a721886509c49c60d663d491f8d49bc3c95e9
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57659709"
---
# <a name="property-json"></a>Property (JSON)
Содержит свойства данных, предоставляемый клиентом для критерии запроса подбор игроков.
<a id="ID4EN"></a>


## <a name="property"></a>Свойство

Объект свойств имеет следующей спецификации.

| Участник| Тип| Описание|
| --- | --- | --- |
| id| Строка| Идентификатор для этого свойства.|
| type| 32-разрядное знаковое целое число | Тип свойства. Поддерживаемые значения:: <ul><li>0 = целое число</li><li>1 = строка</li></ul>| 
| value| Строка| Значение этого свойства.|

<a id="ID4EGC"></a>


## <a name="sample-json-syntax"></a>Образец синтаксиса JSON


```json
{
    "id":"1",
    "value":"20",
    "type":0
}

```


<a id="ID4EPC"></a>


## <a name="see-also"></a>См. также

<a id="ID4ERC"></a>


##### <a name="parent"></a>Parent

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)
