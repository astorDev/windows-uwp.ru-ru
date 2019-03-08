---
title: /media/{marketplaceId}/metadata/mediaItemTypes
assetID: a88f4c31-082a-45d2-e5d7-b881e829e357
permalink: en-us/docs/xboxlive/rest/uri-medialocalemetadatamediaitemtypes.html
description: " /media/{marketplaceId}/metadata/mediaItemTypes"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: bd5884b2d416c74fa7043791c8e817e66dd6e5ef
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57622619"
---
# <a name="mediamarketplaceidmetadatamediaitemtypes"></a>/media/{marketplaceId}/metadata/mediaItemTypes
Обращается к все поддерживаемые mediaItemTypes для данной версии EDS. Доменом для таких URI является `eds.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.| 
  
<a id="ID4EUB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[GET (/media/ {marketplaceId} / metadata/mediaItemTypes)](uri-medialocalemetadatamediaitemtypesget.md)

&nbsp;&nbsp;Список всех поддерживаемых mediaItemTypes для данной версии EDS.
 
<a id="ID4E5B"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EAC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

  
<a id="ID4EKC"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   