---
description: C++/WinRT предоставляет функции и базовые классы, которые позволяют сэкономить много времени и усилий, если требуется реализовать и (или) передавать коллекции.
title: Коллекции с C++/WinRT
ms.date: 04/23/2019
ms.topic: article
keywords: windows 10, uwp, стандартный, c++, cpp, winrt, проекция, коллекция
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 4f1b15ec377b030a467dded634abe3fdde717896
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68270155"
---
# <a name="collections-with-cwinrt"></a>Коллекции с C++/WinRT

На внутреннем уровне коллекция среды выполнения Windows состоит из множества сложных подвижных частей. Но когда вы хотите передать объект коллекции в функцию среды выполнения Windows (WinRT) или реализовать собственные свойства или типы коллекции, то можете воспользоваться для этого функциями и базовыми классами [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt). Эти функции упростят весь процесс и позволят сократить затраты времени и усилий.

[**IVector**](/uwp/api/windows.foundation.collections.ivector_t_) — это интерфейс среды выполнения Windows, реализуемый любой коллекцией элементов произвольного доступа. Если же требуется реализовать сам **IVector**, необходимо также реализовать [**IIterable**](/uwp/api/windows.foundation.collections.iiterable_t_), [**IVectorView**](/uwp/api/windows.foundation.collections.ivectorview_t_) и [**IIterator**](/uwp/api/windows.foundation.collections.iiterator_t_). Даже если вам *нужен* пользовательский тип коллекции, это трудоемкий процесс. Но если у вас есть данные в **std::vector** (или в **std::map**, или в **std::unordered_map**) и вы хотите просто передать их в API среды выполнения Windows, то наверняка постараетесь избежать такой сложной работы, если это возможно. Это *возможно*, так как C++/WinRT поможет вам создавать коллекции эффективно и с минимальными усилиями.

Ознакомьтесь также с разделом [Элементы управления XAML; привязка к коллекции C++/WinRT](binding-collection.md).

## <a name="helper-functions-for-collections"></a>Вспомогательные функции для коллекций

### <a name="general-purpose-collection-empty"></a>Пустая коллекция общего назначения

В этом разделе рассматривается сценарий, где вы хотите создать изначально пустую коллекцию, а *затем* заполнить ее.

Чтобы получить новый объект типа, который реализует коллекцию общего назначения, можно вызвать шаблон функции [**winrt::single_threaded_vector**](/uwp/cpp-ref-for-winrt/single-threaded-vector). Объект возвращается в виде интерфейса [**IVector**](/uwp/api/windows.foundation.collections.ivector_t_), с помощью которого вызываются функции и свойства возвращенного объекта.

Если вы хотите скопировать и вставить этот пример непосредственно в главный файл исходного кода проекта **консольного приложения Windows (C++/WinRT)** , сначала задайте параметр **Не использовать предварительно скомпилированные заголовки** в свойствах проекта.

```cppwinrt
// main.cpp
#include <winrt/Windows.Foundation.Collections.h>
#include <iostream>
using namespace winrt;

int main()
{
    winrt::init_apartment();

    Windows::Foundation::Collections::IVector<int> coll{ winrt::single_threaded_vector<int>() };
    coll.Append(1);
    coll.Append(2);
    coll.Append(3);

    for (auto const& el : coll)
    {
        std::cout << el << std::endl;
    }

    Windows::Foundation::Collections::IVectorView<int> view{ coll.GetView() };
}
```

Как показано в приведенном выше примере кода, после создания коллекции можно добавить элементы, выполнить их итерацию и, как правило, оперировать объектом как и любым объектом коллекции среды выполнения Windows, который вы получили из API. Если требуется неизменяемое представление коллекции, то можно вызвать [**IVector::GetView**](/uwp/api/windows.foundation.collections.ivector-1.getview), как показано в примере. Приведенный выше шаблон для &mdash;создания и использования коллекции&mdash; подходит для простых сценариев, где вы хотите передать данные в API или получить данные из него. Вы можете передать **IVector**, или **IVectorView** куда угодно, где ожидается [**IIterable**](/uwp/api/windows.foundation.collections.iiterable_t_).

В примере кода выше вызов **winrt::init_apartment** инициализирует поток в среде выполнения Windows (по умолчанию — в многопотоковом подразделении). Вызов также инициализирует COM.

### <a name="general-purpose-collection-primed-from-data"></a>Коллекция общего назначения, заполненная данными

