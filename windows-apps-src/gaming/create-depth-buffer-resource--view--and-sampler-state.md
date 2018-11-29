---
title: Создание ресурсов устройства буфера глубины
description: Научитесь создавать ресурсы устройств Direct3D, необходимые для поддержки тестирования глубины для теневых томов.
ms.assetid: 86d5791b-1faa-17e4-44a8-bbba07062756
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, direct3d, буфер глубины
ms.localizationpriority: medium
ms.openlocfilehash: f5ce1ec522a194111e175e41f82c4275cda4fbf5
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "7982567"
---
# <a name="create-depth-buffer-device-resources"></a>Создание ресурсов устройства буфера глубины




Научитесь создавать ресурсы устройств Direct3D, необходимые для поддержки тестирования глубины для теневых томов. [Пошаговое руководство. Реализация теневых объемов с помощью буферов глубины в Direct3D11](implementing-depth-buffers-for-shadow-mapping.md), часть1.

## <a name="resources-youll-need"></a>Необходимые ресурсы


Для прорисовки карты глубины для теневых томов требуются следующие аппаратно-зависимые ресурсы Direct3D:

-   Ресурс (буфер) для карты глубины
-   Представление набора элементов глубины и представление ресурсов шейдера для ресурса
-   Объект сравнения состояний дискретизатора
-   Буферы констант для матриц освещения точки обзора
-   Окно просмотра для прорисовки карты теней (обычно прямоугольное окно просмотра)
-   Объект состояния прорисовки для включения выбора передней поверхности
-   Объект состояния прорисовки (если он уже не используется) также потребуется вам для возвращения к выбору задней поверхности.

Отметим, что создание таких ресурсов должно быть включено в программу создания аппаратно-зависимых ресурсов. При этом ваше средство визуализации сможет, например, заново создавать их, когда устанавливается новый драйвер устройства или когда пользователь переносит ваше приложение на монитор, связанный с другим графическим адаптером.

## <a name="check-feature-support"></a>Проверка поддержки функции


