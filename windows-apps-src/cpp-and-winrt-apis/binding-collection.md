---
author: stevewhims
description: Коллекция, которая может быть эффективно привязана к элементам управления XAML, называется *отслеживаемой*. В этом разделе показано, как реализовать и использовать отслеживаемую коллекцию и привязать к ней элементы управления XAML.
title: Элементы управления XAML; привязка к коллекции C++/WinRT
ms.author: stwhi
ms.date: 05/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, XAML, управление, привязка, коллекция
ms.localizationpriority: medium
ms.openlocfilehash: 9ba935b1a5316c2d7af9c7681705595efea7ca08
ms.sourcegitcommit: 232543fba1fb30bb1489b053310ed6bd4b8f15d5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "4175057"
---
# <a name="xaml-items-controls-bind-to-a-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt-collection"></a>Элементы управления XAML; привязка к коллекции [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)
> [!NOTE]
> **Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.**

Коллекция, которая может быть эффективно привязана к элементам управления XAML, называется *отслеживаемой*. Эта идея основана на шаблоне проектирования программного обеспечения, известном как *шаблон наблюдателя *. В этом разделе показано, как реализовывать отслеживаемые коллекции в C++/WinRT и привязывать к ним элементы управления XAML.

Это пошаговое руководство основано на проекте, созданном в разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md), и дополняет понятия, рассмотренные в этом разделе.

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="what-does-observable-mean-for-a-collection"></a>Что означает понятие *отслеживаемая* для коллекции?
Если класс среды выполнения, представляющий коллекцию, вызывает событие [**IObservableVector&lt;T&gt;::VectorChanged**](/uwp/api/windows.foundation.collections.iobservablevector-1.vectorchanged) при добавлении или удалении из него элементов, значит этот класс среды выполнения представляет собой отслеживаемую коллекцию. Элементы управления XAML могут привязываться к этим событиям и обрабатывать их путем получения обновленной коллекции и последующего самообновления для показа текущих элементов.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

## <a name="implement-singlethreadedobservablevectorlttgt"></a>Реализация **single_threaded_observable_vector&lt;T&gt;**
Полезно иметь отслеживаемый шаблон вектора, который служит реализацией общего назначения [**IObservableVector&lt;T&gt;**](/uwp/api/windows.foundation.collections.iobservablevector_t_). Ниже приведен код класса под названием **single_threaded_observable_vector\<T\>**.

