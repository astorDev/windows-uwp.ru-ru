---
title: PUT (/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName})
assetID: e3e4f164-ac5e-cbd9-8c05-2e1ac00dc55e
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionnameput.html
description: " PUT (/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: d35b3f89f8b866a5236e8f5ac91eb37d9a82d306
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598559"
---
# <a name="put-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionname"></a>PUT (/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName})
Создает, обновляет или подключении к сеансу.

> [!IMPORTANT]
> Этот метод URI требуется элемент заголовка X-Xbl-контракт-версии: 104/105 или более поздней версии, при каждом запросе.

  * ["Примечания"](#ID4ET)
  * [Параметры URI](#ID4EYB)
  * [Коды состояния HTTP](#ID4EFC)
  * [Текст запроса](#ID4EOC)
  * [Текст ответа](#ID4E4C)

<a id="ID4ET"></a>


## <a name="remarks"></a>Замечания

Этот метод HTTP/REST создает соединения, или обновляет сеанс, в зависимости от того, какое подмножество тот же шаблон текста запроса JSON отправляется. В случае успешного выполнения возвращает **MultiplayerSession** объект, содержащий ответ, возвращенный сервером. Атрибуты в нем могут быть отличными от атрибутов в переданный **MultiplayerSession** объекта. Этот метод может быть заключена в **Microsoft.Xbox.Services.Multiplayer.MultiplayerService.WriteSessionAsync**.

Операции создания и обновления сеанса используйте PUT с телом application/json, представляющий для применения изменений. Операции являются идемпотентными, то есть несколько приложений, изменения не действовать дополнительные.

Текст запроса JSON отражает структуру данных сеанса. Все поля и вложенные поля являются необязательными.

Ниже приведен формат подключения для создания сеанса или присоединение режим метод PUT.

> [!NOTE]
> Будьте внимательны при использовании этого шаблона. Обновляет вслепую, применяются независимо от того, текущее состояние сеанса.



```cpp
PUT /serviceconfigs/00000000-0000-0000-0000-000000000000/sessiontemplates/quick/sessions/00000000-0000-0000-0000-000000000001 HTTP/1.1
         Content-Type: application/json

```



Ниже приведен формат подключения для режима обновления сеанса метод PUT.

```cpp
PUT /serviceconfigs/00000000-0000-0000-0000-000000000000/sessiontemplates/quick/sessions/00000000-0000-0000-0000-000000000001 HTTP/1.1
         Content-Type: application/json

```



Ниже приведен формат подключения для метода PUT для обновления свойств сеанса. Это эквивалентно операцию PUT в сеанс URI с текстом с ничего, кроме объекта ниже, как свойства. Разница в том, что эта операция возвращает код ошибки 404 не найдено, если сеанс не существует. Эта операция поддерживает Заголовок If-Match.

```cpp
PUT /serviceconfigs/00000000-0000-0000-0000-000000000000/sessiontemplates/quick/sessions/00000000-0000-0000-0000-000000000001/properties HTTP/1.1
         Content-Type: application/json

         { "system": { }, "custom": { } }

```



<a id="ID4EYB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- | --- |
| scid| Код GUID| Идентификатор конфигурации службы (SCID). Часть 1 из идентификатор сеанса.|
| sessionTemplateName| Строка| Имя текущего экземпляра шаблона сеанса. Часть 2 из идентификатор сеанса.|
| Имя сеанса| Код GUID| Уникальный идентификатор сеанса. Часть 3 из идентификатор сеанса.|

<a id="ID4EFC"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP
Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4EOC"></a>


## <a name="request-body"></a>Тело запроса

Ниже приведен пример текста запроса для создания или присоединение к сеансу. Следующие элементы текста запроса являются необязательными. Все возможные члены запрещены в запросе.

| Участник| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- |
| Константы| Объект| Параметры только для чтения, которые объединяются с помощью шаблона сеанса для создания константы для сеанса. |
| свойства | Объект | Изменения объединены в свойствах сеанса.|
| Members.Me | Объект| Константы и свойства, которые очень как их аналоги верхнего уровня. Любой метод PUT пользователь должен быть участником сеанса и добавляет пользователя, при необходимости. Если «me» задается как значение null, элемент, выполняющего запрос удаляется из сеанса. |
| члены | Объект| Другие объекты, представляющие пользователям добавлять к сеансу, ключом которого является отсчитываемый от нуля индекс. Число элементов в запросе всегда начинается с 0, даже если сеанс уже элементов. Элементы добавляются в сеанс, в порядке, в котором они появляются в запросе. Свойства элементов можно задать только для пользователя, которому они принадлежат. |
| . | Объект| Значения, указывающие, обновления и дополнения к сеансу набор участников связанным сервером. Если сервер указан как null, запись этого сервера удаляется из сеанса. |



```cpp
{
  "properties": {
    "custom": {"KANWE": "MGMSY"},
    "system": {}
  },
  "constants": {
    "custom": {},
    "system": {"visibility": "open"}
  },
  "members": {
    "reserve_0": {
    "constants": {
      "custom": {"type": "leader"},
      "system": {"xuid": "5500461"} }}
   }
}

```


<a id="ID4E4C"></a>


## <a name="response-body"></a>Тело ответа

Пример текста ответа для создания или присоединение к сеансу:


```cpp
{
  "contractVersion": 104,
  "correlationId": "0FE81338-EE96-46E3-A3B5-2DBBD6C41C3B",
  "nextTimer": "2009-06-15T13:45:30.0900000Z",

  "initializing": {
    "stage": "measuring",
    "stageStartTime": "2009-06-15T13:45:30.0900000Z",
    "episode": 1
  },

  "hostCandidates": [ "ab90a362", "99582e67" ],

  "constants": {
    "system": {"visibility": "open"},
    "custom": {}
  },

  "properties": {
     "system": { "turn": [] },
     "custom": { "myProperty": "myValue" }
  },

  "members": {
      "1": {
        "properties": {
        "system": { },
        "custom": { }
      },

      "constants": {
        "system": { "xuid": "5500461" },
        "custom": { }
      }

      "gamertag": "stacy",
      "deviceToken": "9f4032ba7",
      "reserved": true,
      "activeTitleId": "8397267",
      "joinTime": "2009-06-15T13:45:30.0900000Z",
      "turn": true,
      "initializationFailure": "latency",
      "initializationEpisode": 1,
      "next": 4
    },
  },

  "membersInfo": {
      "first": 1,
      "next": 4,
      "count": 1,
      "accepted": 0
  },

  "servers": {
      "name": {
        "constants": { },
        "properties": { }
      }
  }
}

```


<a id="ID4EID"></a>


## <a name="see-also"></a>См. также

<a id="ID4EKD"></a>


##### <a name="parent"></a>Parent

[/serviceconfigs/{scid}/sessiontemplates/{sessionTemplateName}/sessions/{sessionName}](uri-serviceconfigsscidsessiontemplatessessiontemplatenamesessionssessionname.md)
