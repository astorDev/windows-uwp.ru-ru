---
title: Проверка подлинности для проектов универсальной платформы Windows
author: aablackm
description: Узнайте, как выполнять вход пользователей Xbox Live в заголовке универсальной платформы Windows (UWP).
ms.assetid: e54c98ce-e049-4189-a50d-bb1cb319697c
ms.author: aablackm
ms.date: 03/19/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, проверки подлинности и входа в систему
ms.localizationpriority: medium
ms.openlocfilehash: 5473b7ede7731d7d07b7e5bfd72857fdb64f1c89
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57628389"
---
# <a name="authentication-for-uwp-projects"></a>Проверка подлинности для проектов универсальной платформы Windows

Чтобы воспользоваться преимуществами возможностей Xbox Live в играх, пользователю нужно создать профиль Xbox Live, чтобы идентифицировать себя в сообществе Xbox Live.  Службы Xbox Live всем игра связанные действия с использованием этого профиля, Xbox Live, например: тег игрока и игроков рисунка, кто друзей пользователя игры пользователя новые игры пользователя сыграла, какие достижений, разблокировать пользователя, где пользователь ставится на Список лидеров для конкретной игры, и т.д.

Если пользователь хочет получить доступ к службам Xbox Live в каждой конкретной игры на конкретном устройстве, необходимые пользователю для проверки подлинности сначала.  Игру можно вызвать API Xbox Live, чтобы инициировать процесс проверки подлинности.  В некоторых случаях пользователь Откроется интерфейс для предоставления дополнительных сведений, например ввода имени пользователя и пароль учетной записи Майкрософт для использования, даете согласие на разрешения для игры, решение проблем учетной записи, принять новые условия использования, д.

После проверки подлинности пользователя, связанный с этого устройства, пока они явно выйдите Xbox Live из приложения Xbox.  Только один проигрыватель может пройти проверку подлинности на устройстве без консоли за раз (для всех Xbox Live игры);  для нового игрока должны проходить проверку подлинности на устройстве не из консоли существующие проигрыватель прошедшего проверку подлинности необходимо подписать предварительного извлечения.

## <a name="steps-to-sign-in"></a>Действия для входа в систему

На высоком уровне используйте API-интерфейсы Xbox Live, выполнив следующие действия:

1. Создать объект XboxLiveUser для представления пользователя
2. Войдите в автоматическом режиме Xbox Live во время запуска
3. Попытка входа в систему с помощью пользовательского интерфейса при необходимости
4. Создание контекста службы Xbox Live на основе взаимодействующих пользователя
5. Используйте контекст Xbox Live для доступа к службам Xbox Live
6. Когда игра завершает работу или пользователь знаки out, выпуск XboxLiveUser объект и объект XboxLiveContext задавая для них значение null

### <a name="creating-an-xboxliveuser-object"></a>Создание объекта XboxLiveUser

Большая часть действий Xbox Live, связаны с пользователем Xbox Live.  Как разработчик игр необходимо сначала создать объект XboxLiveUser для представления локального пользователя.

C++:

```cpp
auto xboxUser = std::make_shared<xbox_live_user>(Windows::System::User^ windowsSystemUser);
```

C + +/ CX (WinRT):

```cpp
XboxLiveUser xboxUser = ref new XboxLiveUser(Windows::System::User^ windowsSystemUser);
```

C#(WinRT):

```csharp
XboxLiveUser xboxUser = new XboxLiveUser(Windows.System.User windowsSystemUser);
```

