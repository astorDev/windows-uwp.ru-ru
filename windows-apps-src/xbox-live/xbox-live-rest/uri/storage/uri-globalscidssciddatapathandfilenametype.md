---
title: /global/scids/{scid}/data/{pathAndFileName},{type}
assetID: 774ce2dc-15c5-fe12-42b9-4e040bd4d2cf
permalink: en-us/docs/xboxlive/rest/uri-globalscidssciddatapathandfilenametype.html
description: " /global/scids/{scid}/data/{pathAndFileName},{type}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: d8c58ee4888cbbe7d9a752531c489b1da3fdde86
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57656239"
---
# <a name="globalscidssciddatapathandfilenametype"></a>/global/scids/{scid}/data/{pathAndFileName},{type}
Загружает файл. Доменом для таких URI является `titlestorage.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| scid| guid| Идентификатор конфигурации службы для поиска.| 
| pathAndFileName| Строка| Путь и имя файла для элемента, чтобы получить доступ. Допустимые символы для части пути (вплоть до косой черты) содержат прописные буквы (A-Z), строчные буквы (a – z), цифры (0 – 9), подчеркивание (_) и косая черта (/). Компонент пути может быть пустым. Допустимые символы для части имени файла (все, что после косой черты) содержат прописные буквы (A-Z), строчные буквы (a – z), цифры (0 – 9), подчеркивание (_), точка (.) и дефис (-). Имя файла не может быть пустым, заканчиваться точкой или содержать две точки подряд.| 
| type| Строка| Формат данных. Возможными значениями являются: двоичный файл, конфигурации или json.| 
  
<a id="ID4EFC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[ПОЛУЧИТЬ](uri-globalscidssciddatapathandfilenametype-get.md)

&nbsp;&nbsp;Загружает файл.
 
<a id="ID4EPC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ERC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI хранилища Title](atoc-reference-storagev2.md)

   