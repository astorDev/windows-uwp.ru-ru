---
Description: Сведения об включении навигации между двумя простыми страницами в приложении Универсальная платформа Windows (UWP).
title: Одноранговая навигация между двумя страницами
ms.assetid: 0A364C8B-715F-4407-9426-92267E8FB525
label: Peer-to-peer navigation between two pages
template: detail.hbs
op-migration-status: ready
ms.date: 07/13/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
ms.openlocfilehash: c35795eeb6afa32e77acb507c687e334e454d0d5
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "72560809"
---
# <a name="implement-navigation-between-two-pages"></a>Реализация навигации между двумя страницами

Узнайте, как использовать кадр и страницы, чтобы включить базовую одноранговую навигацию в приложении. 

> **Важные API**: класс [**Windows.UI.Xaml.Controls.Frame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame), класс [**Windows.UI.Xaml.Controls.Page**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page), пространство имен [**Windows.UI.Xaml.Navigation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Navigation)

![навигация между одноранговыми элементами](images/peertopeer.png)

## <a name="1-create-a-blank-app"></a>1. Создайте пустое приложение

1.  В меню Microsoft Visual Studio выберите **Файл** > **Создать проект**.
2.  На левой панели диалогового окна **Новый проект** выберите узел **Visual C#**  > **Windows** > **Universal** или **Visual C++**  > **Windows** > **Universal**.
3.  На центральной панели выберите **Пустое приложение**.
4.  В поле **Имя** введите **NavApp1**, а затем нажмите кнопку **ОК**.
    Решение создано, и файлы проекта отображаются в **обозревателе решений**.
5.  Чтобы запустить программу, выберите **Отладка** > **Начать отладку** в меню или нажмите клавишу F5.
    Отобразится пустая страница.
6.  Чтобы остановить отладку и вернуться в Visual Studio, выйдите из приложения или нажмите кнопку **Остановить отладку** в меню.

## <a name="2-add-basic-pages"></a>2. Добавление простых страниц

Далее добавьте в проект две страницы.

1.  В **обозревателе решений** щелкните правой кнопкой мыши узел проекта **BlankApp**, чтобы открыть контекстное меню.
2.  В контекстном меню выберите **Добавить** > **Новый элемент**.
3.  В диалоговом окне **Добавление нового элемента** на центральной панели выберите пункт **Пустая страница**.
4.  В поле **Имя** введите **Page1** (или **Page2**) и нажмите кнопку **Добавить**.
5. Чтобы добавить вторую страницу, повторите шаги 1–4.

Теперь эти файлы должны быть указаны в проекте NavApp1.

<table>
<thead>
<tr class="header">
<th align="left">C#</th>
<th align="left">C++</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="vertical-align:top;"><ul>
<li>Page1.xaml</li>
<li>Page1.xaml.cs</li>
<li>Page2.xaml</li>
<li>Page2.xaml.cs</li>
</ul></td>
<td style="vertical-align:top;"><ul>
<li>Page1.xaml</li>
<li>Page1.xaml.cpp</li>
<li>Page1.xaml.h</li>
<li>Page2.xaml</li>
<li>Page2.xaml.cpp</li>
<li>Page2.xaml.h

</li>
</ul></td>
</tr>
</tbody>
</table>

В файле Page1.xaml добавьте следующее содержимое:

-   Добавьте элемент [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) с именем `pageTitle` в качестве дочернего элемента корневого элемента [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid). Измените значение свойства [**Text**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.text), указав `Page 1`.
```xaml
<TextBlock x:Name="pageTitle" Text="Page 1" />
```

-   Добавьте элемент [**HyperlinkButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.HyperlinkButton) в качестве дочернего элемента корневого элемента [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) и после элемента `pageTitle` [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock).
```xaml
<HyperlinkButton Content="Click to go to page 2"
                 Click="HyperlinkButton_Click"
                 HorizontalAlignment="Center"/>
```

В файле кода программной части Page1.xaml добавьте следующий код для обработки события `Click` для [**HyperlinkButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.HyperlinkButton), которое было добавлено для перехода в файл Page2.xaml.

```csharp
private void HyperlinkButton_Click(object sender, RoutedEventArgs e)
{
    this.Frame.Navigate(typeof(Page2));
}
```

```cppwinrt
void Page1::HyperlinkButton_Click(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const& args)
{
    Frame().Navigate(winrt::xaml_typename<NavApp1::Page2>());
}
```

```cpp
void Page1::HyperlinkButton_Click(Platform::Object^ sender, RoutedEventArgs^ e)
{
    this->Frame->Navigate(Windows::UI::Xaml::Interop::TypeName(Page2::typeid));
}
```

