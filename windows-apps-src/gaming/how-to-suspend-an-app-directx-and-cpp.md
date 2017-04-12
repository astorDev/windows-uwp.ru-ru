---
author: mtoepke
title: "Приостановка работы приложения (DirectX и C++)"
description: "В этом разделе показано, как сохранять важное состояние системы и данные приложения, когда система приостанавливает работу приложения универсальной платформы Windows (UWP), использующего DirectX."
ms.assetid: 5dd435e5-ec7e-9445-fed4-9c0d872a239e
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, игры, приостановка, directx"
ms.openlocfilehash: 028350f3e4bf6bda5a72663c009e8117c9311b3e
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="how-to-suspend-an-app-directx-and-c"></a>Приостановка работы приложения (DirectX и C++)


\[ Обновлено для приложений UWP в Windows10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом разделе показано, как сохранять важное состояние системы и данные приложения, когда система приостанавливает работу приложения универсальной платформы Windows (UWP), использующего DirectX.

## <a name="register-the-suspending-event-handler"></a>Регистрация обработчика события приостановки


Сначала выполните регистрацию для обработки события [**CoreApplication::Suspending**](https://msdn.microsoft.com/library/windows/apps/br205860), возникающего при переходе приложения в состояние приостановки в результате действий пользователя или системы.

Добавьте этот код в реализацию метода [**IFrameworkView::Initialize**](https://msdn.microsoft.com/library/windows/apps/hh700495) вашего поставщика представлений:

```cpp
void App::Initialize(CoreApplicationView^ applicationView)
{
  //...
  
    CoreApplication::Suspending +=
        ref new EventHandler<SuspendingEventArgs^>(this, &App::OnSuspending);

  //...
}
```

## <a name="save-any-app-data-before-suspending"></a>Сохранение всех данных приложения перед приостановкой


При обработке события [**CoreApplication::Suspending**](https://msdn.microsoft.com/library/windows/apps/br205860) приложение имеет возможность сохранить свои важные данные в функции обработчика. Приложение должно использовать для синхронного сохранения своих простых данных API хранилища [**LocalSettings**](https://msdn.microsoft.com/library/windows/apps/br241622). Если вы разрабатываете игру, сохраните всю важную информацию о состоянии игры. Не забудьте приостановить обработку звука!

Теперь реализуйте обратный вызов. Сохраните данные приложения в этом методе.

```cpp
void App::OnSuspending(Platform::Object^ sender, SuspendingEventArgs^ args)
{
    // Save app state asynchronously after requesting a deferral. Holding a deferral
    // indicates that the application is busy performing suspending operations. Be
    // aware that a deferral may not be held indefinitely. After about five seconds,
    // the app will be forced to exit.
    SuspendingDeferral^ deferral = args->SuspendingOperation->GetDeferral();

    create_task([this, deferral]()
    {
        m_deviceResources->Trim();

        // Insert your code here.

        deferral->Complete();
    });
}
```

Обратный вызов должен завершиться в течение пяти секунд. Во время обратного вызова вы должны запросить отсрочку, вызвав метод [**SuspendingOperation::GetDeferral**](https://msdn.microsoft.com/library/windows/apps/br224690), который запускает обратный отсчет. После того как приложение завершит операцию сохранения, вызовите метод [**SuspendingDeferral::Complete**](https://msdn.microsoft.com/library/windows/apps/br224685), чтобы сообщить системе о том, что ваше приложение готово к приостановке. Если не запрашивать отсрочку, или если приложению требуется больше пяти секунд для сохранения данных, оно будет приостановлено автоматически.

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

## <a name="call-trim"></a>Вызов Trim()


Начиная с Windows8.1, все приложения Магазина Windows, использующие DirectX, должны вызывать метод [**IDXGIDevice3::Trim**](https://msdn.microsoft.com/library/windows/desktop/dn280346) при приостановке. Такой вызов сообщает графическому драйверу, что можно освободить все временные буферы, выделенные приложению. Это снижает вероятность завершения приложения для высвобождения памяти в приостановленном состоянии. Это требование необходимо для сертификации Windows8.1.

```cpp
void App::OnSuspending(Platform::Object^ sender, SuspendingEventArgs^ args)
{
    // Save app state asynchronously after requesting a deferral. Holding a deferral
    // indicates that the application is busy performing suspending operations. Be
    // aware that a deferral may not be held indefinitely. After about five seconds,
    // the app will be forced to exit.
    SuspendingDeferral^ deferral = args->SuspendingOperation->GetDeferral();

    create_task([this, deferral]()
    {
        m_deviceResources->Trim();

        // Insert your code here.

        deferral->Complete();
    });
}

// Call this method when the app suspends. It provides a hint to the driver that the app 
// is entering an idle state and that temporary buffers can be reclaimed for use by other apps.
void DX::DeviceResources::Trim()
{
    ComPtr<IDXGIDevice3> dxgiDevice;
    m_d3dDevice.As(&dxgiDevice);

    dxgiDevice->Trim();
}
```

## <a name="release-any-exclusive-resources-and-file-handles"></a>Освобождение всех монопольных ресурсов и дескрипторов файлов


При обработке события [**CoreApplication::Suspending**](https://msdn.microsoft.com/library/windows/apps/br205860) приложение также получает возможность освободить монопольные ресурсы и дескрипторы файлов. Явное освобождение монопольных ресурсов и дескрипторов файлов обеспечивает доступ к ним других приложений на то время, пока ваше приложение их не использует. Приложение, активируемое после завершения работы, должно возвращаться к использованию тех же монопольных ресурсов и дескрипторов файлов.

## <a name="remarks"></a>Примечания


Система приостанавливает работу вашего приложения всякий раз, когда пользователь переключается на другое приложение или на рабочий стол. Система возобновляет работу вашего приложения всякий раз, когда пользователь возвращается в него. Когда система возобновляет работу приложения, содержимое переменных и структур данных остается таким же, каким оно было до приостановки приложения. Система восстанавливает работу приложения точно с места приостановки, поэтому пользователю кажется, что приложение выполнялось в фоновом режиме.

Система старается сохранить ваше приложение и его данные в памяти, пока его выполнение приостановлено. Тем не менее, если ресурсов системы для сохранения вашего приложения в памяти недостаточно, система завершает его работу. Когда пользователь снова переключается на приостановленное приложение, работа которого была завершена, система передает событие [**Activated**](https://msdn.microsoft.com/library/windows/apps/br225018) и должна восстановить данные приложения в обработчике события **CoreApplicationView::Activated**.

Система не уведомляет приложение о завершении его работы, поэтому в случае приостановки приложение должно сохранять свои данные и освобождать монопольные ресурсы и дескрипторы файлов, а затем восстанавливать их во время активации после завершения работы.

## <a name="related-topics"></a>Связанные разделы

* [Возобновление работы приложения (DirectX и C++)](how-to-resume-an-app-directx-and-cpp.md)
* [Активация приложения (DirectX и C++)](how-to-activate-an-app-directx-and-cpp.md)

 

 




