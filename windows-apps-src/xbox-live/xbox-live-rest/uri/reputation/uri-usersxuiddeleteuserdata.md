---
title: /users/xuid({xuid})/deleteuserdata
assetID: 1925da6f-f6c1-ae5b-5af9-e143b70e6717
permalink: en-us/docs/xboxlive/rest/uri-usersxuiddeleteuserdata.html
description: " /users/xuid({xuid})/deleteuserdata"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: efcf214c366edb891e13301da0eedbc87627ca79
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57617469"
---
# <a name="usersxuidxuiddeleteuserdata"></a>/users/xuid({xuid})/deleteuserdata
Полностью сбрасывает данные репутации для тестового пользователя. Только для тестирования. Доменом для таких URI является `reputation.xboxlive.com`. Этот URI всегда вызывается через порт 10443.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| 64-разрядного целого числа без знака| Xbox пользователя идентификатор (XUID) пользователя, в которых данные удаляются.| 
  
<a id="ID4EYB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[POST (/users/xuid({xuid})/deleteuserdata)](uri-usersxuiddeleteuserdatapost.md)

&nbsp;&nbsp;Полностью сбрасывает данные репутации для тестового пользователя. Только для тестирования.
 
<a id="ID4ECC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EEC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI репутации](atoc-reference-reputation.md)

   