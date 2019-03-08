---
title: Обратный поиск видео в EDS
assetID: 773f7a8e-7571-3aec-96d6-478437696ea6
permalink: en-us/docs/xboxlive/rest/edsreverselookup.html
description: " Обратный поиск видео в EDS"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: d535dec8a95eba4d486bfc6946e187e2da66ae49
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598439"
---
# <a name="eds-reverse-lookup-for-video"></a>Обратный поиск видео в EDS
 
  * [Обратный поиск действия](#ID4EQ)
 
<a id="ID4EQ"></a>

 
## <a name="reverse-lookup-steps"></a>Обратный поиск действия
 
Развлечения обнаружения службы (EDS) обратный просмотр поддерживается для всех типов мультимедиа видео (**MediaItemType.Movie**, **MediaItemType.TVSeries**, **MediaItemType.TVEpisode**, **MediaItemType.TVSeason**, и **MediaItemType.TVShow**), а также **MediaItemType.Unknown**.
 
Обратный просмотр требует четыре параметра для передачи: 
   * `idType=ScopedMediaId`
   * `ids=` Идентификатор поставщика мультимедиа
   * `ScopeIdType=Title`
   * `ScopeId=` Идентификатор поставщика title
 
 
Обычно обратного просмотра этапа 2: 
   * Получить идентификатор поставщика мультимедиа (например, из-за обращения сведения), если он не доступен. 

```cpp
GET /media/en-us/details?ids=4eeaf5b4-9af2-56e4-a738-68b48e954494&desiredMediaItemTypes=Movie&desired=Providers
```

 
   * Вызовы для обратного просмотра с помощью **ProviderMediaId** из предыдущего ответа: 

```cpp
GET /media/en-us/details?ids=047d19ca-3a7d-462c-bdbb-163543125583&idType=ScopedMediaId&desiredMediaItemTypes=Movie&fields=all&ScopeIdType=Title&ScopeId=0x5848085B
```

 
  
 
Если **ProviderMediaId** поля не были получены из таблицы РАСПРЕДЕЛЕНИЯ, а затем поле должно быть URL-адреса должны быть переданы правильно EDS.
  
<a id="ID4EOC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EQC"></a>

 
##### <a name="parent"></a>Parent  

[Дополнительная справка](atoc-xboxlivews-reference-additional.md)

  
<a id="ID4E3C"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Идентификаторы URI Marketplace](../uri/marketplace/atoc-reference-marketplace.md)

   