---
title: /serviceconfigs/{scid}/batch
assetID: eb1b510f-d92e-ae9b-a3e6-0edf58b4f075
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidbatch.html
description: " /serviceconfigs/{scid}/batch"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 208ee92106563372dd4d92a8c800cc08f513e8c7
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57589719"
---
# <a name="serviceconfigsscidbatch"></a>/serviceconfigs/{scid}/batch
Поддерживает операцию POST на пакетный запрос на уровне идентификатора конфигурации службы.

> [!IMPORTANT]
> Этот метод используется многопользовательскую 2015 и применяется только к этой многопользовательские версии и более поздних версий. Он предназначен для использования с контрактом шаблона 104/105 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 104/105 или более поздней версии, при каждом запросе.

<a id="ID4ER"></a>


## <a name="domain"></a>Домен
sessiondirectory.xboxlive.com  
<a id="ID4EW"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| scid| Код GUID| Идентификатор конфигурации службы (SCID). Часть 1 из идентификатор сеанса.|

<a id="ID4ESB"></a>


## <a name="valid-methods"></a>Допустимые методы

[POST (/serviceconfigs/ {scid} / batch)](uri-serviceconfigsscidbatchpost.md)

&nbsp;&nbsp;Создает пакетный запрос на несколько Xbox идентификаторы пользователей для конфигурации службы.

<a id="ID4E3B"></a>


## <a name="see-also"></a>См. также

<a id="ID4E5B"></a>


##### <a name="parent"></a>Parent

[Идентификаторы URI каталог сеанса](atoc-reference-sessiondirectory.md)
