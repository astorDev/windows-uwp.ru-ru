---
description: C + +/ WinRT предоставляет функции и базовые классы, которые сэкономить много времени и усилий, если вы хотите реализовать и (или) передавать коллекции.
title: Создание коллекций с помощью C++/WinRT
ms.date: 10/03/2018
ms.topic: article
keywords: Windows 10, uwp, стандартные, c ++, cpp, winrt, проекции, коллекции
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: a50ab5f70faa0c8f8b73eada38444bcafd444d8b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57635439"
---
# <a name="collections-with-cwinrt"></a>Создание коллекций с помощью C++/WinRT

На внутреннем уровне коллекции среды выполнения Windows имеет множество сложных компонентов. Но при необходимости можно передать объект коллекции на функцию среды выполнения Windows или реализовать свои собственные свойства и коллекции типов коллекций, предусмотрены функции и базовые классы в [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) поддержку. Эти функции занять сложности из рук и сохранить большие затраты времени и усилий.

[**IVector** ](/uwp/api/windows.foundation.collections.ivector_t_) — среды выполнения Windows интерфейс, реализуемый коллекции элементов любого произвольного доступа. Если же требуется реализовать **IVector** самостоятельно, необходимо также реализовать [ **IIterable**](/uwp/api/windows.foundation.collections.iiterable_t_), [ **IVectorView** ](/uwp/api/windows.foundation.collections.ivectorview_t_), и [ **IIterator**](/uwp/api/windows.foundation.collections.iiterator_t_). Даже если вы *требуется* пользовательского типа коллекции, который является большой объем работы. Но при наличии данных **std::vector** (или **std::map**, или **std::unordered_map**) и вы хотите сделать всего передать его в API среды выполнения Windows, то следует избегать Этот уровень работы, если это возможно. И избегать его использования *является* невозможно, поскольку C + +/ WinRT поможет вам создать коллекции, эффективно и с минимальными усилиями.

Также см. в разделе [XAML элементов элемента управления; привязка к C + +/ WinRT коллекции](binding-collection.md).

