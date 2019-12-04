---
title: Множественная дискретизация в приложениях UWP
description: Вы научитесь использовать множественную дискретизацию в приложениях универсальной платформы Windows (UWP), созданных с помощью Direct3D.
ms.assetid: 1cd482b8-32ff-1eb0-4c91-83eb52f08484
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, множественная дискретизация, direct3d
ms.localizationpriority: medium
ms.openlocfilehash: f4d3e590f99fdf6ca907fcc8fd5b412c5796f474
ms.sourcegitcommit: ae9c1646398bb5a4a888437628eca09ae06e6076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74735109"
---
# <a name="span-iddev_gamingmultisampling__multi-sample_anti_aliasing__in_windows_store_appsspan-multisampling-in-universal-windows-platform-uwp-apps"></a><span id="dev_gaming.multisampling__multi-sample_anti_aliasing__in_windows_store_apps"></span>Множественная выборка в приложениях универсальная платформа Windows (UWP)



Вы научитесь использовать множественную дискретизацию в приложениях универсальной платформы Windows (UWP), созданных с помощью Direct3D. Множественная дискретизация, или сглаживание с множественной дискретизацией, — это графический метод, позволяющий сделать менее заметными линии стыков. При этом рисуется больше пикселей, чем фактически имеется в конечном целевом объекте прорисовки, затем значения усредняются, чтобы сохранить отображение "частичной" кромки в некоторых пикселях. Подробное описание работы множественной дискретизации в Direct3D см. в разделе [Правила растеризации при сглаживании с множественной дискретизацией](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-rasterizer-stage-rules).

## <a name="multisampling-and-the-flip-model-swap-chain"></a>Множественная дискретизация и цепочка буферов с моделью отражения


Приложения UWP с DirectX должны использовать цепочки буферов с моделью отражения. Цепочки буферов с моделью отражения не поддерживают напрямую множественную выборку сглаживания, и тем не менее этот метод можно использовать, только по-другому. Отрисуем сцену для представления целевого объекта прорисовки с множественной выборкой и перед предъявлением поместим этот объект в задний буфер. В этой статье приведены шаги по добавлению множественной выборки сглаживания в приложение UWP.

### <a name="how-to-use-multisampling"></a>Как использовать множественную дискретизацию

Уровни компонентов Direct3D обеспечивают поддержку определенного минимального числа выборок и гарантируют доступность некоторых форматов буфера с поддержкой множественной дискретизации. Графические устройства часто поддерживают более широкий спектр форматов и большее число выборок, чем минимально необходимо. Проверку на поддержку множественной дискретизации можно провести во время выполнения приложения. Сначала проверяется поддержка компонентов для множественной дискретизации с определенными форматами DXGI, а затем выясняется, какое количество выборок можно использовать с каждым поддерживаемым форматом.

