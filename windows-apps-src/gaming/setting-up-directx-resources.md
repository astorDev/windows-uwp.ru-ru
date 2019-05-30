---
title: Настройка ресурсов DirectX и отображение изображения
description: В этом разделе мы покажем, как создать устройство Direct3D, цепочку буферов и представление целевого объекта обработки, а также как представить обработанное изображение для отображения.
ms.assetid: d54d96fe-3522-4acb-35f4-bb11c3a5b064
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, игры, directx, ресурсы, изображения
ms.localizationpriority: medium
ms.openlocfilehash: 4e0fd2b5f34c17e39def107f72568916ca3ba6fc
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66368003"
---
# <a name="set-up-directx-resources-and-display-an-image"></a>Настройка ресурсов DirectX и отображение изображения



В этом разделе мы покажем, как создать устройство Direct3D, цепочку буферов и представление целевого объекта обработки, а также как представить обработанное изображение для отображения.

**Цель:** Чтобы настроить ресурсы DirectX в приложении C++ универсальной платформы Windows (UWP) и для отображения сплошным цветом.

## <a name="prerequisites"></a>предварительные требования


Предполагается, что вы знакомы с C++. Также вы должны быть знакомы с основными принципами программирования графики.

**Время завершения:** 20 минут.

## <a name="instructions"></a>Инструкция

### <a name="1-declaring-direct3d-interface-variables-with-comptr"></a>1. Объявление переменных интерфейс Direct3D, с объектом ComPtr

