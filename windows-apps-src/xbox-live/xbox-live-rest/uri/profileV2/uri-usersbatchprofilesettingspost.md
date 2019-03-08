---
title: POST (/users/batch/profile/settings)
assetID: 2a619148-a626-f413-bda1-a2790063075d
permalink: en-us/docs/xboxlive/rest/uri-usersbatchprofilesettingspost.html
description: " POST (/users/batch/profile/settings)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 0f859a58e32624223d59d918d46f6230a3abd6db
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57662229"
---
# <a name="post-usersbatchprofilesettings"></a>POST (/users/batch/profile/settings)
Получите профиль для пользователя или пользователей. Доменом для таких URI является `profile.xboxlive.com`.
 
  * ["Примечания"](#ID4EV)
  * [Авторизации](#ID4EFB)
  * [Требуемые заголовки запросов](#ID4EOB)
  * [Текст запроса](#ID4EZC)
  * [Текст ответа](#ID4EJD)
 
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания
 
Это только полный URL-адрес профиля в. Все прочие API профиль, от клиентов, блокируются.
  
<a id="ID4EFB"></a>

 
## <a name="authorization"></a>Authorization
 
Доступа к профилю, требуется только маркер обычной проверки подлинности и утверждения.
  
<a id="ID4EOB"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Тип| Описание| 
| --- | --- | --- | 
| x-xbl контракт version| 32-разрядного целого числа без знака| Версия контракта должно быть присвоено 2, чтобы отличать этот вызов из Xbox 360 API.| 
| Тип содержимого| Строка| Значение = <code>application/json</code>| 
  
<a id="ID4EZC"></a>

 
## <a name="request-body"></a>Тело запроса
 
<a id="ID4E6C"></a>

 
### <a name="sample-request"></a>Пример запроса
 

```cpp
POST /users/batch/profile/settings
   {
      "userIds":[
         "2533274791381930"
       ],
      "settings":[
         "GameDisplayName",
         "GameDisplayPicRaw",
         "Gamerscore",
         "Gamertag"
      ]
   }
      
```

   
<a id="ID4EJD"></a>

 
## <a name="response-body"></a>Тело ответа
 
<a id="ID4EPD"></a>

 
### <a name="sample-response"></a>Пример ответа
 

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
                  "value":"https://images-eds.xboxlive.com/image?url=z951ykn43p4FqWbbFvR2Ec.8vbDhj8G2Xe7JngaTToBrrCmIEEXHC9UNrdJ6P7KIN0gxC2r1YECCd3mf2w1FDdmFCpSokJWa2z7xtVrlzOyVSc6pPRdWEXmYtpS2xE4F"
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

   
<a id="ID4EZD"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E2D"></a>

 
##### <a name="parent"></a>Parent 

[/Users/Batch/Profile/Settings](uri-usersbatchprofilesettings.md)

  
<a id="ID4EFE"></a>

 
##### <a name="reference"></a>Справочные материалы 

[Профиль (JSON)](../../json/json-profile.md)

   