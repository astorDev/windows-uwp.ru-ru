---
author: mtoepke
title: Добавление визуального содержимого в образец Marble Maze
description: В этом документе показано, как игра Marble Maze использует Direct3D и Direct2D в среде приложения универсальной платформы Windows (UWP). Вы освоите приемы программирования и сможете адаптировать их для работы со своей игрой.
ms.assetid: 6e43422e-e1a1-b79e-2c4b-7d5b4fa88647
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, игры, пример, directx, графика
ms.openlocfilehash: b8ee07dc45e53f2ea73f87111fa9eb155854f10a
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.locfileid: "225817"
---
# <a name="adding-visual-content-to-the-marble-maze-sample"></a>Добавление визуального содержимого в пример Marble Maze


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этом документе показано, как игра Marble Maze использует Direct3D и Direct2D в среде приложения универсальной платформы Windows (UWP). Вы освоите приемы программирования и сможете адаптировать их для работы со своей игрой. Чтобы узнать, как визуальные компоненты игры вписываются в общую структуру приложения Marble Maze, ознакомьтесь с разделом [Структура приложения Marble Maze](marble-maze-application-structure.md).

В разработке визуальных аспектов Marble Maze мы следовали следующей базовой схеме.

1.  Создание платформы, которая инициализирует среду Direct3D и среду Direct2D.
2.  Разработка двухмерных и трехмерных ресурсов, встречающихся в игре, в программах для редактирования изображений и моделей.
3.  Обеспечение правильной загрузки и отображения двухмерных и трехмерных ресурсов в игре.
4.  Интеграция вершинных шейдеров и шейдеров пикселей, улучшающих качество отображения ресурсов игры.
5.  Интеграция игровой логики, в том числе анимации и пользовательского ввода.

Кроме того, в первую очередь мы занимались трехмерными ресурсами, а затем — двухмерными. Например, сначала мы реализовали основную игровую логику, а затем добавили систему меню и таймер.

В процессе разработки нам приходилось неоднократно возвращаться к некоторым из этих этапов. Например, после внесения изменений в модели сетки и шарика было необходимо изменить код некоторых шейдеров, поддерживающих эти модели.

