---
author: stevewhims
description: В этом разделе описаны две вспомогательные функции, которые могут использоваться для преобразования между объектами C++/ CX и C++/WinRT.
title: Взаимодействие между C++/WinRT и C++/CX
ms.author: stwhi
ms.date: 05/21/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, перенос, взаимодействие, C++/CX
ms.localizationpriority: medium
ms.openlocfilehash: b60b0d7c201f172261de1546fc250e40b8cd670f
ms.sourcegitcommit: 933e71a31989f8063b020746fdd16e9da94a44c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "4536050"
---
# <a name="interop-between-cwinrt-and-ccx"></a>Взаимодействие между C++/WinRT и C++/CX
В этом разделе описаны две вспомогательные функции, которые могут использоваться для преобразования между [C + +/ CX](/cpp/cppcx/visual-c-language-reference-c-cx?branch=live) и [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) объектов. Их можно использовать для взаимодействия между кодом, который использует эти две языковых проекции, или можно использовать функции, как вы постепенного переноса кода с C + +/ CX в C + +/ WinRT (см. в разделе [Переход на C + +/ WinRT из C + +/ CX](move-to-winrt-from-cx.md)).

## <a name="fromcx-and-tocx-functions"></a>функции from_cx и to_cx
Вспомогательная функция ниже преобразует объект C++/CX в эквивалентный объект C++/WinRT. Она автоматически приводит объект C++/CX к его базовому указателю интерфейса [**IUnknown**](https://msdn.microsoft.com/library/windows/desktop/ms680509). Затем она вызывает [**QueryInterface**](https://msdn.microsoft.com/library/windows/desktop/ms682521) для этого указателя, чтобы запросить интерфейс по умолчанию для объекта C++/WinRT. **QueryInterface** — это эквивалент расширения safe_cast C++/CX в двоичном интерфейсе приложения (ABI) среды выполнения Windows. Функция [**winrt::put_abi**](/uwp/cpp-ref-for-winrt/put-abi) возвращает адрес базового указателя интерфейса **IUnknown** объекта C++/WinRT, чтобы для него можно было задать другое значение.

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

## <a name="code-example"></a>Пример кода
Ниже приведен пример кода (на основе шаблона проекта C++/CX **Пустое приложение**), который показывает использование двух вспомогательных функций. В нем также показано, как использовать псевдонимы пространств имен для различных островов, чтобы справиться с конфликтами пространств имен между проекцией C++/WinRT и ABI, а также проекцией C++/CX.

```cppwinrt
// MainPage.xaml.cpp

#include "pch.h"
#include "MainPage.xaml.h"
#include <winrt/Windows.Foundation.h>
#include <sstream>

using namespace InteropExample;

namespace cx
{
    using namespace Windows::Foundation;
}

namespace winrt
{
    using namespace Windows::Foundation;
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

MainPage::MainPage()
{
    InitializeComponent();

    winrt::init_apartment(winrt::apartment_type::single_threaded);

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
}
```

## <a name="important-apis"></a>Важные API
* [Интерфейс IUnknown interface](https://msdn.microsoft.com/library/windows/desktop/ms680509)
* [Функция QueryInterface](https://msdn.microsoft.com/library/windows/desktop/ms682521)
* [функция winrt::get_abi function](/uwp/cpp-ref-for-winrt/get-abi)
* [функция winrt::put_abi function](/uwp/cpp-ref-for-winrt/put-abi)

## <a name="related-topics"></a>Статьи по теме
* [C++/CX](/cpp/cppcx/visual-c-language-reference-c-cx)
