---
title: /serviceconfigs/{scid}/hoppers/{hoppername}
assetID: ba1e129d-b4c4-6535-46ce-fd184465c85f
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidhoppershoppername.html
description: " /serviceconfigs/{scid}/hoppers/{hoppername}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 1db069f59eeba565a257531907d6be0b1dcd189d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598429"
---
# <a name="serviceconfigsscidhoppershoppername"></a>/serviceconfigs/{scid}/hoppers/{hoppername}

Поддерживает операцию POST для создания билетов совпадения.

> [!IMPORTANT]
> Этот URI предназначен для использования с контрактом 103 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 103 или более поздней версии, при каждом запросе.

<a id="ID4ER"></a>


## <a name="domain"></a>Домен
momatch.xboxlive.com  
<a id="ID4EW"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| scid| Код GUID| Служба идентификатор конфигурации (SCID) для сеанса.|
| hoppername | Строка | Имя hopper. |

<a id="ID4E2B"></a>


## <a name="valid-methods"></a>Допустимые методы

[POST (/hoppers/ /serviceconfigs/ {scid} {hoppername})](uri-serviceconfigsscidhoppershoppernamepost.md)

&nbsp;&nbsp;Создает билет соответствие.

<a id="ID4EFC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EHC"></a>


##### <a name="parent"></a>Parent  

[Идентификаторы URI подбор игроков](atoc-reference-matchtickets.md)
