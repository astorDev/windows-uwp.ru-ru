---
author: scottmill
ms.assetid: 16ad97eb-23f1-0264-23a9-a1791b4a5b95
title: Собственное взаимодействие DirectX и Direct2D композиции с BeginDraw и EndDraw
description: API Windows.UI.Composition предоставляет собственные интерфейсы взаимодействия, позволяющие перемещать содержимое непосредственно в компоновщик.
---
# Собственное взаимодействие DirectX и Direct2D композиции с BeginDraw и EndDraw

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

API Windows.UI.Composition предоставляет собственные интерфейсы взаимодействия [**ICompositorInterop**](https://msdn.microsoft.com/library/windows/apps/Mt620068), [**ICompositionDrawingSurfaceInterop**](https://msdn.microsoft.com/library/windows/apps/Mt620058) и [**ICompositionGraphicsDeviceInterop**](https://msdn.microsoft.com/library/windows/apps/Mt620065), позволяющие перемещать содержимое непосредственно в компоновщик.

Структура собственного взаимодействия построена вокруг объектов поверхности, которые поддерживаются текстурами DirectX. Поверхности создаются из объекта фабрики с именем [**CompositionGraphicsDevice**](https://msdn.microsoft.com/library/windows/apps/Dn706749). Этот объект поддерживается базовым объектом устройства Direct2D или Direct3D, который используется для выделения видеопамяти для поверхностей. API композиции никогда не создает базовое устройство DirectX. Его должно создать приложение и затем передать объекту **CompositionGraphicsDevice**. Приложение может создавать более одного объекта **CompositionGraphicsDevice** одновременно. Приложение может использовать то же устройство DirectX в качестве устройства отрисовки для нескольких объектов **CompositionGraphicsDevice**.

## Создание поверхности

Каждый объект [**CompositionGraphicsDevice**](https://msdn.microsoft.com/library/windows/apps/Dn706749) выступает в качестве фабрики поверхностей. Каждая поверхность создается с начальными размерами (они могут быть равны 0,0), но без действительных пикселей. Поверхность в своем начальном состоянии может быть сразу же использована в визуальном дереве, например с помощью [**CompositionSurfaceBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589415) и [**SpriteVisual**](https://msdn.microsoft.com/library/windows/apps/Mt589433), но в своем начальном состоянии она не оказывает никакого влияния на выводимое изображение. Она полностью прозрачна для всех целей, даже когда указанный режим альфа-канала является непрозрачным.

Иногда устройства DirectX могут стать непригодными к использованию. Причиной этого, помимо прочего, может стать ситуация, когда приложение передает недопустимые аргументы некоторым API-интерфейсам DirectX, когда графический адаптер сбрасывается системой или же когда обновляется драйвер. В Direct3D имеется API, с помощью которого приложение может асинхронно определить, является ли устройство недоступным по какой-либо причине. Если устройство DirectX недоступно, приложению необходимо отказаться от его использования, создать новое устройство и передать его всем объектам [**CompositionGraphicsDevice**](https://msdn.microsoft.com/library/windows/apps/Dn706749), которые ранее были связаны с неисправным устройством DirectX.

## Загрузка пикселей на поверхность

Для загрузки пикселей на поверхность приложение должно вызвать метод [**BeginDraw**](https://msdn.microsoft.com/en-us/library/windows/apps/mt620059.aspx), который возвращает интерфейс DirectX, представляющий текстуру или контекст Direct2D в зависимости от того, что запрашивает приложение. После этого приложение должно отрисовать или передать пиксели на эту текстуру. Когда приложение завершает это действие, оно должно вызвать метод [**EndDraw**](https://msdn.microsoft.com/library/windows/apps/mt620060). Только в этот момент новые пиксели становятся доступны композиции, но они по-прежнему не будут отображаться на экране до тех пор, пока не будут зафиксированы изменения в визуальном дереве. Если изменения в визуальном дереве фиксируются перед вызовом **EndDraw** , то обновление, которое выполняется в настоящий момент, не отображается на экране, а поверхность продолжает отображать то содержимое, которое было на ней до вызова **BeginDraw**. При вызове функции **EndDraw** текстура или указатель на контекст Direct2D, возвращаемые функцией "BeginDraw", становятся недействительными. Приложение никогда не должно кэшировать этот указатель после вызова **EndDraw**.

Приложение может вызывать функцию "BeginDraw" только для одной поверхности за один раз для любого данного [**CompositionGraphicsDevice**](https://msdn.microsoft.com/library/windows/apps/Dn706749). После вызова [**BeginDraw**](https://msdn.microsoft.com/en-us/library/windows/apps/mt620059.aspx) приложение должно вызвать [**EndDraw**](https://msdn.microsoft.com/library/windows/apps/mt620060) для этой поверхности, прежде чем вызывать **BeginDraw** для другой поверхности. Так как API работает быстро, приложение должно синхронизировать эти вызовы, если требуется выполнять отрисовку из нескольких рабочих потоков. Если приложению нужно прервать отрисовку одной поверхности и временно переключиться на отрисовку другой поверхности, оно может использовать метод [**SuspendDraw**](https://msdn.microsoft.com/en-us/library/windows/apps/mt620064.aspx). Это дает возможность успешно выполнить **BeginDraw**, но делает обновление первой поверхности недоступным для экранной композиции. Это позволяет приложению выполнять несколько обновлений в виде транзакций. Когда работа с поверхностью приостановлена, приложение может продолжить обновление, вызвав метод [**ResumeDraw**](https://msdn.microsoft.com/library/windows/apps/mt620062). Оно также может объявить, что обновление завершено, вызвав **EndDraw**. Это означает, что только одна поверхность может активно обновляться в конкретный момент времени для любого данного **CompositionGraphicsDevice**. Каждое графическое устройство сохраняет это состояние независимо от остальных, поэтому приложение может выполнять отрисовку на двух поверхностях одновременно, если они принадлежат разным графическим устройствам. Однако такая процедура не позволяет совместно использовать видеопамять этих двух поверхностей в пуле и, таким образом, снижает эффективность работы с памятью.

Методы [**BeginDraw**](https://msdn.microsoft.com/en-us/library/windows/apps/mt620059.aspx), [**SuspendDraw**](https://msdn.microsoft.com/en-us/library/windows/apps/mt620064.aspx), [**ResumeDraw**](https://msdn.microsoft.com/library/windows/apps/mt620062) и [**EndDraw**](https://msdn.microsoft.com/library/windows/apps/mt620060) возвращают ошибку, если приложение выполняет недопустимую операцию (например, передает недопустимые аргументы или вызывает **BeginDraw** для одной поверхности до вызова **EndDraw** для другой). Такие сбои указывают на ошибки в приложении, поэтому ожидается, что эти сбои будут обработаны с помощью быстрого прекращения. Функция **BeginDraw** также может возвращать ошибку, если базовое устройство DirectX недоступно. Этот сбой не является критическим, так как приложение может повторно создать устройство DirectX и повторить попытку. В связи с этим ожидается, что при отсутствии доступа к устройству приложение будет просто пропускать отрисовку. Если по какой-то причине **BeginDraw** завершается с ошибкой, приложение не должно также вызывать **EndDraw**, так как операция с самого начала не была успешной.

## Прокрутка

Когда приложение вызывает [**BeginDraw**](https://msdn.microsoft.com/en-us/library/windows/apps/mt620059.aspx), то из соображений производительности не гарантируется, что содержимое возвращаемой текстуры будет соответствовать предыдущему содержимому поверхности. Приложение должно исходить из того, что содержимое содержит случайные данные. Вследствие этого приложение должно убедиться, что затронуты все пиксели и либо очистить поверхность перед отрисовкой, либо отрисовать достаточный объем непрозрачного содержимого, чтобы покрыть весь обновленный прямоугольник. Это, а также тот факт, что указатель текстуры действителен только между вызовами **BeginDraw** и [**EndDraw**](https://msdn.microsoft.com/library/windows/apps/mt620060), не позволяет приложению копировать предыдущее содержимое с поверхности. По этой причине мы рекомендуем использовать метод [**Scroll**](https://msdn.microsoft.com/library/windows/apps/mt620063), который позволяет приложению выполнять копирование пикселей для этой же поверхности.

## Пример использования

В следующем примере показан очень простой сценарий, в котором приложение создает поверхности рисования и использует [**BeginDraw**](https://msdn.microsoft.com/en-us/library/windows/apps/mt620059.aspx) и [**EndDraw**](https://msdn.microsoft.com/library/windows/apps/mt620060) для заполнения поверхности текстом. Приложение использует DirectWrite для размещения текста (подробности не показаны), а затем — Direct2D для его отрисовки. Графическое устройство композиции принимает устройство Direct2D непосредственно во время инициализации. Это позволяет функции **BeginDraw** возвратить указатель интерфейса ID2D1DeviceContext. Это значительно эффективнее создания контекста Direct2D и оболочки для возвращаемого интерфейса ID3D11Texture2D при каждой операции рисования.

```cpp
//------------------------------------------------------------------------------
//
// Copyright (C) Microsoft. All rights reserved.
//
//------------------------------------------------------------------------------

#include "stdafx.h"

using namespace Microsoft::WRL;
using namespace Windows::Foundation;
using namespace Windows::Graphics::DirectX;
using namespace Windows::UI::Composition;

// This is an app-provided helper to render lines of text
class SampleText
{
private:
    // The text to draw
    ComPtr<IDWriteTextLayout> _text;

    // The composition surface that we use in the visual tree
    ComPtr<ICompositionDrawingSurfaceInterop> _drawingSurfaceInterop;

    // The device that owns the surface
    ComPtr<ICompositionGraphicsDevice> _compositionGraphicsDevice;

    // For managing our event notifier
    EventRegistrationToken _deviceReplacedEventToken;

public:
    SampleText(IDWriteTextLayout* text, ICompositionGraphicsDevice* compositionGraphicsDevice) :
        _text(text),
        _compositionGraphicsDevice(compositionGraphicsDevice)
    {
        // Create the surface just big enough to hold the formatted text block.
        DWRITE_TEXT_METRICS metrics;
        FailFastOnFailure(text->GetMetrics(&metrics));
        Windows::Foundation::Size surfaceSize = { metrics.width, metrics.height };
        ComPtr<ICompositionDrawingSurface> drawingSurface;
        FailFastOnFailure(_compositionGraphicsDevice->CreateDrawingSurface(
            surfaceSize,
            DirectXPixelFormat::DirectXPixelFormat_B8G8R8A8UIntNormalized,
            DirectXAlphaMode::DirectXAlphaMode_Ignore,
            &drawingSurface));

        // Cache the interop pointer, since that's what we always use.
        FailFastOnFailure(drawingSurface.As(&_drawingSurfaceInterop));

        // Draw the text
        DrawText();

        // If the rendering device is lost, the application will recreate and replace it. We then
        // own redrawing our pixels.
        FailFastOnFailure(_compositionGraphicsDevice->add_RenderingDeviceReplaced(
            Callback<RenderingDeviceReplacedEventHandler>([this](
                ICompositionGraphicsDevice* source, IRenderingDeviceReplacedEventArgs* args)
                -> HRESULT
            {
                // Draw the text again
                DrawText();
                return S_OK;
            }).Get(),
            &_deviceReplacedEventToken));
    }

    ~SampleText()
    {
        FailFastOnFailure(_compositionGraphicsDevice->remove_RenderingDeviceReplaced(
            _deviceReplacedEventToken));
    }

    // Return the underlying surface to the caller
    ComPtr<ICompositionSurface> get_Surface()
    {
        // To the caller, the fact that we have a drawing surface is an implementation detail.
        // Return the base interface instead
        ComPtr<ICompositionSurface> surface;
        FailFastOnFailure(_drawingSurfaceInterop.As(&surface));
        return surface;
    }

private:
    // We may detect device loss on BeginDraw calls. This helper handles this condition or other
    // errors.
    bool CheckForDeviceRemoved(HRESULT hr)
    {
        if (SUCCEEDED(hr))
        {
            // Everything is fine -- go ahead and draw
            return true;
        }
        else if (hr == DXGI_ERROR_DEVICE_REMOVED)
        {
            // We can't draw at this time, but this failure is recoverable. Just skip drawing for
            // now. We will be asked to draw again once the Direct3D device is recreated
            return false;
        }
        else
        {
            // Any other error is unexpected and, therefore, fatal
            FailFast();
        }
    }

    // Renders the text into our composition surface
    void DrawText()
    {
        // Begin our update of the surface pixels. If this is our first update, we are required
        // to specify the entire surface, which nullptr is shorthand for (but, as it works out,
        // any time we make an update we touch the entire surface, so we always pass nullptr).
        ComPtr<ID2D1DeviceContext> d2dDeviceContext;
        POINT offset;
        if (CheckForDeviceRemoved(_drawingSurfaceInterop->BeginDraw(nullptr,
            __uuidof(ID2D1DeviceContext), &d2dDeviceContext, &offset)))
        {
            // Create a solid color brush for the text. A more sophisticated application might want
            // to cache and reuse a brush across all text elements instead, taking care to recreate
            // it in the event of device removed.
            ComPtr<ID2D1SolidColorBrush> brush;
            FailFastOnFailure(d2dDeviceContext->CreateSolidColorBrush(
                D2D1::ColorF(D2D1::ColorF::Black, 1.0f), &brush));

            // Draw the line of text at the specified offset, which corresponds to the top-left
            // corner of our drawing surface. Notice we don't call BeginDraw on the D2D device
            // context; this has already been done for us by the composition API.
            d2dDeviceContext->DrawTextLayout(D2D1::Point2F(offset.x, offset.y), _text.Get(),
                brush.Get());

            // Our update is done. EndDraw never indicates rendering device removed, so any
            // failure here is unexpected and, therefore, fatal.
            FailFastOnFailure(_drawingSurfaceInterop->EndDraw());
        }
    }
};

class SampleApp
{
    ComPtr<ICompositor> _compositor;
    ComPtr<ID2D1Device> _d2dDevice;
    ComPtr<ICompositionGraphicsDevice> _compositionGraphicsDevice;
    std::vector<ComPtr<SampleText>> _textSurfaces;

public:
    // Run once when the application starts up
    void Initialize(ICompositor* compositor)
    {
        // Cache the compositor (created outside of this method)
        _compositor = compositor;

        // Create a Direct2D device (helper implementation not shown here)
        FailFastOnFailure(CreateDirect2DDevice(&_d2dDevice));

        // To create a composition graphics device, we need to QI for another interface
        ComPtr<ICompositorInterop> compositorInterop;
        FailFastOnFailure(_compositor.As(&compositorInterop));

        // Create a graphics device backed by our D3D device
        FailFastOnFailure(compositorInterop->CreateGraphicsDevice(
            _d2dDevice.Get(),
            &_compositionGraphicsDevice));
    }

    // Called when Direct3D signals the device lost event
    void OnDirect3DDeviceLost()
    {
        // Create a new device
        FailFastOnFailure(CreateDirect2DDevice(_d2dDevice.ReleaseAndGetAddressOf()));

        // Restore our composition graphics device to good health
        ComPtr<ICompositionGraphicsDeviceInterop> compositionGraphicsDeviceInterop;
        FailFastOnFailure(_compositionGraphicsDevice.As(&compositionGraphicsDeviceInterop));
        FailFastOnFailure(compositionGraphicsDeviceInterop->SetRenderingDevice(_d2dDevice.Get()));
    }

    // Create a surface that is asynchronously filled with an image
    ComPtr<ICompositionSurface> CreateSurfaceFromTextLayout(IDWriteTextLayout* text)
    {
        // Create our wrapper object that will handle downloading and decoding the image (assume
        // throwing new here)
        SampleText* textSurface = new SampleText(text, _compositionGraphicsDevice.Get());

        // Keep our image alive
        _textSurfaces.push_back(textSurface);

        // The caller is only interested in the underlying surface
        return textSurface->get_Surface();
    }

    // Create a visual that holds an image
    ComPtr<IVisual> CreateVisualFromTextLayout(IDWriteTextLayout* text)
    {
        // Create a sprite visual
        ComPtr<ISpriteVisual> spriteVisual;
        FailFastOnFailure(_compositor->CreateSpriteVisual(&spriteVisual));

        // The sprite visual needs a brush to hold the image
        ComPtr<ICompositionSurfaceBrush> surfaceBrush;
        FailFastOnFailure(_compositor->CreateSurfaceBrushWithSurface(
            CreateSurfaceFromTextLayout(text).Get(),
            &surfaceBrush));

        // Associate the brush with the visual
        ComPtr<ICompositionBrush> brush;
        FailFastOnFailure(surfaceBrush.As(&brush));
        FailFastOnFailure(spriteVisual->put_Brush(brush.Get()));

        // Return the visual to the caller as the base class
        ComPtr<IVisual> visual;
        FailFastOnFailure(spriteVisual.As(&visual));

        return visual;
    }

private:
    // This helper (implementation not shown here) creates a Direct2D device and registers
    // for a device loss notification on the underlying Direct3D device. When that notification is
    // raised, assume the OnDirect3DDeviceLost method is called.
    HRESULT CreateDirect2DDevice(ID2D1Device** ppDevice);
};
```

 

 






<!--HONumber=May16_HO2-->


