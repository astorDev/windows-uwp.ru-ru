---
description: Свойство, которое может быть эффективно привязано к элементу управления XAML, называется *отслеживаемым*. В этом разделе показано, как реализовать и использовать отслеживаемое свойство и привязать к нему элемент управления XAML.
title: Элементы управления XAML; привязка к свойству C++/WinRT
ms.date: 08/21/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, XAML, управление, привязка, свойство
ms.localizationpriority: medium
ms.openlocfilehash: 4033327fa51b0801583a518a0dea055f59e57fc8
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57616629"
---
# <a name="xaml-controls-bind-to-a-cwinrt-property"></a>Элементы управления XAML; привязка к свойству C++/WinRT
Свойство, которое может быть эффективно привязано к элементу управления XAML, называется *отслеживаемым*. Эта идея основана на шаблоне проектирования программного обеспечения, известном как *шаблон наблюдателя* . В этом разделе показано, как реализовать наблюдаемые свойства в [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)и как привязать элементы управления XAML к ним.

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="what-does-observable-mean-for-a-property"></a>Что означает понятие *отслеживаемое* для свойства?
Предположим, что класс среды выполнения с именем **BookSku** имеет свойство с именем **Title**. Если **BookSku** вызывает событие [**INotifyPropertyChanged::PropertyChanged**](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged) событие всякий раз, когда меняется значение **Title**, то **Title** является отслеживаемым свойством. Поведение **BookSku** (вызов или не вызов события) определяет, является ли какое-либо из его свойств, отслеживаемым, и если да, то какое.

Текстовый элемент или элемент управления XAML может привязываться к этим событиям и обрабатывать их путем получения обновленных значений и последующего самообновления для показа новых значений.

