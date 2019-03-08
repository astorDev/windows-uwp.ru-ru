---
title: BatchRequest (JSON)
assetID: 2ca34506-8801-efc5-7c83-3c9ec5572276
permalink: en-us/docs/xboxlive/rest/json-batchrequest.html
description: " BatchRequest (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 51073c71700613edcd7c22e18cc0c00a9222d7e5
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57654159"
---
# <a name="batchrequest-json"></a>BatchRequest (JSON)
Массив свойств для фильтрации сведений о присутствии, включая пользователей, устройств и заголовки.
<a id="ID4EN"></a>


## <a name="batchrequest"></a>BatchRequest

Объект BatchRequest имеет следующей спецификации.

| Участник| Тип| Описание|
| --- | --- | --- |
| пользователи| Массив строк| XUIDs список пользователей, наличие которых вы хотите узнать, с максимальным размером 1100 XUIDs за раз.|
| deviceTypes| Массив строк| Список пользователей, которые вы хотите знать о типов устройств. Если оставить массив пустым, по умолчанию для всех возможных типов устройств (то есть нет отфильтровываются).|
| заголовки| Массив 32-разрядного целого числа без знака| Список устройств типов пользователей, для которого вы хотите узнать о. Если оставить массив пустым, по умолчанию все возможные названия (то есть нет отфильтровываются).|
| level| Строка| Возможные значения: <ul><li>пользователь — get пользователя узлов</li><li>устройства - get пользователя и устройства узлов</li><li>Заголовок: сведения уровня базовый заголовок get</li><li>ALL — получить широкие возможности присутствия и сведения о мультимедиа</li></ul>По умолчанию используется «title».| 
| onlineOnly| Логическое значение| Если это свойство имеет значение true, пакетной операции будет отфильтровать записи для автономных пользователей (включая скрытые). Если он не задан, будет возвращаться автономно и пользователей.|

<a id="ID4EAD"></a>


## <a name="sample-json-syntax"></a>Образец синтаксиса JSON


```json
{
  users:
  [
    "1234567890",
    "4567890123",
    "7890123456"
  ]
}


```


<a id="ID4EJD"></a>


## <a name="see-also"></a>См. также

<a id="ID4ELD"></a>


##### <a name="parent"></a>Parent

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)


<a id="ID4EXD"></a>


##### <a name="reference"></a>Справочные материалы   
