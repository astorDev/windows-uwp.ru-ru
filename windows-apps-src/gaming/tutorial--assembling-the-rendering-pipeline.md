---
title: Введение в отрисовку
description: Узнайте, как разрабатывать конвейер отрисовки для отображения графики. Введение в отрисовку.
ms.assetid: 1da3670b-2067-576f-da50-5eba2f88b3e6
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, отрисовка
ms.localizationpriority: medium
ms.openlocfilehash: d1abb324c5e9e16babbbf8d3650adc39cb995137
ms.sourcegitcommit: 20969781aca50738792631f4b68326f9171a3980
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409643"
---
# <a name="rendering-framework-i-intro-to-rendering"></a>Платформа отрисовки I: введение в отрисовку

> [!NOTE]
> Эта статья является частью серии руководств по [созданию простой универсальная платформа Windows (UWP) с помощью DirectX](tutorial--create-your-first-uwp-directx-game.md) . В разделе этой ссылки задается контекст для ряда.

До сих пор мы рассмотрели, как структурировать игру универсальная платформа Windows (UWP) и как определить конечный автомат для обработки потока игры. Теперь пора научиться разрабатывать платформу визуализации. Давайте посмотрим, как пример игры визуализирует игру с помощью Direct3D 11.

Direct3D 11 содержит набор интерфейсов API, обеспечивающих доступ к дополнительным функциям высокопроизводительного графического оборудования, которые можно использовать для создания трехмерной графики для ресурсоемких приложений, таких как игры.

Визуализация игровой графики на экране означает, по сути, визуализацию последовательности кадров на экране. В каждом кадре необходимо отрисовать объекты, которые видны в сцене, в зависимости от представления.

Для отрисовки кадра необходимо передать оборудованию требуемую информацию о сцене, чтобы ее можно было отобразить на экране. Если вы хотите, чтобы на экране что-нибудь отображалось, начинать отрисовку необходимо сразу же после запуска игры.

## <a name="objectives"></a>Задачи

Чтобы настроить базовую платформу отрисовки для отображения графических данных для игры DirectX UWP. Вы можете слабо приостановить выполнение этих трех шагов.

1. Установите соединение с графическим интерфейсом.
2. Создайте ресурсы, необходимые для рисования графики.
3. Отображение графики путем отрисовки кадра.

В этом разделе описывается визуализация графики, охватывающая шаги 1 и 3.

[Визуализация Framework II. Визуализация игр](tutorial-game-rendering.md) охватывает шаг 2. &mdash; Настройка инфраструктуры отрисовки и подготовка данных перед выполнением подготовки к просмотру.

## <a name="get-started"></a>Приступая к работе

