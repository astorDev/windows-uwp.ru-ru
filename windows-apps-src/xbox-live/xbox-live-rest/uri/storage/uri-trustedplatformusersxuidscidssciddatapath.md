---
title: /trustedplatform/users/xuid({xuid})/scids/{scid}/data/{path}
assetID: a60a231c-359a-ee6a-6d18-f9e8c6afd0fc
permalink: en-us/docs/xboxlive/rest/uri-trustedplatformusersxuidscidssciddatapath.html
description: " /trustedplatform/users/xuid({xuid})/scids/{scid}/data/{path}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: ff0d2589eb970ef05f8746bb407857de7ebb3256
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57589729"
---
# <a name="trustedplatformusersxuidxuidscidssciddatapath"></a>/trustedplatform/users/xuid({xuid})/scids/{scid}/data/{path}
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

[ПОЛУЧИТЬ](uri-trustedplatformusersxuidscidssciddatapath-get.md)

&nbsp;&nbsp;Перечисляет сведения о файле по указанному пути.
 
<a id="ID4EPC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ERC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI хранилища Title](atoc-reference-storagev2.md)

   