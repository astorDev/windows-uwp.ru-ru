---
title: GET (/public/scids/{scid}/clips)
assetID: 15b3e873-1f96-b1da-2f79-6dac1369a4c0
permalink: en-us/docs/xboxlive/rest/uri-publicscidclipsget.html
description: " GET (/public/scids/{scid}/clips)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 5bce1dd261e0ad1172068a0287519cd0480da85f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57589809"
---
# <a name="get-publicscidsscidclips"></a>GET (/public/scids/{scid}/clips)
Список общедоступных клипов. Доменом для этого URI является `gameclipsmetadata.xboxlive.com`.
 
  * ["Примечания"](#ID4EV)
  * [Параметры URI](#ID4ECB)
  * [Параметры строки запроса](#ID4ENB)
 
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания
 
Этот API позволяет для различных способов списка клипы, которые являются открытыми. Список клипов возвращается на основании проверки конфиденциальности и проверки содержимого изоляции для запрашивающего XUID.
 
Оптимизация запросов осуществляется на каждый идентификатор конфигурации службы (SCID). Указав дополнительные фильтры или порядки сортировки, отличные от значений по умолчанию, перечисленных ниже в некоторых случаях занимает больше для возврата. Это становится очевидным для более крупных наборов видео. Запросы нельзя указать сортировку по возрастанию.
 
Квалификатор необходим для получения клипы ofpublic определенной коллекции. Запрашивающий пользователь должен иметь доступ к запрошенной SCID, в противном случае HTTP 403 возвращается.
  
<a id="ID4ECB"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| scid| Строка| Идентификатор конфигурации основной службы открытого клипов.| 
| titleid| Строка| TitleId открытый клипов. Нельзя использовать в качестве SCID же URI. Если указано, будет использоваться для поиска основной SCID.| 
  
<a id="ID4ENB"></a>

 
## <a name="query-string-parameters"></a>Параметры строки запроса
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | 
| <b>? achievementId = {achievementId}</b>| Последние клипы, совпадающий с указанным ключом <b>achievementId</b>.| Дополнительная сортировка и фильтрация не поддерживается.| 
| <b>? greatestMomentId = {greatestMomentId}</b>| Последние клипы, совпадающий с указанным ключом <b>greatestMomentId</b>.| Дополнительная сортировка и фильтрация не поддерживается.| 
| <b>? Квалификатор = создан </b>| Самые последние| Обязательный.| 
  
<a id="ID4EDD"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EFD"></a>

 
##### <a name="parent"></a>Parent 

[/ public/scids / {scid} / отсекает](uri-publicscidclips.md)

   