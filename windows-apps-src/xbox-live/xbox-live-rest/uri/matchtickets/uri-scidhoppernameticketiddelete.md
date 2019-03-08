---
title: DELETE (/serviceconfigs/{scid}/hoppers/{hoppername}/tickets/{ticketid})
assetID: d9ff3f21-aa70-af41-afa1-9a9244fcdb95
permalink: en-us/docs/xboxlive/rest/uri-scidhoppernameticketiddelete.html
description: " DELETE (/serviceconfigs/{scid}/hoppers/{hoppername}/tickets/{ticketid})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: fdd28cb94b31102d9af98aa95afde45424dadce9
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57589669"
---
# <a name="delete-serviceconfigsscidhoppershoppernameticketsticketid"></a>DELETE (/serviceconfigs/{scid}/hoppers/{hoppername}/tickets/{ticketid})

Удаляет билет совпадения.

> [!IMPORTANT]
> Этот метод предназначен для использования с контрактом 103 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 103 или более поздней версии, при каждом запросе.

  * ["Примечания"](#ID4ET)
  * [Параметры URI](#ID4E2)
  * [Авторизации](#ID4EGB)
  * [Коды состояния HTTP](#ID4EOC)
  * [Текст запроса](#ID4EXC)
  * [Текст ответа](#ID4ECD)

<a id="ID4ET"></a>


## <a name="remarks"></a>Замечания

Этот метод HTTP/REST Удаляет указанный билет идентификатор из именованного hopper в конфигурации службы уровень идентификатор (SCID). Этот метод может быть заключена в **Microsoft.Xbox.Services.Matchmaking.MatchmakingService.DeleteMatchTicketAsync**.  
<a id="ID4E2"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| scid| Код GUID| Служба идентификатор конфигурации (SCID) для сеанса.|
| name| Строка| Имя hopper.|
| TicketId| Код GUID| Идентификатор билета|

<a id="ID4EGB"></a>


## <a name="authorization"></a>Authorization

| Тип| Обязательно| Описание| Ответ, если он отсутствует|
| --- | --- | --- | --- | --- | --- | --- | --- |
| XUID (идентификатор пользователя)| Да| Имя пользователя, отправившего запрос должен быть членом билет сеанса, на который ссылается билета.| 403|
| Привилегии и тип устройства| Да| Если Тип_устройства пользователя имеет значение в консоль, для звонков в службу поддержки знакомства допускаются только пользователи с многопользовательской привилегии в утверждения.| 403|

<a id="ID4EOC"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP

Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4EXC"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4ECD"></a>


## <a name="response-body"></a>Тело ответа

Объекты не будут отправлены в тексте ответа.

<a id="ID4EPD"></a>


## <a name="see-also"></a>См. также

<a id="ID4ERD"></a>


##### <a name="parent"></a>Parent  

[/tickets/ /hoppers/ {hoppername} /serviceconfigs/ {scid} {ticketid}](uri-scidhoppernameticketid.md)