Рекомендуется ознакомиться с базовыми концепциями графики и отрисовки. Если вы не знакомы с Direct3D и отрисовкой, ознакомьтесь с [терминами и концепциями](#terms-and-concepts) , чтобы получить краткое описание терминов графики и отрисовки, используемых в этом разделе.

Для этой игры класс **гамерендерер** представляет модуль подготовки отчетов для этого примера игры. Отрисовщик отвечает за создание и поддержание всех объектов Direct3D 11 и Direct2D , используемых для создания визуальных элементов игры. Он также поддерживает ссылку на объект **Simple3DGame** , используемый для получения списка объектов для подготовки к просмотру, а также состояние игры для отображения заголовков (HUD).

В этой части учебника мы подробно рассмотрим отрисовку трехмерных объектов в игре.

## <a name="establish-a-connection-to-the-graphics-interface"></a>Подключение к графическому интерфейсу

Сведения о доступе к оборудованию для подготовки к просмотру см. в разделе [Определение платформы приложений UWP в игре](tutorial--building-the-games-uwp-app-framework.md#the-appinitialize-method) .

### <a name="the-appinitialize-method"></a>Метод App:: Initialize

Функция **std:: make_shared** , как показано ниже, используется для создания **shared_ptr** в **DX::D евицересаурцес**, который также предоставляет доступ к устройству.

В Direct3D 11 [устройство](#device) используется для выделения памяти под объекты и их уничтожения, отрисовки примитивов, а также обмена данными с графической картой через графический драйвер.

```cppwinrt
void Initialize(CoreApplicationView const& applicationView)
{
    ...

    // At this point we have access to the device. 
    // We can create the device-dependent resources.
    m_deviceResources = std::make_shared<DX::DeviceResources>();
}
```

## <a name="display-the-graphics-by-rendering-the-frame"></a>Отображение графики путем отрисовки кадра

При запуске игры необходимо отрисовать игровую сцену. Инструкции по подготовке к просмотру начинаются в методе [**гамемаин:: Run**](#gamemainrun-method) , как показано ниже.

Вот простой поток.

1. **Update**
2. **Render**
3. **Присутствует**

### <a name="gamemainrun-method"></a>Метод GameMain::Run

```cppwinrt
void GameMain::Run()
{
    while (!m_windowClosed)
    {
        if (m_visible) // if the window is visible
        {
            switch (m_updateState)
            {
            ...
            default:
                CoreWindow::GetForCurrentThread().Dispatcher().ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);
                Update();
                m_renderer->Render();
                m_deviceResources->Present();
                m_renderNeeded = false;
            }
        }
        else
        {
            CoreWindow::GetForCurrentThread().Dispatcher().ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
    m_game->OnSuspending();  // Exiting due to window close, so save state.
}
```

### <a name="update"></a>Update

Дополнительные сведения об обновлении состояний игры в методе [**гамемаин:: Update**](tutorial-game-flow-management.md#the-gamemainupdate-method) см. в разделе [Управление потоком игр](tutorial-game-flow-management.md) .

### <a name="render"></a>Render

Отрисовка реализуется путем вызова метода [**гамерендерер:: Render**](#gamerendererrender-method) из **Гамемаин:: Run**.

Если включена [отрисовка стерео](#stereo-rendering) , то &mdash; для левого глаз и для правого глаза выполняется две отрисовки. На каждом проходе отрисовки мы привязываем целевой объект отрисовки и представление трафарета глубины к устройству. После этого мы также очищаем представление трафарета глубины.

> [!NOTE]
> Обеспечить стереоскопическую отрисовку можно и другими способами, например путем стереоскопической отрисовки в один проход с использованием создания экземпляров вершин или шейдеров геометрии. Метод, передающий два метода отрисовки, является медленным, но более удобным способом для получения стерео-отрисовки.

После запуска игры и загрузки ресурсов мы обновляем [матрицу проекции](#projection-transform-matrix)по мере выполнения каждого этапа подготовки к просмотру. Объекты из каждого представления слегка отличаются друг от друга. Далее мы настроили [конвейер отрисовки графики](#rendering-pipeline). 

> [!NOTE]
> Подробнее о том, как загружаются ресурсы, см. в статье [Создание и загрузка графических ресурсов DirectX](tutorial-game-rendering.md#create-and-load-directx-graphic-resources).

В этом примере игры модуль подготовки отчетов предназначен для использования стандартного макета вершин по всем объектам. Это упрощает разработку шейдера и позволяет легко вносить изменения между шейдерами, независимо от геометрии объектов.

#### <a name="gamerendererrender-method"></a>Метод GameRenderer::Render

Мы устанавливаем контекст Direct3D для использования макета входной вершины. Объекты входного макета описывают, как данные буфера вершин передаются в [конвейер отрисовки](#rendering-pipeline). 

Далее мы устанавливаем контекст Direct3D для использования буферов констант, определенных ранее, которые используются на этапе конвейера [вершинного шейдера](#vertex-shaders-and-pixel-shaders) и на этапе конвейера [пиксельного шейдера](#vertex-shaders-and-pixel-shaders) . 

> [!NOTE]
> Подробнее об определении буферов констант см. в статье [Платформа отрисовки II: отрисовка игры](tutorial-game-rendering.md).

Поскольку для всех шейдеров в конвейере используется один и тот же входной макет и набор буферов констант, они задаются по одному разу на кадр.

```cppwinrt
void GameRenderer::Render()
{
    bool stereoEnabled{ m_deviceResources->GetStereoState() };

    auto d3dContext{ m_deviceResources->GetD3DDeviceContext() };
    auto d2dContext{ m_deviceResources->GetD2DDeviceContext() };

    int renderingPasses = 1;
    if (stereoEnabled)
    {
        renderingPasses = 2;
    }

    for (int i = 0; i < renderingPasses; i++)
    {
        // Iterate through the number of rendering passes to be completed.
        // 2 rendering passes if stereo is enabled.
        if (i > 0)
        {
            // Doing the Right Eye View.
            ID3D11RenderTargetView* const targets[1] = { m_deviceResources->GetBackBufferRenderTargetViewRight() };

            // Resets render targets to the screen.
            // OMSetRenderTargets binds 2 things to the device.
            // 1. Binds one render target atomically to the device.
            // 2. Binds the depth-stencil view, as returned by the GetDepthStencilView method, to the device.
            // For more info, see
            // https://docs.microsoft.com/windows/win32/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets

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

            // Direct2D -- discussed later
            d2dContext->SetTarget(m_deviceResources->GetD2DTargetBitmapRight());
        }
        else
        {
            // Doing the Mono or Left Eye View.
            // As compared to the right eye:
            // m_deviceResources->GetBackBufferRenderTargetView instead of GetBackBufferRenderTargetViewRight
            ID3D11RenderTargetView* const targets[1] = { m_deviceResources->GetBackBufferRenderTargetView() };

            // Same as the Right Eye View.
            d3dContext->OMSetRenderTargets(1, targets, m_deviceResources->GetDepthStencilView());
            d3dContext->ClearDepthStencilView(m_deviceResources->GetDepthStencilView(), D3D11_CLEAR_DEPTH, 1.0f, 0);

            // d2d -- Discussed later under Adding UI
            d2dContext->SetTarget(m_deviceResources->GetD2DTargetBitmap());
        }

        const float clearColor[4] = { 0.5f, 0.5f, 0.8f, 1.0f };

        // Only need to clear the background when not rendering the full 3D scene since
        // the 3D world is a fully enclosed box and the dynamics prevents the camera from
        // moving outside this space.
        if (i > 0)
        {
            // Doing the Right Eye View.
            d3dContext->ClearRenderTargetView(m_deviceResources->GetBackBufferRenderTargetViewRight(), clearColor);
        }
        else
        {
            // Doing the Mono or Left Eye View.
            d3dContext->ClearRenderTargetView(m_deviceResources->GetBackBufferRenderTargetView(), clearColor);
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
                            m_game->GameCamera().LeftEyeProjection() :
                            m_game->GameCamera().RightEyeProjection()
                            ),
                        XMMatrixTranspose(XMLoadFloat4x4(&orientation))
                        )
                    );

                d3dContext->UpdateSubresource(
                    m_constantBufferChangeOnResize.get(),
                    0,
                    nullptr,
                    &changesOnResize,
                    0,
                    0
                    );
            }

            // Update variables that change once per frame.
            ConstantBufferChangesEveryFrame constantBufferChangesEveryFrameValue;
            XMStoreFloat4x4(
                &constantBufferChangesEveryFrameValue.view,
                XMMatrixTranspose(m_game->GameCamera().View())
                );
            d3dContext->UpdateSubresource(
                m_constantBufferChangesEveryFrame.get(),
                0,
                nullptr,
                &constantBufferChangesEveryFrameValue,
                0,
                0
                );

            // Set up the graphics pipeline. This sample uses the same InputLayout and set of
            // constant buffers for all shaders, so they only need to be set once per frame.
            // For more info about the graphics or rendering pipeline, see
            // https://docs.microsoft.com/windows/win32/direct3d11/overviews-direct3d-11-graphics-pipeline

            // IASetInputLayout binds an input-layout object to the input-assembler (IA) stage. 
            // Input-layout objects describe how vertex buffer data is streamed into the IA pipeline stage.
            // Set up the Direct3D context to use this vertex layout. For more info, see
            // https://docs.microsoft.com/windows/win32/api/d3d11/nf-d3d11-id3d11devicecontext-iasetinputlayout
            d3dContext->IASetInputLayout(m_vertexLayout.get());

            // VSSetConstantBuffers sets the constant buffers used by the vertex shader pipeline stage.
            // Set up the Direct3D context to use these constant buffers. For more info, see
            // https://docs.microsoft.com/windows/win32/api/d3d11/nf-d3d11-id3d11devicecontext-vssetconstantbuffers

            ID3D11Buffer* constantBufferNeverChanges{ m_constantBufferNeverChanges.get() };
            d3dContext->VSSetConstantBuffers(0, 1, &constantBufferNeverChanges);
            ID3D11Buffer* constantBufferChangeOnResize{ m_constantBufferChangeOnResize.get() };
            d3dContext->VSSetConstantBuffers(1, 1, &constantBufferChangeOnResize);
            ID3D11Buffer* constantBufferChangesEveryFrame{ m_constantBufferChangesEveryFrame.get() };
            d3dContext->VSSetConstantBuffers(2, 1, &constantBufferChangesEveryFrame);
            ID3D11Buffer* constantBufferChangesEveryPrim{ m_constantBufferChangesEveryPrim.get() };
            d3dContext->VSSetConstantBuffers(3, 1, &constantBufferChangesEveryPrim);

            // Sets the constant buffers used by the pixel shader pipeline stage. 
            // For more info, see
            // https://docs.microsoft.com/windows/win32/api/d3d11/nf-d3d11-id3d11devicecontext-pssetconstantbuffers

            d3dContext->PSSetConstantBuffers(2, 1, &constantBufferChangesEveryFrame);
            d3dContext->PSSetConstantBuffers(3, 1, &constantBufferChangesEveryPrim);
            ID3D11SamplerState* samplerLinear{ m_samplerLinear.get() };
            d3dContext->PSSetSamplers(0, 1, &samplerLinear);

            for (auto&& object : m_game->RenderObjects())
            {
                // The 3D object render method handles the rendering.
                // For more info, see Primitive rendering below.
                object->Render(d3dContext, m_constantBufferChangesEveryPrim.get());
            }
        }

        // Start of 2D rendering
        ...
    }
}
```

### <a name="primitive-rendering"></a>Отрисовка примитивов

При отрисовке сцены необходимо перебрать все объекты, которые должны быть отрисованы. Указанные ниже шаги повторяются для каждого объекта (примитива).

- Обновите буфер констант (**m_constantBufferChangesEveryPrim**), используя [матрицу универсального преобразования](#world-transform-matrix) модели и сведения о материалах.
- **M_constantBufferChangesEveryPrim** содержит параметры для каждого объекта. Он включает матрицу преобразования объекта в мир, а также свойства материала, такие как цвет и отражающий показатель для вычислений освещения.
- Установите контекст Direct3D, чтобы использовать входной макет вершины для потоковой передачи данных объекта сетки на этап [конвейера отрисовки](#rendering-pipeline)(IA-Assembler).
- Установите контекст Direct3D, чтобы использовать [буфер индексов](#index-buffer) на этапе IA. Предоставляется информация о примитивах: тип, порядок данных.
- Вызывается метод рисования для рисования индексированного, не являющегося экземпляром примитива. Метод **GameObject::Render** обновляет [буфер констант](#constant-buffer-or-shader-constant-buffer), добавляя данные, характерные для конкретного примитива. В результате происходит вызов метода **DrawIndexed** в контексте для рисования геометрии каждого примитива. В частности, этот вызов метода рисования помещает команды и данные в очередь графического процессора (GPU) с параметрами, заданными данными буфера констант. При каждом вызове метода рисования выполняется вершинный шейдер — по одному разу для каждой вершины, а затем [построитель текстуры](#vertex-shaders-and-pixel-shaders) — по одному разу для каждого пикселя каждого треугольника в примитиве. Текстуры являются частью состояния, которое построитель текстуры использует для отрисовки.

Ниже приведены причины использования нескольких буферов констант.

- В игре используется несколько буферов констант, но для каждого примитива необходимо только один раз обновить эти буферы. Как уже говорилось, буферы констант можно рассматривать как входные данные для шейдеров, которые выполняются для каждого примитива. Некоторые данные являются статическими (**m_constantBufferNeverChanges**); Некоторые данные являются постоянными по отношению к кадру (**m_constantBufferChangesEveryFrame**), например по положению камеры; Некоторые данные зависят от примитива, например его цвета и текстур (**m_constantBufferChangesEveryPrim**).
- Обработчик игры разделяет эти входные данные по разным буферам констант, чтобы оптимизировать пропускную способность памяти, используемую ЦП и GPU. Этот подход также помогает свести к минимальному объему данных, которые необходимо отследить GPU. У GPU большая очередь команд, и каждый раз, когда игра вызывает метод **Draw**, эта команда добавляется в очередь вместе со связанными с ней данными. Когда игра обновляет буфер констант примитива и выдает следующую команду **Draw**, графический драйвер добавляет эту команду и связанные с ней данные в очередь. Если игра рисует 100 примитивов, в очереди может накопиться 100 копий данных буфера констант. Чтобы свести к минимуму количество данных, которое игра отправляет GPU, игра использует отдельный буфер констант примитива, который содержит только обновления для каждого примитива.

#### <a name="gameobjectrender-method"></a>Метод GameObject::Render

```cppwinrt
void GameObject::Render(
    _In_ ID3D11DeviceContext* context,
    _In_ ID3D11Buffer* primitiveConstantBuffer
    )
{
    if (!m_active || (m_mesh == nullptr) || (m_normalMaterial == nullptr))
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
```

#### <a name="meshobjectrender-method"></a>Метод Мешобжект:: Render

```cppwinrt
void MeshObject::Render(_In_ ID3D11DeviceContext* context)
{
    // PNTVertex is a struct. stride provides us the size required for all the mesh data
    // struct PNTVertex
    //{
    //  DirectX::XMFLOAT3 position;
    //  DirectX::XMFLOAT3 normal;
    //  DirectX::XMFLOAT2 textureCoordinate;
    //};
    uint32_t stride{ sizeof(PNTVertex) };
    uint32_t offset{ 0 };

    // Similar to the main render loop.
    // Input-layout objects describe how vertex buffer data is streamed into the IA pipeline stage.
    ID3D11Buffer* vertexBuffer{ m_vertexBuffer.get() };
    context->IASetVertexBuffers(0, 1, &vertexBuffer, &stride, &offset);

    // IASetIndexBuffer binds an index buffer to the input-assembler stage.
    // For more info, see
    // https://docs.microsoft.com/windows/win32/api/d3d11/nf-d3d11-id3d11devicecontext-iasetindexbuffer.
    context->IASetIndexBuffer(m_indexBuffer.get(), DXGI_FORMAT_R16_UINT, 0);

    // Binds information about the primitive type, and data order that describes input data for the input assembler stage.
    // For more info, see
    // https://docs.microsoft.com/windows/win32/api/d3d11/nf-d3d11-id3d11devicecontext-iasetprimitivetopology.
    context->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);

    // Draw indexed, non-instanced primitives. A draw API submits work to the rendering pipeline.
    // For more info, see
    // https://docs.microsoft.com/windows/win32/api/d3d11/nf-d3d11-id3d11devicecontext-drawindexed.
    context->DrawIndexed(m_indexCount, 0, 0);
}
```

### <a name="deviceresourcespresent-method"></a>DeviceResources: метод повторной отправки:P

Мы вызываем метод **DeviceResources::P** повторной отправки, чтобы отобразить содержимое, помещаемое в буферы.

Коллекция буферов, которая используется для отображения кадров пользователю, называется "цепочка буферов". Каждый раз, когда приложение представляет новый кадр для отображение, первый буфер в цепочке буферов занимает место отображаемого буфера. Этот процесс называется заменой или переключением. Подробнее: [Цепочки буферов](../graphics-concepts/swap-chains.md).

- Метод **Present** интерфейса **IDXGISwapChain1** сообщает [DXGI](#dxgi) о блокировке, пока не будет выполнена вертикальная синхронизация (вертикальной синхронизации), перемещая приложение в спящий режим до следующего вертикальной синхронизации. Это гарантирует, что вы не тратите циклы визуализации, которые никогда не будут отображаться на экране.
- Метод **дискардвиев** интерфейса **ID3D11DeviceContext3** отбрасывает содержимое [целевого объекта прорисовки](#render-target). Эта операция допустима только в случае, когда существующее содержимое будет полностью перезаписано. Если используются «грязный» или «прокручиваемый» прямоугольник, этот вызов следует удалить.
* Используя этот же метод **DiscardView**, мы удаляем содержимое [трафарета глубины](#depth-stencil).
* Метод **хандледевицелост** используется для управления сценарием удаляемого [устройства](#device) . Если устройство было удалено либо при отключении, либо при обновлении драйвера, необходимо повторно создать все ресурсы устройства. Подробнее: [Обработка сценариев удаления устройства в Direct3D 11](handling-device-lost-scenarios.md).

> [!TIP]
> Для достижения плавной частоты кадров необходимо убедиться в том, что объем работы для отрисовки кадра должен помещаться в интервал между Всинкс.

```cppwinrt
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
    m_d3dContext->DiscardView(m_d3dRenderTargetView.get());

    // Discard the contents of the depth stencil.
    m_d3dContext->DiscardView(m_d3dDepthStencilView.get());

    // If the device was removed either by a disconnection or a driver upgrade, we 
    // must recreate all device resources.
    if (hr == DXGI_ERROR_DEVICE_REMOVED || hr == DXGI_ERROR_DEVICE_RESET)
    {
        HandleDeviceLost();
    }
    else
    {
        winrt::check_hresult(hr);
    }
}
```

## <a name="next-steps"></a>Следующие шаги

В этом разделе описывается визуализация графики на экране и приводится краткое описание некоторых используемых терминов отрисовки (см. ниже). Дополнительные сведения о подготовке к просмотру см. в статье Подготовка к просмотру с помощью [платформы](tutorial-game-rendering.md) , а также сведения о подготовке данных, необходимых для подготовки к просмотру.

## <a name="terms-and-concepts"></a>Термины и основные понятия

### <a name="simple-game-scene"></a>Простая игровая сцена

Простая игровая сцена состоит из нескольких объектов с несколькими источниками света.

Форма объекта определяется набором координат X, Y, Z в пространстве. Фактическое местоположение отрисовки в игровом мире можно определить, применив к позиционным координатам X, Y, Z матрицу преобразования. Он также может иметь набор координат текстуры, &mdash; которые вы и V &mdash; задают, как материал применяется к объекту. Это определяет свойства поверхности объекта, а также позволяет определить, имеет ли объект определенную поверхность (например, шар) или гладкую информационную поверхность (например, боулинг шар).

Сведения об сцене и объекте используются платформой визуализации для повторного создания кадра сцены по кадрам, что делает его активным на мониторе отображения.

### <a name="rendering-pipeline"></a>Конвейер отрисовки

Конвейер отрисовки — это процесс, с помощью которого данные трехмерной сцены преобразуются в изображение, отображаемое на экране. В Direct3D 11 этот конвейер является программируемым. Вы можете адаптировать его этапы под свои потребности в отрисовке. Этапы, на которых используются общих ядра шейдеров, можно программировать с помощью языка программирования HLSL. Он также называется *конвейером отрисовки графики*или просто *конвейером*.

Чтобы помочь вам создать этот конвейер, необходимо ознакомиться с этими сведениями.

- [HLSL](#hlsl). Для игр UWP на базе DirectX рекомендуется HLSL Shader Model 5.1 и более поздних версий.
- [Шейдеры](#shaders).
- [Шейдеры вершин и шейдеры пикселей](#vertex-shaders-and-pixel-shaders).
- [Этапы шейдера](#shader-stages).
- [Различные форматы файлов шейдера](#various-shader-file-formats).

Подробнее: [Общие сведения о конвейере отрисовки Direct3D 11](/windows/desktop/direct3dgetstarted/understand-the-directx-11-2-graphics-pipeline) и [Графический конвейер](/windows/desktop/direct3d11/overviews-direct3d-11-graphics-pipeline).

#### <a name="hlsl"></a>HLSL

HLSL — это высокоуровневый язык шейдеров для DirectX. С помощью HLSL можно создавать программируемые шейдеры C-LIKE для конвейера Direct3D. Подробнее: [HLSL](/windows/desktop/direct3dhlsl/dx-graphics-hlsl).

#### <a name="shaders"></a>Шейдеры

Шейдер можно рассматривать как набор инструкций, определяющих, как поверхность объекта отображается при подготовке к просмотру. Шейдеры, запрограммированные с использованием HLSL, называются шейдерами HLSL. Файлы исходного кода для шейдеров [HLSL]) (#hlsl) имеют `.hlsl` расширение файла. Эти шейдеры могут быть скомпилированы во время сборки или в среде выполнения, а также заданы на соответствующем этапе. Скомпилированный объект шейдера имеет `.cso` расширение файла.

Шейдеры Direct3D 9 можно разрабатывать с помощью Shader Model 1, Shader Model 2 и Shader Model 3; Шейдеры Direct3D 10 могут быть разработаны только для Shader Model 4. Шейдеры Direct3D 11 могут разрабатываться с использованием Shader Model 5. Шейдеры Direct3D 11.3 и Direct3D 12 могут разрабатываться с использованием Shader Model 5.1; для Direct3D 12 также может использоваться Shader Model 6.

#### <a name="vertex-shaders-and-pixel-shaders"></a>Вершинные шейдеры и построители текстур

Данные поступают в графический конвейер в виде потока примитивов и обрабатываются различными шейдерами, такими как шейдер вершин и шейдеры пикселей. 

Шейдеры вершин обрабатывают вершины, обычно с выполнением таких операций, как преобразования, скиннинг и освещение. Построители текстур (пиксельные шейдеры) позволяют применять сложные приемы затенения, такие как попиксельное освещение и постобработка. Они объединяют константные переменные, данные текстур, интерполированные повершинные значения и другие данные для получения отдельных пикселей. 

#### <a name="shader-stages"></a>Шейдерные этапы

Последовательность упомянутых выше шейдеров, определенная для обработки потока примитивов, называется шейдерными этапами конвейера отрисовки. Фактические этапы зависят от версии Direct3D, но обычно это этапы обработки вершин, геометрии и пикселей. Также существуют другие этапы — например, шейдеры поверхностей и шейдеры доменов для тесселяции, а также шейдер вычислений. Все эти этапы полностью программируемы с помощью [HLSL](#hlsl). Подробнее: [Графический конвейер](/windows/desktop/direct3d11/overviews-direct3d-11-graphics-pipeline).

#### <a name="various-shader-file-formats"></a>Различные форматы файлов шейдеров

Ниже приведены расширения файлов кода шейдера.

- Файл с `.hlsl` расширением содержит [HLSL]) (#hlsl) исходный код.
- Файл с `.cso` расширением содержит скомпилированный объект шейдера.
- Файл с `.h` расширением является файлом заголовка, но в контексте кода шейдера этот файл заголовка определяет массив байтов, содержащий данные шейдера.
- Файл с `.hlsli` расширением содержит формат буферов констант. В примере игры файл является **шейдером**  >  **константбуфферс. hlsli**.

> [!NOTE]
> Шейдер можно внедрить путем загрузки `.cso` файла во время выполнения или путем добавления `.h` файла в исполняемый код. Но для одного шейдера не используются оба.

### <a name="deeper-understanding-of-directx"></a>Подробнее о DirectX

Direct3D 11 — это набор API-интерфейсов, которые помогут нам создавать графики для ресурсоемких приложений, таких как игры, где требуется хорошая графическая карта для обработки ресурсоемких вычислений. В этом разделе кратко рассматриваются понятия, связанные с программированием графики для Direct3D 11: ресурс, подресурс, устройство и контекст устройства.

#### <a name="resource"></a>Ресурс

Ресурсы (также называемые ресурсами устройств) можно считать сведениями о том, как визуализировать объект, например текстуру, расположение или цвет. Ресурсы предоставляют данные конвейеру и определяют, что отображается во время сцены. Ресурсы можно загружать с игрового носителя или динамически создавать во время выполнения.

Ресурс — это, по сути, область памяти, к которой [конвейер](#rendering-pipeline) Direct3D может осуществлять доступ. Чтобы конвейер получал доступ к памяти эффективным образом, данные, предоставляемые конвейеру (например, входные геометрические данные, ресурсы шейдеров и текстуры), должны храниться в ресурсах. Существуют два типа ресурсов, из которых образуются все ресурсы Direct3D: буфер и текстура. На каждом этапе конвейера может быть активно до 128 ресурсов. Дополнительные сведения см. в статье о [ресурсах DSC](../graphics-concepts/resources.md).

#### <a name="subresource"></a>Подресурс

Под ресурсом понимается подмножество ресурса. Direct3D может ссылаться на весь ресурс или может ссылаться на подмножества ресурса. Подробнее: [Подресурс](../graphics-concepts/resource-types.md#subresources).

#### <a name="depth-stencil"></a>Трафарет глубины

Ресурс трафарета глубины содержит формат и буфер для хранения сведений о глубине и трафарете. Он создается с использованием ресурса текстуры. Подробнее о том, как создать ресурс трафарета глубины, см. в статье [Настройка функции трафарета глубины](/windows/desktop/direct3d11/d3d10-graphics-programming-guide-depth-stencil). Доступ к ресурсу трафарета глубины осуществляется через представление трафарета глубины, реализуемое с помощью интерфейса [ID3D11DepthStencilView](/windows/desktop/api/d3d11/nn-d3d11-id3d11depthstencilview).

Сведения о глубине указывают, какие области многоугольников находятся позади других, чтобы мы могли определить, какие из них скрыты. Информация о трафарете позволяет понять, какие пиксели маскируются. Ее можно использовать для создания специальных эффектов, поскольку она определяет, рисуется пиксель или нет (устанавливает бит равным 1 или 0). 

Дополнительные сведения см. в разделе [представление элементов глубины](../graphics-concepts/depth-stencil-view--dsv-.md), [буфер глубины](../graphics-concepts/depth-buffers.md)и [буфер шаблона](../graphics-concepts/stencil-buffers.md).

#### <a name="render-target"></a>Целевой объект отрисовки

Целевой объект отрисовки — это ресурс, в который производится запись данных по окончании этапа отрисовки. Обычно он создается с помощью метода [ID3D11Device::CreateRenderTargetView](/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createrendertargetview) с использованием заднего буфера цепочки буферов (который также является ресурсом) в качестве входного параметра. 

Каждый целевой объект отрисовки также должен иметь соответствующее представление трафарета глубины, потому что мы используем метод [OMSetRenderTargets](/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets) для задания целевого объекта отрисовки, прежде чем использовать его, и этому методу требуется в том числе представление трафарета глубины. Доступ к ресурсу целевого объекта отрисовки осуществляется через представление целевого объекта отрисовки, реализуемого с помощью интерфейса [ID3D11RenderTargetView](/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview). 

#### <a name="device"></a>Устройство

Устройство можно представить как способ выделения и уничтожения объектов, отрисовки примитивов и взаимодействия с графическими картами с помощью графического драйвера. 

Если говорить точнее, устройство Direct3D — это компонент Direct3D, выполняющий отрисовку графики. Устройство инкапсулирует и сохраняет состояние отрисовки, выполняет операции преобразования и освещения и растеризует изображение на поверхность. Подробнее: [Устройства](../graphics-concepts/devices.md)

Устройство представлено интерфейсом [**ID3D11Device**](/windows/desktop/api/d3d11/nn-d3d11-id3d11device) . Иными словами, интерфейс **ID3D11Device** представляет виртуальный адаптер дисплея и используется для создания ресурсов, принадлежащих устройству. 

Существуют разные версии ID3D11Device. [**ID3D11Device5**](/windows/desktop/api/d3d11_4/nn-d3d11_4-id3d11device5) является последней версией и добавляет новые методы в **ID3D11Device4**. Подробнее о том, как Direct3D взаимодействует с оборудованием, см. в статье [Архитектура модели драйверов устройств с Windows (WDDM)](/windows-hardware/drivers/display/windows-vista-and-later-display-driver-model-architecture).

Каждое приложение должно иметь по крайней мере одно устройство; Большинство приложений создают только один. Создайте устройство для одного из драйверов оборудования, установленных на компьютере, вызвав **D3D11CreateDevice** или **D3D11CreateDeviceAndSwapChain** и указав тип драйвера с флагом **D3D_DRIVER_TYPE** . Каждое устройство можно использовать один или несколько контекстов устройств, в зависимости от требуемой функциональности. Подробнее: [Функция D3D11CreateDevice](/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice).

#### <a name="device-context"></a>Контекст устройства

Контекст устройства используется для задания состояния [конвейера](#rendering-pipeline) и создания команд отрисовки с использованием [ресурсов](#resource) , принадлежащих [устройству](#device). 

Direct3D 11 реализует два типа контекста устройств: один для немедленной отрисовки и другой для отложенной отрисовки; оба контекста представляются с помощью интерфейса [ID3D11DeviceContext](/windows/desktop/api/d3d11/nn-d3d11-id3d11devicecontext). 

Интерфейсы **ID3D11DeviceContext** бывают разных версий; в **ID3D11DeviceContext4** добавлены новые методы относительно **ID3D11DeviceContext3**.

**ID3D11DeviceContext4** впервые появился в обновлении Windows 10 Creators и является последней версией интерфейса **ссылку ID3D11DeviceContext** . В приложениях, предназначенных для дизайнеров Windows 10, обновление и более поздние версии должны использовать этот интерфейс вместо более ранних версий. Подробнее: [ID3D11DeviceContext4](/windows/desktop/api/d3d11_3/nn-d3d11_3-id3d11devicecontext4).

#### <a name="dxdeviceresources"></a>DX::DeviceResources

Класс **DX::D евицересаурцес** находится в файлах **DeviceResources. cpp** / **. h** и управляет всеми ресурсами устройств DirectX.

### <a name="buffer"></a>Буфер

Буферный ресурс — это коллекция полностью типизированных данных, сгруппированных в элементы. Буферы можно использовать для хранения самых разных данных, в том числе векторов положений, нормальных векторов, координат текстур (в буфере вершин), индексов (в буфере индексов) или состояния устройства. Элементы буфера могут включать упакованные значения данных (например, значения поверхности **R8G8B8A8** ), однобайтовые 8-разрядные целые числа или 4 32-разрядные значения с плавающей точкой.

Доступно три типа буферов: буфер вершин, буфер индексов и буфер констант.

#### <a name="vertex-buffer"></a>Буфер вершин

Содержит данные вершин, используемые для определения геометрии. К данным вершин относятся координаты положения, данные цвета, данные координат текстуры, данные нормали и т. д. 

#### <a name="index-buffer"></a>Буфер индексов

Содержит целочисленные указатели для буферов вершин и используется для более эффективной отрисовки примитивов. Буфер индексов содержит последовательный набор 16-разрядных или 32-разрядных индексов; каждый индекс используется для идентификации вершины в буфере вершин.

#### <a name="constant-buffer-or-shader-constant-buffer"></a>Буфер константы или буфер построителей текстуры

Позволяет эффективно поставлять данные шейдера в конвейер. Буферы констант можно использовать в качестве входных данных для шейдеров, которые выполняются для каждого примитива и сохраняют результаты этапа потокового вывода конвейера отрисовки. На концептуальном уровне буфер констант аналогичен одноэлементному буферу вершин.

#### <a name="design-and-implementation-of-buffers"></a>Проектирование и реализация буферов

Можно создавать буферы на основе типа данных, например, как в нашем примере игры, один буфер создается для статических данных, другой — для данных, которые являются константами в кадре, а другой — для данных, относящихся к примитиву.

Все типы буферов инкапсулируются интерфейсом **ID3D11Buffer**; создать буферный ресурс можно путем вызова метода **ID3D11Device::CreateBuffer**. Однако прежде чем к буферу можно будет осуществить доступ, его необходимо привязать к конвейеру. Для чтения буферы могут быть привязаны одновременно к нескольким этапам конвейера. Кроме того, буфер можно привязать к одному этапу конвейера для записи; Однако один и тот же буфер не может быть привязан одновременно для чтения и записи.

Вы можете привязать буфер таким образом.

- На этап входного ассемблера путем вызова методов **ссылку ID3D11DeviceContext** , таких как **ссылку ID3D11DeviceContext:: иасетвертексбуфферс** и **ссылку ID3D11DeviceContext:: иасетиндексбуффер**.
- В стадию потокового вывода путем вызова **ссылку ID3D11DeviceContext:: сосеттаржетс**.
- На этап шейдера путем вызова методов шейдера, таких как **ссылку ID3D11DeviceContext:: вссетконстантбуфферс**.

Подробнее: [Вводные сведения о буферах в Direct3D 11](/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-intro).

### <a name="dxgi"></a>DXGI

Графическая инфраструктура Microsoft DirectX (DXGI) — это подсистема, которая инкапсулирует некоторые низкоуровневые задачи, необходимые Direct3D. При использовании DXGI в многопоточном приложении необходимо учитывать особое внимание, чтобы избежать взаимоблокировок. Дополнительные сведения см. в статье [многопоточность и DXGI](/windows/win32/direct3darticles/dxgi-best-practices#multithreading-and-dxgi) .

### <a name="feature-level"></a>Уровень компонентов

Уровень компонентов — это концепция, появившаяся в Direct3D 11 для обработки всего разнообразия видеоадаптеров в новых и существующих компьютерах. Уровень функций — это четко определенный набор функций графического процессора. 

Каждый видеоадаптер реализует определенный уровень функциональности DirectX в зависимости от установленных графических процессоров. В предыдущих версиях Microsoft Direct3D можно было узнать версию Direct3D, реализуемую видеоадаптером, а затем программировать приложение соответствующим образом. 

Используя уровень компонентов, при создании устройства вы можете попытаться создать устройство для уровня компонентов, который вы хотите запросить. Если создать устройство удалось, этот уровень компонентов существует; если нет, оборудование не поддерживает этот уровень компонентов. Можно либо попытаться повторно создать устройство на более низком уровне компонентов, либо выйти из приложения. Например, для уровня компонентов 12_0 требуется Direct3D 11,3 или Direct3D 12, а также модель шейдера 5,1. Подробнее: [Уровни компонентов Direct3D: обзор каждого уровня компонентов](/windows/desktop/direct3d11/overviews-direct3d-11-devices-downlevel-intro).

Используя уровни компонентов, можно разработать приложение для Direct3D 9, Microsoft Direct3D 10 или Direct3D 11, а затем запустить его на оборудовании 9, 10 или 11 (за некоторыми исключениями). Подробнее: [Уровни компонентов Direct3D](/windows/desktop/direct3d11/overviews-direct3d-11-devices-downlevel-intro).

### <a name="stereo-rendering"></a>Стереоскопическая отрисовка

Стереоскопическая отрисовка используется для усиления иллюзии глубины. Она предполагает использование двух изображений — одного для левого глаза, другого для правого — для отображения сцены на экране дисплея. 

С математической точки зрения для достижения этого эффекта применяется матрица стереопроекции, которая представляет собой небольшое горизонтальное смещение обычной матрицы монопроекции вправо и влево.

Мы выполнили два прохода отрисовки для получения стерео отрисовки в этой учебной игре.

- Привязались к правому целевому объекту отрисовки, применили правую проекцию и нарисовали объект примитива.
- Привязались к левому целевому объекту отрисовки, применили левую проекцию и нарисовали объект примитива.

### <a name="camera-and-coordinate-space"></a>Камера и пространство координат

Игра снабжена кодом для обновления мира в его собственной системе координат (которая иногда называется "пространство мира" или "пространство сцены"). Все объекты, включая камеру, расположены и ориентированы в этом пространстве. Подробнее: [Системы координат](../graphics-concepts/coordinate-systems.md).

Вершинный шейдер выполняет преобразование координат модели в координаты устройства с помощью следующего алгоритма (где V — вектор, а M — матрица):

`V(device) = V(model) x M(model-to-world) x M(world-to-view) x M(view-to-device)`

- `M(model-to-world)`Матрица преобразования для координат модели в мировые координаты, также известные как [Матрица универсального преобразования](#world-transform-matrix). Предоставляется примитивом.
- `M(world-to-view)`— это матрица преобразования для координат мира для просмотра координат, также известных как [Матрица преобразования представления](#view-transform-matrix).
  - Получить ее можно из матрицы представления камеры. Он определяется положением камеры вместе с векторами вида ( *вид* вектора, который указывает непосредственно на сцену из камеры, и вектор *поиска* , который находится в обратном направлении к нему).
  - В демонстрационной игре **m_viewMatrix** является матрицей преобразования «представление» и вычисляется с помощью **Camera:: сетвиевпарамс**.
- `M(view-to-device)`Матрица преобразования для представления координат в координаты устройства, также известные как [Матрица преобразования проекции](#projection-transform-matrix).
  - Получить ее можно из проекции камеры. Он предоставляет сведения о том, какая часть пространства на самом деле видна в окончательной сцене. Поле представления (фов), пропорции и отсеченные плоскости определяют матрицу преобразования проекции.
  - В примере игры **m_projectionMatrix** определяет преобразование в координаты проекции, вычисляемые с помощью **Camera:: сетпрожпарамс** (для стерео проекции для каждого взгляда используется две матрицы проекции &mdash; ).

Код шейдера в `VertexShader.hlsl` загружается с этими векторами и матрицами из буферов констант и выполняет это преобразование для каждой вершины.

### <a name="coordinate-transformation"></a>Преобразование координат

В Direct3D используется три преобразования для перевода координат трехмерной модели в координаты пикселей (пространство экрана). Эти преобразования — мировое преобразование, видовое преобразование и проекционное преобразование. Дополнительные сведения см. в разделе [Общие сведения о преобразовании](../graphics-concepts/transform-overview.md).

#### <a name="world-transform-matrix"></a>Матрица мирового преобразования

Мировое преобразование обеспечивает перевод координат из пространства модели, где вершины определяются относительно локального начала координат модели, в мировое пространство, где вершины определяются относительно начала координат, общего для всех объектов в сцене. По сути, мировое преобразование помещает модель в мир, — отсюда и название. Подробнее: [Мировое преобразование](../graphics-concepts/world-transform.md).

#### <a name="view-transform-matrix"></a>Матрица видового преобразования

Видовое преобразование определяет положение наблюдателя в мировом пространстве и обеспечивает преобразование вершин в пространство камеры. В пространстве камеры камера, или наблюдатель, находится в начале координат и смотрит в направлении положительной полуоси Z. Подробнее: [Видовое преобразование](../graphics-concepts/view-transform.md).

####  <a name="projection-transform-matrix"></a>Матрица проекционного преобразования

Проекционное преобразование превращает видимое пространство (усеченную пирамиду) в кубовидную фигуру. Усеченная пирамида видимости — это трехмерный объем в сцене, позиционируемый относительно камеры окна просмотра. Окно просмотра — это двумерный прямоугольник, на который проецируется трехмерная сцена. Подробнее: [Окна просмотра и обрезка](../graphics-concepts/viewports-and-clipping.md)

Так как ближний конец видимого пространства меньше дальнего, это приводит к развертыванию объектов, которые находятся ближе к камере — так перспектива применяется к сцене. Таким образом, объекты, расположенные ближе к проигрывателю, выглядят больше; объекты, которые больше не отображаются, выглядят меньше.

Как правило, преобразование проекции — это матрица, которая обычно является шкалой и перспективной проекцией. Она функционирует подробно объективу камеры. Подробнее: [Проекционное преобразование](../graphics-concepts/projection-transform.md).

### <a name="sampler-state"></a>Состояние дискретизатора

Состояние дискретизатора определяет, как дискретизируются данные текстур, с использованием режимов адресации текстур, фильтрации и уровня детализации. Выборка выполняется каждый раз, когда из текстуры считывается пиксель текстуры (или шаг текселя).

Текстура содержит массив пикселей текстуры. Расположение каждого шаг текселя обозначается с помощью `(u,v)` , где `u` — Ширина и `v` Высота, и сопоставляется между 0 и 1 в зависимости от ширины и высоты текстуры. Полученные координаты текстуры используются для адресации текселя при дискретизации текстуры.

Когда координаты текстуры меньше 0 или больше 0, режим адресации текстуры определяет, как координата текстуры адресует положение пикселя. Например, при использовании **TextureAddressMode.Clamp**любая координата за пределами диапазона 0–1 приводится к максимальному значению (1) и минимальному значению (0) перед дискретизацией.

Если текстура слишком велика или слишком мала для многоугольника, то текстура фильтруется в соответствии с пространством. Фильтр увеличения увеличивает текстуру, а фильтр уменьшения уменьшает ее так, чтобы она поместилась в меньшую область. При увеличении текстуры тексель повторяется для одного или нескольких адресов, что дает более размытое изображение. Текстурная минификации более сложная, так как требует объединения более одного значения шаг текселя в одно значение. Это может привести к искажениям контура (алиасингу), в зависимости от данных текстуры. Самый популярный подход к уменьшению — это использование MIP-карты. MIP-карта — это многоуровневая текстура. Размер каждого уровня является степенью 2, меньшей, чем предыдущий уровень, до текстуры 1x1. При использовании уменьшения игра выбирает уровень MIP-карты, ближайший к необходимому ей размеру, во время отрисовки. 

### <a name="the-basicloader-class"></a>Класс Басиклоадер

**BasicLoader** — это простой класс-загрузчик, который обеспечивает функциональность для загрузки шейдеров, текстур и сеток из файлов на диске. Он предоставляет и синхронные, и асинхронные методы. В этом примере игры файлы находятся `BasicLoader.h/.cpp` в папке **Служебные программы** .

Подробнее: [Basic Loader](complete-code-for-basicloader.md).