---
title: PUT (/users/me/gamerpic)
assetID: ddf71c62-197d-a81d-35a7-47c6dc9e1b0c
permalink: en-us/docs/xboxlive/rest/uri-usersmegamerpicput.html
description: " PUT (/users/me/gamerpic)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 7aedc7cbd8366c9cb8d3a60e2cb1f5e843b24a8a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57661659"
---
# <a name="put-usersmegamerpic"></a>PUT (/users/me/gamerpic)
Передает gamerpic 1080 x 1080. 
  * [Текст запроса](#ID4EQ)
  * [Коды состояния HTTP](#ID4EZ)
  * [Текст ответа](#ID4EXC)
 
<a id="ID4EQ"></a>

 
## <a name="request-body"></a>Тело запроса
 
Текст запроса является gamerpic (1080 x 1080 PNG-файл).
  
<a id="ID4EZ"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | 
| 200| ОК| Успешно GET.| 
| 201| Создан.| Передача успешно выполнена.| 
| 403| Запрещено| Отменяется право доступа.| 
| 500| Ошибка| Возникла проблема.| 
  
<a id="ID4EXC"></a>

 
## <a name="response-body"></a>Тело ответа
 
Объекты не будут отправлены в тексте ответа.
  
<a id="ID4ECD"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EED"></a>

 
##### <a name="parent"></a>Parent 

[/Users/Me/gamerpic](uri-usersmegamerpic.md)

   