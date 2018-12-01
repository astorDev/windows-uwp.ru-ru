---
description: С помощью C++/WinRT можно вызывать API среды выполнения Windows, используя стандартные типы широких строк C++ или тип winrt::hstring.
title: Обработка строк в C++/WinRT
ms.date: 10/03/2018
ms.topic: article
keywords: Windows 10, UWP, стандартные, c++, cpp, winrt, проекция, строка
ms.localizationpriority: medium
ms.openlocfilehash: 9572d9ba8b96d245b783535e159acbae9043ea3e
ms.sourcegitcommit: d2517e522cacc5240f7dffd5bc1eaa278e3f7768
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8343328"
---
# <a name="string-handling-in-cwinrt"></a>Обработка строк в C++/WinRT

С помощью [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), можно вызывать API среды выполнения Windows, используя типы широких строк стандартной библиотеки C++, такие как **std::wstring** (Примечание: не с типами узкой строку, например **std::string**). C++/WinRT имеет настраиваемый тип строки под названием [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) (определяется в базовой библиотеке C++/WinRT — `%WindowsSdkDir%Include\<WindowsTargetPlatformVersion>\cppwinrt\winrt\base.h`). Этот тип строки, который конструкторы, функции и свойства среды выполнения Windows фактически принимают и возвращают. Однако во многих случаях &mdash; благодаря конструкторам преобразования **hstring** и операторам преобразования &mdash; можно выбирать, следует ли учитывать **hstring** в клиентском коде. Если вы *разрабатываете* API-интерфейсы, вероятность того, что вам нужно знать о **hstring**, возрастает.

В C++ существует множество типов строки. Различные варианты существуют во множестве библиотек в дополнение к **std::basic_string** из стандартной библиотеки C++. С++17 имеет служебные программы для преобразования строк и **std::basic_string_view**, чтобы ликвидировать пробелы между всеми типами строк.  [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) дает возможность преобразования с **std::wstring_view** для обеспечения взаимодействия, для которого был создан **std::basic_string_view**.

## <a name="using-stdwstring-and-optionally-winrthstring-with-uri"></a>Использование **std::wstring** (и, при необходимости, **winrt::hstring**) с **Uri**
[**Windows::Foundation::Uri**](/uwp/api/windows.foundation.uri) формируется из [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring).

```cppwinrt
public:
    Uri(winrt::hstring uri) const;
```

