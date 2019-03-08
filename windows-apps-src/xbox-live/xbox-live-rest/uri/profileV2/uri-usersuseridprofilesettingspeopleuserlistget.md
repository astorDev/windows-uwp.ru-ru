---
title: GET (/users/{userId}/profile/settings/people/{userList})
assetID: f6553499-89e2-f21b-a00f-7e5437c045ff
permalink: en-us/docs/xboxlive/rest/uri-usersuseridprofilesettingspeopleuserlistget.html
description: " GET (/users/{userId}/profile/settings/people/{userList})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: f868fdf4f3d5cd36000784d9c5a3437fa5d67ffa
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57593859"
---
# <a name="get-usersuseridprofilesettingspeopleuserlist"></a>GET (/users/{userId}/profile/settings/people/{userList})
Получить профиль для пользователя или пользователей с моникером людей поддержки. Доменом для таких URI является `profile.xboxlive.com`.
 
  * ["Примечания"](#ID4EV)
  * [Параметры URI](#ID4EKB)
  * [Параметры строки запроса](#ID4EVB)
  * [Требуемые заголовки запросов](#ID4EQC)
  * [Текст запроса](#ID4E2D)
 
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания
 
**userList** и **недопустимость** являются взаимоисключающими параметрами. Если указаны оба или любой, вы получите **BadRequest** обратно. **userList** представляет собой массив для перспективность в сценариях, где несколько именованных списки удобно использовать для запроса. **Недопустимость** состоит десятичных строк для XUIDs - JSON неплохо сериализации 64-разрядных целых чисел. Наконец, параметры в Xbox One будет называться параметры с именами обычный удобное для восприятия, а не 64-разрядных целых беззнаковых чисел или непонятные константы, например **XONLINE_PROFILE_ASDF**.
  
<a id="ID4EKB"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| Идентификатор пользователя| Строка| Может быть либо «xuid(12345)», «gt(myGamertag)» или «me».| 
| userList| Строка| Именованный список людей, чтобы получить параметры. Сейчас люди — поддерживается только список.| 
  
<a id="ID4EVB"></a>

 
## <a name="query-string-parameters"></a>Параметры строки запроса
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | 
| параметры| Строка| Разделенный запятыми список имена параметров.| 
  
<a id="ID4EQC"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| x-xbl контракт version| 32-разрядное знаковое целое число| Значение = 2| 
| Тип содержимого| Строка| Значение = <code>application/json</code>| 
  
<a id="ID4E2D"></a>

 
## <a name="request-body"></a>Тело запроса
 
<a id="ID4EBE"></a>

 
### <a name="sample-request"></a>Пример запроса
 

```cpp
GET /users/me/profile/settings/people/people?settings=GameDisplayName,GameDisplayPicRaw,Gamerscore,Gamertag
      
```

  
<a id="ID4EKE"></a>

  
 
<a id="ID4EME"></a>

 
##### <a name="response-body"></a>Тело ответа 
Ответ — **ReadMultiSettingsResponseV2** объекта. При условии, что вызывающий пользователь имеет только один дружественной:
  

```cpp
{
      "profileUsers":[
         {
            "id":"2533274791381930",
            "settings":[
               {
                  "id":"GameDisplayName",
                  "value":"John Smith"
               },
               {
                  "id":"GameDisplayPicRaw",
                  "value":"http://images-eds.xboxlive.com/image?url=z951ykn43p4FqWbbFvR2Ec.8vbDhj8G2Xe7JngaTToBrrCmIEEXHC9UNrdJ6P7KIN0gxC2r1YECCd3mf2w1FDdmFCpSokJWa2z7xtVrlzOyVSc6pPRdWEXmYtpS2xE4F&format=png&w=64&h=64"
               },
               {
                  "id":"Gamerscore",
                  "value":"0"
               },
               {
                  "id":"Gamertag",
                  "value":"CracklierJewel9"
               }
            ]
         }
      ]
   }
         
```

   
<a id="ID4E3E"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E5E"></a>

 
##### <a name="parent"></a>Parent 

[/users/{userId}/profile/settings/people/{userList}?settings={settings}](uri-usersuseridprofilesettingspeopleuserlist.md)

 [Профиль (JSON)](../../json/json-profile.md)

   