---
author: stevewhims
description: Свойство, которое может быть эффективно привязано к элементу управления XAML, называется *отслеживаемым*. В этом разделе показано, как реализовать и использовать отслеживаемое свойство и привязать к нему элемент управления XAML.
title: Элементы управления XAML; привязка к свойству C++/WinRT
ms.author: stwhi
ms.date: 08/21/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, XAML, управление, привязка, свойство
ms.localizationpriority: medium
ms.openlocfilehash: 6b7c20e0e6cf56afa7e2193739401bf49e0403a2
ms.sourcegitcommit: bdc40b08cbcd46fc379feeda3c63204290e055af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "6146781"
---
# <a name="xaml-controls-bind-to-a-cwinrt-property"></a>Элементы управления XAML; привязка к свойству C++/WinRT
Свойство, которое может быть эффективно привязано к элементу управления XAML, называется *отслеживаемым*. Эта идея основана на шаблоне проектирования программного обеспечения, известном как *шаблон наблюдателя *. В этом разделе показано, как реализовать наблюдаемым свойствам в [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)и привязать к ним элементы управления XAML.

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md) и [Создание API-интерфейсов в C++/WinRT ](author-apis.md).

## <a name="what-does-observable-mean-for-a-property"></a>Что означает понятие *отслеживаемое* для свойства?
Предположим, что класс среды выполнения с именем **BookSku** имеет свойство с именем **Title**. Если **BookSku** вызывает событие [**INotifyPropertyChanged::PropertyChanged**](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged) событие всякий раз, когда меняется значение **Title**, то **Title** является отслеживаемым свойством. Поведение **BookSku** (вызов или не вызов события) определяет, является ли какое-либо из его свойств, отслеживаемым, и если да, то какое.

Текстовый элемент или элемент управления XAML может привязываться к этим событиям и обрабатывать их путем получения обновленных значений и последующего самообновления для показа новых значений.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

## <a name="create-a-blank-app-bookstore"></a>Создайте пустое приложение (Bookstore)
Начните с создания нового проекта в Microsoft Visual Studio. Создание **Visual C++** > **Универсальные** > **пустое приложение (C + +/ WinRT)** проект и назовите его *Bookstore*.

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
> Классы модели представления&mdash;на самом деле, любого класса среды выполнения, которые были объявлены в приложении&mdash;требуется является производным от базового класса. Класс **BookSku** объявили выше приведен пример этого. Он реализует интерфейс, но не является производным от базового класса.
>
> Любого класса среды выполнения, которые были объявлены в приложении, что *делает* являются производными от базового класса называется *составные* класса. Также существуют ограничения вокруг составные классы. Приложение могло пройти тесты [Комплект сертификации приложений для Windows](../debug-test-perf/windows-app-certification-kit.md) , используется в Visual Studio и в магазине Майкрософт для проверки отправки (и, следовательно, для приложения быть успешно добавлено в Microsoft Store), необходимо составные класса наследоваться от базового класса Windows. Это означает, что класс очень корне иерархии наследования должен являться типом из пространства имен Windows.*. Если вам нужно создать класс среды выполнения от базового класса&mdash;например, чтобы реализовать класс **BindableBase** для всех ваших моделей представления наследовать от&mdash;, а затем можно использовать наследование от [**Windows.UI.Xaml.DependencyObject**](/uwp/api/windows.ui.xaml.dependencyobject).
>
> Модель представления является абстракцией представления, и поэтому привязаны непосредственно к представлению (разметки XAML). Модель данных — это абстракция данных, и оно было использоваться только из ваших моделей представления и не привязаны непосредственно к XAML. Таким образом вы можете объявить модели данных, не как классы среды выполнения, а в качестве структур C++ или классов. Их не нужно объявлять в MIDL, и вы будете использовать независимо от иерархии наследования, вы хотели.

Сохраните файл и выполните сборку проекта. Во время сборки запускается инструмент `midl.exe` для создания файла метаданных среды выполнения Windows (`\Bookstore\Debug\Bookstore\Unmerged\BookSku.winmd`), описывающего класс среды выполнения. Затем запускается средство `cppwinrt.exe` для создания файлов исходного кода для поддержки создания и использования вашего класса среды выполнения. Эти файлы включают заглушки для начала реализации класса среды выполнения **BookSku**, объявленного в вашем IDL. Это заглушки `\Bookstore\Bookstore\Generated Files\sources\BookSku.h` и `BookSku.cpp`.

