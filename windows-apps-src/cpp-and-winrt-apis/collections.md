---
description: C + +/ WinRT предоставляет функции и базовые классы, которые сэкономить время и усилия, если вы хотите реализовать и/или передать коллекции.
title: Создание коллекций с помощью C++/WinRT
ms.date: 10/03/2018
ms.topic: article
keywords: Windows 10, uwp, стандартные, c ++, cpp, winrt, проекция, коллекции
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: a50ab5f70faa0c8f8b73eada38444bcafd444d8b
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "8191115"
---
# <a name="collections-with-cwinrt"></a>Создание коллекций с помощью C++/WinRT

Внутри системы коллекцию среды выполнения Windows имеет множество сложной движущихся частей. Но когда нужно передать объект коллекции функции среды выполнения Windows, или реализовать собственные свойства коллекции и типы коллекций, функции и базовые классы в [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) для поддержки. Эти функции выполнить сложности из рук и сэкономить издержки в времени и усилий.

[**IVector**](/uwp/api/windows.foundation.collections.ivector_t_) — это интерфейс среды выполнения Windows, реализованный коллекцию элементов любого произвольного доступа. Если вы решите реализовывать **IVector** , необходимо также реализовать [**IIterable**](/uwp/api/windows.foundation.collections.iiterable_t_), [**IVectorView**](/uwp/api/windows.foundation.collections.ivectorview_t_)и [**IIterator**](/uwp/api/windows.foundation.collections.iiterator_t_). Даже если вам *нужны* пользовательской коллекции типов, это много работы. Но если у вас есть данных **std::vector** ( **std::map**или **std::unordered_map**) и все, что вам потребуется сделать это передайте этот API-интерфейс среды выполнения Windows, затем следует избегать такой уровень работы, если это возможно. И избегать его использования *— это* возможно, так как C + +/ WinRT поможет вам создать коллекций эффективно и с минимальными усилиями.

См. также [элементы управления XAML; привязка к C + +/ WinRT коллекции](binding-collection.md).

