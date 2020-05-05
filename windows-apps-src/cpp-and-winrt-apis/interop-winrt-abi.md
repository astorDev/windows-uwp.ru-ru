---
description: В этом разделе показано, как выполнять преобразование между объектами двоичного интерфейса приложений (ABI) и C++/WinRT.
title: Взаимодействие между C++/WinRT и интерфейсом ABI
ms.date: 11/30/2018
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, port, migrate, interop, ABI
ms.localizationpriority: medium
ms.openlocfilehash: 91602c75cdaddc325407529ab4d231db46ecca39
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79209149"
---
# <a name="interop-between-cwinrt-and-the-abi"></a>Взаимодействие между C++/WinRT и интерфейсом ABI

В этом разделе показано, как выполнять преобразование между объектами двоичного интерфейса приложений SDK (ABI) и [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt). Эти методики можно использовать для взаимодействия между кодом, который использует эти два способа программирования, и средой выполнения Windows, или для постепенного переноса кода с ABI на C++/WinRT.

В общем случае C++/WinRT предоставляет типы ABI как **void\*** , поэтому файлы заголовков платформы добавлять не нужно.

## <a name="what-is-the-windows-runtime-abi-and-what-are-abi-types"></a>Что такое ABI среды выполнения Windows и что такое типы ABI?
Класс среды выполнения Windows (класс среды выполнения) — это, на самом деле, абстракция. Такая абстракция определяет двоичный интерфейс (двоичный интерфейс приложения или ABI), позволяющий различным языкам программирования взаимодействовать с объектом. Независимо от языка программирования, взаимодействие клиентского кода с объектом среды выполнения Windows происходит на самом низком уровне, при этом языковые конструкции клиента преобразуются в вызовы ABI объекта.

Заголовки пакета Windows SDK в папке "%WindowsSdkDir%Include\10.0.17134.0\winrt" (при необходимости измените номер версии пакета SDK на используемый), представляют собой файлы заголовков ABI среды выполнения Windows. Они были созданы с помощью компилятора MIDL. Далее приведен пример включения одного из таких заголовков.

```cpp
#include <windows.foundation.h>
```

А также упрощенный пример одного из типов ABI, которые можно найти в этом конкретном заголовке SDK. Обратите внимание, что пространство имен **ABI**, **Windows::Foundation** и все остальные пространства имен Windows объявляются заголовками SDK внутри пространства имен **ABI**.

```cpp
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

**IUriRuntimeClass** представляет собой COM-интерфейс. И более того&mdash;, поскольку он основан на **IInspectable**&mdash;, **IUriRuntimeClass** — это интерфейс среды выполнения Windows. Обратите внимание на тип возврата **HRESULT**, а не на вызов исключений, а также на использование таких артефактов, как дескриптор **HSTRING** (рекомендуется снова задать значение `nullptr` для этого дескриптора после окончания работы с ним). Это дает понять, как выглядит среда выполнения Windows на двоичном уровне приложения; другими словами, на уровне в COM-программирования.

Среда выполнения Windows основывается на API-интерфейсы модели COM. Доступ к среде выполнения Windows можно получить таким образом или через *языковые проекции*. Использование проекции позволяет скрывать подробности COM и обеспечить более естественный подход к программированию на используемом языке.

Например, в папке "%WindowsSdkDir%Include\10.0.17134.0\cppwinrt\winrt" (при необходимости можно изменить номер версии пакета SDK на используемый) вы обнаружите заголовки проекции языка C++/WinRT. Для каждого пространства имен Windows существует заголовок, так же, как для каждого пространства имен Windows есть один заголовок ABI. Пример включения одного из заголовков C++/WinRT.

```cppwinrt
#include <winrt/Windows.Foundation.h>
```

А вот (из этого заголовка) упрощенный эквивалент C++/WinRT типа ABI, который мы только что видели.

```cppwinrt
namespace winrt::Windows::Foundation
{
    struct Uri : IUriRuntimeClass, ...
    {
        winrt::hstring AbsoluteUri() const { ... }
        ...
    };
}
```

Интерфейс здесь представляет собой современный стандартный C++. Он избавляет от необходимости в **HRESULT** (при необходимости C++/WinRT вызывает исключения). Функция доступа возвращает простой строковый объект, который очищается в конце его области видимости.

Этот раздел предназначен для тех случаев, когда вам требуется взаимодействие с кодом, который работает на уровне двоичного интерфейса приложения (ABI), и необходимо портировать этот код.

## <a name="converting-to-and-from-abi-types-in-code"></a>Преобразование в типы ABI и обратно в коде
Для безопасности и простоты во время преобразования в обоих направлениях можно использовать [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr), [**com_ptr::as**](/uwp/cpp-ref-for-winrt/com-ptr#com_ptras-function) и [**winrt::Windows::Foundation::IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function). Далее приведен пример кода (на основе шаблона проекта **Консольное приложение**), в котором также показано, как использовать псевдонимы пространств имен для различных островов, чтобы разрешить конфликты пространств имен между проекцией C++/WinRT и ABI.

```cppwinrt
// pch.h
#pragma once
#include <windows.foundation.h>
#include <unknwn.h>
#include "winrt/Windows.Foundation.h"

