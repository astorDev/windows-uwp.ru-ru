---
description: В этом разделе показано, как регистрировать и отзывать делегаты обработки событий с помощью C++/WinRT.
title: Обработка событий с помощью делегатов в C++/WinRT
ms.date: 04/23/2019
ms.topic: article
keywords: windows 10, uwp, стандартный, c++, cpp, winrt, проецируемый, проекция, обработка, событие, делегат
ms.localizationpriority: medium
ms.openlocfilehash: b64fbe93198af95402161873c1d68d0da41f33f7
ms.sourcegitcommit: d37a543cfd7b449116320ccfee46a95ece4c1887
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68270106"
---
# <a name="handle-events-by-using-delegates-in-cwinrt"></a>Обработка событий с помощью делегатов в C++/WinRT

В этом разделе показано, как регистрировать и отзывать делегаты обработки событий с помощью [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt). Вы можете обрабатывать события с помощью любого стандартного подобного функции объекта C++.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки) см. в разделе о [поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

## <a name="using-visual-studio-2019-to-add-an-event-handler"></a>Добавление обработчика событий с помощью Visual Studio 2019

Пользовательский интерфейс конструктора XAML в Visual Studio 2019 предлагает удобный способ добавления обработчика событий в проект. Откройте страницу XAML в конструкторе XAML и выберите элемент управления, событие которого требуется обрабатывать. На странице свойств этого элемента управления, щелкните расположенный вверху значок с молнией, чтобы отобразить список всех событий, связанных с этим элементом управления. Дважды щелкните событие, которое требуется обрабатывать, например *OnClicked*.

Конструктор XAML добавляет в исходные файлы прототип функции обработчика соответствующего события (и реализацию заглушки), содержимое которого можно заменить собственной реализацией.

> [!NOTE]
> Как правило, обработчики событий не нужно описывать в файле Midl (`.idl`). Поэтому конструктор XAML не добавляет прототипы обработчика событий в файл Midl. Он добавляет их только в файлы `.h` и `.cpp`.

## <a name="register-a-delegate-to-handle-an-event"></a>Регистрация делегата для обработки события

Простым примером является обработка события нажатия кнопки. Обычно используется разметка XAML, чтобы зарегистрировать функцию-член для обработки события следующим образом.

```xaml
// MainPage.xaml
<Button x:Name="Button" Click="ClickHandler">Click Me</Button>
```

```cppwinrt
// MainPage.cpp
void MainPage::ClickHandler(IInspectable const& /* sender */, RoutedEventArgs const& /* args */)
{
    Button().Content(box_value(L"Clicked"));
}
```

Вместо декларативной регистрации в разметке можно принудительно зарегистрировать функцию-член для обработки события. Это может быть не очевидно из примера кода, представленного ниже, но аргумент вызова [**ButtonBase::Click**](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) является экземпляром делегата [**RoutedEventHandler**](/uwp/api/windows.ui.xaml.routedeventhandler). В этом случае мы используем перегрузку конструктора **RoutedEventHandler**, которая принимает объект и указатель на функцию-член.

```cppwinrt
// MainPage.cpp
MainPage::MainPage()
{
    InitializeComponent();

    Button().Click({ this, &MainPage::ClickHandler });
}
```

> [!IMPORTANT]
> При регистрации делегата приведенный выше пример кода передает необработанный указатель *this* (указывающий на текущий объект). Сведения о том, как установить сильную или слабую ссылку на текущий объект, см. в разделе [Использование функции-члена в качестве делегата](weak-references.md#if-you-use-a-member-function-as-a-delegate).

Существуют другие способы создания **RoutedEventHandler**. Ниже приведен блока синтаксиса, взятый из раздела документации для [**RoutedEventHandler**](/uwp/api/windows.ui.xaml.routedeventhandler) (выберите *C++/WinRT* из раскрывающегося списка **Язык** в правом верхнем углу веб-страницы). Обратите внимание на различные конструкторы: один принимает лямбда-функцию; другой — свободную функцию, а третий (который мы использовали выше) принимает объект и указатель на функцию-член.

```cppwinrt
struct RoutedEventHandler : winrt::Windows::Foundation::IUnknown
{
    RoutedEventHandler(std::nullptr_t = nullptr) noexcept;
    template <typename L> RoutedEventHandler(L lambda);
    template <typename F> RoutedEventHandler(F* function);
    template <typename O, typename M> RoutedEventHandler(O* object, M method);
    void operator()(winrt::Windows::Foundation::IInspectable const& sender,
        winrt::Windows::UI::Xaml::RoutedEventArgs const& e) const;
};
```

Полезно также ознакомиться с синтаксисом оператора вызова функции. Вы узнаете, какими должны быть параметры делегата. Как видно, в этом случае синтаксис оператора вызова функции соответствует параметру **MainPage::ClickHandler**.

> [!NOTE]
> Чтобы изучить сведения о делегате любого заданного события, а также параметры этого делегата, сначала перейдите к разделу документации по этому событию. Давайте выберем [событие UIElement.KeyDown](/uwp/api/windows.ui.xaml.uielement.keydown) в качестве примера. Посетите этот раздел и выберите *C++/WinRT* из раскрывающегося списка **Язык**. В блоке синтаксиса в начале раздела вы увидите следующее.
> 
> ```cppwinrt
> // Register
> event_token KeyDown(KeyEventHandler const& handler) const;
> ```
>
> Как видно из примера, у события **UIElement.KeyDown** (в текущем разделе) есть делегат типа **KeyEventHandler**, так как этот тип передается при регистрации делегата этого типа событий. Итак, теперь перейдите по ссылке в раздел о типе [KeyEventHandler делегат](/uwp/api/windows.ui.xaml.input.keyeventhandler). Здесь блок синтаксиса содержит оператор вызова функции. И, как сказано ранее, вы узнаете, какими должны быть параметры делегата.
> 
> ```cppwinrt
> void operator()(winrt::Windows::Foundation::IInspectable const& sender, winrt::Windows::UI::Xaml::Input::KeyRoutedEventArgs const& e) const;
> ```
>
>  Как вы видите, делегат должен объявляться с отправителем **IInspectable** и экземпляром [класса KeyRoutedEventArgs](/uwp/api/windows.ui.xaml.input.keyroutedeventargs) в качестве аргументов.
>
> Чтобы рассмотреть другой пример, давайте взглянем на [событие Popup.Closed](/uwp/api/windows.ui.xaml.controls.primitives.popup.closed). Его тип делегата — [EventHandler\<IInspectable\>](/uwp/api/windows.foundation.eventhandler). Таким образом, в качестве аргументов ваш делегат примет одно значение **IInspectable** в качестве отправителя и еще одно значение **IInspectable** (так как это тип параметра **EventHandler**).

Если вы не делаете большого объема работы в своем обработчике событий, можно использовать лямбда-функцию вместо функции-члена. И снова, это может быть неочевидно из примера кода, представленного ниже, но делегат **RoutedEventHandler** создается из лямбда-функции, которая в свою очередь должна соответствовать синтаксису оператора вызова функции, рассмотренного ранее.

```cppwinrt
MainPage::MainPage()
{
    InitializeComponent();

    Button().Click([this](IInspectable const& /* sender */, RoutedEventArgs const& /* args */)
    {
        Button().Content(box_value(L"Clicked"));
    });
}
```

Вы можете применить более явный подход при создании делегата. Например, если вы хотите передать его или использовать более одного раза.

```cppwinrt
MainPage::MainPage()
{
    InitializeComponent();

    auto click_handler = [](IInspectable const& sender, RoutedEventArgs const& /* args */)
    {
        sender.as<winrt::Windows::UI::Xaml::Controls::Button>().Content(box_value(L"Clicked"));
    };
    Button().Click(click_handler);
    AnotherButton().Click(click_handler);
}
```

## <a name="revoke-a-registered-delegate"></a>Отзыв зарегистрированного делегата

Обычно при регистрации делегата возвращается маркер. Впоследствии можно использовать этот маркер для отзыва делегата. То есть регистрация делегата из события будет отозвана, и делегат не будет вызываться при следующем возникновении события.

Для простоты ни один из приведенных выше примеров кода не показывает, как это можно сделать. Однако следующий пример сохраняет маркер в частном элементе данных структуры, а также отменяет его обработчик в деструкторе.

```cppwinrt
struct Example : ExampleT<Example>
{
    Example(winrt::Windows::UI::Xaml::Controls::Button const& button) : m_button(button)
    {
        m_token = m_button.Click([this](IInspectable const&, RoutedEventArgs const&)
        {
            // ...
        });
    }
    ~Example()
    {
        m_button.Click(m_token);
    }

private:
    winrt::Windows::UI::Xaml::Controls::Button m_button;
    winrt::event_token m_token;
};
```

Вместо строгой ссылки, как в примере выше, вы можете хранить слабую ссылку на кнопку (см. раздел [Сильные и слабые ссылки в C++/WinRT](weak-references.md)).

> [!NOTE]
> Когда события поступают из источника синхронно, вы можете отозвать свой дескриптор. При этом события больше поступать не будут. Но для асинхронных событий даже после отмены (и особенно после отмены в деструкторе) обрабатываемое событие может достичь объекта после начала уничтожения. Попытка отменить подписку перед уничтожением может решить проблему, но вам нужно найти надежное решение — см. сведения о [безопасном получении доступа к указателю *this* с помощью делегата, обрабатывающего события](weak-references.md#safely-accessing-the-this-pointer-with-an-event-handling-delegate).

Кроме того, когда вы регистрируете делегат, вы можете указать **winrt::auto_revoke** (которое является значением типа [**winrt::auto_revoke_t**](/uwp/cpp-ref-for-winrt/auto-revoke-t)) для запроса метода отзыва событий (типа [**winrt::event_revoker**](/uwp/cpp-ref-for-winrt/event-revoker)). Метод отзыва событий содержит слабую ссылку на источник события (объект, который породил событие). Вы можете вручную отозвать событие, вызвав функцию-член **event_revoker::revoke**; но метод отзыва события вызывает эту функцию автоматически при выходе за пределы области действия. Функция **revoke** проверяет, существует ли источник события, и, если это так, отзывает ваш делегат. В этом примере нет необходимости хранить источник событий, а также не требуется деструктор.

```cppwinrt
struct Example : ExampleT<Example>
{
    Example(winrt::Windows::UI::Xaml::Controls::Button button)
    {
        m_event_revoker = button.Click(winrt::auto_revoke, [this](IInspectable const& /* sender */, RoutedEventArgs const& /* args */)
        {
            // ...
        });
    }

private:
    winrt::Windows::UI::Xaml::Controls::Button::Click_revoker m_event_revoker;
};
```

Ниже приведен блок синтаксиса, взятый из раздела документации по событию [**ButtonBase::Click**](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click). Показаны три различные функции регистрации и отзыва. Можно в точности увидеть, какие виды отзыва событий необходимо объявлять из третьей перегрузки.

```cppwinrt
// Register
winrt::event_token Click(winrt::Windows::UI::Xaml::RoutedEventHandler const& handler) const;

// Revoke with event_token
void Click(winrt::event_token const& token) const;

// Revoke with event_revoker
Button::Click_revoker Click(winrt::auto_revoke_t,
    winrt::Windows::UI::Xaml::RoutedEventHandler const& handler) const;
```

> [!NOTE]
> В примере выше `Button::Click_revoker` является псевдонимом для `winrt::event_revoker<winrt::Windows::UI::Xaml::Controls::Primitives::IButtonBase>`. Аналогичный шаблон применяется ко всем событиям C++/WinRT. Каждое событие среды выполнения Windows имеет перегрузку функции revoke, возвращающую метод отзыва событий, и тип этого объекта входит в состав источника события. Рассмотрим другой пример. У события [**CoreWindow::SizeChanged**](/uwp/api/windows.ui.core.corewindow.sizechanged) имеется перегрузка функции регистрации, которая возвращает значение типа **CoreWindow::SizeChanged_revoker**.

Вы можете рассмотреть вариант отзыва обработчиков в сценарии перехода по страницам. Если вы многократно переходите на страницу, а затем обратно, можно отменять все обработчики при переходе со страницы. Кроме того, если вы повторно используете один и тот же экземпляр страницы, проверьте значение маркера и выполняйте регистрацию, только если он еще не был установлен (`if (!m_token){ ... }`). Третий вариант — хранение отзыва событий на странице в качестве элемента данных. Четвертый вариант, как описано далее в этом разделе, состоит в записи строгой или слабой ссылки на объект *this* в вашей лямбда-функции.

### <a name="if-your-auto-revoke-delegate-fails-to-register"></a>Если автоматически отзываемый делегат не удалось зарегистрировать

Если при регистрации делегата вы указываете [**winrt::auto_revoke**](/uwp/cpp-ref-for-winrt/auto-revoke-t), в результате чего возникает исключение [**winrt::hresult_no_interface**](/uwp/cpp-ref-for-winrt/error-handling/hresult-no-interface), это обычно означает, что источник событий не поддерживает слабые ссылки. Это распространенная ситуация, например в пространстве имен [**Windows.UI.Composition**](/uwp/api/windows.ui.composition). В этом случае нельзя использовать функцию автоматического отзыва. Необходимо выполнять отзыв обработчиков событий вручную.

## <a name="delegate-types-for-asynchronous-actions-and-operations"></a>Типы делегатов для асинхронных действий и операций

В примерах выше используется тип делегата **RoutedEventHandler**, но, безусловно, существует множество других типов делегатов. Например, асинхронные действия и операции (с отслеживанием хода выполнения и без) имеют события завершения и (или) выполнения, ожидающие делегаты соответствующего типа. Например, для события выполнения асинхронной операции с отслеживанием хода выполнения (которая представляет собой все, что реализует [**IAsyncOperationWithProgress**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_)) требуется делегат типа [**AsyncOperationProgressHandler**](/uwp/api/windows.foundation.asyncoperationprogresshandler). Вот пример кода для создания делегата такого типа с помощью лямбда-функции. В примере также показано, как создать делегат [**AsyncOperationWithProgressCompletedHandler**](/uwp/api/windows.foundation.asyncoperationwithprogresscompletedhandler).

```cppwinrt
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Web.Syndication.h>

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::Web::Syndication;

void ProcessFeedAsync()
{
    Uri rssFeedUri{ L"https://blogs.windows.com/feed" };
    SyndicationClient syndicationClient;

    auto async_op_with_progress = syndicationClient.RetrieveFeedAsync(rssFeedUri);

    async_op_with_progress.Progress(
        [](IAsyncOperationWithProgress<SyndicationFeed, RetrievalProgress> const& /* sender */, RetrievalProgress const& args)
        {
            uint32_t bytes_retrieved = args.BytesRetrieved;
            // use bytes_retrieved;
        });

    async_op_with_progress.Completed(
        [](IAsyncOperationWithProgress<SyndicationFeed, RetrievalProgress> const& sender, AsyncStatus const /* asyncStatus */)
        {
            SyndicationFeed syndicationFeed = sender.GetResults();
            // use syndicationFeed;
        });

    // or (but this function must then be a coroutine, and return IAsyncAction)
    // SyndicationFeed syndicationFeed{ co_await async_op_with_progress };
}
```

Как предполагает комментарий о соподпрограмме выше, вместо использования делегата с событиями завершения асинхронных действий и операций, вы можете счесть более естественным использование соподпрограмм. Дополнительные сведения и примеры кода приведены в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md).

> [!NOTE]
> Для асинхронного действия или операции нельзя реализовывать несколько *обработчиков завершения*. Вы может использовать только один делегат для события завершения или применить для него `co_await`. Если их два, второй завершится ошибкой.

Если вы используете только делегаты вместо соподпрограмм, то вы можете выбрать более простой синтаксис.

```cppwinrt
async_op_with_progress.Completed(
    [](auto&& /*sender*/, AsyncStatus const /* args */)
{
    // ...
});
```

## <a name="delegate-types-that-return-a-value"></a>Типы делегатов, которые возвращают значение

Некоторые типы делегатов должны сами возвращать значение. Примером может послужить [**ListViewItemToKeyHandler**](/uwp/api/windows.ui.xaml.controls.listviewitemtokeyhandler), который возвращает строку. Ниже приведен пример создания делегата этого типа (обратите внимание на то, что лямбда-функция возвращает значение).

```cppwinrt
using namespace winrt::Windows::UI::Xaml::Controls;

winrt::hstring f(ListView listview)
{
    return ListViewPersistenceHelper::GetRelativeScrollPosition(listview, [](IInspectable const& item)
    {
        return L"key for item goes here";
    });
}
```

## <a name="safely-accessing-the-this-pointer-with-an-event-handling-delegate"></a>Безопасный доступ к указателю *this* с помощью делегата, обрабатывающего события

Но если нужно обработать событие с функцией-членом объекта или из лямбда-функции внутри функции-члена объекта, необходимо подумать об относительном времени существования получателя события (объекта, обрабатывающего событие) и источника события (объекта, порождающего событие). Дополнительные сведения и примеры кода приведены в разделе [Сильные и слабые ссылки в C++/WinRT](weak-references.md#safely-accessing-the-this-pointer-with-an-event-handling-delegate).

## <a name="important-apis"></a>Важные API
* [Структура маркера winrt::auto_revoke_t](/uwp/cpp-ref-for-winrt/auto-revoke-t)
* [Функция winrt::implements::get_weak](/uwp/cpp-ref-for-winrt/implements#implementsget_weak-function)
* [Функция winrt::implements::get_strong](/uwp/cpp-ref-for-winrt/implements#implementsget_strong-function)

## <a name="related-topics"></a>Статьи по теме
* [Создание событий в C++/WinRT](author-events.md)
* [Параллельные обработка и выполнение асинхронных операций с помощью C++/WinRT](concurrency.md)
* [Сильные и слабые ссылки в C++/WinRT](weak-references.md)
