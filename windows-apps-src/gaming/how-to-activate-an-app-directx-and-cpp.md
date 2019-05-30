---
title: Активация приложения (DirectX и C++)
description: В этом разделе показано, как определить процесс активации для приложения универсальной платформы Windows (UWP), использующего DirectX.
ms.assetid: b07c7da1-8a5e-5b57-6f77-6439bf653a53
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, активация
ms.localizationpriority: medium
ms.openlocfilehash: 4aeba58af61cffa33626c64cebcbade272af109b
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66368605"
---
# <a name="how-to-activate-an-app-directx-and-c"></a>Активация приложения (DirectX и C++)



В этом разделе показано, как определить процесс активации для приложения универсальной платформы Windows (UWP), использующего DirectX.

## <a name="register-the-app-activation-event-handler"></a>Регистрация обработчика события активации приложения


Сначала зарегистрируйтесь для обработки события [**CoreApplicationView::Activated**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationview.activated), которое возникает при запуске и инициализации приложения операционной системой.

Добавьте этот код в реализацию метода [**IFrameworkView::Initialize**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.initialize) вашего поставщика представлений (в примере он называется **MyViewProvider**):

```cpp
void App::Initialize(CoreApplicationView^ applicationView)
{
    // Register event handlers for the app lifecycle. This example includes Activated, so that we
    // can make the CoreWindow active and start rendering on the window.
    applicationView->Activated +=
        ref new TypedEventHandler<CoreApplicationView^, IActivatedEventArgs^>(this, &App::OnActivated);
  
  //...

}
```

## <a name="activate-the-corewindow-instance-for-the-app"></a>Активация экземпляра CoreWindow для приложения


При запуске приложения необходимо получить ссылку на [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) для приложения. **CoreWindow** содержит диспетчер сообщений о событиях окон, который ваше приложение использует для обработки событий окон. Получите ссылку с помощью обратного вызова для события активации приложения. Для этого необходимо вызвать [**CoreWindow::GetForCurrentThread**](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.getforcurrentthread). Получив ссылку, активируйте главное окно приложения, вызвав [**CoreWindow::Activate**](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.activate).

```cpp
void App::OnActivated(CoreApplicationView^ applicationView, IActivatedEventArgs^ args)
{
    // Run() won't start until the CoreWindow is activated.
    CoreWindow::GetForCurrentThread()->Activate();
}
```

## <a name="start-processing-event-message-for-the-main-app-window"></a>Запуск обработки сообщения о событии для главного окна приложения


Обратные вызовы происходят при обработке сообщений о событии диспетчером [**CoreDispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) для окна приложения [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow). Обратный вызов не будет запущен, если вы не вызовете [**CoreDispatcher::ProcessEvents**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.processevents) из основного цикла вашего приложения (реализованного в методе поставщика представлений [**IFrameworkView::Run**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.run)).

``` syntax
// This method is called after the window becomes active.
void App::Run()
{
    while (!m_windowClosed)
    {
        if (m_windowVisible)
        {
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);

            m_main->Update();

            if (m_main->Render())
            {
                m_deviceResources->Present();
            }
        }
        else
        {
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
}
```

## <a name="related-topics"></a>См. также


* [Приостановка приложения (DirectX и C++)](how-to-suspend-an-app-directx-and-cpp.md)
* [Возобновление приложения (DirectX и C++)](how-to-resume-an-app-directx-and-cpp.md)

 

 




