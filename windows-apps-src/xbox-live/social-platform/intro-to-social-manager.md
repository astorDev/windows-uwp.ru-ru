---
title: Введение в диспетчер социальные сети
description: Дополнительные сведения о Xbox Live социального API диспетчера для отслеживания друзьями.
ms.assetid: d4c6d5aa-e18c-4d59-91f8-63077116eda3
ms.date: 03/26/2018
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 5dff3dcfd79fe43ff8af1513a4358bd0ff98b8d1
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57609009"
---
# <a name="introduction-to-social-manager"></a>Введение в диспетчер социальные сети

## <a name="description"></a>Описание

Xbox Live предоставляет широкие возможности социальным графом, заголовки можно использовать для различных сценариев.

В Xbox Live API (XSAPI) с помощью интерфейсов API социальных сетей, для получения и настройки сведений о социальным графом сложна, и своевременное обновление этой информации может оказаться сложной задачей.  Этого не делать правильно может привести проблем с производительностью, устаревшие данные или выполняется регулировка из-за вызова службы Xbox Live социального чаще, чем необходимо.

Диспетчер социального решает эту проблему путем:

* Создание простой API-Интерфейс для вызова
* Создание свежую информацию, с помощью службы действия реальном времени за кулисами
* Разработчики могут вызывать API диспетчера социального синхронно без любой дополнительную нагрузку на службу

Диспетчер социального скрывают сложность работы с несколькими подписками возврата и обновление данных для пользователей и позволяет разработчикам легко получить граф в актуальном состоянии, они хотят создавать интересные сценарии.

Для просмотра социального диспетчер памяти и производительности характеристики взгляните на [социального диспетчер памяти и общие сведения о производительности](social-manager-memory-and-performance-overview.md)

## <a name="features"></a>Возможности

Социальные сети Manager предоставляет следующие возможности:

* Упрощенный API социальных сетей
* В актуальном состоянии Социальный граф
* Контролировать уровень детализации сведений, отображаемых
* Уменьшить количество вызовов службы Xbox Live
  * Это напрямую связано с общее Уменьшение задержки получения данных
* Поточно-ориентированным
* Эффективно своевременное обновление данных

## <a name="core-concepts"></a>Основные понятия

**Социальный граф**: Объект *социальным графом* создается для локального пользователя на устройстве. Это создает структуру, которая хранит сведения обо всех друзей пользователей в актуальном состоянии.

> [!NOTE]
> В Windows может быть только одного локального пользователя

**Пользователь социального Xbox**: *Пользователя социальной сети Xbox* — это полный набор социальные данные, связанные с пользователем из группы

**Группа пользователей Xbox социального**: Группа — коллекция пользователей, который используется для таких вещей, как заполнение пользовательского интерфейса. Существует два типа групп

* **Группы фильтров**: Группа фильтров принимает локальный (вызывающего) пользователя *социальным графом* и возвращает согласованно набор пользователей, на основе параметров указанного фильтра
* **Группы пользователей**: Группу пользователей принимает список пользователей и возвращает согласованно свежее представление этих пользователей. Эти пользователи могут находиться за пределами списка друзей пользователя.

Чтобы сохранить *группы пользователя социальной сети* вверх на сегодняшний день функция `social_manager::do_work()` должен вызываться каждого кадра.

## <a name="api-overview"></a>Общие сведения об API

Чаще всего будет использовать следующие ключевые классы:

### <a name="social-manager"></a>Диспетчер социальные сети

