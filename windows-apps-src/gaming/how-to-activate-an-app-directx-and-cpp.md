---
author: mtoepke
title: Активация приложения (DirectX и C++)
description: В этом разделе показано, как определить процесс активации для приложения универсальной платформы Windows (UWP), использующего DirectX.
ms.assetid: b07c7da1-8a5e-5b57-6f77-6439bf653a53
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, активация
ms.localizationpriority: medium
ms.openlocfilehash: b7f700ab97566ad9ec03d0595c55721dd9a9be98
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6202214"
---
# <a name="how-to-activate-an-app-directx-and-c"></a>Активация приложения (DirectX и C++)



В этом разделе показано, как определить процесс активации для приложения универсальной платформы Windows (UWP), использующего DirectX.

## <a name="register-the-app-activation-event-handler"></a>Регистрация обработчика события активации приложения


Сначала зарегистрируйтесь для обработки события [**CoreApplicationView::Activated**](https://msdn.microsoft.com/library/windows/apps/br225018), которое возникает при запуске и инициализации приложения операционной системой.

Добавьте этот код в реализацию метода [**IFrameworkView::Initialize**](https://msdn.microsoft.com/library/windows/apps/hh700495) вашего поставщика представлений (в примере он называется **MyViewProvider**):

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


При запуске приложения необходимо получить ссылку на [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) для приложения. **CoreWindow** содержит диспетчер сообщений о событиях окон, который ваше приложение использует для обработки событий окон. Получите ссылку с помощью обратного вызова для события активации приложения. Для этого необходимо вызвать [**CoreWindow::GetForCurrentThread**](https://msdn.microsoft.com/library/windows/apps/hh701589). Получив ссылку, активируйте главное окно приложения, вызвав [**CoreWindow::Activate**](https://msdn.microsoft.com/library/windows/apps/br208254).

```cpp
void App::OnActivated(CoreApplicationView^ applicationView, IActivatedEventArgs^ args)
{
    // Run() won't start until the CoreWindow is activated.
    CoreWindow::GetForCurrentThread()->Activate();
}
```

## <a name="start-processing-event-message-for-the-main-app-window"></a>Запуск обработки сообщения о событии для главного окна приложения


Обратные вызовы происходят при обработке сообщений о событии диспетчером [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) для окна приложения [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225). Обратный вызов не будет запущен, если вы не вызовете [**CoreDispatcher::ProcessEvents**](https://msdn.microsoft.com/library/windows/apps/br208215) из основного цикла вашего приложения (реализованного в методе поставщика представлений [**IFrameworkView::Run**](https://msdn.microsoft.com/library/windows/apps/hh700505)).

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

## <a name="related-topics"></a>Связанные разделы


* [Приостановка работы приложения (DirectX и C++)](how-to-suspend-an-app-directx-and-cpp.md)
* [Возобновление работы приложения (DirectX и C++)](how-to-resume-an-app-directx-and-cpp.md)

 

 