> [!NOTE]
> Если вы установили [Windows 10 SDK предварительную сборку 17661 пакета](https://www.microsoft.com/software-download/windowsinsiderpreviewSDK)или более поздней версии, затем можно просто напрямую использовать функции фабрики **WinRT:: single_threaded_observable_vector\ < T\ >** вместо приведенного ниже кода (мы позже покажем, точный код в этом разделе). Если вы еще не являетесь эту версию пакета SDK, он будет легко перейти с использования версии с описанием кода для функции **winrt** , когда вы будете.

```cppwinrt
// single_threaded_observable_vector.h
#pragma once

namespace winrt::Bookstore::implementation
{
    using namespace Windows::Foundation::Collections;

    template <typename T>
    struct single_threaded_observable_vector : implements<single_threaded_observable_vector<T>,
        IObservableVector<T>,
        IVector<T>,
        IVectorView<T>,
        IIterable<T>>
    {
        event_token VectorChanged(VectorChangedEventHandler<T> const& handler)
        {
            return m_changed.add(handler);
        }

        void VectorChanged(event_token const cookie)
        {
            m_changed.remove(cookie);
        }

        T GetAt(uint32_t const index) const
        {
            if (index >= m_values.size())
            {
                throw hresult_out_of_bounds();
            }

            return m_values[index];
        }

        uint32_t Size() const noexcept
        {
            return static_cast<uint32_t>(m_values.size());
        }

        IVectorView<T> GetView()
        {
            return *this;
        }

        bool IndexOf(T const& value, uint32_t& index) const noexcept
        {
            index = static_cast<uint32_t>(std::find(m_values.begin(), m_values.end(), value) - m_values.begin());
            return index < m_values.size();
        }

        void SetAt(uint32_t const index, T const& value)
        {
            if (index >= m_values.size())
            {
                throw hresult_out_of_bounds();
            }

            ++m_version;
            m_values[index] = value;
            m_changed(*this, make<args>(CollectionChange::ItemChanged, index));
        }

        void InsertAt(uint32_t const index, T const& value)
        {
            if (index > m_values.size())
            {
                throw hresult_out_of_bounds();
            }

            ++m_version;
            m_values.insert(m_values.begin() + index, value);
            m_changed(*this, make<args>(CollectionChange::ItemInserted, index));
        }

        void RemoveAt(uint32_t const index)
        {
            if (index >= m_values.size())
            {
                throw hresult_out_of_bounds();
            }

            ++m_version;
            m_values.erase(m_values.begin() + index);
            m_changed(*this, make<args>(CollectionChange::ItemRemoved, index));
        }

        void Append(T const& value)
        {
            ++m_version;
            m_values.push_back(value);
            m_changed(*this, make<args>(CollectionChange::ItemInserted, Size() - 1));
        }

        void RemoveAtEnd()
        {
            if (m_values.empty())
            {
                throw hresult_out_of_bounds();
            }

            ++m_version;
            m_values.pop_back();
            m_changed(*this, make<args>(CollectionChange::ItemRemoved, Size()));
        }

        void Clear() noexcept
        {
            ++m_version;
            m_values.clear();
            m_changed(*this, make<args>(CollectionChange::Reset, 0));
        }

        uint32_t GetMany(uint32_t const startIndex, array_view<T> values) const
        {
            if (startIndex >= m_values.size())
            {
                return 0;
            }

            uint32_t actual = static_cast<uint32_t>(m_values.size() - startIndex);

            if (actual > values.size())
            {
                actual = values.size();
            }

            std::copy_n(m_values.begin() + startIndex, actual, values.begin());
            return actual;
        }

        void ReplaceAll(array_view<T const> value)
        {
            ++m_version;
            m_values.assign(value.begin(), value.end());
            m_changed(*this, make<args>(CollectionChange::Reset, 0));
        }

        IIterator<T> First()
        {
            return make<iterator>(this);
        }

    private:

        std::vector<T> m_values;
        event<VectorChangedEventHandler<T>> m_changed;
        uint32_t m_version{};

        struct args : implements<args, IVectorChangedEventArgs>
        {
            args(CollectionChange const change, uint32_t const index) :
                m_change(change),
                m_index(index)
            {
            }

            CollectionChange CollectionChange() const
            {
                return m_change;
            }

            uint32_t Index() const
            {
                return m_index;
            }

        private:

            Windows::Foundation::Collections::CollectionChange const m_change{};
            uint32_t const m_index{};
        };

        struct iterator : implements<iterator, IIterator<T>>
        {
            explicit iterator(single_threaded_observable_vector<T>* owner) noexcept :
            m_version(owner->m_version),
                m_current(owner->m_values.begin()),
                m_end(owner->m_values.end())
            {
                m_owner.copy_from(owner);
            }

            void abi_enter() const
            {
                if (m_version != m_owner->m_version)
                {
                    throw hresult_changed_state();
                }
            }

            T Current() const
            {
                if (m_current == m_end)
                {
                    throw hresult_out_of_bounds();
                }

                return*m_current;
            }

            bool HasCurrent() const noexcept
            {
                return m_current != m_end;
            }

            bool MoveNext() noexcept
            {
                if (m_current != m_end)
                {
                    ++m_current;
                }

                return HasCurrent();
            }

            uint32_t GetMany(array_view<T> values)
            {
                uint32_t actual = static_cast<uint32_t>(std::distance(m_current, m_end));

                if (actual > values.size())
                {
                    actual = values.size();
                }

                std::copy_n(m_current, actual, values.begin());
                std::advance(m_current, actual);
                return actual;
            }

        private:

            com_ptr<single_threaded_observable_vector<T>> m_owner;
            uint32_t const m_version;
            typename std::vector<T>::const_iterator m_current;
            typename std::vector<T>::const_iterator const m_end;
        };
    };
}
```

Функция **Append** показывает, как вызывать событие [**IObservableVector&lt;T&gt;::VectorChanged**](/uwp/api/windows.foundation.collections.iobservablevector-1.vectorchanged).

```cppwinrt
m_changed(*this, make<args>(CollectionChange::ItemInserted, Size() - 1));
```

Аргументы события указывают на то, что был вставлен элемент, а также на то, каков его индекс (в данном случае это последний элемент). Эти аргументы позволяют элементу управления XAML реагировать на событие и обновляться оптимальным способом.

## <a name="add-a-bookskus-collection-to-bookstoreviewmodel"></a>Добавление коллекции **BookSkus** в **BookstoreViewModel**
В разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md) мы добавили свойство типа **BookSku** к нашей модели главного представления. На этом шаге мы используем **single_threaded_observable_vector&lt;T&gt;**, который поможет нам реализовать отслеживаемую коллекцию **BookSku** для той же модели представления.

Объявите новое свойство в `BookstoreViewModel.idl`.

```idl
// BookstoreViewModel.idl
...
runtimeclass BookstoreViewModel
{
    BookSku BookSku{ get; };
    Windows.Foundation.Collections.IVector<IInspectable> BookSkus{ get; };
}
...
```

