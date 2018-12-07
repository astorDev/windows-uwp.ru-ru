---
title: Введение в отрисовку
description: Узнайте, как собрать конвейер отрисовки для отображения графических объектов. Введение в отрисовку.
ms.assetid: 1da3670b-2067-576f-da50-5eba2f88b3e6
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, отрисовка
ms.localizationpriority: medium
ms.openlocfilehash: 6724aedf898706dd4c5bf728616c918d64b2fb32
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8780203"
---
# <a name="rendering-framework-i-intro-to-rendering"></a>Платформа отрисовки I: введение в отрисовку

В предыдущих статьях мы рассмотрели, как структурировать игру универсальной платформы Windows (UWP) для работы в среде выполнения Windows, а также как определить конечный автомат для обработки потока игры. Теперь пора поговорить о построении платформы отрисовки. Давайте рассмотрим, как игра-пример отрисовывает игровую сцену с помощью Direct3D11 (которую обычно называют DirectX 11).

>[!Note]
>Если вы еще не скачали последний код для игры из этого примера, перейдите в раздел [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Инструкции по скачиванию этого примера приводятся в статье [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

Direct3D 11 содержит набор API, которые обеспечивают доступ к возможностям высокопроизводительного графического оборудования. Эти возможности можно использовать для создания трехмерной графики для приложений с интенсивным использованием графики, таких как игры.

Отрисовка графики игры на экране по сути представляет собой отрисовку последовательности кадров. В каждом кадре необходимо отрисовать объекты, которые видны в сцене, в зависимости от представления. 

Для отрисовки кадра необходимо передать оборудованию требуемую информацию о сцене, чтобы ее можно было отобразить на экране. Если вы хотите, чтобы на экране что-нибудь отображалось, начинать отрисовку необходимо сразу же после запуска игры.

## <a name="objective"></a>Цель

Настроить базовую платформу отрисовки для отображения графического вывода для игры UWP на базе DirectX. Этот процесс можно разделить на три этапа.

 1. Подключение к графическому интерфейсу
 2. Создание ресурсов, необходимых для рисования графики
 3. Отображение графики путем отрисовки кадра

В этой статье рассматривается, как отрисовывается графика, т.е. этапы 1 и 3.

Этап 2— как создать платформу отрисовки и как происходит подготовка данных перед отрисовкой— рассматривается в статье [Платформа отрисовки II: отрисовка игры](tutorial-game-rendering.md).

## <a name="get-started"></a>С чего начать

Прежде чем начать, необходимо ознакомиться с основными понятиями, связанными с графикой и отрисовкой. Если вы не знакомы с Direct3D и отрисовкой, обратитесь к статье [Термины и понятия](#terms-and-concepts), где приводится краткое описание связанных с графикой и отрисовкой терминов, используемых в этой статье.

В этой несложной игре отрисовщик представлен объектом класса __GameRenderer__.  Отрисовщик отвечает за создание и поддержание всех объектов Direct3D 11 и Direct2D , используемых для создания визуальных элементов игры.  Кроме того, он содержит ссылку на объект __Simple3DGame__, используемый для получения списка объектов для отрисовки, а также состояния игры для элементов HUD. 

В этой части учебника мы подробно рассмотрим отрисовку трехмерных объектов в игре.

## <a name="establish-a-connection-to-the-graphics-interface"></a>Подключение к графическому интерфейсу

О доступе к оборудованию для отрисовки см. в статье о платформе UWP в описании метода [__App::Initialize__](tutorial--building-the-games-uwp-app-framework.md#appinitialize-method).

Функция __make\_shared__, как показано [ниже](#appinitialize-method), используется для создания указателя __shared\_ptr__ на объект [__DX::DeviceResources__](#dxdeviceresources), который также обеспечивает доступ к устройству. 

В Direct3D 11 [устройство](#device) используется для выделения памяти под объекты и их уничтожения, отрисовки примитивов, а также обмена данными с графической картой через графический драйвер.

### <a name="appinitialize-method"></a>Метод App::Initialize

```cpp
void App::Initialize(
    CoreApplicationView^ applicationView
    )
{
    //...

    // At this point we have access to the device. 
    // We can create the device-dependent resources.
    m_deviceResources = std::make_shared<DX::DeviceResources>();
}
```

## <a name="display-the-graphics-by-rendering-the-frame"></a>Отображение графики путем отрисовки кадра

При запуске игры необходимо отрисовать игровую сцену. Инструкции по отрисовке запускаются в методе [__GameMain::Run__](#gameamainrun-method), как показано ниже.

Поток операций следующий:
1. __Обновление__
2. __Отрисовка__
3. __Представление__

### <a name="gamemainrun-method"></a>Метод GameMain::Run

```cpp
// Comparing this to App::Run() in the DirectX 11 App template
void GameMain::Run()
{
    while (!m_windowClosed)
    {
        if (m_visible) // if the window is visible
        {
            // Added: Switching different game states since this game sample is more complex
            switch (m_updateState) 
            {

                // ...
                CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);
                // 1. Update
                // Also exist in DirectX 11 App template: Update loop to get the latest game changes
                Update();
                
                // 2. Render
                // Present also in DirectX 11 App template: Prepare to render
                m_renderer->Render();
                
                // 3. Present
                // Present also in DirectX 11 App template: Present the 
                // contents of the swap chain to the screen.
                m_deviceResources->Present(); 
                
                // Added: resetting a variable we've added to indicate that 
                // render has been done and no longer needed to render
                m_renderNeeded = false; 
            }
        }
        else
        {   
            // Present also in DirectX 11 App template
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending); 
        }
    }
    m_game->OnSuspending();  // exiting due to window close. Make sure to save state.
}
```

### <a name="update"></a>Обновление

См. статью [Управление потоком игры](tutorial-game-flow-management.md), где подробнее рассматривается обновление состояний игры в методе [__App::Update__ и __GameMain::Update__](tutorial-game-flow-management.md#appupdate-method).

### <a name="render"></a>Отрисовка

Отрисовка реализуется путем вызова метода [__GameRenderer::Render__](#gamerendererrender-method) в __GameMain::Run__.

Если включена [стереоскопическая отрисовка](#stereo-rendering), проходов отрисовки два: один для правого глаза, второй для левого. На каждом проходе отрисовки мы привязываем целевой объект отрисовки и [представление трафарета глубины](#depth-stencil-view) к устройству. После этого мы также очищаем представление трафарета глубины.

> [!Note]
> Обеспечить стереоскопическую отрисовку можно и другими способами, например путем стереоскопической отрисовки в один проход с использованием создания экземпляров вершин или шейдеров геометрии. Отрисовка в два прохода— более медленный, но и более удобный способ реализовать стереоскопическую отрисовку.

Как только игра существует и ресурсы загружены, необходимо обновлять [матрицу проекции](#projection-transform-matrix)— один раз при каждом проходе отрисовки. Объекты из каждого представления слегка отличаются друг от друга. Следующий шаг— создать [конвейер отрисовки графики](#rendering-pipeline). 

> [!Note]
> Подробнее о том, как загружаются ресурсы, см. в статье [Создание и загрузка графических ресурсов DirectX](tutorial-game-rendering.md#create-and-load-directx-graphic-resources).

В этом примере игры отрисовщик рассчитан на использование стандартного вершинного макета для всех объектов. Это упрощает разработку шейдеров и позволяет быстро переходить от одного шейдера к другому, вне зависимости от геометрии объектов.

#### <a name="gamerendererrender-method"></a>Метод GameRenderer::Render

Контекст Direct3D настраивается на использование входного вершинного макета. Объекты входного макета описывают, как данные буфера вершин передаются в [конвейер отрисовки](#rendering-pipeline). 

Следующий шаг— настроить контекст Direct3D на использование ранее определенных [буферов констант](#constant-buffers), используемых на двух этапах конвейера: [вершинными шейдерами](#vertex-shaders-and-pixel-shaders) и [построителями текстур](#vertex-shaders-and-pixel-shaders). 

> [!Note]
> Подробнее об определении буферов констант см. в статье [Платформа отрисовки II: отрисовка игры](tutorial-game-rendering.md).

Поскольку для всех шейдеров в конвейере используется один и тот же входной макет и набор буферов констант, они задаются по одному разу на кадр.

```cpp
void GameRenderer::Render()
{
    bool stereoEnabled = m_deviceResources->GetStereoState();

    auto d3dContext = m_deviceResources->GetD3DDeviceContext();
    auto d2dContext = m_deviceResources->GetD2DDeviceContext();

    int renderingPasses = 1;
    if (stereoEnabled)
    {
        renderingPasses = 2;
    }

    for (int i = 0; i < renderingPasses; i++)
    {
        // Iterate through the number of rendering passes to be completed.
        // 2 rendering passes if stereo is enabled
        if (i > 0)
        {
            // Doing the Right Eye View.
            ID3D11RenderTargetView *const targets[1] = { m_deviceResources->GetBackBufferRenderTargetViewRight() };

            // Resets render targets to the screen.
            // OMSetRenderTargets binds 2 things to the device.
            // 1. Binds one render target atomically to the device.
            // 2. Binds the depth-stencil view, as returned by the GetDepthStencilView method, to the device.
            // For more info, go to: https://msdn.microsoft.com/library/windows/desktop/ff476464.aspx

            d3dContext->OMSetRenderTargets(1, targets, m_deviceResources->GetDepthStencilView());
            
            // Clears the depth stencil view.
            // A depth stencil view contains the format and buffer to hold depth and stencil info.
            // For more info about depth stencil view, go to: 
            // https://docs.microsoft.com/windows/uwp/graphics-concepts/depth-stencil-view--dsv-
            // A depth buffer is used to store depth information to control which areas of 
            // polygons are rendered rather than hidden from view. To learn more about a depth buffer,
            // go to: https://docs.microsoft.com/windows/uwp/graphics-concepts/depth-buffers
            // A stencil buffer is used to mask pixels in an image, to produce special effects. 
            // The mask determines whether a pixel is drawn or not,
            // by setting the bit to a 1 or 0. To learn more about a stencil buffer,
            // go to: https://docs.microsoft.com/windows/uwp/graphics-concepts/stencil-buffers

            d3dContext->ClearDepthStencilView(m_deviceResources->GetDepthStencilView(), D3D11_CLEAR_DEPTH, 1.0f, 0);
            
            // d2d -- Discussed later
            d2dContext->SetTarget(m_deviceResources->GetD2DTargetBitmapRight());
        }
        else
        {
            // Doing the Mono or Left Eye View.
            // As compared to the right eye:
            // m_deviceResources->GetBackBufferRenderTargetView instead of GetBackBufferRenderTargetViewRight
            ID3D11RenderTargetView *const targets[1] = { m_deviceResources->GetBackBufferRenderTargetView() }; 
            
            // Same as the Right Eye View.
            d3dContext->OMSetRenderTargets(1, targets, m_deviceResources->GetDepthStencilView());
            d3dContext->ClearDepthStencilView(m_deviceResources->GetDepthStencilView(), D3D11_CLEAR_DEPTH, 1.0f, 0);
            
            // d2d -- Discussed later under Adding UI
            d2dContext->SetTarget(m_deviceResources->GetD2DTargetBitmap()); 
        }

        // Render the scene objects
        if (m_game != nullptr && m_gameResourcesLoaded && m_levelResourcesLoaded)
        {
            // This section is only used after the game state has been initialized and all device
            // resources needed for the game have been created and associated with the game objects.
            if (stereoEnabled)
            {
                // When doing stereo, it is necessary to update the projection matrix once per rendering pass.

                auto orientation = m_deviceResources->GetOrientationTransform3D();

                ConstantBufferChangeOnResize changesOnResize;

                // Apply either a left or right eye projection, which is an offset from the middle
                XMStoreFloat4x4(
                    &changesOnResize.projection,
                    XMMatrixMultiply(
                        XMMatrixTranspose(
                            i == 0 ?
                            m_game->GameCamera()->LeftEyeProjection() :
                            m_game->GameCamera()->RightEyeProjection()
                            ),
                        XMMatrixTranspose(XMLoadFloat4x4(&orientation))
                        )
                    );

                d3dContext->UpdateSubresource(
                    m_constantBufferChangeOnResize.Get(),
                    0,
                    nullptr,
                    &changesOnResize,
                    0,
                    0
                    );
            }

            // Update variables that change once per frame.
            ConstantBufferChangesEveryFrame constantBufferChangesEveryFrame;
            XMStoreFloat4x4(
                &constantBufferChangesEveryFrame.view,
                XMMatrixTranspose(m_game->GameCamera()->View())
                );
            d3dContext->UpdateSubresource(
                m_constantBufferChangesEveryFrame.Get(),
                0,
                nullptr,
                &constantBufferChangesEveryFrame,
                0,
                0
                );

            // Setup the graphics pipeline. This sample uses the same InputLayout and set of
            // constant buffers for all shaders, so they only need to be set once per frame.
            // For more info about the graphics or rendering pipeline, 
            // go to https://msdn.microsoft.com/library/windows/desktop/ff476882.aspx

            // IASetInputLayout binds an input-layout object to the input-assembler (IA) stage. 
            // Input-layout objects describe how vertex buffer data is streamed into the IA pipeline stage.
            // Set up the Direct3D context to use this vertex layout.
            // For more info, go to: https://msdn.microsoft.com/library/windows/desktop/ff476454.aspx
            d3dContext->IASetInputLayout(m_vertexLayout.Get());

            // VSSetConstantBuffers sets the constant buffers used by the vertex shader pipeline stage.
            // Set up the Direct3D context to use these constant buffers.
            // For more info, go to: https://msdn.microsoft.com/library/windows/desktop/ff476491.aspx

            d3dContext->VSSetConstantBuffers(0, 1, m_constantBufferNeverChanges.GetAddressOf());
            d3dContext->VSSetConstantBuffers(1, 1, m_constantBufferChangeOnResize.GetAddressOf());
            d3dContext->VSSetConstantBuffers(2, 1, m_constantBufferChangesEveryFrame.GetAddressOf());
            d3dContext->VSSetConstantBuffers(3, 1, m_constantBufferChangesEveryPrim.GetAddressOf());

            // Sets the constant buffers used by the pixel shader pipeline stage. 
            // For more info, go to: https://msdn.microsoft.com/library/windows/desktop/ff476470.aspx

            d3dContext->PSSetConstantBuffers(2, 1, m_constantBufferChangesEveryFrame.GetAddressOf());
            d3dContext->PSSetConstantBuffers(3, 1, m_constantBufferChangesEveryPrim.GetAddressOf());
            d3dContext->PSSetSamplers(0, 1, m_samplerLinear.GetAddressOf());

            auto objects = m_game->RenderObjects();
            for (auto object = objects.begin(); object != objects.end(); object++)
            {
                // The 3D object render method handles the rendering.
                // For more info, see Primitive rendering below.
                (*object)->Render(d3dContext, m_constantBufferChangesEveryPrim.Get()); /
            }
        }
        else
        {
            const float ClearColor[4] = {0.5f, 0.5f, 0.8f, 1.0f};

            // Only need to clear the background when not rendering the full 3D scene since
            // the 3D world is a fully enclosed box and the dynamics prevents the camera from
            // moving outside this space.
            if (i > 0)
            {
                // Doing the Right Eye View.
                d3dContext->ClearRenderTargetView(m_deviceResources->GetBackBufferRenderTargetViewRight(), ClearColor);
            }
            else
            {
                // Doing the Mono or Left Eye View.
                d3dContext->ClearRenderTargetView(m_deviceResources->GetBackBufferRenderTargetView(), ClearColor);
            }
        }

        // Start of 2D rendering
        d3dContext->BeginEventInt(L"D2D BeginDraw", 1);
        d2dContext->BeginDraw();

        // ...
    }
}
```

### <a name="primitive-rendering"></a>Отрисовка примитивов

При отрисовке сцены необходимо перебрать все объекты, которые должны быть отрисованы. Указанные ниже шаги повторяются для каждого объекта (примитива).

* Обновите буфер констант (__m\_constantBufferChangesEveryPrim__), добавив [матрицу мирового преобразования](#world-transform-matrix) и информацию о материалах.
* __m\_constantBufferChangesEveryPrim__ содержит параметры для каждого объекта.  Это объект для матрицы мирового преобразования, а также свойства материалов, например цвет и степень зеркального отражения для расчета освещения.
* Контекст Direct3D настраивается на использование входного вершинного макета для данных объекта сетки, передаваемого на этап сборщика входных данных [конвейера отрисовки](#rendering-pipeline)
* Контекст Direct3D настраивается на использование [буфера индексов](#index-buffer) на этап сборщика входных данных. Предоставляется информация о примитивах: тип, порядок данных.
* Вызывается метод рисования для рисования индексированного, не являющегося экземпляром примитива. Метод __GameObject::Render__ обновляет [буфер констант](#constant-buffer-or-shader-constant-buffer), добавляя данные, характерные для конкретного примитива. В результате происходит вызов метода __DrawIndexed__ в контексте для рисования геометрии каждого примитива. В частности, этот вызов метода рисования помещает команды и данные в очередь графического процессора (GPU) с параметрами, заданными данными буфера констант. При каждом вызове метода рисования выполняется [вершинный шейдер](#vertex-shaders-and-pixel-shaders)— по одному разу для каждой вершины, а затем [построитель текстуры](#vertex-shaders-and-pixel-shaders)— по одному разу для каждого пикселя каждого треугольника в примитиве. Текстуры являются частью состояния, которое построитель текстуры использует для отрисовки.

Почему буферов констант несколько?
    * Игра использует несколько буферов констант, но обновлять эти буферы ей нужно только по одному разу для каждого примитива. Как уже говорилось, буферы констант можно рассматривать как входные данные для шейдеров, которые выполняются для каждого примитива. Некоторые данные статичны (__m\_constantBufferNeverChanges__), некоторые неизменны в рамках кадра (__m\_constantBufferChangesEveryFrame)__, например положение камеры, а некоторые специфичны для примитива, например его цвет и текстуры (__m\_constantBufferChangesEveryPrim__).
    * [Отрисовщик](#renderer) игры разделяет эти входные данные по разным буферам констант, чтобы оптимизировать пропускную способность памяти, используемую ЦП и GPU. Этот подход также помогает свести к минимуму количество данных, которые приходится отслеживать GPU. У GPU большая очередь команд, и каждый раз, когда игра вызывает метод __Draw__, эта команда добавляется в очередь вместе со связанными с ней данными. Когда игра обновляет буфер констант примитива и выдает следующую команду __Draw__, графический драйвер добавляет эту команду и связанные с ней данные в очередь. Если игра рисует 100примитивов, в очереди может накопиться 100копий данных буфера констант. Чтобы свести к минимуму количество данных, которое игра отправляет GPU, игра использует отдельный буфер констант примитива, который содержит только обновления для каждого примитива.

#### <a name="gameobjectrender-method"></a>Метод GameObject::Render

```cpp
void GameObject::Render(
    _In_ ID3D11DeviceContext *context,
    _In_ ID3D11Buffer *primitiveConstantBuffer
    )
{
    if (!m\_active || (m\_mesh == nullptr) || (m_normalMaterial == nullptr))
    {
        return;
    }

    ConstantBufferChangesEveryPrim constantBuffer;

    // Put the model matrix info into a constant buffer, in world matrix.
    XMStoreFloat4x4(
        &constantBuffer.worldMatrix,
        XMMatrixTranspose(ModelMatrix())
        );

    // Check to see which material to use on the object.
    // If a collision (a hit) is detected, GameObject::Render checks the current context, which 
    // indicates whether the target has been hit by an ammo sphere. If the target has been hit, 
    // this method applies a hit material, which reverses the colors of the rings of the target to 
    // indicate a successful hit to the player. Otherwise, it applies the default material 
    // with the same method. In both cases, it sets the material by calling Material::RenderSetup, 
    // which sets the appropriate constants into the constant buffer. Then, it calls 
    // ID3D11DeviceContext::PSSetShaderResources to set the corresponding texture resource for the 
    // pixel shader, and ID3D11DeviceContext::VSSetShader and ID3D11DeviceContext::PSSetShader 
    // to set the vertex shader and pixel shader objects themselves, respectively.

    if (m_hit && m_hitMaterial != nullptr)
    {
        m_hitMaterial->RenderSetup(context, &constantBuffer);
    }
    else
    {
        m_normalMaterial->RenderSetup(context, &constantBuffer);
    }

    // Update the primitive constant buffer with the object model's info.
    context->UpdateSubresource(primitiveConstantBuffer, 0, nullptr, &constantBuffer, 0, 0);

    // Render the mesh.
    // See MeshObject::Render method below.
    m_mesh->Render(context);
}

#### MeshObject::Render method

void MeshObject::Render(\_In\_ ID3D11DeviceContext *context)
{
    // PNTVertex is a struct. stride provides us the size required for all the mesh data
    // struct PNTVertex
    //{
    //  DirectX::XMFLOAT3 position;
    //  DirectX::XMFLOAT3 normal;
    //  DirectX::XMFLOAT2 textureCoordinate;
    //};
    uint32 stride = sizeof(PNTVertex);
    uint32 offset = 0;

    // Similar to the main render loop.
    // Input-layout objects describe how vertex buffer data is streamed into the IA pipeline stage.
    context->IASetVertexBuffers(0, 1, m_vertexBuffer.GetAddressOf(), &stride, &offset);

    // IASetIndexBuffer binds an index buffer to the input-assembler stage.
    // For more info, go to: https://msdn.microsoft.com/library/windows/desktop/ff476453.aspx
    context->IASetIndexBuffer(m_indexBuffer.Get(), DXGI_FORMAT_R16_UINT, 0);

    // Binds information about the primitive type, and data order that describes input data for the input assembler stage.
    // For more info, go to: https://msdn.microsoft.com/library/windows/desktop/ff476455.aspx
    context->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);

    // Draw indexed, non-instanced primitives. A draw API submits work to the rendering pipeline.
    // For more info, go to: https://msdn.microsoft.com/library/windows/desktop/ff476409.aspx
    context->DrawIndexed(m_indexCount, 0, 0);
}
```

### <a name="present"></a>Представление

Мы вызываем метод __DX::DeviceResources::Present__, чтобы взять содержимое, которое поместили в буферы, и отобразить его.

Коллекция буферов, которая используется для отображения кадров пользователю, называется "цепочка буферов". Каждый раз, когда приложение представляет новый кадр для отображение, первый буфер в цепочке буферов занимает место отображаемого буфера. Этот процесс называется заменой или переключением. Подробнее: [Цепочки буферов](../graphics-concepts/swap-chains.md).

* Метод __Present__ интерфейса __IDXGISwapChain1__ дает [DXGI](#dxgi) указание блокироваться до вертикальной синхронизации (VSync), т.е. приложение "засыпает" до следующей вертикальной синхронизации. Это гарантирует, что вы не будете тратить циклы на отрисовку кадров, которые никогда не будут отображены на экране.
* Метод __DiscardView__ интерфейса __ID3D11DeviceContext3__ удаляет содержимое [целевого объекта отрисовки](#render-target). Эта операция допустима только в случае, когда существующее содержимое будет полностью перезаписано. При использовании грязных прямоугольников или прямоугольников для прокрутки вызов этого метода следует удалить.
* Используя этот же метод __DiscardView__, мы удаляем содержимое [трафарета глубины](#depth-stencil).
* Метод __HandleDeviceLost__ используется для обработки сценария, когда [устройство](#device) удаляется. Если устройство было удалено путем отключения или обновления драйвера, необходимо заново создать все ресурсы устройства. Подробнее: [Обработка сценариев удаления устройства в Direct3D11](handling-device-lost-scenarios.md).

> [!Tip]
> Чтобы смена кадров была плавной, необходимо, чтобы работу по отрисовке кадра можно было выполнить в промежуток времени между вертикальными синхронизациями.

```cpp
// Present the contents of the swap chain to the screen.
void DX::DeviceResources::Present()
{
    // The first argument instructs DXGI to block until VSync, putting the application
    // to sleep until the next VSync. This ensures we don't waste any cycles rendering
    // frames that will never be displayed to the screen.
    HRESULT hr = m_swapChain->Present(1, 0);

    // Discard the contents of the render target.
    // This is a valid operation only when the existing contents will be entirely
    // overwritten. If dirty or scroll rects are used, this call should be removed.
    m_d3dContext->DiscardView(m_d3dRenderTargetView.Get());

    // Discard the contents of the depth-stencil.
    m_d3dContext->DiscardView(m_d3dDepthStencilView.Get());

    // If the device was removed either by a disconnection or a driver upgrade, we 
    // must recreate all device resources.
    // For more info about how to handle a device lost scenario, go to:
    // https://docs.microsoft.com/windows/uwp/gaming/handling-device-lost-scenarios
    if (hr == DXGI_ERROR_DEVICE_REMOVED || hr == DXGI_ERROR_DEVICE_RESET)
    {
        HandleDeviceLost();
    }
    else
    {
        DX::ThrowIfFailed(hr);
    }
}
```

## <a name="next-steps"></a>Следующие шаги

В этой статье была рассмотрена отрисовка графики на дисплее, с кратким описанием некоторых терминов, используемых применительно к отрисовке. Чтобы узнать больше об отрисовке, а также научиться подготавливать необходимые данные перед отрисовкой, прочитайте статью [Платформа отрисовки II: отрисовка игры](tutorial-game-rendering.md).

## <a name="terms-and-concepts"></a>Термины и понятия

### <a name="simple-game-scene"></a>Простая игровая сцена

Простая игровая сцена состоит из нескольких объектов с несколькими источниками света.

Форма объекта определяется набором координат X, Y, Z в пространстве. Фактическое местоположение отрисовки в игровом мире можно определить, применив к позиционным координатам X, Y, Z матрицу преобразования. Также у объекта может быть набор координат текстуры — U и V, который определяет, как материал применяется к объекту. Это определяет свойства поверхности объекта и дает возможность видеть, какая у объекта поверхность— шершавая, как у теннисного мяча, или гладкая и блестящая, как у шара для боулинга.

Информация о сцене и объектах используется платформой отрисовки для воссоздания сцены кадр за кадром, чтобы она "ожила" на вашем мониторе.

### <a name="rendering-pipeline"></a>Конвейер отрисовки

Конвейер отрисовки — это процесс, посредством которого информация о трехмерной сцене преобразовывается в изображение, отображаемое на экране. В Direct3D 11 этот конвейер является программируемым. Вы можете адаптировать его этапы под свои потребности в отрисовке. Этапы, на которых используются общих ядра шейдеров, можно программировать с помощью языка программирования HLSL. Также этот процесс называется конвейером отрисовки графики или просто конвейером.

Для создания конвейера, вы должны быть знакомы о следующими концепциями:
* [HLSL](#HLSL). Для игр UWP на базе DirectX рекомендуется HLSL Shader Model 5.1 и более поздних версий.
* [Шейдеры](#Shaders)
* [Вершинные шейдеры и построители текстур](#vertext-shaders-pixel-shaders)
* [Шейдерные этапы](#shader-stages)
* [Различные форматы файлов шейдеров](#various-shader-file-formats)

Подробнее: [Общие сведения о конвейере отрисовки Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/dn643746.aspx) и [Графический конвейер](https://msdn.microsoft.com/library/windows/desktop/ff476882.aspx).

#### <a name="hlsl"></a>HLSL

HLSL— это высокоуровневый язык шейдеров (High Level Shading Language) для DirectX. Используя HLSL, можно создавать C-подобные программируемые шейдеры для конвейера Direct3D. Подробнее: [HLSL](https://msdn.microsoft.com/library/windows/desktop/bb509561.aspx).

#### <a name="shaders"></a>Шейдеры

Шейдеры можно рассматривать как набор инструкций, которые определяют, как при отрисовке выглядит поверхность объекта. Шейдеры, запрограммированные с использованием HLSL, называются шейдерами HLSL. Файлы исходного кода шейдеров [HLSL])(#hlsl) имеют расширение .hlsl. Эти шейдеры можно компилировать во время разработки или во время выполнения, а также устанавливать во время выполнения на соответствующий этап конвейера; расширение файла скомпилированного объекта шейдера— .cso.

Шейдеры Direct3D 9 могут разрабатываться с использованием Shader Model 1, Shader Model 2 и Shader Model 3; шейдеры Direct3D 10 могут разрабатываться только с использованием Shader Model 4. Шейдеры Direct3D 11 могут разрабатываться с использованием Shader Model 5. Шейдеры Direct3D 11.3 и Direct3D 12 могут разрабатываться с использованием Shader Model 5.1; для Direct3D 12 также может использоваться Shader Model 6.

#### <a name="vertex-shaders-and-pixel-shaders"></a>Вершинные шейдеры и построители текстур

Данные поступают в графический конвейер в виде потока примитивов и обрабатываются различными шейдерами, например вершинными шейдерами и построителями текстур. 

Шейдеры вершин обрабатывают вершины, обычно с выполнением таких операций, как преобразования, скиннинг и освещение.  Построители текстур (пиксельные шейдеры) позволяют применять сложные приемы затенения, такие как попиксельное освещение и постобработка. Они объединяют константные переменные, данные текстур, интерполированные повершинные значения и другие данные для получения отдельных пикселей. 

#### <a name="shader-stages"></a>Шейдерные этапы

Последовательность упомянутых выше шейдеров, определенная для обработки потока примитивов, называется шейдерными этапами конвейера отрисовки. Фактические этапы зависят от версии Direct3D, но обычно это этапы обработки вершин, геометрии и пикселей. Также существуют другие этапы— например, шейдеры поверхностей и шейдеры доменов для тесселяции, а также шейдер вычислений. Все эти этапы полностью программируются с использованием [HLSL])(#hlsl). Подробнее: [Графический конвейер](https://msdn.microsoft.com/library/windows/desktop/ff476882.aspx).

#### <a name="various-shader-file-formats"></a>Различные форматы файлов шейдеров

Расширения файлов с кодом шейдеров:
    * Файл с расширением .hlsl содержит исходный код на языке [HLSL])(#hlsl).
    * Файл с расширением .cso содержит скомпилированный объект шейдера.
    * Файл с расширением .h— это файл заголовка, но в контексте кода шейдера этот файл заголовка определяет массив байтов, содержащий данные шейдера.
    * Файл с расширением .hlsli содержит формат буферов констант. В игре-примере это файл __Shaders__>__ConstantBuffers.hlsli__.

> [!Note]
> Шейдер внедряется либо путем загрузки CSO-файла во время выполнения, либо путем добавления H-файла в исполняемый код. Использовать оба эти метода для одного и того же шейдера, однако, нельзя.

### <a name="deeper-understanding-of-directx"></a>Подробнее о DirectX

Direct3D 11— это набор API, которые помогают создавать графику для приложений с интенсивным использованием графики, например игр, где необходимо задействовать хороший графический адаптер для обработки интенсивных вычислений. В этом разделе кратко рассматриваются понятия, связанные с программированием графики для Direct3D 11: ресурс, подресурс, устройство и контекст устройства.

#### <a name="resource"></a>Ресурс

Если вы только начинаете знакомство с программированием графики, вы можете представлять себе ресурсы (также их называются ресурсами устройств) как информацию о том, как отрисовывать объект— текстуру, положение, цвет. Ресурсы предоставляют данные конвейеру и определяют, что отрисовывается во время сцены. Ресурсы могут загружаться из мультимедиа вашей игры или создаваться динамически во время выполнения.

Ресурс — это, по сути, область памяти, к которой [конвейер](#rendering-pipeline) Direct3D может осуществлять доступ. Чтобы конвейер получал доступ к памяти эффективным образом, данные, предоставляемые конвейеру (например, входные геометрические данные, ресурсы шейдеров и текстуры), должны храниться в ресурсах. Существует два типа ресурсов, из которых образуются все ресурсы Direct3D: буфер и текстура. На каждом этапе конвейера может быть активно до 128 ресурсов. Подробнее: [Ресурсы](../graphics-concepts/resources.md).

#### <a name="subresource"></a>Подресурс

Под ресурсом понимается подмножество ресурса. Direct3D может ссылаться на весь ресурс или же на подмножества ресурса. Подробнее: [Подресурс](../graphics-concepts/resource-types.md#subresources).

#### <a name="depth-stencil"></a>Трафарет глубины

Ресурс трафарета глубины содержит формат и буфер для хранения сведений о глубине и трафарете. Он создается с использованием ресурса текстуры. Подробнее о том, как создать ресурс трафарета глубины, см. в статье [Настройка функции трафарета глубины](https://msdn.microsoft.com/library/windows/desktop/bb205074.aspx). Доступ к ресурсу трафарета глубины осуществляется через представление трафарета глубины, реализуемое с помощью интерфейса [ID3D11DepthStencilView](https://msdn.microsoft.com/library/windows/desktop/ff476377.aspx).

Информация о глубине позволяет понять, какие области многоугольников отрисовываются, а какие скрываются. Информация о трафарете позволяет понять, какие пиксели маскируются. Ее можно использовать для создания специальных эффектов, поскольку она определяет, рисуется пиксель или нет (устанавливает бит равным 1 или 0). 

Подробнее: [Представление трафарета глубины ](../graphics-concepts/depth-stencil-view--dsv-.md), [буфер глубины](../graphics-concepts/depth-buffers.md) и [буфер трафарета](../graphics-concepts/stencil-buffers.md).

#### <a name="render-target"></a>Целевой объект отрисовки

Целевой объект отрисовки— это ресурс, в который производится запись данных по окончании этапа отрисовки. Обычно он создается с помощью метода [ID3D11Device::CreateRenderTargetView](https://msdn.microsoft.com/library/windows/desktop/ff476517.aspx) с использованием заднего буфера цепочки буферов (который также является ресурсом) в качестве входного параметра. 

Каждый целевой объект отрисовки также должен иметь соответствующее представление трафарета глубины, потому что мы используем метод [OMSetRenderTargets](https://msdn.microsoft.com/library/windows/desktop/ff476464.aspx) для задания целевого объекта отрисовки, прежде чем использовать его, и этому методу требуется в том числе представление трафарета глубины. Доступ к ресурсу целевого объекта отрисовки осуществляется через представление целевого объекта отрисовки, реализуемого с помощью интерфейса [ID3D11RenderTargetView](https://msdn.microsoft.com/library/windows/desktop/ff476582.aspx). 

#### <a name="device"></a>Устройство

Если вы только начинаете знакомство с Direct3D 11, вы можете представлять себе устройство как механизм для выделения памяти под объекты и их уничтожения, отрисовки примитивов, а также обмена данными с графической картой через графический драйвер. 

Если говорить точнее, устройство Direct3D— это компонент Direct3D, выполняющий отрисовку графики. Устройство инкапсулирует и сохраняет состояние отрисовки, выполняет операции преобразования и освещения и растеризует изображение на поверхность. Подробнее: [Устройства](../graphics-concepts/devices.md)

Устройство представляется интерфейсом [ID3D11Device](https://msdn.microsoft.com/library/windows/desktop/ff476379.aspx). Иными словами, интерфейс ID3D11Device представляет виртуальный видеоадаптер и используется для создания ресурсов, которые принадлежат устройству. 

Обратите внимание, что существуют различные версии ID3D11Device. [ID3D11Device5](https://msdn.microsoft.com/library/windows/desktop/mt492478.aspx)— это последняя версия, в которой добавлены новые методы относительно ID3D11Device4. Подробнее о том, как Direct3D взаимодействует с оборудованием, см. в статье [Архитектура модели драйверов устройств с Windows (WDDM)](https://docs.microsoft.com/windows-hardware/drivers/display/windows-vista-and-later-display-driver-model-architecture).

К каждого приложения должно быть хотя бы одно устройство; большинство приложений создают только одно устройство. Создать устройство для одного из драйверов оборудования, установленных на вашем компьютере, можно путем вызова метод __D3D11CreateDevice__ или __D3D11CreateDeviceAndSwapChain__ и указания типа драйвера с помощью флага D3D\_DRIVER\_TYPE. Каждое устройство можно использовать один или несколько контекстов устройств, в зависимости от требуемой функциональности. Подробнее: [Функция D3D11CreateDevice](https://msdn.microsoft.com/library/windows/desktop/ff476082.aspx).

#### <a name="device-context"></a>Контекст устройства

Контекст устройства используется для установки состояния [конвейера](#rendering-pipeline) и генерации команд отрисовки с использованием [ресурсов](#resource), которые принадлежат [устройству](#device). 

Direct3D 11 реализует два типа контекста устройств: один для немедленной отрисовки и другой для отложенной отрисовки; оба контекста представляются с помощью интерфейса [ID3D11DeviceContext](https://msdn.microsoft.com/library/windows/desktop/ff476385.aspx).  

Интерфейсы __ID3D11DeviceContext__ бывают разных версий; в __ID3D11DeviceContext4__ добавлены новые методы относительно __ID3D11DeviceContext3__.

Примечание: __ID3D11DeviceContext4__ впервые появился в Windows 10 Creators Update и является последней версией интерфейса __ID3D11DeviceContext__. В приложениях, предназначенных для Windows 10 Creators Update, следует использовать этот интерфейс, а не более ранние версии. Подробнее: [ID3D11DeviceContext4](https://msdn.microsoft.com/library/windows/desktop/mt492481.aspx).

#### <a name="dxdeviceresources"></a>DX::DeviceResources

Класс __DX::DeviceResources__ находится в файлах __DeviceResources.cpp__/__.h__ и управляет всеми ресурсами устройства DirectX. В проекте с примером игры и проекте с шаблоном приложения DirectX 11 эти файлы находятся в папке __Commons__. Чтобы взять последнюю версию этих файлов, создайте новый проект шаблона приложения DirectX 11 в Visual Studio 2015 или позднее.

### <a name="buffer"></a>Буфер

Буферный ресурс — это коллекция полностью типизированных данных, сгруппированных в элементы. Буферы можно использовать для хранения самых разных данных, в том числе векторов положений, нормальных векторов, координат текстур (в буфере вершин), индексов (в буфере индексов) или состояния устройства. Элементы буфера могут включать в себя упакованные значения данных (например, значения поверхности R8G8B8A8), единичные 8-разрядные целые числа или четыре 32-разрядных значения с плавающей запятой.

Существует три типа буферов: буфер вершин, буфер индексов и буфер констант.

#### <a name="vertex-buffer"></a>Буфер вершин

Содержит данные вершин, используемые для определения геометрии. К данным вершин относятся координаты положения, данные цвета, данные координат текстуры, данные нормали и т. д. 

#### <a name="index-buffer"></a>Буфер индексов

Содержит целочисленные указатели для буферов вершин и используется для более эффективной отрисовки примитивов. Буфер индексов содержит последовательный набор 16-разрядных или 32-разрядных индексов; каждый индекс используется для идентификации вершины в буфере вершин.

#### <a name="constant-buffer-or-shader-constant-buffer"></a>Буфер констант (или буфер констант шейдера)

Позволяет эффективно поставлять данные шейдера в конвейер. Буферы констант можно использовать в качестве входных данных для шейдеров, которые выполняются для каждого примитива и сохраняют результаты этапа потокового вывода конвейера отрисовки. На концептуальном уровне буфер констант аналогичен одноэлементному буферу вершин.

#### <a name="design-and-implementation-of-buffers"></a>Проектирование и реализация буферов

Буферы можно проектировать на основании типа данных. Например, в нашем примере игры создается один буфер для статичных данных, другой для данных, которые являются постоянными на протяжении кадра, и третий для данных, характерных для конкретного примитива.

Все типы буферов инкапсулируются интерфейсом __ID3D11Buffer__; создать буферный ресурс можно путем вызова метода __ID3D11Device::CreateBuffer__. Однако прежде чем к буферу можно будет осуществить доступ, его необходимо привязать к конвейеру. Для чтения буферы могут быть привязаны одновременно к нескольким этапам конвейера. Буфер также может быть привязан к одному этапу конвейера для записи, однако один и тот же буфер не может быть привязан для чтения и записи одновременно.

Привязка буферов производится следующим образом:
    * К этапу сборщика входных данных— путем вызова методов __ID3D11DeviceContext__, таких как __ID3D11DeviceContext::IASetVertexBuffers__ и __ID3D11DeviceContext::IASetIndexBuffer__.
    * К этапу потокового вывода— путем вызова метода __ID3D11DeviceContext::SOSetTargets__.
    * К шейдерному этапу— путем вызова методов шейдеров, например __ID3D11DeviceContext::VSSetConstantBuffers__.

Подробнее: [Вводные сведения о буферах в Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/ff476898.aspx).

### <a name="dxgi"></a>DXGI

Microsoft DirectX Graphics Infrastructure (DXGI) — это новая подсистема, впервые появившаяся в WindowsVista, которая инкапсулирует некоторые задачи низкого уровня, необходимые Direct3D 10 10.1, 11 и 11.1. В многопоточных приложениях использовать DXGI необходимо с особой осторожностью во избежание возникновения взаимоблокировок. Подробнее: [DirectX Graphics Infrastructure (DXGI): рекомендации, связанные с многопоточностью](https://msdn.microsoft.com/library/windows/desktop/ee417025.aspx#multithreading_and_dxgi)

### <a name="feature-level"></a>Уровень компонентов

Уровень компонентов— это концепция, появившаяся в Direct3D 11 для обработки всего разнообразия видеоадаптеров в новых и существующих компьютерах. Уровень компонентов — это четко определенный набор функций графического процессора (GPU). 

Каждый видеоадаптер реализует определенный уровень функциональности DirectX в зависимости от установленных графических процессоров. В предыдущих версиях Microsoft Direct3D можно было узнать версию Direct3D, реализуемую видеоадаптером, а затем программировать приложение соответствующим образом. 

Используя уровень компонентов, при создании устройства вы можете попытаться создать устройство для уровня компонентов, который вы хотите запросить. Если создать устройство удалось, этот уровень компонентов существует; если нет, оборудование не поддерживает этот уровень компонентов. Можно либо попробовать заново создать устройство на более низком уровне компонентов, либо закрыть приложение. Например, уровень компонентов 12\_0 требует Direct3D 11.3 или Direct3D 12 и Shader Model 5.1. Подробнее: [Уровни компонентов Direct3D: обзор каждого уровня компонентов](https://msdn.microsoft.com/library/windows/desktop/ff476876.aspx#Overview).

Используя уровни компонентов, можно разработать приложение для Direct3D9, Direct3D10 Майкрософт или Direct3D11 и затем запустить его на оборудовании 9, 10 или 11 (за некоторыми исключениями). Подробнее: [Уровни компонентов Direct3D](https://msdn.microsoft.com/library/windows/desktop/ff476876.aspx).

### <a name="stereo-rendering"></a>Стереоскопическая отрисовка

Стереоскопическая отрисовка используется для усиления иллюзии глубины. Она предполагает использование двух изображений— одного для левого глаза, другого для правого— для отображения сцены на экране дисплея. 

С математической точки зрения для достижения этого эффекта применяется матрица стереопроекции, которая представляет собой небольшое горизонтальное смещение обычной матрицы монопроекции вправо и влево.

В этом примере игры мы сделали два прохода отрисовки для достижения стереоскопической отрисовки:
* Привязались к правому целевому объекту отрисовки, применили правую проекцию и нарисовали объект примитива.
* Привязались к левому целевому объекту отрисовки, применили левую проекцию и нарисовали объект примитива.

### <a name="camera-and-coordinate-space"></a>Камера и пространство координат

Игра снабжена кодом для обновления мира в его собственной системе координат (которая иногда называется "пространство мира" или "пространство сцены"). Все объекты, включая камеру, расположены и ориентированы в этом пространстве. Подробнее: [Системы координат](../graphics-concepts/coordinate-systems.md).

Вершинный шейдер выполняет преобразование координат модели в координаты устройства с помощью следующего алгоритма (где V— вектор, а M— матрица):

V(устройство) = V(модель) x M(модель-мир) x M(мир-представление) x M(представление-устройство),

где: 
* M(модель-мир)— матрица преобразования координат модели в мировые координаты, также называемая [матрицей мирового преобразования](#world-transform-matrix). Предоставляется примитивом.
* M(мир-представление)— матрица преобразования мировых координат в координаты представления, также называемая [матрицей видового преобразования](#view-transform-matrix).
    * Получить ее можно из матрицы представления камеры. Определяется положением камеры и векторами взгляда (вектор "взгляд на", который направлен из камеры прямо на сцену, и вектор "взгляд вверх", который направлен вверх, перпендикулярно ему).
    * В примере игры __m\_viewMatrix__— это матрица преобразования представления, которая рассчитывается с использованием __Camera::SetViewParams__ 
* M(представление-устройство)— матрица преобразования координат представления в координаты устройства, также называемая [матрицей проекционного преобразования](#projection-transform-matrix).
    * Получить ее можно из проекции камеры. Содержит информацию о том, какая часть этот пространства фактически видна в окончательной сцене. Матрица проекционного преобразования определяется полем зрения, пропорциями и плоскостями обрезки.
    * В примере игры __m\_projectionMatrix__ определяет преобразование координат проекции, рассчитываемое с использованием __Camera::SetProjParams__ (для стереопроекции необходимо использовать две матрицы проекции, по одной для представления каждого глаза). 

Код шейдера в VertexShader.hlsl загружает эти векторы и матрицы из буферов констант и выполняет это преобразование для каждой вершины.

### <a name="coordinate-transformation"></a>Преобразование координат

В Direct3D используется три преобразования для перевода координат трехмерной модели в координаты пикселей (пространство экрана). Эти преобразования— мировое преобразование, видовое преобразование и проекционное преобразование. Подробнее: [Обзор преобразований](../graphics-concepts/transform-overview.md).

#### <a name="world-transform-matrix"></a>Матрица мирового преобразования

Мировое преобразование обеспечивает перевод координат из пространства модели, где вершины определяются относительно локального начала координат модели, в мировое пространство, где вершины определяются относительно начала координат, общего для всех объектов в сцене. По сути, мировое преобразование помещает модель в мир,— отсюда и название. Подробнее: [Мировое преобразование](../graphics-concepts/world-transform.md).

####  <a name="view-transform-matrix"></a>Матрица видового преобразования

Видовое преобразование определяет положение наблюдателя в мировом пространстве и обеспечивает преобразование вершин в пространство камеры. В пространстве камеры камера, или наблюдатель, находится в начале координат и смотрит в направлении положительной полуоси Z. Подробнее: [Видовое преобразование](../graphics-concepts/view-transform.md).

####  <a name="projection-transform-matrix"></a>Матрица проекционного преобразования

Проекционное преобразование превращает видимое пространство (усеченную пирамиду) в кубовидную фигуру. Усеченная пирамида видимости— это трехмерный объем в сцене, позиционируемый относительно камеры окна просмотра. Окно просмотра— это двумерный прямоугольник, на который проецируется трехмерная сцена. Подробнее: [Окна просмотра и обрезка](../graphics-concepts/viewports-and-clipping.md)

Так как ближний конец видимого пространства меньше дальнего, это приводит к развертыванию объектов, которые находятся ближе к камере — так перспектива применяется к сцене. Благодаря этому объекты, которые находятся ближе к игроку, кажутся больше; объекты, которые находятся от него дальше, кажутся меньше.

С математической точки зрения проекционное преобразование — это матрица, которая обычно представляет собой проекцию масштаба и перспективы. Она функционирует подробно объективу камеры. Подробнее: [Проекционное преобразование](../graphics-concepts/projection-transform.md).

### <a name="sampler-state"></a>Состояние дискретизатора

Состояние дискретизатора определяет, как дискретизируются данные текстур, с использованием режимов адресации текстур, фильтрации и уровня детализации. Дискретизация производится всякий раз, когда пиксель текстуры (тексель) считывается из текстуры.

Текстура содержит массив текселей (пикселей текстуры). Положение каждого текселя обозначается координатами (u, v), где u— ширина, а v— высота, и откладывается между 0 и 1, исходя из ширины и высоты текстуры. Полученные координаты текстуры используются для адресации текселя при дискретизации текстуры.

Когда координаты текстуры меньше 0 или больше 0, режим адресации текстуры определяет, как координата текстуры адресует положение пикселя. Например, при использовании __TextureAddressMode.Clamp__любая координата за пределами диапазона 0–1 приводится к максимальному значению (1) и минимальному значению (0) перед дискретизацией.

Если текстура слишком велика или слишком мала для многоугольника, текстура фильтруется так, чтобы она соответствовала размеру пространства. Фильтр увеличения увеличивает текстуру, а фильтр уменьшения уменьшает ее так, чтобы она поместилась в меньшую область. При увеличении текстуры тексель повторяется для одного или нескольких адресов, что дает более размытое изображение. Уменьшение текстуры— более сложный процесс, поскольку он требует объединения нескольких тексельных значений в одно значение. Это может привести к искажениям контура (алиасингу), в зависимости от данных текстуры. Самый популярный подход к уменьшению— это использование MIP-карты. MIP-карта— это многоуровневая текстура. Размер каждого уровня меньше предыдущего на степень двух, вплоть до текстуры размером 1x1. При использовании уменьшения игра выбирает уровень MIP-карты, ближайший к необходимому ей размеру, во время отрисовки. 

### <a name="basicloader"></a>BasicLoader

__BasicLoader__— это простой класс-загрузчик, который обеспечивает функциональность для загрузки шейдеров, текстур и сеток из файлов на диске. Он предоставляет и синхронные, и асинхронные методы. В примере игры файлы BasicLoader.h/.cpp находятся в папке __Commons__.

Подробнее: [Basic Loader](complete-code-for-basicloader.md).