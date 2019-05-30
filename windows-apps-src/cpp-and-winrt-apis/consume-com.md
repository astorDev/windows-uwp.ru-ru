---
description: В этом разделе приведен полный пример кода Direct2D, чтобы показать, как использовать классы и интерфейсы COM с помощью C++/WinRT.
title: Использование COM-компонентов с помощью C++/WinRT
ms.date: 04/24/2019
ms.topic: article
keywords: Windows 10, uwp, standard, c ++, cpp, winrt, COM, компонента, класс, интерфейс
ms.localizationpriority: medium
ms.openlocfilehash: dc4acd288496d83d5d91f1bdf206be19fe2fbb06
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66361149"
---
# <a name="consume-com-components-with-cwinrt"></a>Использование COM-компонентов с помощью C++/WinRT

Можно использовать средства класса [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) библиотеки, использующих компоненты СОМ, например высокопроизводительной двумерной и трехмерной графики DirectX API-интерфейсы. C++/ WinRT — это самый простой способ использования DirectX без ущерба для производительности. В этом разделе используется Direct2D пример кода демонстрирует, как использовать C++/WinRT COM-классов и интерфейсов. Можно Конечно, смешивать программирование COM и среду выполнения Windows в одном C++/WinRT проекта.

В конце этого раздела вы найдете список полный исходный код минимального приложения Direct2D. Мы поднимите выдержки из этого кода и использовать их для демонстрации способов использования компонентов COM с помощью C++/WinRT, с помощью различных средств C++/WinRT библиотеки.

## <a name="com-smart-pointers-winrtcomptruwpcpp-ref-for-winrtcom-ptr"></a>COM smart pointers ([**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr))

При программировании с помощью COM, работа напрямую с интерфейсами, а не с объектами (которые также true незаметно для API среды выполнения Windows, которые являются развитием службы COM). Чтобы вызвать функцию класс COM, например, активации класса, получить интерфейс обратно, а затем вызывать функции в этом интерфейсе. Чтобы оценить состояние объекта, у вас доступа его элементов данных напрямую. Вместо этого вызовите функции метода доступа и мутатора в интерфейсе.

Точнее, мы говорим о взаимодействии с интерфейсом *указатели*. А для этого мы преимущества существования COM-типа интеллектуального указателя в C++/WinRT&mdash; [ **winrt::com_ptr** ](/uwp/cpp-ref-for-winrt/com-ptr) типа.

```cppwinrt
#include <d2d1_1.h>
...
winrt::com_ptr<ID2D1Factory1> factory;
```

В приведенном выше коде показан способ объявления неинициализированный смарт-указатель [ **ID2D1Factory1** ](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1factory1) COM-интерфейса. Интеллектуальный указатель не инициализирован, поэтому он еще не указывает на **ID2D1Factory1** интерфейс, принадлежащих любой реальный объект (он не указывает на интерфейс вообще). Но у него есть возможность сделать это; и (будьте интеллектуального указателя) имеет возможность через ссылку COM, инвентаризации для управления временем жизни объекта-владельца интерфейса, который он указывает, а на носителе, на который вы вызываете функции в этом интерфейсе.

## <a name="com-functions-that-return-an-interface-pointer-as-void"></a>Функции COM, возвращающие указатель интерфейса, как **void**

Можно вызвать [ **com_ptr::put_void** ](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput_void-function) основного необработанный указатель функции для записи в неинициализированное смарт-указатель.

```cppwinrt
D2D1_FACTORY_OPTIONS options{ D2D1_DEBUG_LEVEL_NONE };
D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    __uuidof(factory),
    &options,
    factory.put_void()
);
```

Приведенный выше вызовы [ **D2D1CreateFactory** ](/windows/desktop/api/d2d1/nf-d2d1-d2d1createfactory) функции, которая возвращает **ID2D1Factory1** указателя интерфейса с помощью последнего параметра, который имеет **void \* \***  типа. Многие функции COM возвращают **void\*\*** . Для таких функций, используйте [ **com_ptr::put_void** ](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput_void-function) как показано.

## <a name="com-functions-that-return-a-specific-interface-pointer"></a>Функции COM, возвращающие указатель определенного интерфейса

