---
title: POST (/serviceconfigs/{scid}/hoppers/{hoppername})
assetID: 8cbf62aa-d639-e920-1e39-099133af17f8
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidhoppershoppernamepost.html
description: " POST (/serviceconfigs/{scid}/hoppers/{hoppername})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 7682b92ec61c98679904825e360d73318e9fee90
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57659839"
---
# <a name="post-serviceconfigsscidhoppershoppername"></a>POST (/serviceconfigs/{scid}/hoppers/{hoppername})

Создает билет соответствие.

> [!IMPORTANT]
> Этот метод предназначен для использования с контрактом 103 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 103 или более поздней версии, при каждом запросе.

  * ["Примечания"](#ID4ET)
  * [Параметры URI](#ID4E5)
  * [Авторизации](#ID4EJB)
  * [Коды состояния HTTP](#ID4E3C)
  * [Текст запроса](#ID4EFD)
  * [Текст ответа](#ID4E3G)

<a id="ID4ET"></a>


## <a name="remarks"></a>Замечания

Этот метод HTTP/REST создает запрос hopper в соответствие с определенным именем в конфигурации службы уровень идентификатор (SCID). Этот метод может быть заключена в **Microsoft.Xbox.Services.Matchmaking.MatchmakingService.CreateMatchTicketAsync** метод.  
<a id="ID4E5"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| scid| Код GUID| Служба идентификатор конфигурации (SCID) для сеанса.|
| hoppername | Строка | Имя hopper. |

<a id="ID4EJB"></a>


## <a name="authorization"></a>Authorization

| Тип| Обязательно| Описание| Ответ, если он отсутствует|
| --- | --- | --- | --- | --- | --- | --- | --- |
| Привилегии и тип устройства| Да| Если Тип_устройства пользователя имеет значение в консоль, для звонков в службу поддержки знакомства допускаются только пользователи с многопользовательской привилегии в утверждения. | 403|
| Тип устройства| Да| При Тип_устройства пользователя отсутствует, или набор не-консоль, title, сопоставляемого в не должен быть заголовок только для консоли. | 403|
| Идентификатор Title/проверки типа покупки/устройства| Да| Заголовок, который соответствует в необходимо разрешить координирующему утверждение указанный заголовок, сочетание типов устройств. | 403|

<a id="ID4E3C"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP
Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4EFD"></a>


## <a name="request-body"></a>Тело запроса

<a id="ID4ELD"></a>


### <a name="required-members"></a>Обязательные члены

| Участник| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| serviceConfig| Код GUID| SCID для сеанса.|
| hopperName| Строка| Имя hopper.|
| giveUpDuration| 32-разрядное знаковое целое число| Максимальное время ожидания (целое количество секунд).|
| preserveSession| перечисление| Значение, указывающее, если сеанс повторно используется в качестве сеанса, куда должны совпадать. Возможными значениями являются «всегда» и «никогда». |
| ticketSessionRef| MultiplayerSessionReference| Объект MultiplayerSessionReference для сеанса, в котором проигрыватель или группы в настоящее время воспроизведения. |
| ticketAttributes| Коллекция объектов| Атрибуты и значения, предоставленные пользователю о группе игроков.|

<a id="ID4EXF"></a>


### <a name="prohibited-members"></a>Запрещенные элементы

Все остальные элементы запрещены в запросе.

<a id="ID4ECG"></a>


### <a name="sample-request"></a>Пример запроса

Сеанс ссылается **ticketSessionRef** объект должен быть создан до того, как можно создать билет совпадение сеанс должен содержать игроков сопоставляться; вместе с их атрибутами конкретного проигрывателя. Каждый игрок необходимо создать или подключиться к сеансу с MPSD, добавление связанные сопоставления атрибутов в сеанс. Совпадение атрибуты помещаются в поле пользовательского свойства с именем matchAttrs на каждого игрока.

Отправляется запрос на создание или соединение **https://sessiondirectory.xboxlive.com/serviceconfigs/{scid}/sessiontemplates/{templatename}/sessions/{sessionname}** и может выглядеть следующим образом:


```cpp
{
   "members": {
     "me": {
       "constants": {
         "system": {
           "xuid": 2535285330879750
         }
      },
      "properties": {
         "custom": {
           "matchAttrs": {
             "skill": 5,
             "ageRange": "teenager"
           }
         }
      }
    }
  }
}

```


После создания сеанса, заголовок может вызывать службу партнеров в службу для этого сеанса.


> [!NOTE] 
> Заголовок можно разрешить пользователю повторите вызов, но не следует повторять ее автоматически в случае отрицательного результата.  



```cpp
POST /serviceconfigs/{scid}/hoppers/{hoppername}

{
  "giveUpDuration":10,
  "preserveSession": "never",
  "ticketSessionRef": {
     "scid": "ABBACDDC-0000-0000-0000-000000000001",  
     "templateName": "TestTemplate",
     "name": "5E55104-0000-0000-0000-000000000001"
  },
  "ticketAttributes": {
    "desiredMap": "Test Map",
    "desiredGameType": "Test GameType"
  }
}

```


<a id="ID4E3G"></a>


## <a name="response-body"></a>Тело ответа

| Участник| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| TicketId| Код GUID| Идентификатор для создания билета.|
| Время ожидания| 32-разрядное знаковое целое число| Среднее время ожидания для hopper (целое количество секунд).|


```cpp
{
  "ticketId":"0584338f-a2ff-4eb9-b167-c0e8ddecae72",
  "waitTime":60
}

```


<a id="ID4EHAAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EJAAC"></a>


##### <a name="parent"></a>Parent  

[/hoppers/ /serviceconfigs/ {scid} {hoppername}](uri-serviceconfigsscidhoppershoppername.md)
