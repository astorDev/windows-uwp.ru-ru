---
author: stevewhims
description: Свойство, которое может быть эффективно привязано к элементу управления XAML, называется *отслеживаемым*. В этом разделе показано, как реализовать и использовать отслеживаемое свойство и привязать к нему элемент управления XAML.
title: Элементы управления XAML; привязка к свойству C++/WinRT
ms.author: stwhi
ms.date: 05/07/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, XAML, управление, привязка, свойство
ms.localizationpriority: medium
ms.openlocfilehash: 367bf5d5d554bd094ce3d5b726b818c8c388d398
ms.sourcegitcommit: f2f4820dd2026f1b47a2b1bf2bc89d7220a79c1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "2787161"
---
# <a name="xaml-controls-bind-to-a-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt-property"></a>Элементы управления XAML; привязка к свойству [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)
Свойство, которое может быть эффективно привязано к элементу управления XAML, называется *отслеживаемым*. Эта идея основана на шаблоне проектирования программного обеспечения, известном как *шаблон наблюдателя *. В этом разделе показано, как реализовывать отслеживаемые свойства в C++/WinRT и привязывать к ним элементы управления XAML.

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="what-does-observable-mean-for-a-property"></a>Что означает понятие *отслеживаемое* для свойства?
Предположим, что класс среды выполнения с именем **BookSku** имеет свойство с именем **Title**. Если **BookSku** вызывает событие [**INotifyPropertyChanged::PropertyChanged**](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged) событие всякий раз, когда меняется значение **Title**, то **Title** является отслеживаемым свойством. Поведение **BookSku** (вызов или не вызов события) определяет, является ли какое-либо из его свойств, отслеживаемым, и если да, то какое.

Текстовый элемент или элемент управления XAML может привязываться к этим событиям и обрабатывать их путем получения обновленных значений и последующего самообновления для показа новых значений.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

## <a name="create-a-blank-app-bookstore"></a>Создайте пустое приложение (Bookstore)
Начните с создания нового проекта в Microsoft Visual Studio. Создайте проект **Пустое приложение Visual C++ (C++/WinRT)** и назовите его *Bookstore*.

Мы создадим нового класс, представляющий книгу, которая имеет отслеживаемое свойство названия. Класс создается и используется в рамках одной и той же единицы компиляции. Однако мы хотим иметь возможность привязывать этот класс из XAML, поэтому это будет класс среды выполнения. Кроме того, мы применим C++/WinRT для его создания и использования.

Первый шаг при создании нового класса среды выполнения — добавление в проект нового элемента **файла Midl (.idl)**. Назовите его `BookSku.idl`. Удалите содержимое `BookSku.idl` по умолчанию вставьте в это объявление класса среды выполнения.

```idl
// BookSku.idl
namespace Bookstore
{
    runtimeclass BookSku : Windows.UI.Xaml.DependencyObject, Windows.UI.Xaml.Data.INotifyPropertyChanged
    {
        String Title;
    }
}
```

> [!IMPORTANT]
> Приложения для передачи [Комплект сертификации приложений для Windows](../debug-test-perf/windows-app-certification-kit.md) тестов используется хранилище Microsoft проверить отправленные данные и таким образом для успешного быть ingested в хранилище данных Майкрософт, первичный базовый класс для каждого класса среды выполнения *объявляются в приложение* должен иметь тип, поступающих в пространстве имен Windows.*.

Для выполнения этого требования, классы модели представления должны быть производными от [**Windows.UI.Xaml.DependencyObject**](/uwp/api/windows.ui.xaml.dependencyobject). Кроме того, объявите привязываемый базовый класс, производный от **DependencyObject**и наследуйте модели представления от него. Модели данных можно объявить в качестве структур C++; их не нужно объявлять в MIDL (при условии, что они используются только из ваших моделей представления, и к ним не осуществляется непосредственная привязка XAML; в случае чего они, возможно, по определению не являлись бы моделями представлений).

Сохраните файл и выполните сборку проекта. Во время сборки запускается инструмент `midl.exe` для создания файла метаданных среды выполнения Windows (`\Bookstore\Debug\Bookstore\Unmerged\BookSku.winmd`), описывающего класс среды выполнения. Затем запускается средство `cppwinrt.exe` для создания файлов исходного кода для поддержки создания и использования вашего класса среды выполнения. Эти файлы включают заглушки для начала реализации класса среды выполнения **BookSku**, объявленного в вашем IDL. Это заглушки `\Bookstore\Bookstore\Generated Files\sources\BookSku.h` и `BookSku.cpp`.

