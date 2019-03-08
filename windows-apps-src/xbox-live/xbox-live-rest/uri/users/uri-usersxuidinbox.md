---
title: /users/xuid({xuid})/inbox
assetID: 352740c6-42e2-0000-495d-bf384dc3e941
permalink: en-us/docs/xboxlive/rest/uri-usersxuidinbox.html
description: " /users/xuid({xuid})/inbox"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 8ded70b32dfd291d17a43a1741b26710f681a397
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57640899"
---
# <a name="usersxuidxuidinbox"></a>/users/xuid({xuid})/inbox
Предоставляет доступ пользователю для обмена сообщениями папки "Входящие" для Xbox LIVE Services. Доменом для таких URI является `msg.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI) 
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid | 64-разрядных целых беззнаковых | Идентификатор Xbox пользователя (XUID), игрок, который выполняет запрос. | 
| код (ID) | строки [50] | Идентификатор сообщения полученные или удален. | 
  
<a id="ID4EDC"></a>

 
## <a name="valid-methods"></a>Допустимые методы 

[GET (/users/xuid({xuid})/inbox)](uri-usersxuidinboxget.md)

&nbsp;&nbsp;Получает заданное число сводки сообщений пользователя из службы. 

[DELETE (/users/xuid({xuid})/inbox/{messageId})](uri-usersxuidinboxmessageiddelete.md)

&nbsp;&nbsp;Удаляет пользователя сообщение в папку "Входящие" пользователя.

[GET (/users/xuid({xuid})/inbox/{messageId})](uri-usersxuidinboxmessageidget.md)

&nbsp;&nbsp;Извлекает текст подробные сообщения для сообщения определенному пользователю, пометив его как чтения, в службе. 
 
<a id="ID4EVC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EXC"></a>

 
##### <a name="parent"></a>Parent  

[Идентификаторы URI пользователей](atoc-reference-users.md)

   