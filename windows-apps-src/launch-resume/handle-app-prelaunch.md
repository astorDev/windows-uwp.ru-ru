---
author: TylerMSFT
title: "Обработка предварительного запуска приложения"
description: "Обработка предварительного запуска приложения путем переопределения метода OnLaunched."
ms.assetid: A4838AC2-22D7-46BA-9EB2-F3C248E22F52
ms.sourcegitcommit: 213384a194513a0f98a5f37e7f0e0849bf0a66e2
ms.openlocfilehash: d9d3bdf86d858367008a32d9d6a06ec9fc13787d

---

# Обработка предварительного запуска приложения


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x, см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335)

Обработка предварительного запуска приложения путем переопределения метода [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335).

## Введение


Если доступные системные ресурсы позволяют, производительность запуска приложения магазина Windows повышается с помощью заблаговременного запуска наиболее часто используемых пользователем приложений в фоновом режиме. Заблаговременно запущенное приложение переводится в приостановленное состояние вскоре после его запуска. Когда пользователь вызывает приложение, его работа возобновляется, переводя его из состояния приостановки в состояние выполнения. Это быстрее, чем холодный запуск приложения.

До Windows10 приложения не использовали преимущества предварительного запуска автоматически. Начинаяc Windows 10, все приложения универсальной платформы Windows (UWP) автоматически используют преимущества предварительного запуска.

Большинство приложений обычно работают с предварительным запуском без изменений. Однако некоторым типам приложений может понадобиться изменить поведение при запуске, чтобы правильно работать при предварительном запуске. Примером этого может служить приложение для обмена сообщениями, в котором меняется видимость пользователя в сети во время запуска, или игра, которая предполагает, что пользователь присутствует в игре и отображает сложные визуальные элементы при запуске приложения.

## Предварительный запуск и жизненный цикл приложения


Вскоре после предварительного запуска приложения оно переводится в приостановленное состояние. (см. раздел [Обработка приостановки работы приложения](suspend-an-app.md)).

## Обнаружение и обработка предварительного запуска


Приложения получают флаг [**LaunchActivatedEventArgs.PrelaunchActivated**](https://msdn.microsoft.com/library/windows/apps/dn263740) во время активации. Используйте этот флаг, чтобы указать, нужно ли выполнять операции, которые должны выполняться только если пользователь явно запускает приложение, как показано в следующем отрывке из[**Application.OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335).

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


            **Подсказка.** Если вы хотите явно отказаться от предварительного запуска, установите флажок [**LaunchActivatedEventArgs.PrelaunchActivated**](https://msdn.microsoft.com/library/windows/apps/dn263740). Если он установлен, верните значение OnLaunched(), прежде чем выполнять какую-либо работу по созданию фрейма или активированию окна.

 

## Использование события VisibilityChanged


Приложения, запущенные с помощью предварительного запуска, не видны пользователю. Они отображаются, когда пользователь на них переключается. Возможно, вы захотите отложить определенные операции, пока основное окно приложения не будет отображаться. Например, если приложение отображает список новых элементов веб-канала, вы можете обновить этот список во время события [**VisibilityChanged**](https://msdn.microsoft.com/library/windows/apps/hh702458), а не применять список, который был создан, когда приложение было предварительно запущено и который может быть устаревшим к тому времени, когда пользователь активирует приложение. Следующий код обрабатывает событие **VisibilityChanged** для **MainPage**:

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

## Руководство


-   Приложения не должны выполнять длительные операции во время предварительного запуска, поскольку приложение завершит работу, если его нельзя будет приостановить быстро.
-   Приложения не должны начинать воспроизведение звука из [**Application.OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335), если приложение предварительно запущено, поскольку оно не будет видимым, и будет непонятно, почему воспроизводится звук.
-   Приложения не должны выполнять во время запуска действия, которые предполагают, что приложение отображается для пользователя, или предполагают, что приложение было явно запущено пользователем. Поскольку приложение теперь может быть запущено в фоновом режиме без явных действий пользователя, разработчики должны учитывать влияние конфиденциальности, взаимодействия с пользователем и производительности.
    -   Пример вопроса конфиденциальности: социальное приложение должно изменить статус пользователя на статус "В сети". Оно должно ждать, пока пользователь не переключится на приложение, а не менять статус пользователя при предварительном запуске.
    -   Пример создания взаимодействия с пользователем: если у вас есть приложение (например, игра), которая показывает вводную последовательность при запуске, можно отложить выполнение вводной последовательности до момента, пока пользователь не переключится на это приложение.
    -   Пример влияния производительности: можно ждать, пока пользователь не переключится на приложение, чтобы восстановить текущие сведения о погоде вместо того, чтобы загружать их при предварительном запуске приложения, а затем загружать их снова, когда приложение станет видимым, чтобы убедиться, что отображаются последние сведения.
-   Если приложение при запуске очищает живую плитку, отложите это действие до события смены видимости.
-   Телеметрия вашего приложения должна различать обычную активацию плиток и активацию при предварительном запуске, чтобы можно было определить сценарий, в котором происходит проблема.
-   При наличии обновления 1 для Microsoft Visual Studio2015 и версии 1511 Windows10 вы можете имитировать предварительный запуск своего приложения в Visual Studio2015, последовательно выбрав элементы **Отладка**&gt;**Другие цели отладки**&gt;**Отладка предварительного запуска универсального приложения для Windows**.

## Связанные разделы

* [Жизненный цикл приложения](app-lifecycle.md)

 

 



<!--HONumber=Jun16_HO5-->