В файле Page2.xaml добавьте следующее содержимое:

-   Добавьте элемент [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) с именем `pageTitle` в качестве дочернего элемента корневого элемента [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid). Измените значение свойства [**Text**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.text) на `Page 2`.
```xaml
<TextBlock x:Name="pageTitle" Text="Page 2" />
```

-   Добавьте элемент [**HyperlinkButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.HyperlinkButton) в качестве дочернего элемента корневого элемента [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) и после элемента `pageTitle` [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock).
```xaml
<HyperlinkButton Content="Click to go to page 1" 
                 Click="HyperlinkButton_Click"
                 HorizontalAlignment="Center"/>
```

В файле кода программной части Page2.xaml добавьте следующий код для обработки события `Click` для [**HyperlinkButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.HyperlinkButton), которое было добавлено для перехода в файл Page1.xaml.

```csharp
private void HyperlinkButton_Click(object sender, RoutedEventArgs e)
{
    this.Frame.Navigate(typeof(Page1));
}
```

```cppwinrt
void Page2::HyperlinkButton_Click(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const& args)
{
    Frame().Navigate(winrt::xaml_typename<NavApp1::Page1>());
}
```

```cpp
void Page2::HyperlinkButton_Click(Platform::Object^ sender, RoutedEventArgs^ e)
{
    this->Frame->Navigate(Windows::UI::Xaml::Interop::TypeName(Page1::typeid));
}
```

> [!NOTE]
> Для проектов C++ необходимо добавить директиву `#include` в файл заголовка каждой страницы, которая ссылается на другую страницу. В целях представленного здесь примера навигации между страницами файл page1.xaml.h содержит `#include "Page2.xaml.h"`, файл page2.xaml.h, в свою очередь, содержит `#include "Page1.xaml.h"`.

Теперь, когда мы подготовили страницы, нужно сделать так, чтобы при запуске приложения появлялся элемент Page1.xaml.

Откройте файл кода программной части app.xaml и измените обработчик `OnLaunched`.

