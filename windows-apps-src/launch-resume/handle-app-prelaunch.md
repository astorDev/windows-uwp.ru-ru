---
author: TylerMSFT
title: Обработка предварительного запуска приложения
description: Узнайте, как обрабатывать предварительный запуск приложения путем переопределения метода OnLaunched и вызова метода CoreApplication.EnablePrelaunch (true).
ms.assetid: A4838AC2-22D7-46BA-9EB2-F3C248E22F52
ms.author: twhitney
ms.date: 07/05/2018
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a13ec942080d7fe517a10b837bea9ae8fae27750
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7436308"
---
# <a name="handle-app-prelaunch"></a>Обработка предварительного запуска приложения

Обработка предварительного запуска приложения путем переопределения метода [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335).

## <a name="introduction"></a>Введение

Если разрешить доступных системных ресурсов, скорость запуска приложения UWP на устройствах семейства настольных устройств повышена увеличена благодаря запуску пользователя наиболее часто используемых приложений в фоновом режиме. Заблаговременно запущенное приложение переводится в приостановленное состояние вскоре после его запуска. Таким образом, когда пользователь вызывает приложение, его работа возобновляется путем перевода из состояния приостановки в состояние выполнения. Это быстрее, чем холодный запуск приложения. Пользователь просто видит, что приложение запустилось очень быстро.

До Windows 10 приложения не использовали преимущества предварительного запуска автоматически. В Windows10 версии 1511 все приложения универсальной платформы Windows (UWP) были кандидатами на предварительный запуск. В Windows 10 версии 1607 необходимо явно задать предварительный запуск, вызвав метод [CoreApplication.EnablePrelaunch(true)](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.core.coreapplication.enableprelaunch.aspx). Рекомендуется размещать этот вызов в методе `OnLaunched()` рядом с местом, в котором выполняется проверка условия `if (e.PrelaunchActivated == false)`.

Будет ли приложение запущено предварительно или нет, зависит от системных ресурсов. Если в системе мало свободных ресурсов, предварительный запуск приложения не производится.

Для некоторых типов приложений может понадобиться изменить поведение при запуске, чтобы они правильно работали при предварительном запуске. Например, приложение, которое воспроизводит музыку при запуске, игра, которая предполагает присутствие пользователя и при запуске отображает сложные визуальные элементы, или приложение обмена сообщениями, которое при запуске изменяет статус видимости пользователя в сети, могут обнаруживать факт предварительного запуска и изменять свое поведение при запуске, как описано в следующих разделах.

