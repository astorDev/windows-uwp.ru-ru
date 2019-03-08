---
title: Хранить двоичные BLOB-объектов
description: Узнайте, как хранить двоичные BLOB-объектов в службе Xbox Live название хранилища.
ms.assetid: a0da36ef-5a5a-466d-80a8-e055ba7e4cdc
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, название хранилища
ms.localizationpriority: medium
ms.openlocfilehash: 70e620f2e992dfdd240f71b5c73165f13d4ef5cb
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57660099"
---
# <a name="storing-a-binary-blob-in-xbox-live-title-storage"></a>Хранение двоичный blob в хранилище-Title Xbox Live

1.  Отправка запроса с использованием метода для отправки данных в хранилище title.

        PUT https://titlestorage.xboxlive.com/trustedplatform/users/xuid(1245111)/scids/{scid}/data/lastturn.bin,binary              
        Content-Type: application/octet-stream
        x-xbl-contract-version: 1
        Authorization: XBL3.0 x=<userHash>;<STSTokenString>
        Content-Length: 40
        Connection: Keep-Alive


-   Пользователь должен быть в сеансе, чтобы обновить его.

-   STSTokenString — это заполнитель для краткости и необходимо заменить маркер, возвращенный запрос на проверку подлинности.

2.  Отправьте двоичные данные. Так как данные будут передаваться по протоколу HTTP, данные должны быть ограничены набор допустимых символов. Сведения, такие как изображения или звуковые данные должны быть закодированы. Можно выбрать любой метод шифрования, который приводит к возникновению ошибки совместимости символов HTTP.
d
```
  01EAEFBAD05903A4
  1EA2311656677DFF
  CF00
```

#### <a name="reference"></a>Справочные материалы

**/data/ /trustedplatform/Users/xuid({xuid}) /scids/ {scid} {pathAndFileName}, {type}**
