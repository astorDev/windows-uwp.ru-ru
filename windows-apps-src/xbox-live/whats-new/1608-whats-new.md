---
title: Новые возможности для Xbox Live SDK — август 2016 г.
description: Новые возможности для Xbox Live SDK — август 2016 г.
ms.assetid: fa52e7bd-2c2c-4c25-94ab-761036a7ca79
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 2a498fea1ed0974935a273c9ee72ba2c95d15959
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57627909"
---
# <a name="whats-new-for-the-xbox-live-sdk---august-2016"></a>Новые возможности для Xbox Live SDK — август 2016 г.

См. в разделе [новые возможности — июнь 2016 г.](1606-whats-new.md) статьи о добавленных в июне 2016 г.

## <a name="os-and-tool-support"></a>Поддержка операционной системы и средства
Xbox Live SDK поддерживает Windows 10 RTM [версия 10.0.10240] и Visual Studio 2015 RTM [версия 14.0.23107.0].

## <a name="documentation"></a>Документация
- Если вы создаете приложение UWP и реализуется возможность приглашать пользователей к игре, нет указаний на ```.appxmanifest``` изменения, необходимые в [Настройка Your AppXManifest для многопользовательскую](../multiplayer/service-configuration/configure-your-appxmanifest-for-multiplayer.md).  Это было ранее описано в [форумы](https://forums.xboxlive.com) и [перенос xbox live кода из эры в uwp](../using-xbox-live/porting-xbox-live-code-from-xdk-to-uwp.md) статьи
- [Введение в диспетчер социального](../social-platform/intro-to-social-manager.md) статья будет обновлена для отображения последних изменений API и предоставляют дополнительные сведения о кодах возврата для некоторых функций.

## <a name="unity-samples"></a>Примеры Unity
Были добавлены некоторые новые примеры для разработчиков Unity, создающих приложения универсальной платформы Windows.
- Теперь имеется версия Unity примера социальных сетей, его можно найти в каталоге образцов/социального/Unity.
- Также имеется пример, в которых описываются способы использования подключения хранилища.  Дополнительные сведения см. Примеры/GameSave/Unity образца.
Отсутствует версия Unity AchievementsLeaderboard в разделе примеров/AchievementsLeaderboard/Unity

## <a name="social-manager"></a>Диспетчер социальные сети
Помимо обновления документации, упомянутых выше существуют некоторые новые интерфейсы API, которые были добавлены.  Они описаны ниже, и вы увидите более подробно в social_manager.h

- Добавлен новый API, который позволяет обновлять социальные группы без воссоздания:

```cpp
    _XSAPIIMP xbox_live_result<void> update_social_user_group(
        _In_ const std::shared_ptr<xbox_social_user_group>& group,
        _In_ const std::vector<string_t>& users
        );
```
- Завершенные обновления социальных сетей группы обозначается ```social_user_group_updated``` событий


## <a name="multiplayer"></a>Многопользовательский режим
Улучшенная сеанса просмотра становится доступной, и можно использовать новые интерфейсы API многопользовательскую его использовать.

С помощью новых интерфейсов API, можно фильтровать на теги, строк и других многофункциональных данных пользователям было проще находить сеанса, которые требуется воспроизвести.

Будет исправлена более полную документацию в ближайшие месяцы, но кратко теперь можно связать «дескриптор поиска» с MPSD сеанса с помощью ```set_search_handle``` и затем пользователи могут искать сеансов с помощью надежный механизм фильтрации, свое призвание title ```get_search_handles```

Ниже перечислены новые интерфейсы API.  Попробуйте их и если возникнут проблемы, опубликуйте их поддержка потока в [форумы](https://forums.xboxlive.com) или обратиться к вашей DAM.  У нас есть примеры того, как использовать эти API-интерфейсы в ближайшее время.

```cpp
_XSAPIIMP pplx::task<xbox_live_result<void>> set_search_handle(
    _In_ multiplayer_search_handle_request searchHandleRequest
    );
```

```cpp
_XSAPIIMP pplx::task<xbox_live_result<std::vector<multiplayer_search_handle_details>>> get_search_handles(
    _In_ const string_t& serviceConfigurationId,
    _In_ const string_t& sessionTemplateName,
    _In_ const string_t& orderBy,
    _In_ bool orderAscending,
    _In_ const string_t& searchQuery
    );
```

```cpp
_XSAPIIMP pplx::task<xbox_live_result<void>> clear_search_handle(_In_ const string_t& handleId);
```

### <a name="xbox-integrated-multiplayer"></a>Сетевая интегрированная Xbox

Мы добавили документации для Xbox интегрированной многопользовательскую (XIM) API.  Сам интерфейс API, которые будут доступны в последующих версиях пакета SDK для Xbox Live, но документация и заголовок доступны для предварительной версии.

XIM — это автономное интерфейс для простого добавления многопользовательские взаимодействий в реальном времени сети и чата в игру с помощью мощных возможностей службы Xbox Live.

Эта предварительная версия документации по API здесь совместно для стимулирования отзывы пользователей и запрос. Мы говорили о этого API выше Xfest 2016, и вы увидите архивные [материал презентации на сайте разработчиков управляемого партнера](https://developer.xboxlive.com/en-us/platform/documentlibrary/events/Pages/Xfest2016.aspx) из докладе «Под ключ многопользовательскую сетевые подключения и Chat». Обратите внимание на то, что эта документация Предварительная версия предоставляется только в C++ API. Эквиваленты WinRT для C# и других языках будет выпущена позже в этом году.

Если вы заинтересованы в возможности его XIM, обратную связь или другие вопросы об этом проекте вы можете разместить на [форум разработчиков Xbox](https://forums.xboxlive.com/) или свяжитесь с нами через менеджеру разработчика.

Вы увидите этот новую документацию в xbox_integrated_multiplayer.chm в каталог документации пакета SDK для Xbox Live.  Включаемого файла доступна в предварительной версии в \include\xim\XboxIntegratedMultiplayer.h.  
