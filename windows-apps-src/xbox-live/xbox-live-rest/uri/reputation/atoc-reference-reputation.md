---
title: Коды URI репутации
assetID: d1cb76c0-86a4-8c51-19f6-5f223b517d46
permalink: en-us/docs/xboxlive/rest/atoc-reference-reputation.html
description: " Коды URI репутации"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 93d6d6e6acfd8fa39bd9d26c87ed99362d2c88d6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57614009"
---
# <a name="reputation-uris"></a>Коды URI репутации
 
В этом разделе представлены сведения о универсальный идентификатор ресурса (URI) адреса и связанные методы протокола передачи гипертекста (HTTP) от службы Xbox Live для **Microsoft.Xbox.Services.Social.ReputationService** . Доменом для репутации идентификаторы URI является reputation.xboxlive.com. Типичное представление URI может быть https://reputation.xboxlive.com/users/xuid(2533274790412952)/feedback. 
 
Служба репутации использует отзывы, как описано в разделе [обратной связи (JSON)](../../json/json-feedback.md)для вычисления оценки репутации. Эта оценка будет сохранена в области статистические данные для пользователя в разделе ReputationOverall. Дополнительные сведения о получении Статистика пользователей см. в разделе [получить (/users/xuid({xuid})/scids/{scid}/stats)](../userstats/uri-usersxuidscidsscidstatsget.md). 
 
Игры на всех платформах можно использовать службу репутации.
 
<a id="ID4EMB"></a>

 
## <a name="in-this-section"></a>В этом разделе

[/Users/xuid({xuid})/Feedback](uri-reputationusersxuidfeedback.md)

&nbsp;&nbsp;Использовать из названия, чтобы добавить параметр отзыва в игре, а не с помощью оболочки.

[/ users/batchfeedback](uri-reputationusersbatchfeedback.md)

&nbsp;&nbsp;Используется заголовок вашей службы отправки обратной связи в виде пакета за пределами интерфейса напротив названия.

[/Users/Me/resetreputation](uri-usersmeresetreputation.md)

&nbsp;&nbsp;Позволяет группе принудительного применения для доступа к оценки репутации текущего пользователя.

[/Users/xuid({xuid})/deleteuserdata](uri-usersxuiddeleteuserdata.md)

&nbsp;&nbsp;Полностью сбрасывает данные репутации для тестового пользователя. Только для тестирования.

[/Users/xuid({xuid})/resetreputation](uri-usersxuidresetreputation.md)

&nbsp;&nbsp;Позволяет группе принудительного применения для доступа к оценки репутации указанного пользователя.
 
<a id="ID4E5B"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EAC"></a>

 
##### <a name="parent"></a>Parent 

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)

   