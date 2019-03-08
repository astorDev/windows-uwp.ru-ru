---
title: /media/{marketplaceId}/singleMediaGroupSearch
assetID: f5599db7-4050-640e-db96-2df01a007c07
permalink: en-us/docs/xboxlive/rest/uri-medialocalesinglemediagroupsearch.html
description: " /media/{marketplaceId}/singleMediaGroupSearch"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: b26b4c2dc51ef5591480372aa9908a49d2f8cbe2
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57661759"
---
# <a name="mediamarketplaceidsinglemediagroupsearch"></a>/media/{marketplaceId}/singleMediaGroupSearch
Результаты поиска для элементов в группе один носитель. Обратите внимание на то, что страницы данных, возвращаемых из такого поиска может осуществляться без последовательности с помощью параметра skipItems вместо того чтобы использовать маркер продолжения. Этот API принимает уточнения запроса.
 
Доменом для таких URI является `eds.xboxlive.com`.
 
  * [Параметры URI](#ID4EX)
 
<a id="ID4EX"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.| 
  
<a id="ID4EYB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (media / {marketplaceId} / singleMediaGroupSearch)](uri-medialocalesinglemediagroupsearchget.md)

&nbsp;&nbsp;Результаты поиска для элементов в группе один носитель. 
 
<a id="ID4ECC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EEC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

  
<a id="ID4EOC"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   