---
title: /titles/{titleId}/sessions/{sessionId}/allocationStatus
assetID: 55611f4b-4ba4-fa9a-ce44-fcc4a6df1b35
permalink: en-us/docs/xboxlive/rest/uri-titlestitleidsessionssessionidallocationstatus.html
description: " /titles/{titleId}/sessions/{sessionId}/allocationStatus"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: aa66b81d1e363488a6969ab31d7091b695f09ae4
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57603309"
---
# <a name="titlestitleidsessionssessionidallocationstatus"></a>/titles/{titleId}/sessions/{sessionId}/allocationStatus
Идентификатор с указанным заголовком и идентификатор сеанса получите состояние запроса билета. Домены для таких URI не `gameserverds.xboxlive.com` и `gameserverms.xboxlive.com`.
 
  * [Параметры URI](#ID4EU)
  * [Имя узла](#ID4EPB)
  * [Допустимые методы](#ID4EWB)
 
<a id="ID4EU"></a>

 
## <a name="uri-parameters"></a>Параметры URI
 
| Параметр| Описание| 
| --- | --- | 
| titleId| Идентификатор заголовок, который следует работать запрос.| 
| sessionId| Идентификатор сеанса для поиска.| 
  
<a id="ID4EPB"></a>

 
## <a name="host-name"></a>Имя узла
 
gameserverms.xboxlive.com
  
<a id="ID4EWB"></a>

 
## <a name="valid-methods"></a>Допустимые методы
  
[ПОЛУЧИТЬ](uri-titlestitleidsessionssessionidallocationstatus-get.md)
 
&nbsp;&nbsp;Возвращает состояние размещения sessionhost, идентифицируемый его sessionId.
   