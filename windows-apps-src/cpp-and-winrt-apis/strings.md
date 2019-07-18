---
description: С помощью C++/WinRT можно вызывать интерфейсы API среды выполнения Windows, используя стандартные типы широких строк C++ или тип winrt::hstring.
title: Обработка строк в C++/WinRT
ms.date: 04/23/2019
ms.topic: article
keywords: Windows 10, UWP, стандартный, c++, cpp, winrt, проекция, строка
ms.localizationpriority: medium
ms.openlocfilehash: 004aa3e267bab86527ac3d5c3fe0383ccd4ad904
ms.sourcegitcommit: 8b4c1fdfef21925d372287901ab33441068e1a80
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67844313"
---
# <a name="string-handling-in-cwinrt"></a>Обработка строк в C++/WinRT

С помощью [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) можно вызывать интерфейсы API среды выполнения Windows, используя типы широких строк стандартной библиотеки C++, такие как **std::wstring** (но не типы узких строк, такие как **std::string**). В C++/WinRT имеется настраиваемый строковый тип [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) (определенный в базовой библиотеке C++/WinRT — `%WindowsSdkDir%Include\<WindowsTargetPlatformVersion>\cppwinrt\winrt\base.h`). Этот строковый тип фактически принимают и возвращают конструкторы, функции и свойства среды выполнения Windows. Однако во многих случаях &mdash; благодаря конструкторам преобразования **hstring** и операторам преобразования &mdash; можно выбирать, следует ли учитывать **hstring** в клиентском коде. Если вы *разрабатываете* интерфейсы API, вероятность того, что вам нужно знать о **hstring**, возрастает.

В C++ существует множество строковых типов. Различные варианты существуют во множестве библиотек в дополнение к **std::basic_string** из стандартной библиотеки C++. В С++17 имеются служебные программы для преобразования строк и **std::basic_string_view**, чтобы заполнить пробелы между всеми строковыми типами.  Тип [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) дает возможность преобразования с помощью **std::wstring_view** для обеспечения взаимодействия, для которого был создан **std::basic_string_view**.

## <a name="using-stdwstring-and-optionally-winrthstring-with-uri"></a>Использование **std::wstring** (и, при необходимости, **winrt::hstring**) с **Uri**
[**Windows::Foundation::Uri**](/uwp/api/windows.foundation.uri) формируется из [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring).

```cppwinrt
public:
    Uri(winrt::hstring uri) const;
```