Но **hstring** имеет [конструкторы преобразования](/uwp/api/windows.foundation.uri#hstringhstring-constructor), позволяющие работать с ним без необходимости его учитывать. Ниже приведен пример кода, показывающий, как создать **Uri** из литерала широкой строки, представления широкой строки и **std::wstring**.

```cppwinrt
#include <winrt/Windows.Foundation.h>
#include <string_view>

using namespace winrt;
using namespace Windows::Foundation;

int main()
{
    using namespace std::literals;

    winrt::init_apartment();

    // You can make a Uri from a wide string literal.
    Uri contosoUri{ L"http://www.contoso.com" };

    // Or from a wide string view.
    Uri contosoSVUri{ L"http://www.contoso.com"sv };

    // Or from a std::wstring.
    std::wstring wideString{ L"http://www.adventure-works.com" };
    Uri awUri{ wideString };
}
```

Метод доступа к свойству [**Uri::Domain**](https://docs.microsoft.com/uwp/api/windows.foundation.uri.Domain) имеет тип **hstring**.

```cppwinrt
public:
    winrt::hstring Domain();
```

Но опять же, знать об этом не обязательно благодаря [оператору преобразования **hstring** в **std::wstring_view**](/uwp/api/hstring#hstringoperator-stdwstringview).

```cppwinrt
// Access a property of type hstring, via a conversion operator to a standard type.
std::wstring domainWstring{ contosoUri.Domain() }; // L"contoso.com"
domainWstring = awUri.Domain(); // L"adventure-works.com"

// Or, you can choose to keep the hstring unconverted.
hstring domainHstring{ contosoUri.Domain() }; // L"contoso.com"
domainHstring = awUri.Domain(); // L"adventure-works.com"
```

Аналогичным образом [**IStringable::ToString**](https://msdn.microsoft.com/library/windows/desktop/dn302136) возвращает hstring.

```cppwinrt
public:
    hstring ToString() const;
```

**Uri** реализует интерфейс [**IStringable**](https://msdn.microsoft.com/library/windows/desktop/dn302135).

```cppwinrt
// Access hstring's IStringable::ToString, via a conversion operator to a standard type.
std::wstring tostringWstring{ contosoUri.ToString() }; // L"http://www.contoso.com/"
tostringWstring = awUri.ToString(); // L"http://www.adventure-works.com/"

// Or you can choose to keep the hstring unconverted.
hstring tostringHstring{ contosoUri.ToString() }; // L"http://www.contoso.com/"
tostringHstring = awUri.ToString(); // L"http://www.adventure-works.com/"
```

Можно использовать функцию [**hstring::c_str**](/uwp/api/windows.foundation.uri#hstringcstr-function) для получения стандартной широкой строки из **hstring** (так же, как и из **std::wstring**).

```cppwinrt
#include <iostream>
std::wcout << tostringHstring.c_str() << std::endl;
```
Если у вас есть **hstring**, значит вы можете получить **Uri**.

```cppwinrt
Uri awUriFromHstring{ tostringHstring };
```

Рассмотрим метод, который принимает **hstring**.

```cppwinrt
public:
    Uri CombineUri(winrt::hstring relativeUri) const;
```

Все параметры, которые мы рассмотрели, также применяются в таких случаях.

```cppwinrt
std::wstring contact{ L"contact" };
contosoUri = contosoUri.CombineUri(contact);
    
std::wcout << contosoUri.ToString().c_str() << std::endl;
```

**hstring** имеет член-оператор преобразования **std::wstring_view** и преобразование осуществляется без дополнительных затрат.

```cppwinrt
void legacy_print(std::wstring_view view);

void Print(winrt::hstring const& hstring)
{
    legacy_print(hstring);
}
```

## <a name="winrthstring-functions-and-operators"></a>Функции и операторы **winrt::hstring**
Для [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) реализован ряд конструкторов, операторов, функций и итераторов.

**hstring** является диапазоном, поэтому вы можете использовать его с основанным на диапазоне `for` или с `std::for_each`. Он также предоставляет операторы сравнения для естественного и эффективного сравнения с его аналогами в стандартной библиотеке C++. Кроме того, он включает все, что необходимо для использования **hstring** в качестве ключа для ассоциативных контейнеров.

Мы понимаем, что многие библиотеки C++ используют **std::string** и работают только с текстом UTF-8. Для удобства мы предоставляем вспомогательные методы, такие как [**winrt::to_string**](/uwp/cpp-ref-for-winrt/to-string) и [**winrt::to_hstring**](/uwp/cpp-ref-for-winrt/to-hstring), для двустороннего преобразования.

```cppwinrt
winrt::hstring w{ L"Hello, World!" };

std::string c = winrt::to_string(w);
WINRT_ASSERT(c == "Hello, World!");

w = winrt::to_hstring(c);
WINRT_ASSERT(w == L"Hello, World!");
```

Дополнительные примеры и сведения о функциях и операторах **hstring** см. в справочнике по API в разделе [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring).

## <a name="the-rationale-for-winrthstring-and-winrtparamhstring"></a>Обоснование для **winrt::hstring** и **winrt::param::hstring**
Среда выполнения Windows реализуется в символах **wchar_t**, но двоичный интерфейс приложения (ABI) среды выполнения Windows не является подмножеством того, что предоставляют **std::wstring** или **std::wstring_view**. Их использование приведет к значительной неэффективности. Вместо этого C++/WinRT предоставляет **winrt::hstring**, представляющий собой неизменяемую строку, согласованную с базовым [HSTRING](https://msdn.microsoft.com/library/windows/desktop/br205775) и реализованную за интерфейсом, аналогичным **std::wstring**. 

Можно заметить, что входные параметры C++/ WinRT, которые логически должны принимать **winrt::hstring**, фактически ожидают **winrt::param::hstring**. Пространство имен **param** содержит набор типов, используемых исключительно для оптимизации входных параметров для естественной привязки к типам стандартной библиотеки C++, а также позволяющих избежать копий и других аспектов, снижающих эффективность. Эти типы не следует использовать напрямую. Если вы хотите использовать оптимизацию для собственных функций, применяйте **std::wstring_view**.

Идея состоит в том, что вы можете по большей части игнорировать особенности управления строками среды выполнения Windows и просто эффективно работать с тем, что вам известно. Это важно, учитывая, насколько активно строки используются в среде выполнения Windows.

# <a name="formatting-strings"></a>Форматирование строк
Один из вариантов форматирования строк — **std::wstringstream**. Вот пример, который форматирует и отображает простое сообщения трассировки отладки.

```cppwinrt
#include <sstream>
...
void OnPointerPressed(IInspectable const&, PointerEventArgs const& args)
{
    float2 const point = args.CurrentPoint().Position();
    std::wstringstream wstringstream;
    wstringstream << L"Pointer pressed at (" << point.x << L"," << point.y << L")" << std::endl;
    ::OutputDebugString(wstringstream.str().c_str());
}
```

## <a name="important-apis"></a>Важные API
* [winrt::hstring struct](/uwp/cpp-ref-for-winrt/hstring)
* [функция WinRT::to_hstring](/uwp/cpp-ref-for-winrt/to-hstring)
* [функция WinRT::to_string](/uwp/cpp-ref-for-winrt/to-string)
