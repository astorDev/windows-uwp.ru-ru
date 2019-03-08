---
title: /users/xuid(xuid)/lists/PINS/{listname}/index({index})?insertIndex={insertIndex}
assetID: edcb19bd-87a5-732b-0c45-6f7355fc2dd1
permalink: en-us/docs/xboxlive/rest/uri-usersxuidlistspinslistnameindex.html
description: " /users/xuid(xuid)/lists/PINS/{listname}/index({index})?insertIndex={insertIndex}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: b56b563c72c206340aa2c1ce9f73aa8dfe50809d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57641229"
---
# <a name="usersxuidxuidlistspinslistnameindexindexinsertindexinsertindex"></a>/users/xuid(xuid)/lists/PINS/{listname}/index({index})?insertIndex={insertIndex}
Перемещает элемент в списке. Доменом для таких URI является `eplists.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI) 
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| XUID| Строка| XUID пользователя.| 
| ListName| Строка| Имя списка для управления.| 
| Индекс| Строка| Задает текущий индекс перемещаемого элемента. Если значение индекса 0 или положительным целым числом, это относится к текущему индексу элемента, и тело запроса вызова должно быть пустым. Тем не менее если значение индекса «-1», перемещаемого элемента необходимо указать идентификатор элемента или поставщика/ProviderID в тексте запроса вызова. | 
  
<a id="ID4EHC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[ПОМЕСТИТЬ](uri-usersxuidlistspinslistnameindexpost.md)

&nbsp;&nbsp;Перемещает элемент в списке в другое положение в списке.
 
<a id="ID4ERC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ETC"></a>

 
##### <a name="parent"></a>Parent 

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)

   