---
title: /serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions
assetID: 8d55818f-99fd-146a-896b-0f100e78799f
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessions.html
description: " /serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 99d9a82cb419e6598fc1113692b031487950aa8b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57656099"
---
# <a name="serviceconfigsscidsessiontemplatessessiontemplatenamesessions"></a>/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions
Поддерживает операции GET для извлечения набора шаблонов сеанса с именами указанного шаблона. 
<a id="ID4EO"></a>

 
## <a name="domain"></a>Домен
sessiondirectory.xboxlive.com  
<a id="ID4ET"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| scid| Код GUID| Идентификатор конфигурации службы (SCID). Часть 1 из сеанса идентификатор.| 
| Ключевое слово| Строка| Ключевое слово, используемое для фильтрации результатов только сеансы, идентифицировать с помощью этой строки.| 
| xuid| Код GUID| Идентификаторы пользователей Xbox для пользователей, для которого нужно получить сеансов. Пользователям должна быть активной в сеансах. | 
| резервирования| Строка| Значение, указывающее, если список сеансов включаются те, которые не приняли пользователей. Этот параметр можно задать только значение true. Этот параметр требует, чтобы участник был доступ на уровне сервера к сеансу или XUID вызывающей стороны, отправив запрос на соответствие фильтру идентификатор пользователя Xbox. | 
| Неактивные| Строка| Значение, указывающее, если список сеансов включаются те, которые пользователи приняли, но не воспроизводится активно. Этот параметр можно задать только значение true. | 
| закрытый| Строка| Значение, указывающее, если список сеансов включает частных сеансов. Этот параметр можно задать только значение true. Допустимо только в том случае, при запросе собственный сеанс, или при запросе сервера на сервер. Задать этому параметру значение true вызывающая программа должна иметь доступ на уровне сервера к сеансу или XUID вызывающей стороны, отправив запрос на соответствие фильтру идентификатор пользователя Xbox. | 
| visibility| Строка| Значение перечисления, указывающее состояние видимости, используемые для фильтрации результатов. В настоящее время этот параметр может устанавливаться только к открытым для включения открытых сеансов. См. в разделе <b>MultiplayerSessionVisibility</b>. | 
| version| Строка| Положительное целое число, указывающее, версии основных сеанса или нижнем сеансов для включения. Значение должно быть меньше или равна версии контракта запроса остаток от деления 100. | 
| Take| Строка| Положительное целое число, указывающее максимальное количество сеансов для извлечения.| 
  
<a id="ID4EZD"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (/sessiontemplates/ /serviceconfigs/ {scid} {sessionTemplateName} / сеансов)](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionsget.md)

&nbsp;&nbsp;Извлекает шаблоны документов сеанса.
 
<a id="ID4EDE"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EFE"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI каталог сеанса](atoc-reference-sessiondirectory.md)

   