Скопируйте файлы заглушек `BookSku.h` и `BookSku.cpp` из `\Bookstore\Bookstore\Generated Files\sources\` в папку проекта `\Bookstore\Bookstore\`. Убедитесь, что в **Обозревателе решений** включена функция **Показать все файлы**. Щелкните правой кнопкой мыши скопированные файлы заглушек и выберите **Включить в проект**.

## <a name="implement-booksku"></a>Реализуйте **BookSku**
Теперь давайте откроем `\Bookstore\Bookstore\BookSku.h` и `BookSku.cpp` и реализуем класс среды выполнения. В `BookSku.h` добавьте конструктор, который принимает [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring), частный член для хранения строки названия и еще один — для события, которое мы будем вызывать при изменении названия. После их добавления ваш файл `BookSku.h` будет выглядеть следующим образом.

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

        hstring Title();
        void Title(winrt::hstring const& value);
        event_token PropertyChanged(Windows::UI::Xaml::Data::PropertyChangedEventHandler const& value);
        void PropertyChanged(winrt::event_token const& token);
    
    private:
        hstring m_title;
        event<Windows::UI::Xaml::Data::PropertyChangedEventHandler> m_propertyChanged;
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
    hstring BookSku::Title()
    {
        return m_title;
    }

    BookSku::BookSku(winrt::hstring const& title)
    {
        Title(title);
    }

    void BookSku::Title(winrt::hstring const& value)
    {
        if (m_title != value)
        {
            m_title = value;
            m_propertyChanged(*this, Windows::UI::Xaml::Data::PropertyChangedEventArgs{ L"Title" });
        }
    }

    event_token BookSku::PropertyChanged(Windows::UI::Xaml::Data::PropertyChangedEventHandler const& handler)
    {
        return m_propertyChanged.add(handler);
    }

    void BookSku::PropertyChanged(winrt::event_token const& token)
    {
        m_propertyChanged.remove(token);
    }
}
```

В функции мутатора **Title** мы проверяем, устанавливается ли другое значение, и если да, обновляем название, а также вызываем событие [**INotifyPropertyChanged::PropertyChanged**](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged) с аргументом, равным имени свойства, которое было изменено. Это делается для того, чтобы пользовательскому интерфейсу было известно, значение какого свойства следует запрашивать повторно.

## <a name="declare-and-implement-bookstoreviewmodel"></a>Объявите и реализуйте **BookstoreViewModel**
Наша главная страница XAML будет привязываться к модели главного представления. И модель этого представления будет иметь несколько свойств, включая свойство типа **BookSku**. На этом шаге мы объявим и реализуем класс среды выполнения модели главного представления.

Добавьте новый элемент **файла Midl (.idl)** с именем `BookstoreViewModel.idl`.

```idl
// BookstoreViewModel.idl
import "BookSku.idl";

namespace Bookstore
{
    runtimeclass BookstoreViewModel : Windows.UI.Xaml.DependencyObject
    {
        BookSku BookSku{ get; };
    }
}
```

Сохраните и выполните сборку. Скопируйте `BookstoreViewModel.h` и `BookstoreViewModel.cpp` из `Generated Files` в папку проекта и добавьте их в проект. Откройте эти файлы и реализуйте класс среды выполнения следующим образом.

```cppwinrt
// BookstoreViewModel.h
#pragma once

#include "BookstoreViewModel.g.h"
#include "BookSku.h"

namespace winrt::Bookstore::implementation
{
    struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
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
        m_bookSku = make<Bookstore::implementation::BookSku>(L"Atticus");
    }

    Bookstore::BookSku BookstoreViewModel::BookSku()
    {
        return m_bookSku;
    }
}
```

> [!NOTE]
> Тип `m_bookSku` является проецируемым типом (**winrt::Bookstore::BookSku**), а параметр шаблона, который используется с **make**, является типом реализации (**winrt::Bookstore::implementation::BookSku**) . Даже в этом случае **make** возвращает экземпляр проецируемого типа.

## <a name="add-a-property-of-type-bookstoreviewmodel-to-mainpage"></a>Добавьте свойство типа **BookstoreViewModel** в **MainPage**
Откройте `MainPage.idl`, где объявляется класс среды выполнения, представляющий собой главную страницу пользовательского интерфейса. Добавьте оператор import для импорта `BookstoreViewModel.idl` и свойство только для чтения с именем MainViewModel типа **BookstoreViewModel**. Также удалите свойство **MyProperty** . Кроме директивы **импорта** в следующем списке.

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

