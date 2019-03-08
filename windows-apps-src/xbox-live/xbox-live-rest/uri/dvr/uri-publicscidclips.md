---
title: /public/scids/{scid}/clips
assetID: 55a1f0ae-08bb-6d1e-a1da-cbeb481c42ee
permalink: en-us/docs/xboxlive/rest/uri-publicscidclips.html
description: " /public/scids/{scid}/clips"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: db279d546780ed40158894f73ecb84687ef35ba6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57627979"
---
# <a name="publicscidsscidclips"></a>/public/scids/{scid}/clips
Клипов открытый доступ. Это фактически можно указать код URI в двух формах `/public/scids/{scid}/clips` и `/public/titles/{titleId}/clips`. Ниже приведены дополнительные сведения. Доменом для этого URI является `gameclipsmetadata.xboxlive.com`.
 
  * [Параметры URI](#ID4E1)
 
<a id="ID4E1"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| scid| Строка| Идентификатор конфигурации основной службы открытого клипов.| 
| titleid| Строка| TitleId открытый клипов. Нельзя использовать в качестве SCID же URI. Если указано, будет использоваться для поиска основной SCID.| 
  
<a id="ID4E6B"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (/ public/scids / {scid} / отсекает)](uri-publicscidclipsget.md)

&nbsp;&nbsp;Список общедоступных клипов.
 
<a id="ID4EJC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ELC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI Marketplace](../marketplace/atoc-reference-marketplace.md)

   