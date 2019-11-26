---
title: Добавление визуального содержимого в образец Marble Maze
description: В этом документе показано, как игра Marble Maze использует Direct3D и Direct2D в среде приложения универсальной платформы Windows (UWP). Вы освоите приемы программирования и сможете адаптировать их для работы со своей игрой.
ms.assetid: 6e43422e-e1a1-b79e-2c4b-7d5b4fa88647
ms.date: 09/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, пример, directx, графика
ms.localizationpriority: medium
ms.openlocfilehash: 8e00842a03eecb91e22cedf987830b28e960efd0
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258543"
---
# <a name="adding-visual-content-to-the-marble-maze-sample"></a>Добавление визуального содержимого в образец Marble Maze




В этом документе показано, как игра Marble Maze использует Direct3D и Direct2D в среде приложения универсальной платформы Windows (UWP). Вы освоите приемы программирования и сможете адаптировать их для работы со своей игрой. Чтобы узнать, как визуальные компоненты игры вписываются в общую структуру приложения Marble Maze, ознакомьтесь с разделом [Структура приложения Marble Maze](marble-maze-application-structure.md).

В разработке визуальных аспектов Marble Maze мы следовали следующей базовой схеме.

1.  Создание платформы, которая инициализирует среду Direct3D и среду Direct2D.
2.  Используйте программы редактирования изображений и моделей для проектирования 2D-и трехмерных ресурсов, отображаемых в игре.
3.  Обеспечьте правильную загрузку 2D-и трехмерных ресурсов и их отображение в игре.
4.  Интеграция вершинных шейдеров и шейдеров пикселей, улучшающих качество отображения ресурсов игры.
5.  Интеграция игровой логики, в том числе анимации и пользовательского ввода.

Мы также посвящены добавлению трехмерных ресурсов, а затем к 2D-ресурсам. Например, сначала мы реализовали основную игровую логику, а затем добавили систему меню и таймер.

В процессе разработки нам приходилось неоднократно возвращаться к некоторым из этих этапов. Например, при внесении изменений в модели сетки и мраморов пришлось бы также изменить некоторые из кода шейдера, который поддерживает эти модели.

