---
title: API REST портала устройств Xbox
description: Справочник по API для UWP на Xbox One.
ms.date: 10/25/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 5ae8e953-0465-487b-81dd-54a85c904daf
ms.localizationpriority: medium
ms.openlocfilehash: d8fdcf01d7d1f72624d73acf2d10ce28dfb75e04
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57656999"
---
# <a name="xbox-device-portal-rest-api"></a>API REST портала устройств Xbox

Этот раздел содержит справочные статьи об API REST портала устройств Xbox, который используется для удаленной настройки и администрирования консоли.

| URI        | Описание |
|------------|-------------|
|[/API/App/packagemanager/Register](wdp-loose-folder-register-api.md)| Регистрирует приложение, содержащееся в свободной папке. |
|[/API/App/packagemanager/Upload](wdp-folder-upload.md)| Передает всю папку на консоль. |
|[/Ext/App/sshpins](uwp-sshpins-api.md)| Удаленная очистка всех доверенных PIN-кодов SSH. Требует выполнения повторного связывания по PIN-коду для разработки UWP в Visual Studio. |
|[/Ext/App/deployinfo](uwp-deployinfo-api.md)| Запрашивает сведения о развертывании для одного или нескольких установленных пакетов. |
|[/ ext/fiddler](wdp-fiddler-api.md)| Включение и отключение трассировки сети Fiddler. |
|[/Ext/httpmonitor/Sessions](wdp-httpMonitor-api.md)| Получение трафика HTTP от приложения в фокусе на Xbox. |
|[/ ext/networkcredential](uwp-networkcredentials-api.md)| Добавление, удаление или обновление сетевых учетных данных. |
|[/ext/remoteinput](uwp-remoteinput-api.md)| Удаленная отправка данных ввода с клавиатуры, мыши или контроллера на Xbox. |
|[/Ext/remoteinput/Controllers](uwp-remoteinput-controllers-api.md)| Получите число подключенных физических контроллеров или отключите все физические контроллеры. |
|[Снимок экрана/ext /](wdp-media-capture-api.md)| Захватывает представление экрана, отображаемого в данный момент на консоли, в формате PNG. |
|[/ ext/параметры](wdp-xboxsettings-api.md)| Предоставляет доступ к параметрам разработчика Xbox One. |
|[/Ext/SMB/developerfolder](wdp-smb-api.md)| Предоставляет доступ к папке разработчика на консоли через проводник на компьютере, где ведется разработка. |
|[/ ext/пользователь](wdp-user-management.md)| Управляет пользователями на консоли Xbox One. |
|[/Ext/Xbox/Info](wdp-xboxinfo-api.md)| Предоставляет сведения об устройстве Xbox One. |
|[/Ext/xboxlive/sandbox](wdp-sandbox-api.md)| Управляет песочницей Xbox Live. |

## <a name="see-also"></a>См. также

- [Приложения UWP для Xbox One](index.md)
- [Windows Device Portal](../debug-test-perf/device-portal.md)