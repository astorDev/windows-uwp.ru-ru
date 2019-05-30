---
title: Возобновление работы приложения (DirectX и C++)
description: В этом разделе показано, как восстановить важные данные приложения, когда система возобновляет работу приложения универсальной платформы Windows (UWP), использующего DirectX.
ms.assetid: 5e6bb673-6874-ace5-05eb-f88c045f2178
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, возобновление, directx
ms.localizationpriority: medium
ms.openlocfilehash: b1506351dd06563386154ac35938cbd17f5ced32
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66368616"
---
# <a name="how-to-resume-an-app-directx-and-c"></a>Возобновление работы приложения (DirectX и C++)



В этом разделе показано, как восстановить важные данные приложения, когда система возобновляет работу приложения универсальной платформы Windows (UWP), использующего DirectX.

## <a name="register-the-resuming-event-handler"></a>Регистрация обработчика события возобновления


Выполните регистрацию для обработки события [**CoreApplication::Resuming**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.resuming), которое сигнализирует о том, что пользователь переключился с вашего приложения на другую задачу, а затем вернулся к нему.

Добавьте этот код в реализацию метода [**IFrameworkView::Initialize**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.initialize) вашего поставщика представлений:

```cpp
// The first method is called when the IFrameworkView is being created.
void App::Initialize(CoreApplicationView^ applicationView)
{
  //...
  
    CoreApplication::Resuming +=
        ref new EventHandler<Platform::Object^>(this, &App::OnResuming);
    
  //...

}
```

## <a name="refresh-displayed-content-after-suspension"></a>Обновление отображаемого содержимого после приостановки


При обработке события возобновления приложение имеет возможность обновить свое отображаемое содержимое. Восстановите все приложения, сохраненные обработчиком [**CoreApplication::Suspending**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.suspending), и перезапустите обработку. Разработчикам игр: если вы приостановили обработчик звука, перезапустите его.

```cpp
void App::OnResuming(Platform::Object^ sender, Platform::Object^ args)
{
    // Restore any data or state that was unloaded on suspend. By default, data
    // and state are persisted when resuming from suspend. Note that this event
    // does not occur if the app was previously terminated.

    // Insert your code here.
}
```

Обратный вызов происходит при обработке события диспетчером [**CoreDispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) для окна [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) приложения. Обратный вызов не будет запущен, если вы не вызовете [**CoreDispatcher::ProcessEvents**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.processevents) из основного цикла вашего приложения (реализованного в методе поставщика представлений [**IFrameworkView::Run**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.run)).

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

## <a name="remarks"></a>Примечания


Система приостанавливает работу вашего приложения всякий раз, когда пользователь переключается на другое приложение или на рабочий стол. Система возобновляет работу приложения всякий раз, когда пользователь возвращается в него. Когда система возобновляет работу приложения, содержимое переменных и структур данных остается таким же, каким оно было до приостановки приложения. Система восстанавливает работу приложения точно с места приостановки, поэтому пользователю кажется, что приложение выполнялось в фоновом режиме. Но если приложение было приостановлено в течение длительного времени, оно должно обновить любое отображаемое содержимое, которое могло измениться за это время, и перезапустить все потоки, обрабатывающие отрисовку или звук. Если вы сохранили какие-либо данные о состоянии игры во время предыдущего события приостановки, восстановите их.

## <a name="related-topics"></a>См. также

* [Приостановка приложения (DirectX и C++)](how-to-suspend-an-app-directx-and-cpp.md)
* [Как активировать приложение (DirectX и C++)](how-to-activate-an-app-directx-and-cpp.md)

 

 




