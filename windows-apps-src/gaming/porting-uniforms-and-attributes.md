---
author: mtoepke
title: Перенос буферов, однородных элементов и вершин OpenGLES2.0 в Direct3D
description: В процессе переноса в Direct3D 11 из OpenGL ES 2.0 необходимо изменить синтаксис и поведение API для передачи данных между приложением и программами-шейдерами.
ms.assetid: 9b215874-6549-80c5-cc70-c97b571c74fe
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, opengl, direct3d, буферы, однородности, атрибуты вершин
ms.localizationpriority: medium
ms.openlocfilehash: bc0192eb4b89ef91bc895a96e46cd39524f24c44
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5869191"
---
# <a name="compare-opengl-es-20-buffers-uniforms-and-vertex-attributes-to-direct3d"></a>Сравнение атрибутов буферов, однородностей и вершин OpenGL ES 2.0 с Direct3D




**Важные API**

-   [**ID3D11Device1::CreateBuffer**](https://msdn.microsoft.com/library/windows/desktop/hh404575)
-   [**ID3D11Device1::CreateInputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476512)
-   [**ID3D11DeviceContext1::IASetInputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476454)

В процессе переноса в Direct3D 11 из OpenGL ES 2.0 необходимо изменить синтаксис и поведение API для передачи данных между приложением и программами-шейдерами.

В OpenGL ES 2.0 используются следующие способы обмена данными с программами-шейдерами: постоянные данные — как однородные элементы (uniform), данные вершин — как атрибуты, данные других ресурсов (такие как текстуры) — как объекты буферов. В Direct3D 11 эти способы можно сопоставить с буферами констант, буферами вершин и подчиненными ресурсами. Несмотря на поверхностное сходство, при использовании они обрабатываются существенно по-разному.

Основные сопоставления:

| OpenGL ES 2.0             | Direct3D11                                                                                                                                                                         |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Однородный элемент                   | Поле буфера констант (**cbuffer**)                                                                                                                                                |
| Атрибут                 | Поле элемента буфера вершин, обозначенное в макете входных данных и отмеченное с помощью конкретной семантики HLSL                                                                                |
| Объект буфера             | Буфер (см. описания [**D3D11\_SUBRESOURCE\_DATA**](https://msdn.microsoft.com/library/windows/desktop/ff476220) и [**D3D11\_BUFFER\_DESC**](https://msdn.microsoft.com/library/windows/desktop/ff476092) и определения буферов общего использования) |
| Объект буфера кадров (FBO) | Целевые объекты обработчика (см. [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582) с [**ID3D11Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635))                                       |
| Задний буфер               | Цепочка буферов с поверхностью из «заднего буфера» (см. [**IDXGISwapChain1**](https://msdn.microsoft.com/library/windows/desktop/hh404631) с присоединением [**IDXGISurface1**](https://msdn.microsoft.com/library/windows/desktop/ff471343))                       |

 

## <a name="port-buffers"></a>Перенос буферов


В OpenGL ES 2.0 процесс создания и привязки буферов любого типа обычно укладывается в следующую схему:

-   Вызов glGenBuffers для создания одного или нескольких буферов и возвращения их дескрипторов.
-   Вызов glBindBuffer для определения макета буфера, например GL\_ELEMENT\_ARRAY\_BUFFER.
-   Вызов glBufferData для заполнения буфера конкретными данными (такими как структуры вершин, данные индексов или данные цвета) в определенном макете.

Наиболее часто используемым типом буферов является буфер вершин, который как минимум содержит позиции вершин в некоторой системе координат. При обычном использовании вершина представляется структурой, которая содержит координаты позиции, вектор нормали к позиции вершины, вектор касательной к позиции вершины и координаты подстановки текстуры (uv). Буфер содержит смежный список вершин в определенном порядке (например, список треугольников, полос или развертки веером). С помощью этих вершин визуально представляются многоугольники на вашей сцене. (В Direct3D 11, так же как и в OpenGL ES 2.0, неэффективно использование нескольких буферов вершин в одном вызове.)

Вот пример буфера вершин и буфера индексов в OpenGL ES 2.0:

OpenGL ES 2.0: создание и заполнение буфера вершин и буфера индексов

``` syntax
glGenBuffers(1, &renderer->vertexBuffer);
glBindBuffer(GL_ARRAY_BUFFER, renderer->vertexBuffer);
glBufferData(GL_ARRAY_BUFFER, sizeof(Vertex) * CUBE_VERTICES, renderer->vertices, GL_STATIC_DRAW);

glGenBuffers(1, &renderer->indexBuffer);
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, renderer->indexBuffer);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(int) * CUBE_INDICES, renderer->vertexIndices, GL_STATIC_DRAW);
```

В число других буферов входят построители текстуры и карты, такие как текстуры. Конвейер шейдера может обрабатывать буферы текстуры (пиксельные карты) или обрабатывать буферы объектов и использовать такие буферы в будущих проходах шейдера. В простейшем случае поток вызовов имеет вид:

-   Вызов glGenFramebuffers для создания объекта буфера кадра.
-   Вызов glBindFramebuffer для привязки объекта буфера кадра для записи.
-   Вызов glFramebufferTexture2D для рисования указанной карты текстуры.

В Direct3D 11 элементы данных буферов рассматриваются как «подчиненные ресурсы». В их число могут входить как отдельные элементы данных вершин, так и текстуры с MIP-картами.

-   Заполните структуру [**D3D11\_SUBRESOURCE\_DATA**](https://msdn.microsoft.com/library/windows/desktop/ff476220) данными конфигурации для элемента буфера данных.
-   Заполните структуру [**D3D11\_BUFFER\_DESC**](https://msdn.microsoft.com/library/windows/desktop/ff476092) данными о размерах отдельных элементов в буфере, а также о типе буфера.
-   Вызовите [**ID3D11Device1::CreateBuffer**](https://msdn.microsoft.com/library/windows/desktop/hh404575) с этими двумя структурами.

Direct3D 11: создание и заполнение буфера вершин и буфера индексов

``` syntax
D3D11_SUBRESOURCE_DATA vertexBufferData = {0};
vertexBufferData.pSysMem = cubeVertices;
vertexBufferData.SysMemPitch = 0;
vertexBufferData.SysMemSlicePitch = 0;
CD3D11_BUFFER_DESC vertexBufferDesc(sizeof(cubeVertices), D3D11_BIND_VERTEX_BUFFER);

m_d3dDevice->CreateBuffer(
  &vertexBufferDesc,
  &vertexBufferData,
  &m_vertexBuffer);

m_indexCount = ARRAYSIZE(cubeIndices);

D3D11_SUBRESOURCE_DATA indexBufferData = {0};
indexBufferData.pSysMem = cubeIndices;
indexBufferData.SysMemPitch = 0;
indexBufferData.SysMemSlicePitch = 0;
CD3D11_BUFFER_DESC indexBufferDesc(sizeof(cubeIndices), D3D11_BIND_INDEX_BUFFER);

m_d3dDevice->CreateBuffer(
  &indexBufferDesc,
  &indexBufferData,
  &m_indexBuffer);
    
```

Доступные для записи построители текстуры или карты, такие как буфер кадров, могут создаваться как объекты [**ID3D11Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635). Их можно привязать как ресурсы к [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582) или [**ID3D11ShaderResourceView**](https://msdn.microsoft.com/library/windows/desktop/ff476628), которые после отрисовки могут соответственно отображаться с привязанной цепочкой буферов или передаваться в шейдер.

Direct3D 11: создание объекта буфера кадров

``` syntax
ComPtr<ID3D11RenderTargetView> m_d3dRenderTargetViewWin;
// ...
ComPtr<ID3D11Texture2D> frameBuffer;

m_swapChainCoreWindow->GetBuffer(0, IID_PPV_ARGS(&frameBuffer));
m_d3dDevice->CreateRenderTargetView(
  frameBuffer.Get(),
  nullptr,
  &m_d3dRenderTargetViewWin);
```

## <a name="change-uniforms-and-uniform-buffer-objects-to-direct3d-constant-buffers"></a>Изменение однородных элементов и объектов буфера однородных элементов на буферы констант Direct3D


В OpenGL ES 2.0 однородные элементы являются механизмом для передачи постоянных данных в отдельные программы-шейдеры. Эти данные не могут изменяться внутри программ-шейдеров.

Задание однородных элементов обычно включает предоставление одного из методов glUniform\* с расположением передачи в GPU вместе с указателем на данные в памяти приложения. После выполнения метода glUniform\* данные однородных элементов находятся в памяти GPU и становятся доступными для шейдеров, которые объявили этот однородный элемент. Вы должны обеспечить такую упаковку данных, чтобы шейдер мог их интерпретировать на основании объявления однородного элемента в шейдере (с помощью совместимых типов).

OpenGL ES 2.0: создание однородного элемента и передача в него данных

``` syntax
renderer->mvpLoc = glGetUniformLocation(renderer->programObject, "u_mvpMatrix");

// ...

glUniformMatrix4fv(renderer->mvpLoc, 1, GL_FALSE, (GLfloat*) &renderer->mvpMatrix.m[0][0]);
```

В GLSL-коде шейдера соответствующее объявление однородного элемента выглядит примерно так:

OpenGL ES 2.0: объявление однородного элемента GLSL

``` syntax
uniform mat4 u_mvpMatrix;
```

Direct3D обозначает данные однородных элементов как "буферы констант", которые — аналогично однородным элементам — содержат постоянные данные, передаваемые в отдельные шейдеры. Как и для буферов однородных элементов, важно упаковать данные буферов констант в памяти соответственно способу, который ожидается шейдером для их интерпретации. Использование типов DirectXMath (таких как [**XMFLOAT4**](https://msdn.microsoft.com/library/windows/desktop/ee419608)) вместо типов платформы (таких как **float\*** или **float\[4\]**) гарантирует правильное выравнивание элементов данных.

Буферы констант должны иметь сопоставление с регистром GPU, который используется для ссылки на эти данные в GPU. Данные упаковываются в расположении регистра согласно макету буфера.

Direct3D 11: создание буфера констант и передача в него данных

``` syntax
struct ModelViewProjectionConstantBuffer
{
     DirectX::XMFLOAT4X4 mvp;
};

// ...

ModelViewProjectionConstantBuffer   m_constantBufferData;

// ...

XMStoreFloat4x4(&m_constantBufferData.mvp, mvpMatrix);

CD3D11_BUFFER_DESC constantBufferDesc(sizeof(ModelViewProjectionConstantBuffer), D3D11_BIND_CONSTANT_BUFFER);
m_d3dDevice->CreateBuffer(
  &constantBufferDesc,
  nullptr,
  &m_constantBuffer);
```

В HLSL-коде шейдера соответствующее объявление буфера констант выглядит примерно так:

Direct3D 11: объявление буфера констант HLSL

``` syntax
cbuffer ModelViewProjectionConstantBuffer : register(b0)
{
  matrix mvp;
};
```

Отметим, что регистр должен объявляться для каждого буфера констант. Разные уровней компонентов Direct3D имеют разные максимальные числа доступных регистров, поэтому не следует превышать это число для минимального уровня компонентов, для которого вы ведете разработку.

## <a name="port-vertex-attributes-to-a-direct3d-input-layouts-and-hlsl-semantics"></a>Перенос атрибутов вершин в макеты входных данных Direct3D и семантика HLSL


Поскольку данные вершин могут изменяться конвейером шейдера, OpenGL ES 2.0 требует, чтобы они задавались как "attribute" (атрибуты) вместо "uniform" (однородные элементы). (Это изменено в последних версиях OpenGL и GLSL.) Относящиеся к вершинам данные, такие как позиция вершины, нормали, касательные и значения цвета, передаются в шейдеры как значения атрибутов. Эти значения атрибутов соответствуют конкретным смещениям для каждого элемента данных вершины. Например, первый элемент может указывать компонент позиции отдельной вершины, второй — нормаль и т.д.

Базовый процесс перемещения данных буфера вершин из основной памяти в GPU выглядит примерно так:

-   Передача данных вершин с помощью glBindBuffer.
-   Получение расположения атрибутов в GPU с помощью glGetAttribLocation. Делайте этот вызов для каждого атрибута в элементе данных вершины.
-   Вызов glVertexAttribPointer, чтобы предоставить для атрибута набор правильных значений размера и смещения в отдельном элементе вектора данных. Делайте это для каждого атрибута.
-   Включение информации о макете данных вершины с помощью glEnableVertexAttribArray.

OpenGL ES 2.0: передача данных буфера вершин в атрибут шейдера

``` syntax
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, renderer->vertexBuffer);
loc = glGetAttribLocation(renderer->programObject, "a_position");

glVertexAttribPointer(loc, 3, GL_FLOAT, GL_FALSE, 
  sizeof(Vertex), 0);
loc = glGetAttribLocation(renderer->programObject, "a_color");
glEnableVertexAttribArray(loc);

glVertexAttribPointer(loc, 4, GL_FLOAT, GL_FALSE, 
  sizeof(Vertex), (GLvoid*) (sizeof(float) * 3));
glEnableVertexAttribArray(loc);
```

Теперь в вершинном шейдере следует объявить атрибуты с именами, уже определенными вами в вызове glGetAttribLocation.

OpenGL ES 2.0: объявление атрибута в GLSL

``` syntax
attribute vec4 a_position;
attribute vec4 a_color;                     
```

В некоторых случаях тот же процесс используется для Direct3D. Вместо атрибутов предоставляются данные вершин в буферах входных данных, которые включают буферы вершин и соответствующие буферы индексов. Однако поскольку в Direct3D нет объявления "attribute" (атрибут), необходимо указать макет входных данных, который объявляет отдельные компоненты элементов данных в буфере вершин, и семантику HLSL, которая указывает, где и как эти компоненты интерпретируются вершинным шейдером. Семантика HLSL требует, чтобы вы определили использование каждого компонента с помощью конкретной строки, которая информирует модуль шейдера о его предназначении. Например, данные позиции вершины отмечаются как POSITION, данные нормали отмечаются как NORMAL, а данные цвета вершины отмечаются как COLOR. (Для других стадий шейдера также требуется специфическая семантика, и у этой семантики имеются различные интерпретации, зависящие от стадии шейдера.) Дополнительные сведения о семантиках HLSL см. в разделах [Перенос конвейера шейдера](change-your-shader-loading-code.md) и [Семантики HLSL](https://msdn.microsoft.com/library/windows/desktop/bb205574).

Объединенный процесс установки буферов вершин и индексов и задания макета входных данных называют стадией "сборки входных данных" (IA) в конвейере графики Direct3D.

Direct3D 11: настройка стадии сборки входных данных

``` syntax
// Set up the IA stage corresponding to the current draw operation.
UINT stride = sizeof(VertexPositionColor);
UINT offset = 0;
m_d3dContext->IASetVertexBuffers(
        0,
        1,
        m_vertexBuffer.GetAddressOf(),
        &stride,
        &offset);

m_d3dContext->IASetIndexBuffer(
        m_indexBuffer.Get(),
        DXGI_FORMAT_R16_UINT,
        0);

m_d3dContext->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
m_d3dContext->IASetInputLayout(m_inputLayout.Get());
```

Для объявления макета входных данных и его связывания с вершинным шейдером объявляются формат каждого элемента данных вершины и семантика, используемая для каждого компонента. Макет элемента данных вершины, описанный в созданном вами элементе D3D11\_INPUT\_ELEMENT\_DESC, должен соответствовать макету соответствующей структуры. Здесь мы создаем макет для данных вершин с двумя компонентами:

-   Позиция вершины представлена в основной памяти как XMFLOAT3, то есть как ориентированный массив из трех 32-разрядных значений с плавающей точкой для координат (x, y, z).
-   Значение цвета вершины представлено в основной памяти как XMFLOAT4, то есть как ориентированный массив из четырех 32-разрядных значений с плавающей точкой для цвета (RGBA).

Вы назначаете семантику для каждого элемента и тип формата. Затем описание передается в [**ID3D11Device1::CreateInputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476512). Макет входных данных используется при вызове [**ID3D11DeviceContext1::IASetInputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476454), когда вы настраиваете сборку входных данных в нашем методе обработки.

Direct3D 11: описание макета входных данных с конкретной семантикой

``` syntax
ComPtr<ID3D11InputLayout> m_inputLayout;

// ...

const D3D11_INPUT_ELEMENT_DESC vertexDesc[] = 
{
  { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,  D3D11_INPUT_PER_VERTEX_DATA, 0 },
  { "COLOR",    0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 },
};

m_d3dDevice->CreateInputLayout(
  vertexDesc,
  ARRAYSIZE(vertexDesc),
  fileData->Data,
  fileData->Length,
  &m_inputLayout);

// ...
// When we start the drawing process...

m_d3dContext->IASetInputLayout(m_inputLayout.Get());
```

И наконец вы объявляете входные данные, чтобы гарантировать, что шейдер понимает входные данные. Семантика, назначенная макету, используется для выбора конкретных расположений в памяти GPU.

Direct3D 11: объявление входных данных шейдера с семантикой HLSL

``` syntax
struct VertexShaderInput
{
  float3 pos : POSITION;
  float3 color : COLOR;
};
```

 

 




