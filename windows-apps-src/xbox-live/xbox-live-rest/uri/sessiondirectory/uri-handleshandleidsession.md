---
title: /handles/{handleId}/session
assetID: 4ed2dcf5-5d1f-91ce-4a3f-eb3ba68727bf
permalink: en-us/docs/xboxlive/rest/uri-handleshandleidsession.html
description: " /handles/{handleId}/session"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: e7b6990917437c22dd4d9282492e2a0eab37893b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57628149"
---
# <a name="handleshandleidsession"></a>/handles/{handleId}/session
Поддерживает операции PUT и GET для сеанса, в с помощью разыменование дескриптора. 

> [!NOTE] 
> Этот URI используется многопользовательскую 2015 и применяется только к этой многопользовательские версии и более поздних версий. Он предназначен для использования с контрактом шаблона 104/105 или более поздней версии.  

 

> [!NOTE] 
> Этот URI в данный момент только доступном консолей Xbox One и серверами, с помощью идентификатора службы.  

 
<a id="ID4ES"></a>

 
## <a name="domain"></a>Домен
sessiondirectory.xboxlive.com  
<a id="ID4EX"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | --- | 
| handleId| Код GUID| Уникальный идентификатор маркера сеанса.| 
  
<a id="ID4ESB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (/handles/ {handleId} / сеанс)](uri-handleshandleidsessionget.md)

&nbsp;&nbsp;Получает объект сеанса для идентификатора указанного дескриптора. 

[ПОМЕСТИТЕ (/handles/ {дескриптор id} / сеанс)](uri-handleshandleidsessionput.md)

&nbsp;&nbsp;Создает или обновляет сеанс, разыменование дескриптора.
 
<a id="ID4E6B"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EBC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI каталог сеанса](atoc-reference-sessiondirectory.md)

   