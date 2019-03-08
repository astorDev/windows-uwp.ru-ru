---
title: Проверка подлинности для проектов XDK
description: Узнайте, как выполнять вход пользователей Xbox Live в название пакета средств разработки Xbox (XDK).
ms.assetid: 713bb2e3-80c5-4ac9-8697-257525f243d3
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 597b3becfa2083955d8bd4e0adc91e4ae9b827a1
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57613499"
---
# <a name="authentication-for-xdk-projects"></a>Проверка подлинности для проектов XDK

Чтобы воспользоваться преимуществами возможностей Xbox Live в играх, пользователю нужно создать профиль Xbox Live, чтобы идентифицировать себя в сообществе Xbox Live.  Службы Xbox Live всем игра связанные действия с использованием этого профиля, Xbox Live, например: тег игрока и игроков рисунка, кто друзей пользователя игры пользователя новые игры пользователя сыграла, какие достижений, разблокировать пользователя, где пользователь ставится на Список лидеров для конкретной игры, и т.д.

На высоком уровне используйте API-интерфейсы Xbox Live, выполнив следующие действия:
1. Идентификации взаимодействующих пользователя
2. Создание контекста службы Xbox Live на основе пользователя
3. Используйте контекст Xbox Live для доступа к службам Xbox Live
4. Когда игра завершает работу или пользователь знаки out, освободить объект XboxLiveContext, присвоив ему значение null

### <a name="creating-an-xboxliveuser-object"></a>Создание объекта XboxLiveUser
Большая часть действий Xbox Live, связаны с пользователей Xbox Live.  Как разработчик игр необходимо сначала создать объект XboxLiveUser для представления локального пользователя.

C++:
```
Windows::Xbox::System::User^ user; // the interacting user.  From User::Users, etc
std::shared_ptr<xbox::services::xbox_live_context> xboxLiveContext = std::make_shared<xbox::services::xbox_live_context>( user );
```

WinRT:
```
Windows::Xbox::System::User^ user; // the interacting user.  From User::Users, etc
Microsoft::Xbox::Services::XboxLiveContext^ xboxLiveContext = ref new Microsoft::Xbox::Services::XboxLiveContext( user );
```
