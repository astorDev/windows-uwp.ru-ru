---
description: Свойство, которое может быть эффективно привязано к элементу управления XAML, называется *отслеживаемым*. В этом разделе показано, как реализовать и использовать отслеживаемое свойство и привязать к нему элемент управления XAML.
title: Элементы управления XAML; привязка к свойству C++/WinRT
ms.date: 06/21/2019
ms.topic: article
keywords: windows 10, uwp, стандартный, c++, cpp, winrt, проекция, XAML, управление, привязка, свойство
ms.localizationpriority: medium
ms.openlocfilehash: 06934c1c3b23c244fb32ffa957cffb926ffd1bb0
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209199"
---
# <a name="xaml-controls-bind-to-a-cwinrt-property"></a>Элементы управления XAML; привязка к свойству C++/WinRT
Свойство, которое может быть эффективно привязано к элементу управления XAML, называется *отслеживаемым*. Эта идея основана на шаблоне проектирования программного обеспечения, известном как *шаблон наблюдателя*. В этом разделе показано, как реализовывать отслеживаемые свойства в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) и привязывать к ним элементы управления XAML (вводные сведения см. в разделе [Привязка данных](/windows/uwp/data-binding)).

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью C++/WinRT, описаны в разделах [Использование интерфейсов API с помощью C++/WinRT](consume-apis.md) и [Создание интерфейсов API с помощью C++/WinRT](author-apis.md).

## <a name="what-does-observable-mean-for-a-property"></a>Что означает понятие *отслеживаемое* для свойства?
Предположим, что класс среды выполнения **BookSku** имеет свойство **Title**. Если **BookSku** вызывает событие [**INotifyPropertyChanged::PropertyChanged**](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged) всякий раз, когда меняется значение **Title**, то **Title** является отслеживаемым свойством. Поведение **BookSku** (вызов или не вызов события) определяет, является ли какое-либо из его свойств отслеживаемым, и если да, то какое.

