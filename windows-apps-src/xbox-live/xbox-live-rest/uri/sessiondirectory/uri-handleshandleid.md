---
title: /handles/{handleId}
assetID: 5b722d3e-fe80-fec5-a26b-8b3db6422004
permalink: en-us/docs/xboxlive/rest/uri-handleshandleid.html
description: " /handles/{handleId}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 3a312d3744e96755a899d73307a47c01e3dc79fd
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57632639"
---
# <a name="handleshandleid"></a>/handles/{handleId}
Поддерживает операции DELETE и GET для дескрипторов сеанса, указанного по идентификатору. 

> [!NOTE] 
> Этот URI используется многопользовательскую 2015 и применяется только к этой многопользовательские версии и более поздних версий. Он предназначен для использования с контрактом шаблона 104/105 или более поздней версии.  

 
<a id="ID4EQ"></a>

 
## <a name="domain"></a>Домен
sessiondirectory.xboxlive.com  
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | 
| handleId| Код GUID| Уникальный идентификатор маркера сеанса.| 
  
<a id="ID4ERB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[УДАЛИТЬ (/handles/ {handleId})](uri-handleshandleiddelete.md)

&nbsp;&nbsp;Удаляет дескрипторы, указываемого идентификатором дескриптор.

[GET (/handles/ {дескриптор id})](uri-handleshandleidget.md)

&nbsp;&nbsp;Извлекает дескрипторы, указываемого идентификатором дескриптор.
 
<a id="ID4E4B"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E6B"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI каталог сеанса](atoc-reference-sessiondirectory.md)

   