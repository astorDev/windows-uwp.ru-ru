---
title: GET (/handles/{handleId}/session)
assetID: 1f22954c-e77b-69c2-63f4-741fbd965f8f
permalink: en-us/docs/xboxlive/rest/uri-handleshandleidsessionget.html
description: " GET (/handles/{handleId}/session)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 41911dc53b316f4f323b9859d9101581ec88e497
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57593899"
---
# <a name="get-handleshandleidsession"></a>GET (/handles/{handleId}/session)
Получает объект сеанса для идентификатора указанного дескриптора.

> [!IMPORTANT]
> Этот метод используется многопользовательскую 2015 и применяется только к этой многопользовательские версии и более поздних версий. Он предназначен для использования с контрактом шаблона 104/105 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 104/105 или более поздней версии, при каждом запросе.

  * ["Примечания"](#ID4ET)
  * [Параметры URI](#ID4EDB)
  * [Коды состояния HTTP](#ID4EOB)
  * [Текст запроса](#ID4EVB)
  * [Текст ответа](#ID4E6B)

<a id="ID4ET"></a>


## <a name="remarks"></a>Замечания

Этот метод HTTP/REST извлекает объект сеанса на сервере, используя предоставленный указатель со стороны службы в сеанс (дескриптор). Возвращаемое значение, объект сеанса, с его атрибуты. Этот метод может быть заключена в **Microsoft.Xbox.Services.Multiplayer.MultiplayerService.GetCurrentSessionByHandleAsync**.

Объект, вызывающий этот метод получает идентификатор дескриптора из игрока **MultiplayerActivityDetails** объекта. Кроме того вызывающий объект получает идентификатор из активации протокола после пользователь принял игр приглашения.

<a id="ID4EDB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| handleId| Код GUID| Уникальный идентификатор маркера сеанса.|

<a id="ID4EOB"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP
Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4EVB"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4E6B"></a>


## <a name="response-body"></a>Тело ответа
См. в разделе структуры ответа в [MultiplayerSession (JSON)](../../json/json-multiplayersession.md).  
<a id="ID4EIC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EKC"></a>


##### <a name="parent"></a>Parent

[/Handles/ {handleId} / сеанса](uri-handleshandleidsession.md)
