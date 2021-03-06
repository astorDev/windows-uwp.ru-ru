---
title: Преобразование инфраструктуры отрисовки
description: Здесь показано, как перенести простую инфраструктуру отрисовки с Direct3D 9 на Direct3D 11, включая перенос геометрических буферов, компиляцию и загрузку программ-шейдеров на языке HLSL, а также реализацию цепочки отрисовки в Direct3D 11.
ms.assetid: f6ca1147-9bb8-719a-9a2c-b7ee3e34bd18
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, платформа визуализации, преобразование, direct3d 9, direct3d 11
ms.localizationpriority: medium
ms.openlocfilehash: 6629ba035a7fb0085e28f3fa033e58a1c1105ccf
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66368023"
---
# <a name="convert-the-rendering-framework"></a>Преобразование инфраструктуры отрисовки



**Сводка**

-   [Часть 1. Инициализировать Direct3D 11](simple-port-from-direct3d-9-to-11-1-part-1--initializing-direct3d.md)
-   Часть 2. Преобразование инфраструктуры отрисовки
-   [Часть 3. Порт цикл игры](simple-port-from-direct3d-9-to-11-1-part-3--viewport-and-game-loop.md)


Здесь показано, как перенести простую инфраструктуру отрисовки с Direct3D 9 на Direct3D 11, включая перенос геометрических буферов, компиляцию и загрузку программ-шейдеров на языке HLSL, а также реализацию цепочки отрисовки в Direct3D 11. [Пошаговое руководство: портирование простого приложения с Direct3D 9 на DirectX 11 и на универсальную платформу для Windows (UWP)](walkthrough--simple-port-from-direct3d-9-to-11-1.md), часть 2.

## <a name="convert-effects-to-hlsl-shaders"></a>Преобразование эффектов в HLSL-шейдеры


Ниже приведен простой пример использования D3DX для аппаратного преобразования вершин и пропускания цветов, написанный с применением традиционного API эффектов.

Код шейдеров Direct3D 9

```cpp
// Global variables
matrix g_mWorld;        // world matrix for object
matrix g_View;          // view matrix
matrix g_Projection;    // projection matrix

// Shader pipeline structures
struct VS_OUTPUT
{
    float4 Position   : POSITION;   // vertex position
    float4 Color      : COLOR0;     // vertex diffuse color
};

struct PS_OUTPUT
{
    float4 RGBColor : COLOR0;  // Pixel color    
};

// Vertex shader
VS_OUTPUT RenderSceneVS(float3 vPos : POSITION, 
                        float3 vColor : COLOR0)
{
    VS_OUTPUT Output;
    
    float4 pos = float4(vPos, 1.0f);

    // Transform the position from object space to homogeneous projection space
    pos = mul(pos, g_mWorld);
    pos = mul(pos, g_View);
    pos = mul(pos, g_Projection);

    Output.Position = pos;
    
    // Just pass through the color data
    Output.Color = float4(vColor, 1.0f);
    
    return Output;
}

// Pixel shader
PS_OUTPUT RenderScenePS(VS_OUTPUT In) 
{ 
    PS_OUTPUT Output;

    Output.RGBColor = In.Color;

    return Output;
}

// Technique
technique RenderSceneSimple
{
    pass P0
    {          
        VertexShader = compile vs_2_0 RenderSceneVS();
        PixelShader  = compile ps_2_0 RenderScenePS(); 
    }
}
```

В Direct3D 11 мы по-прежнему можем использовать шейдеры на языке HLSL. Каждый шейдер мы помещаем в собственный HLSL-файл, чтобы среда Visual Studio скомпилировала их в отдельные файлы. Потом мы будем загружать их как отдельные ресурсы Direct3D. Мы установите целевой уровень [шейдера модели 4 уровня 9\_1 (/ 4\_0\_уровень\_9\_1)](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-devices-downlevel-intro) так, как эти построители текстур пишутся для графических процессоров 9.1 DirectX.

