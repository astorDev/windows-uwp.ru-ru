---
title: DELETE (/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/members/{index})
assetID: 00aa2f3d-69a6-6d68-e99b-aad4b102aba3
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionnamemembersindexdelete.html
description: " DELETE (/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/members/{index})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: fbe1942d32ee8facc83f1c723cee2aedaa1078d2
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57618229"
---
# <a name="delete-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnamemembersindex"></a>DELETE (/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/members/{index})
Удаляет указанные члены из сеанса.

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
<a id="ID4EYB"></a>


## <a name="see-also"></a>См. также

<a id="ID4E1B"></a>


##### <a name="parent"></a>Parent

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/members/{index}](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionnamemembersindex.md)
