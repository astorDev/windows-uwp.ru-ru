---
title: Новые возможности для Xbox Live SDK — сентябрь 2015 г.
description: Новые возможности для Xbox Live SDK — сентябрь 2015 г.
ms.assetid: 84b82fde-f6f3-4dc2-b2df-c7c7313a2cc3
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: c618a738dc2670d3d3de1fa2f4c4108c24130eb0
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57602279"
---
# <a name="whats-new-for-the-xbox-live-sdk---september-2015"></a>Новые возможности для Xbox Live SDK — сентябрь 2015 г.

См. в разделе [новые возможности — август 2015 г.](1508-whats-new.md) статьи о добавленных в августе 2015 г.

Сентябрьском выпуске Xbox Live SDK включает следующие обновления:

## <a name="os-and-tool-support"></a>Поддержка операционной системы и средства ##
Xbox Live SDK поддерживает Windows 10 RTM [версия 10.0.10240] и Visual Studio 2015 RTM [версия 14.0.23107.0].

## <a name="contextual-search-apis"></a>API-интерфейсы контекстного поиска
* Включите заголовок или приложение для поиска рассылок с вашей game(s) в реальном времени статистику по своему выбору.
* См. новые интерфейсы API в Microsoft::Xbox::Services::ContextualSearch

## <a name="app-insights-for-events"></a>App Insights для событий

| Примечание. |
|------|
| App Insights относится только к заголовков универсальной платформы Windows.  Если вы разрабатываете заголовок XDK, в этом разделе не применяется к вам |

<p/>

* События, написанные с использованием write_in_game_event() можно просмотреть с помощью appinsights
* Документация будет доступна на это в будущем, в то же время обратитесь к DAM, чтобы получить доступ

## <a name="logging"></a>Ведение журнала
* service_call_logging_config in xbox::services::experimental
* Для запуска и останова трассировок с помощью xbTrace.exe на консоль, необходимо вызвать register_for_protocol_activation service_call_logging_config класса.  Вызывать этот метод один раз во время инициализации игры.

## <a name="resync-for-rta"></a>Повторная синхронизация для возврата
* Повторная синхронизация возникает в том случае, если служба возврата полагает, что пользователи сведения могут быть устаревшими
* Заголовки следует вызвать соответствующие вызовы HTTP для подписок, которые они подписаны
* Заголовки не нужно повторно подписаться
* Added xbox::services::real_time_activity_service::add_resync_handler
* Removed xbox::services::real_time_activity_service::remove_resync_handler
* Добавлена http_status_429_too_many_requests
* Это условие ошибки будут отображаться при заголовок регулируется отправки слишком много запросов http

## <a name="documentation"></a>Документация
* Переход на API Xbox Live Services 2.0
* Обработка ошибок
* Xbox Live проверки подлинности в Windows 10
* Контекстный поиск