> **Примечание**. Пример кода, соответствующий этому документу, можно найти в [примере игры DirectX "Marble Maze"](http://go.microsoft.com/fwlink/?LinkId=624011).

 
В этом документе рассматриваются следующие основные моменты работы с DirectX и визуальным содержимым игры: инициализация графических библиотек DirectX, загрузка ресурсов сцены, обновление сцены и ее отрисовка.

-   Добавление игрового содержимого обычно выполняется в несколько этапов, которые часто приходится повторять. Разработчики игр обычно занимаются в первую очередь добавлением трехмерного игрового содержимого, а затем — двухмерного.
-   Поддержка максимально широкого спектра графического оборудования позволит вам привлечь больше пользователей и предоставить им прекрасную игровую среду.
-   Четко разделяйте форматы времени разработки и времени выполнения. Организуйте ресурсы времени разработки так, чтобы добиться максимальной гибкости и обеспечить быстрое прохождение содержимого по этапам разработки. Сжимайте ресурсы и подбирайте для них такой формат, который обеспечит максимально эффективную загрузку и отрисовку во время выполнения.
-   Устройства Direct3D и Direct2D в приложении UWP создаются во многом так же, как и в классическом приложении Windows. Важным различием является способ привязки цепочки буферов к окну вывода.
-   Во время разработки игры убедитесь, что выбранный формат сетки поддерживает основные сценарии вашей игры. Например, если в игре происходят столкновения, необходимо иметь возможность получать из сетки данные о столкновениях.
-   Отделите игровую логику от логики отрисовки. Перед отрисовкой обновите все объекты сцены.
-   Сначала обычно рисуются трехмерные объекты сцены, а затем все двухмерные объекты, которые находятся перед сценой.
-   Синхронизируйте рисование с разверткой монитора, чтобы не затрачивать игровое время на рисование кадров, которые не будут отображаться на экране.

## <a name="getting-started-with-directx-graphics"></a>Начало работы с графикой DirectX


Планируя игру универсальной платформы Windows (UWP) Marble Maze, мы выбрали C++ и Direct3D 11.1, поскольку это лучшие инструменты для создания трехмерных игр, когда требуется полный контроль над отрисовкой и высокая производительность. DirectX 11.1 поддерживает оборудование уровней от DirectX 9 до DirectX 11 и поэтому позволяет более эффективно расширить пользовательскую базу, поскольку код не нужно переписывать для каждой из предыдущих версий DirectX.

В Marble Maze с помощью Direct3D11.1 выполняется отрисовка трехмерных игровых объектов: шарика и лабиринта. В Marble Maze также используются Direct2D, DirectWrite и компонент обработки изображений Windows (WIC) для рисования двумерных игровых ресурсов: меню, таймера ит.п. Наконец, Marble Maze использует XAML, чтобы предоставить панель приложения, и позволяет вам добавлять элементы управления XAML.

Разработку игры необходимо спланировать. Если вы впервые работаете с графикой DirectX, рекомендуется прочесть раздел "Создание игры DirectX", чтобы ознакомиться с основами создания игры UWP DirectX. Ознакомившись с этим документом и изучив исходный код Marble Maze, вы сможете получить подробную информацию о графике DirectX из следующих ресурсов.

-   [Графика Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/ff476080) Описывает Direct3D 11 — мощный API трехмерной графики с аппаратным ускорением для отрисовки трехмерной геометрии на платформе Windows.
-   [Direct2D](https://msdn.microsoft.com/library/windows/desktop/dd370990) Описывает Direct2D — API двухмерной графики с аппаратным ускорением, который обеспечивает высокую производительность и высокое качество отрисовки двухмерной геометрии, растровых изображений и текста.
-   [DirectWrite](https://msdn.microsoft.com/library/windows/desktop/dd368038) Описывает DirectWrite — API, поддерживающий отрисовку текста высокого качества.
-   [Компонент обработки изображений Windows](https://msdn.microsoft.com/library/windows/desktop/ee719902) Описывает WIC — расширяемую платформу, предоставляющую API низкого уровня для цифровых изображений.

### <a name="feature-levels"></a>Уровни компонентов

В Direct3D 11 появилось понятие уровней компонентов. Уровень компонентов — это заданный набор функций GPU. Уровни компонентов позволяют адаптировать игру для работы на оборудовании Direct3D предыдущих версий. Игра Marble Maze поддерживает уровень компонентов 9.1, поскольку ей не требуются расширенные возможности более высоких уровней. Рекомендуется поддерживать максимально широкий спектр оборудования и масштабировать игровое содержимое таким образом, чтобы и пользователи с компьютерами высшего класса, и пользователи с более скромным оборудованием получили прекрасную игровую среду. Дополнительные сведения об уровнях компонентов см. в разделе [Direct3D 11 на оборудовании прежних версий](https://msdn.microsoft.com/library/windows/desktop/ff476872).

## <a name="initializing-direct3d-and-direct2d"></a>Инициализация Direct3D и Direct2D


Устройство представляет видеоадаптер. Устройства Direct3D и Direct2D в приложении UWP создаются во многом так же, как и в классическом приложении Windows. Основным различием является подключение цепочки буферов Direct3D к системе управления окнами.

*Шаблон приложения DirectX 11 и XAML (универсальное приложение для Windows)* отделяет универсальные функции операционной системы и отрисовки трехмерной графики от специальных игровых функций. Класс **DeviceResources** служит основой для управления Direct3D и Direct2D. Этот класс реализует общую инфраструктуру и ресурсы, не относящиеся к определенной игре. Marble Maze определяет класс **MarbleMaze**, чтобы обрабатывать относящиеся к игре объекты, которые имеют ссылку на объект **DeviceResources**, предоставляющий им доступ к Direct3D и Direct2D.

Во время инициализации метод **DeviceResources::Initialize** создает аппаратно независимые ресурсы, а также устройства Direct3D и Direct2D.

```cpp
// Initialize the Direct3D resources required to run. 
void DeviceResources::DeviceResources(CoreWindow^ window, float dpi)
{
    m_window = window;

    CreateDeviceIndependentResources();
    CreateDeviceResources();
    CreateWindowSizeDependentResources();
    SetDpi(dpi);
}
```

В классе **DeviceResources** эти функции разделены, что упрощает адаптацию к изменениям среды. Например, когда изменяется размер окна, вызывается метод **CreateWindowSizeDependentResources**.

###  <a name="initializing-the-direct2d-directwrite-and-wic-factories"></a>Инициализация фабрик Direct2D, DirectWrite и WIC

Метод **DeviceResources::CreateDeviceIndependentResources** создает фабрики для Direct2D, DirectWrite и WIC. В графике DirectX фабрики служат отправными точками создания графических ресурсов. В Marble Maze задается свойство **D2D1\_FACTORY\_TYPE\_SINGLE\_THREADED**, поскольку все рисование выполняется в основном потоке.

```cpp
// These are the resources required independent of hardware. 
void DeviceResources::CreateDeviceIndependentResources()
{
    D2D1_FACTORY_OPTIONS options;
    ZeroMemory(&options, sizeof(D2D1_FACTORY_OPTIONS));

#if defined(_DEBUG)
     // If the project is in a debug build, enable Direct2D debugging via SDK Layers.
    options.debugLevel = D2D1_DEBUG_LEVEL_INFORMATION;
#endif

    DX::ThrowIfFailed(
        D2D1CreateFactory(
            D2D1_FACTORY_TYPE_SINGLE_THREADED,
            __uuidof(ID2D1Factory1),
            &options,
            &m_d2dFactory
            )
        );

    DX::ThrowIfFailed(
        DWriteCreateFactory(
            DWRITE_FACTORY_TYPE_SHARED,
            __uuidof(IDWriteFactory),
            &m_dwriteFactory
            )
        );

    DX::ThrowIfFailed(
        CoCreateInstance(
            CLSID_WICImagingFactory,
            nullptr,
            CLSCTX_INPROC_SERVER,
            IID_PPV_ARGS(&m_wicFactory)
            )
        );
}
```

###  <a name="creating-the-direct3d-and-direct2d-devices"></a>Создание устройств Direct3D и Direct2D

Метод **DeviceResources::CreateDeviceResources** вызывает [**D3D11CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082), чтобы создать объект устройства, который представляет видеоадаптер Direct3D. Поскольку Marble Maze поддерживает уровень компонентов 9.1 и выше, метод **DeviceResources::CreateDeviceResources** задает в массиве значений **\\** уровни с 9.1 по 11.1. Direct3D по порядку проходит по этому списку и задает для приложения первый доступный уровень компонентов. Поэтому элементы массива **D3D\_FEATURE\_LEVEL** задаются в порядке от максимального к минимальному, чтобы приложение получило самый высокий из доступных уровней компонентов. Метод **DeviceResources::CreateDeviceResources** получает устройство Direct3D11.1, запрашивая устройство Direct3D11, возвращаемое методом **D3D11CreateDevice**.

```cpp
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

// Create the DX11 API device object, and get a corresponding context.
ComPtr<ID3D11Device> device;
ComPtr<ID3D11DeviceContext> context;
DX::ThrowIfFailed(
    D3D11CreateDevice(
        nullptr,                    // Specify null to use the default adapter.
        D3D_DRIVER_TYPE_HARDWARE,
        0,                          // Leave as 0 unless it is a software device.
        creationFlags,              // Optionally, set debug and Direct2D compatibility flags.
        featureLevels,              // A list of feature levels that this app can support.
        ARRAYSIZE(featureLevels),   // The number of entries in the above list.
        D3D11_SDK_VERSION,          // Always set this to D3D11_SDK_VERSION for modern.
        &device,                    // Returns the Direct3D device created.
        &m_featureLevel,            // Returns the feature level of the device created.
        &context                    // Returns the device immediate context.
        )
    );    

// Get the Direct3D 11.1 device by querying the Direct3D 11 device.
DX::ThrowIfFailed(
    device.As(&m_d3dDevice)
    );
```

Метод **DeviceResources::CreateDeviceResources** затем создает устройство Direct2D. Direct2D использует инфраструктуру Microsoft DirectX Graphics Infrastructure (DXGI) для взаимодействия с Direct3D. DXGI позволяет графическим средам выполнения совместно использовать ресурсы видеопамяти. Marble Maze использует базовое устройство DXGI из устройства Direct3D, чтобы создать устройство Direct2D из фабрики Direct2D.

```cpp
// Obtain the underlying DXGI device of the Direct3D 11.1 device.
DX::ThrowIfFailed(
    m_d3dDevice.As(&dxgiDevice)
    );

// Obtain the Direct2D device for 2-D rendering.
DX::ThrowIfFailed(
    m_d2dFactory->CreateDevice(dxgiDevice.Get(), &m_d2dDevice)
    );

// And get its corresponding device context object.
DX::ThrowIfFailed(
    m_d2dDevice->CreateDeviceContext(
        D2D1_DEVICE_CONTEXT_OPTIONS_NONE,
        &m_d2dContext
        )
    );
```

```cpp
// Obtain the underlying DXGI device of the Direct3D 11.1 device.
DX::ThrowIfFailed(
    m_d3dDevice.As(&dxgiDevice)
    );

// Obtain the Direct2D device for 2-D rendering.
DX::ThrowIfFailed(
    m_d2dFactory->CreateDevice(dxgiDevice.Get(), &m_d2dDevice)
    );

// And get its corresponding device context object.
DX::ThrowIfFailed(
    m_d2dDevice->CreateDeviceContext(
        D2D1_DEVICE_CONTEXT_OPTIONS_NONE,
        &m_d2dContext
        )
    );
```

Дополнительные сведения о DXGI и взаимодействии между Direct2D и Direct3D см. в разделах [Обзор DXGI](https://msdn.microsoft.com/library/windows/desktop/bb205075) и [Обзор взаимодействия Direct2D и Direct3D](https://msdn.microsoft.com/library/windows/desktop/dd370966).

### <a name="associating-direct3d-with-the-view"></a>Привязка Direct3D к представлению

Метод **DeviceResources::CreateWindowSizeDependentResources** создает графические ресурсы, которые зависят от заданного размера окна,— например, цепочку буферов и целевые объекты прорисовки Direct3D и Direct2D. Важным отличием приложений UWP на DirectX от классических приложений является способ привязки цепочки буферов к окну вывода. Цепочка буферов отвечает за отображение буфера, в котором устройство выполняет отрисовку, на мониторе. В документе "Структура приложения Marble Maze" описываются отличия системы управления окнами для приложения UWP от классического приложения. Поскольку приложение Магазина Windows не работает с объектами **HWND**, в Marble Maze необходимо использовать метод [**IDXGIFactory2::CreateSwapChainForCoreWindow**](https://msdn.microsoft.com/library/windows/desktop/hh404559) для привязки вывода устройства к представлению. В следующем примере показана часть метода **DeviceResources::CreateWindowSizeDependentResources**, которая создает цепочку буферов.

```cpp
// Obtain the final swap chain for this window from the DXGI factory.
DX::ThrowIfFailed(
    dxgiFactory->CreateSwapChainForCoreWindow(
        m_d3dDevice.Get(),
        reinterpret_cast<IUnknown*>(m_window),
        &swapChainDesc,
        nullptr,    // Allow on all displays.
        &m_swapChain
        )
    );
```

Чтобы обеспечить минимальное энергопотребление (что актуально на устройствах, которые работают от аккумулятора, таких как ноутбуки и планшеты), метод **DeviceResources::CreateWindowSizeDependentResources** вызывает метод [**IDXGIDevice1::SetMaximumFrameLatency**](https://msdn.microsoft.com/library/windows/desktop/ff471334), чтобы гарантировать, что игра отрисовывается только после выдержки интервала между кадрами. Синхронизация с выдержкой интервала между кадрами подробно описана в разделе "Представление сцены" данного документа.

```cpp
// Ensure that DXGI does not queue more than one frame at a time. This both reduces  
// latency and ensures that the application will only render after each VSync, minimizing  
// power consumption.
DX::ThrowIfFailed(
    dxgiDevice->SetMaximumFrameLatency(1)
    );
```

Метод **DeviceResources::CreateWindowSizeDependentResources** инициализирует графические ресурсы таким образом, чтобы обеспечить работоспособность большинства игр.

> **Примечание**. Значение термина *представление* в среде выполнения Windows и в Direct3D различается. В среде выполнения Windows представление — это набор параметров пользовательского интерфейса для приложения, в том числе область отображения и возможные реакции на ввод, а также поток, используемый для обработки. При создании представления задаются нужные параметры и конфигурация. Процедура настройки представления приложения описана в разделе [Структура приложения Marble Maze](marble-maze-application-structure.md). В Direct3D термин "представление" имеет несколько значений. Во-первых, представление ресурса определяет подресурсы, доступные этому ресурсу. Например, если объект текстуры связан с представлением ресурса шейдера, то затем шейдер может получить доступ к текстуре. Преимуществом представления ресурса является возможность различной интерпретации данных на различных участках конвейера отрисовки. Дополнительные сведения о представлениях ресурсов см. в разделе о [представлениях текстур (Direct3D 10)](https://msdn.microsoft.com/library/windows/desktop/bb205128). Термин "представление" в контексте преобразования представления или матрицы преобразования представления означает расположение и ориентацию камеры. При преобразовании представления объекты мира перемещаются относительно положения и ориентации камеры. Дополнительные сведения о преобразованиях представлений см. в разделе о [преобразовании представления (Direct3D 9)](https://msdn.microsoft.com/library/windows/desktop/bb206342). Использование представлений ресурсов и матричных представлений в Marble подробно описано в этом разделе.

 

## <a name="loading-scene-resources"></a>Загрузка ресурсов сцены


Marble Maze использует класс **BasicLoader**, объявленный в файле BasicLoader.h, для загрузки текстур и шейдеров. Marble Maze использует класс **SDKMesh** для загрузки трехмерных сеток для лабиринта и шарика.

Чтобы обеспечить хороший отклик приложения, Marble Maze загружает ресурсы сцены асинхронно, в фоновом режиме. Пока ресурсы загружаются в фоновом режиме, игра может отвечать на события окон. Этот процесс подробно описан в разделе [Загрузка ресурсов игры в фоновом режиме](marble-maze-application-structure.md#loading-game-assets-in-the-background) данного руководства.

###  <a name="loading-the-2-d-overlay-and-user-interface"></a>Загрузка двухмерного наложения и пользовательского интерфейса

В Marble Maze наложение — это изображение, которое отображается в верхней части экрана. Наложение всегда располагается перед сценой. В Marble Maze наложение содержит логотип Windows и текстовую строку "Образец игры DirectX Marble Maze". Управление наложением выполняется классом **SampleOverlay**, который определяется в файле SampleOverlay.h. Наложение используется в составе образцов Direct3D, но можно адаптировать этот код для любого изображения, находящегося перед сценой.

Важным аспектом наложения является постоянство его содержимого. Поэтому класс **SampleOverlay** рисует (кэширует) содержимое наложения в объект [**ID2D1Bitmap1**](https://msdn.microsoft.com/library/windows/desktop/hh404349) во время инициализации. Во время рисования классу **SampleOverlay** остается только нарисовать на экране растровое изображение. Это позволяет не выполнять в каждом кадре ресурсоемкие подпрограммы, например для рисования текста.

Пользовательский интерфейс состоит из двухмерных компонентов (меню, игровых индикаторов HUD и др.), которые располагаются перед сценой. В Marble Maze определяются следующие элементы пользовательского интерфейса:

-   пункты меню, которые позволяют пользователю начать игру или просмотреть таблицу результатов,
-   таймер, отсчитывающий время (три секунды) до начала игры,
-   таймер, который показывает истекшее время игры,
-   таблица, в которой показаны лучшие значения времени прохождения,
-   текст "Paused", который отображается, когда игра приостановлена.

Элементы пользовательского интерфейса игры Marble Maze определяются в файле UserInterface.h. В Marble Maze класс **ElementBase** определяется в качестве базового типа для всех элементов пользовательского интерфейса. В классе **ElementBase** определены такие атрибуты элемента пользовательского интерфейса, как размер, положение, выравнивание и видимость. Он также управляет обновлением и отрисовкой элементов.

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

> **Примечание**. Поскольку язык XAML упрощает создание сложного пользовательского интерфейса, который встречается в симуляторах и стратегических играх, подумайте, стоит ли определять интерфейс вашей игры на XAML. Сведения о разработке пользовательского интерфейса на XAML в игре UWP с использованием DirectX см. в разделе [Расширение образца игры (Windows)](tutorial-resources.md). В этом документе рассматривается образец трехмерной игры-шутера DirectX.

 

###  <a name="loading-shaders"></a>Загрузка построителей текстур

В Marble Maze используется метод **BasicLoader::LoadShader** для загрузки шейдера из файла.

В современных играх шейдеры являются неотъемлемой частью программирования GPU. Практически вся обработка трехмерной графики управляется шейдерами — преобразование моделей, освещение сцены, а также более сложная обработка геометрии, включая привязку текстур к персонажам (скиннинг) и тесселяцию. Дополнительные сведения о модели программирования шейдеров см. в разделе [HLSL](https://msdn.microsoft.com/library/windows/desktop/bb509561).

В Marble Maze используются вершинные построители текстуры и шейдеры пикселей. Вершинный шейдер всегда работает с одной входной вершиной, и в результате дает одну вершину. Шейдер пикселей принимает числовые значения, данные текстуры, интерполированные значения по вершинам и другие данные, а в результате дает цвет пикселя. Поскольку шейдер преобразует элементы по одному, графическое оборудование с несколькими шейдерными конвейерами может параллельно обрабатывать наборы элементов. Число параллельных конвейеров, доступных GPU, может значительно превышать число, доступное ЦП. Поэтому даже простые шейдеры могут значительно повысить пропускную способность.

Метод **MarbleMaze::LoadDeferredResources** после загрузки наложения загружает один вершинный шейдер и один шейдер пикселей. Версии времени разработки этих шейдеров определены соответственно в файлах BasicVertexShader.hlsl и BasicPixelShader.hlsl. Marble Maze применяет эти шейдеры к шарику и лабиринту на этапе отрисовки.

В проект Marble Maze входят обе версии файлов шейдеров (и формат HLSL для времени разработки, и формат CSO для времени выполнения). Во время сборки Visual Studio использует компилятор эффектов fxc.exe для компиляции HLSL-файла исходного кода в двоичный CSO-файл шейдера. Дополнительные сведения о компиляторе эффектов см. в разделе о [средстве компиляции эффектов](https://msdn.microsoft.com/library/windows/desktop/bb232919).

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

> **Осторожно!** Скомпилированный шейдер пикселей содержит 32 арифметические инструкции и одну инструкцию текстуры. Этот шейдер должен обеспечивать высокую производительность на настольных компьютерах и планшетах высшего класса. Однако на менее мощном компьютере, где обработка этого шейдера невозможна, все же можно добиться частоты кадров, обеспечивающей интерактивность. Изучите типичное оборудование целевой аудитории своей игры и разрабатывайте шейдеры с учетом возможностей этого оборудования.

 

Метод **MarbleMaze::LoadDeferredResources** использует метод **BasicLoader::LoadShader** для загрузки шейдеров. В следующем примере загружается вершинный шейдер. Версия этого шейдера в формате времени выполнения находится в файле BasicVertexShader.cso. Переменная-член **m\_vertexShader** представляет собой объект [**ID3D11VertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476641).

```cpp
\loader->LoadShader(
    L"BasicVertexShader.cso",
    layoutDesc,
    ARRAYSIZE(layoutDesc),
    &m_vertexShader,
    &m_inputLayout
    );
```

Переменная-член **m\_inputLayout** представляет собой объект [**ID3D11InputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476575). В объекте макета входных данных инкапсулируется входное состояние этапа сборки входных данных (IA). Одной из задач этапа IA является повышение эффективности шейдеров за счет использования значений, созданных системой (они также называются *семантикой*), для обработки исключительно еще не обработанных примитивов и вершин. Используйте метод [**ID3D11Device::CreateInputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476512) для создания макета входных данных из массива описаний входных элементов. Этот массив содержит один или несколько входных элементов. Каждый входной элемент описывает один элемент vertex-data из одного буфера вершин. Весь набор описаний input-element описывает все элементы vertex-data из всех буферов вершин, которые будут привязаны на этапе IA. В следующем примере показано описание макета, используемое в игре Marble Maze. Описание макета описывает буфер вершин, который содержит четыре элемента vertex-data. Важными частями каждого элемента массива являются имя семантики, формат данных и смещение в байтах. Например, элемент **POSITION** задает положение вершины в пространстве объектов. Он начинается со смещения в 0 байт и содержит три компонента с плавающей запятой (всего 12 байт). Элемент **NORMAL** задает нормальный вектор. Он начинается со смещения в 12 байт, поскольку идет в макете сразу после элемента **POSITION**, которому необходимо 12 байт. Элемент **NORMAL** содержит четырехкомпонентное 32-разрядное целое число без знака.

```cpp
D3D11_INPUT_ELEMENT_DESC layoutDesc[] = 
{
    { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0,  D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "NORMAL",   0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "TEXCOORD",  0, DXGI_FORMAT_R32G32_FLOAT,   0, 24, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "TANGENT", 0, DXGI_FORMAT_R32G32B32_FLOAT,  0, 32, D3D11_INPUT_PER_VERTEX_DATA, 0 }, 
};
m_vertexStride = 44; // You must set this to match the size of layoutDesc above.
```

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

В документе [Семантики](https://msdn.microsoft.com/library/windows/desktop/bb509647) подробно описана каждая из доступных семантик.

> **Примечание**. В макете можно указать дополнительные неиспользуемые компоненты, чтобы разрешить нескольким шейдерам совместно использовать тот же макет. Например, элемент **TANGENT** не используется шейдером. Можно использовать элемент **TANGENT**, если хотите проверить какие-либо приемы, например сопоставление нормали. С помощью сопоставления нормали, которое также называют отображением элементов рельефа, вы можете создавать эффект выпуклостей на поверхностях объектов. Дополнительные сведения см. в статье об [отображении элементов рельефа (Direct3D 9)](https://msdn.microsoft.com/library/windows/desktop/bb172379).

 

Дополнительные сведения см. в статьях о [стадии сборщика входных данных](https://msdn.microsoft.com/library/windows/desktop/bb205116) и [начале работы на стадии сборщика входных данных](https://msdn.microsoft.com/library/windows/desktop/bb205117).

Процесс использования вершинных шейдеров и построителей текстуры для отрисовки сцены описан в разделе [Отрисовка сцены](#rendering-the-scene) ниже в этом документе.

### <a name="creating-the-constant-buffer"></a>Создание буферов констант

Буфер Direct3D группирует коллекцию данных. Буфер констант является типом буфера, который можно использовать, чтобы передавать данные в шейдеры. В Marble Maze используется буфер констант, чтобы удерживать представление модели (или мира), а также матрицы проекции для активного объекта сцены.

Следующий пример демонстрирует, как метод **MarbleMaze::LoadDeferredResources** создает буфер констант, который в дальнейшем будет содержать данные матриц. В примере создается структура **D3D11\_BUFFER\_DESC**, использующая флаг **D3D11\_BIND\_CONSTANT\_BUFFER**, чтобы задать использование в качестве буфера констант. Затем в этом примере структура передается в метод [**ID3D11Device::CreateBuffer**](https://msdn.microsoft.com/library/windows/desktop/ff476501). Переменная **m\_constantBuffer** представляет собой объект [**ID3D11Buffer**](https://msdn.microsoft.com/library/windows/desktop/ff476351).

```cpp
// Create the constant buffer for updating model and camera data.
D3D11_BUFFER_DESC constantBufferDesc = {0};
constantBufferDesc.ByteWidth           = ((sizeof(ConstantBuffer) + 15) / 16) * 16; // Multiple of 16 bytes
constantBufferDesc.Usage               = D3D11_USAGE_DEFAULT;
constantBufferDesc.BindFlags           = D3D11_BIND_CONSTANT_BUFFER;
constantBufferDesc.CPUAccessFlags      = 0;
constantBufferDesc.MiscFlags           = 0;
// This will not be used as a structured buffer, so this parameter is ignored.
constantBufferDesc.StructureByteStride = 0;

DX::ThrowIfFailed(
    m_d3dDevice->CreateBuffer(
        &constantBufferDesc,
        nullptr,             // Leave the buffer uninitialized.
        &m_constantBuffer
        )
    );
```

Затем метод **MarbleMaze::Update** обновляет объекты **ConstantBuffer**: один— для лабиринта, а другой— для шарика. Затем метод **MarbleMaze::Render** привязывает каждый объект **ConstantBuffer** к буферу констант до отрисовки каждого объекта. В следующем примере показана структура **ConstantBuffer**, которая находится в MarbleMaze.h.

```cpp
// Describes the constant buffer that draws the meshes.
struct ConstantBuffer
{
    float4x4 model;
    float4x4 view;
    float4x4 projection;

    float3 marblePosition;
    float marbleRadius;
    float lightStrength;
};
```

Чтобы лучше понять, как буферы констант сопоставляются с кодом шейдера, сравним структуру **ConstantBuffer** с буфером констант **SimpleConstantBuffer**, который определяется вершинным шейдером в файле BasicVertexShader.hlsl:

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

макет структуры **ConstantBuffer** соответствует объекту **cbuffer**. Переменная **cbuffer** указывает регистр b0. Это означает, что данные буфера констант сохраняются в регистре 0. Метод **MarbleMaze::Render** задает регистр 0, когда активирует буфер констант. Этот процесс подробно описан ниже в данном документе.

Дополнительные сведения о буферах констант см. во [вводной статье о буферах Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/ff476898). Дополнительные сведения о ключевом слове register см. в описании [**register**](https://msdn.microsoft.com/library/windows/desktop/dd607359).

###  <a name="loading-meshes"></a>Загрузка сеток

Marble Maze использует SDK-Mesh как формат времени выполнения, поскольку этот формат предоставляет простой способ загрузки данных сетки для образцов приложений. Для использования в производстве вы должны применять формат сетки, отвечающий конкретным требованиям вашей игры.

Метод **MarbleMaze::LoadDeferredResources** загружает данные сетки после того, как загрузит вершинный шейдер и шейдер пикселей. Сетка является коллекцией данных вершин, в которые часто включают такую информацию, как положения, данные нормали, цвета, материалы и координаты текстуры. Сетки обычно создаются в программах трехмерной разработки и содержатся в файлах отдельно от кода приложения. Шарик и лабиринт могут служить двумя примерами сеток, которые используются в игре.

В Marble Maze для управления сетками используется класс **SDKMesh**. Этот класс объявляется в файле SDKMesh.h. **SDKMesh** предоставляет методы для загрузки, отрисовки и уничтожения данных сетки.

> **Важно**! В Marble Maze используется формат SDK-Mesh и предоставляется класс **SDKMesh** только для иллюстрации. Хотя формат SDK-Mesh удобен для обучения и создания прототипов, это очень простой формат, который может не отвечать требованиям большинства игр. Мы рекомендуем использовать формат сетки, отвечающий конкретным требованиям вашей игры.

 

Следующий пример показывает, как метод **MarbleMaze::LoadDeferredResources** использует метод **SDKMesh::Create**, чтобы загружать данные сетки для лабиринта и для шарика.

```cpp
// Load the meshes.
DX::ThrowIfFailed(
    m_mazeMesh.Create(
        m_d3dDevice.Get(),
        L"Media\\Models\\maze1.sdkmesh",
        false
        )
    );

DX::ThrowIfFailed(
    m_marbleMesh.Create(
        m_d3dDevice.Get(),
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

Способ загрузки данных столкновения в основном зависит от используемого формата во время выполнения. Дополнительные сведения о том, как Marble Maze загружает геометрию столкновения из файла SDK-Mesh, см. в методе **MarbleMaze::ExtractTrianglesFromMesh** в исходном коде.

## <a name="updating-game-state"></a>Обновление состояния игры


Marble Maze отделяет логику игры от логики отрисовки, предварительно обновляя все объекты сцены перед их отрисовкой.

Документированная структура приложения Marble Maze описывает основной цикл игры. Обновление сцены, являющееся частью цикла игры, имеет место после обработки событий Windows и входных данных, но до отрисовки сцены. Метод **MarbleMaze::Update** обрабатывает обновление пользовательского интерфейса и игры.

### <a name="updating-the-user-interface"></a>Обновление пользовательского интерфейса

Метод **MarbleMaze::Update** вызывает метод **UserInterface::Update** для обновления состояния пользовательского интерфейса.

```cpp
UserInterface::GetInstance().Update(timeTotal, timeDelta);
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

Классы, являющиеся производными от **ElementBase**, реализуют метод **Update** для выполнения конкретных взаимодействий. Например, метод **StopwatchTimer::Update** обновляет затраченное время на предоставленную величину и обновляет текст, который отображается после этого.

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

Метод **MarbleMaze::Update** обновляет игру на основании текущего состояния конечного автомата. Когда игра находится в активном состоянии, Marble Maze обновляет камеру, чтобы следовать за шариком, обновляет относящуюся к матрице представления часть буферов констант, а также обновляет физическое моделирование.

Следующий пример показывает, как метод **MarbleMaze::Update** обновляет положение камеры. Marble Maze использует переменную **m\_resetCamera** как флажок, показывающий, что камера должна быть сброшена, чтобы она располагалась непосредственно над шариком. Камера сбрасывается, когда запускается игра или когда шарик падает сквозь лабиринт. Когда активны главное меню или таблица результатов, камера остается в постоянном положении. В противоположном случае Marble Maze использует параметр *timeDelta*, чтобы интерполировать позицию камеры между текущим и целевым положением. Целевое положение находится чуть выше и впереди шарика. Использование прошедшего времени кадра позволяет камере постоянно следовать за шариком, т.е. преследовать его.

```cpp
static float eyeDistance = 200.0f;
static float3 eyePosition = float3(0, 0, 0);

// Gradually move the camera above the marble.
float3 targetEyePosition = marblePosition - (eyeDistance * float3(g.x, g.y, g.z));
if (m_resetCamera)
{
    eyePosition = targetEyePosition;
    m_resetCamera = false;
}
else
{
    eyePosition = eyePosition + ((targetEyePosition - eyePosition) * min(1, timeDelta * 8));
}

// Look at the marble. 
if ((m_gameState == GameState::MainMenu) || (m_gameState == GameState::HighScoreDisplay))
{
    // Override camera position for menus.
    eyePosition = marblePosition + float3(75.0f, -150.0f, -75.0f);
    m_camera->SetViewParameters(eyePosition, marblePosition, float3(0.0f, 0.0f, -1.0f));
}
else
{
    m_camera->SetViewParameters(eyePosition, marblePosition, float3(0.0f, 1.0f, 0.0f));
}
```

Следующий пример показывает, как метод **MarbleMaze::Update** обновляет буферы констант для шарика и лабиринта. Матрица модели лабиринта, или мира, всегда остается матрицей тождественного преобразования. Матрица тождественного преобразования является квадратной матрицей, в которой элементы на главной диагонали являются единицами, а все остальные элементы являются нулями. Матрица модели шарика рассчитывается как произведение матрицы его положения на матрицу его поворота. Функции **mul** и **translation** определены в файле BasicMath.h.

```cpp
// Update the model matrices based on the simulation.
m_mazeConstantBufferData.model = identity();
m_marbleConstantBufferData.model = mul(
    translation(marblePosition.x, marblePosition.y, marblePosition.z),
    marbleRotationMatrix
    );

// Update the view matrix based on the camera.
float4x4 view;
m_camera->GetViewMatrix(&view);
m_mazeConstantBufferData.view = view;
m_marbleConstantBufferData.view = view;
```

Сведения о том, как метод **MarbleMaze::Update** читает вводящиеся пользователем данные и моделирует движение шарика, см. в статье [Добавление средств ввода и взаимодействия в пример Marble Maze](adding-input-and-interactivity-to-the-marble-maze-sample.md).

## <a name="rendering-the-scene"></a>Отрисовка сцены


Обычно отрисовка сцены включает следующие шаги.

1.  Задание текущего буфера трафарета глубины для цели отрисовки.
2.  Очистка представлений отрисовки и трафарета глубины.
3.  Подготовка вершинного шейдера и шейдера пикселей для рисования.
4.  Отрисовка трехмерных объектов в сцене.
5.  Отрисовка любого двухмерного объекта, который должен появляться перед сценой.
6.  Вывод отрисованного изображения на монитор.

Метод **MarbleMaze::Render** привязывает целевое представление цели отрисовки и представление трафарета глубины, очищает эти представления, рисует сцену, а затем рисует наложение.

###  <a name="preparing-the-render-targets"></a>Подготовка целевых объектов отрисовки

Перед отрисовкой сцены необходимо задать текущий буфер трафарета глубины для цели отрисовки. Если ваша сцена не займет каждый пиксель на экране, необходимо также очистить представления отрисовки и трафарета глубины. Marble Maze очищает представления отрисовки и трафарета глубины для каждого кадра, чтобы на экране не оставались объекты от предыдущего кадра.

В следующем примере показано, как метод **MarbleMaze::Render** вызывает метод [**ID3D11DeviceContext::OMSetRenderTargets**](https://msdn.microsoft.com/library/windows/desktop/ff476464), чтобы задать целевой объект отрисовки и буфер трафарета глубины в качестве текущих. Переменная-член **m\_renderTargetView** (объект [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582)) и переменная-член **m\_depthStencilView** (объект [**ID3D11DepthStencilView**](https://msdn.microsoft.com/library/windows/desktop/ff476377)) определяются и инициализируются классом **DirectXBase**.

```cpp
// Bind the render targets.
m_d3dContext->OMSetRenderTargets(
    1,
    m_renderTargetView.GetAddressOf(),
    m_depthStencilView.Get()
    );

// Clear the render target and depth stencil to default values. 
const float clearColor[4] = { 0.0f, 0.0f, 0.0f, 1.0f };

m_d3dContext->ClearRenderTargetView(
    m_renderTargetView.Get(),
    clearColor
    );

m_d3dContext->ClearDepthStencilView(
    m_depthStencilView.Get(),
    D3D11_CLEAR_DEPTH,
    1.0f,
    0
    );
```

Интерфейсы [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582) и [**ID3D11DepthStencilView**](https://msdn.microsoft.com/library/windows/desktop/ff476377) поддерживают механизм представления текстуры, который предоставляется в Direct3D 10 и более поздних версиях. Дополнительные сведения см. в статье о [представлениях текстуры (Direct3D 10)](https://msdn.microsoft.com/library/windows/desktop/bb205128). Метод [**OMSetRenderTargets**](https://msdn.microsoft.com/library/windows/desktop/ff476464) подготавливает стадию слияния и вывода для конвейера Direct3D. Дополнительные сведения см. в [описании стадии слияния и вывода](https://msdn.microsoft.com/library/windows/desktop/bb205120).

### <a name="preparing-the-vertex-and-pixel-shaders"></a>Подготовка вершинного шейдера и шейдеров пикселей

Перед отрисовкой объектов сцены выполните следующие шаги, чтобы подготовить вершинный шейдер и шейдер пикселей для рисования.

1.  Задание входного макета шейдера в качестве текущего макета.
2.  Задание вершинного шейдера и шейдера пикселей в качестве текущих шейдеров.
3.  Обновление во всех буферах констант, содержащих данные, которые должны передаваться в шейдеры.

> **Важно**! В Marble Maze для всех трехмерных объектов используется одна пара шейдеров: вершинный шейдер и шейдер пикселей. Если ваша игра использует несколько пар шейдеров, необходимо выполнять эти шаги каждый раз при рисовании объектов, для которых используются другие шейдеры. Чтобы уменьшить служебные данные, связанные с изменением состояний шейдеров, мы рекомендуем группировать вызовы для всех объектов, которые используют те же шейдеры.

 

В разделе [Загрузка шейдеров](#loading-shaders) в этом документе описано, как создается входной макет при создании вершинного шейдера. В следующем примере показано, как метод **MarbleMaze::Render** использует метод [**ID3D11DeviceContext::IASetInputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476454), чтобы задать данный макет в качестве текущего.

```cpp
m_d3dContext->IASetInputLayout(m_inputLayout.Get());
```

В следующем примере показано, как метод **MarbleMaze::Render** использует методы [**ID3D11DeviceContext::VSSetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476493) и [**ID3D11DeviceContext::PSSetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476472), чтобы задать вершинный шейдер и шейдер пикселей в качестве текущих шейдеров соответственно.

```cpp
// Set the vertex shader stage state.
m_d3dContext->VSSetShader(
    m_vertexShader.Get(),   // Use this vertex shader. 
    nullptr,                // Don't use shader linkage.
    0                       // Don't use shader linkage.
    );

// Set the pixel shader stage state.
m_d3dContext->PSSetShader(
    m_pixelShader.Get(),    // Use this pixel shader. 
    nullptr,                // Don't use shader linkage.
    0                       // Don't use shader linkage.
    );

m_d3dContext->PSSetSamplers(
    0,                       // Starting at the first sampler slot
    1,                       // set one sampler binding
    m_sampler.GetAddressOf() // to use this sampler.
    );
```

После того как метод **MarbleMaze::Render** задает шейдеры и входной макет для них, он использует метод [**ID3D11DeviceContext::UpdateSubresource**](https://msdn.microsoft.com/library/windows/desktop/ff476486), чтобы обновить в буфере констант матрицы модели, представления и проекций для лабиринта. Метод **UpdateSubresource** копирует данные матриц из памяти ЦП в память GPU. Не забывайте, что компоненты модели и представления в структуре **ConstantBuffer** обновляются в методе **MarbleMaze::Update**. Метод **MarbleMaze::Render** затем вызывает методы [**ID3D11DeviceContext::VSSetConstantBuffers**](https://msdn.microsoft.com/library/windows/desktop/ff476491) и [**ID3D11DeviceContext::PSSetConstantBuffers**](https://msdn.microsoft.com/library/windows/desktop/ff476470), чтобы задать этот буфер констант в качестве текущего.

```cpp
// Update the constant buffer with the new data.
m_d3dContext->UpdateSubresource(
    m_constantBuffer.Get(),
    0,
    nullptr,
    &m_mazeConstantBufferData,
    0,
    0
    );

m_d3dContext->VSSetConstantBuffers(
    0,                // Starting at the first constant buffer slot
    1,                // set one constant buffer binding
    m_constantBuffer.GetAddressOf() // to use this buffer.
    );

m_d3dContext->PSSetConstantBuffers(
    0,                // Starting at the first constant buffer slot
    1,                // set one constant buffer binding
    m_constantBuffer.GetAddressOf() // to use this buffer.
    );
```

Метод **MarbleMaze::Render** выполняет аналогичные шаги для подготовки отрисовки шарика.

### <a name="rendering-the-maze-and-the-marble"></a>Отрисовка лабиринта и шарика

После активации текущих шейдеров можно рисовать объекты сцены. Метод **MarbleMaze::Render** вызывает метод **SDKMesh::Render** для отрисовки сетки лабиринта.

```cpp
m_mazeMesh.Render(m_d3dContext.Get(), 0, INVALID_SAMPLER_SLOT, INVALID_SAMPLER_SLOT);
```

Метод **MarbleMaze::Render** выполняет аналогичные шаги для отрисовки шарика.

Как отмечено в этом документе, класс **SDKMesh** предоставляется в целях демонстрации. Мы не рекомендуем использовать его в игре производственного качества. Однако обратите внимание, что метод **SDKMesh::RenderMesh**, вызываемый методом **SDKMesh::Render**, использует методы [**ID3D11DeviceContext::IASetVertexBuffers**](https://msdn.microsoft.com/library/windows/desktop/ff476456) и [**ID3D11DeviceContext::IASetIndexBuffer**](https://msdn.microsoft.com/library/windows/desktop/ff476453), чтобы задать текущие буферы вершин и индексов, которые определяют сетку, и метод [**ID3D11DeviceContext::DrawIndexed**](https://msdn.microsoft.com/library/windows/desktop/ff476410), чтобы нарисовать буферы. Дополнительные сведения о работе с буферами вершин и индексов см. во [вводной статье о буферах в Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/ff476898).

### <a name="drawing-the-user-interface-and-overlay"></a>Рисование пользовательского интерфейса и наложения

После рисования трехмерных объектов сцены Marble Maze рисует двухмерные элементы пользовательского интерфейса, которые отображаются перед сценой.

Выполнение метода **MarbleMaze::Render** заканчивается рисованием пользовательского интерфейса и наложения.

```cpp
// Draw the user interface and the overlay.
UserInterface::GetInstance().Render();

m_sampleOverlay->Render();
```

Метод **UserInterface::Render** использует объект [**ID2D1DeviceContext**](https://msdn.microsoft.com/library/windows/desktop/hh404479) для рисования элементов пользовательского интерфейса. Этот метод задает состояние рисования, рисует все активные элементы пользовательского интерфейса, а затем восстанавливает предыдущее состояние рисования.

```cpp
void UserInterface::Render()
{
    m_d2dContext->SaveDrawingState(m_stateBlock.Get());
    m_d2dContext->BeginDraw();
    m_d2dContext->SetTransform(D2D1::Matrix3x2F::Identity());
    m_d2dContext->SetTextAntialiasMode(D2D1_TEXT_ANTIALIAS_MODE_GRAYSCALE);

    for (auto iter = m_elements.begin(); iter != m_elements.end(); ++iter)
    {
        if ((*iter)->IsVisible())
            (*iter)->Render();
    }

    m_d2dContext->EndDraw();
    m_d2dContext->RestoreDrawingState(m_stateBlock.Get());
}
```

Метод **SampleOverlay::Render** использует аналогичные приемы, чтобы рисовать точечный рисунок наложения.

###  <a name="presenting-the-scene"></a>Презентация сцены

После рисования двумерных и трехмерных объектов сцены Marble Maze выводит отрисованное изображение на монитор. Рисунок синхронизируется с пробелом по вертикали, чтобы гарантировать, что время, не потраченное на рисование кадров, никогда не отображалось на дисплее. Marble Maze во время презентации сцены также обрабатывает смену устройств.

После возвращения результатов метода **MarbleMaze::Render** игровой цикл вызывает метод **MarbleMaze::Present** для отправки отрисованного изображения на монитор или дисплей. Класс **MarbleMaze** не переопределяет метод **DirectXBase::Present**. Метод **DirectXBase::Present** вызывает метод [**IDXGISwapChain1::Present**](https://msdn.microsoft.com/library/windows/desktop/hh446797), чтобы выполнить текущую операцию, как показано в следующем примере.

```cpp
// The application may optionally specify "dirty" or "scroll" rects 
// to improve efficiency in certain scenarios. 
// In this sample, however, we do not utilize those features.
DXGI_PRESENT_PARAMETERS parameters = {0};
parameters.DirtyRectsCount = 0;
parameters.pDirtyRects = nullptr;
parameters.pScrollRect = nullptr;
parameters.pScrollOffset = nullptr;

// The first argument instructs DXGI to block until VSync, putting the  
// application to sleep until the next VSync.  
// This ensures we don't waste any cycles rendering frames that will  
// never be displayed to the screen.
HRESULT hr = m_swapChain->Present1(1, 0, &parameters);
```

В этом примере **m\_swapChain** является объектом [**IDXGISwapChain1**](https://msdn.microsoft.com/library/windows/desktop/hh404631). Инициализация этого объекта описана в разделе [Инициализация Direct3D и Direct2D](#initializing-direct3d-and-direct2d) в настоящем документе.

Первый параметр метода [**IDXGISwapChain1::Present**](https://msdn.microsoft.com/library/windows/desktop/hh446797) (*SyncInterval*) задает число пробелов по вертикали перед выводом кадра. Marble Maze указывает 1, т.е. ожидание до следующего пробела по вертикали. Пробел по вертикали представляет время между завершением рисования на мониторе одного кадра и началом рисования следующего кадра.

Метод [**IDXGISwapChain1::Present1**](https://msdn.microsoft.com/library/windows/desktop/hh446797) возвращает код ошибки, указывающий, что устройство было удалено или испытало какой-либо сбой. В этом случае Marble Maze повторно инициализирует устройство.

```cpp
// Reinitialize the renderer if the device was disconnected  
// or the driver was upgraded. 
if (hr == DXGI_ERROR_DEVICE_REMOVED || hr == DXGI_ERROR_DEVICE_RESET)
{
    Initialize(m_window, m_dpi);
}
else
{
    DX::ThrowIfFailed(hr);
}
```

## <a name="next-steps"></a>Следующие действия


Прочитайте статью [Добавление средств ввода и взаимодействия в пример Marble Maze](adding-input-and-interactivity-to-the-marble-maze-sample.md), чтобы ознакомиться с основными приемами работы с устройствами ввода. В этом документе обсуждаются способы поддержки в Marble Maze сенсорного ввода, акселерометра, контроллера Xbox 360 и ввода с помощью мыши.

## <a name="related-topics"></a>Связанные темы


* [Добавление ввода и интерактивной функциональности в образец Marble Maze](adding-input-and-interactivity-to-the-marble-maze-sample.md)
* [Структура приложения Marble Maze](marble-maze-application-structure.md)
* [Разработка Marble Maze — игры UWP — на C++ и DirectX](developing-marble-maze-a-windows-store-game-in-cpp-and-directx.md)

 

 




