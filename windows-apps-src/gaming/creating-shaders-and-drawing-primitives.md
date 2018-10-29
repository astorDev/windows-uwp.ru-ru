---
author: mtoepke
title: Создание шейдеров и примитивов рисования
description: В этом разделе мы покажем, как использовать исходные HLSL-файлы для компиляции и создания шейдеров, при помощи которых впоследствии можно отрисовывать на экране примитивы.
ms.assetid: 91113bbe-96c9-4ef9-6482-39f1ff1a70f4
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, шейдеры, примитивы, directx
ms.localizationpriority: medium
ms.openlocfilehash: 475a69837796b0b64be27c96f10b42d5b61390c1
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5747241"
---
# <a name="create-shaders-and-drawing-primitives"></a>Создание шейдеров и примитивов рисования



В этом разделе мы покажем, как использовать исходные HLSL-файлы для компиляции и создания шейдеров, при помощи которых впоследствии можно отрисовывать на экране примитивы.

Мы создадим и нарисуем желтый треугольник при помощи построителя текстур и вершинного шейдера. Мы создадим устройство Direct3D, цепочку буферов и представление однобуферной обработки, а затем прочитаем данные из двоичного объектного файла шейдера на диске.

**Цель.** Создание шейдеров и рисование примитивов.

## <a name="prerequisites"></a>Необходимые условия


Предполагается, что вы знакомы с C++. Также вы должны быть знакомы с основными принципами программирования графики.

Предполагается также, что вы изучили раздел [Краткое руководство: настройка ресурсов DirectX и вывод изображения.](setting-up-directx-resources.md)

**Время выполнения:** 20 минут.

## <a name="instructions"></a>Инструкции

### <a name="1-compiling-hlsl-source-files"></a>1. Компиляция исходных HLSL-файлов

