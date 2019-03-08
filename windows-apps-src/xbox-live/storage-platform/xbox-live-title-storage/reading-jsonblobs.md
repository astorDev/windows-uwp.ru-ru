---
title: Считывает большой двоичный объект JSON
description: Узнайте, как считать большой двоичный объект JSON в хранилище-Title Xbox Live.
ms.assetid: 3697af16-d054-4835-af7f-7fee8c628345
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 30d2b9f6539e73df1c5ea5ae18b3d1a6ca061d60
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57613489"
---
# <a name="reading-a-json-blob-in-xbox-live-title-storage"></a>Считывает большой двоичный объект JSON в хранилище-Title Xbox Live

1.  Отправка запроса с использованием *получить* метод для чтения данных из хранилища title. В этом примере использует хранилище глобального title.

        GET https://titlestorage.xboxlive.com/global/scids/{scid}/data/surprise.json,json
        Content-Type: application/octet-stream
        x-xbl-contract-version: 1
        Authorization: XBL3.0 x=<userHash>;<STSTokenString>
        Connection: Keep-Alive

-   Пользователь должен быть в сеансе, чтобы обновить его.

-   STSTokenString — это заполнитель для краткости и необходимо заменить маркер, возвращенный запрос на проверку подлинности.

#### <a name="reference"></a>Справочные материалы

**/global/scids/{scid}/data/{pathAndFileName},{type}**
