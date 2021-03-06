---
title: Обработка активации приложения
description: Узнайте, как обрабатывать активацию приложения путем переопределения метода OnLaunched.
ms.assetid: DA9A6A43-F09D-4512-A2AB-9B6132431007
ms.date: 07/02/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
- vb
ms.openlocfilehash: 622fc4246c0ce8051135feab07295034b55a82e4
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370824"
---
# <a name="handle-app-activation"></a>Обработка активации приложения

Узнайте, как выполнять активацию приложения путем переопределения [ **Application.OnLaunched** ](/uwp/api/windows.ui.xaml.application.onlaunched) метод.

## <a name="override-the-launch-handler"></a>Переопределение обработчика запуска

При активации приложения, для какой-либо причине, система отправляет [ **CoreApplicationView.Activated** ](/uwp/api/windows.applicationmodel.core.coreapplicationview.activated) событий. Список типов активации см. в перечислении [**ActivationKind**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ActivationKind).

Класс [**Windows.UI.Xaml.Application**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Application) определяет методы, которые можно переопределить для обработки различных типов активации. Некоторые типы активации имеют специальный метод, который можно переопределить. Для других типов активации необходимо переопределить метод [**OnActivated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onactivated).

Определите класс для своего приложения.

```xml
<Application
    x:Class="AppName.App"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
```

Переопределите метод [**OnLaunched**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched). Этот метод вызывается при каждом запуске приложения пользователем. Параметр [**LaunchActivatedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.LaunchActivatedEventArgs) содержит предыдущее состояние приложения и аргументы активации.

> [!NOTE]
> В Windows запуска приостановленного приложения из начала списка плитки или приложение не вызывает этот метод.

```csharp
using System;
using Windows.ApplicationModel.Activation;
using Windows.UI.Xaml;

namespace AppName
{
   public partial class App
   {
      async protected override void OnLaunched(LaunchActivatedEventArgs args)
      {
         EnsurePageCreatedAndActivate();
      }

      // Creates the MainPage if it isn't already created.  Also activates
      // the window so it takes foreground and input focus.
      private MainPage EnsurePageCreatedAndActivate()
      {
         if (Window.Current.Content == null)
         {
             Window.Current.Content = new MainPage();
         }

         Window.Current.Activate();
         return Window.Current.Content as MainPage;
      }
   }
}
```

```vb
Class App
   Protected Overrides Sub OnLaunched(args As LaunchActivatedEventArgs)
      Window.Current.Content = New MainPage()
      Window.Current.Activate()
   End Sub
End Class
```

```cppwinrt
...
#include "MainPage.h"
#include "winrt/Windows.ApplicationModel.Activation.h"
#include "winrt/Windows.UI.Xaml.h"
#include "winrt/Windows.UI.Xaml.Controls.h"
...
using namespace winrt;
using namespace Windows::ApplicationModel::Activation;
using namespace Windows::UI::Xaml;
using namespace Windows::UI::Xaml::Controls;

struct App : AppT<App>
{
    App();

    /// <summary>
    /// Invoked when the application is launched normally by the end user.  Other entry points
    /// will be used such as when the application is launched to open a specific file.
    /// </summary>
    /// <param name="e">Details about the launch request and process.</param>
    void OnLaunched(LaunchActivatedEventArgs const& e)
    {
        Frame rootFrame{ nullptr };
        auto content = Window::Current().Content();
        if (content)
        {
            rootFrame = content.try_as<Frame>();
        }

        // Do not repeat app initialization when the Window already has content,
        // just ensure that the window is active
        if (rootFrame == nullptr)
        {
            // Create a Frame to act as the navigation context and associate it with
            // a SuspensionManager key
            rootFrame = Frame();

            rootFrame.NavigationFailed({ this, &App::OnNavigationFailed });

            if (e.PreviousExecutionState() == ApplicationExecutionState::Terminated)
            {
                // Restore the saved session state only when appropriate, scheduling the
                // final launch steps after the restore is complete
            }

            if (e.PrelaunchActivated() == false)
            {
                if (rootFrame.Content() == nullptr)
                {
                    // When the navigation stack isn't restored navigate to the first page,
                    // configuring the new page by passing required information as a navigation
                    // parameter
                    rootFrame.Navigate(xaml_typename<BlankApp1::MainPage>(), box_value(e.Arguments()));
                }
                // Place the frame in the current Window
                Window::Current().Content(rootFrame);
                // Ensure the current window is active
                Window::Current().Activate();
            }
        }
        else
        {
            if (e.PrelaunchActivated() == false)
            {
                if (rootFrame.Content() == nullptr)
                {
                    // When the navigation stack isn't restored navigate to the first page,
                    // configuring the new page by passing required information as a navigation
                    // parameter
                    rootFrame.Navigate(xaml_typename<BlankApp1::MainPage>(), box_value(e.Arguments()));
                }
                // Ensure the current window is active
                Window::Current().Activate();
            }
        }
    }
};
```

