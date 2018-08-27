---
author: stevewhims
description: C + +/ WinRT предоставляет функции и базовые классы, которые сэкономить время и силы, если требуется реализовать и/или передачи коллекций.
title: Семейства сайтов с C + +/ WinRT
ms.author: stwhi
ms.date: 08/24/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартные, c ++ cpp, winrt, проекции, семейства сайтов
ms.localizationpriority: medium
ms.openlocfilehash: 54f949c41af885ec379eaa9e5b12764710532b50
ms.sourcegitcommit: 753dfcd0f9fdfc963579dd0b217b445c4b110a18
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "2867951"
---
# <a name="collections-with-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Семейства сайтов с [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)

> [!NOTE]
> **Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.**

Для внутреннего использования коллекция среды выполнения Windows содержит множество сложных компонентов. Однако для передачи коллекцию, содержащую объект функции среды выполнения Windows или для реализации собственного свойства семейства сайтов и коллекции типов, необходимо наличие функции и базовых классов C + +/ WinRT поддержку. Эти функции занять сложности от руки и сохраните дополнительные затраты в время и силы.

> [!IMPORTANT]
> Функции, описанные в этом разделе, доступен, если вы установили [Windows 10 пакет SDK для предварительной версии построения 17661](https://www.microsoft.com/software-download/windowsinsiderpreviewSDK)или более поздней версии.

[**IVector**](/uwp/api/windows.foundation.collections.ivector_t_) — это интерфейс среды выполнения Windows, реализованный любой произвольный доступ коллекцию элементов. Если для реализации **IVector** самостоятельно, необходимо также реализовать [**IIterable**](/uwp/api/windows.foundation.collections.iiterable_t_), [**IVectorView**](/uwp/api/windows.foundation.collections.ivectorview_t_)и [**IIterator**](/uwp/api/windows.foundation.collections.iiterator_t_). Даже в том случае, если *требуется* пользовательской коллекции введите это много времени. Однако если у вас есть данные в **std::vector** (или **std::map**или **std::unordered_map**) и все, что вы собираетесь выполнить — передать его API среды выполнения Windows, затем следует избегать этого уровня работы, если это возможно. И избегать его использования *— это* возможно, так как C + +/ WinRT поможет вам для создания семейства сайтов, эффективное и с минимальными усилиями.

## <a name="helper-functions-for-collections"></a>Вспомогательные функции для семейств сайтов

### <a name="general-purpose-collection-empty"></a>Это семейство общего назначения, пустой

Чтобы получить новый объект типа, который реализует это семейство общего назначения, можно вызвать шаблона функции **winrt::single_threaded_vector** . Объект возвращается в виде [**IVector**](/uwp/api/windows.foundation.collections.ivector_t_), и это интерфейс, через который вызове функции и свойства возвращенного объекта.

```cppwinrt
...
#include <winrt/Windows.Foundation.Collections.h>
#include <iostream>
using namespace winrt;
...
int main()
{
    init_apartment();

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

Как видно в предыдущем примере кода, после создания коллекции можно добавить элементы, выполните итерацию по ним и обычно обрабатывают объекта, как и любой объект коллекции среды выполнения Windows, вы получили от интерфейса API. Если вам требуется представления в коллекции, можно вызвать [IVector::GetView](/uwp/api/windows.foundation.collections.ivector-1.getview), как показано. Шаблон, показанный выше&mdash;создания и использования коллекцию&mdash;подходит для простых сценариев, которых вы собираетесь передачи данных в или получить данные из интерфейса API.

### <a name="general-purpose-collection-primed-from-data"></a>Это семейство общего назначения, заполняется на основе данных

Также можно избежать затрат на вызовы **Append** , можно увидеть в предыдущем примере кода. Уже может быть источника данных, или вы можете предпочесть внесите в нее до создания объекта семейства сайтов среды выполнения Windows. Вот как это сделать.

```cppwinrt
auto coll1{ winrt::single_threaded_vector<int>({ 1,2,3 }) };

std::vector<int> values{ 1,2,3 };
auto coll2{ winrt::single_threaded_vector<int>(std::move(values)) };

for (auto const& el : coll2)
{
    std::cout << el << std::endl;
}
```

Вы можете передать временные object, содержащий данные для **winrt::single_threaded_vector**, как и в случае `coll1`, выше. Или вы можете переместить **std::vector** (Если вы не будут доступ к его еще раз) в функцию. В обоих случаях передается в функцию *rvalue* . Которая позволяет компилятора для повышения эффективности и предотвратить копирование данных. Если вы хотите узнать больше о *rvalues*, просмотрите [значение категорий и ссылки на них](cpp-value-categories.md).

Если вы хотите привязка элемента управления XAML элементов в коллекцию, затем можно. Но помните, что правильно настроить свойство [**ItemsControl.ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) , им необходимо задать значение типа **IVector** **IInspectable** (или взаимодействие типа, например [**IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector)). Ниже приведен пример кода, который создает коллекцию тип, подходящий для привязки и добавляет элемент.

```cppwinrt
auto bookSkus{ winrt::single_threaded_vector<Windows::Foundation::IInspectable>() };
bookSkus.Append(make<Bookstore::implementation::BookSku>(L"Moby Dick"));
```

Коллекции выше *может* быть привязан к элементу управления элементов XAML; но не наблюдаемый объект коллекции.

### <a name="observable-collection"></a>Наблюдаемое семейства сайтов

Чтобы получить новый объект типа, который реализует коллекцию *наблюдаемый объект* , вызовите шаблона функции **winrt::single_threaded_observable_vector** с любого типа элемента. Но чтобы сделать наблюдаемое коллекции подходит для привязки к элементу управления элементов XAML, воспользуйтесь **IInspectable** тип элементов.

Объект возвращается в виде [**IObservableVector**](/uwp/api/windows.foundation.collections.iobservablevector_t_), и это интерфейс, с помощью которой вы (или элемент управления, к которому она привязана) вызова функции и свойства возвращенного объекта.

```cppwinrt
auto bookSkus{ winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>() };
```

Дополнительные сведения и примеры кода, о привязке пользователя пользовательского интерфейса определяет наблюдаемое семейство сайтов, см [XAML элементы, элементы управления, привязка к C + +/ коллекции WinRT](binding-collection.md).

### <a name="associative-container-map"></a>Связанного контейнер (map)

Существует две функции, которые мы узнали версии связанного контейнера.

- Шаблон функции **single_threaded_map** возвращает связанного контейнер в виде [**IMap**](/uwp/api/windows.foundation.collections.imap_k_v_). Карты не наблюдаемое.
- Шаблон функции **single_threaded_observable_map** возвращает наблюдаемое связанного контейнер в виде [**IObservableMap**](/uwp/api/windows.foundation.collections.iobservablemap_k_v_).

При необходимости можно простым эти контейнеры с данными, передав в функцию *rvalue* типа **std::map** или **std::unordered_map**.

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

### <a name="single-threaded"></a>В одном потоке

«В одном потоке» в именах эти функции указывает, что они не предоставляют любого параллельного&mdash;другими словами, они не являющихся потокобезопасными. Упоминание потоков не связано с подразделениях, так как объект, возвращенный эти функции более все гибкой (просмотреть [гибкой объектов в C + +/ WinRT](agile-objects.md)). Это так же, что объекты не используются в одном потоке. И полностью подходящим, если требуется передавать данные одним из способов или другое через двоичный интерфейс приложений (ABI).

## <a name="base-classes-for-collections"></a>Основные классы для семейств сайтов

Если для завершения гибкость, необходимо реализовать настраиваемые коллекции, будет необходимо избегать, путем. Например, это представление векторной будет выглядеть следующим *без помощи C + +/ базовых классов WinRT's*.

```cppwinrt
...
using namespace Windows::Foundation::Collections;

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

Вместо этого намного проще производными векторной представление шаблона структуры **winrt::vector_view_base** и только что внедрять функции **get_container** для предоставления контейнера, содержащего данные.

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

Контейнер, возвращаемые **get_container** необходимо предоставить интерфейс **начала** и **окончания** , **winrt::vector_view_base** ожидает. Как показано в приведенном выше примере **std::vector** предоставляет. Однако можно вернуть любой контейнер, удовлетворяющий тот же контракт, включая настраиваемые контейнера.

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

Это базовый классов C + +/ WinRT предоставляет для реализации настраиваемого семейства сайтов.

- **WinRT::vector_view_base**
- **WinRT::vector_base**
- **WinRT::observable_vector_base**
- **WinRT::map_view_base**
- **WinRT::map_base**
- **WinRT::observable_map_base**

## <a name="important-apis"></a>Важные API
* [ItemsControl.ItemsSource](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource)
* [IObservableVector](/uwp/api/windows.foundation.collections.iobservablevector_t_)
* [IVector](/uwp/api/windows.foundation.collections.ivector_t_)

## <a name="related-topics"></a>Статьи по теме
* [Значение категории и ссылки на них](cpp-value-categories.md)
* [Элементы управления XAML; привязка к коллекции C++/WinRT](binding-collection.md)
