---
title: GET (/media/{marketplaceId}/details)
assetID: 7c222fc7-d70a-84ac-5aaf-f22d186f7a43
permalink: en-us/docs/xboxlive/rest/uri-medialocaledetailsget.html
description: " GET (/media/{marketplaceId}/details)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 5c5be8f144f9c39076ba880223af08a30404c759
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57622719"
---
# <a name="get-mediamarketplaceiddetails"></a>GET (/media/{marketplaceId}/details)
Возвращает обеспечивают сведения и метаданные о один или несколько элементов.
Доменом для таких URI является `eds.xboxlive.com`.

  * ["Примечания"](#ID4EV)
  * [Параметры URI](#ID4ECB)
  * [Параметры строки запроса](#ID4ERB)
  * [Текст ответа](#ID4EYF)

<a id="ID4EV"></a>


## <a name="remarks"></a>Замечания

**SandboxId** теперь извлекается из утверждения в XToken и принудительно. Если **SandboxId** не существует, то службы обнаружения развлечения (EDS) будет выдавать ошибку 400 — Неверный запрос.

<a id="ID4ECB"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.|

<a id="ID4ERB"></a>


## <a name="query-string-parameters"></a>Параметры строки запроса

| Параметр| Тип| Описание|
| --- | --- | --- | --- | --- | --- |
| идентификаторы| string[]| Обязательный. Все идентификаторы (до 10), для которых возвращаются сведения. Обратите внимание, что любой идентификатор, который содержит символы, недопустимые для размещения в URL-адрес (тип ProviderContentId, обычно являются идентификаторы полных URL-адресов, сами и таким образом содержит недопустимые знаки) <b>необходимо</b> быть URL-адреса отправки должным образом на развлечения Обнаружение служб (EDS). Обратите внимание на то, что это может быть только одно значение в случае ProviderContentId тип идентификатора. Если требуется более одного ProviderContentId, несколько необходимо вызывать для EDS.|
| Тип_идентификатора| Строка| Необязательно. Тип идентификаторы, которые передаются в параметр «идентификаторы». Допустимые значения: <ul><li><b>Канонические</b> (Bing или Marketplace) </li><li><b>ZuneCatalog</b></li><li><b>ZuneMediaInstance</b> (например: 132 КБ WMA музыкальных файлов) </li><li><b>AMG</b></li><li><b>MediaNet</b> (pre-MusiWave) </li><li><b>XboxHexTitle</b> (воспроизведение на консоли приложение) </li></ul>|
| DesiredMediaItemTypes| Строка| <b>Требуется, если MediaGroup не передается. Не следует передавать оба.</b> Носитель элемента typs идентификаторов. Все входящие идентификаторы должны совместно использовать тот же тип. Если несколько типов являются необходимыми, передайте все возможные типы описанным выше Тип_идентификатора. По умолчанию задается значение «Неизвестно», если он отсутствует, она может не быть valied для всех типов идентификатор. |
| mediaGroup| Строка| <b>Требуется, если DesiredMediaItemTypes не передается. Не следует передавать оба.</b>|
| ConditionSets| Строка| <b>Необязательный</b>. Клиенты могут запрашивать <b>доступности</b> очистки на основе условия наборов, которые являются парами "ключ значение", заданные с помощью этой строки запроса. Они используются для сопоставления условий наборы доступности. Ниже приведен список ключей, которые могут использоваться для сопоставления наборов условий. <ul><li><b>Платформы</b>: Где строится и продукта может воспроизводиться.</li><li><b>Подписки</b>: Список поддерживаемых подписок для этого доступности (Gold или Silver).</li><li><b>EntitlementIds</b>: Отслеживаться после пользователи покупают игры.</li></ul> | 

<a id="ID4EYF"></a>


## <a name="response-body"></a>Тело ответа

<a id="ID4E5F"></a>


### <a name="sample-response"></a>Пример ответа

Приведенный ниже код JSON: в ответ на вызов `/media/en-us/details?ids=6c5402e4-3cd5-4b29-a9c4-bec7d2c7514a&mediaGroup=GameType`.


```cpp
{
    "Items": [{
        "MediaGroup": "GameType",
        "MediaItemType": "DGame",
        "ID": "fd16e2fb-eca4-4182-8f69-a98fdd6e57a1",
        "Name": "Vector based massively multiplayer Tanks game.",
        "ReducedName": "Tanks",
        "ReleaseDate": "2013-03-15T00: 00: 00Z",
        "TitleId": "69A60C76",
        "VuiDisplayName": "Tanks",
        "DeveloperName": "Microsoft Studios",
        "Images": [{
            "ID": "32ebd9fe-6a22-4111-954e-564ec69d802a",
            "ResizeUrl": "http: //images-eds.dnet.xboxlive.com/image?url=RIJNAEIo6.u.tudW9rXJ2lWDOsMikqfNiHE2Sp4qbgNbH6_drY8Ek2cyHXEnnUKPUXAH_m8a3Oe4_wpV7CkKA0Snc9puIYOGxsIfyyncTBv.MIluDZX6UqAPsJYHE5go_J_BBfxNWW6yrK4.K75aMQ--",
            "Purposes": ["Box_Art"],
            "Purpose": "Box_Art",
            "Height": 300,
            "Width": 219,
            "Order": 1
        },
        {
            "ID": "32ebd9fe-6a22-4111-954e-564ec69d802a",
            "ResizeUrl": "http: //images-eds.dnet.xboxlive.com/image?url=RIJNAEIo6.u.tudW9rXJ2lWDOsMikqfNiHE2Sp4qbgNbH6_drY8Ek2cyHXEnnUKPUXAH_m8a3Oe4_wpV7CkKA0Snc9puIYOGxsIfyyncTBv.MIluDZX6UqAPsJYHE5go_J_BBfxNWW6yrK4.K75aMQ--",
            "Purposes": ["Box_Art"],
            "Purpose": "Box_Art",
            "Height": 120,
            "Width": 85,
            "Order": 1
        },
        {
            "ID": "32ebd9fe-6a22-4111-954e-564ec69d802a",
            "ResizeUrl": "http: //images-eds.dnet.xboxlive.com/image?url=RIJNAEIo6.u.tudW9rXJ2lWDOsMikqfNiHE2Sp4qbgNbH6_drY8Ek2cyHXEnnUKPUXAH_m8a3Oe4_wpV7CkKA0Snc9puIYOGxsIfyyncTBv.MIluDZX6UqAPsJYHE5go_J_BBfxNWW6yrK4.K75aMQ--",
            "Purposes": ["Image"],
            "Purpose": "Image",
            "Height": 720,
            "Width": 1280,
            "Order": 1
        }],
        "PublisherName": "Microsoft Studios",
        "RatingId": "10",
        "ParentalRating": "E",
        "ParentalRatingSystem": "ESRB",
        "SortName": "\n            Vector based massively multiplayer Tanks game.\n          ",
        "Capabilities": [{
            "NonLocalizedName": "onlineMultiplayerMin",
            "Value": "3"
        },
        {
            "NonLocalizedName": "onelineMultiplayerMax",
            "Value": "5"
        }],
        "KValue": "4",
        "KValueNamespace": "bingbox",
        "AllTimePlayCount": 30.0,
        "SevenDaysPlayCount": 30.0,
        "ThirtyDaysPlayCount": 30.0,
        "AllTimeRatingCount": 12.0,
        "ThirtyDaysRatingCount": 12.0,
        "SevenDaysRatingCount": 12.0,
        "AllTimeAverageRating": 0.8,
        "ThirtyDaysAverageRating": 0.8,
        "SevenDaysAverageRating": 0.8,
        "LegacyIds": [{
            "IdType": "TitleId",
            "Value": "69A60C76"
        }],
        "Availabilities": [{
            "AvailabilityID": "",
            "ContentId": "7AE9DAB2-1162-488D-9F80-B804EC5AF879",
            "Devices": [{
                "Name": "Durango"
            }],
            "LicensePolicyTicket": "eyJhbGciOiJIUzI1NiIsImtpZCI6IlYxIn0.eyJhbGxvd1BlcnNpc3RlbnRSb290IjpmYWxzZSwiYmxvY2tlZENvdW50cmllcyI6bnVsbCwiZW50aXRsZWRDb3VudHJpZXMiOm51bGwsImVzY3Jvd2VkQ29udGVudEtleSI6IlJVdENNY3NCQUFBQkFBSUFBQUFGQUFJQUlBQUFBREpsWlRKaVpERTFORGhsT1RZMk5EZGhOVEEyWW1ZeVkyVmpZek5sTVRGa0F3QUNBQUFBQmdBRUFBRUFBQUF4QlFBQ0FBQUFBUUFIQUlBQkFBQzJxb0Vsbm83eWNaZ05iY2R0dFB0TXBlZW95VEVjVUorSmF1WlhmSU9xRFRiV09ud2x1anZEZzU1YzFJby94Q1llTzYxd0VkYTdyZmQ3bVZWMFdTVmNXQUZ2OHplK2toMG1VZVcyUkU3MDd6S05GYzllanVLWlhoY0dkQ1RBYmdVYzJQUVlFNkZOZDJtaWEzdDgyaXlPam9UeGcxWkNaaUR1LzFZaUJ1ZzE1R3BvWVZZREpIbkt5K3NuaEZMdEFKcEI0cER3VXhkVTcvZlV6Y0dvdEtyUW12VDJRQVk5d1NxM0d4WWJ3a1RnVnRrUHVzNTdsN1FVSmp6clBiYkpzeWk5UjhaKzN5RE1EM1JoN0NmekhtVGx3NnBaclFMbnhTTHM0bldPQS92eGo5WS9aN25EM2NJRWlScUhMUU9LSE9GOXVqL0pOOUxtT1FqVkNaaHpLekhRMk5sVURBWmFzYkZ4ZnZjOVh1bDZqZVNCamJsR2xCMlRpZUlTemdPSDh3V0lRWmFGZTMwcVd0a2d4dC9MOEhwTmRwWkwzbG1mcU0vcmswMEtFWUtLaGI4K0E1TGMzeEd2clg3TW9GcmdTUGFONE4xMUNSRVdFZkFGOGc5WWdROEt4R3B0SzdKc0ZpR0NDUmVOeXVYYTQ0NU9NMFNCSFFHVXVMTjl1R0RVTVNNPSIsImV4cGlyYXRpb24iOiIyMDEzLTA4LTEyVDIyOjE3OjI4Ljg2MTYzNzhaIiwibWlpZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC0wMDAwLTAwMDAwMDAwMDAwMCIsIm9paWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJwb2xpY3lDYXJkSWRzIjpbImdhbWVzOjhmY2UwMjkyLTgyZDUtNDFhNi1hN2IzLTM5NzNkOGM5MWZiNyJdLCJwcm9kdWN0SWQiOiIzMmViZDlmZS02YTIyLTQxMTEtOTU0ZS01NjRlYzY5ZDgwMmEiLCJzZSI6WyJHYW1lOzMyZWJkOWZlLTZhMjItNDExMS05NTRlLTU2NGVjNjlkODAyYSJdLCJ2ZXJzaW9uIjoiMiJ9.T0vaH03VDHr913_PRYVBaOXb3XcHX7AwfXNG2rxcVI8",
            "OfferDisplayData": "{
                \"acceptablePaymentInstrumentTypes\": [\"CreditCard\"],
                \"availabilityDescription\": \"AvailabilityDescription for bc545b24-a8e5-422d-bcca-ed1ed5c1dfbc\",
                \"currencyCode\": \"USD\",
                \"displayPrice\": \"$0.00\",
                \"displayListPrice\": \"$0.00\",
                \"distributionType\": \"Full\",
                \"isPurchasable\": true,
                \"listPrice\": 0.0,
                \"price\": 0.0
            }",
            "SignedOffer": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjIzMUZBNzkzQTRENUY3ODc4NUFGQ0FEMEExNTEwRUMwOEJDQURDNTMifQ.eyIkdHlwZSI6Ik9mZmVycy5Db250cmFjdHMuVjMuT2ZmZXIiLCJhY2NlcHRhYmxlUGF5bWVudEluc3RydW1lbnRUeXBlcyI6WyJDcmVkaXRDYXJkIl0sImF2YWlsYWJpbGl0eURlc2NyaXB0aW9uIjoiQXZhaWxhYmlsaXR5RGVzY3JpcHRpb24gZm9yIGJjNTQ1YjI0LWE4ZTUtNDIyZC1iY2NhLWVkMWVkNWMxZGZiYyIsImF2YWlsYWJpbGl0eUlkIjoiN2Q5YWJmOWUtOGQxYy00MWVjLWIxYjAtN2IyMDQ1MTU3MzM2IiwiY3JlYXRlZCI6IjIwMTMtMDQtMTRUMjI6MTc6MjguODYxNjM3OFoiLCJleHBpcmVzIjoiMjAxMy0wOC0xMlQyMjoxNzoyOC44NjE2Mzc4WiIsImlkIjoiZDIwMzljMDctMzBjZi00MzBiLTgxNTQtYjliN2JkNzFiYjY0IiwicHJvZHVjdERlc2NyaXB0aW9uIjp7IiR0eXBlIjoiT2ZmZXJzLkNvbnRyYWN0cy5WMy5Qcm9kdWN0RGVzY3JpcHRpb24iLCJkaXN0cmlidXRpb25UeXBlIjoiRnVsbCIsImdyYW50ZWRFbnRpdGxlbWVudHMiOlt7IiR0eXBlIjoiT2ZmZXJzLkNvbnRyYWN0cy5WMy5FbnRpdGxlbWVudERlc2NyaXB0aW9uIiwiYmlsbGluZ09mZmVySWQiOm51bGwsImNhdGFsb2dPZmZlcklkIjoiMzJlYmQ5ZmUtNmEyMi00MTExLTk1NGUtNTY0ZWM2OWQ4MDJhIiwiY2F0YWxvZ09mZmVySW5zdGFuY2VJZCI6IjMyZWJkOWZlLTZhMjItNDExMS05NTRlLTU2NGVjNjlkODAyYSIsImNvbnRhaW5lcnMiOm51bGwsImRpc3RyaWJ1dGlvblR5cGUiOiJGdWxsIiwiZHVyYXRpb24iOiIxMDY3NTE5OTowMjo0ODowNS40Nzc1ODA3IiwiZW50aXRsZW1lbnRJZCI6IkdhbWU7MzJlYmQ5ZmUtNmEyMi00MTExLTk1NGUtNTY0ZWM2OWQ4MDJhIiwiZW50aXRsZW1lbnRUeXBlIjoiR2FtZSIsInByb2R1Y3RJZCI6IjMyZWJkOWZlLTZhMjItNDExMS05NTRlLTU2NGVjNjlkODAyYSIsInF1YW50aXR5IjpudWxsLCJzYW5kYm94SWQiOiJQQVJULkRldjEiLCJ0aXRsZUlkIjoiMHhGRkZFMDdEMSIsInVvZGJPZmZlcklkIjpudWxsfV0sInByb2R1Y3RJZCI6IjMyZWJkOWZlLTZhMjItNDExMS05NTRlLTU2NGVjNjlkODAyYSIsInByb2R1Y3RUeXBlIjoiREdhbWUiLCJyZWR1Y2VkVGl0bGUiOiJUYW5rcyIsInN1YnNjcmlwdGlvblBvbGljeUlkIjpudWxsfSwicHJpY2UiOnsiJHR5cGUiOiJPZmZlcnMuQ29udHJhY3RzLlYzLlByaWNlIiwiY2FtcGFpZ25TZWdtZW50ZElkIjpudWxsLCJjdXJyZW5jeUNvZGUiOiJVU0QiLCJpc1RheEluY2x1ZGVkIjpmYWxzZSwicHJpY2VDYW1wYWlnbmRJZCI6bnVsbCwicHJpY2VDYXJkSWQiOiI4OTZjZTNhMy0wMDAwLTAwMDAtYWFhYS0wMDE1NWQyNDFjMTgiLCJwcmljZUNvZGUiOm51bGwsInJldGFpbEFtb3VudCI6MC4wLCJza3VzIjpbeyIkdHlwZSI6Ik9mZmVycy5Db250cmFjdHMuVjMuU2t1IiwiYWNjb3VudCI6Ilplcm9Eb2xsYXJTa3UiLCJhbW91bnQiOjAuMCwiYWNjb3VudFR5cGUiOiJSZXZlbnVlIn1dLCJ3aG9sZXNhbGVBbW91bnQiOjAuMH0sInh1aWQiOm51bGx9.LFX_eBwjspSl_n52ysv5avxcDymWFKH2UyJ517D9I6jbkCS9HyEGmqZxmbDPg88qQnRv1Uq0MEvA0v6jjWCKdxYmmjK4PubgmbTQYdgKfas6AjI2P4jQGQILduhgO9YZ6v8DEmihigfanjRwPTi5WhE_2x1mau51mrZBtDERstL9w-mBSkP3Kgu3FT09MNTbqCdDGH1iSpgvNxaXrs336CtEsTLeuy_yvyRvBR-hjxBTo2JH09v5RYQWA8sJ3zmuYxLVe_Cs55DoyCwMwBXbw1tAZE91uyXwQJUFVmZnVxVx9MLQ8EgePJ6BoUpUasp6Jax-mHiBRswsM1V0STGrxQ"
        }],
        "Packages": [{
            "CdnFileLocation": [{
                "SortOrder": null,
                "Uri": "https: //update.dnet.xboxlive.com/test_cdn/tanks-randomkey.xvc"
            },
            {
                "SortOrder": null,
                "Uri": "https: //update.dnet.xboxlive.com/test_cdn/tanks-randomkey.xvc"
            },
            {
                "SortOrder": null,
                "Uri": "https: //update.dnet.xboxlive.com/test_cdn/tanks-randomkey.xvc"
            }],
            "ContentId": "7AE9DAB2-1162-488D-9F80-B804EC5AF879",
            "PackageType": "XVC",
            "LicenseType": "Xbox Live Games Machine and User"
        }],
        "SandboxId": "PART.Dev1"
    }],
    "ImpressionGuid": "8e6bddc2-ded7-4921-b766-b3a887381caa"
}

```


<a id="ID4ENG"></a>


## <a name="see-also"></a>См. также

<a id="ID4EPG"></a>


##### <a name="parent"></a>Parent

[/media/{marketplaceId}/details](uri-medialocaledetails.md)


<a id="ID4EZG"></a>


##### <a name="further-information"></a>Дополнительные сведения

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)
