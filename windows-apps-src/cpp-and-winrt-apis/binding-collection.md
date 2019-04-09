---
description: Коллекция, которая может быть эффективно привязана к элементам управления XAML, называется *отслеживаемой*. В этом разделе показано, как реализовать и использовать отслеживаемую коллекцию и привязать к ней элементы управления XAML.
title: Элементы управления XAML; привязка к коллекции C++/WinRT
ms.date: 10/03/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, XAML, управление, привязка, коллекция
ms.localizationpriority: medium
ms.openlocfilehash: c4bf1805b16d869e7a29c49e8fe53c01cf469132
ms.sourcegitcommit: c315ec3e17489aeee19f5095ec4af613ad2837e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58921670"
---
# <a name="xaml-items-controls-bind-to-a-cwinrt-collection"></a>Элементы управления XAML; привязка к коллекции C++/WinRT

Коллекция, которая может быть эффективно привязана к элементам управления XAML, называется *отслеживаемой*. Эта идея основана на шаблоне проектирования программного обеспечения, известном как *шаблон наблюдателя* . В этом разделе показан способ реализации наблюдаемых наборов в [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), и как выполнить привязку XAML элементов элемента управления к ним.

Это пошаговое руководство основано на проекте, созданном в разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md), и дополняет понятия, рассмотренные в этом разделе.

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="what-does-observable-mean-for-a-collection"></a>Что означает понятие *отслеживаемая* для коллекции?
Если класс среды выполнения, представляющий коллекцию, вызывает событие [**IObservableVector&lt;T&gt;::VectorChanged**](/uwp/api/windows.foundation.collections.iobservablevector-1.vectorchanged) при добавлении или удалении из него элементов, значит этот класс среды выполнения представляет собой отслеживаемую коллекцию. Элементы управления XAML могут привязываться к этим событиям и обрабатывать их путем получения обновленной коллекции и последующего самообновления для показа текущих элементов.

> [!NOTE]
> Сведения об установке и использовании C++WinRT Visual Studio Extension (VSIX) и пакет NuGet (которые вместе обеспечивают шаблон проекта и поддержка сборки), см. в разделе [поддержка Visual Studio C++/WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

## <a name="add-a-bookskus-collection-to-bookstoreviewmodel"></a>Добавление коллекции **BookSkus** в **BookstoreViewModel**

В разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md) мы добавили свойство типа **BookSku** к нашей модели главного представления. На этом шаге мы будем использовать [ **winrt::single_threaded_observable_vector** ](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) фабрики шаблона функции, чтобы помочь нам реализовать наблюдаемую коллекцию элементов **BookSku** на же модель представления.

> [!NOTE]
> Если вы еще не установили пакет Windows SDK версии 10.0.17763.0 (Windows 10, версия 1809) или более поздней версии, затем см. в разделе [при наличии более старую версию пакета SDK Windows](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector#if-you-have-an-older-version-of-the-windows-sdk) пример шаблона наблюдаемый вектор, который можно использовать вместо **winrt::single_threaded_observable_vector**.

Объявите новое свойство в `BookstoreViewModel.idl`.

```idl
// BookstoreViewModel.idl
...
runtimeclass BookstoreViewModel
{
    BookSku BookSku{ get; };
    Windows.Foundation.Collections.IObservableVector<IInspectable> BookSkus{ get; };
}
...
```

> [!IMPORTANT]
> Обратите внимание, что в приведенном выше фрагменте MIDL 3.0, тип **BookSkus** свойство [ **IObservableVector** ](/uwp/api/windows.foundation.collections.ivector_t_) из [ **IInspectable** ](/windows/desktop/api/inspectable/nn-inspectable-iinspectable). В следующем разделе этой статьи, мы будем привязку элементов источника [ **ListBox** ](/uwp/api/windows.ui.xaml.controls.listbox) для **BookSkus**. Поле со списком — это элемент управления и правильно установить [ **ItemsControl.ItemsSource** ](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) свойство, необходимо присвоить значение типа **IObservableVector** (или **IVector**) из **IInspectable**, или имеет тип взаимодействия, таких как [ **IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector).

Сохраните и выполните сборку. Скопируйте заглушки доступа из `BookstoreViewModel.h` и `BookstoreViewModel.cpp` в папку `Generated Files` и реализуйте их.

```cppwinrt
// BookstoreViewModel.h
...
struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
{
    BookstoreViewModel();

    Bookstore::BookSku BookSku();

    Windows::Foundation::Collections::IObservableVector<Windows::Foundation::IInspectable> BookSkus();

private:
    Bookstore::BookSku m_bookSku{ nullptr };
    Windows::Foundation::Collections::IObservableVector<Windows::Foundation::IInspectable> m_bookSkus;
};
...
```

```cppwinrt
// BookstoreViewModel.cpp
...
BookstoreViewModel::BookstoreViewModel()
{
    m_bookSku = winrt::make<Bookstore::implementation::BookSku>(L"Atticus");
    m_bookSkus = winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>();
    m_bookSkus.Append(m_bookSku);
}

Bookstore::BookSku BookstoreViewModel::BookSku()
{
    return m_bookSku;
}

Windows::Foundation::Collections::IObservableVector<Windows::Foundation::IInspectable> BookstoreViewModel::BookSkus()
{
    return m_bookSkus;
}
...
```

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
    MainViewModel().BookSkus().Append(winrt::make<Bookstore::implementation::BookSku>(L"Moby Dick"));
}
...
```

Выполните сборку и запуск проекта. Нажмите кнопку, чтобы запустить обработчик события **Click**. Мы увидели, что реализация **Append** вызывает событие, чтобы сообщить пользовательскому интерфейсу о том, что коллекция изменилась; а **ListBox** повторно запрашивает коллекцию, чтобы обновить свое собственное значение **Items**. Так же, как и раньше, меняется название одной из книг; и это изменение отражается как на кнопке, так и в списке.

## <a name="important-apis"></a>Важные API
* [IObservableVector&lt;T&gt;::VectorChanged](/uwp/api/windows.foundation.collections.iobservablevector-1.vectorchanged)
* [Шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)

## <a name="related-topics"></a>См. также
* [Использование API-интерфейсов с помощью C++/WinRT](consume-apis.md)
* [Создание API-интерфейсов с помощью C++/WinRT](author-apis.md)
