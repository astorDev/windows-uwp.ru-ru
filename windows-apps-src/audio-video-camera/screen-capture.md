---
title: Снимок экрана
description: Пространство имен Windows.Graphics.Capture предоставляет API-интерфейсы для извлечения кадров из окна отображения или приложения, создания видеопотоков или снимков в рамках формирования интерактивного интерфейса для совместной работы.
ms.assetid: 349C959D-9C74-44E7-B5F6-EBDB5CA87B9F
ms.date: 6/14/2019
ms.topic: article
dev_langs:
- csharp
- vb
keywords: windows 10, uwp, снимок экрана
ms.localizationpriority: medium
ms.openlocfilehash: df9f1b536494d2ae7c765eb34a3867f3b2a9e269
ms.sourcegitcommit: 36e692ca19bebfb07ba611bb459db22b5bde650f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153264"
---
# <a name="screen-capture"></a>Снимок экрана

Начиная с Windows 10 версии 1803 пространство имен [Windows.Graphics.Capture](https://docs.microsoft.com/uwp/api/windows.graphics.capture) предоставляет API-интерфейсы для извлечения кадров из окна отображения или приложения, создания видеопотоков или снимков в рамках формирования интерактивного интерфейса для совместной работы.

Снимок экрана позволяет разработчикам вызывать безопасный системный интерфейс, чтобы конечные пользователи могли выбрать экран или окно приложения для захвата, после чего систему рисует желтую границу уведомления вокруг активного элемента. В случае синхронных сеансов захвата желтая граница рисуется вокруг каждого элемента, снимок которого создается.

> [!NOTE]
> Снимок экрана API-интерфейсы поддерживаются только на рабочий стол и иммерсивную смешанной реальности Windows.

## <a name="add-the-screen-capture-capability"></a>Добавление возможности захвата экрана

API-интерфейсов см. в **Windows.Graphics.Capture** пространства имен требуется возможность общей объявлялся в манифесте приложения:

1. Откройте **Package.appxmanifest** в **обозревателе решений**.
2. Перейдите на вкладку **Возможности**.
3. Проверьте **захват графики**.

![Захват графики](images/screen-capture-1.png)

## <a name="launch-the-system-ui-to-start-screen-capture"></a>Запуск системного пользовательского интерфейса для создания снимка экрана

Перед запуском системного пользовательского интерфейса можно проверить, может ли ваше приложение создавать снимки экрана. Существует несколько причин, из-за которых приложение не может использовать снимок экрана — например, устройство не соответствует требованиям к оборудованию или приложение ориентировано на блочный захват экрана. Используйте метод **IsSupported** класса [GraphicsCaptureSession](https://docs.microsoft.com/uwp/api/windows.graphics.capture.graphicscapturesession), чтобы определить, поддерживается ли снимок экрана UWP:

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

```vb
Public Sub OnInitialization()
    If Not GraphicsCaptureSession.IsSupported Then
        CaptureButton.Visibility = Visibility.Collapsed
    End If
End Sub
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

```vb
Public Async Function StartCaptureAsync() As Task
    ' The GraphicsCapturePicker follows the same pattern the
    ' file pickers do.
    Dim picker As New GraphicsCapturePicker
    Dim item As GraphicsCaptureItem = Await picker.PickSingleItemAsync()

    ' The item may be null if the user dismissed the
    ' control without making a selection or hit Cancel.
    If item IsNot Nothing Then
        StartCaptureInternal(item)
    End If
End Function
```

Так как это код пользовательского интерфейса, он должен вызываться в потоке пользовательского интерфейса. Если вы его вызов из кода для страницы приложения (например **MainPage.xaml.cs**) это будет сделано автоматически, но если это не так, можно будет применить его для запуска в потоке пользовательского интерфейса со следующим кодом:

```cs
CoreWindow window = CoreApplication.MainView.CoreWindow;

await window.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, async () =>
{
    await StartCaptureAsync();
});
```

```vb
Dim window As CoreWindow = CoreApplication.MainView.CoreWindow
Await window.Dispatcher.RunAsync(CoreDispatcherPriority.Normal,
                                 Async Sub() Await StartCaptureAsync())
```

## <a name="create-a-capture-frame-pool-and-capture-session"></a>Создание пула кадров захвата и сеанса захвата

С помощью **GraphicsCaptureItem**, вы создадите [Direct3D11CaptureFramePool](https://docs.microsoft.com/uwp/api/windows.graphics.capture.direct3d11captureframepool) с устройства D3D, поддерживаемый формат пикселей (**DXGI\_ФОРМАТ\_ B8G8R8A8\_UNORM**), число нужные кадры (что может быть любое целое число) и кадра размер. Свойство **ContentSize** класса **GraphicsCaptureItem** класс можно использовать в качестве размера кадра:

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

```vb
WithEvents CaptureItem As GraphicsCaptureItem
WithEvents FramePool As Direct3D11CaptureFramePool
Private _canvasDevice As CanvasDevice
Private _session As GraphicsCaptureSession

Private Sub StartCaptureInternal(item As GraphicsCaptureItem)
    CaptureItem = item

    FramePool = Direct3D11CaptureFramePool.Create(
        _canvasDevice, ' D3D device
        DirectXPixelFormat.B8G8R8A8UIntNormalized, ' Pixel format
        2, '  Number of frames
        CaptureItem.Size) ' Size of the buffers
End Sub
```

Затем получите экземпляр класса **GraphicsCaptureSession** для пула **Direct3D11CaptureFramePool**, передав **GraphicsCaptureItem** методу **CreateCaptureSession**:

```cs
_session = _framePool.CreateCaptureSession(_item);
```

```vb
_session = FramePool.CreateCaptureSession(CaptureItem)
```

Если пользователь явно предоставил согласие на захват окна или экрана приложения в системном интерфейсе, **GraphicsCaptureItem** можно связать с несколькими объектами **CaptureSession**. Таким образом ваше приложение может записывать один и тот же элемент для различных процессов.

Для одновременного захвата нескольких элементов приложение должно создать сеанса захвата для каждого записываемого элемента. Для этого требуется вызвать интерфейс средства выбора для каждого элемента.

## <a name="acquire-capture-frames"></a>Получение кадров захвата

После создания пула кадров и сеанса захвата, вызовите метод **StartCapture** метод экземпляра **GraphicsCaptureSession**, чтобы система начала отправлять кадры захвата вашему приложению:

```cs
_session.StartCapture();
```

```vb
_session.StartCapture()
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

```vb
Private Sub FramePool_FrameArrived(sender As Direct3D11CaptureFramePool, args As Object) Handles FramePool.FrameArrived
    ' The FrameArrived event is raised for every frame on the thread
    ' that created the Direct3D11CaptureFramePool. This means we
    ' don't have to do a null-check here, as we know we're the only
    ' one dequeueing frames in our application.  

    ' NOTE Disposing the frame retires it And returns  
    ' the buffer to the pool.

    Using frame = FramePool.TryGetNextFrame()
        ProcessFrame(frame)
    End Using
End Sub
```

Не рекомендуется использовать поток пользовательского интерфейса, если это возможно, для **FrameArrived**, так как это событие вызывается каждый раз, когда доступен новый кадр, что происходит довольно часто. Если вы решили прослушивать **FrameArrived** в потоке пользовательского интерфейса, обратите внимание на действия, которые вы выполняете при каждом событии.

Кроме того, вы можете вручную извлекать кадры с помощью метода **Direct3D11CaptureFramePool.TryGetNextFrame**, пока не получите все нужные кадры.

Объект **Direct3D11CaptureFrame** содержит свойства **размер содержимого**, **Surface** и **SystemRelativeTime**. **SystemRelativeTime** — это  время QPC ([QueryPerformanceCounter](https://docs.microsoft.com/windows/desktop/api/profileapi/nf-profileapi-queryperformancecounter)), которое можно использовать для синхронизации с другими элементами мультимедиа.

## <a name="process-capture-frames"></a>Процесс захвата кадров

Каждый кадр **Direct3D11CaptureFramePool** извлекается при вызове **TryGetNextFrame** и возвращается в соответствии с временем жизни объекта **Direct3D11CaptureFrame**. Для собственных приложений освобождения объекта **Direct3D11CaptureFrame** достаточно для возврата кадра в пул. Для управляемых приложений рекомендуется использовать метод **Direct3D11CaptureFrame.Dispose** (**Close** на C++). **Direct3D11CaptureFrame** реализует интерфейс [IClosable](https://docs.microsoft.com/uwp/api/Windows.Foundation.IClosable), который проецируется как [IDisposable](https://docs.microsoft.com/dotnet/api/system.idisposable?redirectedfrom=MSDN) для вызывающих объектов C#.

Приложения не должны сохранять ссылки на объекты **Direct3D11CaptureFrame** и не должны сохранять ссылки на базовую поверхность Direct3D после возврата кадра.

При обработке кадра приложениям рекомендуется получать блокировку [ID3D11Multithread](https://docs.microsoft.com/windows/desktop/api/d3d11_4/nn-d3d11_4-id3d11multithread) на устройстве, которое связано с объектом **Direct3D11CaptureFramePool**.

Размер базовой поверхности Direct3D всегда равен размеру, указанному при создании (или повторном создании) **Direct3D11CaptureFramePool**. Если содержимое больше кадра, оно обрезается по размеру кадра. Если содержимое меньше кадра, остальная часть кадра содержит неопределенные данные. Приложениям рекомендуется копировать прямоугольник с помощью свойства **ContentSize** для этого кадра **Direct3D11CaptureFrame**, чтобы не отображать неопределенное содержимое.

## <a name="take-a-screenshot"></a>Создание снимка экрана

В нашем примере мы преобразования каждого **Direct3D11CaptureFrame** в [CanvasBitmap](https://microsoft.github.io/Win2D/html/T_Microsoft_Graphics_Canvas_CanvasBitmap.htm), который является частью [Win2D API-интерфейсы](https://microsoft.github.io/Win2D/html/Introduction.htm).

```cs
// Convert our D3D11 surface into a Win2D object.
CanvasBitmap canvasBitmap = CanvasBitmap.CreateFromDirect3D11Surface(
    _canvasDevice,
    frame.Surface);
```

Когда у нас **CanvasBitmap**, его можно сохранить в файл изображения. В следующем примере мы сохраните его как PNG-файл в пользователя **сохранения изображений** папки.

```cs
StorageFolder pictureFolder = KnownFolders.SavedPictures;
StorageFile file = await pictureFolder.CreateFileAsync("test.png", CreationCollisionOption.ReplaceExisting);

using (var fileStream = await file.OpenAsync(FileAccessMode.ReadWrite))
{
    await canvasBitmap.SaveAsync(fileStream, CanvasBitmapFileFormat.Png, 1f);
}
```

## <a name="react-to-capture-item-resizing-or-device-lost"></a>Реагирование на изменение размера элемента или потерю устройства

Во время захвата приложениям может потребоваться изменить аспекты **Direct3D11CaptureFramePool**. В том числе может понадобиться предоставить новое устройство Direct3D, изменить размер буферов кадров или даже изменить количество буферов в пуле. В каждом из этих случаев рекомендуется использовать метод **Recreate** объекта **Direct3D11CaptureFramePool**.

При вызове **Recreate** удаляются все существующие кадры. Это необходимо для предотвращения передачи кадров, базовые поверхности Direct3D которых принадлежат устройству, которое больше недоступно приложению. Поэтому может быть логичным обработать все ожидающие кадры перед вызовом **Recreate**.

## <a name="putting-it-all-together"></a>Объединение элементов

В следующем фрагменте кода приведен пример end-to-end реализации снимки экрана в приложении универсальной платформы Windows. В этом примере у нас есть две кнопки в интерфейсной части: один вызовы **Button_ClickAsync**, а другой вызывает **ScreenshotButton_ClickAsync**.

> [!NOTE]
> Этот фрагмент кода использует [Win2D](https://microsoft.github.io/Win2D/html/Introduction.htm), библиотеку для отрисовки двумерной графики. См. в разделе документации сведения о том, как настроить его для проекта.

```cs
using Microsoft.Graphics.Canvas;
using Microsoft.Graphics.Canvas.UI.Composition;
using System;
using System.Numerics;
using System.Threading.Tasks;
using Windows.Foundation;
using Windows.Graphics;
using Windows.Graphics.Capture;
using Windows.Graphics.DirectX;
using Windows.Storage;
using Windows.UI;
using Windows.UI.Composition;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Hosting;

namespace ScreenCaptureTest
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        // Capture API objects.
        private SizeInt32 _lastSize;
        private GraphicsCaptureItem _item;
        private Direct3D11CaptureFramePool _framePool;
        private GraphicsCaptureSession _session;

        // Non-API related members.
        private CanvasDevice _canvasDevice;
        private CompositionGraphicsDevice _compositionGraphicsDevice;
        private Compositor _compositor;
        private CompositionDrawingSurface _surface;
        private CanvasBitmap _currentFrame;
        private string _screenshotFilename = "test.png";

        public MainPage()
        {
            this.InitializeComponent();
            Setup();
        }

        private void Setup()
        {
            _canvasDevice = new CanvasDevice();

            _compositionGraphicsDevice = CanvasComposition.CreateCompositionGraphicsDevice(
                Window.Current.Compositor,
                _canvasDevice);

            _compositor = Window.Current.Compositor;

            _surface = _compositionGraphicsDevice.CreateDrawingSurface(
                new Size(400, 400),
                DirectXPixelFormat.B8G8R8A8UIntNormalized,
                DirectXAlphaMode.Premultiplied);    // This is the only value that currently works with
                                                    // the composition APIs.

            var visual = _compositor.CreateSpriteVisual();
            visual.RelativeSizeAdjustment = Vector2.One;
            var brush = _compositor.CreateSurfaceBrush(_surface);
            brush.HorizontalAlignmentRatio = 0.5f;
            brush.VerticalAlignmentRatio = 0.5f;
            brush.Stretch = CompositionStretch.Uniform;
            visual.Brush = brush;
            ElementCompositionPreview.SetElementChildVisual(this, visual);
        }

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
            _session.StartCapture();
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

            if ((frame.ContentSize.Width != _lastSize.Width) ||
                (frame.ContentSize.Height != _lastSize.Height))
            {
                needsReset = true;
                _lastSize = frame.ContentSize;
            }

            try
            {
                // Take the D3D11 surface and draw it into a  
                // Composition surface.

                // Convert our D3D11 surface into a Win2D object.
                CanvasBitmap canvasBitmap = CanvasBitmap.CreateFromDirect3D11Surface(
                    _canvasDevice,
                    frame.Surface);

                _currentFrame = canvasBitmap;

                // Helper that handles the drawing for us.
                FillSurfaceWithBitmap(canvasBitmap);
            }

            // This is the device-lost convention for Win2D.
            catch (Exception e) when (_canvasDevice.IsDeviceLost(e.HResult))
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

        private void FillSurfaceWithBitmap(CanvasBitmap canvasBitmap)
        {
            CanvasComposition.Resize(_surface, canvasBitmap.Size);

            using (var session = CanvasComposition.CreateDrawingSession(_surface))
            {
                session.Clear(Colors.Transparent);
                session.DrawImage(canvasBitmap);
            }
        }

        private void ResetFramePool(SizeInt32 size, bool recreateDevice)
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
                catch (Exception e) when (_canvasDevice.IsDeviceLost(e.HResult))
                {
                    _canvasDevice = null;
                    recreateDevice = true;
                }
            } while (_canvasDevice == null);
        }

        private async void Button_ClickAsync(object sender, RoutedEventArgs e)
        {
            await StartCaptureAsync();
        }

        private async void ScreenshotButton_ClickAsync(object sender, RoutedEventArgs e)
        {
            await SaveImageAsync(_screenshotFilename, _currentFrame);
        }

        private async Task SaveImageAsync(string filename, CanvasBitmap frame)
        {
            StorageFolder pictureFolder = KnownFolders.SavedPictures;

            StorageFile file = await pictureFolder.CreateFileAsync(
                filename,
                CreationCollisionOption.ReplaceExisting);

            using (var fileStream = await file.OpenAsync(FileAccessMode.ReadWrite))
            {
                await frame.SaveAsync(fileStream, CanvasBitmapFileFormat.Png, 1f);
            }
        }
    }
}
```

```vb
Imports System.Numerics
Imports Microsoft.Graphics.Canvas
Imports Microsoft.Graphics.Canvas.UI.Composition
Imports Windows.Graphics
Imports Windows.Graphics.Capture
Imports Windows.Graphics.DirectX
Imports Windows.UI
Imports Windows.UI.Composition
Imports Windows.UI.Xaml.Hosting

Partial Public NotInheritable Class MainPage
    Inherits Page

    ' Capture API objects.
    WithEvents CaptureItem As GraphicsCaptureItem
    WithEvents FramePool As Direct3D11CaptureFramePool

    Private _lastSize As SizeInt32
    Private _session As GraphicsCaptureSession

    ' Non-API related members.
    Private _canvasDevice As CanvasDevice
    Private _compositionGraphicsDevice As CompositionGraphicsDevice
    Private _compositor As Compositor
    Private _surface As CompositionDrawingSurface

    Sub New()
        InitializeComponent()
        Setup()
    End Sub

    Private Sub Setup()
        _canvasDevice = New CanvasDevice()
        _compositionGraphicsDevice = CanvasComposition.CreateCompositionGraphicsDevice(Window.Current.Compositor, _canvasDevice)
        _compositor = Window.Current.Compositor
        _surface = _compositionGraphicsDevice.CreateDrawingSurface(
            New Size(400, 400), DirectXPixelFormat.B8G8R8A8UIntNormalized, DirectXAlphaMode.Premultiplied)
        Dim visual = _compositor.CreateSpriteVisual()
        visual.RelativeSizeAdjustment = Vector2.One
        Dim brush = _compositor.CreateSurfaceBrush(_surface)
        brush.HorizontalAlignmentRatio = 0.5F
        brush.VerticalAlignmentRatio = 0.5F
        brush.Stretch = CompositionStretch.Uniform
        visual.Brush = brush
        ElementCompositionPreview.SetElementChildVisual(Me, visual)
    End Sub

    Public Async Function StartCaptureAsync() As Task
        ' The GraphicsCapturePicker follows the same pattern the
        ' file pickers do.
        Dim picker As New GraphicsCapturePicker
        Dim item As GraphicsCaptureItem = Await picker.PickSingleItemAsync()

        ' The item may be null if the user dismissed the
        ' control without making a selection or hit Cancel.
        If item IsNot Nothing Then
            StartCaptureInternal(item)
        End If
    End Function

    Private Sub StartCaptureInternal(item As GraphicsCaptureItem)
        ' Stop the previous capture if we had one.
        StopCapture()

        CaptureItem = item
        _lastSize = CaptureItem.Size

        FramePool = Direct3D11CaptureFramePool.Create(
            _canvasDevice, ' D3D device
            DirectXPixelFormat.B8G8R8A8UIntNormalized, ' Pixel format
            2, '  Number of frames
            CaptureItem.Size) ' Size of the buffers

        _session = FramePool.CreateCaptureSession(CaptureItem)
        _session.StartCapture()
    End Sub

    Private Sub FramePool_FrameArrived(sender As Direct3D11CaptureFramePool, args As Object) Handles FramePool.FrameArrived
        ' The FrameArrived event is raised for every frame on the thread
        ' that created the Direct3D11CaptureFramePool. This means we
        ' don't have to do a null-check here, as we know we're the only
        ' one dequeueing frames in our application.  

        ' NOTE Disposing the frame retires it And returns  
        ' the buffer to the pool.

        Using frame = FramePool.TryGetNextFrame()
            ProcessFrame(frame)
        End Using
    End Sub

    Private Sub CaptureItem_Closed(sender As GraphicsCaptureItem, args As Object) Handles CaptureItem.Closed
        StopCapture()
    End Sub

    Public Sub StopCapture()
        _session?.Dispose()
        FramePool?.Dispose()
        CaptureItem = Nothing
        _session = Nothing
        FramePool = Nothing
    End Sub

    Private Sub ProcessFrame(frame As Direct3D11CaptureFrame)
        ' Resize and device-lost leverage the same function on the
        ' Direct3D11CaptureFramePool. Refactoring it this way avoids
        ' throwing in the catch block below (device creation could always
        ' fail) along with ensuring that resize completes successfully And
        ' isn't vulnerable to device-lost.

        Dim needsReset As Boolean = False
        Dim recreateDevice As Boolean = False

        If (frame.ContentSize.Width <> _lastSize.Width) OrElse
            (frame.ContentSize.Height <> _lastSize.Height) Then
            needsReset = True
            _lastSize = frame.ContentSize
        End If

        Try
            ' Take the D3D11 surface and draw it into a  
            ' Composition surface.

            ' Convert our D3D11 surface into a Win2D object.
            Dim bitmap = CanvasBitmap.CreateFromDirect3D11Surface(
                _canvasDevice,
                frame.Surface)

            ' Helper that handles the drawing for us.
            FillSurfaceWithBitmap(bitmap)
            ' This is the device-lost convention for Win2D.
        Catch e As Exception When _canvasDevice.IsDeviceLost(e.HResult)
            ' We lost our graphics device. Recreate it and reset
            ' our Direct3D11CaptureFramePool.  
            needsReset = True
            recreateDevice = True
        End Try

        If needsReset Then
            ResetFramePool(frame.ContentSize, recreateDevice)
        End If
    End Sub

    Private Sub FillSurfaceWithBitmap(canvasBitmap As CanvasBitmap)
        CanvasComposition.Resize(_surface, canvasBitmap.Size)

        Using session = CanvasComposition.CreateDrawingSession(_surface)
            session.Clear(Colors.Transparent)
            session.DrawImage(canvasBitmap)
        End Using
    End Sub

    Private Sub ResetFramePool(size As SizeInt32, recreateDevice As Boolean)
        Do
            Try
                If recreateDevice Then
                    _canvasDevice = New CanvasDevice()
                End If
                FramePool.Recreate(_canvasDevice, DirectXPixelFormat.B8G8R8A8UIntNormalized, 2, size)
                ' This is the device-lost convention for Win2D.
            Catch e As Exception When _canvasDevice.IsDeviceLost(e.HResult)
                _canvasDevice = Nothing
                recreateDevice = True
            End Try
        Loop While _canvasDevice Is Nothing
    End Sub

    Private Async Sub Button_ClickAsync(sender As Object, e As RoutedEventArgs) Handles CaptureButton.Click
        Await StartCaptureAsync()
    End Sub

End Class
```

## <a name="record-a-video"></a>Запись видео

Если вы хотите сделать видеозапись вашего приложения, поэтому более легко сделать с помощью [пространства имен Windows.Media.AppRecording](https://docs.microsoft.com/uwp/api/windows.media.apprecording). Это является частью рабочего стола расширения пакета SDK, поэтому он работает только на рабочем столе и необходимо добавить ссылку на него из проекта. См. в разделе [Обзор семейств устройств](https://docs.microsoft.com/uwp/extension-sdks/device-families-overview) Дополнительные сведения.

## <a name="see-also"></a>См. также

* [Пространство имен Windows.Graphics.Capture](https://docs.microsoft.com/uwp/api/windows.graphics.capture)
