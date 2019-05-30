---
title: Добавление визуального содержимого в образец Marble Maze
description: В этом документе показано, как игра Marble Maze использует Direct3D и Direct2D в среде приложения универсальной платформы Windows (UWP). Вы освоите приемы программирования и сможете адаптировать их для работы со своей игрой.
ms.assetid: 6e43422e-e1a1-b79e-2c4b-7d5b4fa88647
ms.date: 09/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, пример, directx, графика
ms.localizationpriority: medium
ms.openlocfilehash: ce62e065170349523062fbd42d867edfed63f47c
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66369085"
---
# <a name="adding-visual-content-to-the-marble-maze-sample"></a>Добавление визуального содержимого в образец Marble Maze




В этом документе показано, как игра Marble Maze использует Direct3D и Direct2D в среде приложения универсальной платформы Windows (UWP). Вы освоите приемы программирования и сможете адаптировать их для работы со своей игрой. Чтобы узнать, как визуальные компоненты игры вписываются в общую структуру приложения Marble Maze, ознакомьтесь с разделом [Структура приложения Marble Maze](marble-maze-application-structure.md).

В разработке визуальных аспектов Marble Maze мы следовали следующей базовой схеме.

1.  Создание платформы, которая инициализирует среду Direct3D и среду Direct2D.
2.  Используйте образ и программ для редактирования модели для создания двумерных и трехмерных ресурсов, которые отображаются в игре.
3.  Убедитесь, что двумерных и трехмерных ресурсов правильно загружаются и отображаются в игре.
4.  Интеграция вершинных шейдеров и шейдеров пикселей, улучшающих качество отображения ресурсов игры.
5.  Интеграция игровой логики, в том числе анимации и пользовательского ввода.

Также были рассмотрены сначала на добавление трехмерных ресурсов, а затем 2D средств. Например, сначала мы реализовали основную игровую логику, а затем добавили систему меню и таймер.

В процессе разработки нам приходилось неоднократно возвращаться к некоторым из этих этапов. Например как мы внесли изменения в модели сетки и marble, было необходимо также изменить некоторую часть кода шейдера, который поддерживает эти модели.

