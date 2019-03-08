---
title: /json/users/batch/scids/{scid}/data/{pathAndFileName},json
assetID: 06ae159f-7739-1330-df15-871d260e6ba1
permalink: en-us/docs/xboxlive/rest/uri-jsonusersbatchscidssciddatapathandfilenametype.html
description: " /json/users/batch/scids/{scid}/data/{pathAndFileName},json"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 2b28b0faad1c321137344455bc7cd471f7cb6eba
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598599"
---
# <a name="jsonusersbatchscidssciddatapathandfilenamejson"></a>/json/users/batch/scids/{scid}/data/{pathAndFileName},json
Скачивает несколько файлов из нескольких пользователей с тем же именем файла. Доменом для таких URI является `titlestorage.xboxlive.com`.
 
  * [Параметры URI](#ID4EV)
 
<a id="ID4EV"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| scid| guid| Идентификатор конфигурации службы для поиска.| 
| pathAndFileName| Строка| Путь и имя файла для элемента, чтобы получить доступ. Допустимые символы для части пути (вплоть до косой черты) содержат прописные буквы (A-Z), строчные буквы (a – z), цифры (0 – 9), подчеркивание (_) и косая черта (/). Компонент пути может быть пустым. Допустимые символы для части имени файла (все, что после косой черты) содержат прописные буквы (A-Z), строчные буквы (a – z), цифры (0 – 9), подчеркивание (_), точка (.) и дефис (-). Имя файла не может быть пустым, заканчиваться точкой или содержать две точки подряд.| 
  
<a id="ID4E3B"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[ПОМЕСТИТЬ](uri-jsonusersbatchscidssciddatapathandfilenametype-post.md)

&nbsp;&nbsp;Скачивает несколько файлов из нескольких пользователей с тем же именем файла. Файл для загрузки определяется URI запроса. Текст запроса содержит список XUIDs пользователей, файлы для загрузки. Текст ответа будет составное сообщение MIME, и в каждой части, представляющий файл для конкретного пользователя с свой собственный набор заголовков. Это возможно для частей длину успешных и неудачных попыток ответа.
 
<a id="ID4EGC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EIC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI хранилища Title](atoc-reference-storagev2.md)

   