// main.cpp
#include "pch.h"

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
    winrt::com_ptr<abi::IStringable> ptr{ uri.as<abi::IStringable>() };

    // Convert from an ABI type.
    uri = ptr.as<winrt::Uri>();
    winrt::IStringable uriAsIStringable{ ptr.as<winrt::IStringable>() };
}
```

Реализации функций **as** вызывают [**QueryInterface**](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-queryinterface(q_)). Если вам нужны преобразования более низкого уровня, которые вызывают только [**AddRef**](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-addref), вы можете использовать вспомогательные функции [**winrt::copy_to_abi**](/uwp/cpp-ref-for-winrt/copy-to-abi) и [**winrt::copy_from_abi**](/uwp/cpp-ref-for-winrt/copy-from-abi). В следующем примере кода эти преобразования более низкого уровня добавлены к примеру, приведенному выше.

```cppwinrt
int main()
{
    // The code in main() already shown above remains here.

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

Ниже приведены другие методы преобразования аналогично низкого уровня, но на этот раз в них используются необработанные указатели типов интерфейса ABI (определенные заголовками Windows SDK).

```cppwinrt
    // The code in main() already shown above remains here.

    // Copy to an owning raw ABI pointer with copy_to_abi.
    abi::IStringable* owning{ nullptr };
    winrt::copy_to_abi(uri, *reinterpret_cast<void**>(&owning));

    // Copy from a raw ABI pointer.
    uri = nullptr;
    winrt::copy_from_abi(uri, owning);
    owning->Release();
```

Для преобразований самого низкого уровня, которые копируют только адреса, можно использовать вспомогательные функции [**winrt::get_abi**](/uwp/cpp-ref-for-winrt/get-abi), [**winrt::detach_abi**](/uwp/cpp-ref-for-winrt/detach-abi) и [**winrt::attach_abi**](/uwp/cpp-ref-for-winrt/attach-abi).

`WINRT_ASSERT` — это макроопределение, которое передается в [_ASSERTE](/cpp/c-runtime-library/reference/assert-asserte-assert-expr-macros).

```cppwinrt
    // The code in main() already shown above remains here.

    // Lowest-level conversions that only copy addresses

    // Convert to a non-owning ABI object with get_abi.
    abi::IStringable* non_owning{ static_cast<abi::IStringable*>(winrt::get_abi(uri)) };
    WINRT_ASSERT(non_owning);

    // Avoid interlocks this way.
    owning = static_cast<abi::IStringable*>(winrt::detach_abi(uri));
    WINRT_ASSERT(!uri);
    winrt::attach_abi(uri, owning);
    WINRT_ASSERT(uri);
```

## <a name="convert_from_abi-function"></a>Функция convert_from_abi
Эта вспомогательная функция преобразует необработанный указатель интерфейса ABI в эквивалентный объект C++/WinRT с минимальными затратами.

```cppwinrt
template <typename T>
T convert_from_abi(::IUnknown* from)
{
    T to{ nullptr }; // `T` is a projected type.

    winrt::check_hresult(from->QueryInterface(winrt::guid_of<T>(),
        winrt::put_abi(to)));

    return to;
}
```

Эта функция просто вызывает [**QueryInterface**](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-queryinterface(q_)), чтобы запросить интерфейс по умолчанию запрошенного типа C++/WinRT.

Как мы уже выяснили для преобразования объекта C++/WinRT в эквивалентный указатель интерфейса ABI вспомогательной функции не требуется. Просто используйте функцию-член [**winrt::Windows::Foundation::IUnknown::as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) (или [**try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntry_as-function)) для запроса нужного интерфейса. Функции **as** и **try_as** возвращают объект [**winrt::com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr), в который упакован запрошенный тип ABI.

## <a name="code-example-using-convert_from_abi"></a>Пример кода, в котором используется convert_from_abi
Ниже приведен пример кода, показывающий работу этой вспомогательной функции на практике.

```cppwinrt
// pch.h
#pragma once
#include <windows.foundation.h>
#include <unknwn.h>
#include "winrt/Windows.Foundation.h"

// main.cpp
#include "pch.h"
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

namespace sample
{
    template <typename T>
    T convert_from_abi(::IUnknown* from)
    {
        T to{ nullptr }; // `T` is a projected type.

        winrt::check_hresult(from->QueryInterface(winrt::guid_of<T>(),
            winrt::put_abi(to)));

        return to;
    }
    inline auto put_abi(winrt::hstring& object) noexcept
    {
        return reinterpret_cast<HSTRING*>(winrt::put_abi(object));
    }
}

int main()
{
    winrt::init_apartment();

    winrt::Uri uri(L"http://aka.ms/cppwinrt");
    std::wcout << "C++/WinRT: " << uri.Domain().c_str() << std::endl;

    // Convert to an ABI type.
    winrt::com_ptr<abi::IUriRuntimeClass> ptr = uri.as<abi::IUriRuntimeClass>();
    winrt::hstring domain;
    winrt::check_hresult(ptr->get_Domain(sample::put_abi(domain)));
    std::wcout << "ABI: " << domain.c_str() << std::endl;

    // Convert from an ABI type.
    winrt::Uri uri_from_abi = sample::convert_from_abi<winrt::Uri>(ptr.get());

    WINRT_ASSERT(uri.Domain() == uri_from_abi.Domain());
    WINRT_ASSERT(uri == uri_from_abi);
}
```

## <a name="interoperating-with-abi-com-interface-pointers"></a>Взаимодействие с указателями интерфейса СОМ ABI

Показанный ниже шаблон вспомогательной функции демонстрирует, как скопировать указатель интерфейса COM ABI заданного типа в его эквивалент C++/WinRT, представляющий собой смарт-указатель типа проекции.

```cppwinrt
template<typename To, typename From>
To to_winrt(From* ptr)
{
    To result{ nullptr };
    winrt::check_hresult(ptr->QueryInterface(winrt::guid_of<To>(), winrt::put_abi(result)));
    return result;
}
...
ID2D1Factory1* com_ptr{ ... };
auto cppwinrt_ptr {to_winrt<winrt::com_ptr<ID2D1Factory1>>(com_ptr)};
```

Следующий шаблон вспомогательной функции аналогичен предыдущему, за исключением того, что он копирует тип смарт-указателя из [библиотек реализации Windows (WIL)](https://github.com/Microsoft/wil).

```cppwinrt
template<typename To, typename From, typename ErrorPolicy>
To to_winrt(wil::com_ptr_t<From, ErrorPolicy> const& ptr)
{
    To result{ nullptr };
    if constexpr (std::is_same_v<typename ErrorPolicy::result, void>)
    {
        ptr.query_to(winrt::guid_of<To>(), winrt::put_abi(result));
    }
    else
    {
        winrt::check_result(ptr.query_to(winrt::guid_of<To>(), winrt::put_abi(result)));
    }
    return result;
}
```

Ознакомьтесь также со статьей [Использование компонентов COM с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/consume-com).

### <a name="unsafe-interop-with-abi-com-interface-pointers"></a>Небезопасное взаимодействие с указателями интерфейса СОМ ABI

В таблице ниже показаны (наряду с другими операциями) небезопасные преобразования указателя интерфейса COM ABI заданного типа и его эквивалента C++/WinRT, представляющего собой смарт-указатель типа проекции. Предполагается, что в коде используются следующие объявления.

```cppwinrt
winrt::Sample s;
ISample* p;

void GetSample(_Out_ ISample** pp);
```

Также предполагается, что **ISample** — это интерфейс по умолчанию для **Sample**.

Сделать это можно во время компиляции этого кода.

```cppwinrt
static_assert(std::is_same_v<winrt::default_interface<winrt::Sample>, winrt::ISample>);
```

| Операция | Способ выполнения | Примечания |
|-|-|-|
| Извлечение **ISample\*** из **winrt::Sample** | `p = reinterpret_cast<ISample*>(get_abi(s));` | *s* по-прежнему является владельцем объекта. |
| Отсоединение **ISample\*** от **winrt::Sample** | `p = reinterpret_cast<ISample*>(detach_abi(s));` | *s* больше не является владельцем объекта. |
| Передача **ISample\*** в новый объект **winrt::Sample** | `winrt::Sample s{ p, winrt::take_ownership_from_abi };` | *s* становится владельцем объекта. |
| Указание **ISample\*** в **winrt::Sample** | `*put_abi(s) = p;` | *s* становится владельцем объекта. Высвобождается любой объект, которые ранее принадлежал *s* (происходит при отладке). |
| Получение **ISample\*** в **winrt::Sample** | `GetSample(reinterpret_cast<ISample**>(put_abi(s)));` | *s* становится владельцем объекта. Высвобождается любой объект, которые ранее принадлежал *s* (происходит при отладке). |
| Замена **ISample\*** в **winrt::Sample** | `attach_abi(s, p);` | *s* становится владельцем объекта. Высвобождается объект, который ранее принадлежал *s*. |
| Копирование **ISample\*** в **winrt::Sample** | `copy_from_abi(s, p);` | *s* создает новую ссылку на объект. Высвобождается объект, который ранее принадлежал *s*. |
| Копирование **winrt::Sample** в **ISample\*** | `copy_to_abi(s, reinterpret_cast<void*&>(p));` | *p* получает копию объекта. Высвобождается любой объект, который ранее принадлежал *p*. |

## <a name="interoperating-with-the-abis-guid-struct"></a>Взаимодействие со структурой GUID ABI

[**GUID**](/previous-versions/aa373931(v%3Dvs.80)) теперь проецируется как **winrt::guid**. Для API-интерфейсов, которые вы реализуете, необходимо использовать **winrt::guid** для параметров GUID. В противном случае выполняется автоматическое преобразование **winrt::guid** и **GUID**, если добавлено `unknwn.h` (добавлено неявно с помощью <windows.h> или других файлов заголовков) до добавления заголовков C++/WinRT.

Но вы также можете использовать `reinterpret_cast`. Предполагается, что в коде используются следующие объявления.

```cppwinrt
winrt::guid winrtguid;
GUID abiguid;
```

| Преобразование | С добавлением `#include <unknwn.h>` | Без добавления `#include <unknwn.h>` |
|-|-|-|
| Из **winrt::guid** в **GUID** | `abiguid = winrtguid;` | `abiguid = reinterpret_cast<GUID&>(winrtguid);` |
| Из **GUID** в **winrt::guid** | `winrtguid = abiguid;` | `winrtguid = reinterpret_cast<winrt::guid&>(abiguid);` |

## <a name="interoperating-with-the-abis-hstring"></a>Взаимодействие с HSTRING ABI

В следующей таблице показано, как выполнять преобразования **winrt::hstring** и [**HSTRING**](/windows/win32/winrt/hstring), а также другие операции. Предполагается, что в коде используются следующие объявления.

```cppwinrt
winrt::hstring s;
HSTRING h;

void GetString(_Out_ HSTRING* value);
```

| Операция | Способ выполнения | Примечания |
|-|-|-|
| Извлечение **HSTRING** из **hstring** | `h = static_cast<HSTRING>(get_abi(s));` | *s* по-прежнему является владельцем строки. |
| Отсоединение **HSTRING** от **hstring** | `h = reinterpret_cast<HSTRING>(detach_abi(s));` | *s* больше не является владельцем строки. |
| Указание **HSTRING** в **hstring** | `*put_abi(s) = h;` | *s* становится владельцем строки. Высвобождается любая строка, которая ранее принадлежала *s* (происходит при отладке). |
| Получение **HSTRING** в **hstring** | `GetString(reinterpret_cast<HSTRING*>(put_abi(s)));` | *s* становится владельцем строки. Высвобождается любая строка, которая ранее принадлежала *s* (происходит при отладке). |
| Замена **HSTRING** в **hstring** | `attach_abi(s, h);` | *s* становится владельцем строки. Высвобождается строка, которая ранее принадлежала *s*. |
| Копирование **HSTRING** в **hstring** | `copy_from_abi(s, h);` | *s* создает закрытую копию строки. Высвобождается строка, которая ранее принадлежала *s*. |
| Копирование **hstring** в **HSTRING** | `copy_to_abi(s, reinterpret_cast<void*&>(h));` | *h* получает копию строки. Высвобождается любая строка, которая ранее принадлежала *h*. |

## <a name="important-apis"></a>Важные API
* [IUnknown::AddRef method](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-addref) (Метод IUnknown::AddRef)
* [IUnknown::QueryInterface method](https://docs.microsoft.com/windows/desktop/api/unknwn/nf-unknwn-iunknown-queryinterface(q_)) (Метод IUnknown::QueryInterface)
* [winrt::attach_abi function (C++/WinRT)](/uwp/cpp-ref-for-winrt/attach-abi) (Функция winrt::attach_abi (C++/WinRT))
* [Шаблон структуры winrt::com_ptr](/uwp/cpp-ref-for-winrt/com-ptr)
* [winrt::copy_from_abi function (C++/WinRT)](/uwp/cpp-ref-for-winrt/copy-from-abi) (Функция winrt::copy_from_abi (C++/WinRT))
* [winrt::copy_to_abi function (C++/WinRT)](/uwp/cpp-ref-for-winrt/copy-to-abi) (Функция winrt::copy_to_abi (C++/WinRT))
* [winrt::detach_abi function (C++/WinRT)](/uwp/cpp-ref-for-winrt/detach-abi) (Функция winrt::detach_abi (C++/WinRT))
* [winrt::get_abi function (C++/WinRT)](/uwp/cpp-ref-for-winrt/get-abi) (Функция winrt::get_abi (C++/WinRT))
* [winrt::Windows::Foundation::IUnknown struct (C++/WinRT)](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) (Структура winrt::Windows::Foundation::IUnknown)
* [winrt::Windows::Foundation::IUnknown struct (C++/WinRT)](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntry_as-function) (Структура winrt::Windows::Foundation::IUnknown (C++/WinRT))
