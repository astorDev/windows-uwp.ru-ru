---
title: Новые возможности для Xbox Live SDK — март 2017 г.
description: Новые возможности для Xbox Live SDK — март 2017 г.
ms.assetid: 03180585-6f87-4929-acfc-750bd78988a0
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: be8127e01d8eaae96a1d71f71967a653c00b0280
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57595349"
---
# <a name="whats-new-for-the-xbox-live-sdk---march-2017"></a>Новые возможности для Xbox Live SDK — март 2017 г.

См. в разделе [новинки - декабрь 2016 г.](1612-whats-new.md) статьи о добавленных в от декабря 2016 г.

## <a name="xbox-services-api"></a>API-Интерфейс служб Xbox

### <a name="data-platform-2017"></a>Платформа данных 2017 г.

Мы представили упрощенный API Stats.  Традиционно было необходимо отправлять события, соответствующие stat правила, определенные на XDP или центра партнеров и их обновит stat значения в облаке.  Мы будем называть Stats 2013 к этой модели.

С Stats 2017 название теперь находится в элемент управления stat значений.  Можно просто вызвать API с помощью самое последнее значение stat и отправляется службе напрямую без необходимости для событий.  При этом используется новый `StatsManager` API и можно считать см. в [Stats проигрывателя](../leaderboards-and-stats-2017/player-stats.md)

### <a name="github"></a>GitHub

Xbox Live API (XSAPI) теперь доступна на сайте GitHub в [ https://github.com/Microsoft/xbox-live-api ](https://github.com/Microsoft/xbox-live-api).  Двоичные файлы, полученные с XDK или в качестве NuGet пакеты по-прежнему рекомендуется использовать, однако можно также использовать источник, и мы рады вкладам исходного кода.  

## <a name="xbox-live-creators-program"></a>Программа Xbox Live Creators Program

Xbox Live Creators Program — это программа разработчиков, предлагая подмножество функций, Xbox Live для широкой аудитории разработчиков.  Если вы уже находитесь в ID@Xbox программы, это не окажет какого-либо влияния на вас.

Дополнительные сведения о программе в [Обзор программы разработчика](../developer-program-overview.md).

## <a name="documentation"></a>Документация

Существуют следующие новые статьи

| Статья | Описание |
|---------|-------------|
|[Конфигурации службы в реальном времени Xbox](../xbox-live-service-configuration.md) | Обновленные сведения о выполнении конфигурацию службы для заголовка Xbox Live
| [Настройка Xbox Live в Unity](../get-started-with-creators/configure-xbox-live-in-unity.md) | Новые сведения по установке Unity для разработчиков Xbox Live Creators Program |
| [Xbox Live "песочницы"](../xbox-live-sandboxes.md) | Упрощенное руководство по "песочницы" Xbox Live и изоляция содержимого |
| [Xbox Live тестовых учетных записей](../xbox-live-test-accounts.md) | Сведения о том, как протестировать рабочих учетных записей и об их создании в центре партнеров |
