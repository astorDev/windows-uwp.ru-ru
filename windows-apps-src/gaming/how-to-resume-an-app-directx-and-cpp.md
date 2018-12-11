---
title: Возобновление работы приложения (DirectX и C++)
description: В этом разделе показано, как восстановить важные данные приложения, когда система возобновляет работу приложения универсальной платформы Windows (UWP), использующего DirectX.
ms.assetid: 5e6bb673-6874-ace5-05eb-f88c045f2178
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, возобновление, directx
ms.localizationpriority: medium
ms.openlocfilehash: f0aa60061ae9fc14392bfe4beb0693ba50fda0df
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8947345"
---
# <a name="how-to-resume-an-app-directx-and-c"></a>Возобновление работы приложения (DirectX и C++)



В этом разделе показано, как восстановить важные данные приложения, когда система возобновляет работу приложения универсальной платформы Windows (UWP), использующего DirectX.

## <a name="register-the-resuming-event-handler"></a>Регистрация обработчика события возобновления


Выполните регистрацию для обработки события [**CoreApplication::Resuming**](https://msdn.microsoft.com/library/windows/apps/br205859), которое сигнализирует о том, что пользователь переключился с вашего приложения на другую задачу, а затем вернулся к нему.

Добавьте этот код в реализацию метода [**IFrameworkView::Initialize**](https://msdn.microsoft.com/library/windows/apps/hh700495) вашего поставщика представлений:

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


При обработке события возобновления приложение имеет возможность обновить свое отображаемое содержимое. Восстановите все приложения, сохраненные обработчиком [**CoreApplication::Suspending**](https://msdn.microsoft.com/library/windows/apps/br205860), и перезапустите обработку. Разработчикам игр: если вы приостановили обработчик звука, перезапустите его.

```cpp
void App::OnResuming(Platform::Object^ sender, Platform::Object^ args)
{
    // Restore any data or state that was unloaded on suspend. By default, data
    // and state are persisted when resuming from suspend. Note that this event
    // does not occur if the app was previously terminated.

    // Insert your code here.
}
```

Обратный вызов происходит при обработке события диспетчером [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) для окна [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) приложения. Обратный вызов не будет запущен, если вы не вызовете [**CoreDispatcher::ProcessEvents**](https://msdn.microsoft.com/library/windows/apps/br208215) из основного цикла вашего приложения (реализованного в методе поставщика представлений [**IFrameworkView::Run**](https://msdn.microsoft.com/library/windows/apps/hh700505)).

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

## <a name="remarks"></a>Комментарии


Система приостанавливает работу вашего приложения всякий раз, когда пользователь переключается на другое приложение или на рабочий стол. Система возобновляет работу вашего приложения всякий раз, когда пользователь возвращается в него. Когда система возобновляет работу приложения, содержимое переменных и структур данных остается таким же, каким оно было до приостановки приложения. Система восстанавливает работу приложения точно с места приостановки, поэтому пользователю кажется, что приложение выполнялось в фоновом режиме. Но если приложение было приостановлено в течение длительного времени, оно должно обновить любое отображаемое содержимое, которое могло измениться за это время, и перезапустить все потоки, обрабатывающие отрисовку или звук. Если вы сохранили какие-либо данные о состоянии игры во время предыдущего события приостановки, восстановите их.

## <a name="related-topics"></a>Связанные разделы

* [Приостановка работы приложения (DirectX и C++)](how-to-suspend-an-app-directx-and-cpp.md)
* [Активация приложения (DirectX и C++)](how-to-activate-an-app-directx-and-cpp.md)

 

 