> [!NOTE]
> Сведения об установке и использовании C + +/ WinRT Visual Studio Extension (VSIX) (который поддерживает шаблон проекта) см. в разделе [поддержка Visual Studio для C + +/ WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

## <a name="create-a-blank-app-bookstore"></a>Создайте пустое приложение (Bookstore)
Начните с создания нового проекта в Microsoft Visual Studio. Создание **Visual C++** > **универсальной Windows** > **пустое приложение (C + +/ WinRT)** проект и назовите его *Bookstore*.

Мы создадим нового класс, представляющий книгу, которая имеет отслеживаемое свойство названия. Класс создается и используется в рамках одной и той же единицы компиляции. Однако мы хотим иметь возможность привязывать этот класс из XAML, поэтому это будет класс среды выполнения. Кроме того, мы применим C++/WinRT для его создания и использования.

Первый шаг при создании нового класса среды выполнения — добавление в проект нового элемента **файла Midl (.idl)**. Назовите его `BookSku.idl`. Удалите содержимое `BookSku.idl` по умолчанию вставьте в это объявление класса среды выполнения.

```idl
// BookSku.idl
namespace Bookstore
{
    runtimeclass BookSku : Windows.UI.Xaml.Data.INotifyPropertyChanged
    {
        String Title;
    }
}
```

> [!NOTE]
> Классы модели представления&mdash;на самом деле, любой класс среды выполнения, который объявляется в приложении&mdash;требуется является производным от базового класса. **BookSku** класс, объявленный над является примером. Он реализует интерфейс, но оно не является производным от базового класса.
>
> Любой класс среды выполнения, который объявляется в приложении, *does* являются производными от базового класса называется *составную* класса. А накладываются ограничения вокруг составных классов. Для приложения для передачи [комплект сертификации приложений для Windows](../debug-test-perf/windows-app-certification-kit.md) тестов, используемых Visual Studio и Microsoft Store для проверки отправки (и, следовательно, для приложения, чтобы быть успешно принимаются в Microsoft Store), составляемый класс в конечном счете должны наследоваться от базового класса Windows. Это означает, что класс самого корня иерархии наследования должен быть типом, полученные в пространстве имен Windows.*. Если вам нужно создать класс среды выполнения, производный от базового класса&mdash;пример, как реализовать **BindableBase** класс для всех моделей представления для наследования от&mdash;может быть производным от [ **Windows.UI.Xaml.DependencyObject**](/uwp/api/windows.ui.xaml.dependencyobject).
>
> Модель представления — это абстракция представления, и поэтому привязаны непосредственно к представлению (разметка XAML). Модель данных — это абстрактное представление данных, и он только из модели представления и вы не привязаны непосредственно к XAML. Таким образом можно объявить к моделям данных, не как классы среды выполнения, а также как C++ структур или классов. Они не должны быть объявлены в MIDL, и вы можете использовать любой иерархии наследования, вам нравится.

Сохраните файл и выполните сборку проекта. Во время сборки запускается инструмент `midl.exe` для создания файла метаданных среды выполнения Windows (`\Bookstore\Debug\Bookstore\Unmerged\BookSku.winmd`), описывающего класс среды выполнения. Затем запускается средство `cppwinrt.exe` для создания файлов исходного кода для поддержки создания и использования вашего класса среды выполнения. Эти файлы включают заглушки для начала реализации класса среды выполнения **BookSku**, объявленного в вашем IDL. Это заглушки `\Bookstore\Bookstore\Generated Files\sources\BookSku.h` и `BookSku.cpp`.

Щелкните правой кнопкой мыши узел проекта и нажмите кнопку **открыть папку в проводнике**. Это открывает папку проекта в проводнике. Существует, скопируйте файлы заглушки `BookSku.h` и `BookSku.cpp` из `\Bookstore\Bookstore\Generated Files\sources\` папку и в папку проекта, который является `\Bookstore\Bookstore\`. В **Обозревателе решений**убедитесь, что функция **Показать все файлы** включена. Щелкните правой кнопкой мыши скопированные файлы заглушек и выберите **Включить в проект**.

## <a name="implement-booksku"></a>Реализуйте **BookSku**
Теперь давайте откроем `\Bookstore\Bookstore\BookSku.h` и `BookSku.cpp` и реализуем класс среды выполнения. В `BookSku.h` добавьте конструктор, который принимает [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring), частный член для хранения строки названия и еще один — для события, которое мы будем вызывать при изменении названия. После внесения этих изменений в `BookSku.h` выглядит следующим образом.

```cppwinrt
// BookSku.h
#pragma once

#include "BookSku.g.h"

namespace winrt::Bookstore::implementation
{
    struct BookSku : BookSkuT<BookSku>
    {
        BookSku() = delete;
        BookSku(winrt::hstring const& title);

        winrt::hstring Title();
        void Title(winrt::hstring const& value);
        winrt::event_token PropertyChanged(Windows::UI::Xaml::Data::PropertyChangedEventHandler const& value);
        void PropertyChanged(winrt::event_token const& token);
    
    private:
        winrt::hstring m_title;
        winrt::event<Windows::UI::Xaml::Data::PropertyChangedEventHandler> m_propertyChanged;
    };
}
```

В `BookSku.cpp` реализуйте функции следующим образом.

```cppwinrt
// BookSku.cpp
#include "pch.h"
#include "BookSku.h"

namespace winrt::Bookstore::implementation
{
    BookSku::BookSku(winrt::hstring const& title) : m_title{ title }
    {
    }

    winrt::hstring BookSku::Title()
    {
        return m_title;
    }

    void BookSku::Title(winrt::hstring const& value)
    {
        if (m_title != value)
        {
            m_title = value;
            m_propertyChanged(*this, Windows::UI::Xaml::Data::PropertyChangedEventArgs{ L"Title" });
        }
    }

    winrt::event_token BookSku::PropertyChanged(Windows::UI::Xaml::Data::PropertyChangedEventHandler const& handler)
    {
        return m_propertyChanged.add(handler);
    }

    void BookSku::PropertyChanged(winrt::event_token const& token)
    {
        m_propertyChanged.remove(token);
    }
}
```

В **Title** мутатора функции, мы проверяем, установлено ли значение которое отличается от текущего значения. И, таким образом, мы обновляем заголовок и также вызывать [ **INotifyPropertyChanged::PropertyChanged** ](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged) событий с аргументом, равным имя измененного свойства. Это делается для того, чтобы пользовательскому интерфейсу было известно, значение какого свойства следует запрашивать повторно.

## <a name="declare-and-implement-bookstoreviewmodel"></a>Объявите и реализуйте **BookstoreViewModel**
Наша главная страница XAML будет привязываться к модели главного представления. И модель этого представления будет иметь несколько свойств, включая свойство типа **BookSku**. На этом шаге мы объявим и реализуем класс среды выполнения модели главного представления.

Добавьте новый элемент **файла Midl (.idl)** с именем `BookstoreViewModel.idl`.

```idl
// BookstoreViewModel.idl
import "BookSku.idl";

namespace Bookstore
{
    runtimeclass BookstoreViewModel
    {
        BookSku BookSku{ get; };
    }
}
```

Сохраните и выполните сборку. Скопируйте `BookstoreViewModel.h` и `BookstoreViewModel.cpp` из `Generated Files` в папку проекта и добавьте их в проект. Откройте эти файлы и реализовать класс среды выполнения, как показано ниже. Обратите внимание как в `BookstoreViewModel.h`, мы добавим `BookSku.h`, который объявляет тип реализации (**winrt::Bookstore::implementation::BookSku**). И мы восстанавливаете конструктор по умолчанию, удалив `= delete`.

```cppwinrt
// BookstoreViewModel.h
#pragma once

#include "BookstoreViewModel.g.h"
#include "BookSku.h"

namespace winrt::Bookstore::implementation
{
    struct BookstoreViewModel final : BookstoreViewModelT<BookstoreViewModel>
    {
        BookstoreViewModel();

        Bookstore::BookSku BookSku();

    private:
        Bookstore::BookSku m_bookSku{ nullptr };
    };
}
```

```cppwinrt
// BookstoreViewModel.cpp
#include "pch.h"
#include "BookstoreViewModel.h"

namespace winrt::Bookstore::implementation
{
    BookstoreViewModel::BookstoreViewModel()
    {
        m_bookSku = winrt::make<Bookstore::implementation::BookSku>(L"Atticus");
    }

    Bookstore::BookSku BookstoreViewModel::BookSku()
    {
        return m_bookSku;
    }
}
```

> [!NOTE]
> Тип `m_bookSku` проецируемых тип (**winrt::Bookstore::BookSku**) и параметр шаблона, который используется с [ **winrt::make** ](/uwp/cpp-ref-for-winrt/make) — Тип реализации (**winrt::Bookstore::implementation::BookSku**). Даже в этом случае **make** возвращает экземпляр проецируемого типа.

## <a name="add-a-property-of-type-bookstoreviewmodel-to-mainpage"></a>Добавьте свойство типа **BookstoreViewModel** в **MainPage**
Откройте `MainPage.idl`, где объявляется класс среды выполнения, представляющий собой главную страницу пользовательского интерфейса. Добавьте оператор import для импорта `BookstoreViewModel.idl` и свойство только для чтения с именем MainViewModel типа **BookstoreViewModel**. Также удалите **MyProperty** свойство. Также Обратите внимание, `import` директив в списке ниже.

```idl
// MainPage.idl
import "BookstoreViewModel.idl";

namespace Bookstore
{
    runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
    {
        MainPage();
        BookstoreViewModel MainViewModel{ get; };
    }
}
```

Сохраните файл. Сборка проекта не до конца на данный момент, но создание теперь является полезным, что необходимо сделать, так как он повторно создает файлы исходного кода, в котором **MainPage** реализуется класс среды выполнения (`\Bookstore\Bookstore\Generated Files\sources\MainPage.h` и `MainPage.cpp`). Так что действуйте и построить сейчас. Ошибка построения, можно ожидать на этом этапе **«MainViewModel»: не является членом «winrt::Bookstore::implementation::MainPage»**.

Если опустить include из `BookstoreViewModel.idl` (см. в разделе перечень `MainPage.idl` выше), то появится ошибка **ожидается \< рядом с «MainViewModel»**. Другой совет – убедитесь в том, что все типы остается в том же пространстве имен: пространство имен, которое показано в распечатках кода.

Чтобы устранить эту ошибку, мы ожидаем увидеть, теперь необходимо скопировать заглушки метода доступа для **MainViewModel** свойство из созданных файлов (`\Bookstore\Bookstore\Generated Files\sources\MainPage.h` и `MainPage.cpp`) и в `\Bookstore\Bookstore\MainPage.h` и `MainPage.cpp`.

В `\Bookstore\Bookstore\MainPage.h`, включают `BookstoreViewModel.h`, который объявляет тип реализации (**winrt::Bookstore::implementation::BookstoreViewModel**). Добавьте закрытый элемент для хранения модели представления. Обратите внимание, что функция доступа свойства (и член m_mainViewModel) реализуется относительно **Bookstore::BookstoreViewModel**, который является проецируемым типом. Тип реализации находится в один и тот же проект (единицы компиляции) приложения, поэтому мы создаем m_mainViewModel через перегрузку конструктора, который принимает `nullptr_t`. Также удалите **MyProperty** свойство.

```cppwinrt
// MainPage.h
...
#include "BookstoreViewModel.h"
...
namespace winrt::Bookstore::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
        MainPage();

        Bookstore::BookstoreViewModel MainViewModel();

        void ClickHandler(Windows::Foundation::IInspectable const&, Windows::UI::Xaml::RoutedEventArgs const&);

    private:
        Bookstore::BookstoreViewModel m_mainViewModel{ nullptr };
    };
}
...
```

В `\Bookstore\Bookstore\MainPage.cpp`, вызовите [ **winrt::make** ](/uwp/cpp-ref-for-winrt/make) (с типом реализации) чтобы назначить новый экземпляр типа, проецируемых m_mainViewModel. Назначьте начальное значение для названия книги. Реализуйте метод доступа для свойства MainViewModel. И, наконец, обновите название книги в обработчике событий кнопки. Также удалите **MyProperty** свойство.

```cppwinrt
// MainPage.cpp
#include "pch.h"
#include "MainPage.h"