1.  Вызовите [**ID3D11Device::CheckFeatureSupport**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-checkfeaturesupport), чтобы узнать, какие форматы DXGI можно использовать с множественной дискретизацией. Предоставьте форматы однобуферной прорисовки, которые может использовать ваша игра. Как целевой объект рендеринга, так и целевой объект разрешения должны использовать один и тот же формат, поэтому проверьте [**Формат D3D11\_\_поддержки\_МНОГОпримерных\_рендертаржет**](https://docs.microsoft.com/windows/desktop/api/d3d11/ne-d3d11-d3d11_format_support) и **D3D11\_формата\_поддержки\_многопримерных\_разрешения**.

    **Уровень функции 9.  ** Хотя устройства уровня "функция 9" [гарантируют поддержку для форматов целевого объекта рендеринга с возможностью многовыборочной выборки](https://docs.microsoft.com/previous-versions/ff471324(v=vs.85)), поддержка для целевых объектов с многовыборочной поддержкой не гарантируется. Поэтому необходимо выполнить эту проверку перед использованием техники множественной дискретизации, описанной в этом разделе.

    Следующий код проверяет поддержку множественной выборки для всех значений формата\_DXGI:

    ```cpp
    // Determine the format support for multisampling.
    for (UINT i = 1; i < DXGI_FORMAT_MAX; i++)
    {
        DXGI_FORMAT inFormat = safe_cast<DXGI_FORMAT>(i);
        UINT formatSupport = 0;
        HRESULT hr = m_d3dDevice->CheckFormatSupport(inFormat, &formatSupport);

        if ((formatSupport & D3D11_FORMAT_SUPPORT_MULTISAMPLE_RESOLVE) &&
            (formatSupport & D3D11_FORMAT_SUPPORT_MULTISAMPLE_RENDERTARGET)
            )
        {
            m_supportInfo->SetFormatSupport(i, true);
        }
        else
        {
            m_supportInfo->SetFormatSupport(i, false);
        }
    }
    ```

2.  Запросите для каждого поддерживаемого формата поддержку числа выборок. Для этого вызовите [**ID3D11Device::CheckMultisampleQualityLevels**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-checkmultisamplequalitylevels).

    Следующий код проверяет поддержку размера выборки для поддерживаемых форматов DXGI:

    ```cpp
    // Find available sample sizes for each supported format.
    for (unsigned int i = 0; i < DXGI_FORMAT_MAX; i++)
    {
        for (unsigned int j = 1; j < MAX_SAMPLES_CHECK; j++)
        {
            UINT numQualityFlags;

            HRESULT test = m_d3dDevice->CheckMultisampleQualityLevels(
                (DXGI_FORMAT) i,
                j,
                &numQualityFlags
                );

            if (SUCCEEDED(test) && (numQualityFlags > 0))
            {
                m_supportInfo->SetSampleSize(i, j, 1);
                m_supportInfo->SetQualityFlagsAt(i, j, numQualityFlags);
            }
        }
    }
    ```

    > **Обратите внимание** ,   использовать [**ID3D11Device2:: CheckMultisampleQualityLevels1**](https://docs.microsoft.com/windows/desktop/api/d3d11_2/nf-d3d11_2-id3d11device2-checkmultisamplequalitylevels1) , если необходимо проверить многовыборочную поддержку для буферов мозаичных ресурсов.

     

3.  Создайте буфер и представление целевого объекта прорисовки с требуемым числом выборок. Используйте те\_же формат, ширину и высоту, что и в цепочке буферов, но с помощью многовыборочного измерения текстуры (**D3D11\_ртв\_измерение\_TEXTURE2DMS** , например). При необходимости цепочку буферов можно воссоздать, задав для нее новые параметры, оптимальные для множественной дискретизации.

    Следующий код создает целевой объект прорисовки с множественной дискретизацией:

    ```cpp
    float widthMulti = m_d3dRenderTargetSize.Width;
    float heightMulti = m_d3dRenderTargetSize.Height;

    D3D11_TEXTURE2D_DESC offScreenSurfaceDesc;
    ZeroMemory(&offScreenSurfaceDesc, sizeof(D3D11_TEXTURE2D_DESC));

    offScreenSurfaceDesc.Format = DXGI_FORMAT_B8G8R8A8_UNORM;
    offScreenSurfaceDesc.Width = static_cast<UINT>(widthMulti);
    offScreenSurfaceDesc.Height = static_cast<UINT>(heightMulti);
    offScreenSurfaceDesc.BindFlags = D3D11_BIND_RENDER_TARGET;
    offScreenSurfaceDesc.MipLevels = 1;
    offScreenSurfaceDesc.ArraySize = 1;
    offScreenSurfaceDesc.SampleDesc.Count = m_sampleSize;
    offScreenSurfaceDesc.SampleDesc.Quality = m_qualityFlags;

    // Create a surface that's multisampled.
    DX::ThrowIfFailed(
        m_d3dDevice->CreateTexture2D(
        &offScreenSurfaceDesc,
        nullptr,
        &m_offScreenSurface)
        );

    // Create a render target view. 
    CD3D11_RENDER_TARGET_VIEW_DESC renderTargetViewDesc(D3D11_RTV_DIMENSION_TEXTURE2DMS);
    DX::ThrowIfFailed(
        m_d3dDevice->CreateRenderTargetView(
        m_offScreenSurface.Get(),
        &renderTargetViewDesc,
        &m_d3dRenderTargetView
        )
        );
    ```

4.  Чтобы соответствовать целевому объекту прорисовки с множественной дискретизацией, буфер глубины должен иметь те же ширину, высоту, число выборок и размер текстуры.

    Следующий код создает буфер глубины с множественной дискретизацией:

    ```cpp
    // Create a depth stencil view for use with 3D rendering if needed.
    CD3D11_TEXTURE2D_DESC depthStencilDesc(
        DXGI_FORMAT_D24_UNORM_S8_UINT,
        static_cast<UINT>(widthMulti),
        static_cast<UINT>(heightMulti),
        1, // This depth stencil view has only one texture.
        1, // Use a single mipmap level.
        D3D11_BIND_DEPTH_STENCIL,
        D3D11_USAGE_DEFAULT,
        0,
        m_sampleSize,
        m_qualityFlags
        );

    ComPtr<ID3D11Texture2D> depthStencil;
    DX::ThrowIfFailed(
        m_d3dDevice->CreateTexture2D(
        &depthStencilDesc,
        nullptr,
        &depthStencil
        )
        );

    CD3D11_DEPTH_STENCIL_VIEW_DESC depthStencilViewDesc(D3D11_DSV_DIMENSION_TEXTURE2DMS);
    DX::ThrowIfFailed(
        m_d3dDevice->CreateDepthStencilView(
        depthStencil.Get(),
        &depthStencilViewDesc,
        &m_d3dDepthStencilView
        )
        );
    ```

5.  Пришло время создать окно просмотра, так как ширина и высота окна просмотра также должны соответствовать целевому объекту прорисовки.

    Следующий код создает окно просмотра:

    ```cpp
    // Set the 3D rendering viewport to target the entire window.
    m_screenViewport = CD3D11_VIEWPORT(
        0.0f,
        0.0f,
        widthMulti / m_scalingFactor,
        heightMulti / m_scalingFactor
        );

    m_d3dContext->RSSetViewports(1, &m_screenViewport);
    ```

6.  Отрисуйте каждый кадр в целевом объекте прорисовки с множественной дискретизацией. По завершении прорисовки, но перед предъявлением кадра вызовите [**ID3D11DeviceContext::ResolveSubresource**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-resolvesubresource). Это заставит Direct3D выполнить множественную дискретизацию, рассчитать значение для отображения каждого пикселя и поместить результат в задний буфер. Теперь задний буфер содержит конечное сглаженное изображение и может быть предъявлен.

    Следующий код сопоставляет вспомогательный ресурс перед предъявлением кадра:

    ```cpp
    if (m_sampleSize > 1)
    {
        unsigned int sub = D3D11CalcSubresource(0, 0, 1);

        m_d3dContext->ResolveSubresource(
            m_backBuffer.Get(),
            sub,
            m_offScreenSurface.Get(),
            sub,
            DXGI_FORMAT_B8G8R8A8_UNORM
            );
    }

    // The first argument instructs DXGI to block until VSync, putting the application
    // to sleep until the next VSync. This ensures that we don't waste any cycles rendering
    // frames that will never be displayed to the screen.
    hr = m_swapChain->Present(1, 0);
    ```

 

 