> [!NOTE]
> Если вы еще не установили пакет Windows SDK версии 10.0.17763.0 (Windows 10, версия 1809) или более поздней версии, затем не имеют доступа к функциям и базовые классы, описанные в этом разделе. Вместо этого см. в разделе [при наличии более старую версию пакета SDK Windows](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector#if-you-have-an-older-version-of-the-windows-sdk) пример шаблона наблюдаемый вектор, который можно использовать вместо этого.

## <a name="helper-functions-for-collections"></a>Вспомогательные функции для коллекций

### <a name="general-purpose-collection-empty"></a>Общего назначения пустой коллекции

В этом разделе рассматриваются сценарии, где вы хотите создать коллекцию, которая изначально пуста; затем заполните *после* создания.

Чтобы получить новый объект типа, который реализует коллекцию общего назначения, можно вызвать [ **winrt::single_threaded_vector** ](/uwp/cpp-ref-for-winrt/single-threaded-vector) шаблона функции. Объект возвращается в виде [ **IVector**](/uwp/api/windows.foundation.collections.ivector_t_), и это интерфейс, через который вызове функции и свойства возвращаемого объекта.

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

Как показано в приведенном выше примере кода, после создания коллекции можно добавить элементы, их итерацию и обычно обрабатывают объект, как и любой объект коллекции среды выполнения Windows, который вы получили из API-интерфейса. Если требуется более неизменяемый представления коллекции, то можно вызвать [ **IVector::GetView**](/uwp/api/windows.foundation.collections.ivector-1.getview), как показано. Шаблон, приведенный выше&mdash;создания и использования коллекции&mdash;подходит для простых сценариев, где вы хотите передать данные в или получение данных из API. Вы можете передать **IVector**, или **IVectorView**из любой точки мира [ **IIterable** ](/uwp/api/windows.foundation.collections.iiterable_t_) ожидается.

В примере выше вызов **winrt::init_apartment** инициализирует COM; по умолчанию в многопотоковом подразделении.

### <a name="general-purpose-collection-primed-from-data"></a>Общего назначения коллекции, заполняется на основе данных

В этом разделе рассматривается сценарий, где вы хотите создать коллекцию и заполнить ее в то же время.

Можно избежать вызовов **Append** в предыдущем примере кода. Уже имеется источник данных, либо можно использовать для заполнения источника данных перед созданием объекта коллекции среды выполнения Windows. Вот как это сделать.

```cppwinrt
auto coll1{ winrt::single_threaded_vector<int>({ 1,2,3 }) };

std::vector<int> values{ 1,2,3 };
auto coll2{ winrt::single_threaded_vector<int>(std::move(values)) };

for (auto const& el : coll2)
{
    std::cout << el << std::endl;
}
```

Можно передать временный объект, содержащий данные для **winrt::single_threaded_vector**, как и в `coll1`выше. Или вы можете переместить **std::vector** (при условии, что вы не будет к нему доступ еще раз) в функцию. В обоих случаях передается *rvalue* с заходом в функцию. Это позволяет компилятор эффективность и избежать копирования данных. Если вы хотите узнать больше о *rvalues*, см. в разделе [значение категории и ссылки на них](cpp-value-categories.md).

Если вы хотите привязать элемент управления XAML элементы в коллекцию, затем можно. Но имейте в виду, чтобы правильно установить [ **ItemsControl.ItemsSource** ](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) свойство, необходимо присвоить значение типа **IVector** из **IInspectable** (или имеет тип взаимодействия, таких как [ **IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector)). Ниже приведен пример кода, который создает коллекцию типа, подходящим для связывания и добавляет элемент к нему.

```cppwinrt
auto bookSkus{ winrt::single_threaded_vector<Windows::Foundation::IInspectable>() };
bookSkus.Append(make<Bookstore::implementation::BookSku>(L"Moby Dick"));
```

Создание коллекции среды выполнения Windows на основе данных и получить представление на нем все готово для передачи в API, все не копируя никаких действий.

```cppwinrt
std::vector<float> values{ 0.1f, 0.2f, 0.3f };
IVectorView<float> view{ winrt::single_threaded_vector(std::move(values)).GetView() };
```

В приведенных выше примерах коллекции мы создадим *можно* привязываться к XAML элементы управления; но коллекции не наблюдаемый объект.

### <a name="observable-collection"></a>Наблюдаемую коллекцию

Чтобы получить новый объект типа, который реализует *наблюдаемый объект* коллекцию, вызовите [ **winrt::single_threaded_observable_vector** ](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) шаблона функции с любым Тип элемента. Но чтобы сделать пригодной для привязки к элементу управления элементы XAML, наблюдаемую коллекцию использовать **IInspectable** элемента типа.

Объект возвращается в виде [ **IObservableVector**](/uwp/api/windows.foundation.collections.iobservablevector_t_), и это интерфейс, через который вы (или элемент управления, к которому он привязан) вызова функции и свойства возвращаемого объекта.

```cppwinrt
auto bookSkus{ winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>() };
```

Дополнительные сведения и примеры кода, о привязки пользователя пользовательского интерфейса элементами управления на наблюдаемые коллекции, см. в разделе [XAML элементов элемента управления; привязка к C + +/ WinRT коллекции](binding-collection.md).

### <a name="associative-collection-map"></a>Ассоциативной коллекции (map)

Существуют версии ассоциативной коллекции из двух функций, которые мы рассмотрели.

- [ **Winrt::single_threaded_map** ](/uwp/cpp-ref-for-winrt/single-threaded-map) функция шаблона возвращает коллекцию ассоциативный ненаблюдаемый как [ **IMap**](/uwp/api/windows.foundation.collections.imap_k_v_).
- [ **Winrt::single_threaded_observable_map** ](/uwp/cpp-ref-for-winrt/single-threaded-observable-map) функция шаблон Возвращает наблюдаемый ассоциативной коллекции как [ **IObservableMap** ](/uwp/api/windows.foundation.collections.iobservablemap_k_v_).

При необходимости можно простым этих коллекций с данными путем передачи в функцию *rvalue* типа **std::map** или **std::unordered_map**.

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

«Однопоточный» в именах этих функций указывает, что они не обеспечивают любой параллелизма&mdash;другими словами, они не являются поточно ориентированными. Упоминание потоков не связан с подразделениями, так как все agile объектов, возвращаемых из этих функций (см. в разделе [объекты в C + +/ WinRT](agile-objects.md)). Это только что объекты являются однопоточными. И полностью подходящим, если требуется передать данные одним из способов или другой через двоичный интерфейс приложений (ABI).

## <a name="base-classes-for-collections"></a>Базовые классы для коллекций

Если для наиболее широкие возможности, вы хотите реализовать свои собственные пользовательские коллекции, необходимо избегать, трудным способом. Например, это, как будет выглядеть представления векторной *без использования C + +/ WinRT в базовые классы*.

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

Вместо этого, гораздо проще для получения представления векторной из [ **winrt::vector_view_base** ](/uwp/cpp-ref-for-winrt/vector-view-base) структуры шаблона и просто реализовать **get_container** Выбор функции предоставлять контейнера, содержащего данные.

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

Контейнера, возвращаемого методом **get_container** необходимо предоставить **начать** и **окончания** интерфейс, который **winrt::vector_view_base** ожидает. Как показано в примере выше, **std::vector** , предоставляет. Но можно также вернуть любой контейнер, удовлетворяющий тот же контракт, в том числе собственный пользовательский контейнер.

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

Они являются основой классы C + +/ WinRT предоставляет для реализации настраиваемых коллекций.

### <a name="winrtvectorviewbaseuwpcpp-ref-for-winrtvector-view-base"></a>[WinRT::vector_view_base](/uwp/cpp-ref-for-winrt/vector-view-base)

См. в разделе Вышеприведенные примеры кода.

### <a name="winrtvectorbaseuwpcpp-ref-for-winrtvector-base"></a>[WinRT::vector_base](/uwp/cpp-ref-for-winrt/vector-base)

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

### <a name="winrtobservablevectorbaseuwpcpp-ref-for-winrtobservable-vector-base"></a>[WinRT::observable_vector_base](/uwp/cpp-ref-for-winrt/observable-vector-base)

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

### <a name="winrtmapviewbaseuwpcpp-ref-for-winrtmap-view-base"></a>[winrt::map_view_base](/uwp/cpp-ref-for-winrt/map-view-base)

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

### <a name="winrtmapbaseuwpcpp-ref-for-winrtmap-base"></a>[winrt::map_base](/uwp/cpp-ref-for-winrt/map-base)

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

### <a name="winrtobservablemapbaseuwpcpp-ref-for-winrtobservable-map-base"></a>[WinRT::observable_map_base](/uwp/cpp-ref-for-winrt/observable-map-base)

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
* [Структура шаблона WinRT::map_base](/uwp/cpp-ref-for-winrt/map-base)
* [Структура шаблона WinRT::map_view_base](/uwp/cpp-ref-for-winrt/map-view-base)
* [Структура шаблона WinRT::observable_map_base](/uwp/cpp-ref-for-winrt/observable-map-base)
* [Структура шаблона WinRT::observable_vector_base](/uwp/cpp-ref-for-winrt/observable-vector-base)
* [Шаблон функции WinRT::single_threaded_observable_map](/uwp/cpp-ref-for-winrt/single-threaded-observable-map)
* [Шаблон функции WinRT::single_threaded_map](/uwp/cpp-ref-for-winrt/single-threaded-map)
* [Шаблон функции WinRT::single_threaded_observable_vector](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector)
* [Шаблон функции WinRT::single_threaded_vector](/uwp/cpp-ref-for-winrt/single-threaded-vector)
* [Структура шаблона WinRT::vector_base](/uwp/cpp-ref-for-winrt/vector-base)
* [Структура шаблона WinRT::vector_view_base](/uwp/cpp-ref-for-winrt/vector-view-base)

## <a name="related-topics"></a>Статьи по теме
* [Значение категории и ссылки на них](cpp-value-categories.md)
* [XAML элементов элемента управления; Привязка к C + +/ WinRT коллекции](binding-collection.md)
