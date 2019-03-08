---
title: /users/xuid(xuid)/lists/PINS/{listname}
assetID: b6421b11-fcd1-cfdb-c1fa-6cab3dab89d9
permalink: en-us/docs/xboxlive/rest/uri-usersxuidlistspinslistname.html
description: " /users/xuid(xuid)/lists/PINS/{listname}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 0f9610b400e9530f86e264cea30bfdfdd1b09c8d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57627999"
---
# <a name="usersxuidxuidlistspinslistname"></a>/users/xuid(xuid)/lists/PINS/{listname}
Обращений к элементов в списке. Доменом для таких URI является `eplists.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| Строка| Идентификатор пользователя Xbox (XUID).| 
| listtype| Строка| Тип списка (как она используется и как он действует). Всегда «КОНТАКТЫ» для их соответствующими методами.| 
| ListName| Строка| Имя списка (список заданного listtype реагировать на). Всегда «XBLPins» для элементов в ПИН-кодов.| 
  
<a id="ID4EGC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[УДАЛЕНИЕ](uri-usersxuidlistspinslistnamedelete.md)

&nbsp;&nbsp;Удаляет элементы из списка.

[ПОЛУЧИТЬ](uri-usersxuidlistspinslistnameget.md)

&nbsp;&nbsp;Возвращает содержимое списка.

[ПОМЕСТИТЬ](uri-usersxuidlistspinslistnamepost.md)

&nbsp;&nbsp;Вставляет элементы в списке с индексом, на основе параметра строки запроса **insertIndex**.

[PUT](uri-usersxuidlistspinslistnameput.md)

&nbsp;&nbsp;Обновляет элементы списка в соответствии с индексы, заданные для каждого элемента в тексте запроса.
 
<a id="ID4EZC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E2C"></a>

 
##### <a name="parent"></a>Parent 

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)

   