Но **hstring** имеет [конструкторы преобразования](/uwp/cpp-ref-for-winrt/hstring#hstringhstring-constructor), позволяющие работать с ним, не учитывая этот факт. Ниже приведен пример кода, показывающий, как создать **Uri** из литерала широкой строки, представления широкой строки и **std::wstring**.

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

Но опять же, знать об этом не обязательно благодаря [оператору преобразования **hstring** в **std::wstring_view**](/uwp/cpp-ref-for-winrt/hstring#hstringoperator-stdwstring_view).

```cppwinrt
// Access a property of type hstring, via a conversion operator to a standard type.
std::wstring domainWstring{ contosoUri.Domain() }; // L"contoso.com"
domainWstring = awUri.Domain(); // L"adventure-works.com"

// Or, you can choose to keep the hstring unconverted.
hstring domainHstring{ contosoUri.Domain() }; // L"contoso.com"
domainHstring = awUri.Domain(); // L"adventure-works.com"
```

Аналогичным образом [**IStringable::ToString**](https://docs.microsoft.com/windows/desktop/api/windows.foundation/nf-windows-foundation-istringable-tostring) возвращает hstring.

```cppwinrt
public:
    hstring ToString() const;
```

**Uri** реализует интерфейс [**IStringable**](https://docs.microsoft.com/windows/desktop/api/windows.foundation/nn-windows-foundation-istringable).

```cppwinrt
// Access hstring's IStringable::ToString, via a conversion operator to a standard type.
std::wstring tostringWstring{ contosoUri.ToString() }; // L"http://www.contoso.com/"
tostringWstring = awUri.ToString(); // L"http://www.adventure-works.com/"

// Or you can choose to keep the hstring unconverted.
hstring tostringHstring{ contosoUri.ToString() }; // L"http://www.contoso.com/"
tostringHstring = awUri.ToString(); // L"http://www.adventure-works.com/"
```

Можно использовать функцию [**hstring::c_str**](/uwp/cpp-ref-for-winrt/hstring#hstringc_str-function) для получения стандартной широкой строки из **hstring** (так же, как и из **std::wstring**).

```cppwinrt
#include <iostream>
std::wcout << tostringHstring.c_str() << std::endl;
```
Если у вас есть **hstring**, значит, вы можете получить **Uri**.

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

Для **hstring** имеется член-оператор преобразования **std::wstring_view**, и преобразование осуществляется без дополнительных затрат.

```cppwinrt
void legacy_print(std::wstring_view view);

void Print(winrt::hstring const& hstring)
{
    legacy_print(hstring);
}
```

## <a name="winrthstring-functions-and-operators"></a>Функции и операторы **winrt::hstring**
Для [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring) реализован ряд конструкторов, операторов, функций и итераторов.

Тип **hstring** является диапазоном, поэтому вы можете использовать его с основанным на диапазоне `for` или с `std::for_each`. Он также предоставляет операторы сравнения для естественного и эффективного сравнения с его аналогами в стандартной библиотеке C++. Кроме того, он включает в себя все, что необходимо для использования **hstring** в качестве основы для ассоциативных контейнеров.

Мы понимаем, что многие библиотеки C++ используют **std::string** и работают только с текстом UTF-8. Для удобства мы предоставляем вспомогательные методы, такие как [**winrt::to_string**](/uwp/cpp-ref-for-winrt/to-string) и [**winrt::to_hstring**](/uwp/cpp-ref-for-winrt/to-hstring), для двустороннего преобразования.

`WINRT_ASSERT` — это макроопределение, которое передается в [_ASSERTE](/cpp/c-runtime-library/reference/assert-asserte-assert-expr-macros).

```cppwinrt
winrt::hstring w{ L"Hello, World!" };

std::string c = winrt::to_string(w);
WINRT_ASSERT(c == "Hello, World!");

w = winrt::to_hstring(c);
WINRT_ASSERT(w == L"Hello, World!");
```

Дополнительные примеры и сведения о функциях и операторах **hstring** приведены в справочных материалах по API [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring).

## <a name="the-rationale-for-winrthstring-and-winrtparamhstring"></a>Обоснование для **winrt::hstring** и **winrt::param::hstring**
Среда выполнения Windows реализуется в знаках **wchar_t**, но двоичный интерфейс приложения (ABI) среды выполнения Windows не является подмножеством того, что предоставляют **std::wstring** или **std::wstring_view**. Их использование приведет к значительной неэффективности. Вместо этого C++/WinRT предоставляет **winrt::hstring**, представляющий собой неизменяемую строку, согласованную с базовым [HSTRING](https://docs.microsoft.com/windows/desktop/WinRT/hstring) и реализованную за интерфейсом, аналогичным **std::wstring**. 

Можно заметить, что входные параметры C++/ WinRT, которые логически должны принимать **winrt::hstring**, фактически ожидают **winrt::param::hstring**. Пространство имен **param** содержит набор типов, используемых исключительно для оптимизации входных параметров для естественной привязки к типам стандартной библиотеки C++, а также позволяющих избежать копий и других аспектов, снижающих эффективность. Эти типы не следует использовать напрямую. Если вы хотите использовать оптимизацию для собственных функций, применяйте **std::wstring_view**. Также см. статью о [передаче параметров в интерфейс ABI](/windows/uwp/cpp-and-winrt-apis/pass-parms-to-abi).

Идея состоит в том, что вы можете по большей части игнорировать особенности управления строками среды выполнения Windows и просто эффективно работать с тем, что вам известно. Это важно, учитывая, насколько активно строки используются в среде выполнения Windows.

## <a name="formatting-strings"></a>Форматирование строк
Одно из средств форматирования строк — **std::wstringstream**. Вот пример, который форматирует и отображает простое сообщение отладочной трассировки.

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

## <a name="the-correct-way-to-set-a-property"></a>Правильный способ задать свойство

Для установки свойств значение передается в функцию задания. Рассмотрим пример.

```cppwinrt
// The right way to set the Text property.
myTextBlock.Text(L"Hello!");
```

Ниже приведен неправильный код. Компиляция происходит, но все, что выполняется — это изменение значения **winrt::hstring**, возвращаемое функцией получения доступа**Text()** , а затем результаты отклоняются.

```cppwinrt
// *Not* the right way to set the Text property.
myTextBlock.Text() = L"Hello!";
```

## <a name="important-apis"></a>Важные API
* [Структура WinRT::hstring](/uwp/cpp-ref-for-winrt/hstring)
* [Функция winrt::to_hstring](/uwp/cpp-ref-for-winrt/to-hstring)
* [Функция winrt::to_string](/uwp/cpp-ref-for-winrt/to-string)
