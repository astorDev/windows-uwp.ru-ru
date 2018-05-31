---
author: eliotcowley
title: Снимок экрана
description: Пространство имен Windows.Graphics.Capture предоставляет API-интерфейсы для извлечения кадров из окна отображения или приложения, создания видеопотоков или снимков в рамках формирования интерактивного интерфейса для совместной работы.
ms.assetid: 349C959D-9C74-44E7-B5F6-EBDB5CA87B9F
ms.author: elcowle
ms.date: 3/1/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, снимок экрана
ms.localizationpriority: medium
ms.openlocfilehash: 2b7883acd351c721b4539141cd46e3c199a8d8a1
ms.sourcegitcommit: ef5a1e1807313a2caa9c9b35ea20b129ff7155d0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
ms.locfileid: "1640069"
---
# <a name="screen-capture"></a>Снимок экрана

Начиная с Windows 10 версии 1803 пространство имен [Windows.Graphics.Capture](https://docs.microsoft.com/uwp/api/windows.graphics.capture) предоставляет API-интерфейсы для извлечения кадров из окна отображения или приложения, создания видеопотоков или снимков в рамках формирования интерактивного интерфейса для совместной работы.

Снимок экрана позволяет разработчикам вызывать безопасный системный интерфейс, чтобы конечные пользователи могли выбрать экран или окно приложения для захвата, после чего систему рисует желтую границу уведомления вокруг активного элемента. В случае синхронных сеансов захвата желтая граница рисуется вокруг каждого элемента, снимок которого создается.

## <a name="add-the-screen-capture-capability"></a>Добавление возможности захвата экрана

Для API-интерфейсов в пространстве имен **Windows.Graphics.Capture** требуется объявить общую возможность в манифесте приложения. Это объявление необходимо добавить непосредственно в файл:
    
1. Щелкните **Package.appxmanifest** правой кнопкой в **обозревателе решений**. 
2. Выберите **Открыть с помощью**. 
3. Выберите **Редактор (текстовый) XML**. 
4. Нажмите кнопку **ОК**.
5. В узле **Пакет** добавьте следующий атрибут: `xmlns:uap6="http://schemas.microsoft.com/appx/manifest/uap/windows10/6"`
6. Также в узле **Пакет** добавьте следующее значение в атрибут **IgnorableNamespaces**: `uap6`
7. В узле **Возможности** добавьте следующий элемент: `<uap6:Capability Name="graphicsCapture"/>`

## <a name="launch-the-system-ui-to-start-screen-capture"></a>Запуск системного пользовательского интерфейса для создания снимка экрана

Перед запуском системного пользовательского интерфейса можно проверить, может ли ваше приложение создавать снимки экрана. Существует несколько причин, из-за которых приложение не может использовать снимок экрана— например, устройство не соответствует требованиям к оборудованию или приложение ориентировано на блочный захват экрана. Используйте метод **IsSupported** класса [GraphicsCaptureSession](https://docs.microsoft.com/uwp/api/windows.graphics.capture.graphicscapturesession), чтобы определить, поддерживается ли снимок экрана UWP:

```cs
// This runs when the application starts.
public void OnInitialization()
{
    if (!GraphicsCaptureSession.IsSupported()) 
    {
        // Hide the capture UI if screen capture is not supported.
        CaptureButton.Visibility = Visibility.Collapsed; 
    }    
}
```

Когда вы убедитесь, что снимок экрана поддерживается, используйте класс [GraphicsCapturePicker](https://docs.microsoft.com/uwp/api/windows.graphics.capture.graphicscapturepicker) для вызова интерфейса системного средства выбора. Конечный пользователь применяет этот интерфейс для выбора экрана или окна приложения, из которого требуется делать снимки экрана. Средство выбора вернет элемент [GraphicsCaptureItem](https://docs.microsoft.com/uwp/api/windows.graphics.capture.graphicscaptureitem), который будет использоваться для создания **GraphicsCaptureSession**:

```cs
public async Task StartCaptureAsync() 
{ 
    // The GraphicsCapturePicker follows the same pattern the 
    // file pickers do. 
    var picker = new GraphicsCapturePicker(); 
    GraphicsCaptureItem item = await picker.PickSingleItemAsync(); 

    // The item may be null if the user dismissed the 
    // control without making a selection or hit Cancel. 
    if (item != null) 
    {
        // We'll define this method later in the document.
        StartCaptureInternal(item); 
    } 
}
```

## <a name="create-a-capture-frame-pool-and-capture-session"></a>Создание пула кадров захвата и сеанса захвата

С помощью **GraphicsCaptureItem** вы создадите [Direct3D11CaptureFramePool](https://docs.microsoft.com/uwp/api/windows.graphics.capture.direct3d11captureframepool) с устройством D3D, укажите поддерживаемый формат пикселей (**DXGI\_FORMAT\_B8G8R8A8\_UNORM**), нужное число кадров (любое целое число) и размер кадра. Свойство **ContentSize** класса **GraphicsCaptureItem** класс можно использовать в качестве размера кадра:

```cs
private GraphicsCaptureItem _item;
private Direct3D11CaptureFramePool _framePool;
private CanvasDevice _canvasDevice;
private GraphicsCaptureSession _session;

public void StartCaptureInternal(GraphicsCaptureItem item) 
{
    _item = item;

    _framePool = Direct3D11CaptureFramePool.Create( 
        _canvasDevice, // D3D device 
        DirectXPixelFormat.B8G8R8A8UIntNormalized, // Pixel format 
        2, // Number of frames 
        _item.Size); // Size of the buffers   
} 
```

Затем получите экземпляр класса **GraphicsCaptureSession** для пула **Direct3D11CaptureFramePool**, передав **GraphicsCaptureItem** методу **CreateCaptureSession**:

```cs
_session = _framePool.CreateCaptureSession(_item);
```

Если пользователь явно предоставил согласие на захват окна или экрана приложения в системном интерфейсе, **GraphicsCaptureItem** можно связать с несколькими объектами **CaptureSession**. Таким образом ваше приложение может записывать один и тот же элемент для различных процессов.

Для одновременного захвата нескольких элементов приложение должно создать сеанса захвата для каждого записываемого элемента. Для этого требуется вызвать интерфейс средства выбора для каждого элемента.

## <a name="acquire-capture-frames"></a>Получение кадров захвата

После создания пула кадров и сеанса захвата, вызовите метод **StartCapture** метод экземпляра **GraphicsCaptureSession**, чтобы система начала отправлять кадры захвата вашему приложению:

```cs
_session.StartCapture();
```

Для получения этих кадров захвата, которые являются объектами [Direct3D11CaptureFrame](https://docs.microsoft.com/uwp/api/windows.graphics.capture.direct3d11captureframe), можно использовать событие **Direct3D11CaptureFramePool.FrameArrived**:

```cs
_framePool.FrameArrived += (s, a) => 
{ 
    // The FrameArrived event fires for every frame on the thread that         
    // created the Direct3D11CaptureFramePool. This means we don't have to 
    // do a null-check here, as we know we're the only one  
    // dequeueing frames in our application.  

    // NOTE: Disposing the frame retires it and returns  
    // the buffer to the pool.
    using (var frame = _framePool.TryGetNextFrame()) 
    {
        // We'll define this method later in the document.
        ProcessFrame(frame); 
    }  
}; 
```

Не рекомендуется использовать поток пользовательского интерфейса, если это возможно, для **FrameArrived**, так как это событие вызывается каждый раз, когда доступен новый кадр, что происходит довольно часто. Если вы решили прослушивать **FrameArrived** в потоке пользовательского интерфейса, обратите внимание на действия, которые вы выполняете при каждом событии.

Кроме того, вы можете вручную извлекать кадры с помощью метода **Direct3D11CaptureFramePool.TryGetNextFrame**, пока не получите все нужные кадры.

Объект **Direct3D11CaptureFrame** содержит свойства **размер содержимого**, **Surface** и **SystemRelativeTime**. **SystemRelativeTime** — это  время QPC ([QueryPerformanceCounter](https://msdn.microsoft.com/library/windows/desktop/ms644904)), которое можно использовать для синхронизации с другими элементами мультимедиа.

## <a name="processing-capture-frames"></a>Обработка кадров захвата

Каждый кадр **Direct3D11CaptureFramePool** извлекается при вызове **TryGetNextFrame** и возвращается в соответствии с временем жизни объекта **Direct3D11CaptureFrame**. Для собственных приложений освобождения объекта **Direct3D11CaptureFrame** достаточно для возврата кадра в пул. Для управляемых приложений рекомендуется использовать метод **Direct3D11CaptureFrame.Dispose** (**Close** на C++). **Direct3D11CaptureFrame** реализует интерфейс [IClosable](https://docs.microsoft.com/uwp/api/Windows.Foundation.IClosable), который проецируется как [IDisposable](https://msdn.microsoft.com/library/system.idisposable.aspx) для вызывающих объектов C#.

Приложения не должны сохранять ссылки на объекты **Direct3D11CaptureFrame** и не должны сохранять ссылки на базовую поверхность Direct3D после возврата кадра.

При обработке кадра приложениям рекомендуется получать блокировку [ID3D11Multithread](https://msdn.microsoft.com/library/windows/desktop/mt644886) на устройстве, которое связано с объектом **Direct3D11CaptureFramePool**.

Размер базовой поверхности Direct3D всегда равен размеру, указанному при создании (или повторном создании) **Direct3D11CaptureFramePool**. Если содержимое больше кадра, оно обрезается по размеру кадра. Если содержимое меньше кадра, остальная часть кадра содержит неопределенные данные. Приложениям рекомендуется копировать прямоугольник с помощью свойства **ContentSize** для этого кадра **Direct3D11CaptureFrame**, чтобы не отображать неопределенное содержимое.

## <a name="react-to-capture-item-resizing-or-device-lost"></a>Реагирование на изменение размера элемента или потерю устройства

Во время захвата приложениям может потребоваться изменить аспекты **Direct3D11CaptureFramePool**. В том числе может понадобиться предоставить новое устройство Direct3D, изменить размер буферов кадров или даже изменить количество буферов в пуле. В каждом из этих случаев рекомендуется использовать метод **Recreate** объекта **Direct3D11CaptureFramePool**.

При вызове **Recreate** удаляются все существующие кадры. Это необходимо для предотвращения передачи кадров, базовые поверхности Direct3D которых принадлежат устройству, которое больше недоступно приложению. Поэтому может быть логичным обработать все ожидающие кадры перед вызовом **Recreate**.

## <a name="putting-it-all-together"></a>Объединение элементов

Следующий фрагмент кода— это полный пример реализации снимка экрана в приложении UWP.

```cs
using Microsoft.Graphics.Canvas; 
using System; 
using System.Threading.Tasks; 
using Windows.Graphics.Capture; 
using Windows.Graphics.DirectX; 
using Windows.UI.Composition; 
 
namespace CaptureSamples 
{ 
    class Sample
    {
        // Capture API objects.
        private Vector2 _lastSize; 
        private GraphicsCaptureItem _item; 
        private Direct3D11CaptureFramePool _framePool; 
        private GraphicsCaptureSession _session; 
 
        // Non-API related members.
        private CanvasDevice _canvasDevice; 
        private CompositionDrawingSurface _surface; 

        public async Task StartCaptureAsync() 
        { 
            // The GraphicsCapturePicker follows the same pattern the 
            // file pickers do. 
            var picker = new GraphicsCapturePicker(); 
            GraphicsCaptureItem item = await picker.PickSingleItemAsync(); 
 
            // The item may be null if the user dismissed the 
            // control without making a selection or hit Cancel. 
            if (item != null) 
            { 
                StartCaptureInternal(item); 
            }
        } 
 
        private void StartCaptureInternal(GraphicsCaptureItem item) 
        { 
             // Stop the previous capture if we had one.
            StopCapture(); 
 
            _item = item; 
            _lastSize = _item.Size; 
 
             _framePool = Direct3D11CaptureFramePool.Create( 
                _canvasDevice, // D3D device 
                DirectXPixelFormat.B8G8R8A8UIntNormalized, // Pixel format 
                2, // Number of frames 
                _item.Size); // Size of the buffers 
 
            _framePool.FrameArrived += (s, a) => 
            { 
                // The FrameArrived event is raised for every frame on the thread
                // that created the Direct3D11CaptureFramePool. This means we 
                // don't have to do a null-check here, as we know we're the only 
                // one dequeueing frames in our application.  
 
                // NOTE: Disposing the frame retires it and returns  
                // the buffer to the pool.
 
                using (var frame = _framePool.TryGetNextFrame()) 
                { 
                    ProcessFrame(frame); 
                }  
            }; 
 
            _item.Closed += (s, a) => 
            { 
                StopCapture(); 
            }; 
 
            _session = _framePool.CreateCaptureSession(_item); 
            _session.Start(); 
        } 
 
        public void StopCapture() 
        { 
            _session?.Dispose(); 
            _framePool?.Dispose(); 
            _item = null; 
            _session = null; 
            _framePool = null; 
        } 
 
        private void ProcessFrame(Direct3D11CaptureFrame frame) 
        { 
            // Resize and device-lost leverage the same function on the
            // Direct3D11CaptureFramePool. Refactoring it this way avoids 
            // throwing in the catch block below (device creation could always 
            // fail) along with ensuring that resize completes successfully and 
            // isn’t vulnerable to device-lost.   
            bool needsReset = false; 
            bool recreateDevice = false; 
 
            if (frame.ContentSize != _lastSize) 
            { 
                needsReset = true; 
                _lastSize = frame.ContentSize; 
            } 
            
            try 
            { 
                // Take the D3D11 surface and draw it into a  
                // Composition surface.
 
                // Convert our D3D11 surface into a Win2D object.
                var canvasBitmap = CanvasBitmap.CreateFromDirect3D11Surface( 
                    _canvasDevice, 
                    frame.Surface); 
 
                // Helper that handles the drawing for us, not shown. 
                FillSurfaceWithBitmap(_surface, canvasBitmap); 
            } 
            // This is the device-lost convention for Win2D.
            catch(Exception e) when (_canvasDevice.IsDeviceLost(e.HResult)) 
            { 
                // We lost our graphics device. Recreate it and reset 
                // our Direct3D11CaptureFramePool.  
                needsReset = true; 
                recreateDevice = true; 
            } 
 
            if (needsReset) 
            { 
                ResetFramePool(frame.ContentSize, recreateDevice); 
            }
        } 
 
        private void ResetFramePool(Vector2 size, bool recreateDevice) 
        { 
            do 
            { 
                try 
                { 
                    if (recreateDevice) 
                    { 
                        _canvasDevice = new CanvasDevice(); 
                    } 
 
                    _framePool.Recreate( 
                        _canvasDevice,  
                        DirectXPixelFormat.B8G8R8A8UIntNormalized,  
                        2, 
                        size); 
                } 
                // This is the device-lost convention for Win2D.
                catch(Exception e) when (_canvasDevice.IsDeviceLost(e.HResult)) 
                { 
                    _canvasDevice = null; 
                    recreateDevice = true; 
                } 
            } while (_canvasDevice == null); 
        } 
    } 
} 
```

## <a name="see-also"></a>См. также:

* [Пространство имен Windows.Graphics.Capture](https://docs.microsoft.com/uwp/api/windows.graphics.capture)