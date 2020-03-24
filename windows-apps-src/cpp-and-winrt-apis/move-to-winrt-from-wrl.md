---
description: В этом разделе объясняется, как перенести код WRL в его эквивалент на C++/WinRT.
title: Переход на C++/WinRT с WRL
ms.date: 05/30/2018
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, port, migrate, WRL
ms.localizationpriority: medium
ms.openlocfilehash: 663e0dddb9823e35e2d31ba9667bd3d16bd1d5de
ms.sourcegitcommit: cab95379459ad378163aa4469c9dc6c509cc8c43
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79510997"
---
# <a name="move-to-cwinrt-from-wrl"></a>Переход на C++/WinRT с WRL
В этом разделе показано, как перенести код [библиотеки шаблонов C++ для среды выполнения Windows (WRL)](/cpp/windows/windows-runtime-cpp-template-library-wrl) в его эквивалент на [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

Первым шагом при переносе в C++/WinRT является ручное добавление поддержки C++/WinRT в проект (см. раздел [Поддержка для Visual Studio C++/WinRT, XAML, расширения VSIX и пакет NuGet](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package)). Для этого в свой проект следует установить [пакет Microsoft.Windows.CppWinRT NuGet](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/). В Visual Studio откройте проект, щелкните **Проект** \> **Управление пакетами NuGet...** \> **Обзор**, введите или вставьте **Microsoft.Windows.CppWinRT** в поле поиска, выберите элемент в результатах поиска, а затем нажмите кнопку **Установить**, чтобы установить пакет для этого проекта. Одним из последствий этого изменения является отключение поддержки для [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) в проекте. Если вы используете C++/CX в проекте, впоследствии можно оставить поддержку отключенной и обновить код C++/CX до кода C++/WinRT (см. раздел [Переход на C++/WinRT с C++/CX](move-to-winrt-from-cx.md)). Либо можно включить поддержку (в свойствах проекта перейдите в раздел **C/C++** \> **Общие** \> **Использовать расширение среды выполнения Windows** \> **Да (/ZW)** ) и сначала сосредоточиться на переносе кода WRL. Код C++/CX и C++/WinRT может сосуществовать в одном проекте, за исключением поддержки компилятора XAML и компонентов среды выполнения Windows (см. статью [Переход на C++/WinRT с C++/CX](move-to-winrt-from-cx.md)).

Задайте для свойства проекта **Общие** \> **Версия целевой платформы** значение 10.0.17134.0 (Windows 10, версия 1803) или более позднюю версию.

В предварительно скомпилированный файл заголовка (обычно это `pch.h`) добавьте `winrt/base.h`.

```cppwinrt
#include <winrt/base.h>
```

При добавлении каких-либо спроектированных заголовков Windows API для C++/WinRT, (например, `winrt/Windows.Foundation.h`) необходимость в явном добавлении `winrt/base.h` подобным образом отсутствует, так как этот элемент будет добавлен автоматически.

## <a name="porting-wrl-com-smart-pointers-microsoftwrlcomptr"></a>Перенос интеллектуальных указателей WRL COM ([Microsoft::WRL::ComPtr](/cpp/windows/comptr-class))
Перенесите любой код, использующий **Microsoft::WRL::ComPtr\<T\>** , для использования метода [**winrt::com_ptr\<T\\>** ](/uwp/cpp-ref-for-winrt/com-ptr). Далее приведен пример "до" и "после". В версии *после* функция-член [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput-function) извлекает базовой необработанный указатель, чтобы его можно было настроить.

```cpp
ComPtr<IDXGIAdapter1> previousDefaultAdapter;
DX::ThrowIfFailed(m_dxgiFactory->EnumAdapters1(0, &previousDefaultAdapter));
```

```cppwinrt
winrt::com_ptr<IDXGIAdapter1> previousDefaultAdapter;
winrt::check_hresult(m_dxgiFactory->EnumAdapters1(0, previousDefaultAdapter.put()));
```

> [!IMPORTANT]
> Если вы уже разместили [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr) (у его внутреннего необработанного указателя уже есть цель) и хотите переместить его, чтобы он указывал на другой объект, сначала нужно присвоить `nullptr` ему&mdash;, как показано в примере кода ниже. Если вы этого не сделаете, то уже установленный **com_ptr** вызовет проблему (при вызове [**com_ptr::put**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput-function) или [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput_void-function)), утверждая, что его внутренний указатель не равен нулю.

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

В следующем примере (в версии *после*) функция-член [**com_ptr::put_void**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrput_void-function) извлекает базовый необработанный указатель в качестве указателя на указатель типа "void".

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

Замените **ComPtr::Get** на [**com_ptr::get**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptrget-function).

```cpp
m_d3dDevice->CreateDepthStencilView(m_depthStencil.Get(), &dsvDesc, m_dsvHeap->GetCPUDescriptorHandleForHeapStart());
```

```cppwinrt
m_d3dDevice->CreateDepthStencilView(m_depthStencil.get(), &dsvDesc, m_dsvHeap->GetCPUDescriptorHandleForHeapStart());
```

Если требуется передать базовый необработанный указатель функции, которая ожидает указатель на элемент **IUnknown**, используйте свободную функцию [**winrt::get_unknown**](/uwp/cpp-ref-for-winrt/get-unknown), как показано в следующем примере.

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

## <a name="porting-a-wrl-module-microsoftwrlmodule"></a>Перенос модуля WRL (Microsoft::WRL::Module)
Также можно постепенно добавлять код C++/WinRT в существующий проект, в котором использует WRL для реализации компонента; при этом поддержка существующих классов WRL останется активна. В этом разделе показано, как это сделать.

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

Добавив эти функции в проект, вместо вызова [**Module::GetActivationFactory**](/cpp/windows/module-getactivationfactory-method) напрямую, вызовите функцию **WINRT_GetActivationFactory** (которая внутри вызывает функцию WRL). Далее приведен пример "до" и "после".

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

Вместо вызова [**Module::Terminate**](/cpp/windows/module-terminate-method) напрямую, вызовите функцию **WINRT_CanUnloadNow** (которая внутри вызовет функцию WRL). Далее приведен пример "до" и "после".

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
* [winrt::com_ptr struct template (C++/WinRT)](/uwp/cpp-ref-for-winrt/com-ptr) (Шаблон структуры winrt::com_ptr (C++/WinRT))
* [Структура winrt::Windows::Foundation::IUnknown](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown)

## <a name="related-topics"></a>Связанные темы
* [Введение в C++/WinRT](intro-to-using-cpp-with-winrt.md)
* [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md)
* [Библиотека шаблонов C++ для среды выполнения Windows (WRL)](/cpp/windows/windows-runtime-cpp-template-library-wrl)
