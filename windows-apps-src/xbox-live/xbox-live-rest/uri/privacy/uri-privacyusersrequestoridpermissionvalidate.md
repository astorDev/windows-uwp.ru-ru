---
title: /users/{requestorId}/permission/validate
assetID: 400a9721-bf43-76df-4cd1-9f2ae6ca5035
permalink: en-us/docs/xboxlive/rest/uri-privacyusersrequestoridpermissionvalidate.html
description: " /users/{requestorId}/permission/validate"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 4a062fd417bae37fd66c944e0e534ef7a50de5fa
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57599369"
---
# <a name="usersrequestoridpermissionvalidate"></a>/users/{requestorId}/permission/validate
 
  * [Параметры URI](#ID4EQ)
 
<a id="ID4EQ"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| requestorId| Строка| Обязательный. Идентификатор пользователя, выполняющего действие. Возможные значения: <code>xuid({xuid})</code> и <code>me</code>. Это должен быть вошедшего в систему пользователя. Пример значения: <code>xuid(0987654321)</code>.| 
  
<a id="ID4ETB"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (/users/ {requestorId} / разрешение/проверять)](uri-privacyusersrequestoridpermissionvalidateget.md)

&nbsp;&nbsp;Возвращает ответ Да или нет, о том, разрешено ли пользователь для выполнения указанного действия с целевого пользователя.

[POST (/users/ {requestorId} / разрешение/проверять)](uri-privacyusersrequestoridpermissionvalidatepost.md)

&nbsp;&nbsp;Получает набор Да или нет ответов на вопросы о разрешено ли пользователю для выполнения указанных действий с группы целевых пользователей.
 
<a id="ID4EAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ECC"></a>

   [Идентификаторы URI конфиденциальности](atoc-reference-privacyv2.md)

 [Перечисление PermissionId](../../enums/privacy-enum-permissionid.md)

   