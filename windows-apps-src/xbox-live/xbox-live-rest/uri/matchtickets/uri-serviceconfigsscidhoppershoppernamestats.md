---
title: /serviceconfigs/{scid}/hoppers/{name}/stats
assetID: 56bb4398-445b-e8c5-a4ce-1651576ee7e7
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidhoppershoppernamestats.html
description: " /serviceconfigs/{scid}/hoppers/{name}/stats"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 04376505b76296e052ea431f2a4e5fcfeac7b9e4
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57613939"
---
# <a name="serviceconfigsscidhoppersnamestats"></a>/serviceconfigs/{scid}/hoppers/{name}/stats

Поддерживает операции GET для получения статистики для hopper.

> [!IMPORTANT]
> Этот URI предназначен для использования с контрактом 103 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 103 или более поздней версии, при каждом запросе.

<a id="ID4ER"></a>


## <a name="domain"></a>Домен
momatch.xboxlive.com  
<a id="ID4EW"></a>


## <a name="remarks"></a>Замечания
Этот URI поддерживает xuid значения, gt и me для идентификатора владельца в конфигурации целевого пользователя. Только автор запрос в службу можно удалить билет или получить состояние URI.  
<a id="ID4E6"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| scid| Код GUID| Служба идентификатор конфигурации (SCID) для сеанса.|
| name| Строка| Имя hopper.|

<a id="ID4EEC"></a>


## <a name="valid-methods"></a>Допустимые методы

[Получение (/hoppers/ /serviceconfigs/ {scid} {name} / stats)](uri-serviceconfigsscidhoppershoppernamestatsget.md)

&nbsp;&nbsp;Получает статистику для hopper.

<a id="ID4EQC"></a>


## <a name="see-also"></a>См. также

<a id="ID4ESC"></a>


##### <a name="parent"></a>Parent  

[Идентификаторы URI подбор игроков](atoc-reference-matchtickets.md)
