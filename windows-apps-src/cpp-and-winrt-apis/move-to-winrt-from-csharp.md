---
description: В этом разделе объясняется, как перенести код C# в его эквивалент на C++/WinRT.
title: Переход на C++/WinRT с C#
ms.date: 07/15/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, port, migrate, C#
ms.localizationpriority: medium
ms.openlocfilehash: a63d38db613ebe6425a05ed20563405242ffd441
ms.sourcegitcommit: ba4a046793be85fe9b80901c9ce30df30fc541f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68328857"
---
# <a name="move-to-cwinrt-from-c"></a>Переход на C++/WinRT с C#

В этом разделе показано, как перенести код из проекта [C#](/visualstudio/get-started/csharp) в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).

## <a name="register-an-event-handler"></a>Регистрация обработчика событий

Обработчик событий можно зарегистрировать в разметке XAML.

```xaml
<Button x:Name="OpenButton" Click="OpenButton_Click" />
```

В C# метод **OpenButton_Click** может быть закрытым, но XAML все равно сможет подключать его к событию [**ButtonBase.Click**](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click), вызываемому *OpenButton*.

В C++/WinRT метод **OpenButton_Click** должен быть открытым в вашем [типе имплементации](/windows/uwp/cpp-and-winrt-apis/author-apis).

```cppwinrt
namespace winrt::MyProject::implementation
{
    struct MyPage : MyPageT<MyPage>
    {
        void OpenButton_Click(
            winrt::Windows:Foundation::IInspectable const& sender,
            winrt::Windows::UI::Xaml::RoutedEventArgs const& args);
    }
};
```

Кроме того, можно сделать регистрирующий класс открытым для класса реализации, а **OpenButton_Click** — закрытым.

```cppwinrt
namespace winrt::MyProject::implementation
{
    struct MyPage : MyPageT<MyPage>
    {
    private:
        friend MyPageT;
        void OpenButton_Click(
            winrt::Windows:Foundation::IInspectable const& sender,
            winrt::Windows::UI::Xaml::RoutedEventArgs const& args);
    }
};
```

## <a name="making-a-class-available-to-the-binding-markup-extension"></a>Предоставление расширению разметки {Binding} доступа к классу

См. подробнее об [использовании расширения разметки {Binding} для привязки данных к типу данных](/windows/uwp/data-binding/data-binding-in-depth#binding-object-declared-using-binding).

## <a name="making-a-data-source-available-to-xaml-markup"></a>Предоставление разметке XAML доступа к источнику данных

В C++/WinRT версии 2.0.190530.8 и последующих [**winrt::single_threaded_observable_vector**](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) создает наблюдаемый вектор, который поддерживает как **[IObservableVector](/uwp/api/windows.foundation.collections.iobservablevector_t_)\<T\>** , так и **IObservableVector\<IInspectable\>** .

Вы можете создать **файл Midl (.idl)** аналогичным образом (см. также раздел о [разделении классов среды выполнения на файлы Midl (.idl)](/windows/uwp/cpp-and-winrt-apis/author-apis#factoring-runtime-classes-into-midl-files-idl)).

```idl
namespace Bookstore
{
    runtimeclass BookSku { ... }

    runtimeclass BookstoreViewModel
    {
        Windows.Foundation.Collections.IObservableVector<BookSku> BookSkus{ get; };
    }

    runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
    {
        MainPage();
        BookstoreViewModel MainViewModel{ get; };
    }
}
```

Выполните реализацию следующим образом.

```cppwinrt
// BookstoreViewModel.h
...
struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
{
    BookstoreViewModel()
    {
        m_bookSkus = winrt::single_threaded_observable_vector<Bookstore::BookSku>();
        m_bookSkus.Append(winrt::make<Bookstore::implementation::BookSku>(L"To Kill A Mockingbird"));
    }
    
    Windows::Foundation::Collections::IObservableVector<Bookstore::BookSku> BookSkus();
    {
        return m_bookSkus;
    }

private:
    Windows::Foundation::Collections::IObservableVector<Bookstore::BookSku> m_bookSkus;
};
...
```

Дополнительные сведения см. в разделе [Элементы управления XAML; привязка к коллекции C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-collection).

## <a name="making-a-data-source-available-to-xaml-markup-prior-to-cwinrt-201905308"></a>Предоставление разметке XAML доступа к источнику данных (в версиях, предшествующих C++/WinRT 2.0.190530.8)

Для привязки данных XAML требуется, чтобы источник элементов реализовывал **[IIterable](/uwp/api/windows.foundation.collections.iiterable_t_)\<IInspectable\>** , а также одну из следующих комбинаций интерфейсов.

- **IObservableVector\<IInspectable\>**
- **IBindableVector** и **INotifyCollectionChanged**
- **IBindableVector** и **IBindableObservableVector**
- **IBindableVector** самостоятельно (не будет реагировать на изменения)
- **IVector\<IInspectable\>**
- **IBindableIterable** (будет выполнять итерацию и сохранять элементы в закрытую коллекцию)

Универсальный интерфейс, такой как **IVector\<T\>** , не может быть обнаружен во время выполнения. У каждого интерфейса **IVector\<T\>** есть свой идентификатор интерфейса (IID), который является функцией **T**. Разработчики могут расширять набор **T** произвольным образом, поэтому очевидно, что коду привязки XAML будет неизвестен весь набор для выполнения запросов к нему. Это ограничение не является проблемой в C#, так как каждый объект CLR, реализующий **IEnumerable\<T\>** , автоматически реализует **IEnumerable**. На уровне ABI это означает, что каждый объект, реализующий **IObservableVector\<T\>** , автоматически реализует **IObservableVector\<IInspectable\>** .

В C++/WinRT такая возможность не предусмотрена. Если класс среды выполнения C++/WinRT реализует **IObservableVector\<T\>** , нельзя ожидать предоставления **IObservableVector\<IInspectable\>** .

Следовательно, предыдущий пример должен выглядеть так.

```idl
...
runtimeclass BookstoreViewModel
{
    // This is really an observable vector of BookSku.
    Windows.Foundation.Collections.IObservableVector<Object> BookSkus{ get; };
}
```

Реализация выполняется следующим образом.

```cppwinrt
// BookstoreViewModel.h
...
struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
{
    BookstoreViewModel()
    {
        m_bookSkus = winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>();
        m_bookSkus.Append(winrt::make<Bookstore::implementation::BookSku>(L"To Kill A Mockingbird"));
    }
    
    // This is really an observable vector of BookSku.
    Windows::Foundation::Collections::IObservableVector<Windows::Foundation::IInspectable> BookSkus();
    {
        return m_bookSkus;
    }

private:
    Windows::Foundation::Collections::IObservableVector<Windows::Foundation::IInspectable> m_bookSkus;
};
...
```

Чтобы получить доступ к объектам в *m_bookSkus*, необходимо применить к ним метод QI в **Bookstore::BookSku**.

```cppwinrt
Widget MyPage::BookstoreViewModel(winrt::hstring title)
{
    for (auto&& obj : m_bookSkus)
    {
        auto bookSku = obj.as<Bookstore::BookSku>();
        if (bookSku.Title() == title) return bookSku;
    }
    return nullptr;
}
```

## <a name="tostring"></a>ToString()

Типы C# предоставляют метод [Object.ToString](/dotnet/api/system.object.tostring).

```csharp
int i = 2;
var s = i.ToString(); // s is a System.String with value "2".
```

C++/ WinRT не предоставляет эту функцию напрямую, но можно обратиться к ее альтернативам.

```cppwinrt
int i{ 2 };
auto s{ std::to_wstring(i) }; // s is a std::wstring with value L"2".
```

В C++/WinRT также поддерживается [**winrt::to_hstring**](/uwp/cpp-ref-for-winrt/to-hstring) для ограниченного числа типов. Вам нужно добавить перегрузки для любых дополнительных типов, к которым необходимо применить метод stringify.

| Язык | Stringify int | Stringify enum |
| - | - | - |
| C# | `string result = "hello, " + intValue.ToString();`<br>`string result = $"hello, {intValue}";` | `string result = "status: " + status.ToString();`<br>`string result = $"status: {status}";` |
| C++/WinRT | `hstring result = L"hello, " + to_hstring(intValue);` | `// must define overload (see below)`<br>`hstring result = L"status: " + to_hstring(status);` |

Если применяется stringify enum, необходимо предоставить реализацию **winrt::to_hstring**.

```cppwinrt
namespace winrt
{
    hstring to_hstring(StatusEnum status)
    {
        switch (status)
        {
        case StatusEnum::Success: return L"Success";
        case StatusEnum::AccessDenied: return L"AccessDenied";
        case StatusEnum::DisabledByPolicy: return L"DisabledByPolicy";
        default: return to_hstring(static_cast<int>(status));
        }
    }
}
```

Такие операции часто неявно используются привязкой данных.

```xaml
<TextBlock>
You have <Run Text="{Binding FlowerCount}"/> flowers.
</TextBlock>
<TextBlock>
Most recent status is <Run Text="{x:Bind LatestOperation.Status}"/>.
</TextBlock>
```

Эти привязки будут выполнять **winrt::to_hstring** свойства привязки. Во втором примере (**StatusEnum**) необходимо предоставить свою перегрузку **winrt::to_hstring**, иначе возникнет ошибка компилятора.

## <a name="string-building"></a>Сборка строк

Для сборки строк в C# есть встроенный тип [**StringBuilder**](/dotnet/api/system.text.stringbuilder).

| | C# | C++/WinRT |
|-|-|-|
| Класс построителя | `StringBuilder builder;` | `std::wstringstream stream;` |
| Добавление строки с сохранением значений NULL | `builder.Append(s);` | `stream << std::wstring_view{ s };` |
| Извлечение результата | `s = builder.ToString();` | `ws = stream.str();` |

## <a name="boxing-and-unboxing"></a>Упаковка-преобразование и распаковка-преобразование

В C# автоматически выполняется упаковка-преобразование скалярных значений в объекты. В C++/WinRT необходимо явным образом вызвать функцию [**winrt::box_value**](/uwp/cpp-ref-for-winrt/box-value). В обоих языках распаковка-преобразование выполняется явным образом. См. подробнее об [упаковке-преобразовании и распаковке-преобразовании в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/boxing).

Эти определения используются в следующих таблицах.

| C# | C++/WinRT|
|-|-|
| `int i;` | `int i;` |
| `string s;` | `winrt::hstring s;` |
| `object o;` | `IInspectable o;`|

| Операция | C# | C++/WinRT|
|-|-|-|
| Упаковка-преобразование | `o = 1;`<br>`o = "string";` | `o = box_value(1);`<br>`o = box_value(L"string");` |
| Распаковка-преобразование | `i = (int)o;`<br>`s = (string)o;` | `i = unbox_value<int>(o);`<br>`s = unbox_value<winrt::hstring>(o);` |

В C++/CX и C# вызываются исключения при попытке распаковки-преобразования пустого указателя в тип значения. В C++/WinRT это считается ошибкой программирования, которая приводит к аварийному завершению. Если в C++/WinRT объект имеет не тот тип, который предполагался, используйте функцию [**winrt::unbox_value_or**](/uwp/cpp-ref-for-winrt/unbox-value-or).

| Сценарий | C# | C++/WinRT|
|-|-|-|
| Распаковка-преобразование известного целого числа |`i = (int)o;` | `i = unbox_value<int>(o);` |
| Если o имеет значение NULL | `System.NullReferenceException` | Аварийное завершение |
| Если o не является упакованным целым числом | `System.InvalidCastException` | Аварийное завершение |
| Выполните распаковку-преобразование целого числа, используйте откат при значении NULL; аварийное завершение при других вариантах | `i = o != null ? (int)o : fallback;` | `i = o ? unbox_value<int>(o) : fallback;` |
| Выполните распаковку-преобразование целого числа, если это возможно; используйте откат при других вариантах | `var box = o as int?;`<br>`i = box != null ? box.Value : fallback;` | `i = unbox_value_or<int>(o, fallback);` |

### <a name="boxing-and-unboxing-a-string"></a>Упаковка-преобразование и распаковка-преобразование строки

Строка в некотором роде является и типом значения, и ссылочным типом. В C# и C++/WinRT строки обрабатываются по-разному.

Тип ABI [**HSTRING**](/windows/win32/winrt/hstring) является указателем на строку с учетом ссылок. Но он не является производным от [**IInspectable**](/windows/win32/api/inspectable/nn-inspectable-iinspectable), поэтому с технической точки зрения это не *объект*. Более того, **HSTRING** со значением NULL представляет пустую строку. Упаковка объектов, которые не унаследованы от **IInspectable**, выполняется путем их упаковки в [**IReference\<T\>** ](/uwp/api/windows.foundation.ireference_t_). Среда выполнения Windows при этом предоставляет стандартную реализацию в виде объекта [**PropertyValue**](/uwp/api/windows.foundation.propertyvalue) (настраиваемые типы отображаются как [**PropertyType::OtherType**](/uwp/api/windows.foundation.propertytype)).

C# представляет строку среды выполнения Windows в виде ссылочного типа, хотя C++/WinRT проецирует строку в виде типа значения. Это означает, что упакованная строка со значением NULL может иметь разные представления в зависимости от способа ее получения.

| Операция | C# | C++/WinRT|
|-|-|-|
| Категория типа строки | Ссылочный тип | Тип значения |
| **HSTRING** со значением NULL проецируется как | `""` | `hstring{ nullptr }` |
| Значение NULL и `""` идентичны? | Нет | Да |
| Допустимость значения NULL | `s = null;`<br>`s.Length` вызывает **NullReferenceException** | `s = nullptr;`<br>`s.size() == 0` (допустимо) |
| Упаковка-преобразование строки | `o = s;` | `o = box_value(s);` |
| Если `s` имеет значение `null` | `o = (string)null;`<br>`o == null` | `o = box_value(hstring{nullptr});`<br>`o != nullptr` |
| Если `s` имеет значение `""` | `o = "";`<br>`o != null;` | `o = box_value(hstring{L""});`<br>`o != nullptr;` |
| Упаковка-преобразование строки с сохранением значения NULL | `o = s;` | `o = s.empty() ? nullptr : box_value(s);` |
| Принудительная упаковка-преобразование строки | `o = PropertyValue.CreateString(s);` | `o = box_value(s);` |
| Распаковка-преобразование известной строки | `s = (string)o;` | `s = unbox_value<hstring>(o);` |
| Если `o` имеет значение NULL | `s == null; // not equivalent to ""` | Аварийное завершение |
| Если `o` не является упакованной строкой | `System.InvalidCastException` | Аварийное завершение |
| Выполните распаковку-преобразование строки, используйте откат при значении NULL; аварийное завершение при других вариантах | `s = o != null ? (string)o : fallback;` | `s = o ? unbox_value<hstring>(o) : fallback;` |
| Выполните распаковку-преобразование строки, если это возможно; используйте откат при других вариантах | `var s = o as string ?? fallback;` | `s = unbox_value_or<hstring>(o, fallback);` |

В двух случаях *распаковки с откатом* выше возможна ситуация, когда к строке со значением NULL применяется принудительная упаковка, после чего откат использоваться не будет. Результирующее значение будет пустой строкой, так как в пакете находилась именно она.

## <a name="derived-classes"></a>Производные классы

Чтобы создавать производные классы из класса среды выполнения, базовый класс должен быть *составным*. В отличие от C#, в C++/WinRT нужно выполнять отдельные действия по преобразованию классов в составные. Используйте [ключевое слово unsealed](/uwp/midl-3/intro#base-classes), чтобы указать, что вы хотите использовать класс в виде базового класса.

```idl
unsealed runtimeclass BasePage : Windows.UI.Xaml.Controls.Page
{
    ...
}
runtimeclass DerivedPage : BasePage
{
    ...
}
```

В классе заголовка реализации вам нужно включить файл заголовка базового класса до включения автоматически созданного заголовка для производного класса. В противном случае возникнет проблема, например ошибка с сообщением о недопустимом использовании такого типа в виде выражения.

```cppwinrt
// DerivedPage.h
#include "BasePage.h"       // This comes first.
#include "DerivedPage.g.h"  // Otherwise this header file will produce an error.

namespace winrt::MyNamespace::implementation
{
    struct DerivedPage : DerivedPageT<DerivedPage>
    {
        ...
    }
}
```

## <a name="consuming-objects-from-xaml-markup"></a>Использование объектов из разметки XAML

В проекте C# вы можете использовать закрытые члены и именованные элементы из разметки XAML. Но в C++/WinRT все сущности, используемые при применении [**расширения разметки {x:Bind}** ](/windows/uwp/xaml-platform/x-bind-markup-extension), XAML должны быть общедоступными в IDL.

Кроме того, привязка к логическому значению приводит к отображению `true` или `false` в C#, тогда как в C++/WinRT отображается **Windows.Foundation.IReference`1\<логическое_значение\>** .

Дополнительные сведения и примеры кода см. в разделе [Использование объектов из разметки](/windows/uwp/cpp-and-winrt-apis/binding-property#consuming-objects-from-xaml-markup).

## <a name="important-apis"></a>Важные API
* [Шаблон функции winrt::single_threaded_observable_vector](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector)
* [Пространство имен WinRT](/uwp/cpp-ref-for-winrt/winrt)

## <a name="related-topics"></a>Статьи по теме
* [Руководства по C#](/visualstudio/get-started/csharp)
* [Создание интерфейсов API с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis)
* [Подробно о привязке данных](/windows/uwp/data-binding/data-binding-in-depth)
* [Элементы управления XAML; привязка к коллекции C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-collection)