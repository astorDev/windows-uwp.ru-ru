---
description: В этом разделе показано, как регистрировать и отзывать делегаты обработки событий с помощью C++/WinRT.
title: Обработка событий с помощью делегатов в C++/WinRT
ms.date: 03/04/2019
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проецируемый, проекция, маркер, событие, делегат
ms.localizationpriority: medium
ms.openlocfilehash: c647168f44ffbfc4d753700a87825b5ca7b28544
ms.sourcegitcommit: c315ec3e17489aeee19f5095ec4af613ad2837e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58921680"
---
# <a name="handle-events-by-using-delegates-in-cwinrt"></a>Обработка событий с помощью делегатов в C++/WinRT

В этом разделе показано, как для регистрации и отмены обработки событий делегатов, использующих [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt). Вы можете обрабатывать события с помощью любого стандартного подобного функции объекта C++.

> [!NOTE]
> Сведения об установке и использовании C++WinRT Visual Studio Extension (VSIX) и пакет NuGet (которые вместе обеспечивают шаблон проекта и поддержка сборки), см. в разделе [поддержка Visual Studio C++/WinRT](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

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
> При регистрации делегата, приведенном выше примере кода передает необработанного *это* указатель (с указанием текущего объекта). Чтобы узнать, как можно установить со строгим или слабую ссылку на текущий объект, см. в разделе **при использовании функции-члена в качестве делегата** подраздел в разделе [безопасного доступа к *это* указатель с помощью делегата, обрабатывающего события](weak-references.md#safely-accessing-the-this-pointer-with-an-event-handling-delegate).

Существуют другие способы создания **RoutedEventHandler**. Ниже приведен синтаксис блока, взятое из раздел документации для [ **RoutedEventHandler** ](/uwp/api/windows.ui.xaml.routedeventhandler) (выберите  *C++/WinRT* из **языка** раскрывающееся меню в правом верхнем углу веб-страницы). Обратите внимание на различные конструкторы: один принимает лямбда-функцию; другой — свободную функцию, а третий (который мы использовали выше) принимает объект и указатель на функцию-член.

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

Также полезно ознакомиться с синтаксисом оператора вызова функции. Вы узнаете, какими должны быть параметры делегата. Как видно, в этом случае синтаксис оператора вызова функции соответствует параметра нашего **MainPage::ClickHandler**.

> [!NOTE]
> Любое указанное событие чтобы понять аспекты своего делегата, а также параметры этого делегата, см. в статье первый раздел документации для самого события. Давайте [UIElement.KeyDown событий](/uwp/api/windows.ui.xaml.uielement.keydown) в качестве примера. Посетите этот раздел и выберите  *C++/WinRT* из **языка** раскрывающегося списка. В блоке синтаксиса в начале этого раздела вы увидите следующее.
> 
> ```cppwinrt
> // Register
> event_token KeyDown(KeyEventHandler const& handler) const;
> ```
>
> Что info указывает, что **UIElement.KeyDown** событий (раздел, мы готовим выпуск) с типом делегата **KeyEventHandler**, так как этот тип, который передается при регистрации делегата события этого типа. Итак, теперь перейдите по ссылке на тему, [KeyEventHandler делегат](/uwp/api/windows.ui.xaml.input.keyeventhandler) типа. Здесь блок синтаксис содержит оператор вызова функции. И, как упоминалось выше, сообщающее, что должны быть параметры делегата.
> 
> ```cppwinrt
> void operator()(winrt::Windows::Foundation::IInspectable const& sender, winrt::Windows::UI::Xaml::Input::KeyRoutedEventArgs const& e) const;
> ```
>
>  Как вы видите, делегат должен объявляться вступили **IInspectable** как отправителя и экземпляр [KeyRoutedEventArgs класс](/uwp/api/windows.ui.xaml.input.keyroutedeventargs) как аргументы.
>
> Чтобы воспользоваться другой пример, давайте взглянем на [Popup.Closed событий](/uwp/api/windows.ui.xaml.controls.primitives.popup.closed). Его тип делегата — [EventHandler\<IInspectable\>](/uwp/api/windows.foundation.eventhandler). Таким образом, вступят в делегате **IInspectable** как отправителя, а другой **IInspectable** (так как это **EventHandler**параметр типа) как аргументы.

Если вы не делаете большого объема работы в своем обработчике событий, можно использовать лямбда-функцию вместо функции-члена. Опять же, он может оказаться сложной в примере кода ниже, но **RoutedEventHandler** делегат состоит из лямбда-функции, которая, опять же, должна соответствует синтаксису оператор вызова функции, который рассматривался выше.

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

Вы можете применить более явный подход при создании вашего делегата. Например, если вы хотите передать его или использовать более одного раза.

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

Обычно при регистрации делегата к вам возвращается маркер. Впоследствии можно использовать этот маркер для отзыва делегата; то есть регистрация делегата из события будет отозвана, и делегат не будет вызываться при следующем возникновении события. Для простоты ни один из приведенных выше примеров кода не показывает, как это можно сделать. Однако следующий образец сохраняет маркер в закрытом элементе данных структуры, а также отменяет его обработчик в деструкторе.

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

Вместо строгую ссылку, как показано в примере выше, можно хранить слабую ссылку на кнопку (см. в разделе [сильные и слабые ссылки в C++/WinRT](weak-references.md)).

Кроме того, при регистрации делегата, можно указать **winrt::auto_revoke** (которое является значением типа [ **winrt::auto_revoke_t**](/uwp/cpp-ref-for-winrt/auto-revoke-t)) для запроса событий revoker ( Тип [ **winrt::event_revoker**](/uwp/cpp-ref-for-winrt/event-revoker)). Revoker событий содержит слабую ссылку на источник события (объект, вызывающий событие) для вас. Вы можете вручную отозвать событие, вызвав функцию-член **event_revoker::revoke**; но объект отзыва события вызывает эту функцию автоматически при выходе за пределы области действия. Функция **revoke** проверяет, существует ли источник события, и, если это так, отзывает ваш делегат. В этом примере нет необходимости хранить источник событий, а также не требуется деструктор.

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

Ниже приведен блок синтаксиса, взятый из раздела документации для события [**ButtonBase::Click**](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click). Показано три различных функции регистрации и отзыва. Можно в точности увидеть, какие виды отзыва событий необходимо объявлять из третьей перегрузки.

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
> В примере выше `Button::Click_revoker` является псевдонимом для типа `winrt::event_revoker<winrt::Windows::UI::Xaml::Controls::Primitives::IButtonBase>`. Аналогичный шаблон применяется ко всем событиям C++/WinRT. Каждое событие среды выполнения Windows имеет перегрузку функции revoke, возвращающий revoker событий и тип элемента revoker входит в состав источника события. Таким образом, другой пример [ **CoreWindow::SizeChanged** ](/uwp/api/windows.ui.core.corewindow.sizechanged) событие имеет перегрузку функции регистрации, которая возвращает значение типа **CoreWindow::SizeChanged_revoker**.


Вы можете рассмотреть вариант отзыва обработчиков в сценарии навигации по страницам. Если вы многократно переходите на страницу, а затем обратно, можно отменять все обработчики при переходе со страницы. Кроме того, если вы повторно используете один и тот же экземпляр страницы, проверьте значение маркера и регистрируйте, только если он еще не был установлен (`if (!m_token){ ... }`). Третий вариант — хранение отзыва событий на странице в качестве элемента данных. Четвертый вариант, как описано далее в этом разделе, состоит в записи строгой или слабой ссылки на объект *this* в вашей лямбда-функции.

## <a name="delegate-types-for-asynchronous-actions-and-operations"></a>Типы делегатов для асинхронных действий и операций

Примеры выше используют тип делегата **RoutedEventHandler**, но, безусловно, существует множество других типов делегатов. Например, асинхронные действия и операции (с выполнением и без) имеют завершенные и (или) текущие события, ожидающие делегаты соответствующего типа. Например, события хода выполнения асинхронной операции с выполнением (которое представляет собой все, что реализует [**IAsyncOperationWithProgress**](/uwp/api/windows.foundation.iasyncoperationwithprogress_tresult_tprogress_)) требует делегат типа [**AsyncOperationProgressHandler**](/uwp/api/windows.foundation.asyncoperationprogresshandler). Вот пример кода для создания делегата такого типа с помощью лямбда-функции. В примере также показано, как создать делегат [**AsyncOperationWithProgressCompletedHandler**](/uwp/api/windows.foundation.asyncoperationwithprogresscompletedhandler).

```cppwinrt
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

Как предполагает комментарий о сопрограмме выше, вместо использования делегата с завершенными событиями асинхронных действий и операций, вы можете счесть более естественным использование сопрограмм. Подробные сведения и примеры кода см. в разделе [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md).

> [!NOTE]
> Это не так реализовать более одного *обработчик завершения* асинхронное действие или операцию. Может иметь только один делегат для события завершения, или вы можете `co_await` его. Если у вас есть оба, то второй завершится ошибкой.

Если вы будете использовать с делегатами вместо соподпрограмме, вы можете для более простой синтаксис.

```cppwinrt
async_op_with_progress.Completed(
    [](auto&& /*sender*/, AsyncStatus const /* args */)
{
    // ...
});
```

## <a name="delegate-types-that-return-a-value"></a>Типы делегатов, которые возвращают значение

Некоторые типы делегатов должны сами возвращать значение. Примером может служить [**ListViewItemToKeyHandler**](/uwp/api/windows.ui.xaml.controls.listviewitemtokeyhandler), который возвращает строку. Ниже приведен пример создания делегата этого типа (обратите внимание, что лямбда-функция возвращает значение).

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

## <a name="safely-accessing-the-this-pointer-with-an-event-handling-delegate"></a>Безопасного доступа к *это* указатель с помощью делегата, обрабатывающего события

Если вы обрабатываете события с помощью функции-члена объекта или из внутри лямбда-функции внутри функции-члена объекта затем вам нужно подумать о относительного времени существования получателя событий (объект, обрабатывающий событие) и источник события (объект порождает событие). Дополнительные сведения и примеры кода см. в разделе [сильные и слабые ссылки в C++/WinRT](weak-references.md#safely-accessing-the-this-pointer-with-an-event-handling-delegate).

## <a name="important-apis"></a>Важные API
* [Структура маркера WinRT::auto_revoke_t](/uwp/cpp-ref-for-winrt/auto-revoke-t)
* [Функция winrt::implements::get_weak](/uwp/cpp-ref-for-winrt/implements#implementsget_weak-function)
* [Функция winrt::implements::get_strong](/uwp/cpp-ref-for-winrt/implements#implementsget_strong-function)

## <a name="related-topics"></a>См. также
* [Создание событий в C++/WinRT](author-events.md)
* [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md)
* [Сильные и слабые ссылки в C++/WinRT](weak-references.md)
