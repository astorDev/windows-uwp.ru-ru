---
title: Новые возможности для Xbox Live SDK — июнь 2015 г.
description: Новые возможности для Xbox Live SDK — июнь 2015 г.
ms.assetid: 354bcd47-2564-4dd5-89e3-242bca462b35
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: a42d0fb0a3cb457a60a0542bfc5966893d00f18b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57627879"
---
# <a name="whats-new-for-the-xbox-live-sdk---june-2015"></a>Новые возможности для Xbox Live SDK — июнь 2015 г.

Июньский выпуск пакета SDK для Xbox Live включает следующие обновления:

## <a name="os-and-tool-support"></a>Поддержка операционной системы и средства ##
Xbox Live SDK теперь поддерживает последней сборки программы предварительной оценки Windows 10 и Visual Studio 2015 RC.

## <a name="title-callable-ui-apis"></a>API-интерфейсы Title вызываемой пользовательского интерфейса

| Примечание. |
|------|
| Этот раздел относится только к универсальной платформы Windows названия, заголовки XDK должны ссылаться на класс Windows.Xbox.UI.SystemUI для TCUI  |

Xbox Live SDK теперь содержит программы-оболочки, API, которые поддерживает заголовок пользовательского интерфейса (TCUI вызываемой) для отображения стандартный пользовательский Интерфейс на устройстве Windows 10 ПК и мобильных.

Эти API доступны только для приложений универсальной платформы Windows.

TCUI оболочки можно вызвать из TitleCallableUI (WinRT) и классы title_callable_ui (C++).

Акции, которые включают пользовательские интерфейсы:
* Пользовательский Интерфейс выбора проигрывателя
* Пользовательский Интерфейс выбора игр приглашения
* Карточки профиля проигрывателя пользовательского интерфейса
* Добавление или удаление friend пользовательского интерфейса
* Показать название игры достижения пользовательского интерфейса

См. в разделе нового *TCUI* например пример использования новых интерфейсов API. Вы найдете пример в разделе {*корень пакета SDK для источника*} \Samples\TCUI.

## <a name="new-authentication-model-for-uwp-apps"></a>Новая модель проверки подлинности для приложений UWP
Xbox Live SDK теперь поддерживает, с использованием учетной записи Майкрософт (MSA) для идентификации проигрыватель Xbox Live в Windows 10 ПК или мобильного устройства.

Вы можете войти в пользователя с помощью XboxLiveUser (WinRT) и классы xbox_live_user (C++).

## <a name="new-api-for-writing-events-in-uwp-apps"></a>Новый интерфейс API для записи событий в приложениях UWP

| Примечание. |
|------|
| Этот раздел относится только к продуктам универсальной платформы Windows.  XDK разработчики должны использовать [события игры](https://developer.microsoft.com/en-us/games/xbox/docs/xboxlive/xbox-live-partners/event-driven-data-platform/game-events) статье XDK подробные сведения  |

Новый EventsService (WinRT) и классы events_service (C++) позволяют записывать события в играх, которые могут обновлять Статистика пользователей, достижения, списки лидеров и т. д. Эти новые классы являются только для приложений UWP.

## <a name="breaking-change-to-event-handlers"></a>Критически важное изменение для обработчиков событий ##
Все обработчики событий в пакете SDK для C++ были изменены из одного `void set_*_handler()` метод к паре `function_context add_*_handler()` и `void remove_*_handler(function_context context)` методы.

Каждый `add_*_handler()` метод теперь возвращает `function_context` объекта. При удалении обработчика событий, необходимо передать в `function_context` объекта.

Например:
```
function_context subscriptionLostContext = xbox_live_context()->multiplayer_service().add_multiplayer_subscription_lost_handler(...);

localUser->xbox_live_context()->multiplayer_service().remove_multiplayer_subscription_lost_handler(subscriptionLostContext);
```

## <a name="new-apis-for-managing-multiplayer-scenarios"></a>Новые интерфейсы API для управления использовать многопользовательские сценарии
Xbox Live SDK теперь включает в себя набор интерфейсов API C++ для управления распространенные многопользовательские сценарии. API-интерфейсы, включаются в пространство имен xbox.services.experimental.multiplayer.

Эти API-интерфейсы являются в экспериментальном имен, которое означает, что они часто изменять на основе отзывов.  Мы рекомендуем разработчикам использовать их и отправить отзыв.

См. в разделе нового *MultiplayerManager* например пример использования этих новых интерфейсов API. Вы найдете пример в разделе {*корень пакета SDK для источника*} \Samples\MultiplayerManager.
