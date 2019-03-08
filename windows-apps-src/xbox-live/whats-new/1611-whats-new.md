---
title: Новые возможности для Xbox Live SDK — ноябрь 2016 г.
description: Новые возможности для Xbox Live SDK — ноябрь 2016 г.
ms.assetid: 5cf9ba9d-5a15-4e62-bc1f-45ff8b8bf3b0
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 4a9efeec63399916444de9ba33d4e587a914f2a7
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57658569"
---
# <a name="whats-new-for-the-xbox-live-sdk---november-2016"></a>Новые возможности для Xbox Live SDK — ноябрь 2016 г.

См. в разделе [новые возможности — август 2016 г.](1608-whats-new.md) статьи о добавленных в августе 2016 года.

## <a name="xbox-services-api"></a>API-Интерфейс служб Xbox

### <a name="simplified-achievements"></a>Упрощенное достижений

* [Упрощенное достижения](../achievements-2017/simplified-achievements.md) — новые и более простой способ настроить и использовать достижений.  Вы больше не нужно отправлять события и решить, является ли с достижением разблокирован службы Xbox Live.  Название отвечает за это решение.
* Если вы были частью ранний экспериментального упрощенное достижения мы также добавили поддержку автономной работы.
* Доступен новый образец вызывается SimplifiedAchievements, показывающую, насколько это просто использовать.

### <a name="multiplayer"></a>Многопользовательский режим

* [Обзор сеансов](../multiplayer/session-browse.md) новый способ для пользователей, чтобы узнать игры.  Обзор сеансов позволяет игрокам для поиска списка открытых многопользовательские игры сеансов, соответствующих указанным критериям.
* [Manager многопользовательскую](../multiplayer/multiplayer-manager.md) теперь имеет возможности автоматически заполняется.  Если параметр включен, диспетчер многопользовательскую будет найти элементы через партнеров для заполнения открытых слотов во время игровой процесс.
* Предварительную версию [XIM (Xbox интегрированной многопользовательскую)](../multiplayer/xbox-integrated-multiplayer.md) теперь доступна для разработки XDK.  XIM — это автономное интерфейс для простого добавления многопользовательские взаимодействий в реальном времени сети и чата в игру с помощью мощных возможностей службы Xbox Live.

### <a name="social-manager"></a>Диспетчер социальные сети

* Многочисленные [Manager социального](../social-platform/intro-to-social-manager.md) изменения API:
    * Социальные manager покинул экспериментальный пространства имен. Особую благодарность выражаю тех, кто были первыми и предоставления отзывов!
    * `xbox_social_user`
        * `string_t` объекты были изменены на `char*` объектов
        * Запись пользовательских присутствия с лимитом из шести `social_manager_presence_title_record` на `social_manager_presence_record`
    * `social_event`
        * Возвращает `std::vector<xbox_user_id_container>` вместо `std::vector<xbox_social_user>`
        * Данный вектор может быть передан в новый интерфейс API `xbox_social_user_group::get_users_from_xbox_user_ids()`
    * `xbox_social_user_group`
        * `users()` API теперь возвращает `std::vector<xbox_social_user*>`. Эти указатели становятся недействительными при следующем вызове для `social_manager::do_work()`
        * `get_copy_of_users` вернуть `std::vector<xbox_social_user>` как копию текущих пользователей в social_user_group вызывающему объекту. Вызов этой функции может повлиять на `do_work` время завершения.
* Теперь диспетчер социального никогда не завершится ошибкой после инициализации. Диспетчер социального повторит попытку возврата автоматически при отключении от. `error` И `rta_disconnect_error` устарели и удаления событий
* Заголовок можно указать пользовательские памяти Распределители. См. в разделе новую документацию: [Социальные сети диспетчера памяти и производительности](../social-platform/social-manager-memory-and-performance-overview.md)

### <a name="xbox-one-uwp"></a>Xbox One UWP
* API-интерфейсы TCUI добавляет поддержку нескольких пользователей для приложений UWP один Xbox.  XSAPI C++ добавляет новый Windows::System::User ^ параметров укажите пользователя, и XSAPI WinRT API добавляет ForUserAsync API-интерфейсы.
* Обновленные образцы универсальной платформы Windows для поддержки нескольких пользователей на Xbox

### <a name="other"></a>Другое

* C + +/ добавлена поддержка WinRT.   Дополнительные сведения можно найти [здесь](../introduction-to-xbox-live-apis.md)
* Новые функции в добавлять и удалять свои собственные обратные вызовы ведения журнала.  Уровень диагностики будут переданы обратному вызову, поэтому можно настроить поведение.  См. в разделе `add_logging_handler` и `remove_logging_handler` в `microsoft::xbox::services::system::xbox_live_services_settings` пространства имен

## <a name="documentation"></a>Документация
* Отсутствует новую документацию на [многопользовательскую Manager](../multiplayer/multiplayer-manager.md), [XIM](../multiplayer/xbox-integrated-multiplayer.md), и [многопользовательские понятия](../multiplayer/multiplayer-concepts.md) для Xbox Live.
* [Xbox Live Введение](../get-started-with-partner/get-started-with-xbox-live-partner.md) разделы были переписаны.  Если вы создаете новый заголовок Xbox Live включена или включения другие функциональные возможности, Xbox Live в игре, подготавливающим, можно увидеть новые документы [здесь](../get-started-with-partner/get-started-with-xbox-live-partner.md).

## <a name="tools"></a>Инструменты
* Xbox Live трассировки анализатор которого можно найти на [ https://aka.ms/XboxLiveTools ](https://aka.ms/XboxLiveTools) теперь имеет режим сертификата.  
* Также в анализаторе Xbox Live трассировки является поддержка нескольких консоли.  Если передать трассировки Fiddler, содержащий HTTP-запросы для нескольких консоли, отдельные отчеты будут создаваться для каждого из них.
