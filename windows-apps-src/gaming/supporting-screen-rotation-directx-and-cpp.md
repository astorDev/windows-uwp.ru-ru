---
author: mtoepke
title: Поддержка ориентации экрана (DirectX и C++)
description: Здесь мы обсудим рекомендации по обработке поворота экрана в приложении UWP на базе DirectX, чтобы графическое оборудование устройства Windows10 использовалось эффективно.
ms.assetid: f23818a6-e372-735d-912b-89cabeddb6d4
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, ориентация экрана, directx
ms.localizationpriority: medium
ms.openlocfilehash: 4ed8739f8ba7b2049af154d458ccaa831b8526a5
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "5548256"
---
# <a name="supporting-screen-orientation-directx-and-c"></a>Поддержка ориентации экрана (DirectX и C++)



Приложение универсальной платформы Windows (UWP) может поддерживать разные варианты ориентации экрана при обработке события [**DisplayInformation::OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268). Здесь мы обсудим рекомендации по обработке поворота экрана в приложении UWP на базе DirectX, чтобы графическое оборудование устройства Windows10 использовалось эффективно.

Вначале запомните, что графическое оборудование всегда выводит данные о пикселях одинаковым образом независимо от ориентации устройства. Windows10 устройств можно определить текущую ориентацию экрана (с помощью какого-либо датчика или программного переключателя) и разрешить пользователям изменять настройки экрана. Из-за этого, Windows10 сам обрабатывает вращения изображений, чтобы обеспечить их «вертикальное положение» на основе ориентации устройства. По умолчанию ваше приложение получает уведомление о том, что в ориентации что-то изменилось (например, размер окна). В этом случае Windows10 немедленно поворачивает изображение перед окончательным отображением. Для трех из четырех определенных экрана ориентаций (обсуждается далее) Windows10 использует дополнительные графические ресурсы и вычисления показе окончательного изображения.