В этом разделе рассматривается сценарий, где вы хотите создать коллекцию и сразу заполнить ее.

Можно дополнительных временных затрат, связанных с вызовами **Append** в предыдущем примере кода. У вас уже может быть источник данных или вам может понадобиться добавить исходные данные перед созданием объекта коллекции среды выполнения Windows. Вот как это сделать.

```cppwinrt
auto coll1{ winrt::single_threaded_vector<int>({ 1,2,3 }) };

std::vector<int> values{ 1,2,3 };
auto coll2{ winrt::single_threaded_vector<int>(std::move(values)) };

for (auto const& el : coll2)
{
    std::cout << el << std::endl;
}
```

Можно передать временный объект, содержащий данные, в **winrt::single_threaded_vector**, как и в `coll1` выше. Или вы можете переместить **std::vector** (при условии, что вы не будете к нему больше обращаться) в функцию. В обоих случаях в функцию передается *rvalue*. Это позволяет компилятору действовать эффективно и избежать копирования данных. Если вы хотите узнать больше о значениях *rvalue*, ознакомьтесь с разделом [Категории значений и ссылки](cpp-value-categories.md).

Если вы хотите привязать элемент управления XAML к коллекции, это возможно. Но имейте в виду: чтобы правильно задать свойство [**ItemsControl.ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource), необходимо присвоить ему значение типа **IVector** интерфейса **IInspectable** (или типа взаимодействия, например [**IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector)).

Ниже приведен пример кода, который создает коллекцию типа, подходящего для привязывания, и добавляет в нее элемент. Контекст для этого примера кода можно найти в разделе [Элементы управления XAML; привязка к коллекции C++/WinRT](binding-collection.md).

```cppwinrt
auto bookSkus{ winrt::single_threaded_vector<Windows::Foundation::IInspectable>() };
bookSkus.Append(winrt::make<Bookstore::implementation::BookSku>(L"Moby Dick"));
```

Вы можете создать коллекцию среды выполнения Windows на основе данных и получить представление о ней, готовое для передачи в API, вовсе ничего не копируя.

```cppwinrt
std::vector<float> values{ 0.1f, 0.2f, 0.3f };
Windows::Foundation::Collections::IVectorView<float> view{ winrt::single_threaded_vector(std::move(values)).GetView() };
```

В приведенных выше примерах создаваемую коллекцию *можно* привязать к элементу управления XAML, но эту коллекцию невозможно отслеживать.

### <a name="observable-collection"></a>Отслеживаемая коллекция

Чтобы получить новый объект типа, который реализует *отслеживаемую* коллекцию, можно вызвать шаблон функции [**winrt::single_threaded_observable_vector**](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) с любым типом элемента. Но чтобы сделать эту коллекцию пригодной для привязки к элементу управления XAML, используйте тип элемента **IInspectable**.

Объект возвращается в виде интерфейса [**IObservableVector**](/uwp/api/windows.foundation.collections.iobservablevector_t_), с помощью которого вы (или элемент управления, к которому он привязан) вызываете функции и свойства возвращенного объекта.

```cppwinrt
auto bookSkus{ winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>() };
```

Примеры кода и дополнительные сведения о привязке элементов управления пользовательского интерфейса к отслеживаемой коллекции см. в разделе [Элементы управления XAML; привязка к коллекции C++/WinRT](binding-collection.md).

### <a name="associative-collection-map"></a>Ассоциативная коллекция (сопоставление)

Существуют версии ассоциативной коллекции из двух функций, которые мы рассмотрели.

- Шаблон функции [**winrt::single_threaded_map**](/uwp/cpp-ref-for-winrt/single-threaded-map) возвращает неотслеживаемую ассоциативную коллекцию как [**IMap**](/uwp/api/windows.foundation.collections.imap_k_v_).
- Шаблон функции [**winrt::single_threaded_observable_map**](/uwp/cpp-ref-for-winrt/single-threaded-observable-map) возвращает отслеживаемую ассоциативную коллекцию как [**IObservableMap**](/uwp/api/windows.foundation.collections.iobservablemap_k_v_).

При необходимости эти коллекции можно заполнить данными, передав в функцию значение *rvalue* типа **std::map** или **std::unordered_map**.