[ **D3D11CreateDevice** ](/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) возвращает [ **ID3D11Device** ](/windows/desktop/api/d3d11/nn-d3d11-id3d11device) указатель на интерфейс, через его параметр третий из последнего, имеющая **ID3D11Device\* \***  типа. Для функций, которые возвращают указатель определенного интерфейса, используйте [ **com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptr_put-function).

```cppwinrt
winrt::com_ptr<ID3D11Device> device;
D3D11CreateDevice(
    ...
    device.put(),
    ...);
```

В примере кода в разделе, прежде чем это показан способ вызова необработанный **D2D1CreateFactory** функции. Но на самом деле, а также при вызове в примере кода для этого раздела **D2D1CreateFactory**, он использует шаблон вспомогательные функции, который создает оболочку для необработанных API и поэтому фактически используется в примере кода [ **com_ptr::put** ](/uwp/cpp-ref-for-winrt/com-ptr#com_ptr_put-function).

```cppwinrt
winrt::com_ptr<ID2D1Factory1> factory;
D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    options,
    factory.put());
```

## <a name="com-functions-that-return-an-interface-pointer-as-iunknown"></a>Функции COM, возвращающие указатель интерфейса, как **IUnknown**

[ **DWriteCreateFactory** ](/windows/desktop/api/dwrite/nf-dwrite-dwritecreatefactory) функция возвращает указатель на интерфейс фабрики DirectWrite с помощью последнего параметра, который имеет [ **IUnknown** ](https://docs.microsoft.com/windows/desktop/api/unknwn/nn-unknwn-iunknown)типа. Такие функции, используйте [ **com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptr_put-function), но повторно интерпретируемая приведения, чтобы **IUnknown**.

```cppwinrt
DWriteCreateFactory(
    DWRITE_FACTORY_TYPE_SHARED,
    __uuidof(dwriteFactory2),
    reinterpret_cast<IUnknown**>(dwriteFactory2.put()));
```

## <a name="re-seat-a-winrtcomptr"></a>Re-seat a **winrt::com_ptr**

> [!IMPORTANT]
> Если у вас есть [ **winrt::com_ptr** ](/uwp/cpp-ref-for-winrt/com-ptr) , уже установлен (его внутренней необработанный указатель уже задан целевой объект) и вы хотите повторно рабочих мест, чтобы она указывала на другой объект, то необходимо сначала назначить `nullptr` к нему&mdash;как показано в следующем примере кода. Если этого не сделать, затем уже — сидели **com_ptr** рисующий проблему для вашего внимания (при вызове [ **com_ptr::put** ](/uwp/cpp-ref-for-winrt/com-ptr#com_ptr_put-function) или [ **com_ptr:: put_void**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput_void-function)), утверждая, что его внутренний указатель не равен null.

```cppwinrt
winrt::com_ptr<ID2D1SolidColorBrush> brush;
...
    brush.put()
...
brush = nullptr; // Important because we're about to re-seat
target->CreateSolidColorBrush(
    color_orange,
    D2D1::BrushProperties(0.8f),
    brush.put()));
```

## <a name="handle-hresult-error-codes"></a>Обрабатывает коды ошибок HRESULT

Чтобы проверить, возвращается значение HRESULT из COM-функции и исключение в случае, если оно представляет код ошибки, вызовите [ **winrt::check_hresult**](/uwp/cpp-ref-for-winrt/error-handling/check-hresult).

```cppwinrt
winrt::check_hresult(D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    __uuidof(factory),
    options,
    factory.put_void()));
```

## <a name="com-functions-that-take-a-specific-interface-pointer"></a>Функции COM, которые принимают указатель определенного интерфейса

Можно вызвать [ **com_ptr::get** ](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrget-function) функцию для передачи вашей **com_ptr** на функцию, которая принимает определенный интерфейс указатель того же типа.

```cppwinrt
... ExampleFunction(
    winrt::com_ptr<ID2D1Factory1> const& factory,
    winrt::com_ptr<IDXGIDevice> const& dxdevice)
{
    ...
    winrt::check_hresult(factory->CreateDevice(dxdevice.get(), ...));
    ...
}
```

## <a name="com-functions-that-take-an-iunknown-interface-pointer"></a>COM-функций, принимающих **IUnknown** указатель на интерфейс

Можно вызвать [ **winrt::get_unknown** ](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#get_unknown-function) свободная функция для передачи вашей **com_ptr** на функцию, которая принимает **IUnknown** интерфейса указатель.

```cppwinrt
winrt::check_hresult(factory->CreateSwapChainForCoreWindow(
    ...
    winrt::get_unknown(CoreWindow::GetForCurrentThread()),
    ...));
```

## <a name="passing-and-returning-com-smart-pointers"></a>Передача и возврат COM, смарт-указатели

Функцию, принимающую интеллектуальном указателе COM в виде **winrt::com_ptr** следует делать с постоянной ссылки или по ссылке.

```cppwinrt
... GetDxgiFactory(winrt::com_ptr<ID3D11Device> const& device) ...

... CreateDevice(..., winrt::com_ptr<ID3D11Device>& device) ...
```

Функция, возвращающая **winrt::com_ptr** следует делать по значению.

```cppwinrt
winrt::com_ptr<ID2D1Factory1> CreateFactory() ...
```

## <a name="query-a-com-smart-pointer-for-a-different-interface"></a>Запросить смарт-указатель COM для другой интерфейс

Можно использовать [ **com_ptr::as** ](/uwp/cpp-ref-for-winrt/com-ptr#com_ptras-function) функция для запроса интеллектуальном указателе COM для другой интерфейс. Функция создает исключение, если операция запроса не завершится успешно.

```cppwinrt
void ExampleFunction(winrt::com_ptr<ID3D11Device> const& device)
{
    ...
    winrt::com_ptr<IDXGIDevice> const dxdevice{ device.as<IDXGIDevice>() };
    ...
}
```

Кроме того, с помощью [ **com_ptr::try_as**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrtry_as-function), который возвращает значение, которое можно проверить с `nullptr` чтобы увидеть, является ли запрос выполнен успешно.

## <a name="full-source-code-listing-of-a-minimal-direct2d-application"></a>Полный исходный код список минимального приложения Direct2D

Если вы хотите собрать и запустить этот образец исходного кода, а затем во-первых, в Visual Studio, создать новый **приложения Core (C++/WinRT)** . `Direct2D` является разумным имя для проекта, но можно присвоить файлу имя, но следует. Откройте `App.cpp`, удалить все его содержимое и вставьте в списке ниже.

```cppwinrt
#include "pch.h"
#include <d2d1_1.h>
#include <d3d11.h>
#include <dxgi1_2.h>
#include <winrt/Windows.Graphics.Display.h>

using namespace winrt;

using namespace Windows;
using namespace Windows::ApplicationModel::Core;
using namespace Windows::UI;
using namespace Windows::UI::Core;
using namespace Windows::Graphics::Display;

namespace
{
    winrt::com_ptr<ID2D1Factory1> CreateFactory()
    {
        D2D1_FACTORY_OPTIONS options{};

#ifdef _DEBUG
        options.debugLevel = D2D1_DEBUG_LEVEL_INFORMATION;
#endif

        winrt::com_ptr<ID2D1Factory1> factory;

        winrt::check_hresult(D2D1CreateFactory(
            D2D1_FACTORY_TYPE_SINGLE_THREADED,
            options,
            factory.put()));

        return factory;
    }

    HRESULT CreateDevice(D3D_DRIVER_TYPE const type, winrt::com_ptr<ID3D11Device>& device)
    {
        WINRT_ASSERT(!device);

        return D3D11CreateDevice(
            nullptr,
            type,
            nullptr,
            D3D11_CREATE_DEVICE_BGRA_SUPPORT,
            nullptr, 0,
            D3D11_SDK_VERSION,
            device.put(),
            nullptr,
            nullptr);
    }

    winrt::com_ptr<ID3D11Device> CreateDevice()
    {
        winrt::com_ptr<ID3D11Device> device;
        HRESULT hr{ CreateDevice(D3D_DRIVER_TYPE_HARDWARE, device) };

        if (DXGI_ERROR_UNSUPPORTED == hr)
        {
            hr = CreateDevice(D3D_DRIVER_TYPE_WARP, device);
        }

        winrt::check_hresult(hr);
        return device;
    }

    winrt::com_ptr<ID2D1DeviceContext> CreateRenderTarget(
        winrt::com_ptr<ID2D1Factory1> const& factory,
        winrt::com_ptr<ID3D11Device> const& device)
    {
        WINRT_ASSERT(factory);
        WINRT_ASSERT(device);

        winrt::com_ptr<IDXGIDevice> const dxdevice{ device.as<IDXGIDevice>() };

        winrt::com_ptr<ID2D1Device> d2device;
        winrt::check_hresult(factory->CreateDevice(dxdevice.get(), d2device.put()));

        winrt::com_ptr<ID2D1DeviceContext> target;
        winrt::check_hresult(d2device->CreateDeviceContext(D2D1_DEVICE_CONTEXT_OPTIONS_NONE, target.put()));
        return target;
    }

    winrt::com_ptr<IDXGIFactory2> GetDxgiFactory(winrt::com_ptr<ID3D11Device> const& device)
    {
        WINRT_ASSERT(device);

        winrt::com_ptr<IDXGIDevice> const dxdevice{ device.as<IDXGIDevice>() };

        winrt::com_ptr<IDXGIAdapter> adapter;
        winrt::check_hresult(dxdevice->GetAdapter(adapter.put()));

        winrt::com_ptr<IDXGIFactory2> factory;
        winrt::check_hresult(adapter->GetParent(__uuidof(factory), factory.put_void()));
        return factory;
    }

    void CreateDeviceSwapChainBitmap(
        winrt::com_ptr<IDXGISwapChain1> const& swapchain,
        winrt::com_ptr<ID2D1DeviceContext> const& target)
    {
        WINRT_ASSERT(swapchain);
        WINRT_ASSERT(target);

        winrt::com_ptr<IDXGISurface> surface;
        winrt::check_hresult(swapchain->GetBuffer(0, __uuidof(surface), surface.put_void()));

        D2D1_BITMAP_PROPERTIES1 const props{ D2D1::BitmapProperties1(
            D2D1_BITMAP_OPTIONS_TARGET | D2D1_BITMAP_OPTIONS_CANNOT_DRAW,
            D2D1::PixelFormat(DXGI_FORMAT_B8G8R8A8_UNORM, D2D1_ALPHA_MODE_IGNORE)) };

        winrt::com_ptr<ID2D1Bitmap1> bitmap;

        winrt::check_hresult(target->CreateBitmapFromDxgiSurface(surface.get(),
            props,
            bitmap.put()));

        target->SetTarget(bitmap.get());
    }

    winrt::com_ptr<IDXGISwapChain1> CreateSwapChainForCoreWindow(winrt::com_ptr<ID3D11Device> const& device)
    {
        WINRT_ASSERT(device);

        winrt::com_ptr<IDXGIFactory2> const factory{ GetDxgiFactory(device) };

        DXGI_SWAP_CHAIN_DESC1 props{};
        props.Format = DXGI_FORMAT_B8G8R8A8_UNORM;
        props.SampleDesc.Count = 1;
        props.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
        props.BufferCount = 2;
        props.SwapEffect = DXGI_SWAP_EFFECT_FLIP_SEQUENTIAL;

        winrt::com_ptr<IDXGISwapChain1> swapChain;

        winrt::check_hresult(factory->CreateSwapChainForCoreWindow(
            device.get(),
            winrt::get_unknown(CoreWindow::GetForCurrentThread()),
            &props,
            nullptr, // all or nothing
            swapChain.put()));

        return swapChain;
    }

    constexpr D2D1_COLOR_F color_white{ 1.0f,  1.0f,  1.0f,  1.0f };
    constexpr D2D1_COLOR_F color_orange{ 0.92f,  0.38f,  0.208f,  1.0f };
}

struct App : implements<App, IFrameworkViewSource, IFrameworkView>
{
    winrt::com_ptr<ID2D1Factory1> m_factory;
    winrt::com_ptr<ID2D1DeviceContext> m_target;
    winrt::com_ptr<IDXGISwapChain1> m_swapChain;
    winrt::com_ptr<ID2D1SolidColorBrush> m_brush;
    float m_dpi{};

    IFrameworkView CreateView()
    {
        return *this;
    }

    void Initialize(CoreApplicationView const&)
    {
    }

    void Load(hstring const&)
    {
        CoreWindow const window{ CoreWindow::GetForCurrentThread() };

        window.SizeChanged([&](auto&&...)
        {
            if (m_target)
            {
                ResizeSwapChainBitmap();
                Render();
            }
        });

        DisplayInformation const display{ DisplayInformation::GetForCurrentView() };
        m_dpi = display.LogicalDpi();

        display.DpiChanged([&](DisplayInformation const& display, IInspectable const&)
        {
            if (m_target)
            {
                m_dpi = display.LogicalDpi();
                m_target->SetDpi(m_dpi, m_dpi);
                CreateDeviceSizeResources();
                Render();
            }
        });

        m_factory = CreateFactory();
        CreateDeviceIndependentResources();
    }

    void Uninitialize()
    {
    }

    void Run()
    {
        CoreWindow const window{ CoreWindow::GetForCurrentThread() };
        window.Activate();

        Render();
        CoreDispatcher const dispatcher{ window.Dispatcher() };
        dispatcher.ProcessEvents(CoreProcessEventsOption::ProcessUntilQuit);
    }

    void SetWindow(CoreWindow const&) {}

    void Draw()
    {
        m_target->Clear(color_white);

        D2D1_SIZE_F const size{ m_target->GetSize() };
        D2D1_RECT_F const rect{ 100.0f, 100.0f, size.width - 100.0f, size.height - 100.0f };
        m_target->DrawRectangle(rect, m_brush.get(), 100.0f);

        char buffer[1024];
        (void)snprintf(buffer, sizeof(buffer), "Draw %.2f x %.2f @ %.2f\n", size.width, size.height, m_dpi);
        ::OutputDebugStringA(buffer);
    }

    void Render()
    {
        if (!m_target)
        {
            winrt::com_ptr<ID3D11Device> const device{ CreateDevice() };
            m_target = CreateRenderTarget(m_factory, device);
            m_swapChain = CreateSwapChainForCoreWindow(device);

            CreateDeviceSwapChainBitmap(m_swapChain, m_target);

            m_target->SetDpi(m_dpi, m_dpi);

            CreateDeviceResources();
            CreateDeviceSizeResources();
        }

        m_target->BeginDraw();
        Draw();
        m_target->EndDraw();

        HRESULT const hr{ m_swapChain->Present(1, 0) };

        if (S_OK != hr && DXGI_STATUS_OCCLUDED != hr)
        {
            ReleaseDevice();
        }
    }

    void ReleaseDevice()
    {
        m_target = nullptr;
        m_swapChain = nullptr;

        ReleaseDeviceResources();
    }

    void ResizeSwapChainBitmap()
    {
        WINRT_ASSERT(m_target);
        WINRT_ASSERT(m_swapChain);

        m_target->SetTarget(nullptr);

        if (S_OK == m_swapChain->ResizeBuffers(0, // all buffers
            0, 0, // client area
            DXGI_FORMAT_UNKNOWN, // preserve format
            0)) // flags
        {
            CreateDeviceSwapChainBitmap(m_swapChain, m_target);
            CreateDeviceSizeResources();
        }
        else
        {
            ReleaseDevice();
        }
    }

    void CreateDeviceIndependentResources()
    {
    }

    void CreateDeviceResources()
    {
        winrt::check_hresult(m_target->CreateSolidColorBrush(
            color_orange,
            D2D1::BrushProperties(0.8f),
            m_brush.put()));
    }

    void CreateDeviceSizeResources()
    {
    }

    void ReleaseDeviceResources()
    {
        m_brush = nullptr;
    }
};

int __stdcall wWinMain(HINSTANCE, HINSTANCE, PWSTR, int)
{
    CoreApplication::Run(winrt::make<App>());
}
```

## <a name="working-with-com-types-such-as-bstr-and-variant"></a>Работа с COM-типы, такие как BSTR и VARIANT

Как вы видите, C++/WinRT предоставляет поддержку для реализации и вызов COM-интерфейсов. Для использования COM-типы, такие как BSTR и VARIANT, всегда есть возможность использовать их в их исходном виде (а также соответствующие API-интерфейсы). Кроме того, можно использовать оболочек, предоставляемых платформой, такие как [Active Template Library (ATL)](/cpp/atl/active-template-library-atl-concepts), или с помощью компилятора Visual C++ [поддержки модели COM](/cpp/cpp/compiler-com-support), и даже собственных оболочек.

## <a name="important-apis"></a>Важные API
* [winrt::check_hresult function](/uwp/cpp-ref-for-winrt/error-handling/check-hresult)
* [winrt::com_ptr struct template](/uwp/cpp-ref-for-winrt/com-ptr)
* [Структура WinRT::Windows::Foundation::IUnknown](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown)
