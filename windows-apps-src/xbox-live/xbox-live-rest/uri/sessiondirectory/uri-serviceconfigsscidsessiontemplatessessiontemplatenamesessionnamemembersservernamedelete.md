---
title: DELETE (/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/servers/{server-name})
assetID: 39c921d1-a166-74b9-fcbc-ea3c0c58cc40
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionnamemembersservernamedelete.html
description: " DELETE (/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/servers/{server-name})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 65da52284d49d4d9384685d073f13bd93b10a30b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57658319"
---
# <a name="delete-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnameserversserver-name"></a>DELETE (/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/servers/{server-name})
Удаляет указанный сервер из сеанса.

> [!IMPORTANT]
> Этот метод URI требуется элемент заголовка X-Xbl-контракт-версии: 104/105 или более поздней версии, при каждом запросе.

  * [Параметры URI](#ID4ET)
  * [Коды состояния HTTP](#ID4E5)
  * [Текст запроса](#ID4EFB)
  * [Текст ответа](#ID4EOB)

<a id="ID4ET"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| scid| Код GUID| Идентификатор конфигурации службы (SCID). Часть 1 из идентификатор сеанса.|
| sessionTemplateName| Строка| Имя текущего экземпляра шаблона сеанса. Часть 2 из идентификатор сеанса.|
| Имя сеанса| Код GUID| Уникальный идентификатор сеанса. Часть 3 из идентификатор сеанса.|

<a id="ID4E5"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP
Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4EFB"></a>


## <a name="request-body"></a>Тело запроса
См. в разделе структуры запроса в [MultiplayerSessionRequest (JSON)](../../json/json-multiplayersessionrequest.md).  
<a id="ID4EOB"></a>


## <a name="response-body"></a>Тело ответа
См. в разделе структуры ответа в [MultiplayerSession (JSON)](../../json/json-multiplayersession.md).  
<a id="ID4E1B"></a>


## <a name="see-also"></a>См. также

<a id="ID4E3B"></a>


##### <a name="parent"></a>Parent

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/servers/{server-name}](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionnamemembersservername.md)
