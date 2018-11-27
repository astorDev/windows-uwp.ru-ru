---
title: Настройка ресурсов DirectX и отображение изображения
description: В этом разделе мы покажем, как создать устройство Direct3D, цепочку буферов и представление целевого объекта обработки, а также как представить обработанное изображение для отображения.
ms.assetid: d54d96fe-3522-4acb-35f4-bb11c3a5b064
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, игры, directx, ресурсы, изображения
ms.localizationpriority: medium
ms.openlocfilehash: b650f77627e02427b2861a2e6d0df7d1fc86831a
ms.sourcegitcommit: 681c70f964210ab49ac5d06357ae96505bb78741
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "7697627"
---
# <a name="set-up-directx-resources-and-display-an-image"></a>Настройка ресурсов DirectX и отображение изображения



В этом разделе мы покажем, как создать устройство Direct3D, цепочку буферов и представление целевого объекта обработки, а также как представить обработанное изображение для отображения.

**Цель:** настройка ресурсов DirectX в приложении универсальной платформы Windows (UWP) на языке C++ и отображение одного цвета.

## <a name="prerequisites"></a>Необходимые условия


Предполагается, что вы знакомы с C++. Также вы должны быть знакомы с основными принципами программирования графики.

**Время выполнения:** 20 минут.

## <a name="instructions"></a>Инструкции

### <a name="1-declaring-direct3d-interface-variables-with-comptr"></a>1. Объявление переменных интерфейса Direct3D с помощью ComPtr