Шаблоны по умолчанию для проектов XAML (C#, VB, C++) и WinJS в Visual Studio 2015 с обновлением 3 учитывают возможность предварительного запуска.

## <a name="prelaunch-and-the-app-lifecycle"></a>Предварительный запуск и жизненный цикл приложения

После предварительного запуска приложения оно переводится в приостановленное состояние. (См. раздел [Обработка приостановки работы приложения](suspend-an-app.md)).

## <a name="detect-and-handle-prelaunch"></a>Обнаружение и обработка предварительного запуска

Приложения получают флаг [**LaunchActivatedEventArgs.PrelaunchActivated**](https://msdn.microsoft.com/library/windows/apps/dn263740) во время активации. Используйте этот флаг для выполнения кода, который должен выполняться только когда пользователь явно запускает приложение, как показано в следующем изменения [**Application.OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335).

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    // CoreApplication.EnablePrelaunch was introduced in Windows 10 version 1607
    bool canEnablePrelaunch = Windows.Foundation.Metadata.ApiInformation.IsMethodPresent("Windows.ApplicationModel.Core.CoreApplication", "EnablePrelaunch");

    // NOTE: Only enable this code if you are targeting a version of Windows 10 prior to version 1607
    // and you want to opt-out of prelaunch.
    // In Windows 10 version 1511, all UWP apps were candidates for prelaunch.
    // Starting in Windows 10 version 1607, the app must opt-in to be prelaunched.
    //if ( !canEnablePrelaunch && e.PrelaunchActivated == true)
    //{
    //    return;
    //}

    Frame rootFrame = Window.Current.Content as Frame;

    // Do not repeat app initialization when the Window already has content,
    // just ensure that the window is active
    if (rootFrame == null)
    {
        // Create a Frame to act as the navigation context and navigate to the first page
        rootFrame = new Frame();

        rootFrame.NavigationFailed += OnNavigationFailed;

        if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
        {
            //TODO: Load state from previously suspended application
        }

        // Place the frame in the current Window
        Window.Current.Content = rootFrame;
    }

    if (e.PrelaunchActivated == false)
    {
        // On Windows 10 version 1607 or later, this code signals that this app wants to participate in prelaunch
        if (canEnablePrelaunch)
        {
            TryEnablePrelaunch();
        }

        // TODO: This is not a prelaunch activation. Perform operations which
        // assume that the user explicitly launched the app such as updating
        // the online presence of the user on a social network, updating a
        // what's new feed, etc.

        if (rootFrame.Content == null)
        {
            // When the navigation stack isn't restored navigate to the first page,
            // configuring the new page by passing required information as a navigation
            // parameter
            rootFrame.Navigate(typeof(MainPage), e.Arguments);
        }
        // Ensure the current window is active
        Window.Current.Activate();
    }
}

/// <summary>
/// Encapsulates the call to CoreApplication.EnablePrelaunch() so that the JIT
/// won't encounter that call (and prevent the app from running when it doesn't
/// find it), unless this method gets called. This method should only
/// be called when the caller determines that we are running on a system that
/// supports CoreApplication.EnablePrelaunch().
/// </summary>
private void TryEnablePrelaunch()
{
    Windows.ApplicationModel.Core.CoreApplication.EnablePrelaunch(true);
}
```

Примечание `TryEnablePrelaunch()` функции, выше. Причина вызов для `CoreApplication.EnablePrelaunch()` является влияющих вне в эту функцию происходит потому, что при вызове метода для компиляции весь метод будет пытаться JIT (, так и в компиляции). Если приложение запущено на версию Windows 10, которая не поддерживает `CoreApplication.EnablePrelaunch()`, а затем JIT завершится ошибкой. Путем перемножения вызывать метод, который вызывается только в том случае, когда приложение определит, что платформа поддерживает `CoreApplication.EnablePrelaunch()`, мы избежать этой проблемы.

Существует также кода в примере выше, можно раскомментируйте, если приложению необходимо отказаться от предварительного запуска при работе в Windows 10 версии 1511. В версии 1511 все приложения UWP автоматически были переводятся в предварительный запуск, который могут не подходить для вашего приложения.

## <a name="use-the-visibilitychanged-event"></a>Использование события VisibilityChanged

Приложения, запущенные с помощью предварительного запуска, не видны пользователю. Они отображаются, когда пользователь на них переключается. Возможно, вы захотите отложить определенные операции, пока основное окно приложения не будет отображаться. Например, если приложение отображает список новых элементов веб-канала, вы можете обновить этот список во время события [**VisibilityChanged**](https://msdn.microsoft.com/library/windows/apps/hh702458), а не использовать список, который был создан при предварительном запуске приложения, который может устареть к тому времени, когда пользователь активирует приложение. Следующий код обрабатывает событие **VisibilityChanged** для **MainPage**:

```csharp
public sealed partial class MainPage : Page
{
    public MainPage()
    {
        this.InitializeComponent();

        Window.Current.VisibilityChanged += WindowVisibilityChangedEventHandler;
    }

    void WindowVisibilityChangedEventHandler(System.Object sender, Windows.UI.Core.VisibilityChangedEventArgs e)
    {
        // Perform operations that should take place when the application becomes visible rather than
        // when it is prelaunched, such as building a what's new feed
    }
}
```

## <a name="directx-games-guidance"></a>Рекомендации для игр DirectX

Для игр DirectX обычно не следует включать предварительный запуск, поскольку многие игры DirectX выполняют инициализацию до обнаружения предварительного запуска. Начиная с юбилейного обновления Windows 1607, по умолчанию предварительный запуск игр не используется.  Если вы хотите использовать преимущества предварительного запуска для своей игры, вызовите метод [CoreApplication.EnablePrelaunch(true)](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.core.coreapplication.enableprelaunch.aspx).

Если игра предназначена для более ранней версии Windows 10, можно обрабатывать условие предварительного запуска для выхода из приложения.

```cppwinrt
void ViewProvider::OnActivated(CoreApplicationView const& /* appView */, Windows::ApplicationModel::Activation::IActivatedEventArgs const& args)
{
    if (args.Kind() == Windows::ApplicationModel::Activation::ActivationKind::Launch)
    {
        auto launchArgs{ args.as<Windows::ApplicationModel::Activation::LaunchActivatedEventArgs>()};
        if (launchArgs.PrelaunchActivated())
        {
            // Opt-out of Prelaunch.
            CoreApplication::Exit();
        }
    }
}

void ViewProvider::Initialize(CoreApplicationView const & appView)
{
    appView.Activated({ this, &App::OnActivated });
}
```

```cpp
void ViewProvider::OnActivated(CoreApplicationView^ appView,IActivatedEventArgs^ args)
{
    if (args->Kind == ActivationKind::Launch)
    {
        auto launchArgs = static_cast<LaunchActivatedEventArgs^>(args);
        if (launchArgs->PrelaunchActivated)
        {
            // Opt-out of Prelaunch
            CoreApplication::Exit();
            return;
        }
    }
}
```

## <a name="winjs-app-guidance"></a>Рекомендации для приложений WinJS

Если приложение WinJS предназначено для более ранней версии Windows 10, условие предварительного запуска можно обрабатывать в обработчике [onactivated](https://msdn.microsoft.com/library/windows/apps/br212679.aspx):

```javascript
    app.onactivated = function (args) {
        if (!args.detail.prelaunchActivated) {
            // TODO: This is not a prelaunch activation. Perform operations which
            // assume that the user explicitly launched the app such as updating
            // the online presence of the user on a social network, updating a
            // what's new feed, etc.
        }
    }
```

## <a name="general-guidance"></a>Общие рекомендации

-   Приложения не должны выполнять длительные операции во время предварительного запуска, поскольку приложение завершит работу, если его нельзя быстро приостановить.
-   Приложения не должны начинать воспроизведение звука из [**Application.OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335), если приложение предварительно запущено, поскольку оно не будет видимым, и будет непонятно, почему воспроизводится звук.
-   Приложения не должны выполнять во время запуска действия, которые предполагают, что приложение отображается для пользователя, или предполагают, что приложение было явно запущено пользователем. Поскольку приложение теперь может быть запущено в фоновом режиме без явных действий пользователя, разработчики должны учитывать влияние конфиденциальности, взаимодействия с пользователем и производительности.
    -   Пример вопроса конфиденциальности: социальное приложение должно изменить статус пользователя на статус "В сети". Оно должно ждать, пока пользователь не переключится на приложение, а не менять статус пользователя при предварительном запуске.
    -   Пример создания взаимодействия с пользователем: если у вас есть приложение (например, игра), которая показывает вводную последовательность при запуске, можно отложить выполнение вводной последовательности до момента, пока пользователь не переключится на это приложение.
    -   Пример влияния на производительность: можно ждать, пока пользователь не переключится на приложение, чтобы восстановить текущие сведения о погоде, вместо того, чтобы загружать их при предварительном запуске приложения, а затем загружать их снова, когда приложение станет видимым, чтобы убедиться, что отображаются последние сведения.
-   Если приложение при запуске очищает живую плитку, отложите это действие до события смены видимости.
-   Телеметрия вашего приложения должна различать обычную активацию плиток и активацию при предварительном запуске, чтобы упростить поиск сценария, если возникает неполадка.
-   Если у вас есть Microsoft Visual Studio2015 с обновлением 1 и Windows10, версии 1511, вы можете имитировать предварительный запуск своего приложения в Visual Studio2015, выбрав **Отладка** &gt; **Другие цели отладки** &gt; **Отладка универсальных приложений Windows Предварительный запуск**.

## <a name="related-topics"></a>Ссылки по теме

* [Жизненный цикл приложения](app-lifecycle.md)
* [CoreApplication.EnablePrelaunch](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.core.coreapplication.enableprelaunch.aspx)