Определяя входной макет, мы позаботились о том, чтобы он представлял ту же структуру данных, которую мы используем для хранения данных каждой вершины в системной памяти и памяти графического процессора. Точно так же выходные данные вершинного шейдера должны соответствовать структуре, подаваемой на вход пиксельного шейдера. Правила здесь не такие, как при передаче данных от одной функции к другой в C++: неиспользуемые переменные в конце структуры можно опустить. Но изменять порядок и пропускать элементы в середине структуры данных нельзя.

> **Примечание**    правила в Direct3D 9 для шейдеров вершин привязки к построители текстуры были менее жесткой, чем правила в Direct3D 11. Схема, использовавшаяся в Direct3D 9, была гибкой, но нерациональной.

 

Возможно, что файлы HLSL использует старого синтаксиса для шейдера семантику - например, ЦВЕТ вместо SV\_целевой объект. В этом случае вам необходимо будет включить режим совместимости HLSL (параметр компилятора /Gec) или обновить [семантики](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-semantics) шейдеров, перейдя на текущий синтаксис. В этом примере вершинный шейдер был переписан с использованием нового синтаксиса.

Вот наш вершинный шейдер с аппаратным преобразованием, определенный на этот раз в собственном файле.

> **Примечание**  шейдеры вершин необходимы для вывода SV\_семантической значение system ПОЗИЦИИ. Эта семантика преобразует данные о положении вершины в значения координат, где x находится в диапазоне от –1 до 1, y находится в диапазоне от –1 до 1, z делится на исходное значение однородной координаты w (z/w), а w равняется единице, деленной на исходное значение w (1/w).

 

Вершинный HLSL-шейдер (уровень компонентов 9.1)

```cpp
cbuffer ModelViewProjectionConstantBuffer : register(b0)
{
    matrix mWorld;       // world matrix for object
    matrix View;        // view matrix
    matrix Projection;  // projection matrix
};

struct VS_INPUT
{
    float3 vPos   : POSITION;
    float3 vColor : COLOR0;
};

struct VS_OUTPUT
{
    float4 Position : SV_POSITION; // Vertex shaders must output SV_POSITION
    float4 Color    : COLOR0;
};

VS_OUTPUT main(VS_INPUT input) // main is the default function name
{
    VS_OUTPUT Output;

    float4 pos = float4(input.vPos, 1.0f);

    // Transform the position from object space to homogeneous projection space
    pos = mul(pos, mWorld);
    pos = mul(pos, View);
    pos = mul(pos, Projection);
    Output.Position = pos;

    // Just pass through the color data
    Output.Color = float4(input.vColor, 1.0f);

    return Output;
}
```

Это все, что нам нужно для прозрачного пиксельного шейдера. Хотя мы называем его прозрачным, на самом деле он выдает интерполированные цветовые данные для каждого пикселя с поправкой на перспективу. Обратите внимание, что SV\_системы ЦЕЛЕВОЕ значение семантической применяется к выходное значение цвета с шейдера пикселей при необходимости API-интерфейсом.

> **Примечание**  шейдера уровень 9\_x пиксель, шейдеры не удается считать из SV\_семантической значение system ПОЗИЦИИ. Построители текстуры 4.0 (и более поздних версий) модели можно использовать SV\_ПОЗИЦИИ для получения расположения указывается пикселей на экране, где x — между 0 и ширину целевой объект отрисовки и y — от 0 до высоты целевой объект отрисовки (каждым смещением, 0,5).

 

Большинство пиксельных шейдеров устроены сложнее, нежели прозрачный шейдер. Обратите внимание, что на более высоких уровнях компонентов Direct3D в одной программе-шейдере допускается гораздо большее количество вычислений.

Пиксельный HLSL-шейдер (уровень компонентов 9.1)

```cpp
struct PS_INPUT
{
    float4 Position : SV_POSITION;  // interpolated vertex position (system value)
    float4 Color    : COLOR0;       // interpolated diffuse color
};

struct PS_OUTPUT
{
    float4 RGBColor : SV_TARGET;  // pixel color (your PS computes this system value)
};

PS_OUTPUT main(PS_INPUT In)
{
    PS_OUTPUT Output;

    Output.RGBColor = In.Color;

    return Output;
}
```

## <a name="compile-and-load-shaders"></a>Компиляция и загрузка шейдеров