Мы объявляем переменные интерфейса Direct3D по шаблону [смарт-указателя](https://docs.microsoft.com/cpp/cpp/smart-pointers-modern-cpp) ComPtr из библиотеки шаблонов C++ для среды выполнения Windows (WRL), поэтому можно безопасно управлять временем существования этих переменных. В таком случае мы можем использовать эти переменные, чтобы получить доступ к классу [**ComPtr class**](https://docs.microsoft.com/cpp/windows/comptr-class) и его членам. Пример:

```cpp
    ComPtr<ID3D11RenderTargetView> m_renderTargetView;
    m_d3dDeviceContext->OMSetRenderTargets(
        1,
        m_renderTargetView.GetAddressOf(),
        nullptr // Use no depth stencil.
        );
```

При объявлении [ **ID3D11RenderTargetView** ](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview) с объектом ComPtr, можно использовать в ComPtr **GetAddressOf** метод, чтобы получить адрес указателя на  **ID3D11RenderTargetView** (\*\*ID3D11RenderTargetView) для передачи [ **ID3D11DeviceContext::OMSetRenderTargets**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets). Метод **OMSetRenderTargets** привязывает однобуферную прорисовку к [стадии слияния вывода](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-output-merger-stage), чтобы указать однобуферную прорисовку в качестве цели вывода.

После запуска образец приложения инициализируется, загружается и затем готов к выполнению.

### <a name="2-creating-the-direct3d-device"></a>2. Создания устройства Direct3D

Чтобы использовать API Direct3D для обработки сцены, необходимо сначала создать устройство Direct3D, представляющее видеоадаптер. Чтобы создать устройство Direct3D, необходимо вызвать функцию [**D3D11CreateDevice**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice). Мы указываем уровней 9.1 через 11.1 в массиве [ **D3D\_ФУНКЦИЯ\_уровень** ](https://docs.microsoft.com/windows/desktop/api/d3dcommon/ne-d3dcommon-d3d_feature_level) значения. Direct3D по порядку проходит через массив и возвращает высший уровень поддерживаемого компонента. Таким образом, чтобы получить наивысший уровень возможностей, перечислены **D3D\_ФУНКЦИЯ\_уровень** массива записей от самого высокого до самого низкого. Мы передаем [ **D3D11\_создать\_устройства\_BGRA\_поддержки** ](https://docs.microsoft.com/windows/desktop/api/d3d11/ne-d3d11-d3d11_create_device_flag) флаг *флаги* параметр, чтобы установить Ресурсы Direct3D взаимодействовать с Direct2D. Если мы используем отладочной сборки, мы передаем [ **D3D11\_создать\_устройства\_Отладка** ](https://docs.microsoft.com/windows/desktop/api/d3d11/ne-d3d11-d3d11_create_device_flag) флаг. Дополнительные сведения о приложениях отладки см. в разделе [Отладка приложений с помощью уровня отладки](https://docs.microsoft.com/windows/desktop/direct3d11/using-the-debug-layer-to-test-apps).

Мы получаем устройство Direct3D 11.1 ([**ID3D11Device1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1)) и контекст устройства ([**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1)) с помощью запроса устройства Direct3D 11 и его контекста, возвращаемые [**D3D11CreateDevice**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice).

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

Затем мы создаем цепочку буферов, используемую устройством для визуализации и отображения. Мы объявить и инициализировать [ **DXGI\_ЗАМЕНЫ\_ЦЕПОЧКИ\_DESC1** ](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/ns-dxgi1_2-dxgi_swap_chain_desc1) структуры для описания цепочки буферов. Затем мы устанавливаем цепочки буферов, как отразить модель (то есть цепочку буферов с [ **DXGI\_ЗАМЕНЫ\_эффект\_ПЕРЕВЕРНУТЬ\_SEQUENTIAL** ](https://docs.microsoft.com/windows/desktop/api/dxgi/ne-dxgi-dxgi_swap_effect) значение, заданное в **SwapEffect** член) и задайте **формат** члена [ **DXGI\_ФОРМАТ\_B8G8R8A8\_UNORM** ](https://docs.microsoft.com/windows/desktop/api/dxgiformat/ne-dxgiformat-dxgi_format). Мы устанавливаем **число** членом [ **DXGI\_пример\_DESC** ](https://docs.microsoft.com/windows/desktop/api/dxgicommon/ns-dxgicommon-dxgi_sample_desc) структуру, в которой **SampleDesc** член Указывает значение 1 и **качества** членом **DXGI\_пример\_DESC** до нуля, так как отразить модель не поддерживает несколько пример сглаживания (MSAA). Мы присваиваем члену **BufferCount** значение 2, и поэтому цепочки буферов могут использовать кадровый буфер для предоставления видеоадаптеру и задний буфер для однобуферной прорисовки.

Мы получаем базовое устройство DXGI, опрашивая устройство Direct3D 11.1. Чтобы свести к минимуму потребление энергии, что важно для устройств, работающих от аккумулятора, например ноутбуков и планшетов, необходимо вызвать метод [**IDXGIDevice1::SetMaximumFrameLatency**](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgidevice1-setmaximumframelatency), указав 1 как максимальное количество кадров заднего буфера, которое DXGI может поместить в очередь. Благодаря этому приложение обрабатывается только после выдержки интервала между кадрами.

Для окончательного создания цепочки буферов мы должны получить родительское производство от устройства DXGI. Мы вызываем метод [**IDXGIDevice::GetAdapter**](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgidevice-getadapter), чтобы получить адаптер для устройства, а затем вызывает метод [**IDXGIObject::GetParent**](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiobject-getparent) на адаптере для получения родительского производства ([**IDXGIFactory2**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgifactory2)). Для создания цепочки буферов мы вызываем метод [**IDXGIFactory2::CreateSwapChainForCoreWindow**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgifactory2-createswapchainforcorewindow) с дескриптором цепочки буферов и основное окно приложения.

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

### <a name="4-creating-the-render-target-view"></a>4. Создание представления целевого объекта отрисовки

Для преобразования графики для просмотра в окне мы должны создать представление однобуферной обработки. Мы вызываем метод [**IDXGISwapChain::GetBuffer**](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-getbuffer), чтобы получить задний буфер цепочки буферов и использовать его при создании представления однобуферной прорисовки. Мы указываем задний буфер в качестве 2D-текстуры ([**ID3D11Texture2D**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11texture2d)). Чтобы создать представление однобуферной прорисовки, мы вызываем метод [**ID3D11Device::CreateRenderTargetView**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createrendertargetview) с задним буфером цепочки буферов. Мы должны указать для рисования в окно «основные», указав порта просмотра ([**D3D11\_ПРОСМОТРА**](https://docs.microsoft.com/windows/desktop/api/d3d11/ns-d3d11-d3d11_viewport)) как полный размер заднего буфера цепочки буферов. Мы используем порт представления в обращении к методу [**ID3D11DeviceContext::RSSetViewports**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-rssetviewports) для связывания порта представления и [стадии растеризатора](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-rasterizer-stage) конвейера. Стадия растеризатора преобразует векторную информацию в растровое изображение. В данном случае преобразование нам не нужно, так как мы просто отображаем сплошной цвет.

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

### <a name="5-presenting-the-rendered-image"></a>5. Представления подготовленного изображения

Для непрерывной обработки и вывода сцены на экран мы воспользуемся бесконечным циклом.

В этом примере мы вызываем следующие методы:

1.  [**ID3D11DeviceContext::OMSetRenderTargets** ](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets) для указания целевого объекта отрисовки как назначение выходных данных.
2.  [**ID3D11DeviceContext::ClearRenderTargetView** ](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-clearrendertargetview) снимите целевого объекта отрисовки сплошным цветом.
3.  [**IDXGISwapChain::Present** ](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-present) для представления подготовленного изображения в окно.

Так как мы ранее определили максимальное значение задержки кадра как 1, Windows обычно замедляет цикл обработки до частоты обновления экрана, как правило, около 60 Гц. Windows замедляет цикл отрисовки, переводя приложение в спящий режим при вызове метода [**Present**](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-present). Windows переводит приложение в спящий режим до обновления экрана.

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

### <a name="6-resizing-the-app-window-and-the-swap-chains-buffer"></a>6. Изменение размера окна приложения и буфера цепочки буферов

Если изменяется размер окна приложения, приложение должно изменить размеры буферов цепочки буферов, повторно создать представление однобуферной обработки, а затем представить преобразованное изображение с измененными размерами. Чтобы изменить размеры буферов цепочки буферов, мы вызываем метод [**IDXGISwapChain::ResizeBuffers**](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-resizebuffers). В этом вызове оставить количество буферов и формат буферов без изменений ( *BufferCount* два параметра и *NewFormat* параметр [ **DXGI\_ ФОРМАТИРОВАТЬ\_B8G8R8A8\_UNORM**](https://docs.microsoft.com/windows/desktop/api/dxgiformat/ne-dxgiformat-dxgi_format)). Мы делаем размер заднего буфера цепочки буферов таким же, как измененный размер окна. Изменив размеры буферов цепочки буферов, мы создаем новую однобуферную прорисовку и представляем новое обработанное изображение, как при его инициализации.

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

## <a name="summary-and-next-steps"></a>Краткая сводка и дальнейшие действия


Мы создали устройство Direct3D, цепочку буферов и представление целевого объекта обработки, а также представили обработанное изображение для отображения.

Теперь мы также нарисуем на экране треугольник.

[Создание шейдеров и графических примитивов](creating-shaders-and-drawing-primitives.md)

 

 