> [!NOTE]
> Пример кода, соответствующего этому документу, см. в [примере игры DirectX Marble Maze](https://go.microsoft.com/fwlink/?LinkId=624011).

 
В этом документе рассматриваются следующие основные моменты работы с DirectX и визуальным содержимым игры: инициализация графических библиотек DirectX, загрузка ресурсов сцены, обновление сцены и ее отрисовка.

-   Добавление игрового содержимого обычно выполняется в несколько этапов, которые часто приходится повторять. Разработчики игр основное внимание сначала на добавление трехмерных игр, а затем по добавлению двухмерное содержимое.
-   Поддержка максимально широкого спектра графического оборудования позволит вам привлечь больше пользователей и предоставить им прекрасную игровую среду.
-   Четко разделяйте форматы времени разработки и времени выполнения. Организуйте ресурсы времени разработки так, чтобы добиться максимальной гибкости и обеспечить быстрое прохождение содержимого по этапам разработки. Сжимайте ресурсы и подбирайте для них такой формат, который обеспечит максимально эффективную загрузку и отрисовку во время выполнения.
-   Устройства Direct3D и Direct2D в приложении UWP создаются во многом так же, как и в классическом приложении Windows. Важным различием является способ привязки цепочки буферов к окну вывода.
-   Во время разработки игры убедитесь, что выбранный формат сетки поддерживает основные сценарии вашей игры. Например, если в игре происходят столкновения, необходимо иметь возможность получать из сетки данные о столкновениях.
-   Отделите игровую логику от логики отрисовки. Перед отрисовкой обновите все объекты сцены.
-   Вы обычно рисования трехмерной сцены объектов и затем любые двухмерные объекты, отображаемые перед сценой.
-   Синхронизируйте рисование с разверткой монитора, чтобы не затрачивать игровое время на рисование кадров, которые не будут отображаться на экране. Объект *вертикальной пустое* равен времени между завершении рисование в монитор одного кадра, и начинается следующий кадр.

## <a name="getting-started-with-directx-graphics"></a>Начало работы с графикой DirectX


Когда мы планировали игры Marble Maze универсальной Windows платформы (UWP), мы выбрали C++ и Direct3D 11.1, так как они являются отличные возможности для создания трехмерных игр, требующих максимальный контроль над подготовки к просмотру и высокой производительности. DirectX 11.1 поддерживает оборудование уровней от DirectX 9 до DirectX 11 и поэтому позволяет более эффективно расширить пользовательскую базу, поскольку код не нужно переписывать для каждой из предыдущих версий DirectX.

Marble Maze использует Direct3D 11.1 для отображения трехмерных игр ресурсов, а именно marble и лабиринта. Marble Maze также использует Windows Imaging Component (WIC), DirectWrite и Direct2D для отрисовки 2D ресурсов игры, например меню и таймер.

Разработку игры необходимо спланировать. Если вы не знакомы с графики DirectX, мы рекомендуем прочитать [DirectX: Приступая к работе](directx-getting-started.md) смогли ознакомиться с основными концепциями процесса создания игры DirectX для универсальной платформы Windows. Как читать этот документ и работать Marble Maze исходного кода, могут использовать следующие ресурсы для более подробные сведения о DirectX graphics:

-   [Direct3D 11 графики](https://docs.microsoft.com/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11): Описывает Direct3D 11, мощные и аппаратным ускорением трехмерной графики API для подготовки к просмотру трехмерной геометрии на платформе Windows.
-   [Direct2D](https://docs.microsoft.com/windows/desktop/Direct2D/direct2d-portal): Описывает Direct2D, API аппаратным ускорением, двумерной графики, обеспечивает высокую производительность и высокое качество отрисовки 2D geometry, растровых изображений и текста.
-   [DirectWrite](https://docs.microsoft.com/windows/desktop/DirectWrite/direct-write-portal): Описывает DirectWrite, которая поддерживает отрисовку текста высокого качества.
-   [Компонент обработки изображений Windows](https://docs.microsoft.com/windows/desktop/wic/-wic-lh): Описывает WIC, расширяемая платформа, которая предоставляет низкоуровневый API для цифровых изображений.

### <a name="feature-levels"></a>Уровни компонентов

Direct3D 11 предусмотрена парадигма с именем *компонентов уровни*. Уровень компонентов — это заданный набор функций GPU. Уровни компонентов позволяют адаптировать игру для работы на оборудовании Direct3D предыдущих версий. Игра Marble Maze поддерживает уровень компонентов 9.1, поскольку ей не требуются расширенные возможности более высоких уровней. Рекомендуется поддерживать максимально широкий спектр оборудования и масштабировать игровое содержимое таким образом, чтобы и пользователи с компьютерами высшего класса, и пользователи с более скромным оборудованием получили прекрасную игровую среду. Дополнительные сведения об уровнях компонентов см. в разделе [Direct3D 11 на оборудовании прежних версий](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-devices-downlevel).

## <a name="initializing-direct3d-and-direct2d"></a>Инициализация Direct3D и Direct2D


Устройство представляет видеоадаптер. Устройства Direct3D и Direct2D в приложении UWP создаются во многом так же, как и в классическом приложении Windows. Основным различием является подключение цепочки буферов Direct3D к системе управления окнами.

Класс **DeviceResources** служит основой для управления Direct3D и Direct2D. Этот класс выполняет общие инфраструктуры, ресурсы не играми. Определяет marble Maze **MarbleMazeMain** класса для обработки активов игры, который содержит ссылку на **DeviceResources** объекта для предоставления доступа к Direct3D и Direct2D.

Во время инициализации **DeviceResources** конструктор создает ресурсы зависящих от устройства и устройства Direct3D и Direct2D.

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

Метод **DeviceResources::CreateDeviceIndependentResources** создает фабрики для Direct2D, DirectWrite и WIC. В графике DirectX фабрики служат отправными точками создания графических ресурсов. Указывает marble Maze **D2D1 установлен\_ФАБРИКИ\_тип\_ЕДИНЫЙ\_ОБСУЖДЕНИЙ** так как он выполняет все рисунки в основном потоке.

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

**DeviceResources::CreateDeviceResources** вызовы методов [D3D11CreateDevice](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) для создания объекта устройства, который представляет адаптера дисплея Direct3D. Так как Marble Maze поддерживает функциональном уровне 9.1 и более поздних версий, **DeviceResources::CreateDeviceResources** метод задает уровни 9.1 через 11.1 в **featureLevels** массива. Direct3D по порядку проходит по этому списку и задает для приложения первый доступный уровень компонентов. Таким образом **D3D\_ФУНКЦИЯ\_уровень** записей массива перечисляются от самого высокого до самого низкого таким образом, приложение получит наивысший уровень возможностей. Метод **DeviceResources::CreateDeviceResources** получает устройство Direct3D 11.1, запрашивая устройство Direct3D 11, возвращаемое методом **D3D11CreateDevice**.

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

Метод **DeviceResources::CreateWindowSizeDependentResources** создает графические ресурсы, которые зависят от заданного размера окна, — например, цепочку буферов и целевые объекты прорисовки Direct3D и Direct2D. Важным отличием приложений UWP на DirectX от классических приложений является способ привязки цепочки буферов к окну вывода. Цепочка буферов отвечает за отображение буфера, в котором устройство выполняет отрисовку, на мониторе. [Структура приложения marble Maze](marble-maze-application-structure.md) описаны отличия в системе управления окнами для приложения универсальной платформы Windows из классического приложения. Так как приложение UWP не работает с [HWND](https://docs.microsoft.com/windows/desktop/WinProg/windows-data-types) объектов, необходимо использовать Marble Maze [IDXGIFactory2::CreateSwapChainForCoreWindow](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgifactory2-createswapchainforcorewindow) метод, чтобы сопоставить выходные данные устройства в представление. В следующем примере показана часть метода **DeviceResources::CreateWindowSizeDependentResources**, которая создает цепочку буферов.

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

Чтобы свести к минимуму потребление питания, которые необходимо выполнять на устройствах, работающем от аккумулятора, такие как ноутбуки и планшетные ПК, **DeviceResources::CreateWindowSizeDependentResources** вызовы методов [IDXGIDevice1:: SetMaximumFrameLatency](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgidevice1-setmaximumframelatency) метод, чтобы гарантировать, что игра отображается только после вертикальной пусто. Синхронизация с вертикальной пустую описан более подробно в разделе [представления сцены](#presenting-the-scene) в этом документе.

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
> Термин *представление* имеет разное значение в среде выполнения Windows, чем есть в Direct3D. В среде выполнения Windows представление — это набор параметров пользовательского интерфейса для приложения, в том числе область отображения и возможные реакции на ввод, а также поток, используемый для обработки. При создании представления задаются нужные параметры и конфигурация. Процедура настройки представления приложения описана в разделе [Структура приложения Marble Maze](marble-maze-application-structure.md).
> В Direct3D термин "представление" имеет несколько значений. Представление ресурсов определяет вспомогательных ресурсах, которые можно получить доступ к ресурсу. Например, если объект текстуры связан с представлением ресурса шейдера, то затем шейдер может получить доступ к текстуре. Преимуществом представления ресурса является возможность различной интерпретации данных на различных участках конвейера отрисовки. Дополнительные сведения о представлениях ресурсов см. в разделе [представления ресурсов](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-resources-intro).
> Термин "представление" в контексте преобразования представления или матрицы преобразования представления означает расположение и ориентацию камеры. При преобразовании представления объекты мира перемещаются относительно положения и ориентации камеры. Дополнительные сведения о преобразованиях представлений см. в разделе о [преобразовании представления (Direct3D 9)](https://docs.microsoft.com/windows/desktop/direct3d9/view-transform). Использование представлений ресурсов и матричных представлений в Marble подробно описано в этом разделе.

 

## <a name="loading-scene-resources"></a>Загрузка ресурсов сцены


Marble Maze использует **BasicLoader** класс, который объявляется в **BasicLoader.h**, чтобы загрузить текстуры и шейдеры. Marble Maze использует **SDKMesh** класс для загрузки трехмерных сеток Лабиринт и marble.

Чтобы обеспечить хороший отклик приложения, Marble Maze загружает ресурсы сцены асинхронно, в фоновом режиме. Пока ресурсы загружаются в фоновом режиме, игра может отвечать на события окон. Этот процесс подробно описан в разделе [Загрузка ресурсов игры в фоновом режиме](marble-maze-application-structure.md#loading-game-assets-in-the-background) данного руководства.

###  <a name="loading-the-2d-overlay-and-user-interface"></a>Загрузка двухмерного наложения и пользовательский интерфейс

В Marble Maze наложение — это изображение, которое отображается в верхней части экрана. Наложение всегда располагается перед сценой. В Marble Maze — содержит наложения эмблемы Windows и текстовую строку **пример игры DirectX Marble Maze**. Осуществляется управление наложения **SampleOverlay** класс, который определен в **SampleOverlay.h**. Наложение используется в составе образцов Direct3D, но можно адаптировать этот код для любого изображения, находящегося перед сценой.

Одним важным аспектом наложения то, что, поскольку не изменяйте его содержимое, **SampleOverlay** класса отрисовки, или кэши, его содержимое в [ID2D1Bitmap1](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1bitmap1) объекта во время инициализации. Во время рисования классу **SampleOverlay** остается только нарисовать на экране растровое изображение. Это позволяет не выполнять в каждом кадре ресурсоемкие подпрограммы, например для рисования текста.

Пользовательский интерфейс (UI) состоит из 2D компонентов, таких как меню и отображает информационные индикаторы (HUDs), которые отображаются перед элементами в сцену. В Marble Maze определяются следующие элементы пользовательского интерфейса:

-   пункты меню, которые позволяют пользователю начать игру или просмотреть таблицу результатов,
-   таймер, отсчитывающий время (три секунды) до начала игры,
-   таймер, который показывает истекшее время игры,
-   таблица, в которой показаны лучшие значения времени прохождения,
-   Текст, который считывает **приостановлено** когда игра в режим паузы.

Marble Maze определяет играми элементов пользовательского интерфейса в **UserInterface.h**. В Marble Maze класс **ElementBase** определяется в качестве базового типа для всех элементов пользовательского интерфейса. В классе **ElementBase** определены такие атрибуты элемента пользовательского интерфейса, как размер, положение, выравнивание и видимость. Он также управляет обновлением и отрисовкой элементов.

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
> Поскольку XAML позволяет легко создавать сложные пользовательские интерфейсы, аналогичные операциям в моделирование и стратегические игры, рассмотрите возможность использования XAML для определения пользовательского интерфейса. Сведения о том, как разработать пользовательский интерфейс в XAML в игрой DirectX UWP, см. в разделе [расширить пример игры](tutorial-resources.md), которая относится к 3D DirectX, устранении неполадок пример игры.

 

###  <a name="loading-shaders"></a>Загрузка шейдеров

В Marble Maze используется метод **BasicLoader::LoadShader** для загрузки шейдера из файла.

В современных играх шейдеры являются неотъемлемой частью программирования GPU. Практически вся обработка трехмерной графики управляется через шейдеров, будь то преобразования модели и освещения сцены или более сложной геометрии, обработку, символ создание обложек для тесселяции. Дополнительные сведения о модели программирования шейдеров см. в разделе [HLSL](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl).

В Marble Maze используются вершинные построители текстуры и шейдеры пикселей. Вершинный шейдер всегда работает с одной входной вершиной, и в результате дает одну вершину. Шейдер пикселей принимает числовые значения, данные текстуры, интерполированные значения по вершинам и другие данные, а в результате дает цвет пикселя. Поскольку шейдер преобразует элементы по одному, графическое оборудование с несколькими шейдерными конвейерами может параллельно обрабатывать наборы элементов. Число параллельных конвейеров, доступных GPU, может значительно превышать число, доступное ЦП. Поэтому даже простые шейдеры могут значительно повысить пропускную способность.

**MarbleMazeMain::LoadDeferredResources** метод загружает один вершинный построитель текстуры и один шейдер пикселей, после его загрузки наложения. Во время разработки версиях эти шейдеры определяются в **BasicVertexShader.hlsl** и **BasicPixelShader.hlsl**, соответственно. Marble Maze применяет эти шейдеры к шарику и лабиринту на этапе отрисовки.

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
> Скомпилированный текстуры содержит 32 арифметические инструкции и 1 команда текстуры. Этот шейдер должен обеспечивать высокую производительность на настольных компьютерах и планшетах высшего класса. Однако на менее мощном компьютере, где обработка этого шейдера невозможна, все же можно добиться частоты кадров, обеспечивающей интерактивность. Изучите типичное оборудование целевой аудитории своей игры и разрабатывайте шейдеры с учетом возможностей этого оборудования.

 

**MarbleMazeMain::LoadDeferredResources** использует метод **BasicLoader::LoadShader** метод для загрузки шейдеров. В следующем примере загружается вершинный шейдер. Формат времени выполнения этот шейдер **BasicVertexShader.cso**. **m\_vertexShader** переменная-член является [ID3D11VertexShader](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11vertexshader) объекта.

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

**m\_inputLayout** переменная-член является [ID3D11InputLayout](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11inputlayout) объекта. В объекте макета входных данных инкапсулируется входное состояние этапа сборки входных данных (IA). Одной из задач этапа IA является повышение эффективности шейдеров за счет использования значений, созданных системой (они также называются *семантикой*), для обработки исключительно еще не обработанных примитивов и вершин.

Используйте [ID3D11Device::CreateInputLayout](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createinputlayout) метод, чтобы создать структуру входных данных из массива элемент ввод описания. Этот массив содержит один или несколько входных элементов. Каждый входной элемент описывает один элемент vertex-data из одного буфера вершин. Весь набор описаний input-element описывает все элементы vertex-data из всех буферов вершин, которые будут привязаны на этапе IA. 

**layoutDesc** в приведенном выше коде фрагмент показывает описание макета что Marble Maze использует. Описание макета описывает буфер вершин, который содержит четыре элемента vertex-data. Важными частями каждого элемента массива являются имя семантики, формат данных и смещение в байтах. Например, элемент **POSITION** задает положение вершины в пространстве объектов. Он начинается со смещения в 0 байт и содержит три компонента с плавающей запятой (всего 12 байт). Элемент **NORMAL** задает нормальный вектор. Он начинается со смещения в 12 байт, поскольку идет в макете сразу после элемента **POSITION**, которому необходимо 12 байт. Элемент **NORMAL** содержит четырехкомпонентное 32-разрядное целое число без знака.

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
> В макете можно указать дополнительные компоненты, которые не используются для включения нескольких шейдеры совместно использовать тот же макет. Например, элемент **TANGENT** не используется шейдером. Можно использовать элемент **TANGENT**, если хотите проверить какие-либо приемы, например сопоставление нормали. С помощью сопоставления нормали, которое также называют отображением элементов рельефа, вы можете создавать эффект выпуклостей на поверхностях объектов. Дополнительные сведения см. в статье об [отображении элементов рельефа (Direct3D 9)](https://docs.microsoft.com/windows/desktop/direct3d9/bump-mapping).

 

Дополнительные сведения об этапе входной сборки см. в разделе [сборщик входных данных рабочей области](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-input-assembler-stage) и [Приступая к работе с этапом сборщик входных данных](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-input-assembler-stage-getting-started).

Процесс использования вершинных шейдеров и построителей текстуры для отрисовки сцены описан в разделе [Отрисовка сцены](#rendering-the-scene) ниже в этом документе.

### <a name="creating-the-constant-buffer"></a>Создание буферов констант

Буфер Direct3D группирует коллекцию данных. Буфер констант является типом буфера, который можно использовать, чтобы передавать данные в шейдеры. В Marble Maze используется буфер констант, чтобы удерживать представление модели (или мира), а также матрицы проекции для активного объекта сцены.

В следующем примере показан как **MarbleMazeMain::LoadDeferredResources** метод создает буфер констант, которые позже будут содержать данных матрицы. В примере создается **D3D11\_БУФЕРА\_DESC** структуру, которая использует **D3D11\_ПРИВЯЗАТЬ\_КОНСТАНТЫ\_БУФЕРА** флаг Укажите использование в качестве буфера констант. В этом примере передает этой структуры [ID3D11Device::CreateBuffer](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createbuffer) метод. **m\_constantBuffer** переменная является [ID3D11Buffer](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11buffer) объекта.

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

**MarbleMazeMain::Update** Далее метод обновляет **ConstantBuffer** объектов, один для лабиринта и один для marble. **MarbleMazeMain::Render** метод затем привязывает каждую **ConstantBuffer** объекта буфера констант перед подготовкой к просмотру каждого объекта. В следующем примере показан **ConstantBuffer** структуру, которая находится в **MarbleMazeMain.h**.

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

Чтобы лучше понять, как константа буферов карты кода шейдера, сравнив **ConstantBuffer** структуре **MarbleMazeMain.h** для **ConstantBuffer** буфера констант определяется шейдер вершин в **BasicVertexShader.hlsl**:

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

макет структуры **ConstantBuffer** соответствует объекту **cbuffer**. Переменная **cbuffer** указывает регистр b0. Это означает, что данные буфера констант сохраняются в регистре 0. **MarbleMazeMain::Render** указывает метод регистрации 0, когда он активирует буфер констант. Этот процесс подробно описан ниже в данном документе.

Дополнительные сведения о буферах констант см. во [вводной статье о буферах Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-intro). Дополнительные сведения о ключевом слове регистра см. в разделе [зарегистрировать](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-variable-register).

###  <a name="loading-meshes"></a>Загрузка mesh-объектов

Marble Maze использует SDK-Mesh как формат времени выполнения, поскольку этот формат предоставляет простой способ загрузки данных сетки для образцов приложений. Для использования в производстве вы должны применять формат сетки, отвечающий конкретным требованиям вашей игры.

**MarbleMazeMain::LoadDeferredResources** загрузки методов объединяют данные после загрузки шейдеров вершин и пикселей. Сетка является коллекцией данных вершин, в которые часто включают такую информацию, как положения, данные нормали, цвета, материалы и координаты текстуры. Слои взаимодействия между обычно создаются в трехмерном пространстве разработки программного обеспечения и настраиваются в файлах, которые отделены от кода приложения. Шарик и лабиринт могут служить двумя примерами сеток, которые используются в игре.

В Marble Maze для управления сетками используется класс **SDKMesh**. Этот класс объявлен в **SDKMesh.h**. **SDKMesh** предоставляет методы для загрузки, отрисовки и уничтожения данных сетки.

> [!IMPORTANT]
> Marble Maze использует формат пакета SDK для сетки и предоставляет **SDKMesh** класс только для иллюстрации. Хотя формат SDK-Mesh удобен для обучения и создания прототипов, это очень простой формат, который может не отвечать требованиям большинства игр. Мы рекомендуем использовать формат сетки, отвечающий конкретным требованиям вашей игры.

 

В следующем примере показан как **MarbleMazeMain::LoadDeferredResources** использует метод **SDKMesh::Create** метод для загрузки объединяют данные для лабиринта и шара.

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

Способ загрузки данных конфликта во многом зависит от формат времени выполнения, который используется. Дополнительные сведения о как Marble Maze загружается геометрии конфликт из файла пакета SDK для сетки, см. в разделе **MarbleMazeMain::ExtractTrianglesFromMesh** метода в исходном коде.

## <a name="updating-game-state"></a>Обновление состояния игры


Marble Maze отделяет логику игры от логики отрисовки, предварительно обновляя все объекты сцены перед их отрисовкой.

[Структура приложения marble Maze](marble-maze-application-structure.md) описывает основной цикл игры. Обновление сцены, являющееся частью цикла игры, имеет место после обработки событий Windows и входных данных, но до отрисовки сцены. **MarbleMazeMain::Update** метод обрабатывает обновление пользовательского интерфейса и игры.

### <a name="updating-the-user-interface"></a>Обновление пользовательского интерфейса

**MarbleMazeMain::Update** вызовы методов **UserInterface::Update** метода, чтобы обновить состояние пользовательского интерфейса.

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

Классы, производные от **ElementBase** (определенные в **UserInterface.h**) реализовать **обновления** метод для выполнения определенного поведения. Например, метод **StopwatchTimer::Update** обновляет затраченное время на предоставленную величину и обновляет текст, который отображается после этого.

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

**MarbleMazeMain::Update** метод обновляет игры на основе текущего состояния конечного автомата ( **GameState**, который хранится в **m_gameState**). Когда игра находится в активном состоянии (**GameState::InGameActive**), Marble Maze обновляет камеры следовать marble, обновляет представление матрицы часть постоянные буферы и обновляет физическое моделирование.

В следующем примере показан как **MarbleMazeMain::Update** метод обновляет позицию камеры. Marble Maze использует **m\_resetCamera** переменной на флаг, что камера должны быть сброшены до находится непосредственно над marble. Камера сбрасывается, когда запускается игра или когда шарик падает сквозь лабиринт. Когда активны главное меню или таблица результатов, камера остается в постоянном положении. В противоположном случае Marble Maze использует параметр *timeDelta*, чтобы интерполировать позицию камеры между текущим и целевым положением. Целевое положение находится чуть выше и впереди шарика. Использование прошедшего времени кадра позволяет камере постоянно следовать за шариком, т. е. преследовать его.

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

В следующем примере показан как **MarbleMazeMain::Update** метод обновляет буферов констант для marble и лабиринта. Матрица модели лабиринта, или мира, всегда остается матрицей тождественного преобразования. Матрица тождественного преобразования является квадратной матрицей, в которой элементы на главной диагонали являются единицами, а все остальные элементы являются нулями. Матрица модели шарика рассчитывается как произведение матрицы его положения на матрицу его поворота.

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

Сведения о том, как **MarbleMazeMain::Update** метод считывает входные данные пользователя и имитирует движение marble, см. в разделе [Добавление ввода и взаимодействия в пример Marble Maze](adding-input-and-interactivity-to-the-marble-maze-sample.md).

## <a name="rendering-the-scene"></a>Отрисовка сцены


Обычно отрисовка сцены включает следующие шаги.

1.  Задание текущего буфера трафарета глубины для цели отрисовки.
2.  Очистка представлений отрисовки и трафарета глубины.
3.  Подготовка вершинного шейдера и шейдера пикселей для рисования.
4.  Отображения трехмерных объектов в сцене.
5.  Отображать все двумерного объекта, который будет отображаться перед сценой.
6.  Вывод отрисованного изображения на монитор.

**MarbleMazeMain::Render** метод связывает целевой объект отрисовки, а также трафарет глубины представления, очищает эти представления, рисует сцены и затем выполняется рисование наложенного изображения.

###  <a name="preparing-the-render-targets"></a>Подготовка целевых объектов отрисовки

Перед отрисовкой сцены необходимо задать текущий буфер трафарета глубины для цели отрисовки. Если ваша сцена не займет каждый пиксель на экране, необходимо также очистить представления отрисовки и трафарета глубины. Marble Maze очищает представления отрисовки и трафарета глубины для каждого кадра, чтобы на экране не оставались объекты от предыдущего кадра.

В следующем примере показан как **MarbleMazeMain::Render** вызовы методов [ID3D11DeviceContext::OMSetRenderTargets](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets) метод, чтобы задать в качестве текущего целевого объекта отрисовки и буфера трафарета глубины те, которые.

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

[ID3D11RenderTargetView](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview) и [ID3D11DepthStencilView](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11depthstencilview) интерфейсы поддерживают механизм представления текстуры, который предоставляется по Direct3D 10 и более поздних версий. Дополнительные сведения см. в статье о [представлениях текстуры (Direct3D 10)](https://docs.microsoft.com/windows/desktop/direct3d10/d3d10-graphics-programming-guide-resources-access-views). [OMSetRenderTargets](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-omsetrendertargets) метод подготавливает средство слияния вывода этапа конвейера Direct3D. Дополнительные сведения см. в [описании стадии слияния и вывода](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-output-merger-stage).

### <a name="preparing-the-vertex-and-pixel-shaders"></a>Подготовка вершинного шейдера и шейдеров пикселей

Перед отрисовкой объектов сцены выполните следующие шаги, чтобы подготовить вершинный шейдер и шейдер пикселей для рисования.

1.  Задание входного макета шейдера в качестве текущего макета.
2.  Задание вершинного шейдера и шейдера пикселей в качестве текущих шейдеров.
3.  Обновление во всех буферах констант, содержащих данные, которые должны передаваться в шейдеры.

> [!IMPORTANT]
> Marble Maze использует одну пару шейдеров вершин и пикселей для всех трехмерных объектов. Если ваша игра использует несколько пар шейдеров, необходимо выполнять эти шаги каждый раз при рисовании объектов, для которых используются другие шейдеры. Чтобы уменьшить служебные данные, связанные с изменением состояний шейдеров, мы рекомендуем группировать вызовы для всех объектов, которые используют те же шейдеры.

 

В разделе [Загрузка шейдеров](#loading-shaders) в этом документе описано, как создается входной макет при создании вершинного шейдера. В следующем примере показан как **MarbleMazeMain::Render** использует метод [ID3D11DeviceContext::IASetInputLayout](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetinputlayout) метод должны стать этот макет окна текущим макетом.

```cpp
m_deviceResources->GetD3DDeviceContext()->IASetInputLayout(m_inputLayout.Get());
```

В следующем примере показан как **MarbleMazeMain::Render** использует метод [ID3D11DeviceContext::VSSetShader](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-vssetshader) и [ID3D11DeviceContext::PSSetShader](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-pssetshader) методы должны стать шейдеров вершин и пикселей текущего шейдеров, соответственно.

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

После **MarbleMazeMain::Render** задает преобразователи текстур и их входной макет, он использует [ID3D11DeviceContext::UpdateSubresource](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-updatesubresource) метод обновления буфера констант с помощью модели, представления, и проекция матрицы для лабиринта. Метод **UpdateSubresource** копирует данные матриц из памяти ЦП в память GPU. Помните, что модель и представление компонентов **ConstantBuffer** структура обновляются в **MarbleMazeMain::Update** метод. **MarbleMazeMain::Render** затем вызывает метод [ID3D11DeviceContext::VSSetConstantBuffers](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-vssetconstantbuffers) и [ID3D11DeviceContext::PSSetConstantBuffers](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-pssetconstantbuffers) методы Этот буфер констант в качестве текущего.

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

**MarbleMazeMain::Render** метод выполняет аналогичные действия для подготовки к просмотру marble.

### <a name="rendering-the-maze-and-the-marble"></a>Отрисовка лабиринта и шарика

После активации текущих шейдеров можно рисовать объекты сцены. **MarbleMazeMain::Render** вызовы методов **SDKMesh::Render** метод для отображения сетки лабиринта.

```cpp
m_mazeMesh.Render(
    m_deviceResources->GetD3DDeviceContext(), 
    0, 
    INVALID_SAMPLER_SLOT, 
    INVALID_SAMPLER_SLOT);
```

**MarbleMazeMain::Render** метод выполняет аналогичные действия для подготовки к просмотру marble.

Как отмечено в этом документе, класс **SDKMesh** предоставляется в целях демонстрации. Мы не рекомендуем использовать его в игре производственного качества. Тем не менее, обратите внимание, что **SDKMesh::RenderMesh** метод, который вызывается **SDKMesh::Render**, использует [ID3D11DeviceContext::IASetVertexBuffers](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetvertexbuffers) и [ID3D11DeviceContext::IASetIndexBuffer](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-iasetindexbuffer) методы задания текущей вершины и индексы, которые определяют сетки, и [ID3D11DeviceContext::DrawIndexed](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-drawindexedinstanced) метод для отображения в буферы. Дополнительные сведения о работе с буферами вершин и индексов см. во [вводной статье о буферах в Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-resources-buffers-intro).

### <a name="drawing-the-user-interface-and-overlay"></a>Рисование пользовательского интерфейса и наложения

После рисования объектов в трехмерной сцене, Marble Maze рисует 2D элементы пользовательского интерфейса, которые отображаются перед сценой.

**MarbleMazeMain::Render** метод завершается путем рисования пользовательского интерфейса и наложения.

```cpp
// Draw the user interface and the overlay.
UserInterface::GetInstance().Render(m_deviceResources->GetOrientationTransform2D());

m_deviceResources->GetD3DDeviceContext()->BeginEventInt(L"Render Overlay", 0);
m_sampleOverlay->Render();
m_deviceResources->GetD3DDeviceContext()->EndEvent();
```

**UserInterface::Render** использует метод [ID2D1DeviceContext](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1devicecontext) для рисования элементов пользовательского интерфейса. Этот метод задает состояние рисования, рисует все активные элементы пользовательского интерфейса, а затем восстанавливает предыдущее состояние рисования.

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

Нарисовав все двухмерных и трехмерных объектов сцены, Marble Maze представляется подготовленного изображения к монитору. Рисунок синхронизируется с пробелом по вертикали, чтобы гарантировать, что время, не потраченное на рисование кадров, никогда не отображалось на дисплее. Marble Maze во время презентации сцены также обрабатывает смену устройств.

После **MarbleMazeMain::Render** возвращает метод, вызовы цикла игры **DX::DeviceResources::Present** метод для отправки подготовленного изображения к монитору или отображения. **DX::DeviceResources::Present** вызовы методов [IDXGISwapChain::Present](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-present) для выполнения присутствует операции, как показано в следующем примере:

```cpp
// The first argument instructs DXGI to block until VSync, putting the application
// to sleep until the next VSync. This ensures we don't waste any cycles rendering
// frames that will never be displayed to the screen.
HRESULT hr = m_swapChain->Present(1, 0);
```

В этом примере **m\_swapChain** — [IDXGISwapChain1](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1) объекта. Инициализация этого объекта описана в разделе [Инициализация Direct3D и Direct2D](#initializing-direct3d-and-direct2d) в настоящем документе.

Первый параметр для [IDXGISwapChain::Present](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-present1), *SyncInterval*, указывает количество вертикальной пустых значений для ожидания перед демонстрацией кадр. Marble Maze указывает 1, т. е. ожидание до следующего пробела по вертикали.

[IDXGISwapChain::Present](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-present) метод возвращает код ошибки, который указывает, что устройство было удалено или в противном случае не удалось. В этом случае Marble Maze повторно инициализирует устройство.

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

## <a name="next-steps"></a>Следующие шаги


Прочитайте статью [Добавление средств ввода и взаимодействия в пример Marble Maze](adding-input-and-interactivity-to-the-marble-maze-sample.md), чтобы ознакомиться с основными приемами работы с устройствами ввода. В этом документе рассматриваются как Marble Maze поддерживает touch, акселерометр, контроллеров Xbox и ввод от мыши.

## <a name="related-topics"></a>См. также


* [Добавление средств ввода и взаимодействия в пример Marble Maze](adding-input-and-interactivity-to-the-marble-maze-sample.md)
* [Структура приложения marble Maze](marble-maze-application-structure.md)
* [Разработка Marble Maze, игрой UWP на C++ и DirectX](developing-marble-maze-a-windows-store-game-in-cpp-and-directx.md)

 

 