> [!NOTE]
> Если вы еще не установили пакет Windows SDK версии 10.0.17763.0 (Windows 10, версия 1809) или более поздней версии, затем не имеют доступа к функциям и базовые классы, которые описаны в этом разделе. Вместо этого см. [Если у вас есть более ранней версии Windows SDK](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector#if-you-have-an-older-version-of-the-windows-sdk) перечень отслеживаемый шаблон вектора, которую можно использовать вместо него.

## <a name="helper-functions-for-collections"></a>Вспомогательные функции для коллекций

### <a name="general-purpose-collection-empty"></a>Коллекции общего назначения, пустой

В этом разделе рассматриваются сценарии, которую вы хотите создать коллекцию, изначально пуста; и заполните его *после* создания.

Чтобы получить новый объект тип, реализующий общего назначения коллекции, можно вызвать шаблон функции [**winrt::single_threaded_vector**](/uwp/cpp-ref-for-winrt/single-threaded-vector) . Объект возвращается в виде [**IVector**](/uwp/api/windows.foundation.collections.ivector_t_), и это интерфейса, через который вы вызываете функций и свойств возвращенный объект.

```cppwinrt
...
#include <winrt/Windows.Foundation.Collections.h>
#include <iostream>
using namespace winrt;
...
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

Как видно, в примере выше, после создания коллекции можно добавить элементы, перебирать их и обычно обрабатывают объекта, как и любой объект коллекции среды выполнения Windows, который вы получили из API-интерфейса. Если вам требуется постоянный представления по коллекции, можно вызвать [**IVector::GetView**](/uwp/api/windows.foundation.collections.ivector-1.getview), как показано. Приведенный выше шаблон&mdash;создания и использования коллекции&mdash;подходит для простых сценариях, где требуется передавать данные в или получить данные из API-интерфейса. Вы можете передать **IVector**или **IVectorView**, в любом ожидается, что [**IIterable**](/uwp/api/windows.foundation.collections.iiterable_t_) .

В примере выше вызов **winrt::init_apartment** инициализирует COM; по умолчанию в многопотокового подразделения.

### <a name="general-purpose-collection-primed-from-data"></a>Общего назначения коллекции, заполняется из данных

В этом разделе рассматриваются сценарии, которую вы хотите создать коллекцию и заполните его в то же время.

Вы можете избежать затрат вызовов **Добавить** в предыдущем примере кода. Возможно, уже владеете исходным данным, или можно заполнить в источнике данных до вступления в силу создания объекта коллекции среды выполнения Windows. Вот как это сделать.

```cppwinrt
auto coll1{ winrt::single_threaded_vector<int>({ 1,2,3 }) };

std::vector<int> values{ 1,2,3 };
auto coll2{ winrt::single_threaded_vector<int>(std::move(values)) };

for (auto const& el : coll2)
{
    std::cout << el << std::endl;
}
```

Вы можете передать временный объект, содержащий данные не будут **winrt::single_threaded_vector**, как и в `coll1`, см. выше. Или можно переместить **std::vector** (Если вы не будут доступ к нему снова) в функцию. В обоих случаях *rvalue* передается в функцию. Позволяет компилятору эффективно и избежать копирования данных. Если вы хотите узнать больше о *rvalues*, см. в разделе [категории значений и ссылки на них](cpp-value-categories.md).

Если вы хотите выполнить привязку элементы управления XAML в коллекцию, его можно. Однако имейте в виду, что правильно задать свойство [**ItemsControl.ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) , необходимо задать значение типа **IVector** **IInspectable** (или типа взаимодействия, такие как [**IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector)). Ниже приведен пример кода, который создает набор типов, которым можно выполнить привязку, а затем добавляет элемент к нему.

```cppwinrt
auto bookSkus{ winrt::single_threaded_vector<Windows::Foundation::IInspectable>() };
bookSkus.Append(make<Bookstore::implementation::BookSku>(L"Moby Dick"));
```

Можно создать коллекцию среды выполнения Windows из данных и получить представление на ней Готово к передаче API, все без копирования никаких действий.

```cppwinrt
std::vector<float> values{ 0.1f, 0.2f, 0.3f };
IVectorView<float> view{ winrt::single_threaded_vector(std::move(values)).GetView() };
```

В приведенных выше примерах коллекции, мы создаем *могут* быть привязаны к элементы управления XAML; Однако коллекции не доступным для отслеживания.

### <a name="observable-collection"></a>Отслеживаемую коллекцию

Для получения объекта типа, который реализует коллекцию *отслеживаемым* вызовите шаблон функции [**winrt::single_threaded_observable_vector**](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) с любого типа элемента. Но чтобы отслеживаемую коллекцию подходит для привязки к элементам управления XAML, используйте **IInspectable** в качестве типа элемента.

Объект возвращается в виде [**IObservableVector**](/uwp/api/windows.foundation.collections.iobservablevector_t_), и это интерфейса, через который вас (или элемента управления, к которому он привязан) вызывают функции и свойства возвращенный объект.

```cppwinrt
auto bookSkus{ winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>() };
```

Дополнительные сведения и примеры кода, о привязке пользователей пользовательского интерфейса элементов управления к отслеживаемой коллекции, см. в разделе [элементы управления XAML; привязка к C + +/ WinRT коллекции](binding-collection.md).

### <a name="associative-collection-map"></a>Ассоциативность коллекции (сопоставления)

Существует две функции, которые мы рассмотрели версии ассоциативность коллекции.

- Шаблон функции [**winrt::single_threaded_map**](/uwp/cpp-ref-for-winrt/single-threaded-map) возвращает коллекцию ассоциативность неотслеживаемых как [**IMap**](/uwp/api/windows.foundation.collections.imap_k_v_).
- Шаблон функции [**winrt::single_threaded_observable_map**](/uwp/cpp-ref-for-winrt/single-threaded-observable-map) возвращает отслеживаемую коллекцию ассоциативность как [**IObservableMap**](/uwp/api/windows.foundation.collections.iobservablemap_k_v_).

При необходимости можно простым этих коллекций с данными, передав функции *rvalue* типа **std::map** или **std::unordered_map**.

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

### <a name="single-threaded"></a>Однопотоковом

«Однопотоковом» в имена этих функций означает, что они не предоставляют все параллелизма&mdash;другими словами, они не являются потокобезопасными. Упомянутой потоки не связано с подразделениями, так как объект, возвращенный эти функции перемещаются (см. в разделе [гибкие объекты в C + +/ WinRT](agile-objects.md)). Это просто объекты, которые в одном потоке. И это совершенно соответствующий, если требуется передавать данные один из способов или другой через двоичного интерфейса приложений (ABI).

## <a name="base-classes-for-collections"></a>Базовые классы для коллекций

Если для завершения гибкость, необходимо реализовать собственную пользовательскую коллекцию, вам потребуется избегать, опыте. Например, это представление пользовательского вектор будет выглядеть следующим образом *без помощи C + +/ WinRT базовые классы*.

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

Вместо этого гораздо проще производным от шаблона структуры [**winrt::vector_view_base**](/uwp/cpp-ref-for-winrt/vector-view-base) в представлении пользовательских вектор и просто реализовать функцию **get_container** для предоставления контейнера, содержащего ваши данные.

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

Контейнер, возвращенный **get_container** необходимо предоставить интерфейс **начала** и **окончания** этого **winrt::vector_view_base** ожидает, что. Как показано в примере выше, **std::vector** предоставляет. Но вы можете вернуть любого контейнера, удовлетворяющий тот же контракт, включая пользовательские контейнера.

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

Это базовый классы C + +/ WinRT предоставляет для реализации пользовательских коллекций.

### [<a name="winrtvectorviewbase"></a>WinRT::vector_view_base](/uwp/cpp-ref-for-winrt/vector-view-base)

См. в приведенном выше примере кода.

### [<a name="winrtvectorbase"></a>WinRT::vector_base](/uwp/cpp-ref-for-winrt/vector-base)

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

### [<a name="winrtobservablevectorbase"></a>WinRT::observable_vector_base](/uwp/cpp-ref-for-winrt/observable-vector-base)

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

### [<a name="winrtmapviewbase"></a>WinRT::map_view_base](/uwp/cpp-ref-for-winrt/map-view-base)

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

### [<a name="winrtmapbase"></a>WinRT::map_base](/uwp/cpp-ref-for-winrt/map-base)

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

### [<a name="winrtobservablemapbase"></a>WinRT::observable_map_base](/uwp/cpp-ref-for-winrt/observable-map-base)

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
* [Шаблон структуры WinRT::map_base](/uwp/cpp-ref-for-winrt/map-base)
* [Шаблон структуры WinRT::map_view_base](/uwp/cpp-ref-for-winrt/map-view-base)
* [Шаблон структуры WinRT::observable_map_base](/uwp/cpp-ref-for-winrt/observable-map-base)
* [Шаблон структуры WinRT::observable_vector_base](/uwp/cpp-ref-for-winrt/observable-vector-base)
* [Шаблон функции WinRT::single_threaded_observable_map](/uwp/cpp-ref-for-winrt/single-threaded-observable-map)
* [Шаблон функции WinRT::single_threaded_map](/uwp/cpp-ref-for-winrt/single-threaded-map)
* [Шаблон функции WinRT::single_threaded_observable_vector](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector)
* [Шаблон функции WinRT::single_threaded_vector](/uwp/cpp-ref-for-winrt/single-threaded-vector)
* [Шаблон структуры WinRT::vector_base](/uwp/cpp-ref-for-winrt/vector-base)
* [Шаблон структуры WinRT::vector_view_base](/uwp/cpp-ref-for-winrt/vector-view-base)

## <a name="related-topics"></a>Статьи по теме
* [Категории значений и ссылки на них](cpp-value-categories.md)
* [Элементы управления XAML; привязка к коллекции C++/WinRT](binding-collection.md)