В играх на платформе Direct3D 9 часто используется библиотека эффектов как удобный способ реализации программируемых конвейеров. Эффекты могли компилироваться во время выполнения с использованием метода [**D3DXCreateEffectFromFile function**](https://docs.microsoft.com/windows/desktop/direct3d9/d3dxcreateeffectfromfile).

Загрузка эффекта в Direct3D 9

```cpp
// Turn off preshader optimization to keep calculations on the GPU
DWORD dwShaderFlags = D3DXSHADER_NO_PRESHADER;

// Only enable debug info when compiling for a debug target
#if defined (DEBUG) || defined (_DEBUG)
dwShaderFlags |= D3DXSHADER_DEBUG;
#endif

D3DXCreateEffectFromFile(
    m_pd3dDevice,
    L"CubeShaders.fx",
    NULL,
    NULL,
    dwShaderFlags,
    NULL,
    &m_pEffect,
    NULL
    );
```

Direct3D 11 работает с программами-шейдерами как с двоичными ресурсами. Шейдеры компилируются при сборке проекта и затем обрабатываются как ресурсы. В этом примере мы загрузим байт-код шейдеров в системную память, для каждого шейдера создадим ресурс Direct3D с использованием интерфейса устройства Direct3D, а при создании каждого кадра дадим указатель на ресурсы шейдера Direct3D.

Загрузка ресурса шейдера в Direct3D 11

```cpp
// BasicReaderWriter is a tested file loader used in SDK samples.
BasicReaderWriter^ readerWriter = ref new BasicReaderWriter();


// Load vertex shader:
Platform::Array<byte>^ vertexShaderData =
    readerWriter->ReadData("CubeVertexShader.cso");

// This call allocates a device resource, validates the vertex shader 
// with the device feature level, and stores the vertex shader bits in 
// graphics memory.
m_d3dDevice->CreateVertexShader(
    vertexShaderData->Data,
    vertexShaderData->Length,
    nullptr,
    &m_vertexShader
    );
```

Чтобы включить байт-код шейдеров в скомпилированный пакет приложения, просто добавьте HLSL-файл в проект Visual Studio. Visual Studio скомпилирует HLSL-файлы в двоичные объекты шейдера (CSO-файлы) с помощью [компилятора эффектов](https://docs.microsoft.com/windows/desktop/direct3dtools/fxc) (FXC) и включит их в пакет приложения.

> **Примечание**    не забудьте задать правильную цель функциональный уровень для компилятора HLSL: щелкните правой кнопкой мыши в HLSL исходный файл в Visual Studio, выберите свойства и изменить **модель шейдера** в разделе **Компилятора HLSL -&gt; Общие**. Direct3D проверяет это свойство на соответствие возможностям оборудования, когда ваше приложение создает ресурс шейдера Direct3D.

 

![Свойства HLSL-шейдеров](images/hlslshaderpropertiesmenu.png)![Тип HLSL-шейдера](images/hlslshadertypeproperties.png)

Это удачное место для создания входного макета, который соответствует объявлению потока вершин в Direct3D 9. Структура данных вершины должна соответствовать той, которую использует вершинный шейдер. В Direct3D 11 у нас больше контроля над входным макетом: мы можем задавать размер массива, разрядность векторов с плавающей точкой и семантики для вершинного шейдера. Мы создадим [ **D3D11\_ввода\_элемент\_DESC** ](https://docs.microsoft.com/windows/desktop/api/d3d11/ns-d3d11-d3d11_input_element_desc) структурировать и использовать его для информирования Direct3D, как будет выглядеть данные отдельных вершин. Прежде чем определять входной макет, мы дождались загрузки вершинного шейдера, так как API проверяет входной макет на соответствие ресурсу вершинного шейдера. В случае несовместимого входного макета Direct3D генерирует исключение.

Данные вершин должны храниться в системной памяти с использованием совместимых типов данных. Типы данных DirectXMath может помочь; Например, DXGI\_ФОРМАТ\_R32G32B32\_соответствует FLOAT [ **XMFLOAT3**](https://docs.microsoft.com/windows/desktop/api/directxmath/ns-directxmath-xmfloat3).

> **Примечание**    буферы констант использовать фиксированный входной макет, выравниваемое по четырех чисел с плавающей запятой, за раз. [**XMFLOAT4** ](https://docs.microsoft.com/windows/desktop/api/directxmath/ns-directxmath-xmfloat4) (и его производные) рекомендуются для данных буфера констант.

 

Задание входного макета в Direct3D 11

```cpp
// Create input layout:
const D3D11_INPUT_ELEMENT_DESC vertexDesc[] = 
{
    { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT,
        0, 0,  D3D11_INPUT_PER_VERTEX_DATA, 0 },

    { "COLOR",    0, DXGI_FORMAT_R32G32B32_FLOAT, 
        0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 },
};
```

## <a name="create-geometry-resources"></a>Создание геометрических макетов


В Direct3D 9 мы хранили геометрические ресурсы в устройстве Direct3D, блокируя память и копируя данные из памяти центрального процессора в память графического процессора.

Direct3D 9

```cpp
// Create vertex buffer:
VOID* pVertices;

// In Direct3D 9 we create the buffer, lock it, and copy the data from 
// system memory to graphics memory.
m_pd3dDevice->CreateVertexBuffer(
    sizeof(CubeVertices),
    0,
    D3DFVF_XYZ | D3DFVF_DIFFUSE,
    D3DPOOL_MANAGED,
    &pVertexBuffer,
    NULL);

pVertexBuffer->Lock(
    0,
    sizeof(CubeVertices),
    &pVertices,
    0);

memcpy(pVertices, CubeVertices, sizeof(CubeVertices));
pVertexBuffer->Unlock();
```

В DirectX 11 процесс устроен проще. API автоматически копирует данные из системной памяти в графический процессор. Чтобы облегчить программирование, мы можем использовать интеллектуальные указатели COM.

DirectX 11

```cpp
D3D11_SUBRESOURCE_DATA vertexBufferData = {0};
vertexBufferData.pSysMem = CubeVertices;
vertexBufferData.SysMemPitch = 0;
vertexBufferData.SysMemSlicePitch = 0;
CD3D11_BUFFER_DESC vertexBufferDesc(
    sizeof(CubeVertices),
    D3D11_BIND_VERTEX_BUFFER);
  
// This call allocates a device resource for the vertex buffer and copies
// in the data.
m_d3dDevice->CreateBuffer(
    &vertexBufferDesc,
    &vertexBufferData,
    &m_vertexBuffer
    );
```

## <a name="implement-the-rendering-chain"></a>Реализация цепочки отрисовки


В играх на платформе Direct3D 9 часто используется цепочка отрисовки на основе эффектов. В цепочке этого типа создается объект эффекта, ему предоставляются необходимые ресурсы, а затем он отрисовывает каждый проход.

Цепочка отрисовки Direct3D 9

```cpp
// Clear the render target and the z-buffer.
m_pd3dDevice->Clear(
    0, NULL,
    D3DCLEAR_TARGET | D3DCLEAR_ZBUFFER,
    D3DCOLOR_ARGB(0, 45, 50, 170),
    1.0f, 0
    );

// Set the effect technique
m_pEffect->SetTechnique("RenderSceneSimple");

// Rotate the cube 1 degree per frame.
D3DXMATRIX world;
D3DXMatrixRotationY(&world, D3DXToRadian(m_frameCount++));


// Set the matrices up using traditional functions.
m_pEffect->SetMatrix("g_mWorld", &world);
m_pEffect->SetMatrix("g_View", &m_view);
m_pEffect->SetMatrix("g_Projection", &m_projection);

// Render the scene using the Effects library.
if(SUCCEEDED(m_pd3dDevice->BeginScene()))
{
    // Begin rendering effect passes.
    UINT passes = 0;
    m_pEffect->Begin(&passes, 0);
    
    for (UINT i = 0; i < passes; i++)
    {
        m_pEffect->BeginPass(i);
        
        // Send vertex data to the pipeline.
        m_pd3dDevice->SetFVF(D3DFVF_XYZ | D3DFVF_DIFFUSE);
        m_pd3dDevice->SetStreamSource(
            0, pVertexBuffer,
            0, sizeof(VertexPositionColor)
            );
        m_pd3dDevice->SetIndices(pIndexBuffer);
        
        // Draw the cube.
        m_pd3dDevice->DrawIndexedPrimitive(
            D3DPT_TRIANGLELIST,
            0, 0, 8, 0, 12
            );
        m_pEffect->EndPass();
    }
    m_pEffect->End();
    
    // End drawing.
    m_pd3dDevice->EndScene();
}

// Present frame:
// Show the frame on the primary surface.
m_pd3dDevice->Present(NULL, NULL, NULL, NULL);
```

Цепочка отрисовки DirectX 11 выполняет те же задачи, но проходы отрисовки необходимо реализовать по-другому. Вместо того чтобы помещать всю специфичную часть в FX-файлы и делать методики отрисовки более или менее непрозрачными для нашего кода на C++, мы организуем всю отрисовку в C++.

Вот как будет выглядеть наша цепочка отрисовки. Нам необходимо предоставить входной макет, который мы создали после загрузки вершинного шейдера, и все объекты шейдеров, а также задать буферы констант для каждого шейдера. В этом примере не показана многопроходная отрисовка, но если бы мы взялись ее реализовать, то сделали бы аналогичную цепочку для каждого прохода, меняя конфигурацию по необходимости.

Цепочка отрисовки Direct3D 11

```cpp
// Clear the back buffer.
const float midnightBlue[] = { 0.098f, 0.098f, 0.439f, 1.000f };
m_d3dContext->ClearRenderTargetView(
    m_renderTargetView.Get(),
    midnightBlue
    );

// Set the render target. This starts the drawing operation.
m_d3dContext->OMSetRenderTargets(
    1,  // number of render target views for this drawing operation.
    m_renderTargetView.GetAddressOf(),
    nullptr
    );


// Rotate the cube 1 degree per frame.
XMStoreFloat4x4(
    &m_constantBufferData.model, 
    XMMatrixTranspose(XMMatrixRotationY(m_frameCount++ * XM_PI / 180.f))
    );

// Copy the updated constant buffer from system memory to video memory.
m_d3dContext->UpdateSubresource(
    m_constantBuffer.Get(),
    0,      // update the 0th subresource
    NULL,   // use the whole destination
    &m_constantBufferData,
    0,      // default pitch
    0       // default pitch
    );


// Send vertex data to the Input Assembler stage.
UINT stride = sizeof(VertexPositionColor);
UINT offset = 0;

m_d3dContext->IASetVertexBuffers(
    0,  // start with the first vertex buffer
    1,  // one vertex buffer
    m_vertexBuffer.GetAddressOf(),
    &stride,
    &offset
    );

m_d3dContext->IASetIndexBuffer(
    m_indexBuffer.Get(),
    DXGI_FORMAT_R16_UINT,
    0   // no offset
    );

m_d3dContext->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
m_d3dContext->IASetInputLayout(m_inputLayout.Get());


// Set the vertex shader.
m_d3dContext->VSSetShader(
    m_vertexShader.Get(),
    nullptr,
    0
    );

// Set the vertex shader constant buffer data.
m_d3dContext->VSSetConstantBuffers(
    0,  // register 0
    1,  // one constant buffer
    m_constantBuffer.GetAddressOf()
    );


// Set the pixel shader.
m_d3dContext->PSSetShader(
    m_pixelShader.Get(),
    nullptr,
    0
    );


// Draw the cube.
m_d3dContext->DrawIndexed(
    m_indexCount,
    0,  // start with index 0
    0   // start with vertex 0
    );
```

Цепочка буферов является частью графической инфраструктуры, поэтому мы воспользуемся нашей цепочкой буферов DXGI для представления готового кадра. DXGI блокирует вызов до следующего интервала вертикальной синхронизации, а затем завершается, после чего игровой цикл может продолжаться до следующей итерации.

Представление кадра на экране с использованием DirectX 11

```cpp
m_swapChain->Present(1, 0);
```

Цепочка отрисовки, которую мы только что создали, будет вызываться из игрового цикла, реализованного в методе [**IFrameworkView::Run**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.run). Это показано в [часть 3: Цикл просмотра и игра](simple-port-from-direct3d-9-to-11-1-part-3--viewport-and-game-loop.md).

 

 




