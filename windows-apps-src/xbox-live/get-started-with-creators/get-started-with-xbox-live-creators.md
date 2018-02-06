---
title: "Начало работы с программой Xbox Live Creators Program"
author: KevinAsgari
description: "Предоставляет ссылки, которые помогут вам приступить к работе с программой Xbox Live Creators Program."
ms.assetid: 2a744405-7ee4-42b4-8f36-9916e8c3a530
ms.author: kevinasg
ms.date: 12/13/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "xbox live, xbox, игры, uwp, windows 10, xbox one"
ms.localizationpriority: high
ms.openlocfilehash: ba7f974ec6157e8f1d94232a64099fcae766eba8
ms.sourcegitcommit: c80b9e6589a1ee29c5032a0b942e6a024c224ea7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="get-started-with-the-xbox-live-creators-program"></a>Начало работы с программой Xbox Live Creators Program
 
Программа Xbox Live Creators Program позволяет быстро и напрямую публиковать игры для Xbox One и Windows 10 благодаря упрощенному процессу сертификации и отсутствию необходимости в утверждении ее концепции. Если игра интегрируется в Xbox Live и соответствует нашим [стандартным политикам Store](https://msdn.microsoft.com/en-us/library/windows/apps/dn764944.aspx), можно приступать к публикации. В этой статье приведены шаги, необходимые для публикации игры с интеграцией с Xbox Live. 

Игры по программе Xbox Live Creators Program должны являться приложениями универсальной платформы Windows (UWP). Сведения об Xbox One см. в разделе [Приложения UWP для Xbox One](https://msdn.microsoft.com/en-us/windows/uwp/xbox-apps/index) и в частности [Системные ресурсы для приложений UWP и игр для Xbox One](https://msdn.microsoft.com/en-us/windows/uwp/xbox-apps/system-resource-allocation). Игры, публикуемые по программе Xbox Live Creators Program, не имеют доступа к достижениям и многопользовательским сетевым службам. Полный список поддерживаемых служб см. в разделе [Обзор программ для разработчиков: таблица функций](https://docs.microsoft.com/en-us/windows/uwp/xbox-live/developer-program-overview#feature-table).

## <a name="1-ensure-you-have-a-title-created-on-dev-center"></a>1. Убедитесь, что в Центре разработки указано название игры
Каждая игра Xbox Live должна быть определена в Центре разработки, прежде чем можно будет входить в систему и совершать вызовы службы Xbox Live.  В разделе [Создание новой игры по программе Creators](create-and-test-a-new-creators-title.md) показано, как это сделать.

## <a name="2-follow-the-appropriate-guide-to-setup-your-ide-or-game-engine"></a>2. Следуйте соответствующему руководству для настройки интегрированной среды разработки или игрового движка
Следуйте соответствующем руководству по началу работы для выбранной платформы или движка и получите основные сведения об Xbox Live в процессе работы.

* В разделе [Разработка игры по программе Creators с помощью Visual Studio](develop-creators-title-with-visual-studio.md) показано, как связать проект Visual Studio с конфигурацией Xbox Live в Центре разработки.
* В разделе [Разработка игры по программе Creators](develop-creators-title-with-unity.md) показано, как разработать игру Unity с поддержкой Xbox Live, обрабатывать однопользовательский и многопользовательский вход, добавлять компоненты, такие как списки лидеров и статистику, и создать проект Visual Studio.

Несмотря на то, что Unity является единственным сторонним игровым движком, для которого мы предоставляем документацию, игровые движки [Construct (2 и 3)](https://www.scirra.com/construct2) и [Game Maker Studio](https://www.yoyogames.com/gamemaker) также имеют документацию, которая поможет вам интегрировать Xbox Live в игру на движке Construct или Game Maker Studio.

* В статье [Game Maker Studio 2 UWP теперь поддерживает программу Xbox Live Creators Program](https://www.yoyogames.com/gamemaker/xblc) показано, как экспортировать проекты Game Maker Studio для игры на Xbox One и компьютере с Windows 10.
* В статье [Использование Xbox Live в приложениях UWP — Construct](https://www.scirra.com/tutorials/9540/using-xbox-live-in-uwp-apps) показано, как использовать Xbox Live в играх Construct 2 и 3.

Для других движков для разработки игр без документации по интеграции с Xbox Live по-прежнему можно использовать API-интерфейсы Xbox Live. Чтобы использовать API-интерфейсы Xbox Live из вашего проекта, можно добавить ссылки на двоичные файлы с помощью пакетов NuGet или добавить источник API. Добавление пакетов NuGet ускоряет компиляцию, а добавление источника упрощает отладку.

Для получения поддержки по использованию служб Xbox Live со сторонними игровыми движками, отличными от Unity, обратитесь к специалистам по соответствующим игровым движкам.

## <a name="3-xbox-live-concepts--testing"></a>3. Концепции Xbox Live и тестирование
После создания названия игры необходимо ознакомиться с концепциями Xbox Live, влияющими на процесс разработки игры. Также важно протестировать игру на всех платформах, которые она будет поддерживать, чтобы убедиться, что она работает должным образом.

- [Настройка служб Xbox Live для программы Xbox Live Creators Program](xbox-live-service-configuration-creators.md)
- [Тестовая среда Xbox Live](../xbox-live-sandboxes.md)
- [Авторизация учетных записей Xbox Live](authorize-xbox-live-accounts.md)

## <a name="4-enable-xbox-live-sign-in"></a>4. Включение входа в Xbox Live
Все игры по программе Xbox Live Creators Program должны интегрировать вход в систему Xbox Live и отображать удостоверение пользователя (тег игрока, картинку игрока, и т. д.). Вы можете автоматически выполнять вход пользователя в систему или добавить кнопку для его выполнения. После входа в систему должен отображаться тег игрока, чтобы проигрыватель мог убедиться в том, что он используют нужный профиль.

- [Социальная платформа Xbox Live — профили, друзья, присутствие](../social-platform/social-platform.md)

## <a name="5-add-optional-xbox-live-features"></a>5. Добавление дополнительных функций Xbox Live

Программа Xbox Live Creators Program предоставляет набор возможностей для продвижения игры и сохранения заинтересованности игроков.

- [Платформа данных Xbox Live — статистика, списки лидеров](../data-platform/data-platform.md) помогает поддерживать интерес к игре, позволяя игрокам конкурировать с друзьями и продвигаться вверх в списке лидеров.
- [Платформа хранилища Xbox Live — подключенное хранилище, хранилище игр](../storage-platform/storage-platform.md) обеспечивает бесплатный перенос сохраненных данных игр, чтобы игроки могли с легкостью сохранить прогресс в игре при переходе с Xbox One на компьютер с Windows и обратно.
- [Социальная платформа Xbox Live — профиль, друзья, присутствие](../social-platform/social-platform.md) позволяет игрокам общаться с друзьями и обсуждать вашу игру.

Важно отметить, что программа Xbox Live Creators Program не поддерживает многопользовательскую сетевую игру, достижения и игровой счет.

## <a name="6-release-your-game"></a>6. Выпуск игры

При использовании программы Xbox Live Creators Program процесс ничем не отличается от выпуска любого другого приложения UWP.

- [Политики Microsoft Store](https://msdn.microsoft.com/en-us/library/windows/apps/dn764944.aspx) включая [политики игр и Xbox](https://msdn.microsoft.com/en-us/library/windows/apps/dn764944.aspx#pol_10_13)
- [Публикация приложений для Windows](https://developer.microsoft.com/en-us/store/publish-apps)