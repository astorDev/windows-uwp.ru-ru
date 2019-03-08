---
title: Новые возможности для Xbox Live SDK — апрель 2016 г.
description: Новые возможности для Xbox Live SDK — апрель 2016 г.
ms.assetid: a6f26ffd-f136-4753-b0cd-92b0da522d93
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 9ce63a0174fa0c4158764b8bca2443d58d0aefd9
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57660769"
---
# <a name="whats-new-for-the-xbox-live-sdk---april-2016"></a>Новые возможности для Xbox Live SDK — апрель 2016 г.

См. в разделе [новые возможности — март 2016 г.](1603-whats-new.md) статьи о добавленных в версии 1603

## <a name="os-and-tool-support"></a>Поддержка операционной системы и средства
Xbox Live SDK поддерживает Windows 10 RTM [версия 10.0.10240] и Visual Studio 2015 RTM [версия 14.0.23107.0].

## <a name="tournaments"></a>Соревнования
- Теперь является средство Xbox Live соревнования состав пакета SDK.  Вы увидите его в каталоге Tools, а также некоторые сведения о том, как использовать его.
- Интерфейсы API для соревнования, также доступны.  Пространство имен Xbox::Services::Tournaments см. в разделе
- Документация доступна также в руководстве по программированию.

## <a name="documentation"></a>Документация
- [Руководство по устранению неполадок входа](../using-xbox-live/troubleshooting/troubleshooting-sign-in.md) перечислены некоторые общие стратегии для отладки сбоев входа в систему, а также действия для выполнения на основе кода ошибки.
- [Marketplace](https://developer.microsoft.com/en-us/games/xbox/docs/xboxlive/xbox-live-partners/xbox-marketplace/marketplace-and-downloadable-content) документация для разработчиков Xbox One только теперь находятся в руководстве по программированию.  UWP разработчики должны продолжать см. в документации в хранилище центра партнеров.
- Существует [XDK к переносу UWP руководство](../using-xbox-live/porting-xbox-live-code-from-xdk-to-uwp.md) можно ссылаться на, если вы хотите разместить Xbox One право на универсальную платформу Windows.
- См. в разделе [ограничение скорости детальной](../using-xbox-live/best-practices/fine-grained-rate-limiting.md) статьи, как они применяются для различных конечных точек службы Live Xbox и сценариев, а также сведения о ограничения, описание.

## <a name="multiplayer-manager"></a>Диспетчер многопользовательского режима
[Manager многопользовательскую](../multiplayer/multiplayer-manager.md) больше не находится в экспериментальном.  Мы отзывы от разработчиков, этот API и внесены некоторые интерфейсы API, более согласованным с друг с другом.  Используйте диспетчер многопользовательскую отправной точки при выполнении многопользовательских разработки, так как он обеспечивает более простой API, который управляет многие сложности API 2015 многопользовательскую для вас.

В следующих разделах, мы перечислены некоторые из новых функций API, а также небольшое количество критических изменений.

#### <a name="completed-events"></a>Завершенные события
Все API-интерфейсы теперь быть определена соответствующая``` _competed``` событий и все события, инициируемые независимо от успешности. Прежнее поведение было то, что она активируется только тогда, в случае сбоя для заголовка для выполнения действий на. И так, как вызовы объединяются в пакеты, это означает, что по завершении заголовок будет получать несколько ```_competed``` события.

| API | Возвратное событие |
|-----|----------------|
| ```lobby_session()->set_local_member_properties``` |  ```local_member_property_write_completed ```
| ```lobby_session()->set_local_member_connection_address``` | ```local_member_connection_address_write_completed``` |
| ```lobby_session()->set_properties``` | ```session_property_write_completed``` |
| ```lobby_session()->set_synchronized_properties``` | ```session_synchronized_property_write_completed``` |
| ```lobby_session()->set_synchronized_host``` | ```synchronized_host_write_completed``` |

То же касается ```game_session()```.

#### <a name="application-defined-context"></a>Контекст определенного приложения
Теперь можно передавать в необязательный определяемый приложением контекст для каждого set_ * методов для корреляции многопользовательские событие вызову вызывающей стороны.
Например

```cpp
_XSAPIIMP xbox_live_result<void> set_properties(
        _In_ const string_t& name,
        _In_ const web::json::value& valueJson,
        _In_ context_t context = nullptr
       );
```

Контекст затем возвращается обратно в заголовок через ```_completed``` событий.

#### <a name="breaking-changes"></a>Критические изменения

1.  Оба пригласить API-интерфейсы (```invite_friends``` & ```invite_users```) теперь являются синхронными. После завершения он возвращает invite_sent событие.

2.  ```write_synchronized_properties_and_commit``` был переименован в ```set_synchronized_properties```. После выполнения возвращает ```session_synchronized_property_write_completed``` событий.

3.  ```write_synchronized_host_and_commit``` был переименован в ```set_synchronized_host```. После выполнения возвращает ```synchronized_host_write_completed``` событий.

4.  На ```lobby_session()```

  *Удален*

```cpp
_XSAPIIMP const std::unordered_map<string_t, xbox::services:: multiplayer::multiplayer_session_tournaments_server& tournaments_server() const;
```

  *Добавлен*

```cpp
_XSAPIIMP const std::unordered_map<string_t, xbox::services::tournaments::tournament_team_result>& tournament_team_results() const;
```

5.  На ```game_session()```

  *Удален*

```cpp
_XSAPIIMP const std::unordered_map<string_t, xbox::services:: multiplayer::multiplayer_session_tournaments_server& tournaments_server() const;
_XSAPIIMP const std::unordered_map<string_t, xbox::services:: multiplayer::multiplayer_session_arbitration_server& arbitration_server() const;
```
  *Добавлен*

```cpp
_XSAPIIMP const std::unordered_map<string_t, multiplayer_session_reference>& tournament_teams() const;
_XSAPIIMP const std::unordered_map<string_t, xbox::services::tournaments::tournament_team_result>& tournament_team_results() const;
```

6.  Изменения в типы событий:

  *Удален*

```cpp
write_pending_changes_failed,
tournament_property_changed,
arbitration_property_changed
```

  *Переименован*

  ```write_synchronized_properties_completed``` переименована в ```session_synchronized_property_write_completed```

  ```write_synchronized_host_completed``` переименована в ```synchronized_host_write_completed```
