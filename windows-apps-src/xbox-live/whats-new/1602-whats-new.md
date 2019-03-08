---
title: Новые возможности для Xbox Live SDK — февраль 2016 г.
description: Новые возможности для Xbox Live SDK — февраль 2016 г.
ms.assetid: 7ff34ea4-f07d-4584-98e4-13977994ccca
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: c13b2893397a0d84e8919146435ece338bc1afde
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57594739"
---
# <a name="whats-new-for-the-xbox-live-sdk---february-2016"></a>Новые возможности для Xbox Live SDK — февраль 2016 г.

См. в разделе [новые возможности — октябрь 2015 г.](1510-whats-new.md) статьи о добавленных в версии 1510

## <a name="os-and-tool-support"></a>Поддержка операционной системы и средства
Xbox Live SDK поддерживает Windows 10 RTM [версия 10.0.10240] и Visual Studio 2015 RTM [версия 14.0.23107.0].

## <a name="throttling"></a>Регулирование
- Точное регулирования будет скоро реализована для большинства служб Xbox Live.  API службы Xbox (XSAPI) будет автоматически обрабатывать повторные попытки и информирования о вызовы, которые подавлены во время разработки.  Дополнительные сведения можно найти в [лучшие методики вызова Xbox Live](../using-xbox-live/best-practices/best-practices-for-calling-xbox-live.md) статьи в документации.

## <a name="leaderboards"></a>Списки лидеров
- Списки лидеров по нескольким столбцам можно получить доступ к GetLeaderboard API-интерфейсом. Если указать вектор имена дополнительных столбцов, вектор столбцов на результат будет заполняться Если этих столбцов.

## <a name="documentation"></a>Документация
- [Application Insights](https://developer.microsoft.com/en-us/games/xbox/docs/xboxlive/xbox-live-partners/event-driven-data-platform/application-insights) документацию можно здесь.  Чтобы просмотреть события платформы данных почти в реальном времени можно использовать Application Insights с помощью бесплатной учетной записи Azure.  Эта функция в данный момент доступна только для приложений универсальной платформы Windows на Windows 10 на рабочем столе.
- Обновленная документация на Xbox популярного событий средства для разработчиков универсальной платформы Windows, разговор о способах создания оболочек для отправки событий платформы данных.  Обратите внимание на то, что это необязательно, и можно продолжать использовать WriteInGameEvent API в том случае, если вы предпочитаете.
- С помощью Fiddler для отладки событий платформы данных и убедитесь, что они неправильно отправляются.  Это только для событий универсальной платформы Windows.
- Сведения о том, как собирать журналы трассировки анализатор Live можно найти.  См. в разделе [анализ вызовов к Xbox Live Services](../tools/analyze-service-calls.md) статьи.
