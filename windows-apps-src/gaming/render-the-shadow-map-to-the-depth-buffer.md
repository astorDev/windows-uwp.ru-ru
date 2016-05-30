---
author: mtoepke
title: Прорисовка карты теней в буфер глубины
description: Выполняя прорисовку с точки зрения освещения, вы можете создать двумерную карту глубин, представляющую объемную тень.
ms.assetid: 7f3d0208-c379-8871-cc48-027047c6c2d0
---

# Прорисовка карты теней в буфере глубины


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Выполняя прорисовку с точки зрения освещения, вы можете создать двумерную карту глубин, представляющую объемную тень. Карта глубин маскирует пространство, которое будет прорисовано с затенением. [Пошаговое руководство. Реализация теневых объемов с помощью буферов глубины в Direct3D 11](implementing-depth-buffers-for-shadow-mapping.md), часть 2.

## Очистка буфера глубины


Всегда очищайте буфер глубины перед прорисовкой в него.

```cpp
context->ClearRenderTargetView(m_deviceResources->GetBackBufferRenderTargetView(), DirectX::Colors::CornflowerBlue);
context->ClearDepthStencilView(m_shadowDepthView.Get(), D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
```

## Прорисовка карты теней в буфер глубины


Для прохода прорисовки теней задайте буфер глубины, но не указывайте цель прорисовки.

Задайте окно просмотра освещения, шейдер вершин и буферы констант для светового пространства. При этом проходе используйте выбор передней грани, чтобы оптимизировать значения глубины, помещаемые в буфер теней.

Обратите внимание, что в большинстве устройств вы можете задать nullptr для построителя текстуры (или вовсе не задавать построитель текстуры). Но некоторые драйверы могут генерировать исключение при вызове операции рисования на устройстве Direct3D с пустым указателем на построитель текстуры. Чтобы такого исключения не возникало, вы можете задать минимальный пиксельный шейдер для прохода прорисовки теней. Выходное значение этого шейдера будет игнорироваться; он может вызывать [**discard**](https://msdn.microsoft.com/library/windows/desktop/bb943995) для каждого пикселя.

Прорисовывайте объекты, которые могут отбрасывать тени, но не тратьте усилия на прорисовку геометрических элементов, которые не способны отбрасывать тень (например, пол в комнате или объекты, исключенные из прохода прорисовки теней в целях оптимизации).

```cpp
void ShadowSceneRenderer::RenderShadowMap()
{
    auto context = m_deviceResources->GetD3DDeviceContext();

    // Render all the objects in the scene that can cast shadows onto themselves or onto other objects.

    // Only bind the ID3D11DepthStencilView for output.
    context->OMSetRenderTargets(
        0,
        nullptr,
        m_shadowDepthView.Get()
        );

    // Note that starting with the second frame, the previous call will display
    // warnings in VS debug output about forcing an unbind of the pixel shader
    // resource. This warning can be safely ignored when using shadow buffers
    // as demonstrated in this sample.

    // Set rendering state.
    context->RSSetState(m_shadowRenderState.Get());
    context->RSSetViewports(1, &m_shadowViewport);

    // Each vertex is one instance of the VertexPositionTexNormColor struct.
    UINT stride = sizeof(VertexPositionTexNormColor);
    UINT offset = 0;
    context->IASetVertexBuffers(
        0,
        1,
        m_vertexBuffer.GetAddressOf(),
        &stride,
        &offset
        );

    context->IASetIndexBuffer(
        m_indexBuffer.Get(),
        DXGI_FORMAT_R16_UINT, // Each index is one 16-bit unsigned integer (short).
        0
        );

    context->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
    context->IASetInputLayout(m_inputLayout.Get());

    // Attach our vertex shader.
    context->VSSetShader(
        m_simpleVertexShader.Get(),
        nullptr,
        0
        );

    // Send the constant buffers to the Graphics device.
    context->VSSetConstantBuffers(
        0,
        1,
        m_lightViewProjectionBuffer.GetAddressOf()
        );

    context->VSSetConstantBuffers(
        1,
        1,
        m_rotatedModelBuffer.GetAddressOf()
        );

    // In some configurations, it's possible to avoid setting a pixel shader
    // (or set PS to nullptr). Not all drivers are tolerant of this, so to be
    // safe set a minimal shader here.
    //
    // Direct3D will discard output from this shader because the render target
    // view is unbound.
    context->PSSetShader(
        m_textureShader.Get(),
        nullptr,
        0
        );

    // Draw the objects.
    context->DrawIndexed(
        m_indexCountCube,
        0,
        0
        );
}
```

**Оптимизация усеченного визуального конуса:** позаботьтесь о том, чтобы реализация вычисляла компактный усеченный визуальный конус — так буфер глубины будет обеспечивать наибольшую точность. Другие советы по работе с тенями см. в разделе о [распространенных методиках улучшения карт глубин теней](https://msdn.microsoft.com/library/windows/desktop/ee416324).

## Шейдер вершин для прохода прорисовки теней


Используйте упрощенную версию шейдера вершин, чтобы прорисовать только положение вершины в световом пространстве. Не включайте в него световые нормали, вторичные преобразования и т. д.

```cpp
PixelShaderInput main(VertexShaderInput input)
{
    PixelShaderInput output;
    float4 pos = float4(input.pos, 1.0f);

    // Transform the vertex position into projected space.
    pos = mul(pos, model);
    pos = mul(pos, view);
    pos = mul(pos, projection);
    output.pos = pos;

    return output;
}
```

В следующей части этого пошагового руководства вы научитесь добавлять тени при помощи [прорисовки с проверкой глубины](render-the-scene-with-depth-testing.md).

 

 






<!--HONumber=May16_HO2-->


