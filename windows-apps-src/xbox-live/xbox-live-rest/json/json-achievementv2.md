---
title: Achievement (JSON)
assetID: d3b52f66-ddc7-e676-b419-82209caf71d6
permalink: en-us/docs/xboxlive/rest/json-achievementv2.html
description: " Achievement (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: b0e20f46a0d97cba496df5c6fb9cda14fbeccccd
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57628479"
---
# <a name="achievement-json"></a>Achievement (JSON)
Объект награду (версия 2).
<a id="ID4EN"></a>


## <a name="achievement"></a>Достижение

Следующая спецификация имеет объект награду. Все члены являются обязательными.

| Участник| Тип| Описание|
| --- | --- | --- |
| id| Строка| Идентификатор ресурса.|
| serviceConfigId| Строка| SCID для этого ресурса. Идентифицирует title(s), достижение связана с. |
| name| Строка| Локализованное имя награду.|
| titleAssociations| Массив [TitleAssociation](json-titleassociation.md)| Массив TitleAssociation.|
| progressState| **ProgressState** перечисления| Состояние прогрессии: <ul><li>Недопустимое значение (0): Ход достижения находится в неизвестном состоянии.</li><li>можно добиться (1): Достижение разблокирован.</li><li>inProgress (2): Достижение заблокирован, но пользователь внес ход работ по его закрытия.</li><li>notStarted (3): Достижение заблокирована, и пользователь не задал любой ход ее разблокирование.</li></ul> | 
| Продвижения| [Продвижения](json-progression.md)| Продвижения пользователя в достижение.|
| mediaAssets| Массив [MediaAsset](json-mediaasset.md)| Ресурсы мультимедиа, связанный с достижения, такие как идентификаторы образа. |
| Платформы| Строка| Получено платформы достижение на не было.|
| isSecret| Логическое значение| Ли достижение секрета.|
| Описание| Строка| Описание достижения при разблокировке.|
| lockedDescription| Строка| Описание достижения, прежде чем он разблокируется.|
| productId| Строка| ProductId достижения был выпущен вместе с.|
| achievementType| **AchievementType** перечисления| Тип награду (не совпадает предыдущий тип на достижения прежних версий): <ul><li>Недопустимое значение (0): Тип награду неизвестные и не поддерживается.</li><li>постоянные (1): Достижений и можно разблокировать в любое время без даты окончания.</li><li>Задача (2): Достижений с за определенный временной интервал, в течение которого может быть разблокирован.</li></ul> |
| participationType| **ParticipationType** перечисления| Тип участия для достижения. Допустимые значения: Сотрудник или группа.|
| TimeWindow| TimeWindow| Интервал времени, во время которого достижения может быть разблокирован. Поддерживается только для проблем.|
| вознаграждения| Массив [Reward](json-reward.md)| Коллекция вознаграждения, полученных при разблокировке.|
| estimatedTime| TimeSpan| Предполагаемое время достижения перейдете заработать.|
| Прямая ссылка| Строка| Прямой ссылки в заголовке.|
| isRevoked| Логическое значение| Ли достижение отменяется путем принудительного применения.|

<a id="ID4EIAAC"></a>


## <a name="sample-json-syntax"></a>Образец синтаксиса JSON


```json
{
        "id":"3",
        "serviceConfigId":"b5dd9daf-0000-0000-0000-000000000000",
        "name":"Default NameString for Microsoft Achievements Sample Achievement 3",
        "titleAssociations":
        [{
                "name":"Microsoft Achievements Sample",
                "id":3051199919,
                "version":"abc"
        }],
        "progressState":"Achieved",
        "progression":
        {
          "requirements":
          [{
            "id":"12345678-1234-1234-1234-123456789012",
            "current":null,
            "target":"100"
          }],
          "timeUnlocked":"2013-01-17T03:19:00.3087016Z",
        },
        "mediaAssets":
        [{
                "name":"Icon Name",
                "type":"Icon",
                "url":"https://www.xbox.com"
        }],
        "platform":"D",
        "isSecret":true,
        "description":"Default DescriptionString for Microsoft Achievements Sample Achievement 3",
        "lockedDescription":"Default UnachievedString for Microsoft Achievements Sample Achievement 3",
        "productId":"12345678-1234-1234-1234-123456789012",
        "achievementType":"Challenge",
        "participationType":"Individual",
        "timeWindow":
        {
                "startDate":"2013-02-01T00:00:00Z",
                "endDate":"2100-07-01T00:00:00Z"
        },
        "rewards":
        [{
                "name":null,
                "description":null,
                "value":"10",
                "type":"Gamerscore",
                "valueType":"Int"
        },
        {
                "name":"Default Name for InAppReward for Microsoft Achievements Sample Achievement 3",
                "description":"Default Description for InAppReward for Microsoft Achievements Sample Achievement 3",
                "value":"1",
                "type":"InApp",
                "valueType":"String"
        }],
        "estimatedTime":"06:12:14",
        "deeplink":"aWFtYWRlZXBsaW5r",
        "isRevoked":false
    }

```


<a id="ID4ERAAC"></a>


## <a name="see-also"></a>См. также

<a id="ID4ETAAC"></a>


##### <a name="parent"></a>Parent

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)