В случае приложений UWP с использованием DirectX объект [**DisplayInformation**](https://msdn.microsoft.com/library/windows/apps/dn264258) обеспечивает базовые данные об ориентации экрана, которые может запросить ваше приложение. По умолчанию выбрана *альбомная* ориентация, где ширина экрана в пикселях превышает высоту. Альтернативой служит *книжная* ориентация, позволяющая повернуть экран на 90 градусов в любом направлении, чтобы ширина стала меньше высоты.

Windows10 указаны четыре режима ориентации экрана.

-   Альбомная — по умолчанию ориентация экрана для Windows10 и считается базового или тождественного угла для поворота (на 0 градусов).
-   Книжная — экран повернут по часовой стрелке на 90 градусов (или против часовой стрелки на 270 градусов).
-   Альбомная, перевернутая — экран повернут на 180 градусов (перевернут вверх ногами).
-   Книжная, перевернутая — экран повернут по часовой стрелке на 270 градусов (или против часовой стрелки на 90 градусов).

Когда экран поворачивается от одной ориентации к другой, Windows10 выполняет внутреннюю операцию поворота, чтобы выровнять прорисованное изображение согласно новой ориентации и пользователь увидел вертикальное изображение на экране.

Кроме того Windows10 показывает автоматическую анимацию перехода для создания пользователю было легче воспринимать переход от одной ориентации к другой. При изменении ориентации экрана для пользователя эти переходы выглядят как фиксированная анимация с увеличением и поворотом изображения на экране. Время выделяет Windows10 приложения для отображения макета в новой ориентации.

В целом, обработка изменений в ориентации экрана выполняется обычно следующим образом.

1.  Воспользуйтесь сочетанием значений границ окна и данных об ориентации экрана, сделав так, чтобы цепочка буферов продолжала соответствовать исходной ориентации экрана устройства.
2.  Уведомите Windows10 об ориентации цепочки буферов, с помощью [**IDXGISwapChain1::SetRotation**](https://msdn.microsoft.com/library/windows/desktop/hh446801).
3.  Измените код рендеринга, чтобы создать изображения, которые соответствуют ориентации устройства.

## <a name="resizing-the-swap-chain-and-pre-rotating-its-contents"></a>Изменение размера цепочки буферов и предварительный поворот ее содержимого


Чтобы осуществить базовое изменение размера экрана и предварительный поворот его содержимого в приложении UWP с использованием DirectX, выполните следующие действия.

1.  Обработайте событие [**DisplayInformation::OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268).
2.  Измените размер цепочки буферов в соответствии с новым размером окна.
3.  Вызовите [** IDXGISwapChain1::SetRotation**](https://msdn.microsoft.com/library/windows/desktop/hh446801), чтобы задать ориентацию цепочки буферов.
4.  Заново создайте любые ресурсы, зависящие от размера окна (например, однобуферные прорисовки и другие буферы данных пикселей).

Теперь рассмотрим эти действия чуть более подробно.

Первое состоит в регистрации обработчика для события [**DisplayInformation::OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268). Это событие создается в вашем приложении каждый раз, когда меняется ориентация экрана (например, при повороте экрана).

Чтобы обработать событие [**DisplayInformation::OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268), подключается обработчик для **DisplayInformation::OrientationChanged** в требуемом методе [**SetWindow**](https://msdn.microsoft.com/library/windows/apps/hh700509), являющемся одним из методов интерфейса [**IFrameworkView**](https://msdn.microsoft.com/library/windows/apps/hh700478), который должен реализовать ваш поставщик представлений.

В этом примере кода обработчик событий для [**DisplayInformation::OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268) — это метод, который называется **OnOrientationChanged**. При вызове события **DisplayInformation::OrientationChanged** оно, в свою очередь, вызывает метод, который называется **SetCurrentOrientation** и вызывается затем событие **CreateWindowSizeDependentResources**.

```cpp
void App::SetWindow(CoreWindow^ window)
{
  // ... Other UI event handlers assigned here ...
  
    currentDisplayInformation->OrientationChanged +=
        ref new TypedEventHandler<DisplayInformation^, Object^>(this, &App::OnOrientationChanged);

  // ...
}
}
```

```cpp
void App::OnOrientationChanged(DisplayInformation^ sender, Object^ args)
{
    m_deviceResources->SetCurrentOrientation(sender->CurrentOrientation);
    m_main->CreateWindowSizeDependentResources();
}

// This method is called in the event handler for the OrientationChanged event.
void DX::DeviceResources::SetCurrentOrientation(DisplayOrientations currentOrientation)
{
    if (m_currentOrientation != currentOrientation)
    {
        m_currentOrientation = currentOrientation;
        CreateWindowSizeDependentResources();
    }
}
```

Теперь нужно изменить размер цепочки буферов согласно новой ориентации и подготовить ее к вращению содержимого графического конвейера при выполнении отрисовки. В этом примере **DirectXBase::CreateWindowSizeDependentResources** — это метод, который вызывает IDXGISwapChain::ResizeBuffers, устанавливает трехмерную и двухмерную матрицу вращения, вызывает SetRotation и заново создает ваши ресурсы.

```cpp
void DX::DeviceResources::CreateWindowSizeDependentResources() 
{
    // Clear the previous window size specific context.
    ID3D11RenderTargetView* nullViews[] = {nullptr};
    m_d3dContext->OMSetRenderTargets(ARRAYSIZE(nullViews), nullViews, nullptr);
    m_d3dRenderTargetView = nullptr;
    m_d2dContext->SetTarget(nullptr);
    m_d2dTargetBitmap = nullptr;
    m_d3dDepthStencilView = nullptr;
    m_d3dContext->Flush();

    // Calculate the necessary render target size in pixels.
    m_outputSize.Width = DX::ConvertDipsToPixels(m_logicalSize.Width, m_dpi);
    m_outputSize.Height = DX::ConvertDipsToPixels(m_logicalSize.Height, m_dpi);
    
    // Prevent zero size DirectX content from being created.
    m_outputSize.Width = max(m_outputSize.Width, 1);
    m_outputSize.Height = max(m_outputSize.Height, 1);

    // The width and height of the swap chain must be based on the window's
    // natively-oriented width and height. If the window is not in the native
    // orientation, the dimensions must be reversed.
    DXGI_MODE_ROTATION displayRotation = ComputeDisplayRotation();

    bool swapDimensions = displayRotation == DXGI_MODE_ROTATION_ROTATE90 || displayRotation == DXGI_MODE_ROTATION_ROTATE270;
    m_d3dRenderTargetSize.Width = swapDimensions ? m_outputSize.Height : m_outputSize.Width;
    m_d3dRenderTargetSize.Height = swapDimensions ? m_outputSize.Width : m_outputSize.Height;

    if (m_swapChain != nullptr)
    {
        // If the swap chain already exists, resize it.
        HRESULT hr = m_swapChain->ResizeBuffers(
            2, // Double-buffered swap chain.
            lround(m_d3dRenderTargetSize.Width),
            lround(m_d3dRenderTargetSize.Height),
            DXGI_FORMAT_B8G8R8A8_UNORM,
            0
            );

        if (hr == DXGI_ERROR_DEVICE_REMOVED || hr == DXGI_ERROR_DEVICE_RESET)
        {
            // If the device was removed for any reason, a new device and swap chain will need to be created.
            HandleDeviceLost();

            // Everything is set up now. Do not continue execution of this method. HandleDeviceLost will reenter this method 
            // and correctly set up the new device.
            return;
        }
        else
        {
            DX::ThrowIfFailed(hr);
        }
    }
    else
    {
        // Otherwise, create a new one using the same adapter as the existing Direct3D device.
        DXGI_SWAP_CHAIN_DESC1 swapChainDesc = {0};

        swapChainDesc.Width = lround(m_d3dRenderTargetSize.Width); // Match the size of the window.
        swapChainDesc.Height = lround(m_d3dRenderTargetSize.Height);
        swapChainDesc.Format = DXGI_FORMAT_B8G8R8A8_UNORM; // This is the most common swap chain format.
        swapChainDesc.Stereo = false;
        swapChainDesc.SampleDesc.Count = 1; // Don't use multi-sampling.
        swapChainDesc.SampleDesc.Quality = 0;
        swapChainDesc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
        swapChainDesc.BufferCount = 2; // Use double-buffering to minimize latency.
        swapChainDesc.SwapEffect = DXGI_SWAP_EFFECT_FLIP_SEQUENTIAL; // All UWP apps must use this SwapEffect.
        swapChainDesc.Flags = 0;    
        swapChainDesc.Scaling = DXGI_SCALING_NONE;
        swapChainDesc.AlphaMode = DXGI_ALPHA_MODE_IGNORE;

        // This sequence obtains the DXGI factory that was used to create the Direct3D device above.
        ComPtr<IDXGIDevice3> dxgiDevice;
        DX::ThrowIfFailed(
            m_d3dDevice.As(&dxgiDevice)
            );

        ComPtr<IDXGIAdapter> dxgiAdapter;
        DX::ThrowIfFailed(
            dxgiDevice->GetAdapter(&dxgiAdapter)
            );

        ComPtr<IDXGIFactory2> dxgiFactory;
        DX::ThrowIfFailed(
            dxgiAdapter->GetParent(IID_PPV_ARGS(&dxgiFactory))
            );

        DX::ThrowIfFailed(
            dxgiFactory->CreateSwapChainForCoreWindow(
                m_d3dDevice.Get(),
                reinterpret_cast<IUnknown*>(m_window.Get()),
                &swapChainDesc,
                nullptr,
                &m_swapChain
                )
            );

        // Ensure that DXGI does not queue more than one frame at a time. This both reduces latency and
        // ensures that the application will only render after each VSync, minimizing power consumption.
        DX::ThrowIfFailed(
            dxgiDevice->SetMaximumFrameLatency(1)
            );
    }

    // Set the proper orientation for the swap chain, and generate 2D and
    // 3D matrix transformations for rendering to the rotated swap chain.
    // Note the rotation angle for the 2D and 3D transforms are different.
    // This is due to the difference in coordinate spaces.  Additionally,
    // the 3D matrix is specified explicitly to avoid rounding errors.

    switch (displayRotation)
    {
    case DXGI_MODE_ROTATION_IDENTITY:
        m_orientationTransform2D = Matrix3x2F::Identity();
        m_orientationTransform3D = ScreenRotation::Rotation0;
        break;

    case DXGI_MODE_ROTATION_ROTATE90:
        m_orientationTransform2D = 
            Matrix3x2F::Rotation(90.0f) *
            Matrix3x2F::Translation(m_logicalSize.Height, 0.0f);
        m_orientationTransform3D = ScreenRotation::Rotation270;
        break;

    case DXGI_MODE_ROTATION_ROTATE180:
        m_orientationTransform2D = 
            Matrix3x2F::Rotation(180.0f) *
            Matrix3x2F::Translation(m_logicalSize.Width, m_logicalSize.Height);
        m_orientationTransform3D = ScreenRotation::Rotation180;
        break;

    case DXGI_MODE_ROTATION_ROTATE270:
        m_orientationTransform2D = 
            Matrix3x2F::Rotation(270.0f) *
            Matrix3x2F::Translation(0.0f, m_logicalSize.Width);
        m_orientationTransform3D = ScreenRotation::Rotation90;
        break;

    default:
        throw ref new FailureException();
    }


    //SDM: only instance of SetRotation
    DX::ThrowIfFailed(
        m_swapChain->SetRotation(displayRotation)
        );

    // Create a render target view of the swap chain back buffer.
    ComPtr<ID3D11Texture2D> backBuffer;
    DX::ThrowIfFailed(
        m_swapChain->GetBuffer(0, IID_PPV_ARGS(&backBuffer))
        );

    DX::ThrowIfFailed(
        m_d3dDevice->CreateRenderTargetView(
            backBuffer.Get(),
            nullptr,
            &m_d3dRenderTargetView
            )
        );

    // Create a depth stencil view for use with 3D rendering if needed.
    CD3D11_TEXTURE2D_DESC depthStencilDesc(
        DXGI_FORMAT_D24_UNORM_S8_UINT, 
        lround(m_d3dRenderTargetSize.Width),
        lround(m_d3dRenderTargetSize.Height),
        1, // This depth stencil view has only one texture.
        1, // Use a single mipmap level.
        D3D11_BIND_DEPTH_STENCIL
        );

    ComPtr<ID3D11Texture2D> depthStencil;
    DX::ThrowIfFailed(
        m_d3dDevice->CreateTexture2D(
            &depthStencilDesc,
            nullptr,
            &depthStencil
            )
        );

    CD3D11_DEPTH_STENCIL_VIEW_DESC depthStencilViewDesc(D3D11_DSV_DIMENSION_TEXTURE2D);
    DX::ThrowIfFailed(
        m_d3dDevice->CreateDepthStencilView(
            depthStencil.Get(),
            &depthStencilViewDesc,
            &m_d3dDepthStencilView
            )
        );
    
    // Set the 3D rendering viewport to target the entire window.
    m_screenViewport = CD3D11_VIEWPORT(
        0.0f,
        0.0f,
        m_d3dRenderTargetSize.Width,
        m_d3dRenderTargetSize.Height
        );

    m_d3dContext->RSSetViewports(1, &m_screenViewport);

    // Create a Direct2D target bitmap associated with the
    // swap chain back buffer and set it as the current target.
    D2D1_BITMAP_PROPERTIES1 bitmapProperties = 
        D2D1::BitmapProperties1(
            D2D1_BITMAP_OPTIONS_TARGET | D2D1_BITMAP_OPTIONS_CANNOT_DRAW,
            D2D1::PixelFormat(DXGI_FORMAT_B8G8R8A8_UNORM, D2D1_ALPHA_MODE_PREMULTIPLIED),
            m_dpi,
            m_dpi
            );

    ComPtr<IDXGISurface2> dxgiBackBuffer;
    DX::ThrowIfFailed(
        m_swapChain->GetBuffer(0, IID_PPV_ARGS(&dxgiBackBuffer))
        );

    DX::ThrowIfFailed(
        m_d2dContext->CreateBitmapFromDxgiSurface(
            dxgiBackBuffer.Get(),
            &bitmapProperties,
            &m_d2dTargetBitmap
            )
        );

    m_d2dContext->SetTarget(m_d2dTargetBitmap.Get());

    // Grayscale text anti-aliasing is recommended for all UWP apps.
    m_d2dContext->SetTextAntialiasMode(D2D1_TEXT_ANTIALIAS_MODE_GRAYSCALE);

}

```

После сохранения текущих значений высоты и ширины окна для повторного вызова этого метода преобразуйте значения, выраженные в независимых от устройства пикселях (DIP), для границ экрана в пиксели. В этом примере вызывается простая функция **ConvertDipsToPixels**, которая выполняет код:

` floor((dips * dpi / 96.0f) + 0.5f);`

Добавляется параметр 0,5f, обеспечивающий округление до ближайшего целого значения.

Кстати, координаты [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) всегда определяются с использованием значений DIP. Для Windows10 и более ранних версиях Windows DIP определяется как 1/96 дюйма и для определения ОС *вверх*. Когда ориентация экрана после поворота становится книжной, приложение меняет местами ширину и высоту **CoreWindow**. При этом размер однобуферной прорисовки (границ) должен измениться соответствующим образом. Поскольку координаты Direct3D всегда представлены в виде физических пикселей, требуется преобразовать значения DIP для **CoreWindow** в целочисленные пиксельные значения перед передачей их в Direct3D для настройки цепочки буферов.

В плане процесса вы прикладываете немного больше усилий, чем если бы вы просто изменили размер цепочки буферов. Фактически вы вращаете Direct2D- и Direct3D-компоненты своего изображения перед составлением их для вывода на экран и сообщаете цепочке буферов о том, что вы выполнили рендеринг результатов при новой ориентации. Далее в примере кода для **DX::DeviceResources::CreateWindowSizeDependentResources** этот процесс рассматривается немного подробней.

-   Определите новую ориентацию экрана. Если экран повернулся, сменив альбомную ориентацию на книжную или наоборот, поменяйте местами значения высоты и ширины. И конечно же перейдите от значений DIP к пикселям при измерении границ экрана.

-   Затем проверьте, создана ли цепочка буферов. Если она не создана, создайте ее, вызвав [**IDXGIFactory2::CreateSwapChainForCoreWindow**](https://msdn.microsoft.com/library/windows/desktop/hh404559). В противном случае измените размер существующих буферов цепочки буферов, применив новые размеры экрана путем вызова [**IDXGISwapchain:ResizeBuffers**](https://msdn.microsoft.com/library/windows/desktop/bb174577). Хотя вам не нужно изменять размер цепочки буферов для события поворота ведь, в конце концов, вы выводите содержимое, которое уже было повернуто вашим конвейером рендеринга существуют другие события изменения размера (например, события прикрепления и заполнения), требующие изменить размер.

-   После этого задайте подходящее преобразование двухмерной или трехмерной матрицы, которое будет применяться соответственно к пикселям или вершинам в конвейере графики во время их рендеринга в цепочку буферов. У нас есть 4 возможных матрицы вращения:

    -   альбомная (DXGI\_MODE\_ROTATION\_IDENTITY)
    -   книжная (DXGI\_MODE\_ROTATION\_ROTATE270)
    -   альбомная, перевернутая (DXGI\_MODE\_ROTATION\_ROTATE180)
    -   книжная, перевернутая (DXGI\_MODE\_ROTATION\_ROTATE90)

    Правильная матрица выбирается на основе данных, предоставляемых Windows10 (например, результатов [**DisplayInformation::OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268)) для определения ориентации экрана, и она будет умножена на координаты каждого пикселя (Direct2D) или вершины (Direct3D) в сцене, так их с учетом ориентации экрана. Учтите, что в Direct2D в качестве начала координат экрана определен верхний левый угол, тогда как в Direct3D начало координат определено как логический центр окна.

> **Примечание**  Подробнее о двухмерных трансформациях, используемый для поворота и способах их определения см. в разделе [Определение матриц для двухмерного вращения экрана (2-D)](#appendix-a-applying-matrices-for-screen-rotation-2-d). Подробнее о трехмерных трансформациях, используемых при вращении, см. в разделе [Определение матриц для трехмерного вращения экрана](#appendix-b-applying-matrices-for-screen-rotation-3-d).

 

Теперь настает важный момент. Вызовите [**IDXGISwapChain1::SetRotation**](https://msdn.microsoft.com/library/windows/desktop/hh446801) и обеспечьте его обновленной матрицей вращения. Например, следующей:

`m_swapChain->SetRotation(rotation);`

Также необходимо сохранить выбранную матрицу вращения там, где ваш метод рендеринга сможет обратиться к ней при вычислении новой проекции. Вы сможете использовать эту матрицу при рендеринге окончательной трехмерной проекции или составлении окончательного двухмерного макета. При этом она не выполняет эти действия автоматически за вас.

Теперь создайте новую однобуферную прорисовку для повернутого трехмерного вида, а также новый буфер "глубина-трафарет" для представления. Задайте окно просмотра трехмерного рендеринга для повернутой сцены путем вызова [**ID3D11DeviceContext:RSSetViewports**](https://msdn.microsoft.com/library/windows/desktop/ff476480).

Наконец, если у вас есть двухмерные изображения, которые необходимо повернуть или разместить, создайте двухмерную однобуферную прорисовку в качестве записываемого растрового файла для цепочки буферов с измененным размером с помощью [**ID2D1DeviceContext::CreateBitmapFromDxgiSurface**](https://msdn.microsoft.com/library/windows/desktop/hh404482) и составьте свой новый макет для обновленной ориентации. Задайте любые свойства, необходимые для однобуферной прорисовки. Например, режим сглаживания (как в примере кода).

Теперь отобразите на экране цепочку буферов.

## <a name="reduce-the-rotation-delay-by-using-corewindowresizemanager"></a>Сократите задержку поворота с помощью CoreWindowResizeManager.


По умолчанию Windows10 предоставляет короткие но заметный период времени для любого приложения, независимо от его модели или языка для выполнения вращения изображения. Однако есть вероятность, что когда ваше приложение выполняет вычисление вращения с помощью одного из описанных здесь методов, оно будет завершено задолго до истечения этого периода времени. Вы хотите повернуть время вспять и завершить анимацию вращения, правильно? Тогда настал черед [**CoreWindowResizeManager**](https://msdn.microsoft.com/library/windows/apps/jj215603).

Пользоваться [**CoreWindowResizeManager**](https://msdn.microsoft.com/library/windows/apps/jj215603) нужно следующим образом. При создании события [**DisplayInformation::OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268) вызовите [**CoreWindowResizeManager::GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/hh404170) в обработчике для этого события, чтобы получить экземпляр **CoreWindowResizeManager**, а затем, когда макет для новой ориентации будет завершен и выведен на экран, вызовите [**NotifyLayoutCompleted**](https://msdn.microsoft.com/library/windows/apps/jj215605), чтобы сообщить Windows о том, что можно выполнить анимацию вращения и отобразить экран приложения.

Вот как может выглядеть код в вашем обработчике событий для [**DisplayInformation::OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268):

```cpp
CoreWindowResizeManager^ resizeManager = Windows::UI::Core::CoreWindowResizeManager::GetForCurrentView();

// ... build the layout for the new display orientation ...

resizeManager->NotifyLayoutCompleted();
```

Когда пользователь поворачивает ориентацию экрана, Windows10 отображается анимацию независимо от вашего приложения как обратной связи пользователю. Анимация состоит из трех частей, расположенных в следующем порядке.

-   Windows10 сжимает исходное изображение.
-   Windows10 сохраняет его на время, необходимое для повторного создания нового макета. Это тот период времени, который вы хотели бы сократить, поскольку, возможно, вашему приложению весь он не нужен.
-   Когда истекает срок показа окна макета или при получении уведомления о завершении создания макета, Windows поворачивает изображение, а затем плавно переходит к новой ориентации с увеличением изображения.

Как предполагается в третьем пункте когда приложение вызывает [**NotifyLayoutCompleted**](https://msdn.microsoft.com/library/windows/apps/jj215605)Windows10 истекает период ожидания, завершается анимация вращения и возвращает элемент управления в приложение, которое теперь рисует на экране с новой ориентацией. Общий эффект состоит в том, что теперь ваше приложение стало чуть более легким на подъем и отзывчивым. И работает оно немного эффективней!

## <a name="appendix-a-applying-matrices-for-screen-rotation-2-d"></a>Приложение А. Применение матриц для двухмерного вращения экрана


Возможно, вы заметили в примера кода из раздела [Изменение размера цепочки буферов и предварительный поворот ее содержимого](#resizing-the-swap-chain-and-pre-rotating-its-contents) (и в [примере поворота цепочки буферов DXGI](http://go.microsoft.com/fwlink/p/?linkid=257600)), что у нас были отдельные матрицы поворота для вывода Direct2D и вывода Direct3D. Давайте сначала рассмотрим двухмерные матрицы.

Мы не можем применить одинаковые матрицы вращения к содержимому Direct2D и Direct3D по двум причинам.

-   Во-первых, в них используются разные модели декартовых координат. В Direct2D применяется правило правой руки, согласно которому положительное значение координаты Y увеличивается при перемещении вверх от начала координат. Однако в Direct3D действует правило левой руки, где положительное значение координаты Y увеличивается при перемещении вправо от начала координат. В результате начало координат экрана в случае Direct2D расположено в верхнем левом углу, тогда как начало координат (плоскость проекции) в случае Direct3D находится в нижнем левом углу. Подробнее см. в разделе [Трехмерные системы координат](https://msdn.microsoft.com/library/windows/apps/bb324490.aspx) .

    ![direct3d coordinate system.](images/direct3d-origin.png)![direct2d coordinate system.](images/direct2d-origin.png)

-   Во-вторых, матрицы трехмерного вращения необходимо задавать явно во избежание ошибок при округлении.

цепочка буферов предполагает, что начало координат расположено в левом нижнем углу. Поэтому вам нужно выполнить вращение, чтобы соотнести систему координат Direct2D, основанную на правиле правой руки, с системой координат с правилом левой руки для цепочки буферов. В частности, необходимо переместить изображение при переходе к новой ориентации, основанной на правиле левой руки. Для этого нужно умножить матрицу вращения на матрицу трансляции для начала координат повернутой системы координат. Также надо преобразовать изображение из пространства координат [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) в пространство координат цепочки буферов. Кроме того, ваше приложение должно согласованно выполнять это преобразование, когда однобуферная прорисовка Direct2D подключена к цепочке буферов. Однако если ваше приложение рисует на промежуточных поверхностях, не связанных напрямую с цепочкой буферов, не выполняйте это преобразование пространства координат.

Ваш код для выбора правильной матрицы из четырех возможных вариантов поворота может выглядеть следующим образом (не забывайте о трансляции при переходе к началу координат новой системы координат):

```cpp
   
// Set the proper orientation for the swap chain, and generate 2D and
// 3D matrix transformations for rendering to the rotated swap chain.
// Note the rotation angle for the 2D and 3D transforms are different.
// This is due to the difference in coordinate spaces.  Additionally,
// the 3D matrix is specified explicitly to avoid rounding errors.

switch (displayRotation)
{
case DXGI_MODE_ROTATION_IDENTITY:
    m_orientationTransform2D = Matrix3x2F::Identity();
    m_orientationTransform3D = ScreenRotation::Rotation0;
    break;

case DXGI_MODE_ROTATION_ROTATE90:
    m_orientationTransform2D = 
        Matrix3x2F::Rotation(90.0f) *
        Matrix3x2F::Translation(m_logicalSize.Height, 0.0f);
    m_orientationTransform3D = ScreenRotation::Rotation270;
    break;

case DXGI_MODE_ROTATION_ROTATE180:
    m_orientationTransform2D = 
        Matrix3x2F::Rotation(180.0f) *
        Matrix3x2F::Translation(m_logicalSize.Width, m_logicalSize.Height);
    m_orientationTransform3D = ScreenRotation::Rotation180;
    break;

case DXGI_MODE_ROTATION_ROTATE270:
    m_orientationTransform2D = 
        Matrix3x2F::Rotation(270.0f) *
        Matrix3x2F::Translation(0.0f, m_logicalSize.Width);
    m_orientationTransform3D = ScreenRotation::Rotation90;
    break;

default:
    throw ref new FailureException();
}
    
```

После получения правильной матрицы вращения и начала координат для двухмерного изображения задайте его, вставив вызов [**ID2D1DeviceContext::SetTransform**](https://msdn.microsoft.com/library/windows/desktop/dd742857) между вызовами [**ID2D1DeviceContext::BeginDraw**](https://msdn.microsoft.com/library/windows/desktop/dd371768) и [**ID2D1DeviceContext::EndDraw**](https://msdn.microsoft.com/library/windows/desktop/dd371924).

**Предупреждение**  Direct2D нет стека преобразования. Если ваше приложение также использует [**ID2D1DeviceContext::SetTransform**](https://msdn.microsoft.com/library/windows/desktop/dd742857) как компонент кода прорисовки, эту матрицу необходимо будет затем умножить с учетом любого другого выполненного преобразования.

 

```cpp
    ID2D1DeviceContext* context = m_deviceResources->GetD2DDeviceContext();
    Windows::Foundation::Size logicalSize = m_deviceResources->GetLogicalSize();

    context->SaveDrawingState(m_stateBlock.Get());
    context->BeginDraw();

    // Position on the bottom right corner.
    D2D1::Matrix3x2F screenTranslation = D2D1::Matrix3x2F::Translation(
        logicalSize.Width - m_textMetrics.layoutWidth,
        logicalSize.Height - m_textMetrics.height
        );

    context->SetTransform(screenTranslation * m_deviceResources->GetOrientationTransform2D());

    DX::ThrowIfFailed(
        m_textFormat->SetTextAlignment(DWRITE_TEXT_ALIGNMENT_TRAILING)
        );

    context->DrawTextLayout(
        D2D1::Point2F(0.f, 0.f),
        m_textLayout.Get(),
        m_whiteBrush.Get()
        );

    // Ignore D2DERR_RECREATE_TARGET here. This error indicates that the device
    // is lost. It will be handled during the next call to Present.
    HRESULT hr = context->EndDraw();
```

В следующий раз при выводе на экран цепочки буферов ваше двухмерное изображение будет повернуто согласно новой ориентации экрана.

## <a name="appendix-b-applying-matrices-for-screen-rotation-3-d"></a>Приложение Б. Применение матриц для трехмерного вращения экрана


В примере кода раздела [Изменение размера цепочки буферов и предварительный поворот ее содержимого](#resizing-the-swap-chain-and-pre-rotating-its-contents) (и в [пример поворота цепочки буферов DXGI](http://go.microsoft.com/fwlink/p/?linkid=257600)) мы определили особую матрицу преобразования для каждой из возможных ориентаций экрана. Теперь давайте взглянем на матрицы для поворота трехмерных сцен. Как и раньше, вы создаете набор матриц для каждой из четырех возможных ориентаций. Во избежание ошибок при округлении, вызывающих небольшие искажения графики, явно объявите эти матрицы в своем коде.

Матрицы трехмерного вращения настраиваются следующим образом. Матрицы, указанные в приведенном ниже примере кода, относятся к категории стандартных матриц вращения для поворотов на 0, 90, 180 и 270 градусов вершин, определяющих точки в трехмерном пространстве сцены камеры. Значения координат \[x, y, z\] каждой вершины в этой сцене умножаются на данную матрицу вращения при вычислении двухмерной проекции сцены.

```cpp
   
// 0-degree Z-rotation
static const XMFLOAT4X4 Rotation0( 
    1.0f, 0.0f, 0.0f, 0.0f,
    0.0f, 1.0f, 0.0f, 0.0f,
    0.0f, 0.0f, 1.0f, 0.0f,
    0.0f, 0.0f, 0.0f, 1.0f
    );

// 90-degree Z-rotation
static const XMFLOAT4X4 Rotation90(
    0.0f, 1.0f, 0.0f, 0.0f,
    -1.0f, 0.0f, 0.0f, 0.0f,
    0.0f, 0.0f, 1.0f, 0.0f,
    0.0f, 0.0f, 0.0f, 1.0f
    );

// 180-degree Z-rotation
static const XMFLOAT4X4 Rotation180(
    -1.0f, 0.0f, 0.0f, 0.0f,
    0.0f, -1.0f, 0.0f, 0.0f,
    0.0f, 0.0f, 1.0f, 0.0f,
    0.0f, 0.0f, 0.0f, 1.0f
    );

// 270-degree Z-rotation
static const XMFLOAT4X4 Rotation270( 
    0.0f, -1.0f, 0.0f, 0.0f,
    1.0f, 0.0f, 0.0f, 0.0f,
    0.0f, 0.0f, 1.0f, 0.0f,
    0.0f, 0.0f, 0.0f, 1.0f
    );            
    }
```

Тип вращения цепочки буферов задается с помощью вызова [**IDXGISwapChain1::SetRotation**](https://msdn.microsoft.com/library/windows/desktop/hh446801). Например, следующего:

`   m_swapChain->SetRotation(rotation);`

Теперь включите в свой метод рендеринга определенный код. Например, такой:

``` syntax
struct ConstantBuffer // This struct is provided for illustration.
{
    // Other constant buffer matrices and data are defined here.

    float4x4 projection; // Current matrix for projection
} ;
ConstantBuffer  m_constantBufferData;          // Constant buffer resource data

// ...

// Rotate the projection matrix as it will be used to render to the rotated swap chain.
m_constantBufferData.projection = mul(m_constantBufferData.projection, m_rotationTransform3D);
```

Теперь при вызове вашего метода рендеринга он умножает текущую матрицу вращения (указанную переменной класса **m\_orientationTransform3D**) на текущую матрицу проекции, и результаты этой операции берутся в качестве новой матрицы проекции для вашего модуля подготовки. Выведите цепочку буферов на экран, чтобы просмотреть сцену при обновленной ориентации экрана.

 

 




