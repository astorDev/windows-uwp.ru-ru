---
title: Отправка приглашений в игры
description: Сведения об использовании Xbox Live многопользовательские manager, чтобы разрешить отправлять приглашения игр проигрыватель.
ms.assetid: 8b9a98af-fb78-431b-9a2a-876168e2fd76
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, многопользовательскую, многопользовательские manager, блок-схема, игр приглашения
ms.localizationpriority: medium
ms.openlocfilehash: 85aa45558d1443638ba7dd50dbea8923125ef664
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57645999"
---
# <a name="send-game-invites"></a>Отправка приглашений в игры

Один из более простых ситуациях многопользовательские является возможность игроков, чтобы в игре через Интернет с друзьями. Этот сценарий рассматриваются действия, необходимые для отправки приглашения другой проигрыватель для присоединения к игре.

После [инициализировать диспетчер многопользовательскую](play-multiplayer-with-friends.md)и иметь [созданный сеанс Lobby путем добавления локальных пользователей](play-multiplayer-with-friends.md), дождитесь, пока не получите `user_added` событие, прежде чем можно приступить к отправке приглашения.

Существует два способа отправки приглашения:

1. [Приглашение платформы Xbox TCUI](#xbox-platform-invite-tcui)
2. [Название реализации пользовательского интерфейса](#title-implemented-custom-ui)

Блок-схема процесса здесь можно увидеть: [Блок-схема: Отправка приглашения для другой проигрыватель](mpm-flowcharts/mpm-send-invites.md).

### <a name="1-xbox-platform-invite-tcui-a-namexbox-platform-invite-tcui"></a>(1) приглашение платформы Xbox TCUI <a name="xbox-platform-invite-tcui">

| Метод | Событие, возникающее |
| -----|----------------|
| `multiplayer_lobby_session::invite_friends()` | `invite_sent` |

Вызов `invite_friends()` приведет к появлению стандартный пользовательский Интерфейс Xbox для приглашающей друзей. Откроется пользовательский Интерфейс, который позволяет проигрывателя для выбора друзей или последние игроков на приглашение к игре. После подтверждения переходов проигрывателя, диспетчер многопользовательскую отправляет приглашения выбранного игроков.

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

### <a name="2-title-implemented-custom-uia-nametitle-implemented-custom-ui"></a>(2) заголовок реализации пользовательского интерфейса<a name="title-implemented-custom-ui">

| Метод | Событие, возникающее |
|-----|----------------|
| `multiplayer_lobby_session::invite_users()` | `invite_sent` |

Название можно реализовать пользовательский TCUI для просмотра друзей и приглашение их. Можно использовать игры `invite_users()` метод для отправки приглашения к набору определенных по их идентификаторам пользователей Xbox Live людей. Это полезно, если вы предпочитаете использовать собственный пользовательский Интерфейс в игре, а не стандартный пользовательский Интерфейс Xbox.

**Пример:**

```cpp
std::vector<string_t>& xboxUserIds;
xboxUserIds.push_back();  // Add xbox_user_ids from your in-game roster list

auto result = mpInstance->lobby_session()->invite_users(xboxUserIds);
if (result.err())
{
  // handle error
}
```

**Функции, выполняемые диспетчером многопользовательскую**

* Отправляет приглашение непосредственно к выбранной игроков