Здесь мы определяем `Page1` в вызове [**Frame.Navigate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.navigate) вместо `MainPage`.

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
 
    // Do not repeat app initialization when the Window already has content,
    // just ensure that the window is active
    if (rootFrame == null)
    {
        // Create a Frame to act as the navigation context and navigate to the first page
        rootFrame = new Frame();
        rootFrame.NavigationFailed += OnNavigationFailed;
 
        if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
        {
            //TODO: Load state from previously suspended application
        }
 
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;
    }
 
    if (rootFrame.Content == null)
    {
        // When the navigation stack isn't restored navigate to the first page,
        // configuring the new page by passing required information as a navigation
        // parameter
        rootFrame.Navigate(typeof(Page1), e.Arguments);
    }
    // Ensure the current window is active
    Window.Current.Activate();
}
```

```cppwinrt
void App::OnLaunched(LaunchActivatedEventArgs const& e)
{
    Frame rootFrame{ nullptr };
    auto content = Window::Current().Content();
    if (content)
    {
        rootFrame = content.try_as<Frame>();
    }

    // Do not repeat app initialization when the Window already has content,
    // just ensure that the window is active
    if (rootFrame == nullptr)
    {
        // Create a Frame to act as the navigation context and associate it with
        // a SuspensionManager key
        rootFrame = Frame();

        rootFrame.NavigationFailed({ this, &App::OnNavigationFailed });

        if (e.PreviousExecutionState() == ApplicationExecutionState::Terminated)
        {
            // Restore the saved session state only when appropriate, scheduling the
            // final launch steps after the restore is complete
        }

        if (e.PrelaunchActivated() == false)
        {
            if (rootFrame.Content() == nullptr)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(xaml_typename<NavApp1::Page1>(), box_value(e.Arguments()));
            }
            // Place the frame in the current Window
            Window::Current().Content(rootFrame);
            // Ensure the current window is active
            Window::Current().Activate();
        }
    }
    else
    {
        if (e.PrelaunchActivated() == false)
        {
            if (rootFrame.Content() == nullptr)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(xaml_typename<NavApp1::Page1>(), box_value(e.Arguments()));
            }
            // Ensure the current window is active
            Window::Current().Activate();
        }
    }
}
```

```cpp
void App::OnLaunched(Windows::ApplicationModel::Activation::LaunchActivatedEventArgs^ e)
{
    auto rootFrame = dynamic_cast<Frame^>(Window::Current->Content);

    // Do not repeat app initialization when the Window already has content,
    // just ensure that the window is active
    if (rootFrame == nullptr)
    {
        // Create a Frame to act as the navigation context and associate it with
        // a SuspensionManager key
        rootFrame = ref new Frame();

        rootFrame->NavigationFailed += 
            ref new Windows::UI::Xaml::Navigation::NavigationFailedEventHandler(
                this, &App::OnNavigationFailed);

        if (e->PreviousExecutionState == ApplicationExecutionState::Terminated)
        {
            // TODO: Load state from previously suspended application
        }
        
        // Place the frame in the current Window
        Window::Current->Content = rootFrame;
    }

    if (rootFrame->Content == nullptr)
    {
        // When the navigation stack isn't restored navigate to the first page,
        // configuring the new page by passing required information as a navigation
        // parameter
        rootFrame->Navigate(Windows::UI::Xaml::Interop::TypeName(Page1::typeid), e->Arguments);
    }

    // Ensure the current window is active
    Window::Current->Activate();
}
```

> [!NOTE]
> В этом коде используется возвращаемое значение [**метода Frame.Navigate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.navigate), чтобы вызвать исключение приложения, если при переходе в начальный фрейм приложения происходит сбой. Если **Navigate** возвращает значение **true**, выполняется переход.

Теперь выполните сборку и запустите приложение. Щелкните ссылку «Click to go to page 2» (Нажмите, чтобы перейти к странице 2). Вторая страница с надписью «Page 2» (Страница 2) в верхней части загрузится и появится в фрейме.

### <a name="about-the-frame-and-page-classes"></a>О классах Frame и Page

Прежде чем расширить функциональные возможности приложения, посмотрим, как добавленные нами страницы обеспечивают поддержку навигации в приложении.

Сначала для приложения создается класс [**Frame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame) с именем `rootFrame` в методе `App.OnLaunched` файла кода программной части App.xaml. Класс **Frame** поддерживает различные методы навигации, например [**Navigate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.navigate), [**GoBack**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.goback) и [**GoForward**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.goforward), а также свойства, такие как [**BackStack**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.backstack), [**ForwardStack**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.forwardstack) и[ **BackStackDepth**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.backstackdepth).
 
Метод [**Navigate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.navigate) используется для отображения содержимого в этом **Frame**. По умолчанию этот метод загружает MainPage.xaml. В нашем примере `Page1`передается методу **Navigate**, поэтому метод загружает `Page1` в **Frame**. 

`Page1` является подклассом класса [**Page**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page). Класс **Page** имеет свойство только для чтения **Frame**, которое получает **Frame**, содержащий **Page**. Когда обработчик событий **Click** элемента **HyperlinkButton** в `Page1` вызывает `this.Frame.Navigate(typeof(Page2))`, элемент **Frame** отображает содержимое Page2.xaml.

В результате при загрузке страницы в фрейм эта страница добавляется как [**PageStackEntry**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Navigation.PageStackEntry) в параметр [**BackStack**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.backstack) или [**ForwardStack**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.forwardstack) объекта [**Frame**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.frame), что позволяет выполнять [навигацию по журналу и навигацию в обратном направлении](navigation-history-and-backwards-navigation.md).

## <a name="3-pass-information-between-pages"></a>3. Передача информации между страницами

Наше приложение переключается между страницами, но пока не умеет делать ничего интересного. Часто, если в приложении есть несколько страниц, необходим общий доступ к информации. Давайте передадим какую-нибудь информацию с первой страницы на вторую.

В Page1.xaml замените элемент **HyperlinkButton**, добавленный вами ранее, следующим классом [**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel).

Теперь добавим метку [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) и класс [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) (`name`) для ввода текстовой строки.

```xaml
<StackPanel>
    <TextBlock HorizontalAlignment="Center" Text="Enter your name"/>
    <TextBox HorizontalAlignment="Center" Width="200" Name="name"/>
    <HyperlinkButton Content="Click to go to page 2" 
                     Click="HyperlinkButton_Click"
                     HorizontalAlignment="Center"/>
</StackPanel>
```

В обработчике событий `HyperlinkButton_Click` для файла кода программной части Page1.xaml добавьте параметр, ссылающийся на `Text`свойство `name` **TextBox**, в методе `Navigate`.

```csharp
private void HyperlinkButton_Click(object sender, RoutedEventArgs e)
{
    this.Frame.Navigate(typeof(Page2), name.Text);
}
```

```cppwinrt
void Page1::HyperlinkButton_Click(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const& args)
{
    Frame().Navigate(winrt::xaml_typename<NavApp1::Page2>(), winrt::box_value(name().Text()));
}
```

```cpp
void Page1::HyperlinkButton_Click(Platform::Object^ sender, RoutedEventArgs^ e)
{
    this->Frame->Navigate(Windows::UI::Xaml::Interop::TypeName(Page2::typeid), name->Text);
}
```

В Page2.xaml замените элемент **HyperlinkButton**, добавленный вами ранее, следующим **StackPanel**.

Здесь мы добавляем класс [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) для отображения текстовой строки, передаваемой из Page1.

```xaml
<StackPanel>
    <TextBlock HorizontalAlignment="Center" Name="greeting"/>
    <HyperlinkButton Content="Click to go to page 1" 
                     Click="HyperlinkButton_Click"
                     HorizontalAlignment="Center"/>
