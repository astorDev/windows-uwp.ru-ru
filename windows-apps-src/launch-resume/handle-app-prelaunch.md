---
author: TylerMSFT
title: "Обработка предварительного запуска приложения"
description: "Узнайте, как обрабатывать предварительный запуск приложения путем переопределения метода OnLaunched и вызова метода CoreApplication.EnablePrelaunch (true)."
ms.assetid: A4838AC2-22D7-46BA-9EB2-F3C248E22F52
translationtype: Human Translation
ms.sourcegitcommit: ea9aa37e15dbb6c977b0c0be4f91f77f3879e622
ms.openlocfilehash: cf7cb9f81207f4f25eb8e78283079df27f83d7dc

---

# Обработка предварительного запуска приложения

\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Обработка предварительного запуска приложения путем переопределения метода [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335).

## Введение

Если доступные системные ресурсы позволяют, производительность запуска приложения Магазина Windows для семейства настольных устройств повышается с помощью заблаговременного запуска наиболее часто используемых пользователем приложений в фоновом режиме. Заблаговременно запущенное приложение переводится в приостановленное состояние вскоре после его запуска. Таким образом, когда пользователь вызывает приложение, его работа возобновляется путем перевода из состояния приостановки в состояние выполнения. Это быстрее, чем холодный запуск приложения. Пользователь просто видит, что приложение запустилось очень быстро.

До Windows 10 приложения не использовали преимущества предварительного запуска автоматически. В Windows 10 версии 1511 все приложения универсальной платформы Windows (UWP) были кандидатами на предварительный запуск. В Windows 10 версии 1607 необходимо явно задать предварительный запуск, вызвав метод [CoreApplication.EnablePrelaunch(true)](https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.core.coreapplication.enableprelaunch.aspx). Рекомендуется размещать этот вызов в методе `OnLaunched()` рядом с местом, в котором выполняется проверка условия `if (e.PrelaunchActivated == false)`.

Будет ли приложение запущено предварительно или нет, зависит от системных ресурсов. Если в системе мало свободных ресурсов, предварительный запуск приложения не производится.

Для некоторых типов приложений может понадобиться изменить поведение при запуске, чтобы они правильно работали при предварительном запуске. Например, приложение, которое воспроизводит музыку при запуске, игра, которая предполагает присутствие пользователя и при запуске отображает сложные визуальные элементы, или приложение обмена сообщениями, которое при запуске изменяет статус видимости пользователя в сети, могут обнаруживать факт предварительного запуска и изменять свое поведение при запуске, как описано в следующих разделах.

