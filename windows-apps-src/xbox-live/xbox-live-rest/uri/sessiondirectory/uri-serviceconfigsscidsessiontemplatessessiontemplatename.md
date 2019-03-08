---
title: /serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}
assetID: cc306ca0-57f8-f676-6d4b-f9ddd716dcc0
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidsessiontemplatessessiontemplatename.html
description: " /serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 36b4034607fc6b5011491424551ed60bc3da5fa7
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57599269"
---
# <a name="serviceconfigsscidsessiontemplatessessiontemplatename"></a>/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}
Поддерживает операцию GET для извлечения набора имена шаблонов сеанса. 
<a id="ID4EO"></a>

 
## <a name="domain"></a>Домен
sessiondirectory.xboxlive.com  
<a id="ID4ET"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| scid| Код GUID| Идентификатор конфигурации службы (SCID). Часть 1 из сеанса идентификатор.| 
| sessionTemplateName| Строка| Имя текущего экземпляра шаблона сеанса. Часть 2 из сеанса идентификатор. | 
  
<a id="ID4EYB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[GET (/sessiontemplates/ /serviceconfigs/ {scid} {sessionTemplateName})](uri-serviceconfigsscidsessiontemplatessessiontemplatenameget.md)

&nbsp;&nbsp;Получает набор имен шаблонов сеанса.
 
<a id="ID4ECC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EEC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI каталог сеанса](atoc-reference-sessiondirectory.md)

   