</StackPanel>
```

В файле кода программной части Page2.xaml перезапишите метод `OnNavigatedTo` следующими данными:

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    if (e.Parameter is string && !string.IsNullOrWhiteSpace((string)e.Parameter))
    {
        greeting.Text = $"Hi, {e.Parameter.ToString()}";
    }
    else
    {
        greeting.Text = "Hi!";
    }
    base.OnNavigatedTo(e);
}
```

```cppwinrt
void Page2::OnNavigatedTo(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e)
{
    auto propertyValue{ e.Parameter().as<Windows::Foundation::IPropertyValue>() };
    if (propertyValue.Type() == Windows::Foundation::PropertyType::String)
    {
        greeting().Text(L"Hi, " + winrt::unbox_value<winrt::hstring>(e.Parameter()));
    }
    else
    {
        greeting().Text(L"Hi!");
    }
    __super::OnNavigatedTo(e);
}
```

```cpp
void Page2::OnNavigatedTo(NavigationEventArgs^ e)
{
    if (dynamic_cast<Platform::String^>(e->Parameter) != nullptr)
    {
        greeting->Text = "Hi, " + e->Parameter->ToString();
    }
    else
    {
        greeting->Text = "Hi!";
    }
    ::Windows::UI::Xaml::Controls::Page::OnNavigatedTo(e);
}
```

Запустите приложение, введите ваше имя в текстовое поле, а затем щелкните ссылку **Click to go to page 2** (Нажмите, чтобы перейти к странице 2). 

Когда событие **Click**, принадлежащее **HyperlinkButton** в `Page1`, вызывает метод `this.Frame.Navigate(typeof(Page2), name.Text)`, свойство `name.Text` передается `Page2`, а значение из данных события используется для сообщения, показанного на странице.

## <a name="4-cache-a-page"></a>4. Кэширование страницы

Содержимое и состояние страницы не кэшируются по умолчанию, поэтому если требуется кэшировать информацию, кэширование нужно включать для каждой страницы приложения.

В нашем простом примере кнопки возврата нет (мы покажем возможности обратной навигации в разделе [Навигация в обратном направлении](navigation-history-and-backwards-navigation.md)), но если вы нажали кнопку возврата на `Page2`, **TextBox** (и любое другое поле) на `Page1` будет установлено в состояние по умолчанию. Один способов обойти эту проблему — использовать свойство [**NavigationCacheMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.navigationcachemode), чтобы указать, что страница будет добавлена в кэш страницы фрейма. 

В конструкторе `Page1` можно задать параметру **NavigationCacheMode** значение **Enabled**, чтобы сохранить все содержимое и значения состояния для страницы, пока кэш страницы для кадра не превысит свой размер. Задайте параметру [**NavigationCacheMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.navigationcachemode) значение [**Required**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Navigation.NavigationCacheMode), если требуется игнорировать ограничения [**CacheSize**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.cachesize), обозначающие число страниц в журнале навигации, которые можно кэшировать для кадра. Однако следует помнить, что ограничения размера кэша могут быть критическими в зависимости от ограничений памяти устройства.

```csharp
public Page1()
{
    this.InitializeComponent();
    this.NavigationCacheMode = Windows.UI.Xaml.Navigation.NavigationCacheMode.Enabled;
}
```

```cppwinrt
Page1::Page1()
{
    InitializeComponent();
    NavigationCacheMode(Windows::UI::Xaml::Navigation::NavigationCacheMode::Enabled);
}
```

```cpp
Page1::Page1()
{
    this->InitializeComponent();
    this->NavigationCacheMode = Windows::UI::Xaml::Navigation::NavigationCacheMode::Enabled;
}
```

## <a name="related-articles"></a>Похожие статьи
* [Основы проектирования навигации для приложений UWP](https://docs.microsoft.com/windows/uwp/layout/navigation-basics)
* [Сводка](../controls-and-patterns/pivot.md)
* [Представление навигации](../controls-and-patterns/navigationview.md)
