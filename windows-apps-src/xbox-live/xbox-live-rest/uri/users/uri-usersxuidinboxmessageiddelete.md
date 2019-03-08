---
title: DELETE (/users/xuid({xuid})/inbox/{messageId})
assetID: c54eede3-3e3b-2cbe-1be9-8bf3a48171bc
permalink: en-us/docs/xboxlive/rest/uri-usersxuidinboxmessageiddelete.html
description: " DELETE (/users/xuid({xuid})/inbox/{messageId})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 80ec2a462648177cc6bfc846b9c84278821b0e5e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57594109"
---
# <a name="delete-usersxuidxuidinboxmessageid"></a>DELETE (/users/xuid({xuid})/inbox/{messageId})
Удаляет пользователя сообщение в папку "Входящие" пользователя. Доменом для таких URI является `msg.xboxlive.com`.
 
  * ["Примечания"](#ID4EV)
  * [Параметры URI](#ID4ECB)
  * [Авторизации](#ID4EPB)
  * [Текст запроса](#ID4E1B)
  * [Коды состояния HTTP](#ID4EHC)
  * [Ответ JavaScript Object Notation (JSON)](#ID4EAE)
  * [Влияние параметров конфиденциальности для ресурса](#ID4EYF)
 
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания 
 
Операция удаления является идемпотентной.
 
Только содержимое, этот API поддерживает имеет тип «application/json», который необходим в заголовках HTTP каждого вызова. 
  
<a id="ID4ECB"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI) 
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid | 64-разрядных целых беззнаковых | Идентификатор Xbox пользователя (XUID), игрок, который выполняет запрос. | 
| код (ID) | строки [50] | Идентификатор сообщения полученные или удален. | 
  
<a id="ID4EPB"></a>

 
## <a name="authorization"></a>Authorization 
 
Необходимо иметь пользовательского утверждения для удаления сообщения пользователя.
  
<a id="ID4E1B"></a>

 
## <a name="request-body"></a>Тело запроса 
 
Объекты не будут отправлены в тексте этого запроса.
  
<a id="ID4EHC"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP 
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Описание| 
| --- | --- | --- | --- | --- | 
| 204| Успешное выполнение.| 
| 403| Невозможно преобразовать XUID или не удается найти допустимый XUID утверждения.| 
| 404| Идентификатор сообщения в URI не может быть проанализировано или XUID отсутствует в URI.| 
| 500| Общая ошибка на стороне сервера.| 
  
<a id="ID4EAE"></a>

 
## <a name="javascript-object-notation-json-response"></a>Ответ JavaScript Object Notation (JSON) 
 
В случае ошибки служба может возвращать объект errorResponse, который может содержать значения из среды службы.
 
| Свойство| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | 
| d, источник ошибки| Строка| Указывает, где возникла ошибка.| 
| код ошибки| int| Числовой код, с которым связана ошибка (может иметь значение null).| 
| Сообщение об ошибке| Строка| Сведения об ошибке, если настроен для отображения сведений.| 
  
<a id="ID4EYF"></a>

 
## <a name="effect-of-privacy-settings-on-resource"></a>Влияние параметров конфиденциальности для ресурса 
 
Только свои собственные сообщения пользователя можно удалить. 
  
<a id="ID4EDG"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EFG"></a>

 
##### <a name="parent"></a>Parent  

[/Users/xuid({xuid})/Inbox](uri-usersxuidinbox.md)

  
<a id="ID4ETG"></a>

 
##### <a name="reference--standard-http-status-codesadditionalhttpstatuscodesmd"></a>Справочник по [коды состояния Standard HTTP](../../additional/httpstatuscodes.md)

   