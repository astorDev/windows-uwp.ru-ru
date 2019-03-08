---
title: Многопользовательские игры с помощью SmartMatch
description: Сведения об использовании Xbox Live многопользовательские manager позволяет найти игры с помощью SmartMatch проигрыватель.
ms.assetid: f9001364-214f-4ba0-a0a6-0f3be0b2f523
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, многопользовательскую, многопользовательские manager, блок-схема, smartmatch
ms.localizationpriority: medium
ms.openlocfilehash: 0c0ba897f23eb690c3044b00cdb4ce3bea975e71
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655959"
---
# <a name="find-a-multiplayer-game-by-using-smartmatch"></a>Найти игры с помощью SmartMatch

В некоторых случаях игроков может не иметь достаточно друзей через Интернет по мере необходимости в игре, или они просто хотите изучить от случайных пользователей сети. Можно использовать службу Xbox Live SmartMatch найти другие игроки Xbox Live

В этой статье описываются основные шаги, необходимо реализовать SmartMatch подбор игроков с помощью диспетчера многопользовательскую.

## <a name="find-a-match"></a>Найти совпадение

Участвуют четыре шага при использовании диспетчера многопользовательскую для отправки приглашения дружественной для пользователя, а затем для этого друга как присоединить игры в ход выполнения:

1. [Инициализировать диспетчер многопользовательскую](#initialize-multiplayer-manager)
2. [Создание сеанса основной путем добавления локальных пользователей](#create-lobby)
3. [Отправлять приглашения друзьям](#send-invites)
4. [Принимать приглашения](#accept-invites)
5. [Найти совпадение](#find-match)

Шаги 1, 2, 3 и 5, выполняется на устройства, выполняющего приглашение.  Шаг 4 обычно будет запускаться на компьютере приглашенного пользователя, выполнив запуск приложений с помощью протокола активации.

Блок-схема процесса здесь можно увидеть: [Блок-схема: Play игры с помощью координирующему SmartMatch](mpm-flowcharts/mpm-play-with-smartmatch-matchmaking.md).

### <a name="1-initialize-multiplayer-manager-a-nameinitialize-multiplayer-manager"></a>(1) инициализировать диспетчер многопользовательскую <a name="initialize-multiplayer-manager">

| Вызов | Событие, возникающее |
|-----|----------------|
| `multiplayer_manager::initialize(lobbySessionTemplateName)` | Н/Д |

Основной объект сеанса создается автоматически при инициализации диспетчера многопользовательскую, при условии, что указан действительный сеанс имя шаблона (настраивается в конфигурации службы). Обратите внимание на то, что при этом не создается экземпляр сеанса основной службы.

**Пример:**

```cpp
auto mpInstance = multiplayer_manager::get_singleton_instance();

mpInstance->initialize(lobbySessionTemplateName);
```


### <a name="2-create-the-lobby-session-by-adding-local-usersa-namecreate-lobby"></a>(2) создайте сеанс lobby путем добавления локальных пользователей<a name="create-lobby">

| Метод | Событие, возникающее |
|-----|----------------|
| `multiplayer_lobby_session::add_local_user()` | `user_added_event` |
| `multiplayer_lobby_session::set_local_member_connection_address()` | `local_member_connection_address_write_completed ` |
| `multiplayer_lobby_session::set_local_member_properties()` | `member_property_changed` |

Здесь можно добавить подписанный локально в Xbox Live пользователям lobby сеанса. Это размещает новый основной при добавлении первого пользователя. Для других пользователей они будут добавлены в существующий веселое занятие как вторичный пользователи. Этот API также будет объявлять основной оболочки для друзей, для присоединения. Можно отправлять приглашения, задайте свойства основной, доступа к членам основной через lobby(), только в том случае, после добавления локального пользователя.

После присоединения к выходу, корпорация Майкрософт рекомендует настроить адреса подключения локального элемента, а также любые пользовательские свойства для элемента.

Этот процесс необходимо повторить для всех локально входа пользователей.

**Пример: (один локальный пользователь)**

```cpp
auto mpInstance = multiplayer_manager::get_singleton_instance();

auto result = mpInstance->lobby_session()->add_local_user(xboxLiveContext->user());
if (result.err())
{
  // handle error
}

// Set member connection address
string_t connectionAddress = L"1.1.1.1";
mpInstance->lobby_session()->set_local_member_connection_address(
    xboxLiveContext->user(), connectionAddress);

// Set custom member properties
mpInstance->lobby_session()->set_local_member_properties(xboxLivecontext->user(), ..., ...)
```

**Пример: (несколько локальных пользователей)**

```cpp
auto mpInstance = multiplayer_manager::get_singleton_instance();
string_t connectionAddress = L"1.1.1.1";

for (User^ user : User::Users)
{
  if( user->IsSignedIn )
  {
    auto result = mpInstance.lobby_session()->add_local_user(user);
    if (result.err())
    {
      // handle error
    }

    // Set member connection address
    mpInstance->lobby_session()->set_local_member_connection_address(
        xboxLiveContext->user(), connectionAddress);

    // Set custom member properties
    mpInstance->lobby_session()->set_local_member_properties(xboxLivecontext->user(), ..., ...)
  }
}

```


Изменения объединяются в пакеты при следующем `do_work()` вызова.  
Многопользовательские manager активируется `user_added` каждый раз при добавлении пользователя к сеансу основной. Рекомендуется тщательно изучать код ошибки события, чтобы увидеть, если этот пользователь был успешно добавлен. Если произойдет сбой сообщение об ошибке будет предоставляться с подробным описанием причин сбоя.

**Функции, выполняемые диспетчером многопользовательскую**

* Регистрации действий в режиме реального времени и многопользовательскую подписки в службе Xbox Live многопользовательские
* Создание сеанса основной
* Присоединение всех местных работодателей как активный
* Отправка SDA
* Свойства набора элементов
* Регистрация для события изменения сеанса
* Основной сеанса в качестве активного сеанса

### <a name="3-send-invites-to-friends-optional-a-namesend-invites"></a>(3) send приглашает друзьям (необязательно) <a name="send-invites">

| Метод | Событие, возникающее |
| -----|----------------|
| `multiplayer_lobby_session::invite_friends()` | `invite_sent` |
| `multiplayer_lobby_session::invite_users()` | `invite_sent` |

Далее нужно будет открыть стандартный пользовательский Интерфейс Xbox для приглашающей друзей. Откроется пользовательский Интерфейс, который позволяет проигрывателя для выбора друзей или последние игроков на приглашение к игре. После подтверждения переходов проигрывателя, диспетчер многопользовательскую отправляет приглашения выбранного игроков.

Можно также использовать игры `invite_users()` метод для отправки приглашения к набору определенных по их идентификаторам пользователей Xbox Live людей. Это полезно, если вы предпочитаете использовать собственный пользовательский Интерфейс в игре, а не стандартный пользовательский Интерфейс Xbox.

**Пример:**

```cpp
auto result = mpInstance->lobby_session()->invite_friends(xboxLiveContext);
if (result.err())
{
  // handle error
}
```

**Функции, выполняемые диспетчером многопользовательскую**

* Переводит вверх Xbox stock title вызываемой пользовательского интерфейса (TCUI)
* Отправляет приглашение непосредственно к выбранной игроков

### <a name="4-accept-invites-optional-a-nameaccept-invites"></a>(4) принимать приглашения (необязательно) <a name="accept-invites">

| Метод | Событие, возникающее |
| -----|----------------|
| `multiplayer_manager::join_lobby(Windows::ApplicationModel::Activation::IProtocolActivatedEventArgs^ args)` | `join_lobby_completed_event` |
| `multiplayer_lobby_session::set_local_member_connection_address()` | `local_member_connection_address_write_completed ` |
| `multiplayer_lobby_session::set_local_member_properties()` | `member_property_changed` |

Когда приглашенный проигрывателя примет игр приглашения или объединить игры друзей через оболочку пользовательского интерфейса, игра запускается на устройстве с помощью протокола активации. Один раз игры начинается, многопользовательские Manager может использовать аргументы события активирован протокол для присоединения к основной. При необходимости Если вы еще не добавили локальных пользователей через lobby_session()::add_local_user(), вы можете передать в список пользователей через join_lobby() API. Если приглашенного пользователя не добавляется через lobby_session()::add_local_user() или через join_lobby(), а затем join_lobby() будут завершаться сбоем, предоставив invited_xbox_user_id(), который сообщение invite был отправлен для как часть join_lobby_completed_event_args().

После присоединения к выходу, корпорация Майкрософт рекомендует настроить адреса подключения локального элемента, а также любые пользовательские свойства для элемента. Можно также задать узла через set_synchronized_host, если она не существует.

И, наконец диспетчер многопользовательскую автоматическое соединения пользователя в игры сеанс, если игра уже выполняется и имеется достаточно места для приглашенного пользователя. Заголовок, будут уведомлены событие join_game_completed, предоставляя соответствующий код ошибки и сообщение.

**Пример:**

```cpp
auto result = mpInstance().join_lobby(IProtocolActivatedEventArgs^ args);
if (result.err())
{
  // handle error
}

string_t connectionAddress = L"1.1.1.1";
mpInstance->lobby_session()->set_local_member_connection_address(
    xboxLiveContext->user(), connectionAddress);

```

Ошибка/успех обрабатывается с помощью `join_lobby_completed` событий

**Функции, выполняемые диспетчером многопользовательскую**

* Регистрация возврата & многопользовательские подписок
* Основной веб-трансляции
 * Существующий основной состояние очистки
 * Присоединение всех местных работодателей как активный
 * Отправка SDA
 * Свойства набора элементов
* Регистрация для события изменения сеанса
* Основной сеанса в качестве активного сеанса
* Веб-трансляции игры (если существует)
 * Использует передачи дескриптора


### <a name="5-find-match-a-namefind-match"></a>(5) найти соответствие <a name="find-match">

| Вызов | Событие, возникающее |
|-----|----------------|
| `multiplayer_manager::find_match()` | Многие события, как описано в разделе ```match_status``` (например: ```searching```, ```found```, ```measuring```и т. д) |

После приглашения, если были приняты, и узел готов к запуску, игры, можно использовать SmartMatch либо найти существующий игры, в которой имеется достаточно открыть проигрыватель слотов для всех элементов в сеансе lobby путем вызова `find_match()`, или создайте новый игр se ssion, который включает в себя все элементы из сеанса основной и заполнения откройте зон с другими людьми, ищет совпадение однотипных игр, путем вызова `join_game_from_lobby()` следует `set_auto_fill_members_during_matchmaking()`.

Перед вызовом `find_match()`, необходимо сначала настроить hoppers в конфигурации службы. Hopper определяет правила, которые использует SmartMatch в соответствии с игроков.

**Пример:**

```cpp
auto result = mpInstance.find_match(HOPPER_NAME);
if (result.err())
{
  // handle error
}
```

**Функции, выполняемые диспетчером многопользовательскую**

* Создайте запрос в службу соответствия
* Обрабатывать все этапы QoS
* Дескриптор списка изменений
 * Повторная отправка (при необходимости)
* Соединения целевой игр сеанса
* Объявление игрой, основной сеанса
