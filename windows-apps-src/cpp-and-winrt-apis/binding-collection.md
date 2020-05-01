---
description: Коллекция, которая может быть эффективно привязана к элементам управления XAML, называется *отслеживаемой*. В этом разделе показано, как реализовать и использовать отслеживаемую коллекцию и привязать к ней элементы управления XAML.
title: Элементы управления XAML; привязка к коллекции C++/WinRT
ms.date: 04/24/2019
ms.topic: article
keywords: windows 10, uwp, стандартный, c++, cpp, winrt, проекция, XAML, управление, привязка, коллекция
ms.localizationpriority: medium
ms.openlocfilehash: a98056190d035910a8ed83d2f37799a98b685ce6
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "70304518"
---
# <a name="xaml-items-controls-bind-to-a-cwinrt-collection"></a>Элементы управления XAML; привязка к коллекции C++/WinRT

Коллекция, которая может быть эффективно привязана к элементам управления XAML, называется *отслеживаемой*. Эта идея основана на шаблоне проектирования программного обеспечения, известном как *шаблон наблюдателя*. В этом разделе показано, как реализовывать отслеживаемые коллекции в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) и привязывать к ним элементы управления XAML (вводные сведения см. в разделе [Привязка данных](/windows/uwp/data-binding)).

Если вы хотите следовать инструкциям в этом разделе, рекомендуется сначала создать проект, который описан в разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md). В этом разделе добавляется дополнительный код для этого проекта, а также дополняются основные понятия, приведенные в том разделе.

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, описаны в разделах [Использование интерфейсов API с помощью C++/WinRT](consume-apis.md) и [Создание интерфейсов API с помощью C++/WinRT](author-apis.md).

## <a name="what-does-observable-mean-for-a-collection"></a>Что означает понятие *отслеживаемая* для коллекции?
Если класс среды выполнения, представляющий коллекцию, вызывает событие [**IObservableVector&lt;T&gt;::VectorChanged**](/uwp/api/windows.foundation.collections.iobservablevector-1.vectorchanged) при добавлении или удалении из него элементов, значит, этот класс среды выполнения представляет собой отслеживаемую коллекцию. Элементы управления XAML могут привязываться к этим событиям и обрабатывать их путем получения обновленной коллекции и последующего самообновления для отображения текущих элементов.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки) см. в разделе о [поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

## <a name="add-a-bookskus-collection-to-bookstoreviewmodel"></a>Добавление коллекции **BookSkus** в **BookstoreViewModel**

В разделе [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md) мы добавили свойство типа **BookSku** к нашей модели главного представления. На этом шаге мы используем стандартный шаблон функции [**winrt::single_threaded_observable_vector**](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector), который поможет нам реализовать отслеживаемую коллекцию **BookSku** для той же модели представления.

Объявите новое свойство в `BookstoreViewModel.idl`.

```idl
// BookstoreViewModel.idl
...
runtimeclass BookstoreViewModel
{
    BookSku BookSku{ get; };
    Windows.Foundation.Collections.IObservableVector<BookSku> BookSkus{ get; };
}
...
```

> [!NOTE]
> Обратите внимание, что в приведенном выше листинге MIDL 3.0 типом свойства **BookSkus** является [**IObservableVector**](/uwp/api/windows.foundation.collections.ivector_t_)**BookSku**. В следующем подразделе этого раздела мы привяжем источник элементов [**ListBox**](/uwp/api/windows.ui.xaml.controls.listbox) к **BookSkus**. Список — это элемент управления элементами. Чтобы правильно задать свойство [**ItemsControl.ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource), необходимо присвоить значение типа **IObservableVector** (или **IVector**) или типа взаимодействия, например [**IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector).

> [!WARNING]
> Код, представленный в этом разделе, относится к C++/WinRT 2.0.190530.8 и более поздних версий. Если вы используете более раннюю версию, нужно внести в приведенный код несколько незначительных изменений. В приведенном выше листинге MIDL 3.0 измените значение свойства **BookSkus** на [**IObservableVector**](/uwp/api/windows.foundation.collections.ivector_t_) интерфейса [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable). А затем также используйте в соей реализации **IInspectable** (а не **BookSku**).

Сохраните проект и выполните сборку. Скопируйте заглушки метода доступа из `BookstoreViewModel.h` и `BookstoreViewModel.cpp` в папку `\Bookstore\Bookstore\Generated Files\sources` (дополнительные сведения см. в предыдущем разделе, [Элементы управления XAML; привязка к свойству C++/WinRT](binding-property.md)). Реализуйте эти заглушки метода доступа следующим образом.

```cppwinrt
// BookstoreViewModel.h
...
struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
{
    BookstoreViewModel();

    Bookstore::BookSku BookSku();

    Windows::Foundation::Collections::IObservableVector<Bookstore::BookSku> BookSkus();

private:
    Bookstore::BookSku m_bookSku{ nullptr };
    Windows::Foundation::Collections::IObservableVector<Bookstore::BookSku> m_bookSkus;
};
...
```

```cppwinrt
// BookstoreViewModel.cpp
...
BookstoreViewModel::BookstoreViewModel()
{
    m_bookSku = winrt::make<Bookstore::implementation::BookSku>(L"Atticus");
    m_bookSkus = winrt::single_threaded_observable_vector<Bookstore::BookSku>();
    m_bookSkus.Append(m_bookSku);
}

Bookstore::BookSku BookstoreViewModel::BookSku()
{
    return m_bookSku;
}

Windows::Foundation::Collections::IObservableVector<Bookstore::BookSku> BookstoreViewModel::BookSkus()
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

Теперь выполните сборку и запустите проект. Нажмите кнопку, чтобы запустить обработчик события **Click**. Мы увидели, что реализация **Append** вызывает событие, чтобы сообщить пользовательскому интерфейсу о том, что коллекция изменилась, а **ListBox** повторно запрашивает коллекцию, чтобы обновить свое собственное значение **Items**. Так же, как и раньше, меняется название одной из книг, и это изменение отражается как на кнопке, так и в списке.

## <a name="important-apis"></a>Важные API
* [IObservableVector&lt;T&gt;::VectorChanged](/uwp/api/windows.foundation.collections.iobservablevector-1.vectorchanged)
* [Шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)

## <a name="related-topics"></a>Связанные темы
* [Использование интерфейсов API с помощью C++/WinRT](consume-apis.md)
* [Создание интерфейсов API с помощью C++/WinRT](author-apis.md)
