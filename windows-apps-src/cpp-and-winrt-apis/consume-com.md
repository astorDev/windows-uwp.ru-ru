---
author: stevewhims
description: В этом разделе используется полный пример кода для Direct2D для отображения способы использования C + +/ WinRT для использования COM-классы и интерфейсы.
title: Использование COM-компонентов с помощью C++/WinRT
ms.author: stwhi
ms.date: 07/23/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c ++, cpp, winrt, COM, компонента, класс, интерфейс
ms.localizationpriority: medium
ms.openlocfilehash: a07c9877a6d6d953e578d927959b1202444ede21
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5861367"
---
# <a name="consume-com-components-with-cwinrt"></a>Использование COM-компонентов с помощью C++/WinRT

Вы можете использовать возможности объектов [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) библиотеки для использования COM-компонентов, таких как высокой производительности двухмерной и трехмерной графики DirectX API-интерфейсов. C + +/ WinRT — это самый простой способ использования DirectX без снижения производительности. В этом разделе используются Direct2D пример кода для отображения способы использования C + +/ WinRT для использования COM-классы и интерфейсы. Можно Конечно, смешивать модели COM и среды выполнения Windows программирования в том же C + +/ WinRT проекта.

В конце этой статьи вы найдете список полный исходный код минимального приложения Direct2D. Мы будем поднимите выдержки из этого кода и использовать их для демонстрации использование COM-компонентов с помощью C + +/ WinRT, с помощью различных возможностей C + +/ WinRT библиотеки.

## <a name="com-smart-pointers-winrtcomptruwpcpp-ref-for-winrtcom-ptr"></a>Указатели COM. ([**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr))

При программировании с помощью модели COM, вы работаете непосредственно с интерфейсами, а не с объектами (которые также true незаметно для API среды выполнения Windows, которые являются развитием COM). Вызов функции в классе модели COM, например, активации класса, получите интерфейс назад, а затем вызвать функции на этот интерфейс. Для доступа к состояние объекта, вы не получить доступ к членам данных напрямую; Вместо этого вызовите метод доступа и мутатора функций в интерфейсе.

Чтобы получить более конкретные, мы говорим о взаимодействует с интерфейса *указателей*. И для этого мы преимущества от существования типа смарт-указателя COM в C + +/ WinRT&mdash; [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr) типа.

```cppwinrt
winrt::com_ptr<ID2D1Factory1> factory;
```

