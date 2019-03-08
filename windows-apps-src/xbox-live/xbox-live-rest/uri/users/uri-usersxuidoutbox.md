---
title: /users/xuid({xuid})/outbox
assetID: 0b66b885-15ff-be55-f8be-e6e9d85d087e
permalink: en-us/docs/xboxlive/rest/uri-usersxuidoutbox.html
description: " /users/xuid({xuid})/outbox"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 88f3f3753aeac99db0a8a53e0a2ddde21d034ac5
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57607549"
---
# <a name="usersxuidxuidoutbox"></a>/users/xuid({xuid})/outbox
Предоставляет доступ только для отправки пользователю для обмена сообщениями папки "Исходящие" для Xbox LIVE Services. Доменом для таких URI является `msg.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI) 
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid | 64-разрядных целых беззнаковых | Идентификатор Xbox пользователя (XUID), игрок, который выполняет запрос. | 
  
<a id="ID4EXB"></a>

 
## <a name="valid-methods"></a>Допустимые методы 

[POST (/users/xuid({xuid})/outbox)](uri-usersxuidoutboxpost.md)

&nbsp;&nbsp;Отправляет указанное сообщение в список получателей. 
 
<a id="ID4EFC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EHC"></a>

 
##### <a name="parent"></a>Parent  

[Идентификаторы URI пользователей](atoc-reference-users.md)

   