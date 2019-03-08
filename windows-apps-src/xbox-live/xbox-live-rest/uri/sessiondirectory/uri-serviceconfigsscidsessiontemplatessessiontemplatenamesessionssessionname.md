---
title: /serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}
assetID: 55ce6459-1714-49bc-6231-b547ddf04143
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionname.html
description: " /serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: da8d61634d0a849d51e2ab6ff143469ec05cc75c
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57657479"
---
# <a name="serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionname"></a>/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}
Поддерживает операции PUT и GET для создания и извлечения сеансов.
<a id="ID4EO"></a>


## <a name="domain"></a>Домен
sessiondirectory.xboxlive.com  
<a id="ID4ET"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| scid| Код GUID| Идентификатор конфигурации службы (SCID). Часть 1 из идентификатор сеанса.|
| sessionTemplateName| Строка| Имя текущего экземпляра шаблона сеанса. Часть 2 из идентификатор сеанса.|
| Имя сеанса| Код GUID| Уникальный идентификатор сеанса. Часть 3 из идентификатор сеанса.| 

<a id="ID4EBC"></a>


## <a name="valid-methods"></a>Допустимые методы

[GET (/serviceconfigs/ {scid} /sessions/ /sessiontemplates/ {sessionTemplateName} {sessionName})](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnameget.md)

&nbsp;&nbsp;Получает объект сеанса.

[PUT (/serviceconfigs/ {scid} /sessions/ /sessiontemplates/ {sessionTemplateName} {sessionName})](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnameput.md)

&nbsp;&nbsp;Создает, обновляет или подключении к сеансу.

<a id="ID4EOC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EQC"></a>


##### <a name="parent"></a>Parent

[Идентификаторы URI каталог сеанса](atoc-reference-sessiondirectory.md)