Скопируйте файлы заглушек `BookSku.h` и `BookSku.cpp` из `\Bookstore\Bookstore\Generated Files\sources\` в папку проекта `\Bookstore\Bookstore\`. Убедитесь, что в **Обозревателе решений** включена функция **Показать все файлы**. Щелкните правой кнопкой мыши скопированные файлы заглушек и выберите **Включить в проект**.

## <a name="implement-booksku"></a>Реализуйте **BookSku**
Теперь давайте откроем `\Bookstore\Bookstore\BookSku.h` и `BookSku.cpp` и реализуем класс среды выполнения. В `BookSku.h` добавьте конструктор, который принимает [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring), частный член для хранения строки названия и еще один — для события, которое мы будем вызывать при изменении названия. После выполнения этих изменений вашего `BookSku.h` будет выглядеть следующим образом.

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

Функция мутатора **названия** мы проверяем, является ли значение устанавливается, отличающийся от текущего значения. И, если это так, мы обновляем название, а также вызывать событие [**INotifyPropertyChanged::PropertyChanged**](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged) с аргументом, равным имени свойства, которое было изменено. Это делается для того, чтобы пользовательскому интерфейсу было известно, значение какого свойства следует запрашивать повторно.

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

Сохраните и выполните сборку. Скопируйте `BookstoreViewModel.h` и `BookstoreViewModel.cpp` из `Generated Files` в папку проекта и добавьте их в проект. Откройте эти файлы и реализуем класс среды выполнения, как показано ниже. Обратите внимание как в `BookstoreViewModel.h`, мы включаете `BookSku.h`, который объявляет тип реализации (**winrt::Bookstore::implementation::BookSku**). И мы восстановление конструктор по умолчанию, удаляя `= delete`.

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
> Тип `m_bookSku` является проецируемым типом (**WinRT::Bookstore:: booksku**), а параметр шаблона, который можно использовать с [**winrt::make**](/uwp/cpp-ref-for-winrt/make) является типом реализации (**winrt::Bookstore::implementation::BookSku**). Даже в этом случае **make** возвращает экземпляр проецируемого типа.

## <a name="add-a-property-of-type-bookstoreviewmodel-to-mainpage"></a>Добавьте свойство типа **BookstoreViewModel** в **MainPage**
Откройте `MainPage.idl`, где объявляется класс среды выполнения, представляющий собой главную страницу пользовательского интерфейса. Добавьте оператор import для импорта `BookstoreViewModel.idl` и свойство только для чтения с именем MainViewModel типа **BookstoreViewModel**. Также удалите свойство **MyProperty** . Также Обратите внимание, `import` директиву в описании ниже.

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

Сохраните файл. Проект не будет создан до завершения в данный момент, но теперь построение является полезным очередь, необходимо сделать обязательно, поскольку она заново создает файлы исходного кода, в которых реализован класс среды выполнения **MainPage** (`\Bookstore\Bookstore\Generated Files\sources\MainPage.h` и `MainPage.cpp`). Таким образом и перейти к построить сейчас. Ошибка сборки, которую можно собрать см. на этом этапе **«MainViewModel»: не является членом «winrt::Bookstore::implementation::MainPage»**.

Если вы опустите включения из `BookstoreViewModel.idl` (см. список `MainPage.idl` выше), отобразится ошибка **ожидает \ < вблизи «MainViewModel»**. Еще один совет — убедиться, оставьте все типы в пространстве имен: пространство имен, которое отображается в описаниях кода.

Чтобы устранить ошибки, мы ожидаем, что см. в разделе, теперь необходимо скопируйте заглушки доступа для свойства **MainViewModel** из созданных файлов (`\Bookstore\Bookstore\Generated Files\sources\MainPage.h` и `MainPage.cpp`) и в `\Bookstore\Bookstore\MainPage.h` и `MainPage.cpp`.

В `\Bookstore\Bookstore\MainPage.h`, включают `BookstoreViewModel.h`, который объявляет тип реализации (**winrt::Bookstore::implementation::BookstoreViewModel**). Добавьте частный член для хранения модели представления. Обратите внимание, что функция доступа свойства (и член m_mainViewModel) реализуется относительно **Bookstore::BookstoreViewModel**, который является проецируемым типом. Тип реализации находится в том же проекте (единице компиляции) как приложение, поэтому мы создаем m_mainViewModel через перегрузку конструктора, которая принимает `nullptr_t`. Также удалите свойство **MyProperty** .

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

В `\Bookstore\Bookstore\MainPage.cpp`, вызовите [**winrt::make**](/uwp/cpp-ref-for-winrt/make) (с типом реализации) для назначения нового экземпляра проецируемого типа для m_mainViewModel. Назначьте начальное значение для названия книги. Реализуйте метод доступа для свойства MainViewModel. И, наконец, обновите название книги в обработчике событий кнопки. Также удалите свойство **MyProperty** .

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
Откройте файл `MainPage.xaml`, который содержит разметку XAML для главной страницы пользовательского интерфейса. Как показано в следующем списке, удалите имя с кнопки и измените его **содержимое** значение свойства с литерала на выражение привязки. Обратите внимание на свойство `Mode=OneWay` в выражении привязки (односторонняя из модели представления к пользовательскому интерфейсу). Без этого свойства пользовательский интерфейс не будет реагировать на события изменения свойств.

```xaml
<Button Click="ClickHandler" Content="{x:Bind MainViewModel.BookSku.Title, Mode=OneWay}"/>
```

Выполните сборку и запуск проекта. Нажмите кнопку, чтобы запустить обработчик события **Click**. Этот обработчик вызывает функция мутатора названия книги; этот мутатор вызывает событие, чтобы сообщить пользовательскому интерфейсу о том, что свойство **Title** изменилось; а кнопка повторно запрашивает значение этого свойства, чтобы обновить свое собственное значение **Content**.

## <a name="using-the-binding-markup-extension-with-cwinrt"></a>С помощью расширения разметки {Binding} с помощью C + +/ WinRT
В настоящее время выпущенной версии C + +/ WinRT, чтобы иметь возможность использовать расширение разметки {Binding}, вам потребуется реализовать интерфейсы [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider) и [ICustomProperty](/uwp/api/windows.ui.xaml.data.icustomproperty) .

## <a name="important-apis"></a>Важные API
* [INotifyPropertyChanged::PropertyChanged](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged)
* [Шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)

## <a name="related-topics"></a>Статьи по теме
* [Использование API-интерфейсов в C++/WinRT](consume-apis.md)
* [Создание API-интерфейсов в C++/WinRT](author-apis.md)
