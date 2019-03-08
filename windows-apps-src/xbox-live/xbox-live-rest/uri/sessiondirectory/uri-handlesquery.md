---
title: /handles/query
assetID: e00d31ad-b9ba-8e52-1333-83192eab0446
permalink: en-us/docs/xboxlive/rest/uri-handlesquery.html
description: " /handles/query"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: eaa148972ce1e65056470a6c4082cb4e50de3f09
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598569"
---
# <a name="handlesquery"></a>/handles/query
Поддерживает операции POST для создания запросов маркеров сеанса. 

> [!NOTE] 
> Этот URI используется многопользовательскую 2015 и применяется только к этой многопользовательские версии и более поздних версий. Он предназначен для использования с контрактом шаблона 104/105 или более поздней версии.  

 
<a id="ID4EQ"></a>

 
## <a name="domain"></a>Домен
sessiondirectory.xboxlive.com  
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания
Этот URI поддерживает запросы для дескрипторов. В отличие от запросов сеанса, которые являются строки и пакетные запросы, запросы дескриптор использовать стиль обработчик запросов. Поддерживается до 100 маркеров.  
<a id="ID4E2"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
Нет   
<a id="ID4EEB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[POST (/ дескрипторы/query)](uri-handlesquerypost.md)

&nbsp;&nbsp;Создает запросы для дескрипторов сеанса.

[POST (/handles/query?include=relatedInfo)](uri-handlesqueryincludepost.md)

&nbsp;&nbsp;Создает запросы для сеанса дескрипторов, которые включают сведения о связанных сеанса.
 
<a id="ID4EQB"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ESB"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI каталог сеанса](atoc-reference-sessiondirectory.md)

   