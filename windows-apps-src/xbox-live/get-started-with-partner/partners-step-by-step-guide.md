---
title: Пошаговое руководство для партнеров, Xbox Live
description: Рекомендации, действия, чтобы интегрировать Xbox Live для управляемых партнеров.
ms.assetid: f0c9db8f-f492-4955-8622-e1736f0a4509
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 9a2d0b9e7d97adfb02281e7bae34ed51afd44f7f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57660849"
---
# <a name="step-by-step-guide-to-integrate-xbox-live-for-managed-partners-and-idxbox-members"></a>Пошаговое руководство по интеграции Xbox Live для управляемых партнеров и ID@Xbox члены

В этом разделе помогут вам приступить к работе с Xbox Live:

## <a name="1-choose-a-platform"></a>1. Выберите платформу
Выбор пакета средств разработки Xbox (XDK), универсальной платформы Windows (UWP) или игра кросс play.

- Игры, следует выполнять только на консоль Xbox One, на основе XDK
- Игр для универсальной платформы Windows можно любой платформы Windows, например ПК Windows, Windows Phone или Xbox One
  - Для Xbox One, см. в разделе [универсальной платформы Windows в службе Xbox One](https://msdn.microsoft.com/en-us/windows/uwp/xbox-apps/index) и специально [системные ресурсы для приложений универсальной платформы Windows и игры для Xbox One](https://msdn.microsoft.com/en-us/windows/uwp/xbox-apps/system-resource-allocation)
- Кросс играть в игры, обычно игр, предназначенных для Xbox One и ПК Windows с помощью универсальной платформы Windows и XDK пути.

## <a name="2-ensure-you-have-a-title-created-in-partner-center-or-xdp"></a>2. Убедитесь, что у вас есть заголовок, созданные в центре партнеров или XDP
Каждый заголовок, Xbox Live должен быть определен в центре партнеров или портале разработчиков Xbox (XDP), прежде чем можно будет возможность входа в систему и вызывать Xbox Live службы.  [Создание нового заголовка](create-a-new-title.md) показано, как это сделать.

## <a name="3-follow-the-appropriate-guide-to-setup-your-ide-or-game-engine"></a>3. Выполните соответствующее руководство по настройке IDE или Game Engine
Следуя соответствующие руководстве по началу работы для своей платформы и ядра и изучения основ Xbox Live по мере работы.

* [Приступая к работе с помощью Visual Studio для универсальной платформы Windows игры](get-started-with-visual-studio-and-uwp.md) показано, как связать проект Visual Studio с конфигурацией Xbox Live в центре партнеров.

* [Приступая к работе с помощью Unity для универсальной платформы Windows игры](partner-add-xbox-live-to-unity-uwp.md) покажет вам о том, как создать новый Xbox Live включен заголовок Unity, добавления функции, такие как списки лидеров в заголовок вашей и создания собственного проекта Visual Studio.

* [Приступая к работе с помощью Visual Studio для XDK игры на основе](xdk-developers.md) показано, как получить настройки проекта Visual Studio при создании Xbox One title, с помощью XDK.

* [Начало работы разработка кросс play игры](get-started-with-cross-play-games.md) объясняется, как сделать продукт, который на основе XDK игры для Xbox One и универсальной платформы Windows, на основе игр для ПК с Windows 10.

## <a name="4-xbox-live-concepts"></a>4. Xbox Live основные понятия
После создания названия игры необходимо ознакомиться с концепциями Xbox Live, влияющими на процесс разработки игры.

- [Xbox Live "песочницы"](../xbox-live-sandboxes.md)
- [Xbox Live тестовых учетных записей](../xbox-live-test-accounts.md)
- [Введение в Xbox Live API-интерфейсов](../introduction-to-xbox-live-apis.md)

## <a name="5-add-xbox-live-features"></a>5. Добавление Xbox Live компонентов

Добавьте Xbox Live пополнена игры:

- [Xbox Live на платформе социальной сети - профиль, друзей, присутствие](../social-platform/social-platform.md)
- [Xbox Live, списки лидеров платформа — Stats, данных, достижений](../data-platform/data-platform.md)
- [Xbox Live многопользовательские платформа — соревнования приглашения, подбор игроков,](../multiplayer/multiplayer-intro.md)
- [Платформа хранилища Xbox — подключенного хранилища, название хранилища](../storage-platform/storage-platform.md)
- [Контекстного поиска](../contextual-search/introduction-to-contextual-search.md)

## <a name="6-release-your-title"></a>6. Название выпуска

ID@Xbox заголовки и названия, выпускаемых корпорацией компания-издатель игр это партнера корпорации Майкрософт должны проходить через процесс сертификации до выпуска.  Это, так как эти заголовки имеют доступ к дополнительным функциям, например многопользовательскую, достижений и широкие возможности присутствия.  Когда вы будете готовы к выпуску название, обратитесь к представителю Microsoft для более подробно о процессе отправки и выпуска.
