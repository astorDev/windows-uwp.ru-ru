---
title: Коды URI каталога сеанса
assetID: e3ba951d-b21f-0014-c358-2603d549d118
permalink: en-us/docs/xboxlive/rest/atoc-reference-sessiondirectory.html
description: " Коды URI каталога сеанса"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 9492ff3272af830404a546c9b01d62178adbac96
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651199"
---
# <a name="session-directory-uris"></a>Коды URI каталога сеанса

Этот раздел содержит подробные сведения о универсальный идентификатор ресурса (URI) адреса и связанных методов протокола передачи гипертекста (HTTP) от службы Xbox Live для каталога сеанса многопользовательскую (MPSD).


> [!NOTE] 
> Только заголовки для игр, выполняемых на Xbox 360, на устройстве Windows Phone или на сайте Xbox.com можно использовать каталог сеансов идентификаторы URI.  


  * [Домен](#ID4EUB)
  * [Версия службы](#ID4EZB)
  * [Системные объекты и свойства](#ID4EAC)
  * [Обрабатывает](#ID4EBE)

<a id="ID4EUB"></a>


## <a name="domain"></a>Домен
sessiondirectory.xboxlive.com  
<a id="ID4EZB"></a>


## <a name="service-version"></a>Версия службы

Вызывающие объекты таких URI REST должны передавать значение 104/105 или более поздней версии для X-Xbl-контракт-Version, заголовок HTTP, который указывает версию службы из служб обнаружения развлечения (EDS).

<a id="ID4EAC"></a>


## <a name="system-objects-and-properties"></a>Системные объекты и свойства

Для настройки шаблонов и сеансах MPSD использует несколько объектов сеанса JSON, которые соответствуют с фиксированные схемы, которые directory применяет и интерпретирует. Во время вызовов методов, поддерживаемых каталога сеансов в различные коды URI эти объекты являются проверены и слияние, на основе поддерживаемых схем. Ниже приведены основные объекты JSON, связанные с конфигурацией многопользовательские.

   *  [MultiplayerActivityDetails (JSON)](../../json/json-multiplayeractivitydetails.md)
   *  [MultiplayerSession (JSON)](../../json/json-multiplayersession.md)
   *  [MultiplayerSessionReference (JSON)](../../json/json-multiplayersessionreference.md)
   *  [MultiplayerSessionRequest (JSON)](../../json/json-multiplayersessionrequest.md)


Связанные объекты JSON, которые связаны с игры являются:

   *  [GameMessage (JSON)](../../json/json-gamemessage.md)
   *  [GameResult (JSON)](../../json/json-gameresult.md)
   *  [GameSession (JSON)](../../json/json-gamesession.md)
   *  [GameSessionSummary (JSON)](../../json/json-gamesessionsummary.md)


<a id="ID4EBE"></a>


## <a name="handles"></a>Обрабатывает

Для 2015 многопользовательскую сеансов может осуществляться через сеанс управляет. Были добавлены несколько идентификаторы URI предоставляют функции для поддержки дескрипторов.  
<a id="ID4EFE"></a>


## <a name="in-this-section"></a>В этом разделе

[/Handles](uri-handles.md)

&nbsp;&nbsp;Поддерживает операцию POST для установки сеанса для текущего динамического содержимого для отображения в Xbox One взаимодействие с пользователем панели мониторинга и чтобы пригласить участников сеанса в том случае, если это необходимо.

[/Handles/ {handleId}](uri-handleshandleid.md)

&nbsp;&nbsp;Поддерживает операции DELETE и GET для дескрипторов сеанса, указанного по идентификатору.

[/Handles/ {handleId} / сеанса](uri-handleshandleidsession.md)

&nbsp;&nbsp;Поддерживает операции PUT и GET для сеанса, в с помощью разыменование дескриптора.

[/ дескрипторы/запрос](uri-handlesquery.md)

&nbsp;&nbsp;Поддерживает операции POST для создания запросов маркеров сеанса.

[/serviceconfigs/{scid}/batch](uri-serviceconfigsscidbatch.md)

&nbsp;&nbsp;Поддерживает операцию POST на пакетный запрос на уровне идентификатора конфигурации службы.

[/serviceconfigs/ {scid} / сеансов](uri-serviceconfigsscidsessions.md)

&nbsp;&nbsp;Поддерживает операции GET для извлечения набора документов сеанса.

[/serviceconfigs/{scid}/sessiontemplates](uri-serviceconfigsscidsessiontemplates.md)

&nbsp;&nbsp;Поддерживает операции GET для извлечения набора шаблонов MPSD сеанса.

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}](uri-serviceconfigsscidsessiontemplatessessiontemplatename.md)

&nbsp;&nbsp;Поддерживает операцию GET для извлечения набора имена шаблонов сеанса.

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/batch](uri-serviceconfigscidsessiontemplatessessiontemplatenamebatch.md)

&nbsp;&nbsp;Поддерживает операцию POST для создания пакетного запроса на уровне шаблона сеанса.

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessions.md)

&nbsp;&nbsp;Поддерживает операции GET для извлечения набора шаблонов сеанса с именами указанного шаблона.

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionname.md)

&nbsp;&nbsp;Поддерживает операции PUT и GET для создания и извлечения сеансов.

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/members/{index}](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionnamemembersindex.md)

&nbsp;&nbsp;Поддерживает операции удаления, чтобы удалить члена указанного сеанса.

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/members/me](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnamemembersme.md)

&nbsp;&nbsp;Поддерживает операции удаления для удаления участников сеанса.

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}/servers/{server-name}](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionnamemembersservername.md)

&nbsp;&nbsp;Поддерживает операции удаления, чтобы удалить указанный сервер из сеанса.

<a id="ID4ESF"></a>


## <a name="see-also"></a>См. также

<a id="ID4EUF"></a>

   [Идентификаторы URI подбор игроков](../matchtickets/atoc-reference-matchtickets.md)


<a id="ID4E1F"></a>


##### <a name="parent"></a>Parent

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)
