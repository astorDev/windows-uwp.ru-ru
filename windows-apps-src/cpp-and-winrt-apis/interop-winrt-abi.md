---
author: stevewhims
description: В этом разделе показано, как выполнять преобразование между объектами двоичного интерфейса приложений (ABI) и C++/WinRT.
title: Взаимодействие между C++/WinRT и интерфейсом ABI
ms.author: stwhi
ms.date: 05/21/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, перенос, взаимодействие, ABI
ms.localizationpriority: medium
ms.openlocfilehash: b641591e7be23226edc354e02513d723fbe8afba
ms.sourcegitcommit: e4f3e1b2d08a02b9920e78e802234e5b674e7223
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "4205574"
---
# <a name="interop-between-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt-and-the-abi"></a>Взаимодействие между [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) и интерфейсом ABI
В этом разделе показано, как выполнять преобразование между объектами двоичного интерфейса приложений SDK (ABI) и C++/WinRT. Эти техники можно использовать для взаимодействия между кодом, который использует эти два способа программирования с помощью среды выполнения Windows, или для постепенного переноса кода с ABI на C++/WinRT.

## <a name="what-is-the-windows-runtime-abi-and-what-are-abi-types"></a>Что такое ABI среды выполнения Windows и что такое типы ABI?
Класс среды выполнения Windows (класс среды выполнения)— это, на самом деле, абстракция. Такая абстракция определяет двоичный интерфейс (двоичный интерфейс приложения или ABI), позволяющий различным языкам программирования взаимодействовать с объектом. Независимо от языка программирования, взаимодействие клиентского кода с объектом среды выполнения Windows происходит на самом низком уровне, при этом языковые конструкции клиента преобразуются в вызовы ABI объекта.

Заголовки пакета Windows SDK в папке "%WindowsSdkDir%Include\10.0.17134.0\winrt" (при необходимости измените номер версии пакета SDK на используемый), представляют собой файлы заголовков ABI среды выполнения Windows. Они были созданы с помощью компилятора MIDL. Вот пример включения одного из таких заголовков.

```
#include <windows.foundation.h>
```

А вот упрощенный пример одного из типов ABI, которые можно найти в этом конкретном заголовке SDK. Обратите внимание, что пространство имен **ABI**, **Windows::Foundation** и все остальные пространства имен Windows объявляются заголовками SDK внутри пространства имен **ABI**.

```
namespace ABI::Windows::Foundation
{
    IUriRuntimeClass : public IInspectable
    {
    public:
        /* [propget] */ virtual HRESULT STDMETHODCALLTYPE get_AbsoluteUri(/* [retval, out] */__RPC__deref_out_opt HSTRING * value) = 0;
        ...
    }
}
```

**IUriRuntimeClass** представляет собой COM-интерфейс. И более того, поскольку он основан на **IInspectable**, **IUriRuntimeClass** — это интерфейс среды выполнения Windows. Обратите внимание на тип возврата **HRESULT**, а не на вызов исключений, а также на использование артефактов, таких как дескриптор **HSTRING** (рекомендуется снова задать значение `nullptr` для этого дескриптора после окончания работы с ним). Это дает понять, как выглядит среда выполнения Windows на двоичном уровне приложения; другими словами, на уровне в COM-программирования.

Среда выполнения Windows основывается на API-интерфейсы модели COM. Доступ к среде выполнения Windows можно получить таким образом или через *языковые проекции*. Проекция скрывает подробности COM и обеспечивает более естественный подход к программированию на используемом языке.

Например, в папке "%WindowsSdkDir%Include\10.0.17134.0\cppwinrt\winrt" (при необходимости измените номер версии пакета SDK на используемый) вы обнаружите заголовки проекции языка C++/WinRT. Для каждого пространства имен Windows существует заголовок, так же, как для каждого пространства имен Windows есть один заголовок ABI. Вот пример включения одного из заголовков C++/WinRT.

```cppwinrt
#include <winrt/Windows.Foundation.h>
```

А вот (из этого заголовка) упрощенный эквивалент C++/WinRT типа ABI, который мы только что видели.

