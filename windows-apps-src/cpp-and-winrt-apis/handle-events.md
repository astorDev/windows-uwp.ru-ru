---
author: stevewhims
description: В этом разделе показано, как регистрировать и отзывать делегаты обработки событий с помощью C++/WinRT.
title: Обработка событий с помощью делегатов в C++/WinRT
ms.author: stwhi
ms.date: 05/07/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проецируемый, проекция, маркер, событие, делегат
ms.localizationpriority: medium
ms.openlocfilehash: 9ca257de29be8e732e05c343a4b782b1676627bf
ms.sourcegitcommit: e38b334edb82bf2b1474ba686990f4299b8f59c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "6861127"
---
# <a name="handle-events-by-using-delegates-in-cwinrt"></a>Обработка событий с помощью делегатов в C++/WinRT

В этом разделе показано, как регистрировать и отзывать делегаты обработки событий с помощью [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt). Вы можете обрабатывать события с помощью любого стандартного подобного функции объекта C++.

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) (которое обеспечивает поддержку шаблона проекта, а также свойств и целевых объектов MSBuild C++/WinRT) см. в разделе [Поддержка Visual Studio для C++/WinRT и VSIX](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

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
> При регистрации делегата, приведенном выше примере кода передает необработанные *Этот* указатель (с указанием текущего объекта). Чтобы узнать, как установить строгой или слабой ссылки на текущий объект, см. **Если вы используете функцию-член как делегат** вложенные раздел разделе [безопасный доступ к *Этот* указатель с помощью делегата обработки события](weak-references.md#safely-accessing-the-this-pointer-with-an-event-handling-delegate).

Существуют другие способы создания **RoutedEventHandler**. Ниже приведен блока синтаксиса, взятый из раздела документации для [**RoutedEventHandler**](/uwp/api/windows.ui.xaml.routedeventhandler) (выберите *C++/WinRT* из раскрывающегося списка **Language** на странице). Обратите внимание на различные конструкторы: один принимает лямбда-функцию; другой — свободную функцию, а третий (который мы использовали выше) принимает объект и указатель на функцию-член.

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

Если вы не делаете большого объема работы в своем обработчике событий, можно использовать лямбда-функцию вместо функции-члена. И снова, это может быть неочевидно из примера кода, представленного ниже, но делегат **RoutedEventHandler** создается из лямбда-функции, которая в свою очередь должна соответствовать синтаксису оператора вызова функции.

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

Вместо строгой ссылки, как в примере выше, вы можете хранить слабую ссылку на кнопку (см. в разделе [строгую и слабые ссылки в C + +/ WinRT](weak-references.md)).

Кроме того когда вы регистрируете делегата, можно указать **winrt::auto_revoke** (который является значением типа [**winrt::auto_revoke_t**](/uwp/cpp-ref-for-winrt/auto-revoke-t)) для запроса отзыва событий (of типа [**winrt::event_revoker**](/uwp/cpp-ref-for-winrt/event-revoker)). Отзыва событий содержит слабую ссылку на источник события (объект, который вызывает событие). Вы можете вручную отозвать событие, вызвав функцию-член **event_revoker::revoke**; но объект отзыва события вызывает эту функцию автоматически при выходе за пределы области действия. Функция **revoke** проверяет, существует ли источник события, и, если это так, отзывает ваш делегат. В этом примере нет необходимости хранить источник событий, а также не требуется деструктор.

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
> В приведенном выше примере кода `Button::Click_revoker` является псевдонимом типа для `winrt::event_revoker<winrt::Windows::UI::Xaml::Controls::Primitives::IButtonBase>`. Аналогичный шаблон применяется ко всем событиям C++/WinRT. Каждое событие среды выполнения Windows имеет перегрузки функции revoke отзыва событий, который возвращает и тип отзыва является членом источник события. Таким образом Чтобы выполнить еще один пример, событие [**CoreWindow::SizeChanged**](/uwp/api/windows.ui.core.corewindow.sizechanged) имеет перегрузку функции регистрации, который возвращает значение типа **CoreWindow::SizeChanged_revoker**.


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
> Не является правильным для реализации более одного *обработчик завершения* асинхронные действия или операции. У вас есть только один делегат для события завершения, или вы можете `co_await` его. Если у вас есть оба, второй завершится ошибкой.

Если вы делегаты вместо сопрограммы, затем вы можете отказаться от более простой синтаксис.

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

## <a name="safely-accessing-the-this-pointer-with-an-event-handling-delegate"></a>Безопасный доступ к *Этот* указатель с помощью делегата обработки события

Если вы обрабатывать события с помощью функции-члена объекта или из внутри лямбда-функции внутри функции-члена объекта, затем следует подумать о относительный время существования получателя события (объект, обработав событие) и источник события (объект вызов события). Дополнительные сведения и примеры кода, см. в разделе [строгую и слабые ссылки в C + +/ WinRT](weak-references.md#safely-accessing-the-this-pointer-with-an-event-handling-delegate).

## <a name="important-apis"></a>Важные API
* [Структура маркера WinRT::auto_revoke_t](/uwp/cpp-ref-for-winrt/auto-revoke-t)
* [Функция winrt::implements::get_weak](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function)
* [Функция winrt::implements::get_strong](/uwp/cpp-ref-for-winrt/implements#implementsgetstrong-function)

## <a name="related-topics"></a>Статьи по теме
* [Создание событий в C++/WinRT](author-events.md)
* [Параллельная обработка и асинхронные операции с помощью C++/WinRT](concurrency.md)
* [Надежная и слабые ссылки в C + +/ WinRT](weak-references.md)
