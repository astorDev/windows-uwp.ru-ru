---
title: PUT (/handles/{handle-id}/session)
assetID: d8a3d473-1192-ec0c-3935-c301f4f61e03
permalink: en-us/docs/xboxlive/rest/uri-handleshandleidsessionput.html
description: " PUT (/handles/{handle-id}/session)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 3a1872857d8b8e692f67e3c7b2a067ae86663c00
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57641609"
---
# <a name="put-handleshandle-idsession"></a>PUT (/handles/{handle-id}/session)
Создает или обновляет сеанс, разыменование дескриптора.

> [!IMPORTANT]
> Этот метод используется многопользовательскую 2015 и применяется только к этой многопользовательские версии и более поздних версий. Он предназначен для использования с контрактом шаблона 104/105 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 104/105 или более поздней версии, при каждом запросе.

  * ["Примечания"](#ID4ET)
  * [Параметры URI](#ID4ECB)
  * [Коды состояния HTTP](#ID4ENB)
  * [Текст запроса](#ID4EUB)
  * [Текст ответа](#ID4E6B)

<a id="ID4ET"></a>


## <a name="remarks"></a>Замечания

Этот метод HTTP/REST записывает новые или обновленные сеанса в службу в многопользовательской, используя идентификатор дескриптора указанного сеанса. Результатом является объект, представляющий сеанс новых или обновленных как возвращаемые с сервера. Этот метод может быть заключена в **Microsoft.Xbox.Services.Multiplayer.MultiplayerService.WriteSessionByHandleAsync**.

Объект, вызывающий этот метод получает идентификатор дескриптора из игрока **MultiplayerActivityDetails** объекта. Кроме того вызывающий объект получает идентификатор из активации протокола после пользователь принял игр приглашения.

<a id="ID4ECB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| handleId| Код GUID| Уникальный идентификатор маркера сеанса.|

<a id="ID4ENB"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP
Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4EUB"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4E6B"></a>


## <a name="response-body"></a>Тело ответа

Объекты не будут отправлены в тексте ответа.

<a id="ID4EKC"></a>


## <a name="see-also"></a>См. также

<a id="ID4EMC"></a>


##### <a name="parent"></a>Parent

[/Handles/ {handleId} / сеанса](uri-handleshandleidsession.md)
