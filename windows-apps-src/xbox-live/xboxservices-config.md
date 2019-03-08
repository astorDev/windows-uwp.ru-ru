---
title: XboxServices.config
description: Описывает XboxServices.config-файл для связывания игре для универсальной платформы Windows с конфигурацией Xbox Live.
ms.date: 03/29/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, конфигурации службы, xboxservices.config
ms.localizationpriority: medium
ms.openlocfilehash: 8ff538d691627bf4bb12b3ef6f8b1360e59ac701
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57606689"
---
# <a name="xboxservicesconfig-file-description"></a>Описание файла XboxServices.config

При разработке Xbox Live включена игрой UWP, проект должен содержать файл XboxServices.config.  Этот файл позволяет Xbox Live SDK должен быть сопоставлен игры, приложения центра партнеров и конфигурацию службы Xbox Live. Этот файл содержит объект JSON, сведения, такие как идентификатор конфигурации службы, код и т. д.

При использовании Unity для разработки игр Xbox Live Creators Program с помощью Xbox Live подключаемого модуля, этот файл автоматически создается автоматически с помощью мастера взаимосвязей Xbox Live.

## <a name="xboxservicesconfig-fields"></a>XboxServices.config fields

>[!NOTE]
> Файл, созданный мастером Xbox Live ассоциации могут включать дополнительные поля, не входящими в описанных ниже, но они не используются службой.

В объекте JSON в файле конфигурации определены следующие поля:

Поле | Описание
--- | ---
PrimaryServiceConfigId  |  Xbox Live конфигурации службы идентификатор (SCID). В [центра партнеров](https://partner.microsoft.com/dashboard), это значение можно найти на **Xbox Live** страница (для программы создателей) или **Xbox Live установки** странице (для полной Xbox Live игры), в разделе **Служб** раздел для вашего приложения.
titleId  |  Decimal идентификатор заголовка для приложения. В [центра партнеров](https://partner.microsoft.com/dashboard), это значение можно найти на **Xbox Live** страница (для программы создателей) или **Xbox Live установки** странице (для полной Xbox Live игры), в разделе **Служб** раздел для вашего приложения.
XboxLiveCreatorsTitle  |  Если «true», указывает, что приложение является приложением Xbox Live Creators Program. В противном случае — значение «false».
Область применения  |  **(Необязательно)**  Определяет область действия функций, используемых в приложении. См. ниже дополнительного описания.

### <a name="scope-field"></a>Поле «область»

**Область** поле является полем необязательно, можно использовать для указания функциональные возможности, используемые для игры.


Если **область** поле не задано, то область присваивается значение по умолчанию, который зависит от значения **XboxLiveCreatorsTitle** поле, как описано в следующей таблице:

Значение XboxLiveCreatorsTitle | Область по умолчанию
--- | ---
"true"  |  «xbl.signin xbl.friends»
"false"  |  «xboxlive.signin»



Ниже перечислены допустимые значения для **область** поля.

Значение области действия | Описание
--- | ---
xbl.signin  | Включает знак функциональных возможностей для игр Creators Program. Требуется для игр Creators Program.
xbl.Friends | Включает в себя друзей и социальных сетей списки лидеров функциональные возможности для игр Creators Program.
xboxlive.signin | Включает знак функциональных возможностей для игр, которые обращаются к полному набору функций Xbox Live. Требуется для игр Creators Program.

В настоящее время единственной причиной для указания **область** поля — Если вы вносите Xbox Live Creators Program игры и игры не требуется доступ к списки друзей или социальных сетей списки лидеров (списки лидеров, которые ограничиваются друзьями). Если это так, можно добавить следующую строку в файл XboxServices.config:

```
  "Scope" : "xbl.signin"
```

При добавлении этой строки запрещает приложению UWP запрашивает разрешение на доступ к дружественной списки при запуске приложения в первый раз.

## <a name="example-xboxservicesconfig-file"></a>Пример файла XboxServices.config

```
{
  "PrimaryServiceConfigId": "00000000-0000-0000-0000-000064382e34",
  "TitleId": 9039138423,
  "XboxLiveCreatorsTitle": true,
  "Scope" : "xbl.signin xbl.friends"
}
```
