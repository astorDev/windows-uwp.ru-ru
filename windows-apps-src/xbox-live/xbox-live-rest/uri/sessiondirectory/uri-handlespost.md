---
title: POST (/handles)
assetID: 21f3e289-0b0e-2731-befb-bd4c0d71973e
permalink: en-us/docs/xboxlive/rest/uri-handlespost.html
description: " POST (/handles)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: ed3482b8e629749d294ed25944db16372cc7fee6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57594749"
---
# <a name="post-handles"></a>POST (/handles)
Задает многопользовательской для текущего действия пользователя, а приглашает члены сеанса, если это необходимо.

> [!IMPORTANT]
> Этот метод используется многопользовательскую 2015 и применяется только к этой многопользовательские версии и более поздних версий. Он предназначен для использования с контрактом шаблона 104/105 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 104/105 или более поздней версии, при каждом запросе.

  * ["Примечания"](#ID4ET)
  * [Параметры URI](#ID4EHB)
  * [Коды состояния HTTP](#ID4EPB)
  * [Текст запроса](#ID4EVB)
  * [Текст ответа](#ID4EJC)

<a id="ID4ET"></a>


## <a name="remarks"></a>Замечания

Этот метод HTTP/REST может использоваться для установки сеанса для текущего действия. В этом случае метод может быть заключена в **Microsoft.Xbox.Services.Multiplayer.MultiplayerService.SetActivityAsync**. Текст запроса необходимо определить сеанс ссылка, с использованием **sessionRef** объекта в JSON-файл, в поле типа «действие». Текст ответа не извлекается. Определения элементов, указанных в ссылке на сеанс, см. в разделе **Microsoft.Xbox.Services.Multiplayer.MultiplayerSessionReference**.

Этот метод POST можно также пригласить пользователей, указанных дескрипторов к сеансу. В этом случае метод может быть заключена в **Microsoft.Xbox.Services.Multiplayer.MultiplayerService.SendInvitesAsync**. Такое использование метода POST требует тексте запроса для определения ссылки на сеанс, но с типом поля, равным «пригласить». Тело ответа является дескриптором приглашения.

<a id="ID4EHB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

Нет

<a id="ID4EPB"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP
Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4EVB"></a>


## <a name="request-body"></a>Тело запроса

<a id="ID4E1B"></a>


### <a name="request-body-for-setting-activity"></a>Текст для настройки действия запроса


```cpp
{
  "version": 1,
  "type": "activity",
  "sessionRef": {
    "scid": "bd6c41c3-01c3-468a-a3b5-3e0fe8133862",
    "templateName": "deathmatch",
    // The session name is optional in a POST; if not specified, MPSD fills in a GUID.//
    "name": "session-49"
  },
}

```


<a id="ID4EBC"></a>


### <a name="request-body-for-sending-invites"></a>Текст запроса для отправки приглашения


```cpp
{
  // Common handle fields
  "id: "47ca0049-a5ba-4bc1-aa22-fcf834ce4c13",
  "version": 1,
  "type": "invite",
  "sessionRef": {
    "scid": "bd6c41c3-01c3-468a-a3b5-3e0fe8133862",
    "templateName": "deathmatch",
    "name": "session-49"
   },
   "inviteAttributes": {
     "titleId" : {titleId}, // The title being invited to, in decimal uint32. This value is used to find the title name and/or image.
     "context" : {context}, // The title defined context token. Must be 256 characters or less when URI-encoded.
     "contextString" : {contextstring}, // The string name of a custom invite string to display in the invite notification.
     "senderString" : {sender} // The string name of the sender when the sender is a service.
   },
   "invitedXuid": "3210",
}

```


<a id="ID4EJC"></a>


## <a name="response-body"></a>Тело ответа

<a id="ID4EOC"></a>


### <a name="response-body-for-setting-activity"></a>Текст ответа для задания действия
Нет.  
<a id="ID4ESC"></a>


### <a name="response-body-for-sending-invites"></a>Текст ответа для отправки приглашения
Дескриптор приглашения.   
<a id="ID4EXC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EZC"></a>


##### <a name="parent"></a>Parent

[/Handles](uri-handles.md)