* **windowsSystemUser** windows system объекта-пользователя используются для связывания с пользователем, xbox live. Может быть nullptr, если приложение является application(SUA) одного пользователя.
  * Дополнительные сведения об одном Application(SUA) пользователя и Application(MUA) пользователя с несколькими проверьте [введение в многопользовательских приложений](https://docs.microsoft.com/en-us/windows/uwp/xbox-apps/multi-user-applications#single-user-applications)
  * Дополнительные сведения о том, как получить Windows::System::User ^ из Windows, проверьте [получение пользователя системы windows на UWP](retrieving-windows-system-user-on-UWP.md)

### <a name="sign-in-silently-to-xbox-live-at-startup"></a>Войдите в автоматическом режиме Xbox Live во время запуска ###

Игра должна выполнять проверки подлинности пользователя для Xbox Live как можно раньше после запуска, даже до представить пользовательский интерфейс, выполнять упреждающую выборку данных из службы Xbox Live.

Для проверки подлинности локального пользователя без уведомления, вызывать

C++:

```cpp
pplx::task<xbox_live_result<sign_in_result>> xbox_live_user::signin_silently(Platform::Object^ coreDispatcher)
```

C + +/ CX (WinRT):

```cpp
Windows::Foundation::IAsyncOperation<SignInResult^>^ XboxLiveUser::SignInSilentlyAsync(Platform::Object^ coreDispatcher)
```

C#(WinRT):

```csharp
Microsoft.Xbox.Services.System.SignInResult XboxLiveUser.SignInSilentlyAsync(Windows.UI.Core.CoreDispatcher coreDispatcher);
```

* **coreDispatcher**

  Поток диспетчера используется для обмена данными между потоками. Несмотря на то, что API автоматический вход не будет отображать интерфейс пользователя, XSAPI по-прежнему должен dispatcher потока пользовательского интерфейса для получения сведений о вашей appx языкового стандарта. Вы можете получить статический dispatcher потока пользовательского интерфейса путем вызова Windows::UI::Core::CoreWindow::GetForCurrentThread() "->" Диспетчер в потоке пользовательского интерфейса. Если вы не уверены, что этот API вызывается в потоке пользовательского интерфейса, можно также передать в nullptr (например, на JS UWA).


Существует 3 возможных результата с автоматической попытки входа в систему

* **Успех**

  Если устройство подключено к сети, это означает, что пользователь успешной проверки подлинности для Xbox Live, и мы смогли получить действительный маркер.

  Если устройство находится в автономном режиме, это означает, что пользователь ранее успешной проверки подлинности для Xbox Live и не имеет явно автоматический ухода из этого заголовка.  Примечание в этом случае нет никакой гарантии, что заголовок должен иметь доступ ко допустимый маркер, только гарантируется, что удостоверение пользователя известен и проверки.    Заголовок с помощью идентификатора пользователя xbox (xuid) и тег игрока известно удостоверение пользователя.

* **UserInteractionRequired**

  Это означает, что среда выполнения не удалось выполнить вход пользователя без уведомления.  Игра должна вызывать `xbox_live_user::sign_in` которого вызывает поставщик удостоверений Xbox, чтобы показать поток необходимые UX для пользователя для входа-регистрации или входа в систему.  Распространенным проблемам относятся:

  * Пользователь не имеет учетной записи Майкрософт
  * Пользователь не задал основной учетной записи Майкрософт для игр
  * Выбранная учетная запись Майкрософт не имеет профиля Xbox Live
  * Пользователь должен принять разрешения учетной записи Майкрософт

* **Другие ошибки**

  Среда выполнения не удалось выполнить вход из-за других причин.  Обычно эти проблемы, не практические игры или пользователем. При использовании c ++ API, необходимо проверить ошибки, проверив xbox_live_result <> .err(); на WinRT, потребовалось бы перехватывать Platform::Exception ^.

### <a name="attempt-to-sign-in-with-ux-if-required"></a>Попытка входа в систему с помощью пользовательского интерфейса при необходимости ###

Игры должен проверить подлинность пользователя для Xbox Live с UX, доступна, если не удалось выполнить автоматический вход, и вы готовы представить пользовательский интерфейс.

Чтобы проверить подлинность пользователя с UX, вызовите

C++:

```cpp
pplx::task<xbox_live_result<sign_in_result>> xbox_live_user::signin(Platform::Object^ coreDispatcher)
```


C + +/ CX (WinRT):

```cpp
Windows::Foundation::IAsyncOperation<SignInResult^>^ XboxLiveUser::SignInAsync(Platform::Object^ coreDispatcher)
```

C#(WinRT):

```csharp
Microsoft.Xbox.Services.System.SignInResult  XboxLiveUser.SignInAsync(Windows.UI.Core.CoreDispatcher coreDispatcher);
```

* **coreDispatcher**

  Поток диспетчера используется для обмена данными между потоками. Входа в API требует пользовательского интерфейса диспетчера, чтобы его можно отобразить знак в пользовательском Интерфейсе и получения информации о вашей appx языкового стандарта. Вы можете получить статический dispatcher потока пользовательского интерфейса путем вызова Windows::UI::Core::CoreWindow::GetForCurrentThread() "->" Диспетчер в потоке пользовательского интерфейса. Если вы не уверены, что этот API вызывается в потоке пользовательского интерфейса, можно также передать в nullptr (например, на JS UWA).

Существует 3 возможные результаты после попытки входа в систему с помощью пользовательского интерфейса.

* **Успех**

  Если устройство подключено к сети, это означает, что пользователь успешной проверки подлинности для Xbox Live, и мы смогли получить действительный маркер.

  Если устройство находится в автономном режиме, это означает, что пользователь ранее успешной проверки подлинности для Xbox Live и не имеет явно автоматический ухода из этого заголовка.  Примечание в этом случае нет никакой гарантии, что заголовок должен иметь доступ ко допустимый маркер, только гарантируется, что удостоверение пользователя известен и проверки.    Удостоверение пользователя известен идентификатор пользователя xbox title (xuid) и его тег игрока.

* **UserCancel**

  Это означает, что пользователь отменил операцию входа в систему до завершения.  В этом случае игры не следует повторять автоматически войдите, используя пользовательский интерфейс.  Вместо этого оно должно представлять UX в игре, пользователь может повторить операцию входа.  (Например, кнопки входа)

* **Другие ошибки**

  Среда выполнения не удалось выполнить вход из-за других причин.  Обычно эти проблемы, не практические игры или пользователем. При использовании c ++ API, необходимо проверить ошибки, проверив xbox_live_result <> .err(); на WinRT, потребовалось бы перехватывать Platform::Exception ^.

## <a name="sign-in-code-examples"></a>Примеры кода входа

### <a name="c"></a>C++

```cpp

#include "xsapi\services.h" // contains the xbox::services::system namespace

using namespace xbox::services::system; // contains definitions necessary for sign-in

void SignInSample::SignIn()
{
    //1. Create an xbox_live_user object
    m_user = std::make_shared<xbox::services::system::xbox_live_user>(); // m_user declared in header file

    //2. Sign-In silently to Xbox Live at startup
    m_user->signin_silently()
        .then([this](xbox::services::xbox_live_result<xbox::services::system::sign_in_result> result)
    {
        if (!result.err())
        {
            auto rPayload = result.payload();
            switch (rPayload.status())
            {
            case xbox::services::system::sign_in_status::success:
                // sign-in successful
                signIn = true;
                break;
            case xbox::services::system::sign_in_status::user_interaction_required:
                // 3. Attempt to sign-in with UX if required
                m_user->signin(Windows::UI::Core::CoreWindow::GetForCurrentThread()->Dispatcher)
                    .then([this](xbox::services::xbox_live_result<xbox::services::system::sign_in_result> loudResult) // use task_continuation_context::use_current() to make the continuation task running in current apartment 
                {
                    if (!loudResult.err())
                    {
                        auto resPayload = loudResult.payload();
                        switch (resPayload.status())
                        {
                        case xbox::services::system::sign_in_status::success:
                            // sign-in successful
                            signIn = true;
                            break;
                        case xbox::services::system::sign_in_status::user_cancel:
                            // user cancelled sign in 
                            // present in-game UX that allows the user to retry the sign-in operation. (For example, a sign-in button)
                            break;
                        }
                    }
                    else
                    {
                        //login has failed at this point
                    }
                }, concurrency::task_continuation_context::use_current());
                break;
            }
        }
    });
    if (signIn)
    {
        // 4. Create an Xbox Live context based on the interacting user
        m_xboxLiveContext = std::make_shared<xbox::services::xbox_live_context>(m_user); // m_xboxLiveContext declared in header file

        // add sign out event handler
        AddSignOut();
    }
}

void SignInSample::AddSignOut()
{
    xbox::services::system::xbox_live_user::add_sign_out_completed_handler(
        [this](const xbox::services::system::sign_out_completed_event_args&)

    {
        // 6. When the game exits or the user signs-out, release the XboxLiveUser object and XboxLiveContext object by setting them to null
        m_user = NULL;
        m_xboxLiveContext = NULL;
    });
}

```

### <a name="c-winrt"></a>C#(WinRT)

```csharp

using System.Diagnostics;
using Microsoft.Xbox.Services.System;
using Microsoft.Xbox.Services;

public async Task SignIn()
{
    bool signedIn = false;

    // Get a list of the active Windows users.
    IReadOnlyList<Windows.System.User> users = await Windows.System.User.FindAllAsync();

    // Acquire the CoreDispatcher which will be required for SignInSilentlyAsync and SignInAsync.
    Windows.UI.Core.CoreDispatcher UIDispatcher = Windows.UI.Xaml.Window.Current.CoreWindow.Dispatcher; 

    try
    {
        // 1. Create an XboxLiveUser object to represent the user
        XboxLiveUser primaryUser = new XboxLiveUser(users[0]);

        // 2. Sign-in silently to Xbox Live
        SignInResult signInSilentResult = await primaryUser.SignInSilentlyAsync(UIDispatcher);
        switch (signInSilentResult.Status)
        {
            case SignInStatus.Success:
                signedIn = true;
                break;
            case SignInStatus.UserInteractionRequired:
                //3. Attempt to sign-in with UX if required
                SignInResult signInLoud = await primaryUser.SignInAsync(UIDispatcher);
                switch(signInLoud.Status)
                {
                    case SignInStatus.Success:
                        signedIn = true;
                        break;
                    case SignInStatus.UserCancel:
                        // present in-game UX that allows the user to retry the sign-in operation. (For example, a sign-in button)
                        break;
                    default:
                        break;
                }
                break;
            default:
                break;
        }
        if(signedIn)
        {
            // 4. Create an Xbox Live context based on the interacting user
            Microsoft.Xbox.Services.XboxLiveContext m_xboxLiveContext = new Microsoft.Xbox.Services.XboxLiveContext(user);

            //add the sign out event handler
            XboxLiveUser.SignOutCompleted += OnSignOut;
        }
    }
    catch (Exception e)
    {
        Debug.WriteLine(e.Message);
    }

}

public void OnSignOut(object sender, SignOutCompletedEventArgs e)
    {
        // 6. When the game exits or the user signs-out, release the XboxLiveUser object and XboxLiveContext object by setting them to null
        primaryUser = null;
        xboxLiveContext = null;
    }
```

## <a name="sign-out"></a>Выйдите из системы

### <a name="handling-user-sign-out-completed-event"></a>Обработка пользователя выхода завершенное событие

Пользователь будет выхода из заголовка, если произойдет одно из следующих событий:

1. Пользователь подписан ухода из приложения Xbox (Windows 10) или консоли оболочки (Xbox One). Выйти из системы повлияет на все приложения Xbox Live включена, установленные для этого пользователя.
2. Пользователь переключается на другую учетную запись Майкрософт
3. Этот пользователь вошел в один и тот же заголовок с другого устройства

Во всех этих случаях заголовок будет получено сообщение о событии из `xbox_live_user::add_sign_out_completed_handler` или `XboxLiveUser::SignOutCompleted` обработчиков.  Игра необходимо соответствующим образом обрабатывать выхода завершенное событие:

1. Игра должна отображаться наглядное указание для пользователя, он автоматический ухода из Xbox Live.
2. Вызывать любой службы Xbox Live API-интерфейсы в обработчике событий игры невозможно, поскольку пользователь уже автоматический выход и токен авторизации не доступен.

## <a name="sign-out-handler-code-samples"></a>Выйдите из системы примеры кода обработчика

### <a name="c"></a>C++

```cpp

xbox::services::system::xbox_live_user::add_sign_out_completed_handler(
        [this](const xbox::services::system::sign_out_completed_event_args&)

    {
        // 6. When the game exits or the user signs-out, release the XboxLiveUser object and XboxLiveContext object by setting them to null
        m_user = NULL;
        m_xboxLiveContext = NULL;
    });

```

### <a name="c-winrt"></a>C#(WinRT)

```csharp
XboxLiveUser.SignOutCompleted += OnUserSignOut;

public void OnSignOut(object sender, SignOutCompletedEventArgs e)
        {
            // 6. When the game exits or the user signs-out, release the XboxLiveUser object and XboxLiveContext object by setting them to null
            primaryUser = null;
            xboxLiveContext = null;
        }
```

## <a name="determining-if-the-device-is-offline"></a>Определение, если устройство находится в автономном режиме

Входа в интерфейсы API по-прежнему будет успешной при автономный режим, если пользователь выполнил первый раз, и будет возвращаться последнего входа в учетную запись.  

Если пользователь не имеет подписи прежде, чем автономного входа в систему не будет достичь.

Если заголовок может воспроизводиться в автономном режиме (Кампания режиме, т. д.), заголовок можно разрешить пользователю для воспроизведения и записи игр выполняется через WriteInGameEvent API и подключенных API службы хранилища, оба из них работать должным образом пока устройство находится в автономном режиме.

Если заголовок не может быть воспроизведена вне сети (игры и игры на основе Server т. д.) заголовок следует вызывать API GetNetworkConnectivityLevel, чтобы узнать, если устройство находится в автономном режиме и сообщить пользователю о состоянии и возможные решения (например, "вам нужно подключение к Интернету, чтобы продолжить... ").

## <a name="online-status-code-samples"></a>Примеры кода состояния

### <a name="c"></a>C++

```cpp

using namespace Windows::Networking::Connectivity;

//Retrieve the ConnectionProfile
ConnectionProfile^ InternetConnectionProfile = NetworkInformation::GetInternetConnectionProfile();

NetworkConnectivityLevel connectionLevel = InternetConnectionProfile->GetNetworkConnectivityLevel();

switch (connectionLevel)
{
case NetworkConnectivityLevel::InternetAccess:
    // User is connected to the internet.
    break;
case NetworkConnectivityLevel::ConstrainedInternetAccess: //Limited Internet Access Possible Authentication Required
     // display error message for user.
    LogConnectivityLine("Game Offline: Limited internet access, browser authentication may be required. "); //function writes to UI
    break;
default:
    LogConnectivityLine("Game Offline: No internet access.");
    break;
}

```

### <a name="c-winrt"></a>C#(WinRT)

```csharp
using Windows.Networking.Connectivity;

//Retrieve the ConnectionProfile
string connectionProfileInfo = string.Empty;
ConnectionProfile InternetConnectionProfile = NetworkInformation.GetInternetConnectionProfile();

NetworkConnectivityLevel connectionLevel = InternetConnectionProfile.GetNetworkConnectivityLevel();

switch(connectionLevel)
    {
        case NetworkConnectivityLevel.InternetAccess:
            // User is connected to the internet.
            break;
        case NetworkConnectivityLevel.ConstrainedInternetAccess: //Limited Internet Access Possible Authentication Required
            // display error message for user.
            LogConnectivityLine("Game Offline: Limited internet access, browser authentication may be required. "); //function writes to UI
            break;
        default:
            LogConnectivityLine("Game Offline: No internet access.");
            break;
    }
```