Приведенный выше код показано, как объявить неинициализированные смарт-указатель на COM-интерфейс [**ID2D1Factory1**](https://msdn.microsoft.com/library/Hh404596) . Смарт-указателя не инициализирована, поэтому оно еще не указывает **ID2D1Factory1** интерфейса, принадлежащих любого фактического объекта (он не указывает на интерфейс вообще). Но у него есть потенциально могут сделать это; и (что смарт-указателя) имеет возможность через подсчет для управления жизненным циклом объектов-владельцем интерфейса, который он указывает и иметь средний, по которым вызывать функции на этот интерфейс COM ссылок.

## <a name="com-functions-that-return-an-interface-pointer-as-void"></a>Функции COM, которые возвращают указатель интерфейса как **void**

Можно вызвать функцию [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#comptrputvoid-function) для записи неинициализированные смарт-указателя базовый необработанный указатель.

```cppwinrt
D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    __uuidof(factory),
    &options,
    factory.put_void()
);
```

Приведенный выше код вызывает функцию [**D2D1CreateFactory**](/windows/desktop/api/d2d1/nf-d2d1-d2d1createfactory) , которая возвращает указатель интерфейса **ID2D1Factory1** через его последний параметр, который имеет **void\ * \ *** типа. Многие функции COM возвращают **void\ * \ ***. Для таких функций как показано использование [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#comptrputvoid-function) .

## <a name="com-functions-that-return-a-specific-interface-pointer"></a>Функции COM, которые возвращают указатель определенного интерфейса

Функция [**D3D11CreateDevice**](/windows/desktop/api/dwrite/nf-dwrite-dwritecreatefactory) возвращает указатель интерфейса [**ID3D11Device**](https://msdn.microsoft.com/library/Hh404596) через его antepenultimate параметр, который имеет **ID3D11Device\ * \ *** типа. Функции, которые возвращают указатель определенного интерфейса такую ситуацию используйте [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function).

```cppwinrt
winrt::com_ptr<ID3D11Device> device;
D3D11CreateDevice(
    ...
    device.put(),
    ...);
```

В примере кода в разделе перед данному показано, как вызвать функцию необработанных **D2D1CreateFactory** . Но на самом деле, по запросу **D2D1CreateFactory**в примере кода для этого раздела его используется шаблон вспомогательные функции, который создает программу-оболочку необработанных API и поэтому в примере кода фактически использует [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function).

```cppwinrt
winrt::com_ptr<ID2D1Factory1> factory;
D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    options,
    factory.put());
```

## <a name="com-functions-that-return-an-interface-pointer-as-iunknown"></a>Функции COM, которые возвращают указатель интерфейса как **IUnknown**

Функция [**DWriteCreateFactory**](/windows/desktop/api/dwrite/nf-dwrite-dwritecreatefactory) возвращает указатель интерфейса фабрики DirectWrite через его последний параметр, который имеет тип [**IUnknown**](https://msdn.microsoft.com/library/windows/desktop/ms680509) . Для такой функции использования [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function), но повторно интерпретировать приведен, **IUnknown**.

```cppwinrt
DWriteCreateFactory(
    DWRITE_FACTORY_TYPE_SHARED,
    __uuidof(dwriteFactory2),
    reinterpret_cast<IUnknown**>(dwriteFactory2.put()));
```

## <a name="re-seat-a-winrtcomptr"></a>Повторно число **winrt::com_ptr**

> [!IMPORTANT]
> Если у вас есть [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr) , которая уже закреплена (его внутренних необработанный указатель уже имеет конечное) и вы хотите повторно число его, чтобы указывать на другой объект, а затем сначала необходимо назначить `nullptr` к нему&mdash;как показано в следующем примере кода. Если этого не сделать, затем уже закреплена **com_ptr** будете выводить проблемы вашего внимания (при вызове [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function) или [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#comptrputvoid-function)), утверждая, что его внутреннего указателя не равен null.

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

## <a name="handle-hresult-error-codes"></a>Обрабатывать коды ошибок HRESULT

Чтобы проверить, что значение HRESULT, возвращенное из COM-функции и создает исключение, в том случае, если он представляет код ошибки, вызовите [**winrt::check_hresult**](/uwp/cpp-ref-for-winrt/error-handling/check-hresult).

```cppwinrt
winrt::check_hresult(D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    __uuidof(factory),
    options,
    factory.put_void()));
```

## <a name="com-functions-that-take-a-specific-interface-pointer"></a>Функции COM, которые принимают указатель определенного интерфейса

Можно вызвать функцию [**com_ptr::get**](/uwp/cpp-ref-for-winrt/com-ptr#comptrget-function) для передачи вашего **com_ptr** функции, которая принимает указатель определенного интерфейса того же типа.

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

## <a name="com-functions-that-take-an-iunknown-interface-pointer"></a>Функции COM, которые принимают указатель интерфейса **IUnknown**

Можно вызвать функцию бесплатные [**winrt::get_unknown**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#getunknown-function) для передачи вашего **com_ptr** функции, которая принимает указатель интерфейса **IUnknown** .

```cppwinrt
winrt::check_hresult(factory->CreateSwapChainForCoreWindow(
    ...
    winrt::get_unknown(CoreWindow::GetForCurrentThread()),
    ...));
```

## <a name="passing-and-returning-com-smart-pointers"></a>Передача и возврат COM интеллектуальные указатели

Функция, принимающая смарт-указатель COM в виде **winrt::com_ptr** следует сделать по ссылке констант или по ссылке.

```cppwinrt
... GetDxgiFactory(winrt::com_ptr<ID3D11Device> const& device) ...

... CreateDevice(..., winrt::com_ptr<ID3D11Device>& device) ...
```

Функции, которая возвращает **winrt::com_ptr** специфичное по значению.

```cppwinrt
winrt::com_ptr<ID2D1Factory1> CreateFactory() ...
```

## <a name="query-a-com-smart-pointer-for-a-different-interface"></a>Запрос COM смарт-указателя для другой интерфейс

Можно использовать функцию [**com_ptr::as**](/uwp/cpp-ref-for-winrt/com-ptr#comptras-function) для запроса смарт-указатель COM для другой интерфейс. Функция создает исключение, если не удается в запросе.

```cppwinrt
void ExampleFunction(winrt::com_ptr<ID3D11Device> const& device)
{
    ...
    winrt::com_ptr<IDXGIDevice> const dxdevice{ device.as<IDXGIDevice>() };
    ...
}
```

Кроме того, с помощью [**com_ptr::try_as**](/uwp/cpp-ref-for-winrt/com-ptr#comptrtryas-function), который возвращает значение, которое вы можете сверить `nullptr` чтобы узнать, является ли запрос выполнен успешно.

## <a name="full-source-code-listing-of-a-minimal-direct2d-application"></a>Полный исходный код приведенных минимального приложения Direct2D

Если вы хотите выполнить сборку и запустить этот пример кода источника, а затем во-первых, в Visual Studio, создайте новый **приложения основных компонентов (C + +/ WinRT)**. `Direct2D` — Это разумный имя для проекта, однако можно назвать его своему усмотрению. Откройте `App.cpp`, удалите все содержимое и вставьте в описании ниже.

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

        WINRT_TRACE("Draw %.2f x %.2f @ %.2f\n", size.width, size.height, m_dpi);
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
    CoreApplication::Run(App());
}
```

## <a name="working-with-com-types-such-as-bstr-and-variant"></a>Работа с типами COM, такие как BSTR и VARIANT

Как видно, C + +/ WinRT предоставляет поддержку для реализации и вызова COM-интерфейсы. Для использования COM-типы, такие как BSTR и VARIANT, всегда есть возможность использовать их в их исходном виде (вместе с соответствующие API-интерфейсы). Кроме того можно использовать программы-оболочки, предоставляемый платформой, например [Active Template Library (ATL)](/cpp/atl/active-template-library-atl-concepts), или компилятор Visual C++ [Поддержки модели COM](/cpp/cpp/compiler-com-support)или даже ваших оболочках.

## <a name="important-apis"></a>Важные API
* [Функция winrt::check_hresult](/uwp/cpp-ref-for-winrt/error-handling/check-hresult)
* [Шаблон структуры winrt::com_ptr](/uwp/cpp-ref-for-winrt/com-ptr)
* [winrt::Windows::Foundation::IUnknown struct](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown)