Перед созданием карты глубины вызовите метод [**CheckFeatureSupport**](https://msdn.microsoft.com/library/windows/desktop/ff476497) на устройстве Direct3D, запросите **D3D11\_FEATURE\_D3D9\_SHADOW\_SUPPORT** и предоставьте структуру [**D3D11\_FEATURE\_DATA\_D3D9\_SHADOW\_SUPPORT**](https://msdn.microsoft.com/library/windows/desktop/jj247569).

```cpp
D3D11_FEATURE_DATA_D3D9_SHADOW_SUPPORT isD3D9ShadowSupported;
ZeroMemory(&isD3D9ShadowSupported, sizeof(isD3D9ShadowSupported));
pD3DDevice->CheckFeatureSupport(
    D3D11_FEATURE_D3D9_SHADOW_SUPPORT,
    &isD3D9ShadowSupported,
    sizeof(D3D11_FEATURE_D3D9_SHADOW_SUPPORT)
    );

if (isD3D9ShadowSupported.SupportsDepthAsTextureWithLessEqualComparisonFilter)
{
    // Init shadow map resources

```

Если эта функция не поддерживается, не пытайтесь загружать шейдеры, скомпилированные для модели шейдера4 уровня 9\_x, которые вызывают функции сравнения выборки. Во многих случаях отсутствие поддержки этой функции означает, что GPU— это устаревшее устройство, и его драйвер не обновлен для поддержки по крайней мере WDDM1.2. Если устройство поддерживает хотя бы уровень компонентов 10\_0, можно вместо этого загрузить шейдер сравнения выборки, скомпилированный для модели шейдеров 4\_0.

## <a name="create-depth-buffer"></a>Создание буфера глубины


Сначала попробуйте создать карту глубины с максимально точным форматом глубины. Задайте соответствующие свойства представления ресурса шейдера. Если создать ресурс не удалось, например из-за недостаточной памяти устройства или потому, что формат не поддерживается оборудованием, попытайтесь применить менее точный формат и измените свойства соответствующим образом.

Этот шаг является необязательным, если вам необходим только формат с меньшей точностью, например при прорисовке на устройствах Direct3D среднего разрешения с уровнем компонентов9\_1.

```cpp
D3D11_TEXTURE2D_DESC shadowMapDesc;
ZeroMemory(&shadowMapDesc, sizeof(D3D11_TEXTURE2D_DESC));
shadowMapDesc.Format = DXGI_FORMAT_R24G8_TYPELESS;
shadowMapDesc.MipLevels = 1;
shadowMapDesc.ArraySize = 1;
shadowMapDesc.SampleDesc.Count = 1;
shadowMapDesc.BindFlags = D3D11_BIND_SHADER_RESOURCE | D3D11_BIND_DEPTH_STENCIL;
shadowMapDesc.Height = static_cast<UINT>(m_shadowMapDimension);
shadowMapDesc.Width = static_cast<UINT>(m_shadowMapDimension);

HRESULT hr = pD3DDevice->CreateTexture2D(
    &shadowMapDesc,
    nullptr,
    &m_shadowMap
    );
```

Затем создайте представления ресурсов. Задайте нулевое значение для MIP-среза в представлении трафарета глубины и значение1 для уровней MIP в представлении ресурса шейдера. Оба будут иметь размеры текстуры TEXTURE2D, и обоим требуется соответствующий [**DXGI\_FORMAT**](https://msdn.microsoft.com/library/windows/desktop/bb173059).

```cpp
D3D11_DEPTH_STENCIL_VIEW_DESC depthStencilViewDesc;
ZeroMemory(&depthStencilViewDesc, sizeof(D3D11_DEPTH_STENCIL_VIEW_DESC));
depthStencilViewDesc.Format = DXGI_FORMAT_D24_UNORM_S8_UINT;
depthStencilViewDesc.ViewDimension = D3D11_DSV_DIMENSION_TEXTURE2D;
depthStencilViewDesc.Texture2D.MipSlice = 0;

D3D11_SHADER_RESOURCE_VIEW_DESC shaderResourceViewDesc;
ZeroMemory(&shaderResourceViewDesc, sizeof(D3D11_SHADER_RESOURCE_VIEW_DESC));
shaderResourceViewDesc.ViewDimension = D3D11_SRV_DIMENSION_TEXTURE2D;
shaderResourceViewDesc.Format = DXGI_FORMAT_R24_UNORM_X8_TYPELESS;
shaderResourceViewDesc.Texture2D.MipLevels = 1;

hr = pD3DDevice->CreateDepthStencilView(
    m_shadowMap.Get(),
    &depthStencilViewDesc,
    &m_shadowDepthView
    );

hr = pD3DDevice->CreateShaderResourceView(
    m_shadowMap.Get(),
    &shaderResourceViewDesc,
    &m_shadowResourceView
    );
```

## <a name="create-comparison-state"></a>Создание состояния сравнения


Теперь создайте объект сравнения состояний дискретизатора. Уровень компонентов 9\_1 поддерживает только D3D11\_COMPARISON\_LESS\_EQUAL. Возможности фильтрации подробнее объясняются в документе [Поддержка карт теней на разном оборудовании](target-a-range-of-hardware.md). Можно также выбрать точечную фильтрацию для ускорения работы карт теней.

Отметим, что можно указать режим адресации D3D11\_TEXTURE\_ADDRESS\_BORDER, который будет хорошо работать на устройствах с уровнем компонентов9\_1. Это применимо к шейдерам, которые не проверяют, находится ли пиксель в конусе представления освещения, перед выполнением теста глубины. Указывая значения0 или1 для каждой границы, вы можете контролировать, проходят или не проходят пиксели вне конуса освещения тест на глубину, то есть освещены они или находятся в тени.

На уровне компонентов 9\_1 должны быть заданы следующие обязательные значения: для **MinLOD**— нуль, для **MaxLOD**— **D3D11\_FLOAT32\_MAX**, для **MaxAnisotropy**— нуль.

```cpp
D3D11_SAMPLER_DESC comparisonSamplerDesc;
ZeroMemory(&comparisonSamplerDesc, sizeof(D3D11_SAMPLER_DESC));
comparisonSamplerDesc.AddressU = D3D11_TEXTURE_ADDRESS_BORDER;
comparisonSamplerDesc.AddressV = D3D11_TEXTURE_ADDRESS_BORDER;
comparisonSamplerDesc.AddressW = D3D11_TEXTURE_ADDRESS_BORDER;
comparisonSamplerDesc.BorderColor[0] = 1.0f;
comparisonSamplerDesc.BorderColor[1] = 1.0f;
comparisonSamplerDesc.BorderColor[2] = 1.0f;
comparisonSamplerDesc.BorderColor[3] = 1.0f;
comparisonSamplerDesc.MinLOD = 0.f;
comparisonSamplerDesc.MaxLOD = D3D11_FLOAT32_MAX;
comparisonSamplerDesc.MipLODBias = 0.f;
comparisonSamplerDesc.MaxAnisotropy = 0;
comparisonSamplerDesc.ComparisonFunc = D3D11_COMPARISON_LESS_EQUAL;
comparisonSamplerDesc.Filter = D3D11_FILTER_COMPARISON_MIN_MAG_MIP_POINT;

// Point filtered shadows can be faster, and may be a good choice when
// rendering on hardware with lower feature levels. This sample has a
// UI option to enable/disable filtering so you can see the difference
// in quality and speed.

DX::ThrowIfFailed(
    pD3DDevice->CreateSamplerState(
        &comparisonSamplerDesc,
        &m_comparisonSampler_point
        )
    );
```

## <a name="create-render-states"></a>Создание состояний прорисовки


Теперь создайте состояние прорисовки, которое можно использовать для выбора передней поверхности. Отметим, что для устройств с уровнем компонентов 9\_1 необходимо задать для **DepthClipEnable** значение **true**.

```cpp
D3D11_RASTERIZER_DESC drawingRenderStateDesc;
ZeroMemory(&drawingRenderStateDesc, sizeof(D3D11_RASTERIZER_DESC));
drawingRenderStateDesc.CullMode = D3D11_CULL_BACK;
drawingRenderStateDesc.FillMode = D3D11_FILL_SOLID;
drawingRenderStateDesc.DepthClipEnable = true; // Feature level 9_1 requires DepthClipEnable == true
DX::ThrowIfFailed(
    pD3DDevice->CreateRasterizerState(
        &drawingRenderStateDesc,
        &m_drawingRenderState
        )
    );
```

Создайте состояние прорисовки, которое можно использовать для выбора задней поверхности. Если ваш код прорисовки уже включает выбор задней поверхности, этот шаг можно пропустить.

```cpp
D3D11_RASTERIZER_DESC shadowRenderStateDesc;
ZeroMemory(&shadowRenderStateDesc, sizeof(D3D11_RASTERIZER_DESC));
shadowRenderStateDesc.CullMode = D3D11_CULL_FRONT;
shadowRenderStateDesc.FillMode = D3D11_FILL_SOLID;
shadowRenderStateDesc.DepthClipEnable = true;

DX::ThrowIfFailed(
    pD3DDevice->CreateRasterizerState(
        &shadowRenderStateDesc,
        &m_shadowRenderState
        )
    );
```

## <a name="create-constant-buffers"></a>Создание буферов констант


Не забудьте создать буфер констант для прорисовки с точки зрения освещения. Этот буфер констант можно также использовать, чтобы указывать шейдеру позицию источника освещения. Используйте матрицу перспективы для точечных источников света и ортогональную матрицу для направленных источников света (например, для солнечного света).

```cpp
DX::ThrowIfFailed(
    m_deviceResources->GetD3DDevice()->CreateBuffer(
        &viewProjectionConstantBufferDesc,
        nullptr,
        &m_lightViewProjectionBuffer
        )
    );
```

Заполните данные буфера констант. Обновляйте буферы констант один раз при инициализации и затем снова, если значения освещения были изменены по сравнению с предыдущим кадром.

```cpp
{
    XMMATRIX lightPerspectiveMatrix = XMMatrixPerspectiveFovRH(
        XM_PIDIV2,
        1.0f,
        12.f,
        50.f
        );

    XMStoreFloat4x4(
        &m_lightViewProjectionBufferData.projection,
        XMMatrixTranspose(lightPerspectiveMatrix)
        );

    // Point light at (20, 15, 20), pointed at the origin. POV up-vector is along the y-axis.
    static const XMVECTORF32 eye = { 20.0f, 15.0f, 20.0f, 0.0f };
    static const XMVECTORF32 at = { 0.0f, 0.0f, 0.0f, 0.0f };
    static const XMVECTORF32 up = { 0.0f, 1.0f, 0.0f, 0.0f };

    XMStoreFloat4x4(
        &m_lightViewProjectionBufferData.view,
        XMMatrixTranspose(XMMatrixLookAtRH(eye, at, up))
        );

    // Store the light position to help calculate the shadow offset.
    XMStoreFloat4(&m_lightViewProjectionBufferData.pos, eye);
}
```

```cpp
context->UpdateSubresource(
    m_lightViewProjectionBuffer.Get(),
    0,
    NULL,
    &m_lightViewProjectionBufferData,
    0,
    0
    );
```

## <a name="create-a-viewport"></a>Создание окна просмотра


Для прорисовки на карте глубины требуется отдельное окно просмотра. Окно просмотра не является аппаратно-зависимым ресурсом. Вы можете создать его в любом месте кода. Создание окна просмотра вместе с картой теней делает более удобным согласование размеров окна просмотра с размерами карты теней.

```cpp
// Init viewport for shadow rendering
ZeroMemory(&m_shadowViewport, sizeof(D3D11_VIEWPORT));
m_shadowViewport.Height = m_shadowMapDimension;
m_shadowViewport.Width = m_shadowMapDimension;
m_shadowViewport.MinDepth = 0.f;
m_shadowViewport.MaxDepth = 1.f;
```

В следующей части этого руководства вы научитесь создавать карту теней путем [прорисовки в буфер глубины](render-the-shadow-map-to-the-depth-buffer.md).

 

 




