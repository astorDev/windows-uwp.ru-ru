---
title: UpdateMetadataRequest (JSON)
assetID: 0bc210e3-c1dc-9267-e322-aadb9f0a074a
permalink: en-us/docs/xboxlive/rest/json-updatemetadatarequest.html
description: " UpdateMetadataRequest (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: a76e4b12e0ffadb112913775b500ac0d39d413d5
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57597769"
---
# <a name="updatemetadatarequest-json"></a>UpdateMetadataRequest (JSON)
Метаданные, которые должны быть обновлены для клипа. 
<a id="ID4EN"></a>

 
## <a name="updatemetadatarequest"></a>UpdateMetadataRequest
 
Объект UpdateMetadataRequest имеет следующей спецификации.
 
| Участник| Тип| Описание| 
| --- | --- | --- | 
| userCaption| Строка| Изменяет введенные пользователем, не Локализованная строка для игр клипа.| 
| visibility| [Перечисление GameClipVisibility](../enums/gvr-enum-gameclipvisibility.md)| Изменяет видимость игр клип, так как она опубликована в системе.| 
| titleData| Строка| Контейнер свойств конкретного заголовка. Максимальный размер: 10 КБ.| 
  
<a id="ID4EBC"></a>

 
## <a name="sample-json-syntax"></a>Образец синтаксиса JSON
 
Изменение коллекции имя пользователя и видимости:
 

```json
{
  "userCaption": "I've changed this 100 Times!",
  "visibility": "Owner"
}

```

 
Изменение только свойства title (это лишь пример, так как схема этого поля является вызывающий объект):
 

```json
{
  "titleData": "{ 'Id': '123456', 'Location': 'C:\\videos\\123456.mp4' }"
}

```

  
<a id="ID4EQC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ESC"></a>

 
##### <a name="parent"></a>Parent 

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)

  
<a id="ID4E3C"></a>

 
##### <a name="reference"></a>Справочные материалы 

[POST (/ users/me/scids / {scid} /clips/ {gameClipId})](../uri/dvr/uri-usersmescidclipsgameclipidpost.md)

   