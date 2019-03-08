---
title: GET (/serviceconfigs/{scid}/sessions)
assetID: adc65d0b-58dd-bfb9-54c8-9bc9d02e68ec
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidsessionsget.html
description: " GET (/serviceconfigs/{scid}/sessions)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: e54c9cd68a899cfd040bc3e16a05f6deb2daa7c3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57614109"
---
# <a name="get-serviceconfigsscidsessions"></a>GET (/serviceconfigs/{scid}/sessions)
Получает указанные сведения о сеансе.

> [!IMPORTANT]
> Этот метод теперь требуется элемент заголовка X-Xbl-контракт-версии: 104/105 или более поздней версии, при каждом запросе.

  * ["Примечания"](#ID4ET)
  * [Параметры URI](#ID4EKB)
  * [Коды состояния HTTP](#ID4EXB)
  * [Текст запроса](#ID4EAC)
  * [Текст ответа](#ID4ELC)

<a id="ID4ET"></a>


## <a name="remarks"></a>Замечания

Этот метод HTTP/REST извлекает сведения о сеансе для предоставленного фильтров. Этот метод может быть заключена в **Microsoft.Xbox.Services.Multiplayer.MultiplayerService.GetSessionsAsync**.


> [!NOTE] 
> Для многопользовательскую 2015, этот метод вызывается <b>Microsoft.Xbox.Services.Multiplayer.MultiplayerService.GetSessionsForUsersFilterAsync</b>.  



> [!NOTE] 
> Каждый вызов этого метода должен включать ключевое слово, фильтром идентификатора пользователя Xbox или оба. Если вызывающий объект не имеет правильных разрешений для <i>частного</i> и <i>резервирования</i> параметры, метод возвращает код ошибки «403 запрещено» ли все такие сеансы действительно существуют.  


<a id="ID4EKB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- | --- | --- |
| scid| Код GUID| Идентификатор конфигурации службы (SCID). Часть 1 из сеанса идентификатор.|
| Ключевое слово| Строка| Ключевое слово, используемое для фильтрации результатов только сеансы, идентифицировать с помощью этой строки.|
| xuid| Код GUID| Идентификаторы пользователей Xbox для пользователей, для которого нужно получить сеансов. Пользователям должна быть активной в сеансах.|
| резервирования| Строка| Значение, указывающее, если список сеансов включаются те, которые не приняли пользователей. Этот параметр можно задать только значение true. Этот параметр требует, чтобы участник был доступ на уровне сервера к сеансу или XUID вызывающей стороны, отправив запрос на соответствие фильтру идентификатор пользователя Xbox. |
| Неактивные| Строка| Значение, указывающее, если список сеансов включаются те, которые пользователи приняли, но не воспроизводится активно. Этот параметр можно задать только значение true.|
| закрытый| Строка| Значение, указывающее, если список сеансов включает частных сеансов. Этот параметр можно задать только значение true. Допустимо только в том случае, при запросе собственный сеанс, или при запросе сервера на сервер. Задать этому параметру значение true вызывающая программа должна иметь доступ на уровне сервера к сеансу или XUID вызывающей стороны, отправив запрос на соответствие фильтру идентификатор пользователя Xbox. |
| visibility| Строка| Значение перечисления, указывающее состояние видимости, используемые для фильтрации результатов. В настоящее время этот параметр может устанавливаться только к открытым для включения открытых сеансов. См. в разделе <b>MultiplayerSessionVisibility</b>.|
| version| Строка| Положительное целое число, указывающее, версии основных сеанса или нижнем сеансов для включения. Значение должно быть меньше или равна версии контракта запроса остаток от деления 100.|
| Take| Строка| Положительное целое число, указывающее максимальное количество сеансов для извлечения.|

<a id="ID4EXB"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP
Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4EAC"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4ELC"></a>


## <a name="response-body"></a>Тело ответа

Из этого метода возвращается массив JSON ссылки на сеанс, со встроенным данных включены некоторые сеанса.


```cpp
{
    "results": [ {
            "xuid": "9876",    // If the session was found from a xuid, that xuid.
            "startTime": "2009-06-15T13:45:30.0900000Z",
            "sessionRef": {
                "scid": "foo",
                "templateName": "bar",
                "name": "session-seven"
            },
            "accepted": 4,    // Approximate number of non-reserved members.
            "status": "active",    // or "reserved" or "inactive". This is the state of the user in the session, not the session itself. Only present if the session was found using a xuid.
            "visibility": "open",    // or "private", "visible", or "full"
            "joinRestriction": "local",    // or "followed". Only present if 'visibility' is "open" or "full" and the session has a join restriction.
            "myTurn": true,    // Not present is the same as 'false'. Only present if the session was found using a xuid.
            "keywords": [ "one", "two" ]
        }
    ]
}

```


<a id="ID4EWC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EYC"></a>


##### <a name="parent"></a>Parent

[/serviceconfigs/ {scid} / сеансов](uri-serviceconfigsscidsessions.md)
