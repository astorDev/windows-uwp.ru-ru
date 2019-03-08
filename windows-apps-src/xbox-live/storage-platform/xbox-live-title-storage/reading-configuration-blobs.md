---
title: Считывает большой двоичный объект конфигурации
description: Узнайте, как считать большой двоичный объект конфигурации в хранилище-Title Xbox Live.
ms.assetid: ee62d221-69b9-4f52-9b5d-5a44d04de548
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 71607f05f07ee4a98f73a19c28c85dc325041a10
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57599399"
---
# <a name="reading-a-configuration-blob-in-xbox-live-title-storage"></a>Чтение BLOB-объект конфигурации в службе Xbox Live Title хранилища

*Большие двоичные объекты конфигурации* — это файлы в Xbox Live название хранилища, которые содержат данные игры. Данные являются объектами JSON, включающие виртуальные узлы, которые могут быть отфильтрованы перед отправкой в игры. Дополнительные сведения о больших двоичных объектов конфигурации, см. в разделе **идентификаторы URI хранилища Title**.

### <a name="to-read-a-configuration-blob"></a>Чтение большого двоичного объекта конфигурации

1.  Отправка запроса с использованием метода для чтения данных из хранилища title.

        GET https://titlestorage.xboxlive.com/global/scids/{scid}/data/config.json,config              
        Content-Type: application/octet-stream
        x-xbl-contract-version: 1
        Authorization: XBL3.0 x=<userHash>;<STSTokenString>
        Connection: Keep-Alive


-   Пользователь должен быть в сеансе, чтобы обновить его.
-   STSTokenString — это заполнитель для краткости и необходимо заменить маркер, возвращенный запрос на проверку подлинности.

#### <a name="reference"></a>Справочные материалы

**/global/scids/{scid}/data/{pathAndFileName},{type}**
**GET (/global/scids/{scid}/data/{pathAndFileName},{type})**
