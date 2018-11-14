---
author: stevewhims
description: Коллекция, которая может быть эффективно привязана к элементам управления XAML, называется *отслеживаемой*. В этом разделе показано, как реализовать и использовать отслеживаемую коллекцию и привязать к ней элементы управления XAML.
title: Элементы управления XAML; привязка к коллекции C++/WinRT
ms.author: stwhi
ms.date: 10/03/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, XAML, управление, привязка, коллекция
ms.localizationpriority: medium
ms.openlocfilehash: f9d9d6a2aafe1b81ff331bac92662b111eb48956
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6647360"
---
# <a name="xaml-items-controls-bind-to-a-cwinrt-collection"></a>Элементы управления XAML; привязка к коллекции C++/WinRT

Коллекция, которая может быть эффективно привязана к элементам управления XAML, называется *отслеживаемой*. Эта идея основана на шаблоне проектирования программного обеспечения, известном как *шаблон наблюдателя *. В этом разделе показано, как реализовывать отслеживаемые коллекции в [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), и привязать XAML к ним элементы управления.

Это пошаговое руководство основано на проекте, созданном в разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md), и дополняет понятия, рассмотренные в этом разделе.

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="what-does-observable-mean-for-a-collection"></a>Что означает понятие *отслеживаемая* для коллекции?
Если класс среды выполнения, представляющий коллекцию, вызывает событие [**IObservableVector&lt;T&gt;::VectorChanged**](/uwp/api/windows.foundation.collections.iobservablevector-1.vectorchanged) при добавлении или удалении из него элементов, значит этот класс среды выполнения представляет собой отслеживаемую коллекцию. Элементы управления XAML могут привязываться к этим событиям и обрабатывать их путем получения обновленной коллекции и последующего самообновления для показа текущих элементов.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

## <a name="add-a-bookskus-collection-to-bookstoreviewmodel"></a>Добавление коллекции **BookSkus** в **BookstoreViewModel**

В разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md) мы добавили свойство типа **BookSku** к нашей модели главного представления. На этом этапе мы будем использовать шаблон функции фабрики [**winrt::single_threaded_observable_vector**](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) , который поможет нам реализовать отслеживаемую коллекцию **BookSku** на той же модели представления.

> [!NOTE]
> Если вы еще не установлен пакет Windows SDK версии 10.0.17763.0 (Windows 10, версия 1809) или более поздней версии, обратитесь к разделу [при наличии более ранней версии Windows SDK](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector#if-you-have-an-older-version-of-the-windows-sdk) перечень отслеживаемый шаблон вектора, можно использовать вместо **winrt::single_ threaded_observable_vector**.

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
> Описания MIDL 3.0 выше, обратите внимание, что тип свойства **BookSkus** [**IObservableVector**](/uwp/api/windows.foundation.collections.ivector_t_) из [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable). В следующем разделе этой статьи мы будем привязка источника элементов [**ListBox**](/uwp/api/windows.ui.xaml.controls.listbox) **BookSkus**. Поле со списком — это элемент управления, и правильно задать свойство [**ItemsControl.ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) , необходимо задать его значение с типом **IObservableVector** (или **IVector**) **IInspectable**или типа взаимодействия, например [** IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector).

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

## <a name="related-topics"></a>Статьи по теме
* [Использование API-интерфейсов в C++/WinRT](consume-apis.md)
* [Создание API-интерфейсов в C++/WinRT](author-apis.md)
