---
title: Программирования службы активности в реальном времени
description: Дополнительные сведения о программировании в Xbox Live в реальном времени действия службе с помощью API-интерфейсы C++.
ms.assetid: 98cdcb1f-41d8-43db-98fc-6647755d3b17
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, действие в режиме реального времени
ms.localizationpriority: medium
ms.openlocfilehash: f8846d57343f4f7262bbeea2cec03465fa23b2ab
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57629999"
---
# <a name="programming-the-real-time-activity-service-using-c-apis"></a>Программирование службу действий в реальном времени с помощью интерфейсов API C++

В этой статье содержатся следующие подразделы

* Подключение к службе активности в реальном времени от Xbox Live
* Отключено от службы активности в реальном времени
* Создание статистики
* Подписка на статистику из действия в реальном времени
* Отмена подписки на статистику из службы действий в реальном времени
* Пример

## <a name="connecting-to-the-real-time-activity-service-from-xbox-live"></a>Подключение к службе активности в реальном времени от Xbox Live

Приложениям необходимо подключиться к службе в режиме реального времени действия возврата для получения сведений о событиях из Xbox Live. В этом разделе показано, как создать такое подключение.

> [!NOTE]
> Примеры, которые используются в этом разделе указывают вызовы методов для одного пользователя. Тем не менее название должно выполнять эти вызовы для всех пользователей, для подключения и отключения от службы в реальном времени действия возврата.

### <a name="connecting-to-the-real-time-activity-service"></a>Подключение к службе активности в реальном времени

```cpp
void Example_RealTimeActivity_ConnectAsync()
{
    // Get the list of users from the system, and create an Xbox Live context from the first.
    // This user's authentication token will be used for the service requests.

    // Note, only retrieve an XboxLiveContext for a given user *once*.  Otherwise you may encounter unpredictable behavior.
    std::shared_ptr<xbox_live_context> xboxLiveContext = std::make_shared<xbox_live_context>(User::Users->GetAt(0));

    xboxLiveContext->real_time_activity_service()->activate();
}
```

### <a name="creating-a-statistic"></a>Создание статистики

Создание статистики для XDP, если вы являетесь XDK разработчика или рабочий заголовок, кросс play.  Создание статистики в центре партнеров при создании чисто универсальной платформы Windows, под управлением Windows 10.

#### <a name="xdk-developers"></a>Разработчики XDK

Сведения о том, как создать в состоянии на XDP см. в разделе [XDP документации](https://developer.xboxlive.com/en-us/xdphelp/development/xdpdocs/Pages/setting_up_service_configuration_10_27_15_a.aspx#events).  После создания вашей stat и определенных событий, будет необходимо запустить [XCETool](https://developer.xboxlive.com/en-us/platform/development/documentation/software/Pages/atoc_xce_jun15.aspx) для создания заголовка, используемый в приложении.  Этот заголовок содержит функции, которые можно вызывать для отправки событий, которые изменяют stats.

#### <a name="uwp-developers"></a>Разработчики универсальной платформы Windows

При разработке UWP в Windows 10, который не является заголовок кросс play, вы определите вашей Статистика в [центра партнеров](https://partner.microsoft.com/dashboard). Чтение [статье конфигурации центра партнеров stats](../leaderboards-and-stats-2017/player-stats-configure-2017.md) чтобы узнать, как настроить статистики в центре партнеров.

> [!NOTE]
> Статистика 2013 для разработчиков потребуется обратиться к их DAM для сведений о [настройки Stats 2013](https://developer.microsoft.com/en-us/games/xbox/docs/xdk/windows-configure-stats-2013) в [центра партнеров](https://partner.microsoft.com/dashboard).

### <a name="disconnecting-from-the-real-time-activity-service"></a>Отключение от службы действий в реальном времени

```cpp
void Example_RealTimeActivity_Disconnect()
{
    // Get the list of users from the system, and create an Xbox Live context from the first.
    // This user's authentication token will be used for the service requests.
    std::shared_ptr<xbox_live_context> xboxLiveContext = std::make_shared<xbox_live_context>(User::Users->GetAt(0));

    xboxLiveContext->real_time_activity_service()->deactivate();
}
```

## <a name="subscribing-to-a-statistic-from-the-real-time-activity"></a>Подписка на статистику из действия в реальном времени

Приложения подписываться на в реальном времени действия возврата для получения обновлений, при изменении статистики, настроенных в портале разработчиков Xbox (XDP) или центра партнеров.

### <a name="subscribing-to-a-statistic-from-the-real-time-activity-service"></a>Подписка с показателем от службы действий в реальном времени

```cpp
void Example_RealTimeActivity_SubscribeToStatisticChangeAsync()
{
    // Obtain xboxLiveContext as shown in the connect function.  Then add a handler to be called on statistic changes.
    function_context statisticsChangeContext = xboxLiveContext->user_statistics_service().add_statistic_changed_handler(
        [](statistic_change_event_args args )
        {
            // Called on statistic change.  Inspect args to see which one.
            DebugPrint("%S %S", args.latest_statistic().statistic_name().c_str(), args.latest_statistic().value().c_str());
        }
    );

    // Call to subscribe to an individual statistic.  Once the subscription is complete, the handler will be called with the initial value of the statistic.
    auto statisticResults = xboxLiveContext->user_statistics_service().subscribe_to_statistic_change(
        User::Users->GetAt(0)->XboxUserId->Data(),
        L"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",    // Get SCID from "Product Details" page in XDP or the Xbox Live Setup page in Partner Center
         L"YourStat"
        );

    std::shared_ptr<statistic_change_subscription> statisticsChangeSubscription;

    if(!statisticResults.err())
    {
        statisticsChangeSubscription = statisticResults.payload();
    }
    else
    {
        DebugPrint("Error calling SubscribeToStatistic: %S", statisticResults.err_message().c_str());
    }
}
```

## <a name="unsubscribing-from-a-statistic-from-the-real-time-activity-service"></a>Отмена подписки на статистику из службы действий в реальном времени

Приложения подписываться на статистику от службы в реальном времени действия возврата, чтобы получать обновления при изменении статистики. Когда эти обновления больше не требуются, подписки может быть завершен и кода в этом разделе показано, как это сделать.

### <a name="unsubscribing-from-a-real-time-services-statistic"></a>Отмена подписки на статистику службы в режиме реального времени

```cpp
void Example_RealTimeActivity_UnsubscribeFromStatisticChangeAsync()
{
    // statisticsChangeSubscription from the Example_RealTimeActivity_SubscribeToStatisticChangeAsync function.
    xboxLiveContext->user_statistics_service().unsubscribe_from_statistic_change(
        statisticsChangeSubscription
        );
}
```

> [!IMPORTANT]
> Приведет к отключению службы через два часа использования действия в реальном времени, код должен иметь возможность определит это и повторно установить подключение к службе в режиме реального времени действия, если он по-прежнему необходим. Это делается в первую очередь, чтобы убедиться, что токены аутентификации обновляются после истечения срока действия.
> 
> Если клиент использует возврата для многопользовательской сеансов и отключен в течение 30 секунд, многопользовательские Directory(MPSD) сеанса обнаруживает, что возврата сеанс закрывается и запускает пользователя из сеанса. Он работает для возврата клиенту обнаружить, когда соединение закрывается и инициировать выполняется попытка повторного подключения и повторно подписаться перед MPSD завершает сеанс.