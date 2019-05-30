---
description: В этом разделе описаны две вспомогательные функции, которые могут использоваться для преобразования между объектами C++/ CX и C++/WinRT.
title: Взаимодействие между C++/WinRT и C++/CX
ms.date: 10/09/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, перенос, взаимодействие, C++/CX
ms.localizationpriority: medium
ms.openlocfilehash: 5394443b4832864e5b46bfbf917c04f0af6d8a19
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66360221"
---
# <a name="interop-between-cwinrt-and-ccx"></a>Взаимодействие между C++/WinRT и C++/CX

Стратегии постепенно перенос кода в вашей [ C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx) проект [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) рассматриваются в [переместить C++/WinRT из C++/CX](move-to-winrt-from-cx.md).

В этом разделе показано две вспомогательные функции, которые можно использовать для преобразования между C++/CX и C++/WinRT объектов внутри одного проекта. Их можно использовать для взаимодействия между кодом, который использует два языковых проекций, или можно использовать функции, как перенести код из C++/CX для C++/WinRT.

## <a name="fromcx-and-tocx-functions"></a>функции from_cx и to_cx
Вспомогательная функция ниже преобразует объект C++/CX в эквивалентный объект C++/WinRT. Она автоматически приводит объект C++/CX к его базовому указателю интерфейса [**IUnknown**](https://docs.microsoft.com/windows/desktop/api/unknwn/nn-unknwn-iunknown). Затем она вызывает [**QueryInterface**](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-queryinterface(q_)) для этого указателя, чтобы запросить интерфейс по умолчанию для объекта C++/WinRT. **QueryInterface** — это эквивалент расширения safe_cast C++/CX в двоичном интерфейсе приложения (ABI) среды выполнения Windows. Функция [**winrt::put_abi**](/uwp/cpp-ref-for-winrt/put-abi) возвращает адрес базового указателя интерфейса **IUnknown** объекта C++/WinRT, чтобы для него можно было задать другое значение.

```cppwinrt
template <typename T>
T from_cx(Platform::Object^ from)
{
    T to{ nullptr };

    winrt::check_hresult(reinterpret_cast<::IUnknown*>(from)
        ->QueryInterface(winrt::guid_of<T>(),
            reinterpret_cast<void**>(winrt::put_abi(to))));

    return to;
}
```

Вспомогательная функция ниже преобразует объект C++/WinRT в эквивалентный объект C++/CX. Функция [**Winrt::get_abi**](/uwp/cpp-ref-for-winrt/get-abi) получает указатель на базовый интерфейс **IUnknown** объекта C++/WinRT. Функция приводит этот указатель к объекту C++/CX, перед тем как использовать расширение C++/CX safe_cast для запроса необходимого типа C++/CX.

```cppwinrt
template <typename T>
T^ to_cx(winrt::Windows::Foundation::IUnknown const& from)
{
    return safe_cast<T^>(reinterpret_cast<Platform::Object^>(winrt::get_abi(from)));
}
```

## <a name="example-project-showing-the-two-helper-functions-in-use"></a>Пример проекта, показывающий используется две вспомогательные функции

Чтобы воспроизвести простым способом, сценарий постепенно переноса кода в C++/CX проект, чтобы C++/WinRT, можно начать с создания нового проекта в Visual Studio, с помощью одного из C++/WinRT шаблоны проектов (см. в разделе [Visual Studio Поддержка C++/WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package)).

Этот пример проекта также показывает, как использовать псевдонимы пространств имен для разных острова кода, чтобы иметь дело с в противном случае потенциальные конфликты имен между C++/WinRT проекции и C++/CX проекции.

- Создание **Visual C++**  \> **универсальной Windows** > **приложения Core (C++/WinRT)** проекта.
- В свойствах проекта **C/C++** \> **Общие** \> **использовать расширение среды выполнения Windows** \> **Да (/ZW)** . Это включает поддержку проектов для C++/CX.
- Замените содержимое файла `App.cpp` с помощью кода ниже.

```cppwinrt
// App.cpp
#include "pch.h"
#include <sstream>

namespace cx
{
    using namespace Windows::Foundation;
}

namespace winrt
{
    using namespace Windows;
    using namespace Windows::ApplicationModel::Core;
    using namespace Windows::Foundation;
    using namespace Windows::Foundation::Numerics;
    using namespace Windows::UI;
    using namespace Windows::UI::Core;
    using namespace Windows::UI::Composition;
}

template <typename T>
T from_cx(Platform::Object^ from)
{
    T to{ nullptr };

    winrt::check_hresult(reinterpret_cast<::IUnknown*>(from)
        ->QueryInterface(winrt::guid_of<T>(),
            reinterpret_cast<void**>(winrt::put_abi(to))));

    return to;
}

template <typename T>
T^ to_cx(winrt::Windows::Foundation::IUnknown const& from)
{
    return safe_cast<T^>(reinterpret_cast<Platform::Object^>(winrt::get_abi(from)));
}

struct App : winrt::implements<App, winrt::IFrameworkViewSource, winrt::IFrameworkView>
{
    winrt::CompositionTarget m_target{ nullptr };
    winrt::VisualCollection m_visuals{ nullptr };
    winrt::Visual m_selected{ nullptr };
    winrt::float2 m_offset{};

    winrt::IFrameworkView CreateView()
    {
        return *this;
    }

    void Initialize(winrt::CoreApplicationView const &)
    {
    }

    void Load(winrt::hstring const&)
    {
    }

    void Uninitialize()
    {
    }

    void Run()
    {
        winrt::CoreWindow window = winrt::CoreWindow::GetForCurrentThread();
        window.Activate();

        winrt::CoreDispatcher dispatcher = window.Dispatcher();
        dispatcher.ProcessEvents(winrt::CoreProcessEventsOption::ProcessUntilQuit);
    }

    void SetWindow(winrt::CoreWindow const & window)
    {
        winrt::Compositor compositor;
        winrt::ContainerVisual root = compositor.CreateContainerVisual();
        m_target = compositor.CreateTargetForCurrentView();
        m_target.Root(root);
        m_visuals = root.Children();

        window.PointerPressed({ this, &App::OnPointerPressed });
        window.PointerMoved({ this, &App::OnPointerMoved });

        window.PointerReleased([&](auto && ...)
        {
            m_selected = nullptr;
        });
    }

    void OnPointerPressed(IInspectable const &, winrt::PointerEventArgs const & args)
    {
        winrt::float2 const point = args.CurrentPoint().Position();

        for (winrt::Visual visual : m_visuals)
        {
            winrt::float3 const offset = visual.Offset();
            winrt::float2 const size = visual.Size();

            if (point.x >= offset.x &&
                point.x < offset.x + size.x &&
                point.y >= offset.y &&
                point.y < offset.y + size.y)
            {
                m_selected = visual;
                m_offset.x = offset.x - point.x;
                m_offset.y = offset.y - point.y;
            }
        }

        if (m_selected)
        {
            m_visuals.Remove(m_selected);
            m_visuals.InsertAtTop(m_selected);
        }
        else
        {
            AddVisual(point);
        }
    }

    void OnPointerMoved(IInspectable const &, winrt::PointerEventArgs const & args)
    {
        if (m_selected)
        {
            winrt::float2 const point = args.CurrentPoint().Position();

            m_selected.Offset(
            {
                point.x + m_offset.x,
                point.y + m_offset.y,
                0.0f
            });
        }
    }

    void AddVisual(winrt::float2 const point)
    {
        winrt::Compositor compositor = m_visuals.Compositor();
        winrt::SpriteVisual visual = compositor.CreateSpriteVisual();

        static winrt::Color colors[] =
        {
            { 0xDC, 0x5B, 0x9B, 0xD5 },
            { 0xDC, 0xED, 0x7D, 0x31 },
            { 0xDC, 0x70, 0xAD, 0x47 },
            { 0xDC, 0xFF, 0xC0, 0x00 }
        };

        static unsigned last = 0;
        unsigned const next = ++last % _countof(colors);
        visual.Brush(compositor.CreateColorBrush(colors[next]));

        float const BlockSize = 100.0f;

        visual.Size(
        {
            BlockSize,
            BlockSize
        });

        visual.Offset(
        {
            point.x - BlockSize / 2.0f,
            point.y - BlockSize / 2.0f,
            0.0f,
        });

        m_visuals.InsertAtTop(visual);

        m_selected = visual;
        m_offset.x = -BlockSize / 2.0f;
        m_offset.y = -BlockSize / 2.0f;
    }
};

int __stdcall wWinMain(HINSTANCE, HINSTANCE, PWSTR, int)
{
    winrt::init_apartment();

    winrt::Uri uri(L"http://aka.ms/cppwinrt");
    std::wstringstream wstringstream;
    wstringstream << L"C++/WinRT: " << uri.Domain().c_str() << std::endl;

    // Convert from a C++/WinRT type to a C++/CX type.
    cx::Uri^ cx = to_cx<cx::Uri>(uri);
    wstringstream << L"C++/CX: " << cx->Domain->Data() << std::endl;
    ::OutputDebugString(wstringstream.str().c_str());

    // Convert from a C++/CX type to a C++/WinRT type.
    winrt::Uri uri_from_cx = from_cx<winrt::Uri>(cx);
    WINRT_ASSERT(uri.Domain() == uri_from_cx.Domain());
    WINRT_ASSERT(uri == uri_from_cx);

    winrt::CoreApplication::Run(winrt::make<App>());
}
```

## <a name="important-apis"></a>Важные API
* [Интерфейс IUnknown](https://docs.microsoft.com/windows/desktop/api/unknwn/nn-unknwn-iunknown)
* [QueryInterface-функция](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-queryinterface(q_))
* [winrt::get_abi function](/uwp/cpp-ref-for-winrt/get-abi)
* [функция WinRT::put_abi](/uwp/cpp-ref-for-winrt/put-abi)

## <a name="related-topics"></a>См. также
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
* [Переход на C++/WinRT из C++/CX](move-to-winrt-from-cx.md)
