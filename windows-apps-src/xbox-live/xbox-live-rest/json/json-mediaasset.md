---
title: MediaAsset (JSON)
assetID: 858c720a-1648-738b-bb43-f050e7cac19e
permalink: en-us/docs/xboxlive/rest/json-mediaasset.html
description: " MediaAsset (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 308a65b7c049a6aba0405865bab63fb9d28b8506
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57623479"
---
# <a name="mediaasset-json"></a>MediaAsset (JSON)
Ресурсы мультимедиа, связанные с достижением или прохладцей.
<a id="ID4EN"></a>


## <a name="mediaasset"></a>MediaAsset

Объект MediaAsset имеет следующей спецификации.

| Участник| Тип| Описание|
| --- | --- | --- |
| name| Строка| Имя MediaAsset, например «tile01».|
| type| Перечисление MediaAssetType| Тип мультимедиа: <ul><li>значок (0): Значок достижения.</li><li>Искусство (1): Средства цифровой art.</li></ul> | 
| URL-адрес| Строка| URL-адрес MediaAsset.|

<a id="ID4EFC"></a>


## <a name="sample-json-syntax"></a>Образец синтаксиса JSON


```json
{
  "name":"Icon Name",
  "type":"Icon",
  "url":"https://www.xbox.com"
}

```


<a id="ID4EOC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EQC"></a>


##### <a name="parent"></a>Parent

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)