Текстовый элемент или элемент управления XAML может привязываться к этим событиям и обрабатывать их путем получения обновленных значений и последующего самообновления для показа новых значений.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки) см. в разделе о [поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

## <a name="create-a-blank-app-bookstore"></a>Создание пустого приложения (Bookstore)
Начните с создания нового проекта в Microsoft Visual Studio. Создайте проект **Пустое приложение (C++/WinRT)** и назовите его *Bookstore*.

Мы создадим новый класс, представляющий книгу, которая имеет отслеживаемое свойство названия. Класс создается и используется в рамках одной и той же единицы компиляции. Однако мы хотим иметь возможность привязывать этот класс из XAML, поэтому это будет класс среды выполнения. Кроме того, мы применим C++/WinRT для его создания и использования.

Первый шаг при создании нового класса среды выполнения — добавление в проект нового элемента **Файл Midl (.idl)** . Назовите его `BookSku.idl`. Удалите содержимое `BookSku.idl` по умолчанию вставьте в него это объявление класса среды выполнения.

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
> Классы модели представления (фактически, как и любой класс среды выполнения, который объявляется в приложении) не обязательно должны быть производными от базового класса. Примером этого является класс **BookSku**, объявленный выше. Он реализует интерфейс, но он не является производным от базового класса.
>
> Любой класс среды выполнения, объявленный в приложении, который *является* производным от базового класса, называется *составным*. На составные классы накладываются определенные ограничения. Чтобы приложение прошло тесты [комплекта сертификации приложений для Windows](../debug-test-perf/windows-app-certification-kit.md), используемые Visual Studio и Microsoft Store для проверки отправляемого кода (и, следовательно, чтобы оно было успешно принято в Microsoft Store), составляемый класс в конечном счете должен быть производным от базового класса Windows. Это означает, что класс с самого корня иерархии наследования должен иметь тип, полученный в пространстве имен Windows.*. Если вам нужно создать класс среды выполнения, производный от базового класса (например, чтобы реализовать класс **BindableBase** для всех моделей представления для создания производных объектов), то он может быть производным от [**Windows.UI.Xaml.DependencyObject**](/uwp/api/windows.ui.xaml.dependencyobject).
>
> Модель представления — это абстракция представления, поэтому она привязана непосредственно к представлению (разметке XAML). Модель данных — это абстракция данных, которая используется только из моделей представления и не привязывается непосредственно к XAML. Поэтому вы можете объявлять модели данных не как классы среды выполнения, а как структуры или классы C++. Их не нужно объявлять в MIDL, и вы можете использовать любую иерархию наследования.

Сохраните файл и выполните сборку проекта. Во время сборки запускается инструмент `midl.exe` для создания файла метаданных среды выполнения Windows (`\Bookstore\Debug\Bookstore\Unmerged\BookSku.winmd`), описывающего класс среды выполнения. Затем запускается инструмент `cppwinrt.exe` для создания файлов исходного кода для поддержки создания и использования вашего класса среды выполнения. Эти файлы содержат заглушки для начала реализации класса среды выполнения **BookSku**, объявленного в вашем IDL. Это заглушки `\Bookstore\Bookstore\Generated Files\sources\BookSku.h` и `BookSku.cpp`.

Щелкните правой кнопкой мыши узел проекта и нажмите кнопку **Открыть папку в проводнике**. После этого в проводнике откроется папка проекта. Теперь скопируйте файлы заглушек `BookSku.h` и `BookSku.cpp` из папки `\Bookstore\Bookstore\Generated Files\sources\` в папку проекта `\Bookstore\Bookstore\`. В **обозревателе решений**, выбрав узел проекта, убедитесь, что включена функция **Показать все файлы**. Щелкните правой кнопкой мыши скопированные файлы заглушек и выберите **Включить в проект**.

## <a name="implement-booksku"></a>Реализуйте **BookSku**
Теперь давайте откроем `\Bookstore\Bookstore\BookSku.h` и `BookSku.cpp` и реализуем класс среды выполнения. В `BookSku.h` добавьте конструктор, который принимает [**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring), частный член для хранения строки названия и еще один — для события, которое мы будем вызывать при изменении названия. После внесения этих изменений `BookSku.h` выглядит следующим образом.

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
#include "BookSku.g.cpp"

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

В функции-мутаторе **Title** мы проверяем, задано ли значение, которое отличается от текущего значения. Если это так, мы обновляем название, а также вызываем событие [**INotifyPropertyChanged::PropertyChanged**](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged) с аргументом, равным имени свойства, которое было изменено. Это делается для того, чтобы пользовательскому интерфейсу было известно, значение какого свойства следует запрашивать повторно.

## <a name="declare-and-implement-bookstoreviewmodel"></a>Объявление и реализация **BookstoreViewModel**
Наша главная страница XAML будет привязываться к модели главного представления. И модель этого представления будет иметь несколько свойств, включая свойство типа **BookSku**. На этом шаге мы объявим и реализуем класс среды выполнения модели главного представления.

Добавьте новый элемент **Файл Midl (.idl)** с именем `BookstoreViewModel.idl`. Также см. раздел о [разделении классов среды выполнения на файлы Midl (.idl)](/windows/uwp/cpp-and-winrt-apis/author-apis#factoring-runtime-classes-into-midl-files-idl).

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

Сохраните проект и выполните сборку. Скопируйте `BookstoreViewModel.h` и `BookstoreViewModel.cpp` из папки `Generated Files\sources` в папку проекта и добавьте их в проект. Откройте эти файлы и реализуйте класс среды выполнения следующим образом. Обратите внимание на то, как в `BookstoreViewModel.h` мы добавляем `BookSku.h`, который объявляет тип реализации для **BookSku** (то есть **winrt::Bookstore::implementation::BookSku**). И мы удаляем `= default` из конструктора по умолчанию.

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
#include "BookstoreViewModel.g.cpp"

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
> Тип `m_bookSku` является проецируемым типом (**winrt::Bookstore::BookSku**), а параметр шаблона, который используется с [**winrt::make**](/uwp/cpp-ref-for-winrt/make), является типом реализации (**winrt::Bookstore::implementation::BookSku**). Даже в этом случае **make** возвращает экземпляр проецируемого типа.

## <a name="add-a-property-of-type-bookstoreviewmodel-to-mainpage"></a>Добавьте свойство типа **BookstoreViewModel** в **MainPage**
Откройте `MainPage.idl`, где объявляется класс среды выполнения, представляющий собой главную страницу пользовательского интерфейса. Добавьте оператор import для импорта `BookstoreViewModel.idl` и свойство только для чтения с именем MainViewModel типа **BookstoreViewModel**. Также удалите свойство **MyProperty**. Обратите внимание на директиву `import` в списке ниже.

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

Сохраните файл. На данный момент сборка проекта не будет выполнена до конца, но выполнить сборку будет необходимо, так как будут повторно созданы файлы исходного кода, в которых реализуется класс среды выполнения **MainPage** (`\Bookstore\Bookstore\Generated Files\sources\MainPage.h` и `MainPage.cpp`). Так что действуйте и выполните сборку. На этом этапе должна появиться ошибка сборки **'MainViewModel': не является членом 'winrt::Bookstore::implementation::MainPage'** .

Если опустить добавление `BookstoreViewModel.idl` (см. список `MainPage.idl` выше), то появится ошибка **ожидается \< рядом с MainViewModel**. Еще один совет: убедитесь, что все типы остаются в одном и том же пространстве имен — пространстве имен, которое показано в листингах кода.

Чтобы устранить эту ожидаемую ошибку, необходимо скопировать заглушки метода доступа для свойства **MainViewModel** из созданных файлов (`\Bookstore\Bookstore\Generated Files\sources\MainPage.h` и `MainPage.cpp`) в `\Bookstore\Bookstore\MainPage.h` и `MainPage.cpp`. Как это сделать, описано далее.

В `\Bookstore\Bookstore\MainPage.h` мы добавьте `BookstoreViewModel.h`, который объявляет тип реализации для **BookstoreViewModel** (то есть **winrt::Bookstore::implementation::BookstoreViewModel**). Добавьте частный член для хранения модели представления. Обратите внимание на то, что функция доступа свойства (и член m_mainViewModel) реализуется как проецируемый тип **BookstoreViewModel**, то есть **Bookstore::BookstoreViewModel**. Тип реализации находится в том же проекте (единице компиляции), что и приложение, поэтому мы создаем m_mainViewModel через перегрузку конструктора, которая принимает **std::nullptr_t**. Также удалите свойство **MyProperty**.

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

В `\Bookstore\Bookstore\MainPage.cpp` вызовите [**winrt::make**](/uwp/cpp-ref-for-winrt/make) (с типом реализации) для назначения нового экземпляра проецируемого типа для m_mainViewModel. Назначьте начальное значение для названия книги. Реализуйте метод доступа для свойства MainViewModel. И, наконец, обновите название книги в обработчике событий кнопки. Также удалите свойство **MyProperty**.

```cppwinrt
// MainPage.cpp
#include "pch.h"
#include "MainPage.h"
#include "MainPage.g.cpp"

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

## <a name="bind-the-button-to-the-title-property"></a>Привязывание кнопки к свойству **Title**
Откройте файл `MainPage.xaml`, который содержит разметку XAML для главной страницы пользовательского интерфейса. Как показано в листинге иже, удалите имя с кнопки и измените значение ее свойства **Content** с литерала на выражение привязки. Обратите внимание на свойство `Mode=OneWay` в выражении привязки (односторонняя из модели представления к пользовательскому интерфейсу). Без этого свойства пользовательский интерфейс не будет реагировать на события изменения свойств.

```xaml
<Button Click="ClickHandler" Content="{x:Bind MainViewModel.BookSku.Title, Mode=OneWay}"/>
```

Теперь выполните сборку и запустите проект. Нажмите кнопку, чтобы запустить обработчик события **Click**. Этот обработчик вызывает функция-мутатор названия книги. Этот мутатор порождает событие, чтобы сообщить пользовательскому интерфейсу о том, что свойство **Title** изменилось. А кнопка повторно запрашивает значение этого свойства, чтобы обновить свое собственное значение **Content**.

## <a name="using-the-binding-markup-extension-with-cwinrt"></a>Использование расширения разметки {Binding} с C++/WinRT
Чтобы иметь возможность использовать расширение разметки {Binding} с текущей выпущенной версией C++/WinRT, необходимо реализовать интерфейсы [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider) и [ICustomProperty](/uwp/api/windows.ui.xaml.data.icustomproperty).

## <a name="element-to-element-binding"></a>Привязка между элементами

Вы можете привязать свойство одного элемента XAML к свойству другого элемента XAML. Вот как это выглядит в разметке:

```xaml
<TextBox x:Name="myTextBox" />
<TextBlock Text="{x:Bind myTextBox.Text, Mode=OneWay}" />
```

Вам потребуется объявить именованную сущность XAML `myTextBox` как свойство только для чтения в своем файле Midl (IDL-файле).

```idl
// MainPage.idl
runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
{
    MainPage();
    Windows.UI.Xaml.Controls.TextBox myTextBox{ get; };
}
```

Вот почему это требуется. Все типы, которые компилятору XAML нужно проверить (включая те, которые используются в [{x: Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension)) считываются из метаданных Windows (WinMD). Вам нужно всего лишь добавить свойство только для чтения в файл Midl. Не реализуйте это, так как автоматически созданный код XAML обеспечивает реализацию.

## <a name="consuming-objects-from-xaml-markup"></a>Использование объектов из разметки XAML

Все сущности, используемые при применении [**расширения разметки XAML {x:Bind}** ](/windows/uwp/xaml-platform/x-bind-markup-extension), должны быть общедоступными в IDL. Более того, если разметка XAML содержит ссылку на другой элемент, который также находится в разметке, метод получения для этой разметки должен присутствовать в IDL.

```xaml
<Page x:Name="MyPage">
    <StackPanel>
        <CheckBox x:Name="UseCustomColorCheckBox" Content="Use custom color"
             Click="UseCustomColorCheckBox_Click" />
        <Button x:Name="ChangeColorButton" Content="Change color"
            Click="{x:Bind ChangeColorButton_OnClick}"
            IsEnabled="{x:Bind UseCustomColorCheckBox.IsChecked.Value, Mode=OneWay}"/>
    </StackPanel>
</Page>
```

Элемент *ChangeColorButton* ссылается на элемент *UseCustomColorCheckBox* с помощью привязки. Поэтому в IDL для этой страницы необходимо объявить свойство только для чтения с именем *UseCustomColorCheckBox*, чтобы это свойство было доступно для привязки.

В делегате обработчика событий щелчков для *UseCustomColorCheckBox* используется классический синтаксис делегата XAML. Поэтому запись в IDL не требуется. Свойство просто должно быть общедоступным в классе реализации. С другой стороны, *ChangeColorButton* также имеет обработчик событий щелчков `{x:Bind}`, который аналогичным образом должен войти в IDL.

```idl
runtimeclass MyPage : Windows.UI.Xaml.Controls.Page
{
    MyPage();

    // These members are consumed by binding.
    void ChangeColorButton_OnClick();
    Windows.UI.Xaml.Controls.CheckBox UseCustomColorCheckBox{ get; };
}
```

Вам не нужно предоставлять реализацию для свойства **UseCustomColorCheckBox**. Это сделает генератор кода XAML.

### <a name="binding-to-boolean"></a>Привязка к логическому значению

Такую привязку можно установить в режиме диагностики.

<syntaxhighlight lang="xml">
<TextBlock Text="{Binding CanPair}"/>
</syntaxhighlight>

В C++/CX значение будет `true` или `false`, а в C++/WinRT — **Windows.Foundation.IReference`1<Boolean>** .

При привязке к логическому значению используйте `x:Bind`.

```xaml
<TextBlock Text="{x:Bind CanPair}"/>
```

## <a name="important-apis"></a>Важные API
* [INotifyPropertyChanged::PropertyChanged](/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged)
* [Шаблон функции winrt::make](/uwp/cpp-ref-for-winrt/make)

## <a name="related-topics"></a>Связанные темы
* [Использование интерфейсов API с помощью C++/WinRT](consume-apis.md)
* [Создание интерфейсов API с помощью C++/WinRT](author-apis.md)