* Имя класса C++ API: social_manager
* WinRT (C#) имя класса API: [SocialManager](https://docs.microsoft.com/en-us/dotnet/api/microsoft.xbox.services.social.manager.socialmanager?view=xboxlive-dotnet-2017.11.20171204.01)

Это одноэлементный класс, который может использоваться для получения **группы пользователя социальной сети Xbox** которого представления описанных выше.

Диспетчер социального сохранит xbox группы пользователя социальной сети в актуальном состоянии, а также фильтровать групп пользователей, присутствие или связь с пользователем.  Например может быть создан группу социальных сетей пользователей xbox, содержащий все друзей пользователя, которые находятся в оперативном режиме и их воспроизведении заголовок текущей.  Это будет сохраняться в актуальном состоянии при друзей, запустить или остановить воспроизведение заголовок.

### <a name="xbox-social-user-group"></a>Группа пользователя социальной сети Xbox

* Имя класса C++ API: xbox_social_user_group
* WinRT (C#) имя класса API: [XboxSocialUserGroup](https://docs.microsoft.com/en-us/dotnet/api/microsoft.xbox.services.social.manager.xboxsocialusergroup?view=xboxlive-dotnet-2017.11.20171204.01)

Группа пользователей, которые соответствуют определенным критериям, как описано выше. Группы пользователя социальной сети Xbox предоставлять тип группы пользователей, которые отслеживаются, или задать фильтр включен, а также локального пользователя, к которой принадлежит группе.

Вы найдете полное описание интерфейсов API диспетчера социального в [Справочник по Xbox Live API](https://aka.ms/xboxliveuwpdocs).
Можно также найти API WinRT в [Microsoft.Xbox.Services.Social.Manager.Namespace документации](https://docs.microsoft.com/en-us/dotnet/api/microsoft.xbox.services.social.manager?view=xboxlive-dotnet-2017.11.20171204.01)

## <a name="usage"></a>Использование

### <a name="creating-a-social-user-group-from-filters"></a>Создание группы пользователя социальной сети из фильтров

В этом случае требуется список пользователей из фильтра, например всех людей, этот пользователь подписан или не отмечен как Избранное.

#### <a name="source-example-using-the-c-api"></a>Образец исходного, с помощью C++ API

```cpp
//#include "Social.h"

auto socialManager = social_manager::get_singleton_instance();

socialManger->add_local_user(
    xboxLiveContext->user(),
    social_manager_extra_detail_level::preferred_color_level | social_manager_extra_detail_level::title_history_level
    );

auto socialUserGroup = socialManager->create_social_user_group_from_filters(
    xboxLiveContext->user(),
    presence_filter::all,
    relationship_filter::friends
    );

while(true)
{
    // some update loop in the game
    socialManager->do_work();
    // TODO: render the friends list using game UI, passing in socialUserGroup->users()
}
```

#### <a name="source-example-using-the-c-api"></a>Пример с использованием источника C# API

```csharp
// using Microsoft.Xbox.Services;
// using Microsoft.Xbox.Services.System;
// using Microsoft.Xbox.Services.Social.Manager;

socialManager = SocialManager.SingletonInstance;

socialManager.AddLocalUser(
     xboxLiveContext.User,
     SocialManagerExtraDetailLevel.PreferredColorLevel | SocialManagerExtraDetailLevel.TitleHistoryLevel
     );

socialUserGroup = socialManager.CreateSocialUserGroupFromFilters(
     xboxLiveContext.User,
     PresenceFilter.All,
     RelationshipFilter.Friends
     );

while(true)
{
     // some update loop in the game
     socialManager.DoWork();
     // // TODO: render the friends list using game UI, passing in socialUserGroup.Users
}

```

#### <a name="events-returned"></a>Возвращаемых событий

`local_user_added`(C++) | `LocalUserAdded`(C#)-Активирует после завершения загрузки социальным графом пользователей. Указать, если ошибок во время инициализации

`social_user_group_loaded`(C++) | `SocialUserGroupLoaded`(C#)-Триггеры при создании группы пользователя социальной сети

`users_added_to_social_graph`(C++) | `UsersAddedToSocialGraph`(C#)-Активирует при загрузке пользователей в

#### <a name="additional-details"></a>Дополнительные сведения

Приведенный выше пример показано, как инициализировать диспетчер социального для пользователя, создайте группу пользователя социальной сети для этого пользователя и поддерживать актуальность.

Параметры фильтрации можно увидеть в `presence_filter` и `relationship_filter` перечислений

В цикле игры `do_work` функция обновляет все созданные представления с последний моментальный снимок пользователи в этой группе.

Пользователи в представлении можно получить, вызвав `xbox_social_user_group::get_users()` функцию, которая возвращает список `xbox_social_user` объектов.  `xbox_social_user` Сведениями социальных сетей, например: тег игрока, gamerpic uri и т. д.

### <a name="create-and-update-a-social-user-group-from-list"></a>Создание и обновление группы пользователя социальной сети из списка

В этом случае необходимо, социальных сетей из списка пользователей, таких как пользователи в многопользовательской.

#### <a name="source-example-using-the-c-api"></a>Образец исходного, с помощью C++ API

```cpp
//#include "Social.h"

auto socialManager = social_manager::get_singleton_instance();

socialManger->add_local_user(
    xboxLiveContext->user(),
    social_manager_extra_detail_level::preferred_color_level | social_manager_extra_detail_level::title_history_level
    );

auto socialUserGroup = socialManager->create_social_user_group_from_list(
    xboxLiveContext->user(),
    userList  // this is a std::vector<string_t> (list of xuids)
    );

while(true)
{
    // some update loop in the game
    socialManager->do_work();
    // TODO: render the friends list using game UI, passing in socialUserGroup->users()
}
```

#### <a name="source-example-using-the-c-api"></a>Пример с использованием источника C# API

```csharp
// using Microsoft.Xbox.Services;
// using Microsoft.Xbox.Services.System;
// using Microsoft.Xbox.Services.Social.Manager;

socialManager = SocialManager.SingletonInstance;

socialManager.AddLocalUser(
     xboxLiveContext.User,
     SocialManagerExtraDetailLevel.PreferredColorLevel | SocialManagerExtraDetailLevel.TitleHistoryLevel
     );

socialUserGroup = socialManager.CreateSocialUserGroupFromList(
     xboxLiveContext.User,
     userList //this is a IReadOnlyList<string> (list of xbox user ids a.k.a. xuids)
    );

while(true)
{
     // some update loop in the game
     socialManager.DoWork();
     // // TODO: render the friends list using game UI, passing in socialUserGroup.Users
}
```

#### <a name="events-returned"></a>Возвращаемых событий

`local_user_added`(C++) | `LocalUserAdded`(C#)-Активирует после завершения загрузки социальным графом пользователей. Указать, если ошибок во время инициализации

`social_user_group_loaded`(C++) | `SocialUserGroupLoaded`(C#)-Триггеры при создании группы пользователя социальной сети

`users_added_to_social_graph`(C++) | `UsersAddedToSocialGraph`(C#)-Активирует при загрузке пользователей в

### <a name="updating-social-user-group-from-list"></a>Обновление социального группу пользователей из списка

Можно также изменить список отслеживаемых пользователей в группу пользователей социальных сетей путем вызова update_social_user_group()

#### <a name="source-example-using-the-c-api"></a>Образец исходного, с помощью C++ API

```cpp
//#include "Social.h"

socialManager->update_social_user_group(
    xboxSocialUserGroup,
    newUserList    // std::vector<string_t> (list of xuids)
    );

    while(true)
    {
    // some update loop in the game
    socialManager->do_work();
    // TODO: render the friends list using game UI, passing in socialUserGroup->users()
    }
```

#### <a name="source-example-using-the-c-api"></a>Пример с использованием источника C# API

```csharp
// using Microsoft.Xbox.Services.Social.Manager;

socialManager.UpdateSocialUserGroup(
     xboxSocialUserGroup,
     newUserList //IReadOnlyList<string> (list of xbox user ids a.k.a. xuids)
     );

while(true)
{
     // some update loop in the game
     socialManager.DoWork();
     // // TODO: render the friends list using game UI, passing in socialUserGroup.Users
}
```

#### <a name="events-returned"></a>Возвращаемых событий

`social_user_group_updated`(C++) | `SocialUserGroupUpdated`(C#)-Активирует после завершения обновления группы пользователя социальной сети.

`users_added_to_social_graph` | `UsersAddedToSocialGraph`(C#)-Активирует при загрузке пользователей в. Если пользователи, добавленные с помощью списка уже в графе, не будет вызывать это событие

### <a name="using-social-manager-events"></a>С помощью события диспетчера социальные сети

Диспетчер социального также сообщит, что произошло в форме событий.  Эти события можно использовать для настройки пользовательского интерфейса или выполнения других операций.

#### <a name="source-example-using-the-c-api"></a>Образец исходного, с помощью C++ API

```cpp
//#include "Social.h"

auto socialManager = social_manager::get_singleton_instance();
socialManger->add_local_user(
    xboxLiveContext->user(),
    social_manager_extra_detail_level::no_extra_detail
    );

auto socialUserGroup = socialManager->create_social_user_group_from_filters(
    xboxLiveContext->user(),
    presence_filter::all,
    relationship_filter::friends
    );

socialManager->do_work();
// TODO: initialize the game UI containing the friends list using game UI, socialUserGroup->users()

while(true)
{
    // some update loop in the game
    auto events = socialManager->do_work();
    for(auto evt : events)
    {
        auto affectedUsersFromGraph = socialUserGroup->get_users_from_xbox_user_ids(evt.users_affected());
        // TODO: render the changes to the friends list using game UI, passing in affectedUsersFromGraph
    }
}
```

##### <a name="source-example-using-the-c-api"></a>Пример с использованием источника C# API

```csharp
// using Microsoft.Xbox.Services;
// using Microsoft.Xbox.Services.System;
// using Microsoft.Xbox.Services.Social.Manager;

socialManager = SocialManager.SingletonInstance;

socialManager.AddLocalUser(
     xboxLiveContext.User,
     SocialManagerExtraDetailLevel.PreferredColorLevel | SocialManagerExtraDetailLevel.TitleHistoryLevel
     );

socialUserGroup = socialManager.CreateSocialUserGroupFromFilters(
     xboxLiveContext.User,
     PresenceFilter.All,
     RelationshipFilter.Friends
     );

socialManager.DoWork();
// TODO: initialize the game UI containing the friends list using game UI, socialUserGroup->users()

while(true)
{
    // some update loop in the game
    IReadOnlyList<SocialEvent> Events = socialManager.DoWork();
    IReadOnlyList<XboxSocialUser> affectedUsersFromGraph;
    foreach(SocialEvent managerEvent in Events)
    {
        affectedUsersFromGraph = socialUserGroup.GetUsersFromXboxUserIds(managerEvent.UsersAffected);
    }
}

```

#### <a name="events-returned"></a>Возвращаемых событий

`local_user_added`(C++) | `LocalUserAdded`(C#)-Активирует после завершения загрузки социальным графом пользователей. Указать, если ошибок во время инициализации

`social_user_group_loaded`(C++) | `SocialUserGroupLoaded`(C#)-Триггеры при создании группы пользователя социальной сети

`users_added_to_social_graph`(C++) | `UsersAddedToSocialGraph`(C#)-Активирует при загрузке пользователей в

#### <a name="additional-details"></a>Дополнительные сведения

В этом примере показаны некоторые предлагаемые дополнительный доверенный контроль.  Вместо того чтобы полагаться на фильтры групп пользователя социальной сети для получения списка обновленный пользовательский во время цикла игры, Социальный граф инициализируется за пределами цикла игры.  Затем зависит от заголовка `events` возвращаемые `socialManager->do_work()` функции.  `events` приведен список `social_event`и каждый `social_event` изменяет Социальный граф, возникшей во время последнего кадра.  Например `profiles_changed`, `users_added`и т. д.  Дополнительные сведения можно найти в `social_event` документации по API.

### <a name="cleanup"></a>Очистка

#### <a name="cleaning-up-social-user-groups"></a>Удаление из группы пользователя социальной сети

```cpp
//#include "Social.h"

socialManger->destroy_social_user_group(
    socialUserGroup
    );
```

```csharp
// using Microsoft.Xbox.Services.Social.Manager;

socialManager.DestroySocialUserGroup(
     socialUserGroup
     );
```

Удаляет группы пользователя социальной сети, которая была создана. Вызывающий объект также следует удалить все ссылки, которые им нужно любую группу созданного пользователя социальных сетей, так как он теперь содержит устаревшие данные.

#### <a name="cleaning-up-local-users"></a>Очистка локальных пользователей

```cpp
//#include "Social.h"

socialManger->remove_local_user(
    xboxLiveContext->user()
    );
```

```csharp
// using Microsoft.Xbox.Services.Social.Manager;

socialManager.RemoveLocalUser(
     xboxLiveContext.User
     );
```

Удаление локального пользователя удаляет Социальный граф пользователей загружены, а также все группы пользователя социальной сети, которые были созданы с помощью этого пользователя.

#### <a name="events-returned"></a>Возвращаемых событий

`local_user_removed`(C++) | `LocalUserRemoved`(C#)-Активирует при локального пользователя успешно удалена