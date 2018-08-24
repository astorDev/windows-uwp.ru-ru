---
author: stevewhims
description: В этом разделе использует полный пример кода возможности Direct2D для использования C + +/ WinRT COM-классы и интерфейсы.
title: Использование DirectX и другие COM API для C + +/ WinRT
ms.author: stwhi
ms.date: 07/23/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартный, c ++, cpp, winrt, COM, компонент, класс, интерфейс
ms.localizationpriority: medium
ms.openlocfilehash: b87eb90ed5ecf731cc851e81e81ad016956e5fea
ms.sourcegitcommit: c6d6f8b54253e79354f8db14e5cf3b113a3e5014
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "2837096"
---
# <a name="consume-directx-and-other-com-apis-with-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Использование DirectX и другие COM API с [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)

Можно использовать средства C + +/ WinRT библиотеки для использования COM-компоненты, такие как высокопроизводительные плоских и объемных графические API-интерфейсы DirectX. C + +/ WinRT — это самый простой способ использования DirectX без снижения производительности. В этом разделе использует возможности Direct2D пример кода для использования C + +/ WinRT COM-классы и интерфейсы. Можно Конечно, совмещать COM и среды выполнения Windows программирования в пределах того же C + +/ WinRT проекта.

В конце этого раздела вы найдете пример кода полного источника для минимального возможности Direct2D приложения. Мы будем поднимите выдержки из этого кода и использовать их в качестве демонстрации использования компонентов COM, с помощью C + +/ WinRT, используя различные средства C + +/ WinRT библиотеки.

## <a name="com-smart-pointers-winrtcomptruwpcpp-ref-for-winrtcom-ptr"></a>Смарт-указатели COM ([**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr))

При программировании с COM, осуществляется непосредственно с интерфейсами, а не с объектами (которые также значение true в фоновом режиме для API среды выполнения Windows, которые являются развитием COM). Для вызова функции в классе COM, например, активация класс получить обратно интерфейс, а затем вызвать функции на этот интерфейс. Для доступа к состояние объекта, вы не прямой доступ к его члены данных; Вместо этого вызов функции доступа к данным и мутатора на интерфейс.

Для более конкретные, речь идет о взаимодействие с интерфейсом *указатели*. И для этого мы преимущества от наличия типа смарт-указателя COM в C + +/ WinRT&mdash; [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr) типа.

```cppwinrt
winrt::com_ptr<ID2D1Factory1> factory;
```

