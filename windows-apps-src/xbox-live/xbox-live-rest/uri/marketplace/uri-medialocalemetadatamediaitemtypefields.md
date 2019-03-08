---
title: /media/{marketplaceId}/metadata/mediaItemTypes/{mediaItemType}/fields
assetID: fc9b556a-7fc7-64ec-cb5c-b5cabd2ab4ce
permalink: en-us/docs/xboxlive/rest/uri-medialocalemetadatamediaitemtypefields.html
description: " /media/{marketplaceId}/metadata/mediaItemTypes/{mediaItemType}/fields"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 53d0113368754abc3be36b4e7dda213adf38b640
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57613059"
---
# <a name="mediamarketplaceidmetadatamediaitemtypesmediaitemtypefields"></a>/media/{marketplaceId}/metadata/mediaItemTypes/{mediaItemType}/fields
Обращений к поля, из которых можно предположить, данные, для заданного mediaitemtype и данная версия EDS. Доменом для таких URI является `eds.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.| 
| mediaitemtype| Строка| Обязательный. Одно из значений из [получить (/media/ {marketplaceId} / metadata/mediaGroups / {mediagroup} / mediaItemTypes)](uri-medialocalemetadatamediagroupsmediaitemtypesget.md).| 
  
<a id="ID4EBC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (/media/ {marketplaceId} / metadata/mediaItemTypes / {mediaItemType} / поля)](uri-medialocalemetadatamediaitemtypefieldsget.md)

&nbsp;&nbsp;Список полей, из которых можно предположить, данные, для заданного mediaitemtype и данная версия EDS.
 
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

   