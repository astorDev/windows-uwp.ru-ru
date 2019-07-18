---
description: В этом разделе приведен полный пример кода Direct2D, чтобы показать, как использовать классы и интерфейсы COM с помощью C++/WinRT.
title: Использование компонентов COM с помощью C++/WinRT
ms.date: 04/24/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, COM, component, class, interface
ms.localizationpriority: medium
ms.openlocfilehash: bb28ec7afa22f81033bfce2aff530119e53a4b91
ms.sourcegitcommit: 7585bf66405b307d7ed7788d49003dc4ddba65e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67660151"
---
# <a name="consume-com-components-with-cwinrt"></a>Использование компонентов COM с помощью C++/WinRT

Возможности библиотеки [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) можно применять для использования компонентов COM, таких как высокопроизводительная двумерная и трехмерная графика API-интерфейсов DirectX. C++/WinRT — это самый простой способ использовать DirectX без ущерба для производительности. В этой статье приведен пример кода Direct2D, демонстрирующий, как использовать классы и интерфейсы COM с помощью C++/WinRT. Конечно, можно смешивать модели программирования COM и среды выполнения Windows в одном проекте C++/WinRT.

В конце этой статьи вы найдете полный список исходного кода минимального приложения Direct2D. Мы возьмем выдержки из этого кода и рассмотрим, как использовать компоненты COM вместе с C++/WinRT, применяя различные средства библиотеки C++/WinRT.

## <a name="com-smart-pointers-winrtcomptruwpcpp-ref-for-winrtcom-ptr"></a>Интеллектуальные указатели COM ([**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr))

Когда вы программируете с помощью COM, вы работаете непосредственно с интерфейсами, а не с объектами (это также верно для API-интерфейсов среды выполнения Windows, которые являются развитием COM). Например, чтобы вызвать функцию в классе COM, вы активируете класс, получите интерфейс обратно, а затем вызовете функции для этого интерфейса. Чтобы получить доступ к состоянию объекта, вы не можете напрямую обращаться к его элементам данных. Вместо этого вы вызываете функцию доступа и функцию-мутатор в интерфейсе.

Если точнее, то мы говорим о взаимодействии с *указателями* интерфейса. И для этого мы воспользуемся указателями COM интеллектуального типа в C++/WinRT (тип [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr)).

```cppwinrt
#include <d2d1_1.h>
...
winrt::com_ptr<ID2D1Factory1> factory;
```

Приведенный выше код показывает, как объявить неинициализированный интеллектуальный указатель на COM-интерфейс [**ID2D1Factory1**](/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1factory1). Интеллектуальный указатель не инициализирован, поэтому он еще не указывает на интерфейс **ID2D1Factory1**, принадлежащий какому-либо реальному объекту (он вообще не указывает на интерфейс). Но у него есть такая возможность. Кроме того, с помощью счетчика ссылок COM он может управлять временем жизни объекта-владельца интерфейса, на который он указывает. Указатель можно использовать как средство для вызова функций в этом интерфейсе.

## <a name="com-functions-that-return-an-interface-pointer-as-void"></a>Функции COM, которые возвращают указатель интерфейса с типом **void**

Вы можете вызвать функцию [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput_void-function) для записи в базовый необработанный указатель неинициализированного интеллектуального указателя.

```cppwinrt
D2D1_FACTORY_OPTIONS options{ D2D1_DEBUG_LEVEL_NONE };
D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    __uuidof(factory),
    &options,
    factory.put_void()
);
```

Приведенный выше код вызывает функцию [**D2D1CreateFactory**](/windows/desktop/api/d2d1/nf-d2d1-d2d1createfactory), которая возвращает указатель интерфейса **ID2D1Factory1** с помощью своего последнего параметра, который имеет тип **void\*\*** . Многие функции COM возвращают параметры типа **void\*\*** . Для таких функций используйте [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput_void-function), как показано.

## <a name="com-functions-that-return-a-specific-interface-pointer"></a>Функции COM, которые возвращают определенный указатель интерфейса

Функция [**D3D11CreateDevice**](/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) возвращает указатель интерфейса [**ID3D11Device**](/windows/desktop/api/d3d11/nn-d3d11-id3d11device) с помощью третьего параметра от последнего, который имеет тип **ID3D11Device\*\*** . Для функций, которые возвращают определенный указатель интерфейса, используйте [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptr_put-function).

```cppwinrt
winrt::com_ptr<ID3D11Device> device;
D3D11CreateDevice(
    ...
    device.put(),
    ...);
```