Сохраните файл. Не выполняется построение проекта до завершения в данный момент, но теперь построение — это полезно что следует сделать так, как повторно файлы исходного кода, в которых реализуется класс **MainPage** среды выполнения (`\Bookstore\Bookstore\Generated Files\sources\MainPage.h` и `MainPage.cpp`). Поэтому отключать построить сейчас. Ошибка построения можно ожидать на этом этапе **'MainViewModel': не является членом «winrt::Bookstore::implementation::MainPage»**.

Если опустить include из `BookstoreViewModel.idl` (список `MainPage.idl` выше), а затем вы увидите ошибки **ожидается \ < около «MainViewModel»**. Другой совет – убедитесь в том, что все типы остается в то же пространство имен: пространство имен, которая отображается в списке кода.

Чтобы устранить ошибку, мы рассчитываем, теперь необходимо скопировать заглушки доступа к данным для свойства **MainViewModel** из него созданные файлы и в `\Bookstore\Bookstore\MainPage.h` и `MainPage.cpp`.

Добавьте частный член для хранения модели представления в `\Bookstore\Bookstore\MainPage.h`. Обратите внимание, что функция доступа свойства (и член m_mainViewModel) реализуется относительно **Bookstore::BookstoreViewModel**, который является проецируемым типом. Тип реализации находится в том же проекте (единице компиляции), поэтому мы создаем m_mainViewModel через перегрузку конструктора, которая принимает `nullptr_t`. Также удалите свойство **MyProperty** .

```cppwinrt
// MainPage.h
...
namespace winrt::Bookstore::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
        MainPage();

        Bookstore::BookstoreViewModel MainViewModel();

        void ClickHandler(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const& args);

    private:
        Bookstore::BookstoreViewModel m_mainViewModel{ nullptr };
    };
}
...
```

В `\Bookstore\Bookstore\MainPage.cpp` включите `BookstoreViewModel.h`, который объявляет тип реализации. Вызовите [**winrt::make**](/uwp/cpp-ref-for-winrt/make) (с типом реализации) для назначения нового экземпляра проецируемого типа для m_mainViewModel. Назначьте начальное значение для названия книги. Реализуйте метод доступа для свойства MainViewModel. И, наконец, обновите название книги в обработчике событий кнопки. Также удалите свойство **MyProperty** .

```cppwinrt
// MainPage.cpp
#include "pch.h"
#include "MainPage.h"
#include "BookstoreViewModel.h"

using namespace winrt;
using namespace Windows::UI::Xaml;

namespace winrt::Bookstore::implementation
{
    MainPage::MainPage()
    {
        m_mainViewModel = make<Bookstore::implementation::BookstoreViewModel>();
        InitializeComponent();
    }

    void MainPage::ClickHandler(IInspectable const&, RoutedEventArgs const&)
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
Откройте файл `MainPage.xaml`, который содержит разметку XAML для главной страницы пользовательского интерфейса. Удалите имя с кнопки и измените значение ее свойства **Content** с литерала на выражение привязки. Обратите внимание на свойство `Mode=OneWay` в выражении привязки (односторонняя из модели представления к пользовательскому интерфейсу). Без этого свойства пользовательский интерфейс не будет реагировать на события изменения свойств.

```xaml
<Button Click="ClickHandler" Content="{x:Bind MainViewModel.BookSku.Title, Mode=OneWay}"/>
```

Выполните сборку и запуск проекта. Нажмите кнопку, чтобы запустить обработчик события **Click**. Этот обработчик вызывает функция мутатора названия книги; этот мутатор вызывает событие, чтобы сообщить пользовательскому интерфейсу о том, что свойство **Title** изменилось; а кнопка повторно запрашивает значение этого свойства, чтобы обновить свое собственное значение **Content**.

## <a name="using-the-binding-markup-extension-with-cwinrt"></a>Использование расширения разметки {Binding} с C + +/ WinRT
В настоящее время выпущенной версии C + +/ WinRT, чтобы иметь возможность использовать расширения разметки {Binding}, необходимые для реализации интерфейсов [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider) и [ICustomProperty](/uwp/api/windows.ui.xaml.data.icustomproperty) .

## <a name="important-apis"></a>Важные API
* [INotifyPropertyChanged::PropertyChanged](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged)
* [Шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)

## <a name="related-topics"></a>Статьи по теме
* [Использование API-интерфейсов в C++/WinRT](consume-apis.md)
* [Создание API-интерфейсов в C++/WinRT](author-apis.md)
