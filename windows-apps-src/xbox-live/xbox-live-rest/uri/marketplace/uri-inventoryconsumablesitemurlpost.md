---
title: POST ({itemID})
assetID: 2c3c166b-e638-cfb9-d68e-9f8ab9a838d3
permalink: en-us/docs/xboxlive/rest/uri-inventoryconsumablesitemurlpost.html
description: " POST ({itemID})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 877986ce9d48269295a68dbfd644f14785916b88
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57640859"
---
# <a name="post-itemid"></a>POST ({itemID})
Указывает, что все или часть элемента готовых к использованию инвентаризации было использовано и уменьшает количество потребляемых указанного.
Доменом для таких URI является `inventory.xboxlive.com`.

  * ["Примечания"](#ID4EX)
  * [Параметры URI](#ID4EQB)
  * [Текст запроса](#ID4E2B)
  * [Текст ответа](#ID4ENC)

<a id="ID4EX"></a>


## <a name="remarks"></a>Замечания

   * Если вызывающий объект предложено использовать количество превышает оставшиеся питания элемента, вызов будут отклонены.
   * Количество предложено использовать вызывающий объект должен быть положительным целым числом больше 0. Вызовы со значением потребления, меньшим или равным 0, будут отклонены.
   * Если вызывающий объект предоставляет пустой идентификатор транзакции, запрос будет отклонен.
   * При наличии заголовка утверждение будет записываться таким образом, чтобы его можно будет определить, какой заголовок передает потребление.
   * Дополнительные сообщения с тем же transactionId будет игнорироваться в течение некоторого времени.


> [!NOTE]
> <b>Заголовка x-xbl контракт version</b> для этого API — «4».


<a id="ID4EQB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- | --- |
| Идентификатор элемента| Строка| Идентификатор, уникально для каждого пользователя для элемента единственного числа инвентаризации|

<a id="ID4E2B"></a>


## <a name="request-body"></a>Тело запроса

<a id="ID4EBC"></a>


### <a name="sample-request"></a>Пример запроса


```cpp
{
  "transactionId": String
  "removeQuantity": Int
}

```


Удаление поля «количество» позволяет вызывающему объекту указать количество потребляемых, которым требуется удалить из оставшееся количество потребляемых. Поле идентификатора транзакции предоставляет вызывающему объекту средствами, чтобы повторить попытку с помощью готовых к использованию операции с содержимым, при этом ограничивая риск инвентаризации же использования дважды.

<a id="ID4ENC"></a>


## <a name="response-body"></a>Тело ответа

Ответ на вызов POST, при условии, что он проходит проверку подлинности и назначается контекст соответствующей авторизацией на acknolodgement поступления с тем же transactionId, переданного службе в запросе POST, URL-адрес готовых к использованию элемента и элемента новый значение количества.

<a id="ID4EVC"></a>


### <a name="sample-response"></a>Пример ответа


```cpp
{
  "transactionId": String
  "url": String
  "newQuantity": Int
}

```


<a id="ID4E6C"></a>


## <a name="see-also"></a>См. также

<a id="ID4EBD"></a>


##### <a name="parent"></a>Parent

[/ users/me/расходных / {itemID}](uri-inventoryconsumablesitemurl.md)


<a id="ID4ELD"></a>


##### <a name="further-information"></a>Дополнительные сведения

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)
