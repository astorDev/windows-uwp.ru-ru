---
title: PagingInfo (JSON)
assetID: 645e575d-3e8e-d954-90e6-e51dd83da93b
permalink: en-us/docs/xboxlive/rest/json-paginginfo.html
description: " PagingInfo (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 0e773d73499e79fe23f736a536027932ca1a07b4
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651219"
---
# <a name="paginginfo-json"></a>PagingInfo (JSON)
Содержит сведения разбиение по страницам результаты, возвращаемые на страницах данных. 
<a id="ID4EN"></a>

 
## <a name="paginginfo"></a>pagingInfo
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| continuationToken| Строка| Маркера непрозрачного продолжения, используемый для доступа к следующей страницы результатов. Более 32 символов. Вызывающие объекты могут предоставлять это значение в <b>continuationToken</b> параметр запроса для получения следующего набора элементов в коллекции. Если это свойство имеет <b>null</b>, то нет дополнительных элементов в коллекции. Это свойство является обязательным и предоставляется, даже если коллекция оповещается с <b>skipItems</b>.| 
| totalItems| 32-разрядное знаковое целое число| Общее число элементов в коллекции. Этот параметр не указан, если служба не сможет предоставить в режиме реального времени, в размер коллекции.| 
  
<a id="ID4E4B"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 

```json
{
   "continuationToken":"16354135464161213-0708c1ba-147f-48cc-9ad9-546gaadg648"
   "totalItems":5,
}
    
```

  
<a id="ID4EGC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EIC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

   