---
title: Перенос Xbox Live кода из XDK в UWP
description: Узнайте, как перенести код Xbox Live на платформе Xbox Development Kit (XDK) для универсальной платформы Windows (UWP).
ms.assetid: 69939f95-44ad-4ffd-851f-59b0745907c8
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, xdk, перенос
ms.localizationpriority: medium
ms.openlocfilehash: c6e8a6ebe716f1e062940066184e9f734441371b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57590819"
---
# <a name="porting-xbox-live-code-from-the-xbox-developer-kit-xdk-to-universal-windows-platform-uwp"></a>Перенос Xbox Live кода из пакета средств разработки Xbox (XDK) для универсальной платформы Windows (UWP)

## <a name="introduction"></a>Введение

Эта статья предназначена для разработчиков, которые использовали один XDK Xbox для начала переноса кода Xbox Live на 10 универсальных Windows платформу Windows (UWP).

Частью этой миграции включает в себя переключение с XSAPI 1.0 (Xbox Live API служб, включенных в один XDK Xbox до августа 2015) 2.0 XSAPI (включена в один XDK Xbox, начиная с ноября 2015 г. и также доступны в пакете SDK для Xbox Live. Функциональные возможности этих API-интерфейсов практически идентичны, но существуют некоторые различия реализацией.

Другие разделы в этой статье рассматриваются включает подготовку компьютера разработчика Windows, и установка других API-интерфейсы обычно необходимо при использовании службы Xbox Live, например Secure Sockets API, а также подключение API службы хранилища для управления на основе облака сохраняет игры.

<a name="_Setting_up_and"></a>

## <a name="setting-up-and-configuring-your-project-in-partner-center-and-xdp"></a>Установка и настройка проекта в центре партнеров и XDP

Заголовок универсальной платформы Windows, который использует Xbox Live services необходимо настроить в [центра партнеров](https://partner.microsoft.com/dashboard). Последние сведения см. в разделе [Добавление Xbox Live в новый или существующий проект UWP](../get-started-with-partner/get-started-with-visual-studio-and-uwp.md) в Xbox Live руководства по программированию состав [Xbox Live SDK](https://developer.xboxlive.com/en-us/live/development/Pages/Downloads.aspx).

Эти действия по использованию службы Xbox Live в заголовок вашей статьи на этой странице:

-   Создайте проект приложения универсальной платформы Windows в центре партнеров.

-   Используйте XDP, чтобы настроить проект для использования на Xbox Live.

-   Свяжете ваш продукт центра партнеров к вашему продукту XDP.

-   Создание учетных записей разработчиков в XDP (требуется при запуске заголовок вашей Xbox Live в изолированную среду).

Если свои заголовки поддерживает многопользовательской игры, в шаблонах многопользовательской, может потребоваться некоторые дополнительные параметры. Все названия Windows 10, использующих Xbox Live многопользовательской и запись в MPSD (многопользовательской документ) требуют этого нового поля в списке «возможности» в шаблонах сеанса: ```userAuthorizationStyle: true```.

### <a name="enabling-cross-play"></a>Включение кросс play

Если будет поддерживать «кросс play» (общую Xbox Live конфигурацию между игры Xbox One и ПК, позволяя многопользовательские игры между устройствами), также необходимо будет добавить эту возможность в свои шаблоны сеанса: **crossPlay: true**.

Дополнительные сведения о поддержке в XDP кросс play и его требования к конфигурации см. в разделе «Прием XDK и UWP кросс-Play заголовки в XDP» в Xbox Live руководства по программированию.

Кроме того, некоторые программные рекомендации, см. в разделе Далее [поддержки многопользовательских кросс play между Xbox One и ПК](#_Supporting_multiplayer_cross-play).

## <a name="setting-up-your-windows-development-environment"></a>Настройка среды разработки Windows

1.  [Загрузите последнюю версию **Xbox Live SDK** ](https://developer.xboxlive.com/en-us/live/development/Pages/Downloads.aspx) и извлеките локально.

2.  [Установка **Live SDK расширения платформы Xbox** ](https://developer.xboxlive.com/en-us/live/development/Pages/Downloads.aspx) Если вам требуется Secure Sockets API и/или API сохраните игру (также называемые подключенных хранилище) для универсальной платформы Windows.

3.  Добавьте поддержку Xbox Live в проект приложения универсальной Windows в Visual Studio. Можно добавить либо полный исходный код или сослаться на двоичные файлы, установите пакет NuGet в проект Visual Studio. Пакеты будут доступны для C++ и WinRT. Для получения дополнительных сведений см. в разделе [Добавление Xbox Live в новый или существующий проект UWP](../get-started-with-partner/get-started-with-visual-studio-and-uwp.md)

4.  Настройка компьютера разработки для использования в изолированной среде. В каталоге средств пакета SDK Xbox Live, можно использовать из командной строки администратора есть сценарий командной строки (например: SwitchSandbox.cmd XDKS.1).

  **Примечание** переключиться обратно на розничной торговли "песочницы", можно либо удалить раздел реестра, который изменяет скрипт, или можно переключиться в «песочнице», называется розничной ТОРГОВЛИ.

1.  Добавьте учетную запись разработчика на свой компьютер разработки. Учетная запись разработчика, созданной в XDP необходим для взаимодействия со службами Xbox Live, во время выполнения при разработке в изолированную среду назначенных или выполнение образцов. Чтобы добавить один или несколько учетных записей в Windows:

    1.  Откройте **параметры** (ярлыка: Ключ Windows + I).

    2.  Откройте **учетные записи**.

    3.  На **учетной записи** щелкните **добавить учетную запись Майкрософт**.

    4.  Введите адрес электронной почты учетной записи разработчика и пароль.

### <a name="appxmanifest-changes"></a>Изменения AppxManifest

Ниже приведены наиболее распространенные виды изменений между версиями файл appxmanifest.xml Xbox и универсальной платформы Windows.

1. Удостоверение пакета имеет значение в универсальной платформы Windows, даже во время разработки. Имя удостоверения и издатель *должно соответствовать* определенному в центре партнеров для приложения универсальной платформы Windows.

1. Раздел зависимость пакета является обязательным. Например:

```xml
  <Dependencies>
    <TargetDeviceFamily Name="Windows.Universal" MinVersion="10.0.10240.0" MaxVersionTested="10.0.10240.0" />
  </Dependencies>
```

1.  См. другие разделы манифеста приложения, имеют особые требования к пример манифеста приложения универсальной платформы Windows (например, один из примеров на UWP, включенные в пакет SDK для Xbox Live или проект приложения универсальной Windows по умолчанию, созданные в Visual Studio) UWP, таких как ```<VisualElements>```.

1.  Заголовок и SCID определены в файле xboxservices.config (см. в разделе [разделу](#_Define_your_title)) вместо в категории «xbox.live» расширение.

1.  Категория расширения «xbox.system.resources» не требуется.

1.  Secure Sockets определены в файле networkmanifest.xml (см. в разделе [Secure sockets](#_Secure_sockets)) вместо в категории «windows.xbox.networking».

1.  Расширение категории «windows.protocol» должен быть определен для получения приглашений на Xbox Live в заголовок вашей универсальной платформы Windows (см. в разделе [отправка и получение приглашения](#_Sending_and_receiving)).

1.  Если вы используете GameChat API, нужно будет добавить возможность устройства "микрофон" внутри ```<Capabilities>``` элемент. Например:

  ```<DeviceCapability Name="microphone">```

<a name="_Define_your_title"></a>

### <a name="define-your-title-and-scid-for-the-xbox-live-sdk-in-a-config-file"></a>Определите заголовок и SCID для Xbox Live SDK в файле конфигурации

Xbox Live SDK необходимо указать название идентификатора и SCID, который больше не включается в appxmanifest.xml заголовков универсальной платформы Windows. Вместо этого создайте текстовый файл с именем **xboxservices.config** в проекте корневой каталог и добавьте следующие поля, заменив значения с данными для заголовка:

```
{
  "TitleId": 123456789,
  "PrimaryServiceConfigId": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee"
}
```

> [!NOTE]
> Все значения внутри xboxservices.config чувствительны к регистру.

Описан этот файл как содержимое в проекте, что он доступен в выходных данных построения.

**Примечание** эти значения будут доступны программным способом в заголовок вашей с помощью следующего API:

```cpp
Microsoft::Xbox::Services::XboxLiveAppConfiguration^ xblConfig = xblContext->AppConfig;
unsigned int titleId = xblConfig->TitleId;
Platform::String^ scid = xblConfig->ServiceConfigurationId;
```

### <a name="api-namespace-mapping"></a>Сопоставление пространств имен API

Таблица 1: Сопоставление пространств имен из XDK универсальной платформы Windows.

<table>
  <tr>
    <td></td>
    <td><b>Xbox One XDK</b></td><td><b>UWP</b></td>
    <td><b>API входит в состав...</b></td>
  </tr>
  <tr>
    <td>Службы Xbox API (XSAPI)</td>
    <td>Microsoft::Xbox::Services</td>
    <td>Microsoft::Xbox::Services (<i>без изменений</i>)</td>
    <td>Xbox Live SDK (с помощью NuGet двоичный или источника)</td>
  </tr>
  <tr>
    <td>GameChat</td>
    <td>
Microsoft::Xbox::GameChat Windows::Xbox::Chat </td>
    <td>
Microsoft::Xbox::GameChat (*без изменений*) Microsoft::Xbox::ChatAudio </td>
    <td>
Xbox Live SDK (используйте двоичные NuGet) </td>
  </tr>
  <tr>
    <td>SecureSockets</td>
    <td>Windows::Xbox::Networking</td>
    <td>Windows::Networking::XboxLive</td>
    <td>Xbox Live SDK расширения</td>
  </tr>
  <tr>
    <td>Подключенное хранилище</td>
    <td>Windows::Xbox::Storage</td>
    <td>Windows::Gaming::XboxLive::Storage</td>
    <td>Xbox Live SDK расширения</td>
  </tr>
</table>

### <a name="multiplayer-subscriptions-and-event-handling"></a>Многопользовательские подписок и обработка событий

Критические изменения XSAPI 1.0 до 2.0 XSAPI, столкнутся с наиболее многопользовательские заголовки, он перемещения несколько методов и событий из **RealTimeActivityService** для **MultiplayerService**.

Например:

-   **EnableMultiplayerSubscriptions()\***  метод

-   **DisableMultiplayerSubscriptions()** метод

-   **MultiplayerSessionChanged** событий

-   **MultiplayerSubscriptionLost** событий

-   **MultiplayerSubscriptionsEnabled** свойство

**Примечание реализацией** несмотря на то, что возможно, вы не используете явно другими компонентами в **RealTimeActivityService** после перемещения эти события и методы для **MultiplayerService**, по-прежнему необходимо вызвать **xblContext -&gt;RealTimeActivityService -&gt;Activate()** перед вызовом **EnableMultiplayerSubscriptions()** так как служба возврата требуется многопользовательские подписок.

## <a name="whats-handled-differently-in-uwp"></a>Что обрабатывается по-разному в UWP

Ниже приведен список очень высокого уровня части кода, скорее всего будет различия между XDK и UWP, описанной в новом [NetRumble пример](https://developer.xboxlive.com/en-us/platform/development/education/Pages/Samples.aspx) (который содержит версии XDK и универсальной платформы Windows):

-   Доступ к идентификатор и SCID данных заголовка

-   Предварительный запуск активации (новые возможности для универсальной платформы Windows)

-   Остановка и возобновление обработки PLM

-   (Новые возможности для универсальной платформы Windows) расширенного выполнения

-   Xbox **пользователя** объекта и различия в обработке пользователя

    -   Обработка входа в систему и выхода

    -   Контроллер, связывание (только для обработки на Xbox)

    -   Обработка Gamepad

-   Проверка многопользовательские привилегий

-   Поддержка многопользовательские кросс play между Xbox One и ПК

-   Отправка приглашения игр

    -   Возможность открывать приложение из игры - н/д на UWP

    -   Возможность перечисления сторона членов из игры - н/д на UWP

-   Отображение игровой профиль

-   Secure Socket API поверхности изменения

-   Инициации измерения качества обслуживания и обработка результатов

-   Запись события игры

-   GameChat: события, параметры и объект ChatUser

-   Подключенные поверхности изменения API службы хранилища

-   События PIX (только на Xbox; не рассматривается в данном документе)

-   Некоторые различия отрисовки

Следующие разделы подробно дальнейшей на многие из этих отличий.

### <a name="accessing-title-id-and-scid-info"></a>Доступ к идентификатор и SCID данных заголовка

В универсальной платформы Windows, свой идентификатор title и идентификатор конфигурации службы осуществляется через свойство AppConfig экземпляра **XboxLiveContext**.

```cpp
Microsoft::Xbox::Services::XboxLiveAppConfiguration^ xblConfig = xblContext->AppConfig;
unsigned int titleId = xblConfig->TitleId;
Platform::String^ scid = xblConfig->ServiceConfigurationId;
```

**Примечание** в XDK идентификаторы можно получить их с помощью этих новых свойств или старый статические свойства в **Windows::Xbox::Services::XboxLiveConfiguration**.

### <a name="prelaunch-activation"></a>Предварительный запуск активации

Часто используемые названия в Windows 10 могут prelaunched при входе пользователя. Для обработки этой ситуации название должны иметь код, который проверяет аргументы запуска для **PreLaunchActivated**. К примеру возможно, необходимо загрузить все ресурсы во время активации. Дополнительные сведения см. в статье MSDN [предварительный запуск приложения дескриптор](https://msdn.microsoft.com/library/windows/apps/mt593297.ASPx).

### <a name="suspendresume-plm-handling"></a>Остановка и возобновление обработки PLM

Приостановка и возобновление и PLM в целом, работают точно так же в приложении универсальной Windows так, как они работают на Xbox One; Тем не менее существует несколько важных различий, которые следует учитывать:

-   Существует не **Constrained** состояния на ПК — это концепция, эксклюзивные Xbox One.

-   Приостановка начинается немедленно в том случае, когда заголовок свернут; способ обойти это, см. в разделе [расширенных выполнения](#_Extended_execution).

-   Время отличается: у вас есть 5 секунд для приостановки на ПК вместо 1 секунды на консоли.

Другим важным аспектом при подключенном хранилище появился **ContainersChangedSinceLastSync** свойство в универсальной платформы Windows, версия этого API. При обработке события resume можно проверить это свойство на предмет изменений все контейнеры в облаке во время приостановки заголовка. Это может произойти, если игрок приостановлено игры на одном Компьютере, воспроизводить в другом месте и затем возвращается к первому компьютеру. Если считать данные из этих контейнеров в памяти прежде, чем приостановлено, может потребоваться считывать их снова, чтобы увидеть, что изменен и обрабатывать их соответствующим образом.

Дополнительные сведения об обработке PLM в приложении универсальной платформы Windows в Windows 10 см. в статье MSDN [Launching, resuming и фоновых задач](https://msdn.microsoft.com/library/windows/apps/xaml/mt227652.aspx).

Можно также найти [PLM для Xbox One](https://developer.xboxlive.com/en-us/platform/development/education/Documents/PLM%20for%20Xbox%20One.aspx) Технический документ по GDN полезно, так как он был записан с игры в виду, и большинство принципов обработки жизненном цикле приложений по-прежнему действуют на ПК.

<a name="_Extended_execution"></a>

### <a name="extended-execution"></a>Расширенного выполнения

Минимизация UWP приложений на Компьютере обычно приводит он немедленно начинает приостановки. С помощью расширенного выполнения, у вас есть возможность отложить этот процесс. Пример реализации:

```cpp
using namespace Windows::ApplicationModel::ExtendedExecution;
//If this goes out of scope the request is nullified
ExtendedExecutionSession^ session;
void App::RequestExtension()
{
       if (!session)
       {
              session = ref new ExtendedExecutionSession();
       }
       session->Reason = ExtendedExecutionReason::Unspecified;
       session->Description = "foo";
       session->Revoked += ref new TypedEventHandler<Platform::Object^, ExtendedExecutionRevokedEventArgs^>(this, &App::ExtensionRevokedHandler);
       IAsyncOperation<ExtendedExecutionResult>^ request = session->RequestExtensionAsync();
       //At this point the request has been made. When the IAsyncOperation request completes, verify that the ExtendedExecutionResult == Allowed and you will not suspend for up to 10 minutes while minimized.
}

void App::ExtensionRevokedHandler(Platform::Object^ obj, ExtendedExecutionRevokedEventArgs^ args)
{
       if (args->Reason == Windows::ApplicationModel::ExtendedExecutionRevokedReason::Resumed)
       {
              //Request the extension again in preparation for the next suspend.
RequestExtension();
       }
       //The app will either complete suspending if the extension was revoked by system policy or resume running if the user has switched back to the app.
}

```

После **ExtensionRevokedHandler** был вызван, новое расширение должен запрашиваться для будущих потенциальных приостановки. **ExtensionRevokedHandler** вызывается, когда имеется достаточный объем памяти в системе, прошедших 10 минут или пользователь переключится обратно в игру на время игра находящегося в свернутом состоянии. Поэтому **RequestExtension()** должен быть вызван, скорее всего, в таких случаях:

-   Во время запуска.

-   В **ExtensionRevokedHandler** при args -&gt;причина == возобновлена (пользователя, с вкладками при игре был свернут до истечения срока действия таймера 10 минут).

-   В **OnResuming** обработчик (если название было приостановлено из-за нехватки памяти или таймера 10 минут).

### <a name="handling-users-and-controllers"></a>Обработка пользователей и контроллеры

В Windows вы работаете с одного вошедшего пользователя за раз. В Xbox Live SDK, сначала создается **XboxLiveUser** объекта, входа в Xbox Live и затем создать **XboxLiveContext** объекты из этого пользователя.

Ранее на один XDK Xbox:

1.  Получить пользователя (из игровой взаимодействия, например).
2.  Создание **XboxLiveContext** этого пользователя:
  ```
  ref new Microsoft::Xbox::Services::XboxLiveContext( Windows::Xbox::System::User^ user )
  ```
1.  Обрабатывать **SignOut** событий:
  ```
  Windows::Xbox::System::User::SignOutStarted
  ```
1.  Обрабатывать связывания игровой/контроллера с помощью:
  ```
  Windows::Xbox::Input::Controller::ControllerRemoved
  Windows::Xbox::Input::Controller::ControllerPairingChanged
  ```

Теперь для универсальной платформы Windows и Xbox Live SDK:

1.  Создание **XboxLiveUser**:

  ```
  auto xblUser = ref new Microsoft::Xbox::Services::System::XboxLiveUser();
  ```

1.  Попробуйте войти их, используя последнюю учетную запись Майкрософт, их использовать, без обращения к специалистам их с помощью пользовательского интерфейса:

  ```
  xblUser->SignInSilentlyAsync();
  ```

1.  Если вы получаете **SignInResult::Success** в результате из этой асинхронной операции создания **XboxLiveContext**, и затем закончите:

  ```
  auto xblContext = ref new Microsoft::Xbox::Services::XboxLiveContext( xblUser );
  ```

1.  Если вместо этого вы получаете **SignInResult::UserInteractionRequired**, необходимо вызвать интерактивный вход в метод, который можно открыть пользовательский Интерфейс системы:

  ```
  xblUser->SignInAsync();
  ```

1.  Здесь вы можете получить **SignInResult::UserCancel**, в этом случае у вас нет пользователя вошедшего в систему, и следует искать пункт меню, чтобы выполнить вход еще раз.

  **Примечание** при предоставлении пункты меню, рекомендуется предоставить им возможность переключаться на другую учетную запись Майкрософт:

  ```
  xblUser->SwitchAccountAsync( nullptr );
  ```

1.  После вошедшего в систему пользователя, вы можете подключить **XboxLiveUser::SignOutCompleted** событий, чтобы реагировать на завершение сеанса пользователя:

  ```
  xblUser->SignOutCompleted += ref new Windows::Foundation::EventHandler<Microsoft::Xbox::Services::System::SignOutCompletedEventArgs^>( &OnSignOutCompleted );
  ```

1.  Нет ни одного контроллера, связывание для обработки в Windows 10.

Это упрощенный пример для C++ / WinRT. Более подробный пример см. в разделе «Xbox Live проверка подлинности в Windows 10» в Xbox Live руководства по программированию. Можно также найти в «Добавление Xbox Live в новый проект универсальной платформы Windows» широкой пример полезными.

### <a name="checking-multiplayer-privileges"></a>Проверка многопользовательские привилегий

Эквивалентно **CheckPrivilegeAsync()** еще не доступен в пакете SDK для Xbox Live. Сейчас, вам потребуется сначала найти для прав доступа, необходимо в списке строк, возвращенных **привилегии** свойство для **XboxLiveUser**. Например чтобы узнать о многопользовательской привилегии, найдите привилегий «254.» Работа с документацией по XDK, можно найти список все привилегии Xbox Live в **Windows::Xbox::ApplicationModel::Store::KnownPrivileges** перечисления.

Дополнительные сведения по этой теме, см. в сообщении форума [xsapi & пользователя права](https://forums.xboxlive.com/questions/48513/xsapi-user-privileges.html).

<a name="_Supporting_multiplayer_cross-play"></a>

### <a name="supporting-multiplayer-cross-play-between-xbox-one-and-pc-uwp"></a>Поддержка многопользовательские кросс play между Xbox One и UWP ПК

В дополнение к требованиям шаблон нового сеанса в XDP (см. в разделе [Установка и настройка проекта в центре партнеров и XDP](#_Setting_up_and)), кросс play поставляется с новые ограничения на способность соединения сеанса. «None» как ограничение соединения сеанса больше не используется. Необходимо использовать «Оформлена» или «Local» (ограничение по умолчанию является «Local»).

Кроме того, соединения и чтения ограничения по умолчанию «Local» из-за необходимая **userAuthorizationStyle** возможность для многопользовательскую Windows 10.

В этой статье форум [имеется возможность создания общей многопользовательской](https://forums.xboxlive.com/questions/46781/is-it-possible-to-create-public-multiplayer-sessio.html), содержит дополнительные сведения.

Дополнительные сведения и примеры можно найти в обновленный многопользовательские разработчика блок-схем, поддержкой кросс play многопользовательские образец NetRumble, или из диспетчера разработчика учетной записи (DAM).

<a name="_Sending_and_receiving"></a>

### <a name="sending-and-receiving-invites"></a>Отправка и получение приглашения

Теперь является API-интерфейса, вызвать его пользовательский Интерфейс для отправки приглашения **Microsoft:: Xbox::Services::System::TitleCallableUI::ShowGameInviteUIAsync()**. Передайте в сеансе -&gt; **SessionReference** объекта во время действия сеанса (обычно вашей основной). При необходимости можно передать в ссылки на пользовательский пригласить строковый идентификатор, который определен в конфигурации службы в XDP второго параметра. Строка вами там будут отображаться в всплывающее уведомление, отправленное приглашенные участники проекта. Обратите внимание на то, что новые, передаются как параметр этого метода — номер идентификатора, и он должен быть отформатирован правильно для службы. Например, идентификатор строки «1» должен быть передан в качестве «/ / / 1".

Если вы хотите отправлять приглашения непосредственно с помощью многопользовательские службы (то есть без отображения пользовательского интерфейса), по-прежнему можно использовать другой метод приглашения, **Microsoft:: Xbox::Services::Multiplayer::MultiplayerService::SendInvitesAsync()** от пользователя **XboxLiveContext**.

Чтобы разрешить приглашения, поступающих на Windows для активации название протокола, необходимо добавить это расширение, чтобы **&lt;приложения&gt;** элемент appxmanifest:

```xml
<Extensions>
  <uap:Extension Category="windows.protocol">
    <uap:Protocol Name="ms-xbl-multiplayer" />
  </uap:Extension>
</Extensions>
```

Затем можно обработать сообщение invite, что и на Xbox One при вашей **CoreApplication** получает **Activated** событий и активации, тип — **ActivationKind::Protocol**.

### <a name="showing-the-gamer-profile-card"></a>Отображение игроков Карточка профиля

Всплывает игроков Карточка профиля на UWP, использовать **Microsoft:: Xbox::Services::System::TitleCallableUI::ShowProfileCardUIAsync()**, передавая XUID для целевого пользователя.

<a name="_Secure_sockets"></a>

### <a name="secure-sockets"></a>Безопасные сокеты

Интерфейс API Secure Socket включается в отдельной [Live SDK расширения платформы Xbox](https://developer.xboxlive.com/en-us/live/development/Pages/Downloads.aspx).

См. в разделе этого форума для использования API: [Настройка SecureDeviceAssociation для различных платформах](https://forums.xboxlive.com/answers/45722/view.html).

**Примечание** для UWP **SocketDescriptions** раздела были перемещены из appxmanifest и поместить в собственную [networkmanifest.xml](https://forums.xboxlive.com/storage/attachments/410-networkmanifestxml.txt). Формат внутри &lt;SocketDescriptions&gt; элемент, практически не отличается, только без **mx:** префикс.

Кросс play между Xbox и Windows 10, быть *действительно* , все, что определенный *одинаково* между двумя разными видами манифеста (Package.appxmanifest для Xbox One и networkmanifest.xml для Windows 10). Должно соответствовать имя сокетов, протокола и др. *точно*.

Также для кросс play, будет необходимо определить следующие четыре варианты использования SDA внутри ```<AllowedUsages>``` элемент в *оба* Package.appxmanifest один Xbox и Windows 10 networkmanifest.xml:

```xml
<SecureDeviceAssociationUsage Type="InitiateFromMicrosoftConsole" />
<SecureDeviceAssociationUsage Type="AcceptOnMicrosoftConsole" />
<SecureDeviceAssociationUsage Type="InitiateFromWindowsDesktop" />
<SecureDeviceAssociationUsage Type="AcceptOnWindowsDesktop" />
```

### <a name="multiplayer-qos-measurements"></a>QoS многопользовательскую измерения

Помимо изменения пространства имен в Secure Sockets API некоторые имена объектов и значения также изменены. Сопоставление для измерения обычно используется состояние находится в следующей таблице.

Таблица 2. Как правило, используется сопоставление состояния измерения.

| XDK (Windows::Xbox::Networking::QualityOfServiceMeasurementStatus)  | UWP (Windows::Networking::XboxLive::XboxLiveQualityOfServiceMeasurementStatus)  |
|------------------------------------|--------------------------------------------|
| HostUnreachable                    | NoCompatibleNetworkPaths                   |
| MeasurementTimedOut                | TimedOut                                   |
| PartialResults                     | InProgressWithProvisionalResults           |
| Успешно                            | Успешно                                  |

Шаги в *измерение* качества обслуживания (QoS) и *обработки результатов* одинаковы в принципе при сравнении версий XDK и универсальной платформы Windows API-интерфейса. Тем не менее из-за изменения имен и несколько изменениями структуры, результирующий код выглядит по-разному в некоторых местах.

Для измерения качества обслуживания для **XDK**, вы создали коллекцию адресов устройство обеспечения безопасности и коллекции метрик и передать их в **MeasureQualityOfServiceAsync()** метод.

Для измерения качества обслуживания для **UWP**, вы создаете новый **XboxLiveQualityOfServiceMeasurement()** , вызовите **функцией Append()** для его **метрики** и **DeviceAddresses** свойства, а затем вызовите метод объекта на **MeasureAsync()** метод.

Например:

```cpp
auto qosMeasurement = ref new Windows::Networking::XboxLive::XboxLiveQualityOfServiceMeasurement();
qosMeasurement->Metrics->Append(Windows::Networking::XboxLive::XboxLiveQualityOfServiceMetric::AverageInboundBitsPerSecond);
qosMeasurement->Metrics->Append(Windows::Networking::XboxLive::XboxLiveQualityOfServiceMetric::AverageOutboundBitsPerSecond);
qosMeasurement->Metrics->Append(Windows::Networking::XboxLive::XboxLiveQualityOfServiceMetric::AverageLatencyInMilliseconds);
qosMeasurement->NumberOfProbesToAttempt = myDefaultQosProbeCount;
qosMeasurement->TimeoutInMilliseconds = myDefaultQosMeasurementTimeout;

// Add secure addresses for each session member
for (const auto& member : session->GetMembers())
{
    if (!member->IsCurrentUser)
    {
        auto sda = member->SecureDeviceAddressBase64;

        if (!sda->IsEmpty())
        {
qosMeasurement->DeviceAddresses->Append(Windows::Networking::XboxLive::XboxLiveDeviceAddress::CreateFromSnapshotBase64(sda));
        }
    }
}

if (qosMeasurement->DeviceAddresses->Size > 0)
{
    qosMeasurement->MeasureAsync();
}

```

Дополнительные примеры см. в разделе **MatchmakingSession::MeasureQualityOfService()** и **MatchmakingSession::ProcessQosMeasurements()** функции в образце NetRumble.

### <a name="writing-game-events"></a>Запись события игры

Отправка события игры, настроенные в заголовок вашей конфигурации службы имеет другой API универсальной платформы Windows. Пакет SDK для Xbox Live использует **EventsService** и модель контейнер свойства.

Например:

```cpp
auto properties = ref new Windows::Foundation::Collections::PropertySet();
properties->Insert("RoundId", m_roundId);
properties->Insert("SectionId", safe_cast<Platform::Object^>(0));
properties->Insert("MultiplayerCorrelationId", m_multiplayerCorrelationId);
properties->Insert("GameplayModeId", safe_cast<Platform::Object^>(0));
properties->Insert("MatchTypeId", safe_cast<Platform::Object^>(0));
properties->Insert("DifficultyLevelId", safe_cast<Platform::Object^>(0));

auto measurements = ref new Windows::Foundation::Collections::PropertySet();

xblContext->EventsService->WriteInGameEvent("MultiplayerRoundStart", properties, measurements);

```

Дополнительные сведения см. в документации Xbox Live SDK.

**Совет** можно использовать **xcetool.exe** состав Xbox Live SDK (расположен в каталоге Tools) для преобразования файла events.man, загруженный из XDP в h-файл заголовка. Используйте "-x" параметр, чтобы создать этот заголовок C++ с помощью новой схемы v2 свойства контейнера. Этот заголовок содержит функции C++, которые можно вызвать для всех настроенных событий; например **EventWriteMultiplayerRoundStart()**. Если вы предпочитаете использовать интерфейс WinRT, могут также ссылаться на этот файл заголовка, чтобы увидеть, как построены свойств и значений для каждого события.

### <a name="game-chat"></a>Чат, игры

GameChat в универсальной платформы Windows входит в состав пакета SDK для Xbox Live как двоичный пакет NuGet. Инструкции см. в Xbox Live руководства по программированию по добавлению пакета NuGet в проект.

Использование базового приложения практически идентичен между XDK и версии универсальной платформы Windows. Включают несколько отличий в API:

1.  **User::AudioDeviceAdded** событий не нужно подключать наименования универсальной платформы Windows. В базовое устройство обрабатывает чата библиотека добавляет и удаляет.

2.  **ChatUser** теперь называется **GameChatUser**.

3.  **Microsoft::Xbox::GameChat** пространство имен остается тем же, но **Windows::Xbox::Chat** становится пространством имен **Microsoft::Xbox::ChatAudio**.

4.  **AddLocalUserToChatChannelAsync()** принимает либо XUID или в **ChatAudio::IChatUser ^** вместо **XboxUser**.

5.  **RemoveLocalUserFromChatChannelAsync()** требует **ChatAudio::IChatUser ^** вместо **XboxUser**. Вы можете получить **IChatUser** из **GameChatUser**-&gt;**пользователя**.

### <a name="connected-storage"></a>Подключенные устройства хранения

Подключение API службы хранилища предоставляется в отдельной [Live SDK расширения платформы Xbox](https://developer.xboxlive.com/en-us/live/development/Pages/Downloads.aspx). Документация включена в документации по Xbox Live SDK.

Общий поток выглядит так же, как и на Xbox One, с добавлением **ContainersChangedSinceLastSync** свойство в версии универсальной платформы Windows. Это свойство должно проверяться при название обрабатывает событие резюме, после вызова метода **GetForUserAsync()** еще раз, чтобы увидеть измененные контейнеры в облаке при название было приостановлено. Если у вас есть данные, загруженные в память из одного из контейнеров, которые изменены, может потребоваться чтение данных снова, чтобы увидеть, что изменен и обрабатывать их соответствующим образом.

В версии универсальной платформы Windows другие важные отличия.

1.  Изменено пространство имен из **Windows::Xbox::Storage** для **Windows::Gaming::XboxLive::Storage**.

2.  **ConnectedStorageSpace** переименовывается **GameSaveProvider**.

3.  **Windows::System::User** используется в **GetForUserAsync()** вместо **XboxUser**, и SCID теперь является обязательным.

4.  Не локальное хранилище «machine» (то есть **GetForMachineAsync()** был удален). Рассмотрите возможность использования **Windows::Storage::ApplicationData** вместо этого для вашего неперемещающегося, локального сохранения данных.

5.  Результаты Async безо всяких исключение \*объект тип результата (например, **GameSaveProviderGetResult**); это можно проверить из **состояние** свойство, и если ошибки отсутствуют, чтение из возвращенного объекта **значение** свойства.

6.  **Перечисление ConnectedStorageErrorStatus** переименовывается **GameSaveErrorStatus** и возвращается в **состояние** свойство результата. Все старые значения существуют, и были добавлены несколько новых:

-   Abort

-   ObjectExpired

-   ОК

-   UserHasNoXboxLiveInfo

См. пример GameSave или пример NetRumble пример использования.

**Примечание** Gamesaveutil.exe является эквивалентом xbstorage.exe (состав XDK служебная программа командной строки разработчика). После установки пакета SDK расширения платформы Live Xbox, эту программу можно найти здесь: C:\\Program Files (x86)\\комплекты Windows\\10\\пакетов SDK расширений\\XboxLive\\1.0\\Bin\\x64

## <a name="summary"></a>Сводка

Изменения в API и новые требования, описанные в настоящем документе, которые чаще всего возникают при переносе существующего кода игры из одного XDK Xbox на новый uwp. Определенного внимания было уделено приложения и настройки среды, а также функциональные области, относящиеся к службам Xbox Live, например многопользовательской и подключенного хранилища. Ссылкам в этой статье и в следующие ссылки на дополнительные сведения ниже и не забудьте посетить в разделе «Windows 10» [форумы для разработчиков](https://forums.xboxlive.com) дополнительные справки, ответы и новости.

## <a name="references"></a>Ссылок

-   [Перенос приложений из Xbox One в Windows 10](https://developer.xboxlive.com/en-us/platform/development/education/Documents/Porting%20from%20Xbox%20One%20to%20Windows%2010.aspx)

-   [Технические документы для Xbox One](https://developer.xboxlive.com/en-us/platform/development/education/Pages/WhitePapers.aspx)

-   [Примеры](https://developer.xboxlive.com/en-us/platform/development/education/Pages/Samples.aspx)
