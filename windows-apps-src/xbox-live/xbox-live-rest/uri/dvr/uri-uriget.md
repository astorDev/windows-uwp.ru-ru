---
title: GET (/{uri})
assetID: a67a3288-88f9-c504-5fa8-8fd06055d079
permalink: en-us/docs/xboxlive/rest/uri-uriget.html
description: " GET (/{uri})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 757d84c9ad5a005e042b42d699ada08504dc57ff
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57650619"
---
# <a name="get-uri"></a>GET (/{uri})
Скачайте игр клипов. Домены для таких URI не `gameclipsmetadata.xboxlive.com` и `gameclipstransfer.xboxlive.com`, в зависимости от функции в URI.
 
  * ["Примечания"](#ID4EX)
  * [Параметры URI](#ID4EDB)
  * [Требуемые заголовки запросов](#ID4EEC)
  * [Необязательные заголовки запросов](#ID4EQE)
  * [Текст запроса](#ID4EZF)
  * [Заголовки ответа требуется](#ID4EEG)
  * [Коды состояния HTTP](#ID4EYAAC)
  * [Заголовки ответа необязательно](#ID4EOFAC)
  * [Текст ответа](#ID4EOGAC)
 
<a id="ID4EX"></a>

 
## <a name="remarks"></a>Замечания
 
Клиент можно загрузить любой ролика или эскиз, достигает состояния опубликовано и загрузки типа, как указано в **GameClipUri** объекта. URI для запроса файла добавляется в тексте ответа, когда получение списка пользователя или открытый клипов.
  
<a id="ID4EDB"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| <b>URI</b>| Строка| <b>Uri</b> поле в пределах <b>GameClipUri</b> объекта.| 
  
<a id="ID4EEC"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | 
| Authorization| Строка| Учетные данные проверки подлинности для проверки подлинности HTTP. Примеры значений <b>XAUTH =&lt;authtoken ></b>| 
| X-RequestedServiceVersion| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос будет перенаправлен только к этой службе после проверки допустимости заголовок, утверждения в маркер проверки подлинности и т. д. Примеры 1 vnext.| 
| Content-Type| Строка| Тип MIME тела ответа. Пример: <b>application/json</b>.| 
| Принять| Строка| Допустимое значение Content-Type. Пример: <b>application/json</b>.| 
| Cache-Control| Строка| Мягкое запрос для указания поведения кэширования.| 
  
<a id="ID4EQE"></a>

 
## <a name="optional-request-headers"></a>Необязательные заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Accept-Encoding| Строка| Алгоритмы сжатия приемлемым. Примеры значений: gzip, deflate, удостоверение.| 
| ETag| Строка| Используется для оптимизации кэша. Пример значения: «686897696a7c876b7e».| 
  
<a id="ID4EZF"></a>

 
## <a name="request-body"></a>Тело запроса
 
Объекты не будут отправлены в тексте этого запроса.
  
<a id="ID4EEG"></a>

 
## <a name="required-response-headers"></a>Заголовки ответа требуется
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| X-RequestedServiceVersion| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос будет перенаправлен только к этой службе после проверки допустимости заголовок, утверждения в маркер проверки подлинности и т. д. Примеры 1 vnext.| 
| Content-Type| Строка| Тип MIME тела ответа. Пример: <b>application/json</b>.| 
| Cache-Control| Строка| Мягкое запрос для указания поведения кэширования.| 
| Принять| Строка| Допустимое значение Content-Type. Пример: <b>application/json</b>.| 
| Retry-After| Строка| Указывает, что клиенту в случае сервер недоступен, повторите попытку позже.| 
| Различаются| Строка| Указывает подчиненный учетных записей-посредников, как кэшировать ответы.| 
  
<a id="ID4EYAAC"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 200| ОК| Сеанс был успешно извлечен.| 
| 301| Перемещено навсегда| Служба была перемещена на другой URI.| 
| 307| Временное перенаправление| Служба была перемещена на другой URI.| 
| 400| Неверный запрос| Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.| 
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.| 
| 403| Запрещено| Запрос не допускается для пользователя или службы.| 
| 404| Не найден| Не удалось найти указанный ресурс.| 
| 406| Неприемлемо| Версия ресурса не поддерживается.| 
| 408| Время ожидания запроса| Запрос заняло слишком много времени.| 
| 410| Прошло| Запрошенный ресурс больше не доступен.| 
  
<a id="ID4EOFAC"></a>

 
## <a name="optional-response-headers"></a>Заголовки ответа необязательно
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| ETag| Строка| Используется для оптимизации кэша. Пример. «686897696a7c876b7e».| 
  
<a id="ID4EOGAC"></a>

 
## <a name="response-body"></a>Тело ответа
 
<a id="ID4EUGAC"></a>

  
 
В случае успеха сервер возвратит видеоклипа, возможно, обрезанное в соответствии с запрос заголовок диапазона. Усеченный клипа ответ будет Частичное содержимое (206). Если сервер возвращает весь файл, он будет отвечать ОК (200). В случае ошибки **GameClipsServiceErrorResponse** объекта могут быть возвращены вместе с соответствующий код состояния HTTP (например, 416, запрошенный диапазон невыполним).
   
<a id="ID4E4GAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E6GAC"></a>

 
##### <a name="parent"></a>Parent 

[/ {uri}](uri-uri.md)

   