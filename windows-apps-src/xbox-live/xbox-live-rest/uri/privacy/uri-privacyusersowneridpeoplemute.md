---
title: /users/{ownerId}/people/mute
assetID: efb929d8-79a7-83f0-c348-c92ced42bc05
permalink: en-us/docs/xboxlive/rest/uri-privacyusersowneridpeoplemute.html
description: " /users/{ownerId}/people/mute"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 86010d11ff0a7ebe188766f51bc3160a4f2befa4
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57641549"
---
# <a name="usersowneridpeoplemute"></a>/users/{ownerId}/people/mute
Обращается к списке отключить звук для пользователя.

  * [Параметры URI](#ID4EQ)

<a id="ID4EQ"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| ownerId| Строка| Обязательный. Идентификатор пользователя, ресурсом которого осуществляется. Возможными значениями являются «me» <code>xuid({xuid})</code>, или gt({gamertag}). Должен быть авторизованного пользователя. Примеры значений: <code>xuid(2603643534573581)</code>, <code>gt(SomeGamertag)</code>. Максимальный размер: нет. |

<a id="ID4ETB"></a>


## <a name="valid-methods"></a>Допустимые методы

[GET (/users/ {ownerId} / people/Выкл.)](uri-privacyusersowneridpeoplemuteget.md)

&nbsp;&nbsp;Получает список отключить звук для пользователя.

<a id="ID4E4B"></a>


## <a name="see-also"></a>См. также

<a id="ID4E6B"></a>


##### <a name="parent"></a>Parent

[Идентификаторы URI конфиденциальности](atoc-reference-privacyv2.md)
