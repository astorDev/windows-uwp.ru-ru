---
author: TylerMSFT
title: "Обработка активации приложения"
description: "Узнайте, как обрабатывать активацию приложения путем переопределения метода OnLaunched."
ms.assetid: DA9A6A43-F09D-4512-A2AB-9B6132431007
translationtype: Human Translation
ms.sourcegitcommit: a1bb0d5d24291fad1acab41c149dd9d763610907
ms.openlocfilehash: e41a683026a4543545556e98f6b4e9194099b362

---

# Обработка активации приложения


\[ Обновлено для приложений UWP в Windows10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Узнайте, как обрабатывать активацию приложения путем переопределения метода [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335).

## Переопределение обработчика запуска

Если приложение о какой-либо причине активируется, система отправляет событие [**Activated**](https://msdn.microsoft.com/library/windows/apps/br225018). Список типов активации см. в перечислении [**ActivationKind**](https://msdn.microsoft.com/library/windows/apps/br224693).

Класс [**Windows.UI.Xaml.Application**](https://msdn.microsoft.com/library/windows/apps/br242324) определяет методы, которые можно переопределить для обработки различных типов активации. Некоторые типы активации имеют специальный метод, который можно переопределить. Для других типов активации необходимо переопределить метод [**OnActivated**](https://msdn.microsoft.com/library/windows/apps/br242330).

Определите класс для своего приложения.

```xml
<Application xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             x:Class="AppName.App" >
```

Переопределите метод [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335). Этот метод вызывается при каждом запуске приложения пользователем. Параметр [**LaunchActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br224731) содержит предыдущее состояние приложения и аргументы активации.

**Примечание**. Для приложений из Магазина Windows Phone этот метод вызывается каждый раз, когда пользователь запускает приложение с начальной плитки или из списка приложений, даже если приложение находится в памяти в приостановленном состоянии. В Windows при запуске приостановленного приложения с начального экрана или из списка приложений этот метод не вызывается.

> [!div class="tabbedCodeSnippets"]
> ```cs
> using System;
> using Windows.ApplicationModel.Activation;
> using Windows.UI.Xaml;
>
> namespace AppName
> {
>    public partial class App
>    {
>       async protected override void OnLaunched(LaunchActivatedEventArgs args)
>       {
>          EnsurePageCreatedAndActivate();
>       }
>
>       // Creates the MainPage if it isn't already created.  Also activates
>       // the window so it takes foreground and input focus.
>       private MainPage EnsurePageCreatedAndActivate()
>       {
>          if (Window.Current.Content == null)
>          {
>              Window.Current.Content = new MainPage();
>          }
>
>          Window.Current.Activate();
>          return Window.Current.Content as MainPage;
>       }
>    }
> }
> ```
> ```vb
> Class App
>    Protected Overrides Sub OnLaunched(args As LaunchActivatedEventArgs)
>       Window.Current.Content = New MainPage()
>       Window.Current.Activate()
>    End Sub
> End Class
> ```
> ```cpp
> using namespace Windows::ApplicationModel::Activation;
> using namespace Windows::Foundation;
> using namespace Windows::UI::Xaml;
> using namespace AppName;
> void App::OnLaunched(LaunchActivatedEventArgs^ args)
> {
>    EnsurePageCreatedAndActivate();
> }
>
> // Creates the MainPage if it isn't already created.  Also activates
> // the window so it takes foreground and input focus.
> void App::EnsurePageCreatedAndActivate()
> {
>     if (_mainPage == nullptr)
>     {
>         // Save the MainPage for use if we get activated later
>         _mainPage = ref new MainPage();
>     }
>     Window::Current->Content = _mainPage;
>     Window::Current->Activate();
> }
> ```

## Восстановление данных приложения, если его работа была приостановлена, а затем завершена


Когда пользователь переключается на приложение, которое завершило работу, система отправляет событие [**Activated**](https://msdn.microsoft.com/library/windows/apps/br225018), свойству [**Kind**](https://msdn.microsoft.com/library/windows/apps/br224728) которого задано значение **Launch**, а свойству [**PreviousExecutionState**](https://msdn.microsoft.com/library/windows/apps/br224729)— значение **Terminated** или **ClosedByUser**. Приложение должно загрузить свои сохраненные данные и обновить отображаемое содержимое.

> [!div class="tabbedCodeSnippets"]
> ```cs
> async protected override void OnLaunched(LaunchActivatedEventArgs args)
> {
>    if (args.PreviousExecutionState == ApplicationExecutionState.Terminated ||
>        args.PreviousExecutionState == ApplicationExecutionState.ClosedByUser)
>    {
>       // TODO: Populate the UI with the previously saved application data
>    }
>    else
>    {
>       // TODO: Populate the UI with defaults
>    }
>
>    EnsurePageCreatedAndActivate();
> }
> ```
> ```vb
> Protected Overrides Sub OnLaunched(args As Windows.ApplicationModel.Activation.LaunchActivatedEventArgs)
>    Dim restoreState As Boolean = False
>
>    Select Case args.PreviousExecutionState
>       Case ApplicationExecutionState.Terminated
>          ' TODO: Populate the UI with the previously saved application data
>          restoreState = True
>       Case ApplicationExecutionState.ClosedByUser
>          ' TODO: Populate the UI with the previously saved application data
>          restoreState = True
>       Case Else
>          ' TODO: Populate the UI with defaults
>    End Select
>
>    Window.Current.Content = New MainPage(restoreState)
>    Window.Current.Activate()
> End Sub
> ```
> ```cpp
> void App::OnLaunched(Windows::ApplicationModel::Activation::LaunchActivatedEventArgs^ args)
> {
>    if (args->PreviousExecutionState == ApplicationExecutionState::Terminated ||
>        args->PreviousExecutionState == ApplicationExecutionState::ClosedByUser)
>    {
>       // TODO: Populate the UI with the previously saved application data
>    }
>    else
>    {
>       // TODO: Populate the UI with defaults
>    }
>
>    EnsurePageCreatedAndActivate();
> }
> ```

Если для свойства [**PreviousExecutionState**](https://msdn.microsoft.com/library/windows/apps/br224729) задано значение **NotRunning**, это означает, что приложению не удалось успешно сохранить свои данные и поэтому оно вынуждено начать работу с состояния первоначального запуска.

## Примечания

> **Примечание**. Для приложений Магазина Windows Phone за событием [**Resuming**](https://msdn.microsoft.com/library/windows/apps/br242339) всегда следует событие [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335), даже если работа приложения в настоящее время приостановлена и пользователь повторно запускает приложение с основной плитки или из списка приложений. Приложения могут пропустить инициализацию, если содержимое уже задано в текущем окне. Можно проверить свойство [**LaunchActivatedEventArgs.TileId**](https://msdn.microsoft.com/library/windows/apps/br224736), чтобы определить, было ли приложение запущено с основной или вспомогательной плитки, и на основе этих данных решить, нужно ли запускать приложение заново или можно возобновить его.

## Связанные темы

* [Обработка приостановки работы приложения](suspend-an-app.md)
* [Обработка возобновления работы приложения](resume-an-app.md)
* [Руководство по приостановке и возобновлению работы приложения](https://msdn.microsoft.com/library/windows/apps/hh465088)
* [Жизненный цикл приложения](app-lifecycle.md)

**Справка**

* [**Windows.ApplicationModel.Activation**](https://msdn.microsoft.com/library/windows/apps/br224766)
* [**Windows.UI.Xaml.Application**](https://msdn.microsoft.com/library/windows/apps/br242324)

 

 



<!--HONumber=Aug16_HO3-->


