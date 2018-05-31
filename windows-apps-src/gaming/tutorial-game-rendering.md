---
author: joannaleecy
title: Настройка
description: Узнайте, как собрать конвейер визуализации для отображения графических объектов. Отрисовка игры, настройка и подготовка данных.
ms.assetid: 7720ac98-9662-4cf3-89c5-7ff81896364a
ms.author: joanlee
ms.date: 10/24/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, игры, отрисовка
ms.localizationpriority: medium
ms.openlocfilehash: 42662d7ecfa415f9528f0acd3e4d4b7ce25cf40a
ms.sourcegitcommit: f9a4854b6aecfda472fb3f8b4a2d3b271b327800
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
ms.locfileid: "1396983"
---
# <a name="rendering-framework-ii-game-rendering"></a>Платформа отрисовки II: отрисовка игры

В разделе [Платформа отрисовки I](tutorial--assembling-the-rendering-pipeline.md) мы рассмотрели, как сведения о сцене и выводятся на экран для отображения. Теперь мы сделаем шаг назад и узнаем, как подготовить данные для отрисовки.

>[!Note]
>Если вы еще не скачали последний код для игры из этого примера, перейдите в раздел [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

## <a name="objective"></a>Цель

Краткое обобщение задачи. Требуется понять, как настроить базовую платформу отрисовки для отображения вывода графики для игр UWP на базе DirectX. Мы можем гибко сгруппировать необходимые процедуры в эти три шага.

 1. Подключение к нашему графическому интерфейсу
 2. Подготовка: создание ресурсов, необходимых для рисования графики
 3. Отображение графики: отрисовка кадра

В разделе [Платформа отрисовки I: введение в отрисовку](tutorial--assembling-the-rendering-pipeline.md) описано, как выполняется отрисовка графики, что охватывает шаги 1 и 3. 

В этой статье объясняется, как настроить другие компоненты этой платформы и подготовить необходимые данные для выполнения отрисовки, что представляет собой шаг 2.

## <a name="design-the-renderer"></a>Проектирование обработчика

Обработчик отвечает за создание и поддержание всех объектов D3D11 и D2D, используемых для создания визуальных элементов игры. Класс __GameRenderer__ — обработчик для этого примера игры и он спроектирован с учетом потребностей отрисовки этой игры.

Ниже перечислены некоторые основные понятия, которые можно использовать, чтобы спроектировать обработчик для вашей игры:
* Так как API Direct3D 11 определены как API-интерфейсы [COM](https://msdn.microsoft.com/library/windows/desktop/ms694363.aspx), необходимо предоставить ссылки [ComPtr](https://docs.microsoft.com/cpp/windows/comptr-class) на объекты, определяемые этими API. Эти объекты автоматически освобождаются, когда последняя ссылка выходит за пределы области при завершении работы приложения. Дополнительные сведения см. в статье [ComPtr](https://github.com/Microsoft/DirectXTK/wiki/ComPtr). Пример этих объектов: буферы констант, объекты шейдера — [вершинный шейдер](tutorial--assembling-the-rendering-pipeline.md#vertex-shaders-and-pixel-shaders), [пиксельный шейдер](tutorial--assembling-the-rendering-pipeline.md#vertex-shaders-and-pixel-shaders) и объекты ресурсов шейдеров.
* Буферы констант определяются в этом классе для хранения различных данных, необходимых для отрисовки.
    * Используйте несколько буферов констант с различной частотой обновления для уменьшения количества данных, которые необходимо посылать GPU для каждого кадра. В этом примере константы разделяются по разным буферам на основании частоты их обновления. Это лучшая методика программирования в Direct3D. 
    * В этом примере игры определены 4 буфера констант.
        1. __m\_constantBufferNeverChanges__ содержит параметры освещения. Он задается однократно в методе __FinalizeCreateGameDeviceResources__ и никогда не меняется.
        2. __m\_constantBufferChangeOnResize__ содержит матрицу проекции. Матрица проекции зависит от размера и пропорций окна. Он задается в [__CreateWindowSizeDependentResources__](#createwindowsizedependentresources-method) и затем обновляется после загрузки ресурсов в методе [__FinalizeCreateGameDeviceResources__](#finalizecreategamedeviceresources-method). Если выполняется отрисовка в трехмерном пространстве, он также изменяется дважды для каждого кадра.
        3. __m\_constantBufferChangesEveryFrame__ содержит матрицу представления. Эта матрица зависит от положения камеры и направления взгляда (перпендикулярно проекции) и изменяется только один раз за каждый кадр в методе __Render__. Это было описано ранее в разделе __Платформа отрисовки I: введение в отрисовку__ в описании метода [__GameRenderer::Render__](tutorial--assembling-the-rendering-pipeline.md#gamerendererrender-method).
        4. __m\_constantBufferChangesEveryPrim__ содержит матрицу модели и свойства материала каждого примитива. Матрица модели преобразует вершины из локальных координат в мировые. Эти константы являются специфическими для каждого примитива и обновляются при каждом вызове метода рисования. Это было описано ранее в разделе __Платформа отрисовки I: введение в отрисовку__ в подразделе [Отрисовка примитивов](tutorial--assembling-the-rendering-pipeline.md#primitive-rendering).
* В этом классе также определяются объекты ресурсов шейдеров, которые содержат текстуры для примитивов.
    * Некоторые текстуры являются предопределенными ([DDS](https://msdn.microsoft.com/library/windows/desktop/bb943991.aspx) — это формат файла, который может использоваться для хранения сжатых и несжатых текстур. Текстуры DDS используются для стен и пола мира, а также сферы боеприпасов).
    * В этом примере игры используются следующие объекты ресурсов шейдеров: __m\_sphereTexture__, __m\_cylinderTexture__, __m\_ceilingTexture__, __m\_floorTexture__, __m\_wallsTexture__.
* Объекты шейдера определены в этом классе для расчета наших примитивов и текстур. 
    * В этом примере игры используются следующие объекты шейдера: __m\_vertexShader__, __m\_vertexShaderFlat__ и __m\_pixelShader__, __m\_pixelShaderFlat__.
    * Вершинный шейдер обрабатывает примитивы и базовое освещение, а построитель текстуры (иногда называемый фрагментным шейдером) обрабатывает текстуры и все пиксельные эффекты.
    * Есть две версии этих шейдеров (обычные и плоские) для визуализации различных примитивов. Причина наличия различных версий в том, что плоские версии гораздо проще и не используют световые блики, как и любые попиксельные эффекты освещения. Они используются для текстур стен и ускоряют прорисовку на менее мощных устройствах.

## <a name="gamerendererh"></a>GameRenderer.h

Теперь рассмотрим код в объекте класса обработчика в нашем примере игры и сравним его с обработчиком __Sample3DSceneRenderer.h__, представленным в шаблоне приложения DirectX 11.

```cpp
// Class object handling the rendering of the game
ref class GameRenderer
{
internal:
    GameRenderer(const std::shared_ptr<DX::DeviceResources>& deviceResources);
    
    // Compared with Sample3DSceneRenderer.h in the DirectX 11 App template sample. 
    
    // These methods are present.
    void CreateDeviceDependentResources();
    void CreateWindowSizeDependentResources();
    void ReleaseDeviceDependentResources();
    void Render();

    // Added: Async related methods to prepare 3D game objects for rendering
    concurrency::task<void> CreateGameDeviceResourcesAsync(_In_ Simple3DGame^ game);
    void FinalizeCreateGameDeviceResources();
    concurrency::task<void> LoadLevelResourcesAsync();
    void FinalizeLoadLevelResources();
    // --- end of async related methods section

    // Added: Methods for rendering overlay
    Simple3DGameDX::IGameUIControl^ GameUIControl()  { return m_gameInfoOverlay; };

    DirectX::XMFLOAT2 GameInfoOverlayUpperLeft()
    {
        return DirectX::XMFLOAT2(m_gameInfoOverlayRect.left, m_gameInfoOverlayRect.top);
    };
    DirectX::XMFLOAT2 GameInfoOverlayLowerRight()
    {
        return DirectX::XMFLOAT2(m_gameInfoOverlayRect.right, m_gameInfoOverlayRect.bottom);
    };
    bool GameInfoOverlayVisible() { return m_gameInfoOverlay->Visible(); }
    // --- end of rendering overlay section

    //...
    protected private:
    // Cached pointer to device resources.
    std::shared_ptr<DX::DeviceResources>                m_deviceResources;

    // ...

    // Shader resource objects
    Microsoft::WRL::ComPtr<ID3D11ShaderResourceView>    m_sphereTexture;
    Microsoft::WRL::ComPtr<ID3D11ShaderResourceView>    m_cylinderTexture;
    Microsoft::WRL::ComPtr<ID3D11ShaderResourceView>    m_ceilingTexture;
    Microsoft::WRL::ComPtr<ID3D11ShaderResourceView>    m_floorTexture;
    Microsoft::WRL::ComPtr<ID3D11ShaderResourceView>    m_wallsTexture;

    // Constant Buffers
    Microsoft::WRL::ComPtr<ID3D11Buffer>                m_constantBufferNeverChanges;
    Microsoft::WRL::ComPtr<ID3D11Buffer>                m_constantBufferChangeOnResize;
    Microsoft::WRL::ComPtr<ID3D11Buffer>                m_constantBufferChangesEveryFrame;
    Microsoft::WRL::ComPtr<ID3D11Buffer>                m_constantBufferChangesEveryPrim;

    // Texture sampler
    Microsoft::WRL::ComPtr<ID3D11SamplerState>          m_samplerLinear;

    // Shader objects: Vertex shaders and pixel shaders
    Microsoft::WRL::ComPtr<ID3D11VertexShader>          m_vertexShader;
    Microsoft::WRL::ComPtr<ID3D11VertexShader>          m_vertexShaderFlat;
    Microsoft::WRL::ComPtr<ID3D11PixelShader>           m_pixelShader;
    Microsoft::WRL::ComPtr<ID3D11PixelShader>           m_pixelShaderFlat;
    Microsoft::WRL::ComPtr<ID3D11InputLayout>           m_vertexLayout;
};
```

## <a name="constructor"></a>Конструктор

Далее рассмотрим конструктор __GameRenderer__ в нашем примере игры и сравним его с конструктором __Sample3DSceneRenderer.h__, представленным в шаблоне приложения DirectX 11.

```cpp
// Constructor method of the main rendering class object
GameRenderer::GameRenderer(const std::shared_ptr<DX::DeviceResources>& deviceResources) : //...
{
    // Compared with Sample3DSceneRenderer::Sample3DSceneRenderer in the DirectX 11 App template sample. 
    
    // Added: Create a new GameHud object to rendered text on the top left corner of the screen
    m_gameHud = ref new GameHud(
        deviceResources,
        "Windows platform samples",
        "DirectX first-person game sample"
        );
    //--- end of new GameHud object section
        
    // Added: Game info rendered as an overlay on the top right corner of the screen (eg. Hits, Shots, Time)    
    m_gameInfoOverlay = ref new GameInfoOverlay(deviceResources);
    //--- end of game info rendered as overlay section

    // These methods are present.
    CreateDeviceDependentResources();
    CreateWindowSizeDependentResources();
}
```

## <a name="create-and-load-directx-graphic-resources"></a>Создание и загрузка графических ресурсов DirectX

В нашем примере игры (и в шаблоне Visual Studio для __приложения DirectX 11 (Universal Windows)__) создание и загрузка ресурсов игры реализуются с помощью этих двух методов, которые вызываются из конструктора __GameRenderer__:

* [__CreateDeviceDependentResources__](#createdevicedependentresources-method)
* [__CreateWindowSizeDependentResources__](#createwindowsizedependentresources-method)

## <a name="createdevicedependentresources-method"></a>Метод CreateDeviceDependentResources

В шаблоне приложения DirectX 11 этот метод используется для асинхронной загрузки шейдеров вершин и пикселей, создания буфер шейдеров и константы, создания сетки с вершинами, содержащими сведения о положении и цвете. 

В нашем примере игры эти операции над объектами сцены вместо этого распределены между методами [__CreateGameDeviceResourcesAsync__](#creategamedeviceresourcesasync-method) и [__FinalizeCreateGameDeviceResources__](#finalizecreategamedeviceresources-method). 

Что поступает в этот метод в нашем примере игры?

* Созданные переменные (__m\_gameResourcesLoaded__ = false и __m\_levelResourcesLoaded__ = false), указывающие, загружены ли ресурсы, для перехода к отрисовке, так как мы загружаем их асинхронно. 
* Поскольку отрисовка наложения и элементов HUD производятся в отдельных объектах класса, здесь необходимо вызвать методы __GameHud::CreateDeviceDependentResources__ и __GameInfoOverlay::CreateDeviceDependentResources__.

Ниже представлен код для __GameRenderer::CreateDeviceDependentResources__.

```cpp
// This method is called in GameRenderer constructor when it's created in GameMain constructor.
void GameRenderer::CreateDeviceDependentResources()
{
    // instantiate variables that indicate if resources were loaded.
    m_gameResourcesLoaded = false;
    m_levelResourcesLoaded = false;

    // game HUD and overlay are design as separate class objects.
    m_gameHud->CreateDeviceDependentResources();
    m_gameInfoOverlay->CreateDeviceDependentResources();
}
```
В таблице ниже перечислены методы, которые используются для создания и загрузки ресурсов. __CreateGameDeviceResourcesAsync__ и __FinalizeCreateGameDeviceResources__ добавляются в примере игры таким образом, что ресурсы загружаются асинхронно.

|Исходный шаблон приложения DirectX 11           |Пример игры                                                                |
|-------------------------------------------|---------------------------------------------------------------------------|
|CreateDeviceDependentResources             |CreateDeviceDependentResources                                             |
|                                           | - CreateGameDeviceResourcesAsync (добавлено)                                  |
|                                           | - FinalizeCreateGameDeviceResources (добавлено)                               |
|CreateWindowSizeDependentResources         |CreateWindowSizeDependentResources                                         |

Прежде чем углубляться в другие методы, которые используются для создания и загрузки ресурсов, сначала создадим обработчик и посмотрим, какое место он занимает в цикле игры.

## <a name="create-the-renderer"></a>Создание обработчика

__GameRenderer__ создается в конструкторе __GameMain__. Он также вызывает два других метода, [__CreateGameDeviceResourcesAsync__](#creategamedeviceresourcesasync-method) и [__FinalizeCreateGameDeviceResources__](#finalizecreategamedeviceresources-method), добавляемые поддержки при создании и загрузке ресурсов.

```cpp

GameMain::GameMain(const std::shared_ptr<DX::DeviceResources>& deviceResources) : // ...
{
    m_deviceResources->RegisterDeviceNotify(this);

    // These methods are used in the DirectX 11 App template to create the class objects used for rendering. 
    // But are replaced in this game sample with GameRenderer as shown below.
    // m_sceneRenderer = std::unique_ptr<Sample3DSceneRenderer>(new Sample3DSceneRenderer(m_deviceResources));
    // m_fpsTextRenderer = std::unique_ptr<SampleFpsTextRenderer>(new SampleFpsTextRenderer(m_deviceResources));
    
    // Creation of GameRenderer
    m_renderer = ref new GameRenderer(m_deviceResources);
    
    //...

     create_task([this]()
    {
        // Asynchronously initialize the game class and load the renderer device resources.
        // By doing all this asynchronously, the game gets to its main loop more quickly
        // and in parallel all the necessary resources are loaded on other threads.
        m_game->Initialize(m_controller, m_renderer);

        return m_renderer->CreateGameDeviceResourcesAsync(m_game);

    }).then([this]()
    {
        // The finalize code needs to run in the same thread context
        // as the m_renderer object was created because the D3D device context
        // can ONLY be accessed on a single thread.
        m_renderer->FinalizeCreateGameDeviceResources();

        InitializeGameState();
    
    //...
}
```

## <a name="creategamedeviceresourcesasync-method"></a>Метод CreateGameDeviceResourcesAsync

__CreateGameDeviceResourcesAsync__ вызывается из метода конструктора __GameMain__ в цикле __create\_task__, так как мы загружаем ресурсы игры асинхронно.
        
__CreateDeviceResourcesAsync__ — метод, выполняющийся как отдельный набор асинхронных задач для загрузки ресурсов игры. Так как от него ожидается выполнение в отдельном потоке, у него есть доступ только к методам устройств Direct3D 11 (определенным __ID3D11Device__), но не методам контекста устройств (определенным на __ID3D11DeviceContext__); поэтому он не выполняет никакой визуализации.

Метод __FinalizeCreateGameDeviceResources__ выполняется в основном потоке, и у него имеется доступ к методам контекста устройств Direct3D 11.

Принцип:
* Используйте только методы __ID3D11Device__ в __CreateGameDeviceResourcesAsync__, поскольку они не привязаны к потокам, то есть могут выполняться в любом потоке. Также предполагается, что они не будут выполняться в том же потоке, где создан __GameRenderer__. 
* Не используйте здесь методы из __ID3D11DeviceContext__, так как они должны выполняться в одном потоке и в том же потоке, что __GameRenderer__.
* Используйте этот метод, чтобы создать буферы констант.
* Используйте этот метод для загрузки текстур (например, DDS-файлов) и сведения шейдера (например, CSO-файлы) в [шейдеры](tutorial--assembling-the-rendering-pipeline.md#shaders).

Этот метод используется в следующих целях:
* Создание 4 [буферов констант](tutorial--assembling-the-rendering-pipeline.md#buffer): __m\_constantBufferNeverChanges__, __m\_constantBufferChangeOnResize__, __m\_constantBufferChangesEveryFrame__, __m\_ constantBufferChangesEveryPrim__
* Создание объекта [состояния дискретизатора](tutorial--assembling-the-rendering-pipeline.md#sampler-state), инкапсулирующего информацию о дискретизации для текстуры
* Создание группы задач, включающей все асинхронные задачи, созданные методом. Она ожидает завершения всех этих асинхронных задач и вызывает __FinalizeCreateGameDeviceResources__.
* Создание загрузчика с помощью [Basic Loader](tutorial--assembling-the-rendering-pipeline.md#basicloader). Добавление операций асинхронной загрузки загрузчика в виде задач в ранее созданную группу задач.
* Такие методы, как __BasicLoader::LoadShaderAsync__ и __BasicLoader::LoadTextureAsync__, используются для загрузки следующих компонентов:
    * скомпилированные объекты шейдера (VertextShader.cso, VertexShaderFlat.cso, PixelShader.cso и PixelShaderFlat.cso). Дополнительные сведения см. в разделе [Различные форматы файлов шейдера](tutorial--assembling-the-rendering-pipeline.md#various-shader-file-formats).
    * текстуры для данной игры (Assets\\seafloor.dds metal_texture.dds, cellceiling.dds, cellfloor.dds, cellwall.dds).

```cpp
task<void> GameRenderer::CreateGameDeviceResourcesAsync(_In_ Simple3DGame^ game)
{
    // Create the device dependent game resources.
    // Only the d3dDevice is used in this method.  It is expected
    // to not run on the same thread as the GameRenderer was created.
    // Create methods on the d3dDevice are free-threaded and are safe while any methods
    // in the d3dContext should only be used on a single thread and handled
    // in the FinalizeCreateGameDeviceResources method.
    m_game = game;

    auto d3dDevice = m_deviceResources->GetD3DDevice();

    // Define D3D11_BUFFER_DESC.
    // For API ref, go to: https://msdn.microsoft.com/library/windows/desktop/ff476092.aspx
    D3D11_BUFFER_DESC bd;
    ZeroMemory(&bd, sizeof(bd));
    
    bd.Usage = D3D11_USAGE_DEFAULT;
    // ...
    
    // Create the constant buffers: m_constantBufferNeverChanges, m_constantBufferChangeOnResize,
    // m_constantBufferChangesEveryFrame, m_constantBufferChangesEveryPrim
    // CreateBuffer is used to create one of these buffers: vertex buffer, index buffer, or 
    // shader-constant buffer. For CreateBuffer API ref info, go to: https://msdn.microsoft.com/library/windows/desktop/ff476501.aspx
    
    DX::ThrowIfFailed(
        d3dDevice->CreateBuffer(&bd, nullptr, &m_constantBufferNeverChanges) 
        );
    // ...
    
    // Define D3D11_SAMPLER_DESC. For API ref, go to: https://msdn.microsoft.com/library/windows/desktop/ff476207.aspx
    D3D11_SAMPLER_DESC sampDesc;

    // ZeroMemory fills a block of memory with zeros. 
    // For API ref, go to: https://msdn.microsoft.com/en-us/library/windows/desktop/aa366920(v=vs.85).aspx
    ZeroMemory(&sampDesc, sizeof(sampDesc));

    sampDesc.Filter = D3D11_FILTER_MIN_MAG_MIP_LINEAR;
    sampDesc.AddressU = D3D11_TEXTURE_ADDRESS_WRAP;
    sampDesc.AddressV = D3D11_TEXTURE_ADDRESS_WRAP;
    // ...
    
    // Create a sampler-state object that encapsulates sampling information for a texture.
    // The sampler-state interface holds a description for sampler state that you can bind to any 
    // shader stage of the pipeline for reference by texture sample operations.
    DX::ThrowIfFailed(
        d3dDevice->CreateSamplerState(&sampDesc, &m_samplerLinear)
        );

    // Start the async tasks to load the shaders and textures (resources).
    
    // Load compiled shader objects (VertextShader.cso, VertexShaderFlat.cso, PixelShader.cso, and PixelShaderFlat.cso).
    // The BasicLoader class is used to convert and load common graphics resources, such as meshes, textures, 
    // and various shader objects into the constant buffers. 
    // For more info, go to: https://docs.microsoft.com/windows/uwp/gaming/complete-code-for-basicloader
    BasicLoader^ loader = ref new BasicLoader(d3dDevice);

    std::vector<task<void>> tasks;

    uint32 numElements = ARRAYSIZE(PNTVertexLayout);

    // Load shaders asynchronously with the shader and pixel data using the BasicLoader::LoadShaderAsync method
    // Push these method calls into a list of tasks
    tasks.push_back(loader->LoadShaderAsync("VertexShader.cso", PNTVertexLayout, numElements, &m_vertexShader, &m_vertexLayout));
    tasks.push_back(loader->LoadShaderAsync("VertexShaderFlat.cso", nullptr, numElements, &m_vertexShaderFlat, nullptr));
    tasks.push_back(loader->LoadShaderAsync("PixelShader.cso", &m_pixelShader));
    tasks.push_back(loader->LoadShaderAsync("PixelShaderFlat.cso", &m_pixelShaderFlat));

    // Make sure the previous versions are set to NULL before any of the textures are loaded.
    m_sphereTexture = nullptr;
    // ...

    // Load Game specific textures (Assets\\seafloor.dds, metal_texture.dds, cellceiling.dds, cellfloor.dds, cellwall.dds).
    // Push these method calls also into a list of tasks
    tasks.push_back(loader->LoadTextureAsync("Assets\\seafloor.dds", nullptr, &m_sphereTexture));
    // ...
    
    tasks.push_back(create_task([]()
    {
        // Simulate loading additional resources by introducing a delay.
        wait(GameConstants::InitialLoadingDelay);
    }));

    // Returns when all the async tasks for loading the shader and texture assets have completed.
    return when_all(tasks.begin(), tasks.end());
}
```

## <a name="finalizecreategamedeviceresources-method"></a>Метод FinalizeCreateGameDeviceResources

Метод __FinalizeCreateGameDeviceResources__ вызывается после выполнения всех задач загрузки ресурсов, которые находятся в методе __CreateGameDeviceResourcesAsync__. 

* Инициализируйте constantBufferNeverChanges со значениями положения и цвета источников света. Загружает исходные данные в буферы констант посредством вызова метода контекста устройства к __ID3D11DeviceContext::UpdateSubresource__.
* После завершения загрузки асинхронно загружаемых ресурсов следует связать их с соответствующими объектами в игре.
* Для каждого объекта в игре создайте сетку и материал с помощью загруженных текстур. Затем привяжите сетку и материал к игровому объекту.
* Для игровых объектов в виде мишени текстура, состоящая из цветных концентрических кругов с числовыми значениями поверх них, не загружается из файла текстуры. Вместо этого она процедурно создается с помощью кода в __TargetTexture.cpp__. Класс __TargetTexture__ создает необходимые ресурсы для рисования текстуры в находящийся вне экрана ресурс во время инициализации. Полученная текстура затем связывается с соответствующими игровыми объектами в виде мишеней.

В __FinalizeCreateGameDeviceResources__ и [__CreateWindowSizeDependentResources__](#createwindowsizedependentresource-method) есть похожие фрагменты кода для следующих элементов:
* Используйте __SetProjParams__ для обеспечения того, что камера использует верную матрицу проекции. Подробнее см. в статье [Камера и пространство координат](tutorial--assembling-the-rendering-pipeline.md#camera-and-coordinate-space).
* Обрабатывайте поворот экрана путем последующего умножения матрицы 3D-вращения на матрицу проекции камеры. Затем обновите буфер констант __ConstantBufferChangeOnResize__, используя полученную матрицу проекции.
* Задайте глобальную переменную __m\_gameResourcesLoaded__ типа __Boolean__, чтобы указать, что ресурсы теперь загружены в буферы и готовы к следующему шагу. Помните, что мы инициализировали эту переменную со значением __FALSE__ в методе-конструкторе __GameRenderer__ с помощью метода __GameRenderer::CreateDeviceDependentResources__. 
* Когда значение __m\_gameResourcesLoaded__ — __TRUE__, может выполняться отрисовка объектов сцены. Это рассматривается в разделе __Платформа отрисовки I: введение в отрисовку__ в описании [__метода GameRenderer::Render__](tutorial--assembling-the-rendering-pipeline.md#gamerendererrender-method).

```cpp
// When creating this sample game using the DirectX 11 App template, this method needs to be created.
// This new method is called from GameMain constructor in the .then loop.
// Make sure the 2D rendering is occurring on the same thread as the main rendering.
// Note: Helper class .h and .cpp files used in this method are located in the SharedContent/cpp/GameContent folder
void GameRenderer::FinalizeCreateGameDeviceResources()
{
    // All asynchronously loaded resources have completed loading.
    // Now associate all the resources with the appropriate game objects.
    // This method is expected to run in the same thread as the GameRenderer
    // was created. All work will happen behind the "Loading ..." screen after the
    // main loop has been entered.

    // Initialize constantBufferNeverChanges with the light positions and color.
    // These are handled here to ensure that the d3dContext is only
    // used in one thread.

    auto d3dDevice = m_deviceResources->GetD3DDevice();
    ConstantBufferNeverChanges constantBufferNeverChanges;
    constantBufferNeverChanges.lightPosition[0] = XMFLOAT4( 3.5f, 2.5f,  5.5f, 1.0f);
    // ...
    constantBufferNeverChanges.lightColor = XMFLOAT4(0.25f, 0.25f, 0.25f, 1.0f);

    // CPU copies data from memory (constantBufferNeverChanges) to a subresource 
    // created in non-mappable memory (m_constantBufferNeverChanges) which was created in the earlier 
    // CreateGameDeviceResourcesAsync method. For UpdateSubresource API ref info, 
    // go to: https://msdn.microsoft.com/library/windows/desktop/ff476486.aspx
    // To learn more about what a subresource is, go to:
    // https://msdn.microsoft.com/library/windows/desktop/ff476901.aspx

    m_deviceResources->GetD3DDeviceContext()->UpdateSubresource(
        m_constantBufferNeverChanges.Get(),
        0,
        nullptr,
        &constantBufferNeverChanges,
        0,
        0
        );

    // For the objects that function as targets, they have two unique generated textures.
    // One version is used to show that they have never been hit and the other is 
    // used to show that they have been hit.
    // TargetTexture is a helper class to procedurally generate textures for game
    // targets. The class creates the necessary resources to draw the texture into 
    // an off screen resource at initialization time.

    TargetTexture^ textureGenerator = ref new TargetTexture(
        d3dDevice,
        m_deviceResources->GetD2DFactory(),
        m_deviceResources->GetDWriteFactory(),
        m_deviceResources->GetD2DDeviceContext()
        );

    // CylinderMesh is a class derived from MeshObject and creates a ID3D11Buffer of
    // vertices and indices to represent a canonical cylinder (capped at
    // both ends) that is positioned at the origin with a radius of 1.0,
    // a height of 1.0 and with its axis in the +Z direction.
    // In the game sample, there are various types of mesh types:
    // CylinderMesh (vertical rods), SphereMesh (balls that the player shoots), 
    // FaceMesh (target objects), and WorldMesh (Floors and ceilings that define the enclosed area)

    MeshObject^ cylinderMesh = ref new CylinderMesh(d3dDevice, 26);
    // ...

    // The Material class maintains the properties that represent how an object will
    // look when it is rendered.  This includes the color of the object, the
    // texture used to render the object, and the vertex and pixel shader that
    // should be used for rendering.

    Material^ cylinderMaterial = ref new Material(
        XMFLOAT4(0.8f, 0.8f, 0.8f, .5f),
        XMFLOAT4(0.8f, 0.8f, 0.8f, .5f),
        XMFLOAT4(1.0f, 1.0f, 1.0f, 1.0f),
        15.0f,
        m_cylinderTexture.Get(),
        m_vertexShader.Get(),
        m_pixelShader.Get()
        );

    // ...
    auto objects = m_game->RenderObjects();

    // Attach the textures to the appropriate game objects.
    // We'll loop through all the objects that need to be rendered.
    for (auto object = objects.begin(); object != objects.end(); object++)
    {

        if ((*object)->TargetId() == GameConstants::WorldFloorId)
        {
            // Assign a normal material for the floor object.
            // This normal material uses the floor texture (cellfloor.dds) that was loaded asynchronously from
            // the Assets folder using BasicLoader::LoadTextureAsync method in the earlier 
            // CreateGameDeviceResourcesAsync loop

            (*object)->NormalMaterial(
                ref new Material(
                    XMFLOAT4(0.5f, 0.5f, 0.5f, 1.0f),
                    XMFLOAT4(0.8f, 0.8f, 0.8f, 1.0f),
                    XMFLOAT4(0.3f, 0.3f, 0.3f, 1.0f),
                    150.0f,
                    m_floorTexture.Get(),
                    m_vertexShaderFlat.Get(),
                    m_pixelShaderFlat.Get()
                    )
                );
            // Creates a mesh object called WorldFloorMesh and assign it to the floor object.
            (*object)->Mesh(ref new WorldFloorMesh(d3dDevice));
        }
        // ...
        else if (Cylinder^ cylinder = dynamic_cast<Cylinder^>(*object))
        {
            cylinder->Mesh(cylinderMesh);
            cylinder->NormalMaterial(cylinderMaterial);
        }
        else if (Face^ target = dynamic_cast<Face^>(*object))
        {
            const int bufferLength = 16;
            char16 str[bufferLength];
            int len = swprintf_s(str, bufferLength, L"%d", target->TargetId());
            Platform::String^ string = ref new Platform::String(str, len);

            ComPtr<ID3D11ShaderResourceView> texture;
            textureGenerator->CreateTextureResourceView(string, &texture);
            target->NormalMaterial(
                ref new Material(
                    XMFLOAT4(0.8f, 0.8f, 0.8f, 0.5f),
                    XMFLOAT4(0.8f, 0.8f, 0.8f, 0.5f),
                    XMFLOAT4(0.3f, 0.3f, 0.3f, 1.0f),
                    5.0f,
                    texture.Get(),
                    m_vertexShader.Get(),
                    m_pixelShader.Get()
                    )
                );

            textureGenerator->CreateHitTextureResourceView(string, &texture);
            target->HitMaterial(
                ref new Material(
                    XMFLOAT4(0.8f, 0.8f, 0.8f, 0.5f),
                    XMFLOAT4(0.8f, 0.8f, 0.8f, 0.5f),
                    XMFLOAT4(0.3f, 0.3f, 0.3f, 1.0f),
                    5.0f,
                    texture.Get(),
                    m_vertexShader.Get(),
                    m_pixelShader.Get()
                    )
                );

            target->Mesh(targetMesh);
        }
        // ...
    }


    // The SetProjParams method calculates the projection matrix based on input params and
    // ensures that the camera has been initialized with the right projection
    // matrix.  
    // The camera is not created at the time the first window resize event occurs.

    auto renderTargetSize = m_deviceResources->GetRenderTargetSize();
    m_game->GameCamera()->SetProjParams(
        XM_PI / 2,
        renderTargetSize.Width / renderTargetSize.Height,
        0.01f,
        100.0f
        );

    // Make sure that the correct projection matrix is set in the ConstantBufferChangeOnResize buffer.

    // Get the 3D rotation transform matrix. We are handling screen rotations directly to eliminate an unaligned 
    // fullscreen copy. So it is necessary to post multiply the 3D rotation matrix to the camera's projection matrix
    // to get the projection matrix that we need.

    auto orientation = m_deviceResources->GetOrientationTransform3D();

    ConstantBufferChangeOnResize changesOnResize;

    // The matrices are transposed due to the shader code expecting the matrices in the opposite
    // row/column order from the DirectX math library.

    // XMStoreFloat4x4 takes a matrix and writes the components out to sixteen single-precision floating-point values at the given address. 
    // The most significant component of the first row vector is written to the first four bytes of the address, 
    // followed by the second most significant component of the first row, and so on. The second row is then written out in a 
    // like manner to memory beginning at byte 16, followed by the third row to memory beginning at byte 32, and finally 
    // the fourth row to memory beginning at byte 48. For more API ref info, go to: 
    // https://msdn.microsoft.com/library/windows/desktop/microsoft.directx_sdk.storing.xmstorefloat4x4.aspx

    XMStoreFloat4x4(
        &changesOnResize.projection,
        XMMatrixMultiply(
            XMMatrixTranspose(m_game->GameCamera()->Projection()),
            XMMatrixTranspose(XMLoadFloat4x4(&orientation))
            )
        );

    // UpdateSubresource method instructs CPU to copy data from memory (changesOnResize) to a subresource 
    // created in non-mappable memory (m_constantBufferChangeOnResize ) which was created in the earlier 
    // CreateGameDeviceResourcesAsync method.

    m_deviceResources->GetD3DDeviceContext()->UpdateSubresource(
        m_constantBufferChangeOnResize.Get(),
        0,
        nullptr,
        &changesOnResize,
        0,
        0
        );

    // Finally we set the m_gameResourcesLoaded as TRUE, so we can start rendering.
    m_gameResourcesLoaded = true;
}
```

## <a name="createwindowsizedependentresource-method"></a>Метод CreateWindowSizeDependentResource

Методы CreateWindowSizeDependentResources вызываются при каждом изменении размера окна, ориентации, отрисовки в стереорежиме и разрешения. В образце игры производится обновление матрицы проекции в __ConstantBufferChangeOnResize__.

Ресурсы размера окна обновляются следующим образом. 
* Платформа приложения получает одно из нескольких возможных событий, указывающих на изменение состояния окна. 
* Затем главный игровой цикл оповещается об этом событии и вызывает __CreateWindowSizeDependentResources__ на экземпляре основного класса (__GameMain__), который затем вызывает реализацию __CreateWindowSizeDependentResources__ в классе обработчика игры (__GameRenderer__).
* Основная задача этого метода— гарантировать, что в результате изменения свойств окна визуальные объекты не станут беспорядочными или недействительными.

В этом примере игры количество вызовов метода совпадает с методом [__FinalizeCreateGameDeviceResources__](#finalizecreategamedeviceresources-method). Пошаговый анализ кода приводится в предыдущем разделе.

Изменение отрисовки размера окна наложения и элементов HUD рассматривается в разделе [Добавление пользовательского интерфейса](#tutorial--adding-a-user-interface).

```cpp
// Initializes view parameters when the window size changes.
void GameRenderer::CreateWindowSizeDependentResources()
{

    // Game HUD and overlay window size rendering adjustments are done here
    // but they'll be covered in the UI section instead.

    m_gameHud->CreateWindowSizeDependentResources();

    // ...

    auto d3dContext = m_deviceResources->GetD3DDeviceContext();
    // In Sample3DSceneRenderer::CreateWindowSizeDependentResources, we had:
    // Size outputSize = m_deviceResources->GetOutputSize();

    auto renderTargetSize = m_deviceResources->GetRenderTargetSize();

    // ...

    m_gameInfoOverlay->CreateWindowSizeDependentResources(m_gameInfoOverlaySize);

    if (m_game != nullptr)
    {
        // Similar operations as the last section of FinalizeCreateGameDeviceResources method
        m_game->GameCamera()->SetProjParams(
            XM_PI / 2, renderTargetSize.Width / renderTargetSize.Height,
            0.01f,
            100.0f
            );

        XMFLOAT4X4 orientation = m_deviceResources->GetOrientationTransform3D();

        ConstantBufferChangeOnResize changesOnResize;
        XMStoreFloat4x4(
            &changesOnResize.projection,
            XMMatrixMultiply(
                XMMatrixTranspose(m_game->GameCamera()->Projection()),
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
}
```

## <a name="next-steps"></a>Дальнейшие действия

Это базовый процесс реализации графической платформы отрисовки игры. Чем больше ваша игра, тем больше абстракций потребуется применить для обработки иерархии типов объектов и поведения анимации. Потребуется реализовать более сложные методы для загрузки и управления активами, такими как сетки и текстуры. Теперь посмотрим, как [добавить пользовательский интерфейс](tutorial--adding-a-user-interface.md).