```cpp
using namespace Windows::ApplicationModel::Activation;
using namespace Windows::Foundation;
using namespace Windows::UI::Xaml;
using namespace AppName;
void App::OnLaunched(LaunchActivatedEventArgs^ args)
{
   EnsurePageCreatedAndActivate();
}

// Creates the MainPage if it isn't already created.  Also activates
// the window so it takes foreground and input focus.
void App::EnsurePageCreatedAndActivate()
{
    if (_mainPage == nullptr)
    {
        // Save the MainPage for use if we get activated later
        _mainPage = ref new MainPage();
    }
    Window::Current->Content = _mainPage;
    Window::Current->Activate();
}
```

## <a name="restore-application-data-if-app-was-suspended-then-terminated"></a>Восстановление данных приложения, если его работа была приостановлена, а затем завершена

Когда пользователь переключается на приложение, которое завершило работу, система отправляет событие [**Activated**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationview.activated), свойству [**Kind**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.iactivatedeventargs.kind) которого задано значение **Launch**, а свойству [**PreviousExecutionState**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.iactivatedeventargs.previousexecutionstate) — значение **Terminated** или **ClosedByUser**. Приложение должно загрузить свои сохраненные данные и обновить отображаемое содержимое.

```csharp
async protected override void OnLaunched(LaunchActivatedEventArgs args)
{
   if (args.PreviousExecutionState == ApplicationExecutionState.Terminated ||
       args.PreviousExecutionState == ApplicationExecutionState.ClosedByUser)
   {
      // TODO: Populate the UI with the previously saved application data
   }
   else
   {
      // TODO: Populate the UI with defaults
   }

   EnsurePageCreatedAndActivate();
}
```

```vb
Protected Overrides Sub OnLaunched(args As Windows.ApplicationModel.Activation.LaunchActivatedEventArgs)
   Dim restoreState As Boolean = False

   Select Case args.PreviousExecutionState
      Case ApplicationExecutionState.Terminated
         ' TODO: Populate the UI with the previously saved application data
         restoreState = True
      Case ApplicationExecutionState.ClosedByUser
         ' TODO: Populate the UI with the previously saved application data
         restoreState = True
      Case Else
         ' TODO: Populate the UI with defaults
   End Select

   Window.Current.Content = New MainPage(restoreState)
   Window.Current.Activate()
End Sub
```

```cppwinrt
void App::OnLaunched(Windows::ApplicationModel::Activation::LaunchActivatedEventArgs const& e)
{
    if (e.PreviousExecutionState() == ApplicationExecutionState::Terminated ||
        e.PreviousExecutionState() == ApplicationExecutionState::ClosedByUser)
    {
        // Populate the UI with the previously saved application data.
    }
    else
    {
        // Populate the UI with defaults.
    }
    ...
}
```

```cpp
void App::OnLaunched(Windows::ApplicationModel::Activation::LaunchActivatedEventArgs^ args)
{
   if (args->PreviousExecutionState == ApplicationExecutionState::Terminated ||
       args->PreviousExecutionState == ApplicationExecutionState::ClosedByUser)
   {
      // TODO: Populate the UI with the previously saved application data
   }
   else
   {
      // TODO: Populate the UI with defaults
   }

   EnsurePageCreatedAndActivate();
}
```

Если для свойства [**PreviousExecutionState**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.iactivatedeventargs.previousexecutionstate) задано значение **NotRunning**, это означает, что приложению не удалось успешно сохранить свои данные и поэтому оно вынуждено начать работу с состояния первоначального запуска.

## <a name="remarks"></a>Примечания

> [!NOTE]
> Приложения могут пропустить инициализацию, если содержимое уже задано в текущем окне. Вы можете проверить [ **LaunchActivatedEventArgs.TileId** ](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.launchactivatedeventargs.tileid) свойства, чтобы определить ли приложение было запущено из первичной или вторичной плитки и, на основе этой информации решить, должна ли вы предоставляют новый или возобновления работы приложения.

## <a name="important-apis"></a>Важные API
* [Windows.ApplicationModel.Activation](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation)
* [Windows.UI.Xaml.Application](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Application)

## <a name="related-topics"></a>См. также
* [Обработка приостановки работы приложения](suspend-an-app.md)
* [Обработка возобновления работы приложения](resume-an-app.md)
* [Рекомендации для приложения, приостановка и возобновление](https://docs.microsoft.com/windows/uwp/launch-resume/index)
* [Жизненный цикл приложения](app-lifecycle.md)
