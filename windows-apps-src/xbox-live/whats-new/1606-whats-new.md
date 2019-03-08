---
title: Новые возможности для Xbox Live SDK — июнь 2016 г.
description: Новые возможности для Xbox Live SDK — июнь 2016 г.
ms.assetid: 306e7fa8-14f0-437f-a991-6693f5c0d6a8
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 1d984d054d9e5fd7f9d34b42c1a224d53632e719
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57595289"
---
# <a name="whats-new-for-the-xbox-live-sdk---june-2016"></a>Новые возможности для Xbox Live SDK — июнь 2016 г.

См. в разделе [новые возможности — апрель 2016 г.](1604-whats-new.md) статьи о добавленных в апреле 2016 г.

> **Примечание.** Если вы используете комплект разработчиков Xbox (XDK), то *новые возможности — апрель 2016 г.* статье рассматриваются дополнительные новые функции, который был добавлен, так как выпуск XDK марта.

## <a name="os-and-tool-support"></a>Поддержка операционной системы и средства
Xbox Live SDK поддерживает Windows 10 RTM [версия 10.0.10240] и Visual Studio 2015 RTM [версия 14.0.23107.0].

## <a name="contextual-search"></a>Контекстный поиск
Были добавлены следующие новые классы `contextual_search` API для получения клипов игр:

* `contextual_search_game_clip`
* `contextual_search_game_clip_stat`
* `contextual_search_game_clip_thumbnail`
* `contextual_search_game_clip_uri_info`
* `contextual_search_game_clips_result`

Дополнительные сведения см.

## <a name="networking"></a>Сеть
Xbox Live SDK теперь включает новый утверждения, обнаруживает, что если 5 или более websockets создаются отдельно для каждого пользователя в экземпляре одно наименование.

Эту функцию можно отключить путем вызова assert `disable_asserts_for_maximum_number_of_websockets_activated()`.

> **Примечание.** Социальные сети и менеджера многопользовательскую использовать один объединенный websocket, при использовании этих функций в заголовок вашей.

## <a name="tools"></a>Инструменты
* Xbox Live устойчивости подключаемый модуль для Fiddler теперь входит в пакет SDK для Xbox Live.  
Он представляет собой дополнение к Fiddler, позволяющая разработчикам для выборочного блокирования вызовов к Xbox Live.
Используя этот подключаемый модуль, разработчики могут тестировать отказоустойчивость через частичные перерывам в заголовках игр.
Средство содержит ряд Xbox Live службы конечные точки встроенных и различные типы ошибок для проверки.
Поддерживаются все Xbox One и UWP заголовки.