Выше кода показано, как для объявления не инициализированный смарт-указатель на интерфейс COM [**ID2D1Factory1**](https://msdn.microsoft.com/library/Hh404596) . Смарт-указатель не инициализирован, поэтому еще не указывает на интерфейс **ID2D1Factory1** , относящегося к любой фактический объект (она не указывает на интерфейс вообще). Но имеет возможность делать это; и (сейчас смарт-указатель) имеет возможность с помощью подсчета для управления жизненным циклом объекта интерфейса, указывающий на и быть средней, с помощью которого можно вызывать функции интерфейса COM ссылок.

## <a name="com-functions-that-return-an-interface-pointer-as-void"></a>Функции COM, которые возвращают указатель интерфейса как **void\ * \ ***

Можно вызвать функцию [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#comptrputvoid-function) для записи не инициализированный смарт-указатель базовым необработанные указатель.

```cppwinrt
D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    __uuidof(factory),
    &options,
    factory.put_void()
);
```

Приведенный выше код вызывает функцию [**D2D1CreateFactory**](/windows/desktop/api/d2d1/nf-d2d1-d2d1createfactory) , которая возвращает указатель интерфейса **ID2D1Factory1** с помощью последнего параметра, который имеет **void\ * \ *** типа. Многие функции COM возвращают **void\ * \ ***. Для таких функций как показано использование [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#comptrputvoid-function) .

## <a name="com-functions-that-return-a-specific-interface-pointer"></a>Функции COM, которые возвращают указатель определенного интерфейса

Функция [**D3D11CreateDevice**](/windows/desktop/api/dwrite/nf-dwrite-dwritecreatefactory) возвращает указатель интерфейса [**ID3D11Device**](https://msdn.microsoft.com/library/Hh404596) с помощью его antepenultimate параметр, который имеет **ID3D11Device\ * \ *** типа. Для функций, возвращающих указатель определенного интерфейса, который можно используйте [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function).

```cppwinrt
winrt::com_ptr<ID3D11Device> device;
D3D11CreateDevice(
    ...
    device.put(),
    ...);
```

В примере кода в разделе перед данным проектом показано, как вызывать функцию необработанные **D2D1CreateFactory** . Но на самом деле при в примере кода для этого раздела вызывает **D2D1CreateFactory**, использует шаблон вспомогательные функции, которая имеет необработанные API и поэтому использует пример кода [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function).

```cppwinrt
winrt::com_ptr<ID2D1Factory1> factory;
D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    options,
    factory.put());
```

## <a name="com-functions-that-return-an-interface-pointer-as-iunknown"></a>Функции COM, которые возвращают указатель интерфейса как **IUnknown\ * \ ***

Функция [**DWriteCreateFactory**](/windows/desktop/api/dwrite/nf-dwrite-dwritecreatefactory) возвращает указатель интерфейса фабрики DirectWrite с помощью последнего параметра, который имеет **IUnknown\ * \ *** типа. Для такие функции, можно использовать [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function), но выполнена повторная приведения, который будет **IUnknown\ * \ ***.

```cppwinrt
DWriteCreateFactory(
    DWRITE_FACTORY_TYPE_SHARED,
    __uuidof(dwriteFactory2),
    reinterpret_cast<IUnknown**>(dwriteFactory2.put()));
```

## <a name="re-seat-a-winrtcomptr"></a>Повторно установите **winrt::com_ptr**

> [!IMPORTANT]
> Если у вас есть [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr) , который уже установлен (его внутреннего указателя необработанные уже имеет целевого) и нужно повторно установите его для указания на другой объект, а затем сначала необходимо назначить `nullptr` к нему&mdash;как показано в следующем примере кода. Если вы не затем уже вошла **com_ptr** будет нарисовать проблему для вашего внимания (при вызове [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function) или [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#comptrputvoid-function)), утверждении, что его внутренний указатель не может быть null.

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

Чтобы возвращается значение HRESULT из COM-функции и проверять и создать исключение в случае, если он представляет код ошибки, вызовите [**winrt::check_hresult**](/uwp/cpp-ref-for-winrt/error-handling/check-hresult).

```cppwinrt
winrt::check_hresult(D2D1CreateFactory(
    D2D1_FACTORY_TYPE_SINGLE_THREADED,
    __uuidof(factory),
    options,
    factory.put_void()));
```

## <a name="com-functions-that-take-a-specific-interface-pointer"></a>Функции COM, которые принимают указатель определенного интерфейса

Можно вызвать функцию [**com_ptr::get**](/uwp/cpp-ref-for-winrt/com-ptr#comptrget-function) для передачи вашего **com_ptr** функция, которая принимает указатель определенного интерфейса того же типа.

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

## <a name="com-functions-that-take-an-iunknown-interface-pointer"></a>Функции COM, которые принимают указатель интерфейс **IUnknown**

Можно вызвать функцию бесплатные [**winrt::get_unknown**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#getunknown-function) для передачи вашего **com_ptr** функция, которая принимает указатель интерфейс **IUnknown** .

```cppwinrt
winrt::check_hresult(factory->CreateSwapChainForCoreWindow(
    ...
    winrt::get_unknown(CoreWindow::GetForCurrentThread()),
    ...));
```

## <a name="passing-and-returning-com-smart-pointers"></a>Передача и возврат COM смарт-указатели

Функции работы с COM смарт-указатель в виде **winrt::com_ptr** следует сделать с помощью ссылки на константу или с помощью ссылки.

```cppwinrt
... GetDxgiFactory(winrt::com_ptr<ID3D11Device> const& device) ...

... CreateDevice(..., winrt::com_ptr<ID3D11Device>& device) ...
```

Функция, которая возвращает **winrt::com_ptr** делать по значению.

```cppwinrt
winrt::com_ptr<ID2D1Factory1> CreateFactory() ...
```

## <a name="query-a-com-smart-pointer-for-a-different-interface"></a>Смарт-указатель запроса COM для разных интерфейса

Функция [**com_ptr::as**](/uwp/cpp-ref-for-winrt/com-ptr#comptras-function) можно использовать для запроса смарт-указатель COM для разных интерфейса. Функция создает исключение, если запрос не выполняются успешно.

```cppwinrt
void ExampleFunction(winrt::com_ptr<ID3D11Device> const& device)
{
    ...
    winrt::com_ptr<IDXGIDevice> const dxdevice{ device.as<IDXGIDevice>() };
    ...
}
```

В качестве альтернативы использовать [**com_ptr::try_as**](/uwp/cpp-ref-for-winrt/com-ptr#comptrtryas-function), который возвращает значение, чтобы проверить от `nullptr` для просмотра ли запрос выполнен успешно.

## <a name="full-source-code-listing-of-a-minimal-direct2d-application"></a>Пример кода полного источника для минимального возможности Direct2D приложения

Если требуется построить и запустить этот пример кода исходного then первого, в Visual Studio, создать новую **Основные приложения (C + +/ WinRT)**. `Direct2D` — Это сравнительно имя для проекта, но вы можете ему имя любое. Открыть `App.cpp`, удалите все содержимое и вставьте в списке ниже.

```cppwinrt
#include "pch.h"

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

## <a name="important-apis"></a>Важные API
* [WinRT::check_hresult](/uwp/cpp-ref-for-winrt/error-handling/check-hresult)
* [winrt::com_ptr](/uwp/cpp-ref-for-winrt/com-ptr)
* [winrt::Windows::Foundation::IUnknown struct](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown)
