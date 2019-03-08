---
title: Коды URI для поиска сочетаний
assetID: 667b02a9-6f34-8165-001b-ee8782575202
permalink: en-us/docs/xboxlive/rest/atoc-reference-matchtickets.html
description: " Коды URI для поиска сочетаний"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: c52abca7ed49d4a5e14520095ae944938b86f093
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57590469"
---
# <a name="matchmaking-uris"></a>Коды URI для поиска сочетаний
 
Этот раздел содержит подробные сведения о универсальный идентификатор ресурса (URI) адреса и связанных методов протокола передачи гипертекста (HTTP) от службы Xbox Live для службы подбор игроков. 
 
<a id="ID4E6"></a>

 
## <a name="domain"></a>Домен
momatch.xboxlive.com  
<a id="ID4EEB"></a>

 
## <a name="service-version"></a>Версия службы
 
Вызывающие объекты таких URI HTTP/REST должны передавать значение 103 или более поздней версии для X-Xbl-контракт-Version, заголовок HTTP, который указывает версию службы из служб обнаружения развлечения (EDS). 
  
<a id="ID4ELB"></a>

 
## <a name="system-objects-and-properties"></a>Системные объекты и свойства
 
В настоящее время все конфигурации службы координирующему происходит вручную, с помощью службы конфигурации части [портале разработчиков Xbox (XDP)](https://xdp.xboxlive.com) или [центра партнеров](https://partner.microsoft.com/dashboard). Некоторые данные координирующему также отражаются в объекты, определенные для MPSD. 
 
Основные объекты JSON, используемым для настройки координирующему определяются в [MatchTicket (JSON)](../../json/json-matchticket.md) и [HopperStatsResults (JSON)](../../json/json-hopperstatsresults.md). Обратите внимание, что все соответствуют билеты необходимо определить **ticketSessionRef** предоставляющий ссылку на многопользовательской содержащий проигрыватель или проигрывателей, которые хотят сопоставляться с другими пользователями. 
  
<a id="ID4EBC"></a>

 
## <a name="in-this-section"></a>В этом разделе

[/hoppers/ /serviceconfigs/ {scid} {hoppername}](uri-serviceconfigsscidhoppershoppername.md)

&nbsp;&nbsp;Поддерживает операцию POST для создания билетов совпадения. 

[/hoppers/ /serviceconfigs/ {scid} {name} / stats](uri-serviceconfigsscidhoppershoppernamestats.md)

&nbsp;&nbsp;Поддерживает операции GET для получения статистики для hopper.

[/tickets/ /hoppers/ {hoppername} /serviceconfigs/ {scid} {ticketid}](uri-scidhoppernameticketid.md)

&nbsp;&nbsp;Поддерживает операции удаления для билета совпадения.
 
<a id="ID4ENC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EPC"></a>

   [MatchTicket (JSON)](../../json/json-matchticket.md)

 [HopperStatsResults (JSON)](../../json/json-hopperstatsresults.md)

 [Идентификаторы URI каталог сеанса](../sessiondirectory/atoc-reference-sessiondirectory.md)

  
<a id="ID4E2C"></a>

 
##### <a name="parent"></a>Parent 

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)

   