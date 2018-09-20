---
author: stevewhims
description: В это разделе объясняется, как перенести код WRL в его эквивалент на C++/WinRT.
title: Переход на C++/WinRT с WRL
ms.author: stwhi
ms.date: 05/30/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, перенос, WRL
ms.localizationpriority: medium
ms.openlocfilehash: 935b76e668153c9519bc6516da0c2872c2428f2e
ms.sourcegitcommit: 4f6dc806229a8226894c55ceb6d6eab391ec8ab6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2018
ms.locfileid: "4083961"
---
# <a name="move-to-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt-from-wrl"></a>Переход на [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) с WRL
В этом разделе объясняется, как перенести код [библиотеки шаблонов C++ для среды выполнения Windows (WRL)](/cpp/windows/windows-runtime-cpp-template-library-wrl) в его эквивалент на C++/WinRT.

Первым шагом при переносе в C++/WinRT является ручное добавление поддержки C++/WinRT в проект (см. раздел [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix)). Чтобы это сделать, измените файл `.vcxproj`, найдите `<PropertyGroup Label="Globals">` и внутри этой группы свойств настройте свойство `<CppWinRTEnabled>true</CppWinRTEnabled>`. Одним из последствий этого изменения является поддержки для [C + +/ CX](/cpp/cppcx/visual-c-language-reference-c-cx) отключена в проекте. Если вы используете C++/CX в проекте, в последствии можно оставить поддержку отключенной и обновить код C++/CX до кода C++/WinRT (см. раздел [Переход на C++/WinRT с C++/CX](move-to-winrt-from-cx.md)). Либо можно включить поддержку (в свойствах проекта перейдите в раздел **C/C++** \> **Общие** \> **Использовать расширение среды выполнения Windows** \> **Да (/ZW)**) и сначала сосредоточиться на переносе кода WRL. C + +/ CX и C + +/ WinRT кода могут сосуществовать в том же проекте, за исключением поддержку компилятора XAML и компоненты среды выполнения Windows (см. в разделе [Переход на C + +/ WinRT из C + +/ CX](move-to-winrt-from-cx.md)).

Задайте свойству проекта **Общие** \> **Версия целевой платформы** значение 10.0.17134.0 (Windows 10, версия 1803) или более позднюю версию.

В предварительно скомпилированный файл заголовка (обычно это `pch.h`) добавьте `winrt/base.h`.

```cppwinrt
#include <winrt/base.h>
```

При добавлении заголовков с API-интерфейсами Windows, проецируемыми в C++/WinRT, (например, `winrt/Windows.Foundation.h`) необходимость в явном добавлении `winrt/base.h` подобным образом отсутствует, так как этот элемент будет добавлен автоматически.

## <a name="porting-wrl-com-smart-pointers-microsoftwrlcomptrcppwindowscomptr-class"></a>Перенос интеллектуальных указателей WRL COM ([Microsoft::WRL::ComPtr](/cpp/windows/comptr-class))
Перенесите любой код, использующий **Microsoft::WRL::ComPtr\<T\>**, для использования метода [**winrt::com_ptr\<T\>**](/uwp/cpp-ref-for-winrt/com-ptr). Вот пример "до" и "после". В версии *после* функция-член [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function) извлекает базовой необработанные указатели, чтобы их можно было настроить.

```cpp
ComPtr<IDXGIAdapter1> previousDefaultAdapter;
DX::ThrowIfFailed(m_dxgiFactory->EnumAdapters1(0, &previousDefaultAdapter));
```

```cppwinrt
winrt::com_ptr<IDXGIAdapter1> previousDefaultAdapter;
winrt::check_hresult(m_dxgiFactory->EnumAdapters1(0, previousDefaultAdapter.put()));
```

