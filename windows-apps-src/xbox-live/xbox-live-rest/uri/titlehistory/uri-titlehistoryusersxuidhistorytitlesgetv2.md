---
title: GET (/users/xuid({xuid})/history/titles)
assetID: c0a6cb3b-bab6-03b8-a79e-87defaaa6421
permalink: en-us/docs/xboxlive/rest/uri-titlehistoryusersxuidhistorytitlesgetv2.html
description: " GET (/users/xuid({xuid})/history/titles)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: cadbf38385bbc321ef5bf23eb93c3fbc5c1a2417
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655589"
---
# <a name="get-usersxuidxuidhistorytitles"></a>GET (/users/xuid({xuid})/history/titles)
Получает список заголовков, для которых пользователь разблокировать или добилась успехов по его достижения. Этот API не возвращает полный журнал пользователя воспроизведения или запуске заголовок. Доменом для таких URI является `achievements.xboxlive.com`.
 
  * [Параметры URI](#ID4EY)
  * [Параметры строки запроса](#ID4EDB)
  * [Авторизации](#ID4EFD)
  * [Необязательные заголовки запросов](#ID4EGE)
  * [Текст запроса](#ID4ERF)
 
<a id="ID4EY"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| xuid| 64-разрядного целого числа без знака| Xbox пользователя идентификатор (XUID) пользователя, доступ к журналу которой title.| 
  
<a id="ID4EDB"></a>

 
## <a name="query-string-parameters"></a>Параметры строки запроса
 
| Параметр| Обязательно| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | 
| skipItems| Нет| 32-разрядное знаковое целое число| Возвращает элементы, начиная с версии после заданного числа элементов. Например <b>skipItems = «3»</b> будет извлекать элементы получены, начиная с четвертого элемента. | 
| continuationToken| Нет| Строка| Возвращает элементы, начиная с токен данного продолжения. | 
| maxItems| Нет| 32-разрядное знаковое целое число| Максимальное количество элементов, возвращаемых из коллекции, которые можно объединять с <b>skipItems</b> и <b>continuationToken</b> можно получить диапазон элементов. Служба может предоставлять значение по умолчанию, если <b>maxItems</b> отсутствует и может вернуть меньше, чем <b>maxItems</b>, даже если на последней странице результаты еще не были возвращены. | 
  
<a id="ID4EFD"></a>

 
## <a name="authorization"></a>Authorization
 
| Утверждение| Требуется?| Описание| Поведение, если отсутствует| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Пользователь| Вызывающий объект является авторизованный пользователь Xbox LIVE.| Вызывающий объект должен быть допустимым пользователем в Xbox LIVE.| «403 запрещено»| 
  
<a id="ID4EGE"></a>

 
## <a name="optional-request-headers"></a>Необязательные заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| <b>X-RequestedServiceVersion</b>| Строка| Имя или номер службы Xbox LIVE, к которому следует направлять этот запрос сборки. Запрос будет перенаправлен только к этой службе после проверки допустимости заголовок, утверждения в маркер проверки подлинности и т. д.| 
| <b>x-xbl-contract-version</b>| 32-разрядного целого числа без знака| При наличии и значение 2, на версию V2 этот API будет использоваться. В противном случае V1.| 
  
<a id="ID4ERF"></a>

 
## <a name="request-body"></a>Тело запроса
 
Объекты не будут отправлены в тексте этого запроса.
  
<a id="ID4EDG"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EFG"></a>

 
##### <a name="parent"></a>Parent 

[/Users/xuid({xuid})/History/titles](uri-titlehistoryusersxuidhistorytitlesv2.md)

  
<a id="ID4EPG"></a>

 
##### <a name="reference"></a>Справочные материалы 

[UserTitle (JSON)](../../json/json-usertitlev2.md)

 [PagingInfo (JSON)](../../json/json-paginginfo.md)

 [Параметры разбиения по страницам](../../additional/pagingparameters.md)

   