---
title: /media/{marketplaceId}/metadata/mediaItemTypes/{mediaitemtype}/queryrefiners
assetID: 5a519314-1df1-cbdc-cb04-3a8b663003de
permalink: en-us/docs/xboxlive/rest/uri-medialocalemetadatamediaitemtypequeryrefiners.html
description: " /media/{marketplaceId}/metadata/mediaItemTypes/{mediaitemtype}/queryrefiners"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: c0a891e7ba1b111d00593de55561c89988d2a4ad
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57603169"
---
# <a name="mediamarketplaceidmetadatamediaitemtypesmediaitemtypequeryrefiners"></a>/media/{marketplaceId}/metadata/mediaItemTypes/{mediaitemtype}/queryrefiners
Обращается к уточнения запроса для данного носителя тип элемента. Доменом для таких URI является `eds.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.| 
| mediaitemtype| Строка| Обязательный. Одно из значений из [получить (/media/ {marketplaceId} / metadata/mediaGroups / {mediagroup} / mediaItemTypes)](uri-medialocalemetadatamediagroupsmediaitemtypesget.md).| 
  
<a id="ID4EBC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (/media/ {marketplaceId} / metadata/mediaItemTypes / {mediaitemtype} / queryrefiners)](uri-medialocalemetadatamediaitemtypequeryrefinersget.md)

&nbsp;&nbsp;Список уточнения запроса для указанного элемента типа мультимедиа.
 
<a id="ID4ELC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ENC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

  
<a id="ID4EXC"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   