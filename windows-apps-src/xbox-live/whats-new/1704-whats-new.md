---
title: Новые возможности для API-интерфейсы Xbox Live — апрель 2017 г.
description: Новые возможности для API-интерфейсы Xbox Live — апрель 2017 г.
ms.assetid: ''
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, решают, соревнования
ms.localizationpriority: medium
ms.openlocfilehash: a9256bfce05c14a0bfa63c7ec656dd899648d844
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57651309"
---
# <a name="whats-new-for-the-xbox-live-apis---april-2017"></a>Новые возможности для API-интерфейсы Xbox Live — апрель 2017 г.

См. в разделе [новинки - марта 2017 г.](1703-whats-new.md) статьи о добавленных в за март 2017 года.

## <a name="xbox-services-apis"></a>API-интерфейсов служб Xbox

### <a name="visual-studio-2017"></a>Visual Studio 2017

API-интерфейсы Xbox Live были обновлены для поддержки Visual Studio 2017 для универсальной платформы Windows (UWP) и Xbox One названия.

### <a name="tournaments"></a>Соревнования

Были добавлены новые интерфейсы API для поддержки соревнования. Теперь вы можете использовать `xbox::services::tournaments::tournament_service` класс для доступа к службе соревнования из заголовка.

Эти новые турнира API-интерфейсы реализации следующих сценариев:

* Запросы к службе, чтобы найти все существующие соревнования для текущего заголовка.
* Получите сведения о турнира из службы.
* Запросы к службе для получения списка групп для турнира.
* Получите сведения о группах для турнира из службы.
* Отслеживание изменений соревнования и группам с помощью подписки в режиме реального времени действия возврата.