Microsoft Visual Studio использует компилятор кода HLSL [fxc.exe](https://msdn.microsoft.com/library/windows/desktop/bb232919) для компиляции исходных HLSL-файлов (SimpleVertexShader.hlsl и SimplePixelShader.hlsl) в двоичные объектные CSO-файлы шейдеров (SimpleVertexShader.cso и SimplePixelShader.cso). Подробнее о компиляторе кода HLSL см. в разделе, посвященном средству компиляции эффектов. Подробнее о компиляции кода шейдера см. в разделе о [компиляции шейдеров](https://msdn.microsoft.com/library/windows/desktop/bb509633).

Код в файле SimpleVertexShader.hlsl:

```hlsl
struct VertexShaderInput
{
    DirectX::XMFLOAT2 pos : POSITION;
};

struct PixelShaderInput
{
    float4 pos : SV_POSITION;
};

PixelShaderInput SimpleVertexShader(VertexShaderInput input)
{
    PixelShaderInput vertexShaderOutput;

    // For this lesson, set the vertex depth value to 0.5, so it is guaranteed to be drawn.
    vertexShaderOutput.pos = float4(input.pos, 0.5f, 1.0f);

    return vertexShaderOutput;
}
```

Код в файле SimplePixelShader.hlsl:

```hlsl
struct PixelShaderInput
{
    float4 pos : SV_POSITION;
};

float4 SimplePixelShader(PixelShaderInput input) : SV_TARGET
{
    // Draw the entire triangle yellow.
    return float4(1.0f, 1.0f, 0.0f, 1.0f);
}
```

### <a name="2-reading-data-from-disk"></a>2. Чтение данных с диска

Мы используем функцию DX::ReadDataAsync из DirectXHelper.h в шаблоне приложения DirectX11 (универсальное приложение Windows) для асинхронного чтения данных из файла на диске.

### <a name="3-creating-vertex-and-pixel-shaders"></a>3. Создание вершинных и пискельных шейдеров

Считываем данные из файла SimpleVertexShader.cso и назначаем данные массиву байтов *vertexShaderBytecode*. Вызываем [**ID3D11Device::CreateVertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476524) с массивом байтов, чтобы создать вершинный шейдер ([**ID3D11VertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476641)). Чтобы обеспечить отрисовку треугольника, мы задаем для параметра vertex depth в исходном файле SimpleVertexShader.hlsl значение 0,5. Далее необходимо заполнить массив структур [**D3D11\_INPUT\_ELEMENT\_DESC**](https://msdn.microsoft.com/library/windows/desktop/ff476180), чтобы описать структуру кода вершинного шейдера, а затем создать эту структуру при помощи вызова метода [**ID3D11Device::CreateInputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476512). Массив содержит один элемент структуры, определяющий расположение вершины. Считываем данные из файла SimplePixelShader.cso и назначаем их массиву байтов *pixelShaderBytecode*. Вызываем [**ID3D11Device::CreatePixelShader**](https://msdn.microsoft.com/library/windows/desktop/ff476513) с массивом байтов, чтобы создать пиксельный шейдер ([**ID3D11PixelShader**](https://msdn.microsoft.com/library/windows/desktop/ff476576)). Чтобы наш треугольник стал желтым, мы задаем для параметра pixel value в исходном файле SimplePixelShader.hlsl значение «1,1,1,1». Изменяя это значение, можно изменять цвет примитива.

Нам нужно создать буферы вершин и индексов, определяющих простой треугольник. Для этого мы сначала определяем треугольник, затем описываем буферы вершин и индексов ([**D3D11\_BUFFER\_DESC**](https://msdn.microsoft.com/library/windows/desktop/ff476092) и [**D3D11\_SUBRESOURCE\_DATA**](https://msdn.microsoft.com/library/windows/desktop/ff476220)) с помощью определения треугольника и, наконец, вызываем метод [**ID3D11Device::CreateBuffer**](https://msdn.microsoft.com/library/windows/desktop/ff476501) по одному разу для каждого буфера.

```cpp
        auto loadVSTask = DX::ReadDataAsync(L"SimpleVertexShader.cso");
        auto loadPSTask = DX::ReadDataAsync(L"SimplePixelShader.cso");
        
        // Load the raw vertex shader bytecode from disk and create a vertex shader with it.
        auto createVSTask = loadVSTask.then([this](const std::vector<byte>& vertexShaderBytecode) {


          ComPtr<ID3D11VertexShader> vertexShader;
          DX::ThrowIfFailed(
              m_d3dDevice->CreateVertexShader(
                  vertexShaderBytecode->Data,
                  vertexShaderBytecode->Length,
                  nullptr,
                  &vertexShader
                  )
              );

          // Create an input layout that matches the layout defined in the vertex shader code.
          // For this lesson, this is simply a DirectX::XMFLOAT2 vector defining the vertex position.
          const D3D11_INPUT_ELEMENT_DESC basicVertexLayoutDesc[] =
          {
              { "POSITION", 0, DXGI_FORMAT_R32G32_FLOAT, 0, 0, D3D11_INPUT_PER_VERTEX_DATA, 0 },
          };

          ComPtr<ID3D11InputLayout> inputLayout;
          DX::ThrowIfFailed(
              m_d3dDevice->CreateInputLayout(
                  basicVertexLayoutDesc,
                  ARRAYSIZE(basicVertexLayoutDesc),
                  vertexShaderBytecode->Data,
                  vertexShaderBytecode->Length,
                  &inputLayout
                  )
              );
        });
        
        // Load the raw pixel shader bytecode from disk and create a pixel shader with it.
        auto createPSTask = loadPSTask.then([this](const std::vector<byte>& pixelShaderBytecode) {
          ComPtr<ID3D11PixelShader> pixelShader;
          DX::ThrowIfFailed(
              m_d3dDevice->CreatePixelShader(
                  pixelShaderBytecode->Data,
                  pixelShaderBytecode->Length,
                  nullptr,
                  &pixelShader
                  )
              );
        });

        // Create vertex and index buffers that define a simple triangle.
        auto createTriangleTask = (createPSTask && createVSTask).then([this] () {

          DirectX::XMFLOAT2 triangleVertices[] =
          {
              float2(-0.5f, -0.5f),
              float2( 0.0f,  0.5f),
              float2( 0.5f, -0.5f),
          };

          unsigned short triangleIndices[] =
          {
              0, 1, 2,
          };

          D3D11_BUFFER_DESC vertexBufferDesc = {0};
          vertexBufferDesc.ByteWidth = sizeof(float2) * ARRAYSIZE(triangleVertices);
          vertexBufferDesc.Usage = D3D11_USAGE_DEFAULT;
          vertexBufferDesc.BindFlags = D3D11_BIND_VERTEX_BUFFER;
          vertexBufferDesc.CPUAccessFlags = 0;
          vertexBufferDesc.MiscFlags = 0;
          vertexBufferDesc.StructureByteStride = 0;

          D3D11_SUBRESOURCE_DATA vertexBufferData;
          vertexBufferData.pSysMem = triangleVertices;
          vertexBufferData.SysMemPitch = 0;
          vertexBufferData.SysMemSlicePitch = 0;

          ComPtr<ID3D11Buffer> vertexBuffer;
          DX::ThrowIfFailed(
              m_d3dDevice->CreateBuffer(
                  &vertexBufferDesc,
                  &vertexBufferData,
                  &vertexBuffer
                  )
              );

          D3D11_BUFFER_DESC indexBufferDesc;
          indexBufferDesc.ByteWidth = sizeof(unsigned short) * ARRAYSIZE(triangleIndices);
          indexBufferDesc.Usage = D3D11_USAGE_DEFAULT;
          indexBufferDesc.BindFlags = D3D11_BIND_INDEX_BUFFER;
          indexBufferDesc.CPUAccessFlags = 0;
          indexBufferDesc.MiscFlags = 0;
          indexBufferDesc.StructureByteStride = 0;

          D3D11_SUBRESOURCE_DATA indexBufferData;
          indexBufferData.pSysMem = triangleIndices;
          indexBufferData.SysMemPitch = 0;
          indexBufferData.SysMemSlicePitch = 0;

          ComPtr<ID3D11Buffer> indexBuffer;
          DX::ThrowIfFailed(
              m_d3dDevice->CreateBuffer(
                  &indexBufferDesc,
                  &indexBufferData,
                  &indexBuffer
                  )
              );
        });
```

Чтобы нарисовать желтый треугольник, мы используем вершинные и пиксельные шейдеры, структуру вершинного шейдера, а также буферы вершин и индексов.

### <a name="4-drawing-the-triangle-and-presenting-the-rendered-image"></a>4. Рисование треугольника и представление обработанного изображения

Для непрерывной обработки и вывода сцены на экран мы воспользуемся бесконечным циклом. Вызываем [**ID3D11DeviceContext::OMSetRenderTargets**](https://msdn.microsoft.com/library/windows/desktop/ff476464), чтобы указать однобуферную прорисовку в качестве цели вывода. Вызываем [**ID3D11DeviceContext::ClearRenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476388) с параметрами { 0.071f, 0.04f, 0.561f, 1.0f } для очистки однобуферной прорисовки до сплошного синего цвета.

В бесконечном цикле выполняется отрисовка желтого треугольника на синем фоне.

**Рисование желтого треугольника**

1.  Сначала мы вызываем метод [**ID3D11DeviceContext::IASetInputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476454), чтобы описать способ потоковой передачи данных буфера вершин на этап входной сборки.
2.  Далее вызываем [**ID3D11DeviceContext::IASetVertexBuffers**](https://msdn.microsoft.com/library/windows/desktop/ff476456) и [**ID3D11DeviceContext::IASetIndexBuffer**](https://msdn.microsoft.com/library/windows/desktop/ff476453), чтобы связать буферы индексов и вершин с этапом входной сборки.
3.  Затем вызываем [**ID3D11DeviceContext::IASetPrimitiveTopology**](https://msdn.microsoft.com/library/windows/desktop/ff476455) со значением [**D3D11\_PRIMITIVE\_TOPOLOGY\_TRIANGLESTRIP**](https://msdn.microsoft.com/library/windows/desktop/ff476189#D3D11_PRIMITIVE_TOPOLOGY_TRIANGLESTRIP), чтобы на этапе входной сборки данные вершин интерпретировались как полоса треугольников.
4.  Затем вызывается метод [**ID3D11DeviceContext::VSSetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476493) для инициализации стадии вершинного шейдера и метод [**ID3D11DeviceContext::PSSetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476472) для инициализации построителя текстур с кодом построителя текстур.
5.  В завершение вызывается метод [**ID3D11DeviceContext::DrawIndexed**](https://msdn.microsoft.com/library/windows/desktop/ff476409) для отрисовки треугольника и его передачи в канал визуализации.

Мы вызываем метод [**IDXGISwapChain::Present**](https://msdn.microsoft.com/library/windows/desktop/bb174576), чтобы представить отрисованное изображение в окне.

```cpp
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

            m_d3dDeviceContext->IASetInputLayout(inputLayout.Get());

            // Set the vertex and index buffers, and specify the way they define geometry.
            UINT stride = sizeof(float2);
            UINT offset = 0;
            m_d3dDeviceContext->IASetVertexBuffers(
                0,
                1,
                vertexBuffer.GetAddressOf(),
                &stride,
                &offset
                );

            m_d3dDeviceContext->IASetIndexBuffer(
                indexBuffer.Get(),
                DXGI_FORMAT_R16_UINT,
                0
                );

            m_d3dDeviceContext->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);

            // Set the vertex and pixel shader stage state.
            m_d3dDeviceContext->VSSetShader(
                vertexShader.Get(),
                nullptr,
                0
                );

            m_d3dDeviceContext->PSSetShader(
                pixelShader.Get(),
                nullptr,
                0
                );

            // Draw the cube.
            m_d3dDeviceContext->DrawIndexed(
                ARRAYSIZE(triangleIndices),
                0,
                0
                );

            // Present the rendered image to the window.  Because the maximum frame latency is set to 1,
            // the render loop will generally be throttled to the screen refresh rate, typically around
            // 60 Hz, by sleeping the application on Present until the screen is refreshed.
            DX::ThrowIfFailed(
                m_swapChain->Present(1, 0)
                );
```

## <a name="summary-and-next-steps"></a>Сводка и дальнейшие действия


Мы создаем и рисуем желтый треугольник при помощи построителя текстур и вершинного шейдера.

Затем мы создаем вращающийся трехмерный куб и применяем к нему эффекты освещения.

[Применение глубины и эффектов к примитивам](using-depth-and-effects-on-primitives.md)

 

 