> [!NOTE]
> Пример кода, соответствующего этому документу, см. в [примере игры DirectX Marble Maze](https://github.com/microsoft/Windows-appsample-marble-maze).

 
В этом документе рассматриваются следующие основные моменты работы с DirectX и визуальным содержимым игры: инициализация графических библиотек DirectX, загрузка ресурсов сцены, обновление сцены и ее отрисовка.

-   Добавление игрового содержимого обычно выполняется в несколько этапов, которые часто приходится повторять. Разработчики игр часто сначала добавляют содержимое трехмерной игры, а затем добавляют 2D-содержимое.
-   Поддержка максимально широкого спектра графического оборудования позволит вам привлечь больше пользователей и предоставить им прекрасную игровую среду.
-   Четко разделяйте форматы времени разработки и времени выполнения. Организуйте ресурсы времени разработки так, чтобы добиться максимальной гибкости и обеспечить быстрое прохождение содержимого по этапам разработки. Сжимайте ресурсы и подбирайте для них такой формат, который обеспечит максимально эффективную загрузку и отрисовку во время выполнения.
-   Устройства Direct3D и Direct2D в приложении UWP создаются во многом так же, как и в классическом приложении Windows. Важным различием является способ привязки цепочки буферов к окну вывода.
-   Во время разработки игры убедитесь, что выбранный формат сетки поддерживает основные сценарии вашей игры. Например, если в игре происходят столкновения, необходимо иметь возможность получать из сетки данные о столкновениях.
-   Отделите игровую логику от логики отрисовки. Перед отрисовкой обновите все объекты сцены.
-   Обычно объекты трехмерной сцены рисуются, а затем все 2D-объекты, отображаемые перед сценой.
-   Синхронизируйте рисование с разверткой монитора, чтобы не затрачивать игровое время на рисование кадров, которые не будут отображаться на экране. *Пустой вертикальный* интервал — это время между завершением отрисовки одного кадра на монитор и началом следующего кадра.

## <a name="getting-started-with-directx-graphics"></a>Начало работы с графикой DirectX


Когда мы планировали игру по лабиринту мрамора универсальная платформа Windows (UWP) C++ , мы выбрали и Direct3D 11,1, так как они отлично подходят для создания трехмерных игр, требующих максимального контроля над отрисовкой и высокой производительностью. DirectX 11.1 поддерживает оборудование уровней от DirectX 9 до DirectX 11 и поэтому позволяет более эффективно расширить пользовательскую базу, поскольку код не нужно переписывать для каждой из предыдущих версий DirectX.

Лабиринт мрамора использует Direct3D 11,1 для визуализации ресурсов трехмерной игры, а именно для мрамора и лабиринта. Лабиринты мраморов также используют Direct2D, DirectWrite и компонент Windows Imaging Component (WIC) для рисования ресурсов 2D Game, таких как меню и таймер.

Разработку игры необходимо спланировать. Если вы не знакомы с графикой DirectX, рекомендуем ознакомиться с разрешениями [DirectX: Приступая к работе](directx-getting-started.md) , чтобы ознакомиться с основными принципами создания игры DirectX UWP. По мере прочтения этого документа и работы с исходным кодом лабиринта мрамора вы можете обратиться к следующим ресурсам, чтобы получить более подробные сведения о графике DirectX.

-   [Графическая графика Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11): описывает мощный API трехмерной графики Direct3D 11 для отрисовки трехмерной геометрии на платформе Windows.
-   [Direct2D](https://docs.microsoft.com/windows/desktop/Direct2D/direct2d-portal): описывает Direct2D, интерфейс API двухмерной графики с аппаратным ускорением, обеспечивающий высокую производительность и высококачественную визуализацию для двухмерной геометрии, точечных рисунков и текста.
-   [DirectWrite](https://docs.microsoft.com/windows/desktop/DirectWrite/direct-write-portal): Описывает DirectWrite, поддерживающий визуализацию текста высокого качества.
-   [Компонент Windows Imaging Component](https://docs.microsoft.com/windows/desktop/wic/-wic-lh): описывает WIC — расширяемую платформу, которая предоставляет низкоуровневые API для цифровых изображений.

### <a name="feature-levels"></a>Уровни компонентов

В Direct3D 11 появился парадигма с именем *Feature Levels*. Уровень компонентов — это заданный набор функций GPU. Уровни компонентов позволяют адаптировать игру для работы на оборудовании Direct3D предыдущих версий. Игра Marble Maze поддерживает уровень компонентов 9.1, поскольку ей не требуются расширенные возможности более высоких уровней. Рекомендуется поддерживать максимально широкий спектр оборудования и масштабировать игровое содержимое таким образом, чтобы и пользователи с компьютерами высшего класса, и пользователи с более скромным оборудованием получили прекрасную игровую среду. Дополнительные сведения об уровнях компонентов см. в разделе [Direct3D 11 на оборудовании прежних версий](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-devices-downlevel).

## <a name="initializing-direct3d-and-direct2d"></a>Инициализация Direct3D и Direct2D


Устройство представляет видеоадаптер. Устройства Direct3D и Direct2D в приложении UWP создаются во многом так же, как и в классическом приложении Windows. Основным различием является подключение цепочки буферов Direct3D к системе управления окнами.

Класс **DeviceResources** служит основой для управления Direct3D и Direct2D. Этот класс обрабатывает общую инфраструктуру, а не ресурсы для конкретных игр. Лабиринт мрамора определяет класс **марблемаземаин** для управления ресурсами, предназначенными для игр, которые имеют ссылку на объект **DeviceResources** , чтобы предоставить ему доступ к Direct3D и Direct2D.

Во время инициализации Конструктор **DeviceResources** создает ресурсы, независимые от устройства, а также устройства Direct3D и Direct2D.

```cpp
// Initialize the Direct3D resources required to run. 
DX::DeviceResources::DeviceResources() :
    m_screenViewport(),
    m_d3dFeatureLevel(D3D_FEATURE_LEVEL_9_1),
    m_d3dRenderTargetSize(),
    m_outputSize(),
    m_logicalSize(),
    m_nativeOrientation(DisplayOrientations::None),
    m_currentOrientation(DisplayOrientations::None),
    m_dpi(-1.0f),
    m_deviceNotify(nullptr)
{
    CreateDeviceIndependentResources();
    CreateDeviceResources();
}
```

В классе **DeviceResources** эти функции разделены, что упрощает адаптацию к изменениям среды. Например, когда изменяется размер окна, вызывается метод **CreateWindowSizeDependentResources**.

###  <a name="initializing-the-direct2d-directwrite-and-wic-factories"></a>Инициализация фабрик Direct2D, DirectWrite и WIC

Метод **DeviceResources::CreateDeviceIndependentResources** создает фабрики для Direct2D, DirectWrite и WIC. В графике DirectX фабрики служат отправными точками создания графических ресурсов. Лабиринт мрамора указывает **тип D2D1\_фабрики\_\_один\_потоковый** , так как он выполняет всю прорисовку в основном потоке.

```cpp
// These are the resources required independent of hardware. 
void DX::DeviceResources::CreateDeviceIndependentResources()
{
    // Initialize Direct2D resources.
    D2D1_FACTORY_OPTIONS options;
    ZeroMemory(&options, sizeof(D2D1_FACTORY_OPTIONS));

#if defined(_DEBUG)
    // If the project is in a debug build, enable Direct2D debugging via SDK Layers.
    options.debugLevel = D2D1_DEBUG_LEVEL_INFORMATION;
#endif

    // Initialize the Direct2D Factory.
    DX::ThrowIfFailed(
        D2D1CreateFactory(
            D2D1_FACTORY_TYPE_SINGLE_THREADED,
            __uuidof(ID2D1Factory2),
            &options,
            &m_d2dFactory
            )
        );

    // Initialize the DirectWrite Factory.
    DX::ThrowIfFailed(
        DWriteCreateFactory(
            DWRITE_FACTORY_TYPE_SHARED,
            __uuidof(IDWriteFactory2),
            &m_dwriteFactory
            )
        );

    // Initialize the Windows Imaging Component (WIC) Factory.
    DX::ThrowIfFailed(
        CoCreateInstance(
            CLSID_WICImagingFactory2,
            nullptr,
            CLSCTX_INPROC_SERVER,
            IID_PPV_ARGS(&m_wicFactory)
            )
        );
}
```

###  <a name="creating-the-direct3d-and-direct2d-devices"></a>Создание устройств Direct3D и Direct2D

Метод **DeviceResources:: креатедевицересаурцес** вызывает [D3D11CreateDevice](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) , чтобы создать объект устройства, представляющий адаптер дисплея Direct3D. Поскольку Мраморный лабиринт поддерживает функции уровня 9,1 и выше, метод **DeviceResources:: креатедевицересаурцес** задает уровни 9,1 – 11,1 в массиве **феатурелевелс** . Direct3D по порядку проходит по этому списку и задает для приложения первый доступный уровень компонентов. Таким образом, **компонент D3D\_\_** записи массива уровней перечисляются по убыванию, чтобы приложение получило максимальный уровень доступности. Метод **DeviceResources::CreateDeviceResources** получает устройство Direct3D 11.1, запрашивая устройство Direct3D 11, возвращаемое методом **D3D11CreateDevice**.

```cpp
// This flag adds support for surfaces with a different color channel ordering
// than the API default. It is required for compatibility with Direct2D.
UINT creationFlags = D3D11_CREATE_DEVICE_BGRA_SUPPORT;

#if defined(_DEBUG)
    if (DX::SdkLayersAvailable())
    {
        // If the project is in a debug build, enable debugging via SDK Layers 
        // with this flag.
        creationFlags |= D3D11_CREATE_DEVICE_DEBUG;
    }
#endif

// This array defines the set of DirectX hardware feature levels this app will support.
// Note the ordering should be preserved.
// Don't forget to declare your application's minimum required feature level in its
// description.  All applications are assumed to support 9.1 unless otherwise stated.
D3D_FEATURE_LEVEL featureLevels[] =
{
    D3D_FEATURE_LEVEL_11_1,
    D3D_FEATURE_LEVEL_11_0,
    D3D_FEATURE_LEVEL_10_1,
    D3D_FEATURE_LEVEL_10_0,
    D3D_FEATURE_LEVEL_9_3,
    D3D_FEATURE_LEVEL_9_2,
    D3D_FEATURE_LEVEL_9_1
};

// Create the Direct3D 11 API device object and a corresponding context.
ComPtr<ID3D11Device> device;
ComPtr<ID3D11DeviceContext> context;

HRESULT hr = D3D11CreateDevice(
    nullptr,                    // Specify nullptr to use the default adapter.
    D3D_DRIVER_TYPE_HARDWARE,   // Create a device using the hardware graphics driver.
    0,                          // Should be 0 unless the driver is D3D_DRIVER_TYPE_SOFTWARE.
    creationFlags,              // Set debug and Direct2D compatibility flags.
    featureLevels,              // List of feature levels this app can support.
    ARRAYSIZE(featureLevels),   // Size of the list above.
    D3D11_SDK_VERSION,          // Always set this to D3D11_SDK_VERSION for UWP apps.
    &device,                    // Returns the Direct3D device created.
    &m_d3dFeatureLevel,         // Returns feature level of device created.
    &context                    // Returns the device immediate context.
    );

if (FAILED(hr))
{
    // If the initialization fails, fall back to the WARP device.
    // For more information on WARP, see:
    // https://go.microsoft.com/fwlink/?LinkId=286690
    DX::ThrowIfFailed(
        D3D11CreateDevice(
            nullptr,
            D3D_DRIVER_TYPE_WARP, // Create a WARP device instead of a hardware device.
            0,
            creationFlags,
            featureLevels,
            ARRAYSIZE(featureLevels),
            D3D11_SDK_VERSION,
            &device,
            &m_d3dFeatureLevel,
            &context
            )
        );
}

// Store pointers to the Direct3D 11.1 API device and immediate context.
DX::ThrowIfFailed(
    device.As(&m_d3dDevice)
    );

DX::ThrowIfFailed(
    context.As(&m_d3dContext)
    );
```

Метод **DeviceResources::CreateDeviceResources** затем создает устройство Direct2D. Direct2D использует инфраструктуру Microsoft DirectX Graphics Infrastructure (DXGI) для взаимодействия с Direct3D. DXGI позволяет графическим средам выполнения совместно использовать ресурсы видеопамяти. Marble Maze использует базовое устройство DXGI из устройства Direct3D, чтобы создать устройство Direct2D из фабрики Direct2D.

```cpp
// Create the Direct2D device object and a corresponding context.
ComPtr<IDXGIDevice3> dxgiDevice;
DX::ThrowIfFailed(
    m_d3dDevice.As(&dxgiDevice)
    );

DX::ThrowIfFailed(
    m_d2dFactory->CreateDevice(dxgiDevice.Get(), &m_d2dDevice)
    );

DX::ThrowIfFailed(
    m_d2dDevice->CreateDeviceContext(
        D2D1_DEVICE_CONTEXT_OPTIONS_NONE,
        &m_d2dContext
        )
    );
```

Дополнительные сведения о DXGI и взаимодействии между Direct2D и Direct3D см. в разделах [Обзор DXGI](https://docs.microsoft.com/windows/desktop/direct3ddxgi/d3d10-graphics-programming-guide-dxgi) и [Обзор взаимодействия Direct2D и Direct3D](https://docs.microsoft.com/windows/desktop/Direct2D/direct2d-and-direct3d-interoperation-overview).

### <a name="associating-direct3d-with-the-view"></a>Привязка Direct3D к представлению

Метод **DeviceResources::CreateWindowSizeDependentResources** создает графические ресурсы, которые зависят от заданного размера окна, — например, цепочку буферов и целевые объекты прорисовки Direct3D и Direct2D. Важным отличием приложений UWP на DirectX от классических приложений является способ привязки цепочки буферов к окну вывода. Цепочка буферов отвечает за отображение буфера, в котором устройство выполняет отрисовку, на мониторе. [Структура приложения лабиринта на мраморе](marble-maze-application-structure.md) описывает, как система окон для приложения UWP отличается от классического приложения. Так как приложение UWP не работает с объектами [HWND](https://docs.microsoft.com/windows/desktop/WinProg/windows-data-types) , для связывания вывода устройства с представлением лабиринт должен использовать метод [IDXGIFactory2:: креатесвапчаинфоркоревиндов](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgifactory2-createswapchainforcorewindow) . В следующем примере показана часть метода **DeviceResources::CreateWindowSizeDependentResources**, которая создает цепочку буферов.

```cpp
// Obtain the final swap chain for this window from the DXGI factory.
DX::ThrowIfFailed(
    dxgiFactory->CreateSwapChainForCoreWindow(
        m_d3dDevice.Get(),
        reinterpret_cast<IUnknown*>(m_window.Get()),
        &swapChainDesc,
        nullptr,
        &m_swapChain
        )
    );
```

Чтобы ограничить энергопотребление, что важно для устройств с питанием от аккумулятора, например ноутбуков и планшетов, метод **DeviceResources:: креатевиндовсизедепендентресаурцес** вызывает метод [IDXGIDevice1:: сетмаксимумфрамелатенци](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgidevice1-setmaximumframelatency) , чтобы убедиться, что игра отображается только после вертикального пустого поля. Синхронизация с помощью вертикального пробела описывается более подробно в разделе, посвященном [демонстрации сцены](#presenting-the-scene) в этом документе.

```cpp
// Ensure that DXGI does not queue more than one frame at a time. This both 
// reduces latency and ensures that the application will only render after each
// VSync, minimizing power consumption.
DX::ThrowIfFailed(
    dxgiDevice->SetMaximumFrameLatency(1)
    );
```

Метод **DeviceResources::CreateWindowSizeDependentResources** инициализирует графические ресурсы таким образом, чтобы обеспечить работоспособность большинства игр.

> [!NOTE]
> Термин *представление* имеет другое значение в среда выполнения Windows, чем в Direct3D. В среде выполнения Windows представление — это набор параметров пользовательского интерфейса для приложения, в том числе область отображения и возможные реакции на ввод, а также поток, используемый для обработки. При создании представления задаются нужные параметры и конфигурация. Процедура настройки представления приложения описана в разделе [Структура приложения Marble Maze](marble-maze-application-structure.md).
> В Direct3D термин "представление" имеет несколько значений. Представление ресурсов определяет подресурсы, к которым ресурс может получить доступ. Например, если объект текстуры связан с представлением ресурса шейдера, то затем шейдер может получить доступ к текстуре. Преимуществом представления ресурса является возможность различной интерпретации данных на различных участках конвейера отрисовки. Дополнительные сведения о представлениях ресурсов см. в разделе [представления ресурсов](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-resources-intro).
> Термин "представление" в контексте преобразования представления или матрицы преобразования представления означает расположение и ориентацию камеры. При преобразовании представления объекты мира перемещаются относительно положения и ориентации камеры. Дополнительные сведения о преобразованиях представлений см. в разделе о [преобразовании представления (Direct3D 9)](https://docs.microsoft.com/windows/desktop/direct3d9/view-transform). Использование представлений ресурсов и матричных представлений в Marble подробно описано в этом разделе.

 

## <a name="loading-scene-resources"></a>Загрузка ресурсов сцены


Лабиринт мрамора использует класс **басиклоадер** , объявленный в **басиклоадер. h**, для загрузки текстур и шейдеров. Лабиринт мрамора использует класс **сдкмеш** для загрузки трехмерных сеток для лабиринта и мрамора.

Чтобы обеспечить хороший отклик приложения, Marble Maze загружает ресурсы сцены асинхронно, в фоновом режиме. Пока ресурсы загружаются в фоновом режиме, игра может отвечать на события окон. Этот процесс подробно описан в разделе [Загрузка ресурсов игры в фоновом режиме](marble-maze-application-structure.md#loading-game-assets-in-the-background) данного руководства.

###  <a name="loading-the-2d-overlay-and-user-interface"></a>Загрузка 2D-оверлея и пользовательского интерфейса

В Marble Maze наложение — это изображение, которое отображается в верхней части экрана. Наложение всегда располагается перед сценой. В лабиринте мрамора наложение содержит эмблему Windows и текстовую строку **Пример игры Microsoft DirectX мрамор лабиринта**. Управление наложением выполняется классом **самплеоверлай** , который определен в **самплеоверлай. h**. Наложение используется в составе образцов Direct3D, но можно адаптировать этот код для любого изображения, находящегося перед сценой.

Одним из важных аспектов наложения является то, что, поскольку его содержимое не изменяется, класс **самплеоверлай** рисует или кэширует содержимое объекта [ID2D1Bitmap1](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1bitmap1) во время инициализации. Во время рисования классу **SampleOverlay** остается только нарисовать на экране растровое изображение. Это позволяет не выполнять в каждом кадре ресурсоемкие подпрограммы, например для рисования текста.

Пользовательский интерфейс состоит из двумерных компонентов, таких как меню и головные экраны (Худс), которые отображаются перед сценой. В Marble Maze определяются следующие элементы пользовательского интерфейса:

-   пункты меню, которые позволяют пользователю начать игру или просмотреть таблицу результатов,
-   таймер, отсчитывающий время (три секунды) до начала игры,
-   таймер, который показывает истекшее время игры,
-   таблица, в которой показаны лучшие значения времени прохождения,
-   Текст, чтение которого **приостановлено** при приостановке игры.

Лабиринт мрамора определяет элементы пользовательского интерфейса, относящиеся к игре, в **усеринтерфаце. h**. В Marble Maze класс **ElementBase** определяется в качестве базового типа для всех элементов пользовательского интерфейса. В классе **ElementBase** определены такие атрибуты элемента пользовательского интерфейса, как размер, положение, выравнивание и видимость. Он также управляет обновлением и отрисовкой элементов.

```cpp
class ElementBase
{
public:
    virtual void Initialize() { }
    virtual void Update(float timeTotal, float timeDelta) { }
    virtual void Render() { }

    void SetAlignment(AlignType horizontal, AlignType vertical);
    virtual void SetContainer(const D2D1_RECT_F& container);
    void SetVisible(bool visible);

    D2D1_RECT_F GetBounds();

    bool IsVisible() const { return m_visible; }

protected:
    ElementBase();

    virtual void CalculateSize() { }

    Alignment       m_alignment;
    D2D1_RECT_F     m_container;
    D2D1_SIZE_F     m_size;
    bool            m_visible;
};
```

Наличие общего базового класса для элементов пользовательского интерфейса позволяет классу **UserInterface**, который управляет интерфейсом, просто хранить коллекцию объектов **ElementBase**. Это упрощает управление интерфейсом и обеспечивает возможность повторного использования диспетчера пользовательского интерфейса. Marble Maze определяет типы, производные от **ElementBase**, которые реализуют реакцию на игровые события. Например, **HighScoreTable** определяет порядок работы таблицы результатов. Чтобы узнать больше об этих типах, обратитесь к исходному коду.

> [!NOTE]
> Так как XAML позволяет более легко создавать сложные пользовательские интерфейсы, такие как те, что находятся в играх моделирования и стратегии, рассмотрите возможность использования XAML для определения пользовательского интерфейса. Сведения о том, как разрабатывать пользовательский интерфейс в XAML в игре DirectX UWP, см. в разделе [расширение игры с](tutorial-resources.md)помощью примера "игра DirectX 3D".

 

###  <a name="loading-shaders"></a>Загрузка шейдеров

В Marble Maze используется метод **BasicLoader::LoadShader** для загрузки шейдера из файла.

В современных играх шейдеры являются неотъемлемой частью программирования GPU. Почти вся обработка трехмерной графики осуществляется с помощью шейдеров, будь то преобразование модели и освещение сцены или более сложная обработка геометрии, от выборки символов до тесселяции. Дополнительные сведения о модели программирования шейдеров см. в разделе [HLSL](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl).

В Marble Maze используются вершинные построители текстуры и шейдеры пикселей. Вершинный шейдер всегда работает с одной входной вершиной, и в результате дает одну вершину. Шейдер пикселей принимает числовые значения, данные текстуры, интерполированные значения по вершинам и другие данные, а в результате дает цвет пикселя. Поскольку шейдер преобразует элементы по одному, графическое оборудование с несколькими шейдерными конвейерами может параллельно обрабатывать наборы элементов. Число параллельных конвейеров, доступных GPU, может значительно превышать число, доступное ЦП. Поэтому даже простые шейдеры могут значительно повысить пропускную способность.

Метод **марблемаземаин:: лоаддеферредресаурцес** загружает один шейдер вершин и один построитель текстуры после загрузки наложения. Версии этих шейдеров времени разработки определяются в **басиквертексшадер. HLSL** и **басикпикселшадер. HLSL**соответственно. Marble Maze применяет эти шейдеры к шарику и лабиринту на этапе отрисовки.

В проект Marble Maze входят обе версии файлов шейдеров (и формат HLSL для времени разработки, и формат CSO для времени выполнения). Во время сборки Visual Studio использует компилятор эффектов fxc.exe для компиляции HLSL-файла исходного кода в двоичный CSO-файл шейдера. Дополнительные сведения о компиляторе эффектов см. в разделе о [средстве компиляции эффектов](https://docs.microsoft.com/windows/desktop/direct3dtools/fxc).

Вершинный шейдер использует предоставленную модель, представление и матрицы проекции для преобразования входной геометрии. Данные о положении из входной геометрии преобразуются и выводятся дважды: один раз в пространстве экрана, что необходимо для отрисовки, а второй раз в абсолютном пространстве, что позволяет шейдеру пикселей выполнить расчет освещения. Вектор нормали к поверхности преобразуется в абсолютное пространство, которое также используется шейдером пикселей для освещения. Координаты текстуры передаются в шейдер пикселей без изменения.

```hlsl
sPSInput main(sVSInput input)
{
    sPSInput output;
    float4 temp = float4(input.pos, 1.0f);
    temp = mul(temp, model);
    output.worldPos = temp.xyz / temp.w;
    temp = mul(temp, view);
    temp = mul(temp, projection);
    output.pos = temp;
    output.tex = input.tex;
    output.norm = mul(float4(input.norm, 0.0f), model).xyz;
    return output;
}
```

Входными данными шейдера пикселей служат выходные данные вершинного шейдера. Этот шейдер выполняет расчет освещения с плавными границами, имитируя свет прожектора, парящего над лабиринтом и двигающегося вместе с шариком. Наиболее яркое освещены поверхности, которые непосредственно обращены к свету. По мере того, как нормаль к поверхности становится перпендикулярной свету, диффузная составляющая освещения уменьшается до нуля, а когда нормаль отворачивается от света, уменьшается освещение окружения. Точки, расположенные ближе к шарику (то есть ближе к центру прожектора), освещаются более ярко. Для точек, находящихся под шариком, освещение модулируется, создавая эффект мягкой тени. В реальном мире объект, подобный белому шарику, будет диффузно отражать свет прожектора на другие объекты сцены. Этот эффект реализуется для поверхностей, которые находятся с яркой стороны шарика. Дополнительные факторы освещения рассчитываются с учетом угла и расстояния до шарика. Итоговый цвет пикселя представляет собой сочетание выборки текстуры с расчетами освещения.

```hlsl
float4 main(sPSInput input) : SV_TARGET
{
    float3 lightDirection = float3(0, 0, -1);
    float3 ambientColor = float3(0.43, 0.31, 0.24);
    float3 lightColor = 1 - ambientColor;
    float spotRadius = 50;

    // Basic ambient (Ka) and diffuse (Kd) lighting from above.
    float3 N = normalize(input.norm);
    float NdotL = dot(N, lightDirection);
    float Ka = saturate(NdotL + 1);
    float Kd = saturate(NdotL);

    // Spotlight.
    float3 vec = input.worldPos - marblePosition;
    float dist2D = sqrt(dot(vec.xy, vec.xy));
    Kd = Kd * saturate(spotRadius / dist2D);

    // Shadowing from ball.
    if (input.worldPos.z > marblePosition.z)
        Kd = Kd * saturate(dist2D / (marbleRadius * 1.5));

    // Diffuse reflection of light off ball.
    float dist3D = sqrt(dot(vec, vec));
    float3 V = normalize(vec);
    Kd += saturate(dot(-V, N)) * saturate(dot(V, lightDirection))
        * saturate(marbleRadius / dist3D);

    // Final composite.
    float4 diffuseTexture = Texture.Sample(Sampler, input.tex);
    float3 color = diffuseTexture.rgb * ((ambientColor * Ka) + (lightColor * Kd));
    return float4(color * lightStrength, diffuseTexture.a);
}
```

> [!WARNING]
> Скомпилированный построитель текстуры содержит 32 арифметических инструкций и одну инструкцию текстуры. Этот шейдер должен обеспечивать высокую производительность на настольных компьютерах и планшетах высшего класса. Однако на менее мощном компьютере, где обработка этого шейдера невозможна, все же можно добиться частоты кадров, обеспечивающей интерактивность. Изучите типичное оборудование целевой аудитории своей игры и разрабатывайте шейдеры с учетом возможностей этого оборудования.

 

Метод **марблемаземаин:: лоаддеферредресаурцес** использует метод **Басиклоадер:: лоадшадер** для загрузки шейдеров. В следующем примере загружается вершинный шейдер. Формат времени выполнения для этого шейдера — **басиквертексшадер. cso**. Переменная-член **m\_вертексшадер** является объектом [ID3D11VertexShader](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11vertexshader) .

```cpp
BasicLoader^ loader = ref new BasicLoader(m_deviceResources->GetD3DDevice());

D3D11_INPUT_ELEMENT_DESC layoutDesc [] =
{
    { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "NORMAL", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "TEXCOORD", 0, DXGI_FORMAT_R32G32_FLOAT, 0, 24, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "TANGENT", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 32, D3D11_INPUT_PER_VERTEX_DATA, 0 },
};
m_vertexStride = 44; // must set this to match the size of layoutDesc above

Platform::String^ vertexShaderName = L"BasicVertexShader.cso";
loader->LoadShader(
    vertexShaderName,
    layoutDesc,
    ARRAYSIZE(layoutDesc),
    &m_vertexShader,
    &m_inputLayout
    );
```

Переменная-член **m\_инпутлайаут** является объектом [ID3D11InputLayout](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11inputlayout) . В объекте макета входных данных инкапсулируется входное состояние этапа сборки входных данных (IA). Одной из задач этапа IA является повышение эффективности шейдеров за счет использования значений, созданных системой (они также называются *семантикой*), для обработки исключительно еще не обработанных примитивов и вершин.

Используйте метод [ID3D11Device:: креатеинпутлайаут](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createinputlayout) , чтобы создать входной макет из массива описаний входного элемента. Этот массив содержит один или несколько входных элементов. Каждый входной элемент описывает один элемент vertex-data из одного буфера вершин. Весь набор описаний input-element описывает все элементы vertex-data из всех буферов вершин, которые будут привязаны на этапе IA. 

**лайаутдеск** в приведенном выше фрагменте кода показывает описание макета, которое используется для лабиринта мрамора. Описание макета описывает буфер вершин, который содержит четыре элемента vertex-data. Важными частями каждого элемента массива являются имя семантики, формат данных и смещение в байтах. Например, элемент **POSITION** задает положение вершины в пространстве объектов. Он начинается со смещения в 0 байт и содержит три компонента с плавающей запятой (всего 12 байт). Элемент **NORMAL** задает нормальный вектор. Он начинается со смещения в 12 байт, поскольку идет в макете сразу после элемента **POSITION**, которому необходимо 12 байт. Элемент **NORMAL** содержит четырехкомпонентное 32-разрядное целое число без знака.

Сравним макет входных данных со структурой **sVSInput**, которая определена вершинным шейдером, как показано в следующем примере. Структура **sVSInput** определяет элементы **POSITION**, **NORMAL** и **TEXCOORD0**. Во время выполнения DirectX каждый элемент в макете сопоставляется с входной структурой, которая определяется шейдером.

```hlsl
struct sVSInput
{
    float3 pos : POSITION;
    float3 norm : NORMAL;
    float2 tex : TEXCOORD0;
};

struct sPSInput
{
    float4 pos : SV_POSITION;
    float3 norm : NORMAL;
    float2 tex : TEXCOORD0;
    float3 worldPos : TEXCOORD1;
};

sPSInput main(sVSInput input)
{
    sPSInput output;
    float4 temp = float4(input.pos, 1.0f);
    temp = mul(temp, model);
    output.worldPos = temp.xyz / temp.w;
    temp = mul(temp, view);
    temp = mul(temp, projection);
    output.pos = temp;
    output.tex = input.tex;
    output.norm = mul(float4(input.norm, 0.0f), model).xyz;
    return output;
}
```

В документе [Семантики](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-semantics) подробно описана каждая из доступных семантик.

> [!NOTE]
> В макете можно указать дополнительные компоненты, которые не используются, чтобы разрешить нескольким шейдерам использовать один и тот же макет. Например, элемент **TANGENT** не используется шейдером. Можно использовать элемент **TANGENT**, если хотите проверить какие-либо приемы, например сопоставление нормали. С помощью сопоставления нормали, которое также называют отображением элементов рельефа, вы можете создавать эффект выпуклостей на поверхностях объектов. Дополнительные сведения см. в статье об [отображении элементов рельефа (Direct3D 9)](https://docs.microsoft.com/windows/desktop/direct3d9/bump-mapping).

 

Дополнительные сведения о стадии входной сборки см. в разделе [этап ввода-ассемблера](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-input-assembler-stage) и [Начало работы с помощью этапа ассемблера ввода](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-input-assembler-stage-getting-started).

Процесс использования вершинных шейдеров и построителей текстуры для отрисовки сцены описан в разделе [Отрисовка сцены](#rendering-the-scene) ниже в этом документе.

### <a name="creating-the-constant-buffer"></a>Создание буферов констант

Буфер Direct3D группирует коллекцию данных. Буфер констант является типом буфера, который можно использовать, чтобы передавать данные в шейдеры. В Marble Maze используется буфер констант, чтобы удерживать представление модели (или мира), а также матрицы проекции для активного объекта сцены.

В следующем примере показано, как метод **марблемаземаин:: лоаддеферредресаурцес** создает буфер констант, который впоследствии будет содержать данные матрицы. В примере создается **буфер\_D3D11\_DESC** Structure, использующий **D3D11\_BIND\_Constant\_флаг buffer** , чтобы указать использование в качестве буфера константы. Затем этот пример передает эту структуру методу [ID3D11Device:: CreateBuffer](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createbuffer) . Переменная **константбуффер m\_** является объектом [ID3D11Buffer](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11buffer) .

```cpp
// Create the constant buffer for updating model and camera data.
D3D11_BUFFER_DESC constantBufferDesc = {0};

// Multiple of 16 bytes
constantBufferDesc.ByteWidth = ((sizeof(ConstantBuffer) + 15) / 16) * 16;

constantBufferDesc.Usage               = D3D11_USAGE_DEFAULT;
constantBufferDesc.BindFlags           = D3D11_BIND_CONSTANT_BUFFER;
constantBufferDesc.CPUAccessFlags      = 0;
constantBufferDesc.MiscFlags           = 0;

// This will not be used as a structured buffer, so this parameter is ignored.
constantBufferDesc.StructureByteStride = 0;

DX::ThrowIfFailed(
    m_deviceResources->GetD3DDevice()->CreateBuffer(
        &constantBufferDesc,
        nullptr,    // leave the buffer uninitialized
        &m_constantBuffer
        )
    );
```

Метод **марблемаземаин:: Update** впоследствии обновляет объекты **константбуффер** , один для лабиринта и один для мрамора. Затем метод **марблемаземаин:: Render** привязывает каждый объект **константбуффер** к буферу констант перед отрисовкой каждого объекта. В следующем примере показана структура **константбуффер** , которая находится в **марблемаземаин. h**.

```cpp
// Describes the constant buffer that draws the meshes.
struct ConstantBuffer
{
    XMFLOAT4X4 model;
    XMFLOAT4X4 view;
    XMFLOAT4X4 projection;

    XMFLOAT3 marblePosition;
    float marbleRadius;
    float lightStrength;
};
```

Чтобы лучше понять, как постоянные буферы сопоставляются с кодом шейдера, Сравните структуру **константбуффер** в **марблемаземаин. h** с буфером констант **константбуффер** , который определен в шейдере вершин в **басиквертексшадер. HLSL**:

```hlsl
cbuffer ConstantBuffer : register(b0)
{
    matrix model;
    matrix view;
    matrix projection;
    float3 marblePosition;
    float marbleRadius;
    float lightStrength;
};
```

макет структуры **ConstantBuffer** соответствует объекту **cbuffer**. Переменная **cbuffer** указывает регистр b0. Это означает, что данные буфера констант сохраняются в регистре 0. Метод **марблемаземаин:: Render** задает регистр 0 при активации буфера констант. Этот процесс подробно описан ниже в данном документе.

Дополнительные сведения о буферах констант см. во [вводной статье о буферах Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-intro). Дополнительные сведения о ключевом слове Register см. в разделе [Регистрация](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-variable-register).

###  <a name="loading-meshes"></a>Загрузка mesh-объектов

Marble Maze использует SDK-Mesh как формат времени выполнения, поскольку этот формат предоставляет простой способ загрузки данных сетки для образцов приложений. Для использования в производстве вы должны применять формат сетки, отвечающий конкретным требованиям вашей игры.

Метод **марблемаземаин:: лоаддеферредресаурцес** загружает данные сетки после загрузки вершин и шейдеров пикселей. Сетка является коллекцией данных вершин, в которые часто включают такую информацию, как положения, данные нормали, цвета, материалы и координаты текстуры. Сетки обычно создаются в трехмерном программном обеспечении и хранятся в файлах, отделяющих от кода приложения. Шарик и лабиринт могут служить двумя примерами сеток, которые используются в игре.

В Marble Maze для управления сетками используется класс **SDKMesh**. Этот класс объявлен в **сдкмеш. h**. **SDKMesh** предоставляет методы для загрузки, отрисовки и уничтожения данных сетки.

> [!IMPORTANT]
> Лабиринт мрамора использует формат сетки SDK и предоставляет класс **сдкмеш** только для иллюстрации. Хотя формат SDK-Mesh удобен для обучения и создания прототипов, это очень простой формат, который может не отвечать требованиям большинства игр. Мы рекомендуем использовать формат сетки, отвечающий конкретным требованиям вашей игры.

 

В следующем примере показано, как метод **марблемаземаин:: лоаддеферредресаурцес** использует метод **Сдкмеш:: Create** для загрузки данных сетки для лабиринта и шарика.

```cpp
// Load the meshes.
DX::ThrowIfFailed(
    m_mazeMesh.Create(
        m_deviceResources->GetD3DDevice(),
        L"Media\\Models\\maze1.sdkmesh",
        false
        )
    );

DX::ThrowIfFailed(
    m_marbleMesh.Create(
        m_deviceResources->GetD3DDevice(),
        L"Media\\Models\\marble2.sdkmesh",
        false
        )
    );
```

###  <a name="loading-collision-data"></a>Загрузка данных столкновения

Хотя этот раздел не посвящен тому, как в Marble Maze реализуется моделирование движения шарика по лабиринту, отметим, что геометрия сетки для физической системы читается при загрузке сеток.

```cpp
// Extract mesh geometry for the physics system.
DX::ThrowIfFailed(
    ExtractTrianglesFromMesh(
        m_mazeMesh,
        "Mesh_walls",
        m_collision.m_wallTriList
        )
    );

DX::ThrowIfFailed(
    ExtractTrianglesFromMesh(
        m_mazeMesh,
        "Mesh_Floor",
        m_collision.m_groundTriList
        )
    );

DX::ThrowIfFailed(
    ExtractTrianglesFromMesh(
        m_mazeMesh,
        "Mesh_floorSides",
        m_collision.m_floorTriList
        )
    );

m_physics.SetCollision(&m_collision);
float radius = m_marbleMesh.GetMeshBoundingBoxExtents(0).x / 2;
m_physics.SetRadius(radius);
```

Способ загрузки данных о конфликтах во многом зависит от используемого формата времени выполнения. Дополнительные сведения о том, как лабиринт в мраморе загружает геометрию конфликтов из файла в сети SDK, см. в описании метода **марблемаземаин:: екстракттрианглесфроммеш** в исходном коде.

## <a name="updating-game-state"></a>Обновление состояния игры


Marble Maze отделяет логику игры от логики отрисовки, предварительно обновляя все объекты сцены перед их отрисовкой.

[Структура приложения лабиринта на мраморе](marble-maze-application-structure.md) описывает основной цикл игры. Обновление сцены, являющееся частью цикла игры, имеет место после обработки событий Windows и входных данных, но до отрисовки сцены. Метод **марблемаземаин:: Update** обрабатывает обновление пользовательского интерфейса и игры.

### <a name="updating-the-user-interface"></a>Обновление пользовательского интерфейса

Метод **марблемаземаин:: Update** вызывает метод **Усеринтерфаце:: Update** для обновления состояния пользовательского интерфейса.

```cpp
UserInterface::GetInstance().Update(
    static_cast<float>(m_timer.GetTotalSeconds()), 
    static_cast<float>(m_timer.GetElapsedSeconds()));
```

Метод **UserInterface::Update** обновляет каждый элемент в коллекции элементов пользовательского интерфейса.

```cpp
void UserInterface::Update(float timeTotal, float timeDelta)
{
    for (auto iter = m_elements.begin(); iter != m_elements.end(); ++iter)
    {
        (*iter)->Update(timeTotal, timeDelta);
    }
}
```

Классы, производные от **елементбасе** (определенные в **усеринтерфаце. h**), реализуют метод **Update** для выполнения определенных поведений. Например, метод **StopwatchTimer::Update** обновляет затраченное время на предоставленную величину и обновляет текст, который отображается после этого.

```cpp
void StopwatchTimer::Update(float timeTotal, float timeDelta)
{
    if (m_active)
    {
        m_elapsedTime += timeDelta;

        WCHAR buffer[16];
        GetFormattedTime(buffer);
        SetText(buffer);
    }

    TextElement::Update(timeTotal, timeDelta);
}
```

###  <a name="updating-the-scene"></a>Обновление сцены

Метод **марблемаземаин:: Update** обновляет игру на основе текущего состояния конечного автомата ( **гаместате**, хранящегося в **m_gameState**). Когда игра находится в активном состоянии (**гаместате:: ингамеактиве**), мрамор лабиринта обновляет камеру, чтобы она подследовала мрамору, обновляет матрицу представления в виде части буферов констант и обновляет симуляцию физикы.

В следующем примере показано, как метод **марблемаземаин:: Update** обновляет расположение камеры. Лабиринт мрамора использует переменную **m\_ресеткамера** , чтобы отметить, что камеру необходимо сбросить, чтобы она находилась непосредственно над мрамором. Камера сбрасывается, когда запускается игра или когда шарик падает сквозь лабиринт. Когда активны главное меню или таблица результатов, камера остается в постоянном положении. В противоположном случае Marble Maze использует параметр *timeDelta*, чтобы интерполировать позицию камеры между текущим и целевым положением. Целевое положение находится чуть выше и впереди шарика. Использование прошедшего времени кадра позволяет камере постоянно следовать за шариком, т. е. преследовать его.

```cpp
static float eyeDistance = 200.0f;
static XMFLOAT3A eyePosition = XMFLOAT3A(0, 0, 0);

// Gradually move the camera above the marble.
XMFLOAT3A targetEyePosition;
XMStoreFloat3A(
    &targetEyePosition, 
    XMLoadFloat3A(&marblePosition) - (XMLoadFloat3A(&g) * eyeDistance));

if (m_resetCamera)
{
    eyePosition = targetEyePosition;
    m_resetCamera = false;
}
else
{
    XMStoreFloat3A(
        &eyePosition, 
        XMLoadFloat3A(&eyePosition) 
            + ((XMLoadFloat3A(&targetEyePosition) - XMLoadFloat3A(&eyePosition)) 
                * min(1, static_cast<float>(m_timer.GetElapsedSeconds()) * 8)
            )
    );
}

// Look at the marble. 
if ((m_gameState == GameState::MainMenu) || (m_gameState == GameState::HighScoreDisplay))
{
    // Override camera position for menus.
    XMStoreFloat3A(
        &eyePosition, 
        XMLoadFloat3A(&marblePosition) + XMVectorSet(75.0f, -150.0f, -75.0f, 0.0f));

    m_camera->SetViewParameters(
        eyePosition, 
        marblePosition, 
        XMFLOAT3(0.0f, 0.0f, -1.0f));
}
else
{
    m_camera->SetViewParameters(eyePosition, marblePosition, XMFLOAT3(0.0f, 1.0f, 0.0f));
}
```

В следующем примере показано, как метод **марблемаземаин:: Update** обновляет буферы констант для мрамора и лабиринта. Матрица модели лабиринта, или мира, всегда остается матрицей тождественного преобразования. Матрица тождественного преобразования является квадратной матрицей, в которой элементы на главной диагонали являются единицами, а все остальные элементы являются нулями. Матрица модели шарика рассчитывается как произведение матрицы его положения на матрицу его поворота.

```cpp
// Update the model matrices based on the simulation.
XMStoreFloat4x4(&m_mazeConstantBufferData.model, XMMatrixIdentity());

XMStoreFloat4x4(
    &m_marbleConstantBufferData.model, 
    XMMatrixTranspose(
        XMMatrixMultiply(
            marbleRotationMatrix, 
            XMMatrixTranslationFromVector(XMLoadFloat3A(&marblePosition))
        )
    )
);

// Update the view matrix based on the camera.
XMFLOAT4X4 view;
m_camera->GetViewMatrix(&view);
m_mazeConstantBufferData.view = view;
m_marbleConstantBufferData.view = view;
```

Сведения о том, как метод **марблемаземаин:: Update** считывает входные данные пользователя и имитирует движение мрамора, см. в разделе [Добавление входных и интерактивных данных в образец лабиринта мрамора](adding-input-and-interactivity-to-the-marble-maze-sample.md).

## <a name="rendering-the-scene"></a>Отрисовка сцены


Обычно отрисовка сцены включает следующие шаги.

1.  Задание текущего буфера трафарета глубины для цели отрисовки.
2.  Очистка представлений отрисовки и трафарета глубины.
3.  Подготовка вершинного шейдера и шейдера пикселей для рисования.
4.  Отрисовка трехмерных объектов в сцене.
5.  Отрисовка любого 2D-объекта, который должен отображаться перед сценой.
6.  Вывод отрисованного изображения на монитор.

Метод **марблемаземаин:: Render** привязывает целевой объект отрисовки и представления трафарета глубины, очищает эти представления, рисует сцену, а затем рисует наложенный элемент.

###  <a name="preparing-the-render-targets"></a>Подготовка целевых объектов отрисовки

Перед отрисовкой сцены необходимо задать текущий буфер трафарета глубины для цели отрисовки. Если ваша сцена не займет каждый пиксель на экране, необходимо также очистить представления отрисовки и трафарета глубины. Marble Maze очищает представления отрисовки и трафарета глубины для каждого кадра, чтобы на экране не оставались объекты от предыдущего кадра.

В следующем примере показано, как метод **марблемаземаин:: Render** вызывает метод [ссылку ID3D11DeviceContext:: омсетрендертаржетс](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets) , чтобы задать целевой объект отрисовки и буфер шаблона глубины в качестве текущих.

```cpp
auto context = m_deviceResources->GetD3DDeviceContext();

// Reset the viewport to target the whole screen.
auto viewport = m_deviceResources->GetScreenViewport();
context->RSSetViewports(1, &viewport);

// Reset render targets to the screen.
ID3D11RenderTargetView *const targets[1] = 
    { m_deviceResources->GetBackBufferRenderTargetView() };

context->OMSetRenderTargets(1, targets, m_deviceResources->GetDepthStencilView());

// Clear the back buffer and depth stencil view.
context->ClearRenderTargetView(
    m_deviceResources->GetBackBufferRenderTargetView(), 
    DirectX::Colors::Black);

context->ClearDepthStencilView(
    m_deviceResources->GetDepthStencilView(), 
    D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 
    1.0f, 
    0);
```

Интерфейсы [ID3D11RenderTargetView](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview) и [ID3D11DepthStencilView](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11depthstencilview) поддерживают механизм представления текстур, предоставляемый Direct3D 10 и более поздних версий. Дополнительные сведения см. в статье о [представлениях текстуры (Direct3D 10)](https://docs.microsoft.com/windows/desktop/direct3d10/d3d10-graphics-programming-guide-resources-access-views). Метод [омсетрендертаржетс](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets) готовит этап слияния выходных данных конвейера Direct3D. Дополнительные сведения см. в [описании стадии слияния и вывода](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-output-merger-stage).

### <a name="preparing-the-vertex-and-pixel-shaders"></a>Подготовка вершинного шейдера и шейдеров пикселей

Перед отрисовкой объектов сцены выполните следующие шаги, чтобы подготовить вершинный шейдер и шейдер пикселей для рисования.

1.  Задание входного макета шейдера в качестве текущего макета.
2.  Задание вершинного шейдера и шейдера пикселей в качестве текущих шейдеров.
3.  Обновление во всех буферах констант, содержащих данные, которые должны передаваться в шейдеры.

> [!IMPORTANT]
> Лабиринт мрамора использует одну пару шейдеров вершин и пикселей для всех трехмерных объектов. Если ваша игра использует несколько пар шейдеров, необходимо выполнять эти шаги каждый раз при рисовании объектов, для которых используются другие шейдеры. Чтобы уменьшить служебные данные, связанные с изменением состояний шейдеров, мы рекомендуем группировать вызовы для всех объектов, которые используют те же шейдеры.

 

В разделе [Загрузка шейдеров](#loading-shaders) в этом документе описано, как создается входной макет при создании вершинного шейдера. В следующем примере показано, как метод **марблемаземаин:: Render** использует метод [ссылку ID3D11DeviceContext:: иасетинпутлайаут](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetinputlayout) , чтобы установить этот макет в качестве текущего макета.

```cpp
m_deviceResources->GetD3DDeviceContext()->IASetInputLayout(m_inputLayout.Get());
```

В следующем примере показано, как метод **марблемаземаин:: Render** использует методы [ссылку ID3D11DeviceContext:: вссетшадер](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-vssetshader) и [ссылку ID3D11DeviceContext::P ссетшадер](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-pssetshader) для задания шейдеров вершин и пикселей в качестве текущих шейдеров соответственно.

```cpp
// Set the vertex shader stage state.
m_deviceResources->GetD3DDeviceContext()->VSSetShader(
    m_vertexShader.Get(),   // use this vertex shader
    nullptr,                // don't use shader linkage
    0);                     // don't use shader linkage

m_deviceResources->GetD3DDeviceContext()->PSSetShader(
    m_pixelShader.Get(),    // use this pixel shader
    nullptr,                // don't use shader linkage
    0);                     // don't use shader linkage

m_deviceResources->GetD3DDeviceContext()->PSSetSamplers(
    0,                          // starting at the first sampler slot
    1,                          // set one sampler binding
    m_sampler.GetAddressOf());  // to use this sampler
```

После того как **марблемаземаин:: Render** задает шейдеры и их входные данные, он использует метод [ссылку ID3D11DeviceContext:: упдатесубресаурце](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-updatesubresource) , чтобы обновить буфер констант с помощью матрицы модели, представления и проекции для лабиринта. Метод **UpdateSubresource** копирует данные матриц из памяти ЦП в память GPU. Вспомним, что компоненты модели и представления структуры **константбуффер** обновляются в методе **Марблемаземаин:: Update** . Затем метод **марблемаземаин:: Render** вызывает методы [ссылку ID3D11DeviceContext:: вссетконстантбуфферс](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-vssetconstantbuffers) и [ссылку ID3D11DeviceContext::P ссетконстантбуфферс](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-pssetconstantbuffers) , чтобы установить этот буфер константы в качестве текущего.

```cpp
// Update the constant buffer with the new data.
m_deviceResources->GetD3DDeviceContext()->UpdateSubresource(
    m_constantBuffer.Get(),
    0,
    nullptr,
    &m_mazeConstantBufferData,
    0,
    0);

m_deviceResources->GetD3DDeviceContext()->VSSetConstantBuffers(
    0,                                  // starting at the first constant buffer slot
    1,                                  // set one constant buffer binding
    m_constantBuffer.GetAddressOf());   // to use this buffer

m_deviceResources->GetD3DDeviceContext()->PSSetConstantBuffers(
    0,                                  // starting at the first constant buffer slot
    1,                                  // set one constant buffer binding
    m_constantBuffer.GetAddressOf());   // to use this buffer
```

Метод **марблемаземаин:: Render** выполняет аналогичные действия, чтобы подготовить мрамор для подготовки к просмотру.

### <a name="rendering-the-maze-and-the-marble"></a>Отрисовка лабиринта и шарика

После активации текущих шейдеров можно рисовать объекты сцены. Метод **марблемаземаин:: Render** вызывает метод **Сдкмеш:: Render** для отрисовки сетки лабиринта.

```cpp
m_mazeMesh.Render(
    m_deviceResources->GetD3DDeviceContext(), 
    0, 
    INVALID_SAMPLER_SLOT, 
    INVALID_SAMPLER_SLOT);
```

Метод **марблемаземаин:: Render** выполняет аналогичные действия для отрисовки мрамора.

Как отмечено в этом документе, класс **SDKMesh** предоставляется в целях демонстрации. Мы не рекомендуем использовать его в игре производственного качества. Однако обратите внимание, что метод **сдкмеш:: рендермеш** , который вызывается **Сдкмеш:: Render**, использует методы [ссылку ID3D11DeviceContext:: Иасетвертексбуфферс](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetvertexbuffers) и [ссылку ID3D11DeviceContext:: иасетиндексбуффер](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetindexbuffer) для установки текущих буферов вершин и индексов, определяющих сетку, и метод [ссылку ID3D11DeviceContext::D равиндексед](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-drawindexedinstanced) для рисования буферов. Дополнительные сведения о работе с буферами вершин и индексов см. во [вводной статье о буферах в Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-intro).

### <a name="drawing-the-user-interface-and-overlay"></a>Рисование пользовательского интерфейса и наложения

После рисования трехмерных объектов сцены мраморы рисуют двумерные элементы пользовательского интерфейса, которые отображаются перед сценой.

Метод **марблемаземаин:: Render** завершается рисованием пользовательского интерфейса и наложения.

```cpp
// Draw the user interface and the overlay.
UserInterface::GetInstance().Render(m_deviceResources->GetOrientationTransform2D());

m_deviceResources->GetD3DDeviceContext()->BeginEventInt(L"Render Overlay", 0);
m_sampleOverlay->Render();
m_deviceResources->GetD3DDeviceContext()->EndEvent();
```

Метод **усеринтерфаце:: Render** использует объект [ID2D1DeviceContext](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1devicecontext) для рисования элементов пользовательского интерфейса. Этот метод задает состояние рисования, рисует все активные элементы пользовательского интерфейса, а затем восстанавливает предыдущее состояние рисования.

```cpp
void UserInterface::Render(D2D1::Matrix3x2F orientation2D)
{
    m_d2dContext->SaveDrawingState(m_stateBlock.Get());
    m_d2dContext->BeginDraw();
    m_d2dContext->SetTransform(orientation2D);

    m_d2dContext->SetTextAntialiasMode(D2D1_TEXT_ANTIALIAS_MODE_GRAYSCALE);

    for (auto iter = m_elements.begin(); iter != m_elements.end(); ++iter)
    {
        if ((*iter)->IsVisible())
            (*iter)->Render();
    }

    // We ignore D2DERR_RECREATE_TARGET here. This error indicates that the device
    // is lost. It will be handled during the next call to Present.
    HRESULT hr = m_d2dContext->EndDraw();
    if (hr != D2DERR_RECREATE_TARGET)
    {
        DX::ThrowIfFailed(hr);
    }

    m_d2dContext->RestoreDrawingState(m_stateBlock.Get());
}
```

Метод **SampleOverlay::Render** использует аналогичные приемы, чтобы рисовать точечный рисунок наложения.

###  <a name="presenting-the-scene"></a>Презентация сцены

После рисования всех 2D-и трехмерных объектов сцены Мраморный лабиринт представляет изображение, подготовленное к просмотру. Рисунок синхронизируется с пробелом по вертикали, чтобы гарантировать, что время, не потраченное на рисование кадров, никогда не отображалось на дисплее. Marble Maze во время презентации сцены также обрабатывает смену устройств.

После возврата метода **марблемаземаин:: Render** цикл игры вызывает метод **DX::D евицересаурцес::P** , чтобы отправить Визуализированное изображение на монитор или на экран. Метод " **DX::D евицересаурцес::P** повторный вызов метода" вызывает [идксгисвапчаин::P](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-present) для выполнения текущей операции, как показано в следующем примере:

```cpp
// The first argument instructs DXGI to block until VSync, putting the application
// to sleep until the next VSync. This ensures we don't waste any cycles rendering
// frames that will never be displayed to the screen.
HRESULT hr = m_swapChain->Present(1, 0);
```

В этом примере **m\_имеющуюся цепочку буферов** является объектом [IDXGISwapChain1](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1) . Инициализация этого объекта описана в разделе [Инициализация Direct3D и Direct2D](#initializing-direct3d-and-direct2d) в настоящем документе.

Первый параметр для [идксгисвапчаин::P](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-present1)повторной отправки, *синЦинтервал*, указывает количество вертикальных пробелов, которые должны ожидать перед показом кадра. Marble Maze указывает 1, т. е. ожидание до следующего пробела по вертикали.

Метод повторной отправки [идксгисвапчаин::P](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-present) возвращает код ошибки, указывающий на то, что устройство было удалено или в противном случае произошел сбой. В этом случае Marble Maze повторно инициализирует устройство.

```cpp
// If the device was removed either by a disconnection or a driver upgrade, we
// must recreate all device resources.
if (hr == DXGI_ERROR_DEVICE_REMOVED)
{
    HandleDeviceLost();
}
else
{
    DX::ThrowIfFailed(hr);
}
```

## <a name="next-steps"></a>Дальнейшие действия


Прочитайте статью [Добавление средств ввода и взаимодействия в пример Marble Maze](adding-input-and-interactivity-to-the-marble-maze-sample.md), чтобы ознакомиться с основными приемами работы с устройствами ввода. В этом документе описывается, как лабиринт мрамора поддерживает сенсорные технологии, акселерометр, контроллеры Xbox и ввод с помощью мыши.

## <a name="related-topics"></a>См. также


* [Добавление входных и интерактивных данных в образец лабиринта мрамора](adding-input-and-interactivity-to-the-marble-maze-sample.md)
* [Структура приложения лабиринта мрамора](marble-maze-application-structure.md)
* [Разработка лабиринта мрамора, игры UWP C++ в и DirectX](developing-marble-maze-a-windows-store-game-in-cpp-and-directx.md)

 

 