Пример кода в предыдущем разделе показывает, как вызвать необработанную функцию **D2D1CreateFactory**. Но на самом деле, когда пример кода в этой статье вызывает функцию **D2D1CreateFactory**, он применяет шаблон вспомогательной функции, который создает оболочку для необработанного API, и поэтому пример кода фактически использует [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptr_put-function).

```cppwinrt
winrt::com_ptr<ID2D1Factory1> factory;
D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    options,
    factory.put());
```

## <a name="com-functions-that-return-an-interface-pointer-as-iunknown"></a>Функции COM, которые возвращают указатель интерфейса с типом **IUnknown**

Функция [**DWriteCreateFactory**](/windows/desktop/api/dwrite/nf-dwrite-dwritecreatefactory) возвращает указатель интерфейса фабрики DirectWrite с помощью последнего параметра с типом [**IUnknown**](/windows/desktop/api/unknwn/nn-unknwn-iunknown). Для такой функции используйте [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptr_put-function), но повторно интерпретируйте приведение к типу **IUnknown**.

```cppwinrt
DWriteCreateFactory(
    DWRITE_FACTORY_TYPE_SHARED,
    __uuidof(dwriteFactory2),
    reinterpret_cast<IUnknown**>(dwriteFactory2.put()));
```

## <a name="re-seat-a-winrtcomptr"></a>Повторное размещение **winrt::com_ptr**

> [!IMPORTANT]
> Если вы уже разместили [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr) (у его внутреннего необработанного указателя уже есть цель) и хотите переместить его, чтобы он указывал на другой объект, сначала нужно присвоить ему `nullptr`, как показано в примере кода ниже. Если вы этого не сделаете, то уже установленный **com_ptr** вызовет проблему (при вызове [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptr_put-function) или [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput_void-function)), утверждая, что его внутренний указатель не равен нулю.

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

## <a name="handle-hresult-error-codes"></a>Обработка кодов ошибок HRESULT

Чтобы проверить значение HRESULT, возвращенного функцией COM, и вызвать исключение в случае, если это значение представляет код ошибки, вызовите [**winrt::check_hresult**](/uwp/cpp-ref-for-winrt/error-handling/check-hresult).

```cppwinrt
winrt::check_hresult(D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    __uuidof(factory),
    options,
    factory.put_void()));
```

## <a name="com-functions-that-take-a-specific-interface-pointer"></a>Функции COM, которые принимают определенный указатель интерфейса

Вы можете вызвать функцию [**com_ptr::get**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrget-function), чтобы передать **com_ptr** в функцию, которая принимает определенный указатель интерфейса того же типа.

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

## <a name="com-functions-that-take-an-iunknown-interface-pointer"></a>Функции COM, которые принимают указатель интерфейса типа **IUnknown**

