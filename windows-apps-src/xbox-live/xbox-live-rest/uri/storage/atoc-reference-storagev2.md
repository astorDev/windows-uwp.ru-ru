---
title: Коды URI хранилища игр
assetID: 32bba1e4-0980-785e-c098-a96cd88a8e5f
permalink: en-us/docs/xboxlive/rest/atoc-reference-storagev2.html
description: " Коды URI хранилища игр"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: e0296eff0937ea5075630db0e049c86e2ea2c8ce
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57622679"
---
# <a name="title-storage-uris"></a>Коды URI хранилища игр
 
Здесь содержатся сведения об адресах универсальный идентификатор ресурса (URI) и связанных методов протокола передачи гипертекста (HTTP) от службы Xbox Live для *название хранилища*.
 
Эту службу можно использовать для игровых программ для всех платформ.
 
Доменом для таких URI является titlestorage.xboxlive.com.
 
<a id="ID4EFB"></a>

 
## <a name="in-this-section"></a>В этом разделе

[/ global/scids / {scid}](uri-globalscidsscid.md)

&nbsp;&nbsp;Извлекает сведения о квотах для этого типа хранения.

[/global/scids/{scid}/data/{path}](uri-globalscidssciddatapath.md)

&nbsp;&nbsp;Перечисляет сведения о файле по указанному пути. 

[/global/scids/{scid}/data/{pathAndFileName},{type}](uri-globalscidssciddatapathandfilenametype.md)

&nbsp;&nbsp;Загружает файл.

[/json/users/batch/scids/{scid}/data/{pathAndFileName},json](uri-jsonusersbatchscidssciddatapathandfilenametype.md)

&nbsp;&nbsp;Скачивает несколько файлов из нескольких пользователей с тем же именем файла.

[/JSON/Users/xuid({xuid}) /scids/ {scid}](uri-jsonusersxuidscidsscid.md)

&nbsp;&nbsp;Извлекает сведения о квотах для этого типа хранения.

[/data/ /JSON/Users/xuid({xuid}) /scids/ {scid} {path}](uri-jsonusersxuidscidssciddatapath.md)

&nbsp;&nbsp;Перечисляет сведения о файле по указанному пути. 

[/data/ /JSON/Users/xuid({xuid}) /scids/ {scid} {pathAndFileName}, json](uri-jsonusersxuidscidssciddatapathandfilenametype.md)

&nbsp;&nbsp;Загружает и выгружает удаляет файл.

[/sessions/{sessionId}/scids/{scid}](uri-sessionssessionidscidsscid.md)

&nbsp;&nbsp;Извлекает сведения о квотах для этого типа хранения.

[/sessions/{sessionId}/scids/{scid}/data/{path}](uri-sessionssessionidscidssciddatapath.md)

&nbsp;&nbsp;Перечисляет сведения о файле по указанному пути. 

[/sessions/{sessionId}/scids/{scid}/data/{pathAndFileName},{type}](uri-sessionssessionidscidssciddatapathandfilenametype.md)

&nbsp;&nbsp;Загружает файл.

[/trustedplatform/users/batch/scids/{scid}/data/{pathAndFileName},{type}](uri-trustedplatformusersbatchscidssciddatapathandfilenametype.md)

&nbsp;&nbsp;Скачивает несколько файлов из нескольких пользователей с тем же именем файла.

[/trustedplatform/Users/xuid({xuid}) /scids/ {scid}](uri-trustedplatformusersxuidscidsscid.md)

&nbsp;&nbsp;Извлекает сведения о квотах для этого типа хранения.

[/data/ /trustedplatform/Users/xuid({xuid}) /scids/ {scid} {path}](uri-trustedplatformusersxuidscidssciddatapath.md)

&nbsp;&nbsp;Перечисляет сведения о файле по указанному пути. 

[/data/ /trustedplatform/Users/xuid({xuid}) /scids/ {scid} {pathAndFileName}, {type}](uri-trustedplatformusersxuidscidssciddatapathandfilenametype.md)

&nbsp;&nbsp;Загружает и выгружает удаляет файл.

[/untrustedplatform/users/batch/scids/{scid}/data/{pathAndFileName},{type}](uri-untrustedplatformusersbatchscidssciddatapathandfilenametype.md)

&nbsp;&nbsp;Скачивает несколько файлов из нескольких пользователей с тем же именем файла.

[/untrustedplatform/Users/xuid({xuid}) /scids/ {scid}](uri-untrustedplatformusersxuidscidsscid.md)

&nbsp;&nbsp;Извлекает сведения о квотах для этого типа хранения.

[/data/ /untrustedplatform/Users/xuid({xuid}) /scids/ {scid} {path}](uri-untrustedplatformusersxuidscidssciddatapath.md)

&nbsp;&nbsp;Перечисляет сведения о файле по указанному пути. 

[/untrustedplatform/users/xuid({xuid})/scids/{scid}/data/{pathAndFileName},{type}](uri-untrustedplatformusersxuidscidssciddatapathandfilenametype.md)

&nbsp;&nbsp;Загружает и выгружает удаляет файл.
 
<a id="ID4E5C"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EAD"></a>

 
##### <a name="parent"></a>Parent 

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)

   