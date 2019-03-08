---
title: Чтение большого двоичного
description: Дополнительные сведения о чтении двоичные BLOB-объектов в хранилище-Title Xbox Live.
ms.assetid: 9b8e0c35-0cea-4491-bf30-22fad224f11b
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, название хранилища
ms.localizationpriority: medium
ms.openlocfilehash: e2a0be72142a3e11c7c680cfc998287f396c2afc
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57641659"
---
# <a name="reading-a-binary-blob-in-xbox-live-title-storage"></a>Чтение двоичных больших двоичных объектов в хранилище-Title Xbox Live

1.  Отправка запроса с использованием *получить* метод для чтения данных из хранилища title. В этом примере использует хранилище глобального title.

        GET https://titlestorage.xboxlive.com/global/scids/{scid}/data/userinfo.bin,binary
        Content-Type: application/octet-stream
        x-xbl-contract-version: 1
        Authorization: XBL3.0 x=<userHash>;<STSTokenString>
        Connection: Keep-Alive



-   Пользователь должен быть в сеансе, чтобы обновить его.

-   STSTokenString — это заполнитель для краткости и необходимо заменить маркер, возвращенный запрос на проверку подлинности.

#### <a name="reference"></a>Справочные материалы

**/global/scids/{scid}/data/{pathAndFileName},{type}**
