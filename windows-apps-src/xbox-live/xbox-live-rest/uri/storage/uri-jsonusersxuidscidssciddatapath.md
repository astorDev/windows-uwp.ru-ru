---
title: /json/users/xuid({xuid})/scids/{scid}/data/{path}
assetID: c2745955-5e52-ea2b-7389-cb85202e01c3
permalink: en-us/docs/xboxlive/rest/uri-jsonusersxuidscidssciddatapath.html
description: " /json/users/xuid({xuid})/scids/{scid}/data/{path}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 275842420b73abc6c2fd8a8fafa265777e2fa00f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57637159"
---
# <a name="jsonusersxuidxuidscidssciddatapath"></a>/json/users/xuid({xuid})/scids/{scid}/data/{path}
Перечисляет сведения о файле по указанному пути. Доменом для таких URI является `titlestorage.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| 64-разрядных целых беззнаковых| Xbox идентификатора пользователя (XUID) исполнителя, выполняющего запрос.| 
| scid| guid| Идентификатор конфигурации службы для поиска.| 
| путь| Строка| Путь к возвращаемых элементов данных. Все каталоги и подкаталоги сопоставления вернуться. Допустимые символы включать прописные буквы (A-Z), строчные буквы (a – z), цифры (0 – 9), символ подчеркивания (_) и косой черты (/). Может быть пустым. Максимальная длина 256.| 
  
<a id="ID4EFC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[ПОЛУЧИТЬ](uri-jsonusersxuidscidssciddatapath-get.md)

&nbsp;&nbsp;Перечисляет сведения о файле по указанному пути.
 
<a id="ID4EPC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ERC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI хранилища Title](atoc-reference-storagev2.md)

   