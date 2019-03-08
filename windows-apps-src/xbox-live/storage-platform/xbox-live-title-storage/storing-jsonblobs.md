---
title: Сохраняющая большой двоичный объект JSON
description: Узнайте, как сохранение большого двоичного объекта JSON в хранилище-Title Xbox Live.
ms.assetid: f424aca1-e671-4e31-84c6-098fda4a9558
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, название хранилища
ms.localizationpriority: medium
ms.openlocfilehash: dabcd0634bb20bc6b82ae302c77338b5bb726a63
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57595069"
---
# <a name="storing-a-json-blob-in-xbox-live-title-storage"></a>Сохраняющая большой двоичный объект JSON в хранилище-Title Xbox Live

1.  Отправка запроса с использованием *ПОМЕСТИТЬ* метод для отправки данных в хранилище title.

        PUT https://titlestorage.xboxlive.com/json/users/xuid(1245111)/scids/{scid}/data/{pathAndFileName},json
        Content-Type: application/octet-stream
        x-xbl-contract-version: 1
        Authorization: XBL3.0 x=<userHash>;<STSTokenString>
        Content-Length: 240
        Connection: Keep-Alive



-   Пользователь должен быть в сеансе, чтобы обновить его.

-   STSTokenString — это заполнитель для краткости и необходимо заменить маркер, возвращенный запрос на проверку подлинности.

2.  Отправьте объект JSON.

        {
            "startlevel":"1",
            "expression":"smile"
        }

#### <a name="reference"></a>Справочные материалы

**/json/users/xuid({xuid})/scids/{scid}/data/{pathAndFileName},json)**