```cppwinrt
auto coll1{
    winrt::single_threaded_map<winrt::hstring, int>(std::map<winrt::hstring, int>{
        { L"AliceBlue", 0xfff0f8ff }, { L"AntiqueWhite", 0xfffaebd7 }
    })
};

std::map<winrt::hstring, int> values{
    { L"AliceBlue", 0xfff0f8ff }, { L"AntiqueWhite", 0xfffaebd7 }
};
auto coll2{ winrt::single_threaded_map<winrt::hstring, int>(std::move(values)) };
```

### <a name="single-threaded"></a>Один поток

Слово "single_threaded" (Однопоточный) в именах этих функций указывает, что они не обеспечивают параллельную обработку &mdash; другими словами, они не являются потокобезопасными. Упоминание потоков не связано с подразделениями, так как все объекты, возвращаемые из этих функций, являются гибкими (см. раздел [Гибкие объекты в C++/WinRT](agile-objects.md)). Просто эти объекты являются однопоточными. И вполне подходят, если вам нужно тем или иным способом передать данные через двоичный интерфейс приложения (ABI).

## <a name="base-classes-for-collections"></a>Базовые классы для коллекций

Если, чтобы обеспечить максимальную гибкость, вы хотите реализовать свою собственную пользовательскую коллекцию, необходимо выбрать легкий путь. Например, вот как будет выглядеть представление пользовательского вектора *без использования базовых классов C++/WinRT*.

```cppwinrt
...
using namespace winrt;
using namespace Windows::Foundation::Collections;
...
struct MyVectorView :
    implements<MyVectorView, IVectorView<float>, IIterable<float>>
{
    // IVectorView
    float GetAt(uint32_t const) { ... };
    uint32_t GetMany(uint32_t, winrt::array_view<float>) const { ... };
    bool IndexOf(float, uint32_t&) { ... };
    uint32_t Size() { ... };

    // IIterable
    IIterator<float> First() const { ... };
};
...
IVectorView<float> view{ winrt::make<MyVectorView>() };
```

Вместо этого гораздо проще получить представление пользовательского вектора с помощью шаблона структуры [**winrt::vector_view_base**](/uwp/cpp-ref-for-winrt/vector-view-base) и просто реализовать функцию **get_container**, чтобы предоставить контейнер, содержащий ваши данные.

```cppwinrt
struct MyVectorView2 :
    implements<MyVectorView2, IVectorView<float>, IIterable<float>>,
    winrt::vector_view_base<MyVectorView2, float>
{
    auto& get_container() const noexcept
    {
        return m_values;
    }

private:
    std::vector<float> m_values{ 0.1f, 0.2f, 0.3f };
};
```

Контейнер, возвращаемый функцией **get_container**, должен предоставить интерфейсы **begin** и **end**, которые ожидает **winrt::vector_view_base**. Как показано в примере выше, это обеспечивает **std::vector**. Но можно вернуть любой контейнер, удовлетворяющий этим условиям, в том числе собственный пользовательский контейнер.

```cppwinrt
struct MyVectorView3 :
    implements<MyVectorView3, IVectorView<float>, IIterable<float>>,
    winrt::vector_view_base<MyVectorView3, float>
{
    auto get_container() const noexcept
    {
        struct container
        {
            float const* const first;
            float const* const last;

            auto begin() const noexcept
            {
                return first;
            }

            auto end() const noexcept
            {
                return last;
            }
        };

        return container{ m_values.data(), m_values.data() + m_values.size() };
    }

private:
    std::array<float, 3> m_values{ 0.2f, 0.3f, 0.4f };
};
```

Ниже приведены базовые классы, которые C++/WinRT предоставляет для реализации пользовательских коллекций.

### <a name="winrtvector_view_base"></a>[winrt::vector_view_base](/uwp/cpp-ref-for-winrt/vector-view-base)

Ознакомьтесь с примерами кода выше.

### <a name="winrtvector_base"></a>[winrt::vector_base](/uwp/cpp-ref-for-winrt/vector-base)

```cppwinrt
struct MyVector :
    implements<MyVector, IVector<float>, IVectorView<float>, IIterable<float>>,
    winrt::vector_base<MyVector, float>
{
    auto& get_container() const noexcept
    {
        return m_values;
    }

    auto& get_container() noexcept
    {
        return m_values;
    }

private:
    std::vector<float> m_values{ 0.1f, 0.2f, 0.3f };
};
```

### <a name="winrtobservable_vector_base"></a>[winrt::observable_vector_base](/uwp/cpp-ref-for-winrt/observable-vector-base)

