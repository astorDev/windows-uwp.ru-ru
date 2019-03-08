---
title: /serviceconfigs/{scid}/hoppers/{hoppername}/tickets/{ticketid}
assetID: 25deb7fe-859c-01d2-d14f-455a36c08a7c
permalink: en-us/docs/xboxlive/rest/uri-scidhoppernameticketid.html
description: " /serviceconfigs/{scid}/hoppers/{hoppername}/tickets/{ticketid}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 9722d06af64c5fd24f53485a1bcfe765f89b08cf
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57627319"
---
# <a name="serviceconfigsscidhoppershoppernameticketsticketid"></a>/serviceconfigs/{scid}/hoppers/{hoppername}/tickets/{ticketid}

Поддерживает операции удаления для билета совпадения.

> [!IMPORTANT]
> Этот URI предназначен для использования с контрактом 103 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 103 или более поздней версии, при каждом запросе.

<a id="ID4ER"></a>


## <a name="domain"></a>Домен
momatch.xboxlive.com  
<a id="ID4EW"></a>


## <a name="remarks"></a>Замечания
Этот URI поддерживает xuid значения, gt и me для идентификатора владельца в конфигурации целевого пользователя.  
<a id="ID4E2"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| scid| Код GUID| Служба идентификатор конфигурации (SCID) для сеанса.|
| name| Строка| Имя hopper.|
| TicketId| Код GUID| Идентификатор билета|

<a id="ID4EJC"></a>


## <a name="valid-methods"></a>Допустимые методы

[УДАЛИТЬ (/serviceconfigs/ {scid} /tickets/ /hoppers/ {hoppername} {ticketid})](uri-scidhoppernameticketiddelete.md)

&nbsp;&nbsp;Удаляет билет совпадения.

<a id="ID4ETC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EVC"></a>


##### <a name="parent"></a>Parent  

[Идентификаторы URI подбор игроков](atoc-reference-matchtickets.md)
