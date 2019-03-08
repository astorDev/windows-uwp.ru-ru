---
title: /users/me/scids/{scid}/clips/{gameClipId}
assetID: f5bead69-4fc9-f551-39cb-c8754645ac88
permalink: en-us/docs/xboxlive/rest/uri-usersmescidclipsgameclipid.html
description: " /users/me/scids/{scid}/clips/{gameClipId}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 3cbe2d2c996b466fd94287129f1add0868f05b05
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57661779"
---
# <a name="usersmescidsscidclipsgameclipid"></a>/users/me/scids/{scid}/clips/{gameClipId}
Доступ к данным игр клипов и метаданные. Домены для таких URI не `gameclipsmetadata.xboxlive.com` и `gameclipstransfer.xboxlive.com`, в зависимости от функции в URI.
 
  * [Параметры URI](#ID4EX)
 
<a id="ID4EX"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| scid| Строка| Идентификатор конфигурации службы ресурса, к которому выполняется обращение. Должно соответствовать SCID пользователя, прошедшего проверку подлинности.| 
| gameClipId| Строка| GameClip идентификатор ресурса, к которому выполняется обращение.| 
  
<a id="ID4E3B"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[УДАЛИТЬ (/ users/me/scids / {scid} /clips/ {gameClipId})](uri-usersmescidclipsgameclipiddelete.md)

&nbsp;&nbsp;Удалить игр клипов

[POST (/ users/me/scids / {scid} /clips/ {gameClipId})](uri-usersmescidclipsgameclipidpost.md)

&nbsp;&nbsp;Обновите метаданные игры clip для данных пользователя.
 
<a id="ID4EJC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ELC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI DVR для игр](atoc-reference-dvr.md)

   