Вы можете вызвать свободную функцию [**winrt::get_unknown**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#get_unknown-function), чтобы передать **com_ptr** в функцию, которая принимает указатель интерфейса типа **IUnknown**.

```cppwinrt
winrt::check_hresult(factory->CreateSwapChainForCoreWindow(
    ...
    winrt::get_unknown(CoreWindow::GetForCurrentThread()),
    ...));
```

## <a name="passing-and-returning-com-smart-pointers"></a>Передача и возврат интеллектуальных указателей COM

Функция, принимающая интеллектуальный указатель COM в виде **winrt::com_ptr**, должна делать это с использованием постоянной или обычной ссылки.

```cppwinrt
... GetDxgiFactory(winrt::com_ptr<ID3D11Device> const& device) ...

... CreateDevice(..., winrt::com_ptr<ID3D11Device>& device) ...
```

Функция, которая возвращает **winrt:: com_ptr**, должна делать это по значению.

```cppwinrt
winrt::com_ptr<ID2D1Factory1> CreateFactory() ...
```

## <a name="query-a-com-smart-pointer-for-a-different-interface"></a>Запрос интеллектуального указателя COM для другого интерфейса

Вы можете использовать функцию [**com_ptr::as**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptras-function), чтобы запросить интеллектуальный указатель COM для другого интерфейса. Если завершается неудачно, функция создает исключение.

```cppwinrt
void ExampleFunction(winrt::com_ptr<ID3D11Device> const& device)
{
    ...
    winrt::com_ptr<IDXGIDevice> const dxdevice{ device.as<IDXGIDevice>() };
    ...
}
```

Кроме того, можно использовать функцию [**com_ptr::try_as**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrtry_as-function), возвращающую значение, которое можно проверить с помощью `nullptr` и определить, успешно ли выполнен запрос.

## <a name="full-source-code-listing-of-a-minimal-direct2d-application"></a>Полный список исходного кода минимального приложения Direct2D

Если вы хотите создать и запустить этот пример исходного кода, сначала в Visual Studio создайте **приложение основных компонентов (C++/WinRT)** . `Direct2D` — подходящее имя для проекта, но вы можете назвать его как угодно. Откройте файл `App.cpp`, удалите все его содержимое и вставьте список ниже.

В приведенном ниже коде используется функция [winrt::com_ptr::capture](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrcapture-function) там, где это возможно. `WINRT_ASSERT` — это макроопределение, которое передается в [_ASSERTE](/cpp/c-runtime-library/reference/assert-asserte-assert-expr-macros).

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
        factory.capture(adapter, &IDXGIAdapter::GetParent);
        return factory;
    }

    void CreateDeviceSwapChainBitmap(
        winrt::com_ptr<IDXGISwapChain1> const& swapchain,
        winrt::com_ptr<ID2D1DeviceContext> const& target)
    {
        WINRT_ASSERT(swapchain);
        WINRT_ASSERT(target);

        winrt::com_ptr<IDXGISurface> surface;
        surface.capture(swapchain, &IDXGISwapChain1::GetBuffer, 0);

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

## <a name="working-with-com-types-such-as-bstr-and-variant"></a>Работа с типами COM, такими как BSTR и VARIANT

Как видите, C++/WinRT обеспечивает поддержку как реализации, так и вызова интерфейсов COM. Для таких типов COM, как BSTR и VARIANT, мы рекомендуем использовать оболочки, предоставляемые [библиотеками реализации Windows (WIL)](https://github.com/Microsoft/wil), например **wil::unique_bstr** и **wil::unique_variant** (с управлением временем жизни ресурсов).

[WIL](https://github.com/Microsoft/wil) заменяет платформы, такие как библиотека шаблонных классов ATL, и поддержку COM компиляторами Visual C++. Мы рекомендуем это вместо написания собственных оболочек или использования типов COM, таких как BSTR и VARIANT, в необработанном виде (вместе с соответствующими API-интерфейсами).

## <a name="avoiding-namespace-collisions"></a>Предотвращение конфликтов пространств имен

Произвольное использование директив using — распространенная практика в C++/WinRT,&mdash;как показано в коде, приведенном в этомразделе&mdash;. В некоторых случаях это может привести к проблеме, при которой конфликтующие имена импортируются в глобальное пространство имен. Рассмотрим пример.

C++/ WinRT содержит тип с именем [**winrt::Windows::Foundation::IUnknown**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown). При этом COM определяет тип с именем [ **::IUnknown**](/windows/desktop/api/unknwn/nn-unknwn-iunknown). Поэтому в проекте C++/WinRT с применением заголовков COM рекомендуем использовать следующий код:

```cppwinrt
using namespace winrt::Windows::Foundation;
...
void MyFunction(IUnknown*); // error C2872:  'IUnknown': ambiguous symbol
```

Неизвестное имя *IUnknown* вызывает конфликт в глобальном пространстве имен из-за ошибки компилятора при использовании *неоднозначного символа*. Вместо этого вы можете изолировать версию имени C++/WinRT в пространстве имен **winrt**, как показано ниже.

```cppwinrt
namespace winrt
{
    using namespace Windows::Foundation;
}
...
void MyFunctionA(IUnknown*); // Ok.
void MyFunctionB(winrt::IUnknown const&); // Ok.
```

Или, если вы сможете использовать `using namespace winrt`, если это удобнее. Для этого достаточно определить глобальную версию *IUnknown*, как показано ниже.

```cppwinrt
using namespace winrt;
namespace winrt
{
    using namespace Windows::Foundation;
}
...
void MyFunctionA(::IUnknown*); // Ok.
void MyFunctionB(winrt::IUnknown const&); // Ok.
```

Это работает с любым пространством имен C++/WinRT.

```cppwinrt
namespace winrt
{
    using namespace Windows::Storage;
    using namespace Windows::System;
}
```

Затем можно ссылаться на **winrt::Windows::Storage::StorageFile**, например, как просто на **winrt::StorageFile**.

## <a name="important-apis"></a>Важные API
* [Функция winrt::check_hresult](/uwp/cpp-ref-for-winrt/error-handling/check-hresult)
* [Шаблон структуры winrt::com_ptr](/uwp/cpp-ref-for-winrt/com-ptr)
* [Структура winrt::Windows::Foundation::IUnknown](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown)