```cppwinrt
struct MyObservableVector :
    implements<MyObservableVector, IObservableVector<float>, IVector<float>, IVectorView<float>, IIterable<float>>,
    winrt::observable_vector_base<MyObservableVector, float>
{
    auto& get_container() const noexcept
    {
        return m_values;
    }

    auto& get_container() noexcept
    {
        return m_values;
    }

private:
    std::vector<float> m_values{ 0.1f, 0.2f, 0.3f };
};
```

### <a name="winrtmap_view_base"></a>[winrt::map_view_base](/uwp/cpp-ref-for-winrt/map-view-base)

```cppwinrt
struct MyMapView :
    implements<MyMapView, IMapView<winrt::hstring, int>, IIterable<IKeyValuePair<winrt::hstring, int>>>,
    winrt::map_view_base<MyMapView, winrt::hstring, int>
{
    auto& get_container() const noexcept
    {
        return m_values;
    }

private:
    std::map<winrt::hstring, int> m_values{
        { L"AliceBlue", 0xfff0f8ff }, { L"AntiqueWhite", 0xfffaebd7 }
    };
};
```

### <a name="winrtmap_base"></a>[winrt::map_base](/uwp/cpp-ref-for-winrt/map-base)

```cppwinrt
struct MyMap :
    implements<MyMap, IMap<winrt::hstring, int>, IMapView<winrt::hstring, int>, IIterable<IKeyValuePair<winrt::hstring, int>>>,
    winrt::map_base<MyMap, winrt::hstring, int>
{
    auto& get_container() const noexcept
    {
        return m_values;
    }

    auto& get_container() noexcept
    {
        return m_values;
    }

private:
    std::map<winrt::hstring, int> m_values{
        { L"AliceBlue", 0xfff0f8ff }, { L"AntiqueWhite", 0xfffaebd7 }
    };
};
```

### <a name="winrtobservable_map_base"></a>[winrt::observable_map_base](/uwp/cpp-ref-for-winrt/observable-map-base)

```cppwinrt
struct MyObservableMap :
    implements<MyObservableMap, IObservableMap<winrt::hstring, int>, IMap<winrt::hstring, int>, IMapView<winrt::hstring, int>, IIterable<IKeyValuePair<winrt::hstring, int>>>,
    winrt::observable_map_base<MyObservableMap, winrt::hstring, int>
{
    auto& get_container() const noexcept
    {
        return m_values;
    }

    auto& get_container() noexcept
    {
        return m_values;
    }

private:
    std::map<winrt::hstring, int> m_values{
        { L"AliceBlue", 0xfff0f8ff }, { L"AntiqueWhite", 0xfffaebd7 }
    };
};
```

## <a name="important-apis"></a>Важные API
* [Свойство ItemsControl.ItemsSource](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource)
* [Интерфейс IObservableVector](/uwp/api/windows.foundation.collections.iobservablevector_t_)
* [Интерфейс IVector](/uwp/api/windows.foundation.collections.ivector_t_)
* [Шаблон структуры winrt::map_base](/uwp/cpp-ref-for-winrt/map-base)
* [Шаблон структуры winrt::map_view_base](/uwp/cpp-ref-for-winrt/map-view-base)
* [Шаблон структуры winrt::observable_map_base](/uwp/cpp-ref-for-winrt/observable-map-base)
* [Шаблон структуры winrt::observable_vector_base](/uwp/cpp-ref-for-winrt/observable-vector-base)
* [Шаблон функции winrt::single_threaded_observable_map](/uwp/cpp-ref-for-winrt/single-threaded-observable-map)
* [Шаблон функции winrt::single_threaded_map](/uwp/cpp-ref-for-winrt/single-threaded-map)
* [Шаблон функции winrt::single_threaded_observable_vector](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector)
* [Шаблон функции winrt::single_threaded_vector](/uwp/cpp-ref-for-winrt/single-threaded-vector)
* [Шаблон структуры winrt::vector_base](/uwp/cpp-ref-for-winrt/vector-base)
* [Шаблон структуры winrt::vector_view_base](/uwp/cpp-ref-for-winrt/vector-view-base)

## <a name="related-topics"></a>Связанные темы
* [Категории значений и ссылки на них](cpp-value-categories.md)
* [Элементы управления XAML; привязка к коллекции C++/WinRT](binding-collection.md)
