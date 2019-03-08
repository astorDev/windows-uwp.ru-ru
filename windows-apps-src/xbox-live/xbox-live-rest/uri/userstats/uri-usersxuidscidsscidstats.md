---
title: /users/xuid({xuid})/scids/{scid}/stats
assetID: 3cf9ffd4-9a8b-2658-402b-2e933f7f6f1b
permalink: en-us/docs/xboxlive/rest/uri-usersxuidscidsscidstats.html
description: " /users/xuid({xuid})/scids/{scid}/stats"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 53a6c7bb0e7390b024b01e221d8061316a80509e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57650419"
---
# <a name="usersxuidxuidscidsscidstats"></a>/users/xuid({xuid})/scids/{scid}/stats
Осуществляет доступ к конфигурации службы, ограничивается разделенный запятыми список имен пользователей статистический показатель, от имени указанного пользователя. Доменом для таких URI является `userstats.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| Код GUID| Xbox пользователя идентификатор (XUID) пользователя, от лица которого для доступа к конфигурации службы.| 
| scid| Код GUID| Идентификатор конфигурации службы, содержащего ресурс к которому выполняется доступ.| 
  
<a id="ID4E4B"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[ПОЛУЧИТЬ](uri-usersxuidscidsscidstatsget.md)

&nbsp;&nbsp;Получает конфигурацию службы, ограничивается разделенный запятыми список имен пользователей статистический показатель, от имени указанного пользователя.

[ПОЛУЧИТЬ значение метаданных](uri-usersxuidscidsscidstatsgetvaluemetadata.md)

&nbsp;&nbsp;Получает список указанного статистику, включая метаданные, связанные с статистических значений, для пользователя в конфигурации указанной службы.
 
<a id="ID4EKC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EMC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI статистики пользователя](atoc-reference-userstats.md)

   