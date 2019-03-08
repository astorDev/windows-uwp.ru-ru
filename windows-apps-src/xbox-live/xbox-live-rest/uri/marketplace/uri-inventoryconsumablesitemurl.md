---
title: /users/me/consumables/{itemID}
assetID: 45724827-5e35-326f-3f17-f49e606d9e08
permalink: en-us/docs/xboxlive/rest/uri-inventoryconsumablesitemurl.html
description: Конечная точка rESTful для Xbox готовых к использованию элементов для пользователя.
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 4822180f11879417be67351f901474a38f89d82e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57614089"
---
# <a name="usersmeconsumablesitemid"></a>/users/me/consumables/{itemID}
Доступ, доступ к полному набору сведений для конкретному элементу готовых к использованию.
Доменом для таких URI является `inventory.xboxlive.com`.

  * [Параметры URI](#ID4EV)

<a id="ID4EV"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| Идентификатор элемента| Строка| Идентификатор, уникально для каждого пользователя для элемента единственного числа инвентаризации|

<a id="ID4ERB"></a>


## <a name="valid-methods"></a>Допустимые методы

[POST ({itemID})](uri-inventoryconsumablesitemurlpost.md)

&nbsp;&nbsp;Указывает, что все или часть элемента готовых к использованию инвентаризации было использовано и уменьшает количество потребляемых указанного.

<a id="ID4E4B"></a>


## <a name="see-also"></a>См. также

<a id="ID4E6B"></a>


##### <a name="parent"></a>Parent

[Идентификаторы URI Marketplace](atoc-reference-marketplace.md)


<a id="ID4EJC"></a>


##### <a name="further-information"></a>Дополнительные сведения

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)