using namespace winrt;
using namespace Windows::UI::Xaml;

namespace winrt::Bookstore::implementation
{
    MainPage::MainPage()
    {
        m_mainViewModel = winrt::make<Bookstore::implementation::BookstoreViewModel>();
        InitializeComponent();
    }

    void MainPage::ClickHandler(Windows::Foundation::IInspectable const& /* sender */, Windows::UI::Xaml::RoutedEventArgs const& /* args */)
    {
        MainViewModel().BookSku().Title(L"To Kill a Mockingbird");
    }

    Bookstore::BookstoreViewModel MainPage::MainViewModel()
    {
        return m_mainViewModel;
    }
}
```

## <a name="bind-the-button-to-the-title-property"></a>Привяжите кнопку к свойству **Title**
Откройте файл `MainPage.xaml`, который содержит разметку XAML для главной страницы пользовательского интерфейса. Как показано в примере ниже, удалите имя с помощью кнопки и измените его **содержимого** значение свойства из литерала в выражении привязки. Обратите внимание на свойство `Mode=OneWay` в выражении привязки (односторонняя из модели представления к пользовательскому интерфейсу). Без этого свойства пользовательский интерфейс не будет реагировать на события изменения свойств.

```xaml
<Button Click="ClickHandler" Content="{x:Bind MainViewModel.BookSku.Title, Mode=OneWay}"/>
```

Выполните сборку и запуск проекта. Нажмите кнопку, чтобы запустить обработчик события **Click**. Этот обработчик вызывает функция мутатора названия книги; этот мутатор вызывает событие, чтобы сообщить пользовательскому интерфейсу о том, что свойство **Title** изменилось; а кнопка повторно запрашивает значение этого свойства, чтобы обновить свое собственное значение **Content**.

## <a name="using-the-binding-markup-extension-with-cwinrt"></a>С помощью расширения разметки {Binding} с использованием C + +/ WinRT
В настоящее время окончательной версии C + +/ WinRT, чтобы иметь возможность использовать расширение разметки {Binding}, необходимо реализовать [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider) и [ICustomProperty](/uwp/api/windows.ui.xaml.data.icustomproperty) интерфейсов.

## <a name="important-apis"></a>Важные API
* [INotifyPropertyChanged::PropertyChanged](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged)
* [Шаблон функции WinRT::make](/uwp/cpp-ref-for-winrt/make)

## <a name="related-topics"></a>Статьи по теме
* [Использование интерфейсов API с помощью C++/WinRT](consume-apis.md)
* [Создание интерфейсов API с помощью C++/WinRT](author-apis.md)