> [!IMPORTANT]
> Описания MIDL 3.0 выше, обратите внимание, что тип свойства **BookSkus** [**IVector**](/uwp/api/windows.foundation.collections.ivector_t_) из [**IInspectable**](https://msdn.microsoft.com/library/windows/desktop/br205821). В следующем разделе этой статьи мы будем привязка источника элементов [**ListBox**](/uwp/api/windows.ui.xaml.controls.listbox) **BookSkus**. Поле со списком — это элемент управления, и правильно задать свойство [**ItemsControl.ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) , необходимо задать его значение с типом **IVector** **IInspectable**или типа взаимодействия, например [**IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector).

Сохраните и выполните сборку. Скопируйте заглушки доступа из `BookstoreViewModel.h` и `BookstoreViewModel.cpp` в папку `Generated Files` и реализуйте их.

```cppwinrt
// BookstoreViewModel.h
...
#include "single_threaded_observable_vector.h"
...
struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
{
    BookstoreViewModel();

    Bookstore::BookSku BookSku();

    Windows::Foundation::Collections::IVector<Windows::Foundation::IInspectable> BookSkus();

private:
    Bookstore::BookSku m_bookSku{ nullptr };
    Windows::Foundation::Collections::IVector<Windows::Foundation::IInspectable> m_bookSkus;
};
...
```

```cppwinrt
// BookstoreViewModel.cpp
...
BookstoreViewModel::BookstoreViewModel()
{
    m_bookSku = make<Bookstore::implementation::BookSku>(L"Atticus");
    m_bookSkus = winrt::make<single_threaded_observable_vector<Windows::Foundation::IInspectable>>();
    m_bookSkus.Append(m_bookSku);
}

Bookstore::BookSku BookstoreViewModel::BookSku()
{
    return m_bookSku;
}

Windows::Foundation::Collections::IVector<Windows::Foundation::IInspectable> BookstoreViewModel::BookSkus()
{
    return m_bookSkus;
}
...
```

## <a name="if-you-have-a-windows-10-sdk-preview-build"></a>Если у вас есть SDK предварительной сборке Windows 10
Если вы установили [Windows 10 SDK предварительную сборку 17661 пакета](https://www.microsoft.com/software-download/windowsinsiderpreviewSDK), или более поздней версии, замените эту строку кода

```cppwinrt
m_bookSkus = winrt::make<single_threaded_observable_vector<Windows::Foundation::IInspectable>>();
```

с этим.

```cppwinrt
m_bookSkus = winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>();
```

Вместо вызова [**winrt::make**](https://docs.microsoft.com/en-us/uwp/cpp-ref-for-winrt/make), вы создаете объект соответствующую коллекцию путем вызова функции фабрики **WinRT:: single_threaded_observable_vector\ < T\ >** .

## <a name="bind-a-listbox-to-the-bookskus-property"></a>Привязка элемента ListBox к свойству **BookSkus**
Откройте файл `MainPage.xaml`, который содержит разметку XAML для главной страницы пользовательского интерфейса. Добавьте следующую разметку внутри того же элемента **StackPanel**, что и **Button**.

```xaml
<ListBox ItemsSource="{x:Bind MainViewModel.BookSkus}">
    <ItemsControl.ItemTemplate>
        <DataTemplate x:DataType="local:BookSku">
            <TextBlock Text="{x:Bind Title, Mode=OneWay}"/>
        </DataTemplate>
    </ItemsControl.ItemTemplate>
</ListBox>
```

В файле `MainPage.cpp` добавьте строку в обработчик события **Click** для добавления книги в коллекцию.

```cppwinrt
// MainPage.cpp
...
void MainPage::ClickHandler(IInspectable const&, RoutedEventArgs const&)
{
    MainViewModel().BookSku().Title(L"To Kill a Mockingbird");
    MainViewModel().BookSkus().Append(make<Bookstore::implementation::BookSku>(L"Moby Dick"));
}
...
```

Выполните сборку и запуск проекта. Нажмите кнопку, чтобы запустить обработчик события **Click**. Мы увидели, что реализация **Append** вызывает событие, чтобы сообщить пользовательскому интерфейсу о том, что коллекция изменилась; а **ListBox** повторно запрашивает коллекцию, чтобы обновить свое собственное значение **Items**. Так же, как и раньше, меняется название одной из книг; и это изменение отражается как на кнопке, так и в списке.

## <a name="important-apis"></a>Важные API
* [IObservableVector&lt;T&gt;::VectorChanged](/uwp/api/windows.foundation.collections.iobservablevector-1.vectorchanged)
* [Шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)

## <a name="related-topics"></a>Статьи по теме
* [Использование API-интерфейсов в C++/WinRT](consume-apis.md)
* [Создание API-интерфейсов в C++/WinRT](author-apis.md)
