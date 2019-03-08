---
title: /media/{marketplaceId}/metadata/mediaItemTypes/{mediaitemtype}/queryrefiners/{queryrefinername}
assetID: 1e2f5fd3-3d14-9a97-ffbf-ab2a18ff4f15
permalink: en-us/docs/xboxlive/rest/uri-medialocalemetadatamediaitemtypequeryrefinersqueryrefinername.html
description: " /media/{marketplaceId}/metadata/mediaItemTypes/{mediaitemtype}/queryrefiners/{queryrefinername}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 356c7d6eb30eb14156e434f717530d6f4f48a990
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57611479"
---
# <a name="mediamarketplaceidmetadatamediaitemtypesmediaitemtypequeryrefinersqueryrefinername"></a>/media/{marketplaceId}/metadata/mediaItemTypes/{mediaitemtype}/queryrefiners/{queryrefinername}
Обращается к допустимые значения для заданного запроса уточнения имени и указанного типа носителя элемента. Доменом для таких URI является `eds.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.| 
| mediaitemtype| Строка| Обязательный. Одно из значений из [получить (/media/ {marketplaceId} / metadata/mediaGroups / {mediagroup} / mediaItemTypes)](uri-medialocalemetadatamediagroupsmediaitemtypesget.md).| 
| queryrefinername| Строка| Обязательный. Имя уточнение запроса, для которого требуются значения, такие как «Genre» или «Десять лет». См. в разделе QueryRefiners.| 
  
<a id="ID4EKC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (/media/ {marketplaceId} / метаданных/mediaItemTypes / {mediaitemtype} /queryrefiners/ {queryrefinername})](uri-medialocalemetadatamediaitemtypequeryrefinersqueryrefinernameget.md)

&nbsp;&nbsp;Перечисляет допустимые значения для заданного запроса уточнения имени и указанного типа носителя элемента.
 
<a id="ID4EUC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EWC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

  
<a id="ID4EAD"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   