Шаблоны по умолчанию для проектов XAML (C#, VB, C++) и WinJS в Visual Studio 2015 с обновлением 3 учитывают возможность предварительного запуска.

## Предварительный запуск и жизненный цикл приложения

После предварительного запуска приложения оно переводится в приостановленное состояние. (См. раздел [Обработка приостановки работы приложения](suspend-an-app.md)).

## Обнаружение и обработка предварительного запуска

Приложения получают флаг [**LaunchActivatedEventArgs.PrelaunchActivated**](https://msdn.microsoft.com/library/windows/apps/dn263740) во время активации. Используйте этот флаг для выполнения кода, который должен выполняться только в случае, когда пользователь явно запускает приложение, как показано в следующем отрывке из метода [**Application.OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335).

```cs
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    // Do not repeat app initialization when the Window already has content - rather just ensure that the window is active
    if (rootFrame == null)
    {
        // Create a Frame to act as the navigation context and navigate to the first page
        rootFrame = new Frame();

        rootFrame.NavigationFailed += OnNavigationFailed;

        if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
        {
            //TODO: Load state from previously suspended application
        }

        if (!e.PrelaunchActivated)
        {
            // TODO: This is not a prelaunch activation. Perform operations which
            // assume that the user explicitly launched the app such as updating
            // the online presence of the user on a social network, updating a
            // what's new feed, etc.
        }

        // Place the frame in the current Window
        Window.Current.Content = rootFrame;
    }

    if (rootFrame.Content == null)
    {
        // When the navigation stack isn't restored navigate to the first page,
        // configuring the new page by passing required information as a navigation parameter
        rootFrame.Navigate(typeof(MainPage), e.Arguments);
    }
    // Ensure the current window is active
    Window.Current.Activate();
}
```

**Совет.** Если вы разрабатываете приложение для версии Windows 10 до 1607 и хотите явно отказаться от предварительного запуска, установите флажок [**LaunchActivatedEventArgs.PrelaunchActivated**](https://msdn.microsoft.com/library/windows/apps/dn263740). Если он установлен, верните значение OnLaunched(), прежде чем выполнять какую-либо работу по созданию фрейма или активированию окна.

## Использование события VisibilityChanged

Приложения, запущенные с помощью предварительного запуска, не видны пользователю. Они отображаются, когда пользователь на них переключается. Возможно, вы захотите отложить определенные операции, пока основное окно приложения не будет отображаться. Например, если приложение отображает список новых элементов веб-канала, вы можете обновить этот список во время события [**VisibilityChanged**](https://msdn.microsoft.com/library/windows/apps/hh702458), а не использовать список, который был создан при предварительном запуске приложения, который может устареть к тому времени, когда пользователь активирует приложение. Следующий код обрабатывает событие **VisibilityChanged** для **MainPage**:

```cs
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

## Рекомендации для игр DirectX

Для игр DirectX обычно не следует включать предварительный запуск, поскольку многие игры DirectX выполняют инициализацию до обнаружения предварительного запуска. Начиная с юбилейного обновления Windows 1607, по умолчанию предварительный запуск игр не используется.  Если вы хотите использовать преимущества предварительного запуска для своей игры, вызовите метод [CoreApplication.EnablePrelaunch(true)](https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.core.coreapplication.enableprelaunch.aspx).

Если игра предназначена для более ранней версии Windows 10, можно обрабатывать условие предварительного запуска для выхода из приложения.

```cs
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

## Рекомендации для приложений WinJS

Если приложение WinJS предназначено для более ранней версии Windows 10, условие предварительного запуска можно обрабатывать в обработчике [onactivated](https://msdn.microsoft.com/en-us/library/windows/apps/br212679.aspx):

```js
    app.onactivated = function (args) {
        if (!args.detail.prelaunchActivated) {
            // TODO: This is not a prelaunch activation. Perform operations which
            // assume that the user explicitly launched the app such as updating
            // the online presence of the user on a social network, updating a
            // what's new feed, etc.
        }
    }
```

## Общие рекомендации

-   Приложения не должны выполнять длительные операции во время предварительного запуска, поскольку приложение завершит работу, если его нельзя быстро приостановить.
-   Приложения не должны начинать воспроизведение звука из [**Application.OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335), если приложение предварительно запущено, поскольку оно не будет видимым, и будет непонятно, почему воспроизводится звук.
-   Приложения не должны выполнять во время запуска действия, которые предполагают, что приложение отображается для пользователя, или предполагают, что приложение было явно запущено пользователем. Поскольку приложение теперь может быть запущено в фоновом режиме без явных действий пользователя, разработчики должны учитывать влияние конфиденциальности, взаимодействия с пользователем и производительности.
    -   Пример вопроса конфиденциальности: социальное приложение должно изменить статус пользователя на статус "В сети". Оно должно ждать, пока пользователь не переключится на приложение, а не менять статус пользователя при предварительном запуске.
    -   Пример создания взаимодействия с пользователем: если у вас есть приложение (например, игра), которая показывает вводную последовательность при запуске, можно отложить выполнение вводной последовательности до момента, пока пользователь не переключится на это приложение.
    -   Пример влияния на производительность: можно ждать, пока пользователь не переключится на приложение, чтобы восстановить текущие сведения о погоде, вместо того, чтобы загружать их при предварительном запуске приложения, а затем загружать их снова, когда приложение станет видимым, чтобы убедиться, что отображаются последние сведения.
-   Если приложение при запуске очищает живую плитку, отложите это действие до события смены видимости.
-   Телеметрия вашего приложения должна различать обычную активацию плиток и активацию при предварительном запуске, чтобы упростить поиск сценария, если возникает неполадка.
-   При наличии обновления 1 для Microsoft Visual Studio 2015 и версии 1511 Windows 10 вы можете имитировать предварительный запуск своего приложения в Visual Studio 2015, последовательно выбрав элементы **Отладка** &gt; **Другие цели отладки** &gt; **Отладка предварительного запуска универсального приложения для Windows**.

## Связанные разделы

* [Жизненный цикл приложения](app-lifecycle.md)
* [CoreApplication.EnablePrelaunch](https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.core.coreapplication.enableprelaunch.aspx)



<!--HONumber=Aug16_HO4-->


