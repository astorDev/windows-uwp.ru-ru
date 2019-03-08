---
title: Заметки о выпуске многопользовательские интегрированных Xbox
description: Заметки о выпуске о встроенной многопользовательскую Xbox.
ms.assetid: 38df7a49-71b9-4d86-9c49-683ffa7308d6
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, встроенная многопользовательскую xbox
ms.localizationpriority: medium
ms.openlocfilehash: 8d7bef69d9a14455d10af3745533c26e5fb54332
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57658119"
---
# <a name="xbox-integrated-multiplayer-release-notes"></a>Заметки о выпуске многопользовательские интегрированных Xbox

Обновленный с 14 декабря 2016 года

В этом выпуске для Xbox интегрированной многопользовательскую (XIM) не доступны следующие методы:

-   `xim_authority::set_authority_reconciled_data()`
-   `xim_authority::set_authority_reconciliation_data()`
-   `xim_authority::send_data_to_players()`
-   `xim_authority::network_path_information()`
-   `xim_player::xim_local::send_data_to_authority()`

В этом выпуске XIM не предоставляются изменениях следующих состояний:

-   `xim_state_change_type::player_to_authority_data_received`
-   `xim_state_change_type::authority_to_player_data_received`
-   `xim_state_change_type::authority_reconciling`
-   `xim_state_change_type::authority_reconciled_local`
-   `xim_state_change_type::authority_reconciled_remote`
-   `xim_state_change_type::send_queue_alert_triggered`
