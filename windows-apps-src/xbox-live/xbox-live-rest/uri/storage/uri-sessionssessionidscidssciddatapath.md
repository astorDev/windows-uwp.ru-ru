---
title: /sessions/{sessionId}/scids/{scid}/data/{path}
assetID: 932459b4-24b4-5b09-8146-ed214de0083a
permalink: en-us/docs/xboxlive/rest/uri-sessionssessionidscidssciddatapath.html
description: " /sessions/{sessionId}/scids/{scid}/data/{path}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 1af8befe28c407948dfa03d706f476458bb77c14
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655079"
---
# <a name="sessionssessionidscidssciddatapath"></a>/sessions/{sessionId}/scids/{scid}/data/{path}
Перечисляет сведения о файле по указанному пути. Доменом для таких URI является `titlestorage.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| sessionId| Строка| Идентификатор сеанса для поиска.| 
| scid| guid| Идентификатор конфигурации службы для поиска.| 
| путь| Строка| Путь к возвращаемых элементов данных. Все каталоги и подкаталоги сопоставления вернуться. Допустимые символы включать прописные буквы (A-Z), строчные буквы (a – z), цифры (0 – 9), символ подчеркивания (_) и косой черты (/). Может быть пустым. Максимальная длина 256.| 
  
<a id="ID4EFC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[ПОЛУЧИТЬ](uri-sessionssessionidscidssciddatapath-get.md)

&nbsp;&nbsp;Перечисляет сведения о файле по указанному пути.
 
<a id="ID4EPC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ERC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI хранилища Title](atoc-reference-storagev2.md)

   