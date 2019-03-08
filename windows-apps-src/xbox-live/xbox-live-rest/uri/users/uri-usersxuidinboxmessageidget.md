---
title: GET (/users/xuid({xuid})/inbox/{messageId})
assetID: d76563d0-2c74-0308-054b-762c80392a02
permalink: en-us/docs/xboxlive/rest/uri-usersxuidinboxmessageidget.html
description: " GET (/users/xuid({xuid})/inbox/{messageId})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 29b4c57468148a431a10e0d74f85d360ff0992b3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57618059"
---
# <a name="get-usersxuidxuidinboxmessageid"></a>GET (/users/xuid({xuid})/inbox/{messageId})
Извлекает текст подробные сообщения для сообщения определенному пользователю, пометив его как чтения, в службе.
Доменом для таких URI является `msg.xboxlive.com`.

  * ["Примечания"](#ID4EV)
  * [Параметры URI](#ID4EEB)
  * [Авторизации](#ID4ERB)
  * [Текст запроса](#ID4E3B)
  * [Влияние параметров конфиденциальности для ресурса](#ID4EJC)
  * [Коды состояния HTTP](#ID4EUC)
  * [Ответ JavaScript Object Notation (JSON)](#ID4EUE)

<a id="ID4EV"></a>


## <a name="remarks"></a>Замечания

Операция получения может выполняться только на пользователя, системы и FriendRequest типы сообщений.

Этот URI требует обновления на сайте Xbox.com. В настоящее время Xbox 360 не будут обновляться прочитанного и непрочитанного состояние, пока пользователь выходит из системы и повторно войдите.

Только содержимое, этот API поддерживает имеет тип «application/json», который необходим в заголовках HTTP каждого вызова.

<a id="ID4EEB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| xuid | 64-разрядных целых беззнаковых | Идентификатор Xbox пользователя (XUID), игрок, который выполняет запрос. |
| код (ID) | строки [50] | Идентификатор сообщения полученные или удален. |

<a id="ID4ERB"></a>


## <a name="authorization"></a>Authorization

Необходимо иметь пользовательского утверждения для получения сообщения пользователь.

<a id="ID4E3B"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4EJC"></a>


## <a name="effect-of-privacy-settings-on-resource"></a>Влияние параметров конфиденциальности для ресурса

Только вы можете извлечь собственных сообщений пользователя.

<a id="ID4EUC"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP

Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).

| Код| Описание|
| --- | --- | --- | --- | --- |
| 200| Успешное выполнение.|
| 400| Не удается правильно преобразовать XUID.|
| 403| Невозможно преобразовать XUID или не удается найти допустимый XUID утверждения.|
| 404| Отсутствует допустимый XUID, или идентификатор сообщения не удается найти или неправильного анализа.|
| 500| Общая ошибка на сервере, или тип сообщений не является допустимым для GET.|

<a id="ID4EUE"></a>


## <a name="javascript-object-notation-json-response"></a>Ответ JavaScript Object Notation (JSON)

Если вызывается успешно, служба возвращает данные результатов в формате JSON. Корневой объект является объектом UserMessageHeader.

#### <a name="usermessageheader"></a>UserMessageHeader

| Свойство| Тип| Максимальная длина| Замечания|
| --- | --- | --- | --- |
| header| Заголовок|  | Объект JSON|
| messageText| Строка| 256| UTF-8|

#### <a name="header"></a>Заголовок

| Свойство| Тип| Максимальная длина| Замечания|
| --- | --- | --- | --- |
| Отправлено| DateTime|  | Дата и время отправки сообщения. (Предоставляемых службой).|
| истечение срока действия| DateTime|  | Дата и время истечения срока действия сообщения. (Все сообщения имеют максимальный срок жизни, должны определяться в будущем).|
| MessageType| Строка| 13| Типы сообщений: Пользователь, системный, FriendRequest.|
| senderXuid| ulong|  | XUID отправителя.|
| Отправитель| Строка| 15| Тег игрока отправителя.|
| hasAudio| bool|  | Имеет ли сообщение вложения аудио (голос).|
| hasPhoto| bool|  | Сообщение, имеет ли вложение фотографии.|
| hasText| bool|  | Является ли сообщение содержит текст.|

#### <a name="sample-response"></a>Пример ответа

```cpp
{
          "header":
          {
            "expiration":"2011-10-11T23:59:59.9999999",
            "messageType":"User",
            "senderXuid":"123456789",
            "sender":"Striker",
            "sent":"2011-05-08T17:30:00Z",
            "hasAudio":false,
            "hasPhoto":false,
            "hasText":true
          },
        "messageText":"random user text up to 256 characters"
        }

```

#### <a name="error-response"></a>Сообщение об ошибке

В случае ошибки служба может возвращать объект errorResponse, который может содержать значения из среды службы.

| Свойство| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| d, источник ошибки| Строка| Указывает, где возникла ошибка.|
| код ошибки| int| Числовой код, с которым связана ошибка (может иметь значение null).|
| Сообщение об ошибке| Строка| Сведения об ошибке, если настроен для отображения сведений.|

<a id="ID4E3DAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4E5DAC"></a>


##### <a name="parent"></a>Parent  

[/Users/xuid({xuid})/Inbox](uri-usersxuidinbox.md)


<a id="ID4EMEAC"></a>


##### <a name="reference--standard-http-status-codesadditionalhttpstatuscodesmd"></a>Справочник по [коды состояния Standard HTTP](../../additional/httpstatuscodes.md)
