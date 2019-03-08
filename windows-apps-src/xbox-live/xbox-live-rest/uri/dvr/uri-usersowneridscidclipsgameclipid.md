---
title: /users/{ownerId}/scids/{scid}/clips/{gameClipId}
assetID: 49b68418-71f1-c5a2-3a9b-869fd1fa663c
permalink: en-us/docs/xboxlive/rest/uri-usersowneridscidclipsgameclipid.html
description: " /users/{ownerId}/scids/{scid}/clips/{gameClipId}"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: e7ea92e89d54df17e8d82084d840a7ee9ef7d032
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57658339"
---
# <a name="usersowneridscidsscidclipsgameclipid"></a>/users/{ownerId}/scids/{scid}/clips/{gameClipId}
Доступ к игр клип из системы, если известны все идентификаторы для ее поиска. Домены для таких URI не `gameclipsmetadata.xboxlive.com` и `gameclipstransfer.xboxlive.com`, в зависимости от функции в URI.
 
  * [Параметры URI](#ID4EX)
 
<a id="ID4EX"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| ownerId| Строка| Удостоверение пользователя, ресурсом которого осуществляется пользователя. Поддерживаемые форматы: «me» или «xuid(123456789)». Максимальная длина: 16.| 
| scid| Строка| Идентификатор конфигурации службы ресурса, к которому выполняется обращение. Должно соответствовать SCID пользователя, прошедшего проверку подлинности.| 
| gameClipId| Строка| GameClip идентификатор ресурса, к которому выполняется обращение.| 
  
<a id="ID4EFC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[GET (/users/{ownerId}/scids/{scid}/clips/{gameClipId})](uri-usersowneridscidclipsgameclipidget.md)

&nbsp;&nbsp;Получение игр клип из системы, если известны все идентификаторы для ее поиска.
 
<a id="ID4EPC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ERC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI DVR для игр](atoc-reference-dvr.md)

   