```
namespace winrt::Windows::Foundation
{
    struct Uri : IUriRuntimeClass, ...
    {
        winrt::hstring AbsoluteUri() const { ... }
        ...
    };
}
```

Интерфейс здесь представляет собой современный стандартный C++. Он избавляет от необходимости в **HRESULT** (при необходимости C++/ WinRT вызывает исключения). Функция доступа возвращает простой строковый объект, который очищается в конце его области видимости.

Этот раздел предназначен для тех случаев, когда вам требуется взаимодействие с кодом, который работает на уровне двоичного интерфейса приложения (ABI), и необходимо портировать этот код.

## <a name="converting-to-and-from-abi-types-in-code"></a>Преобразование в типы ABI и обратно в коде
Для безопасности и простоты для преобразования в обоих направлениях можно использовать [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr), [**com_ptr::as**](/uwp/cpp-ref-for-winrt/com-ptr#comptras-function) и [**winrt::Windows::Foundation::IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function). Вот пример кода (на основе шаблона проекта **Консольное приложение**), который также показывает, как использовать псевдонимы пространств имен для различных островов, чтобы справиться с конфликтами пространств имен между проекцией C++/WinRT и ABI.

```cppwinrt
// main.cpp
#include "pch.h"

#include <windows.foundation.h>
#include <winrt/Windows.Foundation.h>

namespace winrt
{
    using namespace Windows::Foundation;
}

namespace abi
{
    using namespace ABI::Windows::Foundation;
};

int main()
{
    winrt::init_apartment();

    winrt::Uri uri(L"http://aka.ms/cppwinrt");

    // Convert to an ABI type.
    winrt::com_ptr<abi::IStringable> ptr = uri.as<abi::IStringable>();

    // Convert from an ABI type.
    uri = ptr.as<winrt::Uri>();
    winrt::IStringable uriAsIStringable = ptr.as<winrt::IStringable>();
}
```

Реализации функций **as** вызывают [**QueryInterface**](https://msdn.microsoft.com/library/windows/desktop/ms682521). Если вам нужны преобразования более низкого уровня, которые вызывают только [**AddRef**](https://msdn.microsoft.com/library/windows/desktop/ms691379), вы можете использовать вспомогательные функции [**winrt::copy_to_abi**](/uwp/cpp-ref-for-winrt/copy-to-abi) и [**winrt::copy_from_abi**](/uwp/cpp-ref-for-winrt/copy-from-abi). В следующем примере кода эти преобразования более низкого уровня добавлены к примеру, приведенному выше.

```cppwinrt
int main()
{
    ...

    // Lower-level conversions that only call AddRef.

    // Convert to an ABI type.
    ptr = nullptr;
    winrt::copy_to_abi(uri, *ptr.put_void());

    // Convert from an ABI type.
    uri = nullptr;
    winrt::copy_from_abi(uri, ptr.get());
    ptr = nullptr;
}
```

Ниже приведены другие методы преобразования аналогично низкого уровня, но на этот раз с использованием необработанных указателей на типы интерфейса ABI (определенные заголовками Windows SDK).

```cppwinrt
    ...

    // Copy to an owning raw ABI pointer with copy_to_abi.
    abi::IStringable* owning = nullptr;
    winrt::copy_to_abi(uri, *reinterpret_cast<void**>(&owning));

    // Copy from a raw ABI pointer.
    uri = nullptr;
    winrt::copy_from_abi(uri, owning);
    owning->Release();
```

Для преобразований самого низкого уровня, которые копируют только адреса, можно использовать вспомогательные функции [**winrt::get_abi**](/uwp/cpp-ref-for-winrt/get-abi), [**winrt::detach_abi**](/uwp/cpp-ref-for-winrt/detach-abi) и [**winrt::attach_abi**](/uwp/cpp-ref-for-winrt/attach-abi).

```cppwinrt
    ...

    // Lowest-level conversions that only copy addresses

    // Convert to a non-owning ABI object with get_abi.
    abi::IStringable* non_owning = static_cast<abi::IStringable*>(winrt::get_abi(uri));
    WINRT_ASSERT(non_owning);

    // Avoid interlocks this way.
    owning = static_cast<abi::IStringable*>(winrt::detach_abi(uri));
    WINRT_ASSERT(!uri);
    winrt::attach_abi(uri, owning);
    WINRT_ASSERT(uri);
```

## <a name="convertfromabi-function"></a>Функция convert_from_abi
Эта вспомогательная функция преобразует необработанный указатель интерфейса ABI в эквивалентный объект C++/WinRT с минимальными затратами.

```cppwinrt
template <typename T>
T convert_from_abi(::IUnknown* from)
{
    T to{ nullptr };

    winrt::check_hresult(from->QueryInterface(winrt::guid_of<T>(),
        reinterpret_cast<void**>(winrt::put_abi(to))));

    return to;
}
```

Эта функция просто вызывает [**QueryInterface**](https://msdn.microsoft.com/library/windows/desktop/ms682521), чтобы запросить интерфейс по умолчанию запрошенного типа C++/WinRT.

Как мы выяснили, вспомогательная функция не требуется для преобразования объекта C++/WinRT в эквивалентный указатель интерфейса ABI. Просто используйте функцию-член [**winrt::Windows::Foundation::IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) (или [**try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntryas-function)) для запроса нужного интерфейса. Функции **as** и **try_as** возвращают объект [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr), в который упакован запрошенный тип ABI.

## <a name="code-example-using-convertfromabi"></a>Пример кода, использующий convert_from_abi
Ниже приведен пример кода, показывающий работу этой вспомогательной функции на практике.

```cppwinrt
// main.cpp
#include "pch.h"

#include <windows.foundation.h>
#include <winrt/Windows.Foundation.h>
#include <iostream>

using namespace winrt;
using namespace Windows::Foundation;

namespace winrt
{
    using namespace Windows::Foundation;
}

namespace abi
{
    using namespace ABI::Windows::Foundation;
};

template <typename T>
T convert_from_abi(::IUnknown* from)
{
    T to{ nullptr };

    winrt::check_hresult(from->QueryInterface(winrt::guid_of<T>(),
        reinterpret_cast<void**>(winrt::put_abi(to))));

    return to;
}

int main()
{
    winrt::init_apartment();

    winrt::Uri uri(L"http://aka.ms/cppwinrt");
    std::wcout << "C++/WinRT: " << uri.Domain().c_str() << std::endl;

    // Convert to an ABI type.
    winrt::com_ptr<abi::IUriRuntimeClass> ptr = uri.as<abi::IUriRuntimeClass>();
    winrt::hstring domain;
    winrt::check_hresult(ptr->get_Domain(put_abi(domain)));
    std::wcout << "ABI: " << domain.c_str() << std::endl;

    // Convert from an ABI type.
    winrt::Uri uri_from_abi = convert_from_abi<winrt::Uri>(ptr.get());

    WINRT_ASSERT(uri.Domain() == uri_from_abi.Domain());
    WINRT_ASSERT(uri == uri_from_abi);
}
```

## <a name="important-apis"></a>Важные API
* [Функция AddRef](https://msdn.microsoft.com/library/windows/desktop/ms691379)
* [Функция QueryInterface](https://msdn.microsoft.com/library/windows/desktop/ms682521)
* [функция WinRT::attach_abi](/uwp/cpp-ref-for-winrt/attach-abi)
* [Шаблон структуры winrt::com_ptr](/uwp/cpp-ref-for-winrt/com-ptr)
* [функция WinRT::copy_from_abi](/uwp/cpp-ref-for-winrt/copy-from-abi)
* [функция WinRT::copy_to_abi](/uwp/cpp-ref-for-winrt/copy-to-abi)
* [функция WinRT::detach_abi](/uwp/cpp-ref-for-winrt/detach-abi)
* [функция winrt::get_abi function](/uwp/cpp-ref-for-winrt/get-abi)
* [Функция-член winrt::Windows::Foundation::IUnknown::as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function)
* [Функция-член winrt::Windows::Foundation::IUnknown::try_as](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntryas-function)
