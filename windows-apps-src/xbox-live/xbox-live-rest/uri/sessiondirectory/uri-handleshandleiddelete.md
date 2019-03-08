---
title: DELETE (/handles/{handleId})
assetID: 71cceff4-3a74-dc05-12db-cfe3f20a8aea
permalink: en-us/docs/xboxlive/rest/uri-handleshandleiddelete.html
description: " DELETE (/handles/{handleId})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 354f3563c48139edc5d5cc041e8304998af55620
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57613409"
---
# <a name="delete-handleshandleid"></a>DELETE (/handles/{handleId})
Удаляет дескрипторы, указываемого идентификатором дескриптор.

> [!IMPORTANT]
> Этот метод используется многопользовательскую 2015 и применяется только к этой многопользовательские версии и более поздних версий. Он предназначен для использования с контрактом шаблона 104/105 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 104/105 или более поздней версии, при каждом запросе.

  * ["Примечания"](#ID4ET)
  * [Параметры URI](#ID4EAB)
  * [Коды состояния HTTP](#ID4ELB)
  * [Текст запроса](#ID4ESB)
  * [Текст ответа](#ID4E4B)

<a id="ID4ET"></a>


## <a name="remarks"></a>Замечания
Этот метод HTTP/REST удаляет дескрипторы для указанного идентификатора и очищает текущее действие пользователя для сеанса. Этот метод может быть заключена в **Microsoft.Xbox.Services.Multiplayer.MultiplayerService.ClearActivityAsync**.  
<a id="ID4EAB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| handleId| Код GUID| Уникальный идентификатор маркера сеанса.|

<a id="ID4ELB"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP
Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4ESB"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4E4B"></a>


## <a name="response-body"></a>Тело ответа

Объекты не будут отправлены в тексте ответа.

<a id="ID4EIC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EKC"></a>


##### <a name="parent"></a>Parent

[/Handles/ {handleId}](uri-handleshandleid.md)
