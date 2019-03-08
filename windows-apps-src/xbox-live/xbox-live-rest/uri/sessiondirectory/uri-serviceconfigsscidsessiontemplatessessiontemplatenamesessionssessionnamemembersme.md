---
title: /serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/members/me
assetID: a6c2fa17-8bed-d0df-d7ff-db1aa60f44b3
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnamemembersme.html
description: " /serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/members/me"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 2b0ecadb543434383ef32c7fd2a749760d5bcc98
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608459"
---
# <a name="serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnamemembersme"></a>/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/members/me
Поддерживает операции удаления для удаления участников сеанса.
<a id="ID4EO"></a>


## <a name="domain"></a>Домен
sessiondirectory.xboxlive.com  
<a id="ID4ET"></a>

 
## <a name="remarks"></a>Замечания

Все операции ресурсов член сеанса требуют, пользователь с Идентификатором пользователя Xbox (XUID) утверждения авторизации.

<a id="ID4EAB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| scid| Код GUID| Идентификатор конфигурации службы (SCID). Часть 1 из идентификатор сеанса.|
| sessionTemplateName| Строка| Имя текущего экземпляра шаблона сеанса. Часть 2 из идентификатор сеанса.|
| Имя сеанса| Код GUID| Уникальный идентификатор сеанса. Часть 3 из идентификатор сеанса.|

<a id="ID4EOC"></a>


## <a name="valid-methods"></a>Допустимые методы

[УДАЛИТЬ (/serviceconfigs/ {scid} /sessiontemplates/ {sessionTemplateName} /sessions/ {sessionName} / члены/me)](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnamemembersmedelete.md)

&nbsp;&nbsp;Удаляет член из сеанса.

<a id="ID4EYC"></a>


## <a name="see-also"></a>См. также

<a id="ID4E1C"></a>


##### <a name="parent"></a>Parent

[Идентификаторы URI каталог сеанса](atoc-reference-sessiondirectory.md)