> [!IMPORTANT]
> Если у вас есть [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr) , которая уже закреплена (его внутренних необработанный указатель уже имеет конечное) и вы хотите повторно число его, чтобы он указывал на другой объект, а затем сначала необходимо назначить `nullptr` к нему&mdash;как показано в следующем примере кода. Если этого не сделать, затем уже вошла **com_ptr** будете выводить проблемы вашего внимания (при вызове [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#comptrput-function) или [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#comptrputvoid-function)), утверждая, что его внутреннего указателя не равен null.

```cppwinrt
winrt::com_ptr<IDXGISwapChain1> m_pDXGISwapChain1;
...
// We execute the code below each time the window size changes.
m_pDXGISwapChain1 = nullptr; // Important because we're about to re-seat 
winrt::check_hresult(
    m_pDxgiFactory->CreateSwapChainForHwnd(
        m_pCommandQueue.get(), // For Direct3D 12, this is a pointer to a direct command queue, and not to the device.
        m_hWnd,
        &swapChainDesc,
        nullptr,
        nullptr,
        m_pDXGISwapChain1.put())
);
```

В следующем примере (в версии *после*) функция-член [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#comptrputvoid-function) извлекает базовый необработанный указатель в качестве указателя на указатель типа "void".

```cpp
ComPtr<ID3D12Debug> debugController;
if (SUCCEEDED(D3D12GetDebugInterface(IID_PPV_ARGS(&debugController))))
{
    debugController->EnableDebugLayer();
}
```

```cppwinrt
winrt::com_ptr<ID3D12Debug> debugController;
if (SUCCEEDED(D3D12GetDebugInterface(__uuidof(debugController), debugController.put_void())))
{
    debugController->EnableDebugLayer();
}
```

Замените **ComPtr::Get** на [**com_ptr::get**](/uwp/cpp-ref-for-winrt/com-ptr#comptrget-function).

```cpp
m_d3dDevice->CreateDepthStencilView(m_depthStencil.Get(), &dsvDesc, m_dsvHeap->GetCPUDescriptorHandleForHeapStart());
```

```cppwinrt
m_d3dDevice->CreateDepthStencilView(m_depthStencil.get(), &dsvDesc, m_dsvHeap->GetCPUDescriptorHandleForHeapStart());
```

Если требуется передать базовый необработанный указатель функции, которая ожидает указатель на **интерфейс IUnknown**, функция [**winrt::get_unknown**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#getunknown-function) бесплатный, как показано в следующем примере.

```cpp
ComPtr<IDXGISwapChain1> swapChain;
DX::ThrowIfFailed(
    m_dxgiFactory->CreateSwapChainForCoreWindow(
        m_commandQueue.Get(),
        reinterpret_cast<IUnknown*>(m_window.Get()),
        &swapChainDesc,
        nullptr,
        &swapChain
    )
);
```

```cppwinrt
winrt::agile_ref<winrt::Windows::UI::Core::CoreWindow> m_window; 
winrt::com_ptr<IDXGISwapChain1> swapChain;
winrt::check_hresult(
    m_dxgiFactory->CreateSwapChainForCoreWindow(
        m_commandQueue.get(),
        winrt::get_unknown(m_window.get()),
        &swapChainDesc,
        nullptr,
        swapChain.put()
    )
);
```

## <a name="porting-a-wrl-module-microsoftwrlmodule"></a>Перенос модуля WRL ([Microsoft::WRL::Module]())
Вы можете постепенно добавлять код C++/WinRT в существующий проект, который использует WRL для реализации компонента; при этом поддержка существующих классов WRL останется активна. В этом разделе показано, как это сделать.

При создании нового проекта типа **Компонент среды выполнения Windows (C++/WinRT)** в Visual Studio и в ходе последующей сборки создается файл `Generated Files\module.g.cpp`. Этот файл содержит определения двух полезных функций C++/WinRT (перечислены ниже), которые можно скопировать и добавить в проект. Этими функциями являются **WINRT_CanUnloadNow** и **WINRT_GetActivationFactory**. Как видно, они осуществляют условный вызов WRL для предоставления поддержки на любом этапе переноса.

```cppwinrt
HRESULT WINRT_CALL WINRT_CanUnloadNow()
{
#ifdef _WRL_MODULE_H_
    if (!::Microsoft::WRL::Module<::Microsoft::WRL::InProc>::GetModule().Terminate())
    {
        return S_FALSE;
    }
#endif

    if (winrt::get_module_lock())
    {
        return S_FALSE;
    }

    winrt::clear_factory_cache();
    return S_OK;
}

HRESULT WINRT_CALL WINRT_GetActivationFactory(HSTRING classId, void** factory)
{
    try
    {
        *factory = nullptr;
        wchar_t const* const name = WINRT_WindowsGetStringRawBuffer(classId, nullptr);

        if (0 == wcscmp(name, L"MoveFromWRLTest.Class"))
        {
            *factory = winrt::detach_abi(winrt::make<winrt::MoveFromWRLTest::factory_implementation::Class>());
            return S_OK;
        }

#ifdef _WRL_MODULE_H_
        return ::Microsoft::WRL::Module<::Microsoft::WRL::InProc>::GetModule().GetActivationFactory(classId, reinterpret_cast<::IActivationFactory**>(factory));
#else
        return winrt::hresult_class_not_available().to_abi();
#endif
    }
    catch (...) { return winrt::to_hresult(); }
}
```

Добавив эти функции в свой проект, вместо вызова [**Module::GetActivationFactory**](/cpp/windows/module-getactivationfactory-method) напрямую, вызовите функцию **WINRT_GetActivationFactory** (которая внутри вызывает функцию WRL). Вот пример "до" и "после".

```cpp
HRESULT WINAPI DllGetActivationFactory(_In_ HSTRING activatableClassId, _Out_ ::IActivationFactory **factory)
{
    auto & module = Microsoft::WRL::Module<Microsoft::WRL::InProc>::GetModule();
    return module.GetActivationFactory(activatableClassId, factory);
}
```

```cppwinrt
HRESULT __stdcall WINRT_GetActivationFactory(HSTRING activatableClassId, void** factory);
HRESULT WINAPI DllGetActivationFactory(_In_ HSTRING activatableClassId, _Out_ ::IActivationFactory **factory)
{
    return WINRT_GetActivationFactory(activatableClassId, reinterpret_cast<void**>(factory));
}
```

Вместо вызова [**Module::Terminate**](/cpp/windows/module-terminate-method) напрямую, вызовите функцию **WINRT_CanUnloadNow** (которая внутри вызовет функцию WRL). Вот пример "до" и "после".

```cpp
HRESULT __stdcall DllCanUnloadNow(void)
{
    auto &module = Microsoft::WRL::Module<Microsoft::WRL::InProc>::GetModule();
    HRESULT hr = (module.Terminate() ? S_OK : S_FALSE);
    if (hr == S_OK)
    {
        hr = ...
    }
    return hr;
}
```

```cppwinrt
HRESULT __stdcall WINRT_CanUnloadNow();
HRESULT __stdcall DllCanUnloadNow(void)
{
    HRESULT hr = WINRT_CanUnloadNow();
    if (hr == S_OK)
    {
        hr = ...
    }
    return hr;
}
```

## <a name="important-apis"></a>Важные API
* [Шаблон структуры winrt::com_ptr](/uwp/cpp-ref-for-winrt/com-ptr)
* [winrt::Windows::Foundation::IUnknown struct](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown)

## <a name="related-topics"></a>Смежные разделы
* [Введение в C++/WinRT](intro-to-using-cpp-with-winrt.md)
* [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md)
* [Библиотека шаблонов C++ для среды выполнения Windows (WRL)](/cpp/windows/windows-runtime-cpp-template-library-wrl)