Мы объявляем переменные интерфейса Direct3D по шаблону [смарт-указателя](https://msdn.microsoft.com/library/windows/apps/hh279674.aspx) ComPtr из библиотеки шаблонов C++ для среды выполнения Windows (WRL), поэтому можно безопасно управлять временем существования этих переменных. В таком случае мы можем использовать эти переменные, чтобы получить доступ к классу [**ComPtr class**](https://msdn.microsoft.com/library/windows/apps/br244983.aspx) и его членам. Пример.

```cpp
    ComPtr<ID3D11RenderTargetView> m_renderTargetView;
    m_d3dDeviceContext->OMSetRenderTargets(
        1,
        m_renderTargetView.GetAddressOf(),
        nullptr // Use no depth stencil.
        );
```

Если вы объявите [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582) с помощью ComPtr, то сможете затем использовать метод **GetAddressOf** шаблона ComPtr, чтобы передать адрес указателя на **ID3D11RenderTargetView** (**ID3D11RenderTargetView) методу [**ID3D11DeviceContext::OMSetRenderTargets**](https://msdn.microsoft.com/library/windows/desktop/ff476464). **OMSetRenderTargets** привязывает однобуферную прорисовку к [стадии слияния вывода](https://msdn.microsoft.com/library/windows/desktop/bb205120), чтобы указать однобуферную прорисовку в качестве цели вывода.

После запуска образец приложения инициализируется, загружается и затем готов к выполнению.

### <a name="2-creating-the-direct3d-device"></a>2. Создание устройства Direct3D

Чтобы использовать API Direct3D для обработки сцены, необходимо сначала создать устройство Direct3D, представляющее видеоадаптер. Чтобы создать устройство Direct3D, необходимо вызвать функцию [**D3D11CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082). Мы определяем уровни с9.1 по11.1 в массиве значений [**D3D\_FEATURE\_LEVEL**](https://msdn.microsoft.com/library/windows/desktop/ff476329). Direct3D по порядку проходит через массив и возвращает высший уровень поддерживаемого компонента. Таким образом, чтобы получить наивысший доступный уровень компонента, мы перечисляем записи массива **D3D\_FEATURE\_LEVEL** от высшего к низшему. Чтобы ресурсы Direct3D могли взаимодействовать с Direct2D, мы передаем флаг [**D3D11\_CREATE\_DEVICE\_BGRA\_SUPPORT**](https://msdn.microsoft.com/library/windows/desktop/ff476107#D3D11_CREATE_DEVICE_BGRA_SUPPORT) параметру *Флаги*. Если мы используем отладочную сборку, мы также передаем флаг [**D3D11\_CREATE\_DEVICE\_DEBUG**](https://msdn.microsoft.com/library/windows/desktop/ff476107#D3D11_CREATE_DEVICE_DEBUG). Дополнительные сведения о приложениях отладки см. в разделе [Отладка приложений с помощью уровня отладки](https://msdn.microsoft.com/library/windows/desktop/jj200584).

Мы получаем устройство Direct3D 11.1 ([**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575)) и контекст устройства ([**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598)) с помощью запроса устройства Direct3D 11 и его контекста, возвращаемые [**D3D11CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082).

```cpp
        // First, create the Direct3D device.

        // This flag is required in order to enable compatibility with Direct2D.
        UINT creationFlags = D3D11_CREATE_DEVICE_BGRA_SUPPORT;

#if defined(_DEBUG)
        // If the project is in a debug build, enable debugging via SDK Layers with this flag.
        creationFlags |= D3D11_CREATE_DEVICE_DEBUG;
#endif

        // This array defines the ordering of feature levels that D3D should attempt to create.
        D3D_FEATURE_LEVEL featureLevels[] =
        {
            D3D_FEATURE_LEVEL_11_1,
            D3D_FEATURE_LEVEL_11_0,
            D3D_FEATURE_LEVEL_10_1,
            D3D_FEATURE_LEVEL_10_0,
            D3D_FEATURE_LEVEL_9_3,
            D3D_FEATURE_LEVEL_9_1
        };

        ComPtr<ID3D11Device> d3dDevice;
        ComPtr<ID3D11DeviceContext> d3dDeviceContext;
        DX::ThrowIfFailed(
            D3D11CreateDevice(
                nullptr,                    // Specify nullptr to use the default adapter.
                D3D_DRIVER_TYPE_HARDWARE,
                nullptr,                    // leave as nullptr if hardware is used
                creationFlags,              // optionally set debug and Direct2D compatibility flags
                featureLevels,
                ARRAYSIZE(featureLevels),
                D3D11_SDK_VERSION,          // always set this to D3D11_SDK_VERSION
                &d3dDevice,
                nullptr,
                &d3dDeviceContext
                )
            );

        // Retrieve the Direct3D 11.1 interfaces.
        DX::ThrowIfFailed(
            d3dDevice.As(&m_d3dDevice)
            );

        DX::ThrowIfFailed(
            d3dDeviceContext.As(&m_d3dDeviceContext)
            );
```

### <a name="3-creating-the-swap-chain"></a>3. Создание цепочки буферов

Затем мы создаем цепочку буферов, используемую устройством для визуализации и отображения. Для описания цепочки буферов мы объявляем и инициализируем структуру [**DXGI\_SWAP\_CHAIN\_DESC1**](https://msdn.microsoft.com/library/windows/desktop/hh404528). Затем мы настраиваем цепочку буферов на основе модели отражений (то есть цепочку буферов, в которой член **SwapEffect** имеет значение [**DXGI\_SWAP\_EFFECT\_FLIP\_SEQUENTIAL**](https://msdn.microsoft.com/library/windows/desktop/bb173077#DXGI_SWAP_EFFECT_FLIP_SEQUENTIAL)) и присваиваем члену **Формат** значение [**DXGI\_FORMAT\_B8G8R8A8\_UNORM**](https://msdn.microsoft.com/library/windows/desktop/bb173059#DXGI_FORMAT_B8G8R8A8_UNORM). Мы присваиваем члену **Count** структуры [**DXGI\_SAMPLE\_DESC**](https://msdn.microsoft.com/library/windows/desktop/bb173072), указанной членом **SampleDesc**, значение 1, а члену **Quality** структуры **DXGI\_SAMPLE\_DESC** — 0, так как модель переворота не поддерживает сглаживание нескольких примеров (Microsoft Active Accessibility). Мы присваиваем члену **BufferCount** значение 2, и поэтому цепочки буферов могут использовать кадровый буфер для предоставления видеоадаптеру и задний буфер для однобуферной прорисовки.

Мы получаем базовое устройство DXGI, опрашивая устройство Direct3D 11.1. Чтобы свести к минимуму потребление энергии, что важно для устройств, работающих от аккумулятора, например ноутбуков и планшетов, необходимо вызвать метод [**IDXGIDevice1::SetMaximumFrameLatency**](https://msdn.microsoft.com/library/windows/desktop/ff471334), указав 1 как максимальное количество кадров заднего буфера, которое DXGI может поместить в очередь. Благодаря этому приложение обрабатывается только после выдержки интервала между кадрами.

Для окончательного создания цепочки буферов мы должны получить родительское производство от устройства DXGI. Мы вызываем метод [**IDXGIDevice::GetAdapter**](https://msdn.microsoft.com/library/windows/desktop/bb174531), чтобы получить адаптер для устройства, а затем вызывает метод [**IDXGIObject::GetParent**](https://msdn.microsoft.com/library/windows/desktop/bb174542) на адаптере для получения родительского производства ([**IDXGIFactory2**](https://msdn.microsoft.com/library/windows/desktop/hh404556)). Для создания цепочки буферов мы вызываем метод [**IDXGIFactory2::CreateSwapChainForCoreWindow**](https://msdn.microsoft.com/library/windows/desktop/hh404559) с дескриптором цепочки буферов и основное окно приложения.

```cpp
            // If the swap chain does not exist, create it.
            DXGI_SWAP_CHAIN_DESC1 swapChainDesc = {0};

            swapChainDesc.Stereo = false;
            swapChainDesc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
            swapChainDesc.Scaling = DXGI_SCALING_NONE;
            swapChainDesc.Flags = 0;

            // Use automatic sizing.
            swapChainDesc.Width = 0;
            swapChainDesc.Height = 0;

            // This is the most common swap chain format.
            swapChainDesc.Format = DXGI_FORMAT_B8G8R8A8_UNORM;

            // Don't use multi-sampling.
            swapChainDesc.SampleDesc.Count = 1;
            swapChainDesc.SampleDesc.Quality = 0;

            // Use two buffers to enable the flip effect.
            swapChainDesc.BufferCount = 2;

            // We recommend using this swap effect for all applications.
            swapChainDesc.SwapEffect = DXGI_SWAP_EFFECT_FLIP_SEQUENTIAL;


            // Once the swap chain description is configured, it must be
            // created on the same adapter as the existing D3D Device.

            // First, retrieve the underlying DXGI Device from the D3D Device.
            ComPtr<IDXGIDevice2> dxgiDevice;
            DX::ThrowIfFailed(
                m_d3dDevice.As(&dxgiDevice)
                );

            // Ensure that DXGI does not queue more than one frame at a time. This both reduces
            // latency and ensures that the application will only render after each VSync, minimizing
            // power consumption.
            DX::ThrowIfFailed(
                dxgiDevice->SetMaximumFrameLatency(1)
                );

            // Next, get the parent factory from the DXGI Device.
            ComPtr<IDXGIAdapter> dxgiAdapter;
            DX::ThrowIfFailed(
                dxgiDevice->GetAdapter(&dxgiAdapter)
                );

            ComPtr<IDXGIFactory2> dxgiFactory;
            DX::ThrowIfFailed(
                dxgiAdapter->GetParent(IID_PPV_ARGS(&dxgiFactory))
                );

            // Finally, create the swap chain.
            CoreWindow^ window = m_window.Get();
            DX::ThrowIfFailed(
                dxgiFactory->CreateSwapChainForCoreWindow(
                    m_d3dDevice.Get(),
                    reinterpret_cast<IUnknown*>(window),
                    &swapChainDesc,
                    nullptr, // Allow on all displays.
                    &m_swapChain
                    )
                );
```

### <a name="4-creating-the-render-target-view"></a>4. Создание представления однобуферной обработки

Для преобразования графики для просмотра в окне мы должны создать представление однобуферной обработки. Мы вызываем метод [**IDXGISwapChain::GetBuffer**](https://msdn.microsoft.com/library/windows/desktop/bb174570), чтобы получить задний буфер цепочки буферов и использовать его при создании представления однобуферной прорисовки. Мы указываем задний буфер в качестве 2D-текстуры ([**ID3D11Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635)). Чтобы создать представление однобуферной прорисовки, мы вызываем метод [**ID3D11Device::CreateRenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476517) с задним буфером цепочки буферов. Мы должны задать отрисовку всего основного окна, указав порт представления ([**D3D11\_VIEWPORT**](https://msdn.microsoft.com/library/windows/desktop/ff476260)) в качестве полного размера заднего буфера цепочки буферов. Мы используем порт представления в обращении к методу [**ID3D11DeviceContext::RSSetViewports**](https://msdn.microsoft.com/library/windows/desktop/ff476480) для связывания порта представления и [стадии растеризатора](https://msdn.microsoft.com/library/windows/desktop/bb205125) конвейера. Стадия растеризатора преобразует векторную информацию в растровое изображение. В данном случае преобразование нам не нужно, так как мы просто отображаем сплошной цвет.

```cpp
        // Once the swap chain is created, create a render target view.  This will
        // allow Direct3D to render graphics to the window.

        ComPtr<ID3D11Texture2D> backBuffer;
        DX::ThrowIfFailed(
            m_swapChain->GetBuffer(0, IID_PPV_ARGS(&backBuffer))
            );

        DX::ThrowIfFailed(
            m_d3dDevice->CreateRenderTargetView(
                backBuffer.Get(),
                nullptr,
                &m_renderTargetView
                )
            );


        // After the render target view is created, specify that the viewport,
        // which describes what portion of the window to draw to, should cover
        // the entire window.

        D3D11_TEXTURE2D_DESC backBufferDesc = {0};
        backBuffer->GetDesc(&backBufferDesc);

        D3D11_VIEWPORT viewport;
        viewport.TopLeftX = 0.0f;
        viewport.TopLeftY = 0.0f;
        viewport.Width = static_cast<float>(backBufferDesc.Width);
        viewport.Height = static_cast<float>(backBufferDesc.Height);
        viewport.MinDepth = D3D11_MIN_DEPTH;
        viewport.MaxDepth = D3D11_MAX_DEPTH;

        m_d3dDeviceContext->RSSetViewports(1, &viewport);
```

### <a name="5-presenting-the-rendered-image"></a>5. Представление обработанного изображения

Для непрерывной обработки и вывода сцены на экран мы воспользуемся бесконечным циклом.

В этом примере мы вызываем следующие методы:

1.  [**ID3D11DeviceContext::OMSetRenderTargets**](https://msdn.microsoft.com/library/windows/desktop/ff476464) — чтобы указать однобуферную прорисовку в качестве цели вывода.
2.  [**ID3D11DeviceContext::ClearRenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476388) — чтобы очистить однобуферную прорисовку до сплошного цвета.
3.  [**IDXGISwapChain::Present**](https://msdn.microsoft.com/library/windows/desktop/bb174576) — чтобы представить отрисованное изображение в окне.

Так как мы ранее определили максимальное значение задержки кадра как 1, Windows обычно замедляет цикл обработки до частоты обновления экрана, как правило, около 60 Гц. Windows замедляет цикл отрисовки, переводя приложение в спящий режим при вызове метода [**Present**](https://msdn.microsoft.com/library/windows/desktop/bb174576). Windows переводит приложение в спящий режим до обновления экрана.

```cpp
        // Enter the render loop.  Note that UWP apps should never exit.
        while (true)
        {
            // Process events incoming to the window.
            m_window->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);

            // Specify the render target we created as the output target.
            m_d3dDeviceContext->OMSetRenderTargets(
                1,
                m_renderTargetView.GetAddressOf(),
                nullptr // Use no depth stencil.
                );

            // Clear the render target to a solid color.
            const float clearColor[4] = { 0.071f, 0.04f, 0.561f, 1.0f };
            m_d3dDeviceContext->ClearRenderTargetView(
                m_renderTargetView.Get(),
                clearColor
                );

            // Present the rendered image to the window.  Because the maximum frame latency is set to 1,
            // the render loop will generally be throttled to the screen refresh rate, typically around
            // 60 Hz, by sleeping the application on Present until the screen is refreshed.
            DX::ThrowIfFailed(
                m_swapChain->Present(1, 0)
                );
        }
```

### <a name="6-resizing-the-app-window-and-the-swap-chains-buffer"></a>6. Изменение размеров окна приложения и буфера цепочки буферов

Если изменяется размер окна приложения, приложение должно изменить размеры буферов цепочки буферов, повторно создать представление однобуферной обработки, а затем представить преобразованное изображение с измененными размерами. Чтобы изменить размеры буферов цепочки буферов, мы вызываем метод [**IDXGISwapChain::ResizeBuffers**](https://msdn.microsoft.com/library/windows/desktop/bb174577). В этом вызове мы оставляем количество и формат буферов без изменений (параметр *BufferCount* со значением "два" и параметр *NewFormat* со значением [**DXGI\_FORMAT\_B8G8R8A8\_UNORM**](https://msdn.microsoft.com/library/windows/desktop/bb173059#DXGI_FORMAT_B8G8R8A8_UNORM)). Мы делаем размер заднего буфера цепочки буферов таким же, как измененный размер окна. Изменив размеры буферов цепочки буферов, мы создаем новую однобуферную прорисовку и представляем новое обработанное изображение, как при его инициализации.

```cpp
            // If the swap chain already exists, resize it.
            DX::ThrowIfFailed(
                m_swapChain->ResizeBuffers(
                    2,
                    0,
                    0,
                    DXGI_FORMAT_B8G8R8A8_UNORM,
                    0
                    )
                );
```

## <a name="summary-and-next-steps"></a>Сводка и дальнейшие действия


Мы создали устройство Direct3D, цепочку буферов и представление целевого объекта обработки, а также представили обработанное изображение для отображения.

Теперь мы также нарисуем на экране треугольник.

[Создание шейдеров и рисование примитивов](creating-shaders-and-drawing-primitives.md)

 

 




