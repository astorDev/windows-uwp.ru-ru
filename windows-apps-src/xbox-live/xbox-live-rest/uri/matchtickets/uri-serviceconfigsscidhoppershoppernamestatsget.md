---
title: GET (/serviceconfigs/{scid}/hoppers/{name}/stats)
assetID: 4de5b07d-93e1-8ff0-05dd-1d3bb1802088
permalink: en-us/docs/xboxlive/rest/uri-serviceconfigsscidhoppershoppernamestatsget.html
description: " GET (/serviceconfigs/{scid}/hoppers/{name}/stats)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 95de95b35de496331dd3fe0a4c69f18e047c1020
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57621699"
---
# <a name="get-serviceconfigsscidhoppersnamestats"></a>GET (/serviceconfigs/{scid}/hoppers/{name}/stats)

Получает статистику для hopper.

> [!IMPORTANT]
> Этот метод предназначен для использования с контрактом 103 или более поздней версии и требуется элемент заголовка X-Xbl-контракт-версии: 103 или более поздней версии, при каждом запросе.

  * ["Примечания"](#ID4ET)
  * [Параметры URI](#ID4E5)
  * [Авторизации](#ID4EJB)
  * [Коды состояния HTTP](#ID4E3C)
  * [Текст запроса](#ID4EFD)
  * [Текст ответа](#ID4EQD)

<a id="ID4ET"></a>


## <a name="remarks"></a>Замечания
Этот метод HTTP/REST возвращает статистические данные из именованного hopper в конфигурации службы уровень идентификатор (SCID). Этот метод может быть заключена в **Microsoft.Xbox.Services.Matchmaking.MatchmakingService.GetHopperStatisticsAsync** API.  
<a id="ID4E5"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| scid| Код GUID| Служба идентификатор конфигурации (SCID) для сеанса.|
| name| Строка| Имя hopper.|

<a id="ID4EJB"></a>


## <a name="authorization"></a>Authorization

| Тип| Обязательно| Описание| Ответ, если он отсутствует|
| --- | --- | --- | --- | --- | --- | --- | --- |
| XUID (идентификатор пользователя)| Да| Имя пользователя, отправившего запрос должен быть членом билет сеанса, на который ссылается билета. | 403|
| Привилегии и тип устройства| Да| Если Тип_устройства пользователя имеет значение в консоль, для звонков в службу поддержки знакомства допускаются только пользователи с многопользовательской привилегии в утверждения. | 403|
| Идентификатор Title/проверки типа покупки/устройства| Да| Заголовок, который соответствует в необходимо разрешить координирующему утверждение указанный заголовок, сочетание типов устройств. | 403|

<a id="ID4E3C"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP
Служба возвращает код состояния HTTP, применительно к MPSD.  
<a id="ID4EFD"></a>


## <a name="request-body"></a>Тело запроса

Объекты не будут отправлены в тексте этого запроса.

<a id="ID4EQD"></a>


## <a name="response-body"></a>Тело ответа

| Участник| Тип| Описание|
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| hopperName| Строка| Имя выбранного hopper.|
| Время ожидания| 32-разрядное знаковое целое число| Среднее значение соответствия время hopper (целое количество секунд). |
| Заполнение| 32-разрядное знаковое целое число| Число людей, Ожидание совпадений в hopper.|

<a id="ID4E1D"></a>


### <a name="sample-response"></a>Пример ответа


```cpp
{
      "hopperName":"contosoawesome2",
      "waitTime":30,
      "population":1
    }


```


<a id="ID4EJE"></a>


## <a name="see-also"></a>См. также

<a id="ID4ELE"></a>


##### <a name="parent"></a>Parent  

[/hoppers/ /serviceconfigs/ {scid} {name} / stats](uri-serviceconfigsscidhoppershoppernamestats.md)
