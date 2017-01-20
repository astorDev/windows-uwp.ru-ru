---
author: Jwmsft
Description: "Узнайте, как перемещаться в простом одноранговом приложении универсальной платформы Windows (UWP) с двумя страницами."
title: "Одноранговая навигация между двумя страницами"
ms.assetid: 0A364C8B-715F-4407-9426-92267E8FB525
label: Peer-to-peer navigation between two pages
template: detail.hbs
op-migration-status: ready
translationtype: Human Translation
ms.sourcegitcommit: a3924fef520d7ba70873d6838f8e194e5fc96c62
ms.openlocfilehash: 324bdcd8ae61826a2be765f6a6a93441036d6984

---

# <a name="peer-to-peer-navigation-between-two-pages"></a>Одноранговая навигация между двумя страницами

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Узнайте, как перемещаться в простом одноранговом приложении универсальной платформы Windows (UWP) с двумя страницами.

![Пример одноранговой навигации между двумя страницами](images/nav-peertopeer-2page.png)

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Windows.UI.Xaml.Controls.Frame**](https://msdn.microsoft.com/library/windows/apps/br242682)</li>
<li>[**Windows.UI.Xaml.Controls.Page**](https://msdn.microsoft.com/library/windows/apps/br227503)</li>
<li>[**Windows.UI.Xaml.Navigation**](https://msdn.microsoft.com/library/windows/apps/br243300)</li>
</ul>
</div>



## <a name="create-the-blank-app"></a>Создание пустого приложения


1.  В меню Microsoft Visual Studio выберите **Файл &gt; Создать проект**.
2.  На левой панели диалогового окна **Новый проект** выберите узел **Visual C# &gt; Windows &gt; Universal** или **Visual C++ &gt; Windows &gt; Universal**.
3.  На центральной панели выберите **Пустое приложение**.
4.  В поле **Имя** введите **NavApp1**, а затем нажмите кнопку **ОК**.

    Решение создано, и файлы проекта отображаются в **обозревателе решений**.

5.  Чтобы запустить программу, выберите **Отладка** &gt; **Начать отладку** в меню или нажмите клавишу F5.

    Отобразится пустая страница.

6.  Нажмите клавиши SHIFT+F5, чтобы остановить отладку и вернуться в Visual Studio.

## <a name="add-basic-pages"></a>Добавление простых страниц

Далее добавьте в проект две страницы с содержимым.

Выполните следующие действия дважды, чтобы добавить две страницы для перемещения между ними.

1.  В **обозревателе решений** щелкните правой кнопкой мыши узел проекта **BlankApp**, чтобы открыть контекстное меню.
2.  В контекстном меню выберите **Добавить** &gt; **Новый элемент**.
3.  В диалоговом окне **Добавление нового элемента** на центральной панели выберите пункт **Пустая страница**.
4.  В поле **Имя** введите **Page1** (или **Page2**) и нажмите кнопку **Добавить**.

Эти файлы теперь должны быть указаны в проекте NavApp1.

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

 

Добавьте следующее содержимое в пользовательский интерфейс Page1.xaml.

-   Добавьте элемент [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652) с именем `pageTitle` в качестве дочернего элемента корневого элемента [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704). Измените значение свойства [**Text**](https://msdn.microsoft.com/library/windows/apps/br209676), указав `Page 1`.

```xaml
<TextBlock x:Name="pageTitle" Text="Page 1" />
```

-   Добавьте следующий элемент [**HyperlinkButton**](https://msdn.microsoft.com/library/windows/apps/br242739) в качестве дочернего элемента корневого элемента [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704) и после элемента `pageTitle`[**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652).

    
```xaml
<HyperlinkButton Content="Click to go to page 2"
                 Click="HyperlinkButton_Click"
                 HorizontalAlignment="Center"/>
```

Добавьте следующий код в класс `Page1` в файле кода программной части Page1.xaml для обработки события `Click` для [**HyperlinkButton**](https://msdn.microsoft.com/library/windows/apps/br242739), добавленного ранее. Здесь мы переходим на страницу Page2.xaml.

> [!div class="tabbedCodeSnippets"]
```csharp
private void HyperlinkButton_Click(object sender, RoutedEventArgs e)
{
    this.Frame.Navigate(typeof(Page2));
}
```
```cpp
void Page1::HyperlinkButton_Click(Platform::Object^ sender, RoutedEventArgs^ e)
{
    this->Frame->Navigate(Windows::UI::Xaml::Interop::TypeName(Page2::typeid));
}
```

Измените пользовательский интерфейс страницы Page2.xaml следующим образом.

-   Добавьте элемент [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652) с именем `pageTitle` в качестве дочернего элемента корневого элемента [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704). Измените значение свойства [**Text**](https://msdn.microsoft.com/library/windows/apps/br209676) на `Page 2`.

```xaml
<TextBlock x:Name="pageTitle" Text="Page 2" />
```

-   Добавьте следующий элемент [**HyperlinkButton**](https://msdn.microsoft.com/library/windows/apps/br242739) в качестве дочернего элемента корневого элемента [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704) и после элемента `pageTitle`[**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652).

```xaml
<HyperlinkButton Content="Click to go to page 1" 
                 Click="HyperlinkButton_Click"
                 HorizontalAlignment="Center"/>
```

Добавьте следующий код в класс `Page2` в файле кода программной части Page2.xaml для обработки события `Click` для [**HyperlinkButton**](https://msdn.microsoft.com/library/windows/apps/br242739), добавленного ранее. Здесь мы переходим на страницу Page1.xaml.

> [!NOTE]
> Для проектов C++ необходимо добавить директиву `#include` в файл заголовка каждой страницы, которая ссылается на другую страницу. В целях представленного здесь примера навигации между страницами файл page1.xaml.h содержит `#include "Page2.xaml.h"`, файл page2.xaml.h, в свою очередь, содержит `#include "Page1.xaml.h"`.

> [!div class="tabbedCodeSnippets"]
```csharp
private void HyperlinkButton_Click(object sender, RoutedEventArgs e)
{
    this.Frame.Navigate(typeof(Page1));
}
```
```cpp
void Page2::HyperlinkButton_Click(Platform::Object^ sender, RoutedEventArgs^ e)
{
    this->Frame->Navigate(Windows::UI::Xaml::Interop::TypeName(Page1::typeid));
}
```

Теперь, когда мы подготовили страницы с содержимым, нужно сделать так, чтобы при запуске приложения появлялся элемент Page1.xaml.

Откройте файл кода программной части app.xaml и измените обработчик `OnLaunched`.

Здесь мы определяем `Page1` в вызове [**Frame.Navigate**](https://msdn.microsoft.com/library/windows/apps/br242694) вместо `MainPage`.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> protected override void OnLaunched(LaunchActivatedEventArgs e)
> {
>     Frame rootFrame = Window.Current.Content as Frame;
> 
>     // Do not repeat app initialization when the Window already has content,
>     // just ensure that the window is active
>     if (rootFrame == null)
>     {
>         // Create a Frame to act as the navigation context and navigate to the first page
>         rootFrame = new Frame();
> 
>         rootFrame.NavigationFailed += OnNavigationFailed;
> 
>         if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
>         {
>             //TODO: Load state from previously suspended application
>         }
> 
>         // Place the frame in the current Window
>         Window.Current.Content = rootFrame;
>     }
> 
>     if (rootFrame.Content == null)
>     {
>         // When the navigation stack isn&#39;t restored navigate to the first page,
>         // configuring the new page by passing required information as a navigation
>         // parameter
>         rootFrame.Navigate(typeof(Page1), e.Arguments);
>     }
>     // Ensure the current window is active
>     Window.Current.Activate();
> }
> ```
> ```cpp
> void App::OnLaunched(Windows::ApplicationModel::Activation::LaunchActivatedEventArgs^ e)
> {
>     auto rootFrame = dynamic_cast<Frame^>(Window::Current->Content);
> 
>     // Do not repeat app initialization when the Window already has content,
>     // just ensure that the window is active
>     if (rootFrame == nullptr)
>     {
>         // Create a Frame to act as the navigation context and associate it with
>         // a SuspensionManager key
>         rootFrame = ref new Frame();
> 
>         rootFrame->NavigationFailed += 
>             ref new Windows::UI::Xaml::Navigation::NavigationFailedEventHandler(
>                 this, &amp;App::OnNavigationFailed);
> 
>         if (e->PreviousExecutionState == ApplicationExecutionState::Terminated)
>         {
>             // TODO: Load state from previously suspended application
>         }
>         
>         // Place the frame in the current Window
>         Window::Current->Content = rootFrame;
>     }
> 
>     if (rootFrame->Content == nullptr)
>     {
>         // When the navigation stack isn&#39;t restored navigate to the first page,
>         // configuring the new page by passing required information as a navigation
>         // parameter
>         rootFrame->Navigate(Windows::UI::Xaml::Interop::TypeName(Page1::typeid), e->Arguments);
>     }
> 
>     // Ensure the current window is active
>     Window::Current->Activate();
> }
> ```

**Примечание.** В этом коде используется возвращаемое значение [**Navigate**](https://msdn.microsoft.com/library/windows/apps/br242694), чтобы вызвать исключение приложения, если при переходе в начальный фрейм приложения происходит сбой. Если **Navigate** возвращает значение **true**, выполняется переход.

Теперь выполните сборку и запустите приложение. Щелкните ссылку «Click to go to page 2» (Нажмите, чтобы перейти к странице 2). Вторая страница с надписью «Page 2» (Страница 2) в верхней части загрузится и появится в фрейме.

## <a name="frame-and-page-classes"></a>Классы Frame и Page

Прежде чем расширить функциональные возможности приложения, посмотрим, как добавленные нами страницы обеспечивают поддержку навигации в приложении.

Сначала для приложения создается [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) (`rootFrame`) в методе `App.OnLaunched` файла кода программной части App.xaml. Метод [**Navigate**](https://msdn.microsoft.com/library/windows/apps/br242694) используется для отображения содержимого в этом **Frame**.

**Примечание.**  
Класс [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) поддерживает различные методы навигации, например [**Navigate**](https://msdn.microsoft.com/library/windows/apps/br242694), [**GoBack**](https://msdn.microsoft.com/library/windows/apps/dn996568) и [**GoForward**](https://msdn.microsoft.com/library/windows/apps/br242693), а также свойства, такие как [**BackStack**](https://msdn.microsoft.com/library/windows/apps/dn279543), [**ForwardStack**](https://msdn.microsoft.com/library/windows/apps/dn279547) и [**BackStackDepth**](https://msdn.microsoft.com/library/windows/apps/hh967995).

 
В нашем примере `Page1` передается методу [**Navigate**](https://msdn.microsoft.com/library/windows/apps/br242694). Этот метод назначает содержимое текущего окна приложения классу [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) и загружает содержимое страницы, указанной в **Frame** (в нашем примере это Page1.xaml, по умолчанию — MainPage.xaml).

`Page1` является подклассом класса [**Page**](https://msdn.microsoft.com/library/windows/apps/br227503). Класс **Page** имеет свойство только для чтения [**Frame**](https://msdn.microsoft.com/library/windows/apps/br227504), которое получает [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682), содержащий **Page**. Когда обработчик событий [**Click**](https://msdn.microsoft.com/library/windows/apps/br227737) элемента [**HyperlinkButton**](https://msdn.microsoft.com/library/windows/apps/br242739) вызывает ` Frame.Navigate(typeof(Page2))`, элемент **Frame** в окне приложения отображает содержимое Page2.xaml.

При загрузке страницы в фрейм эта страница добавляется как [**PageStackEntry**](https://msdn.microsoft.com/library/windows/apps/dn298572) в параметр [**BackStack**](https://msdn.microsoft.com/library/windows/apps/dn279543) или [**ForwardStack**](https://msdn.microsoft.com/library/windows/apps/dn279547) объекта [**Frame**](https://msdn.microsoft.com/library/windows/apps/br227504).

## <a name="pass-information-between-pages"></a>Передача информации между страницами

Наше приложение переключается между страницами, но пока не умеет делать ничего интересного. Часто, если в приложении есть несколько страниц, необходим общий доступ к информации. Давайте передадим какую-нибудь информацию с первой страницы на вторую.

В Page1.xaml замените элемент [**HyperlinkButton**](https://msdn.microsoft.com/library/windows/apps/br242739), добавленный вами ранее, следующим [**StackPanel**](https://msdn.microsoft.com/library/windows/apps/br209635).

Теперь добавим метку [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652) и [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) (`name`) для ввода текстовой строки.

```xaml
<StackPanel>
    <TextBlock HorizontalAlignment="Center" Text="Enter your name"/>
    <TextBox HorizontalAlignment="Center" Width="200" Name="name"/>
    <HyperlinkButton Content="Click to go to page 2" 
                     Click="HyperlinkButton_Click"
                     HorizontalAlignment="Center"/>
</StackPanel>
```

В обработчике событий `HyperlinkButton_Click` для файла кода программной части Page1.xaml добавьте параметр, ссылающийся на свойство `Text` `name` [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683), в метод `Navigate`.

> [!div class="tabbedCodeSnippets"]
```csharp
private void HyperlinkButton_Click(object sender, RoutedEventArgs e)
{
    this.Frame.Navigate(typeof(Page2), name.Text);
}
```
```cpp
void Page1::HyperlinkButton_Click(Platform::Object^ sender, RoutedEventArgs^ e)
{
    this->Frame->Navigate(Windows::UI::Xaml::Interop::TypeName(Page2::typeid), name->Text);
}
```

В файле кода программной части Page2.xaml перезапишите метод `OnNavigatedTo` следующими данными:

> [!div class="tabbedCodeSnippets"]
```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    if (e.Parameter is string)
    {
        greeting.Text = "Hi, " + e.Parameter.ToString();
    }
    else
    {
        greeting.Text = "Hi!";
    }
    base.OnNavigatedTo(e);
}
```
```cpp
void Page2::OnNavigatedTo(NavigationEventArgs^ e)
{
    if (dynamic_cast<Platform::String^>(e->Parameter) != nullptr)
    {
        greeting->Text = "Hi," + e->Parameter->ToString();
    }
    else
    {
        greeting->Text = "Hi!";
    }
    ::Windows::UI::Xaml::Controls::Page::OnNavigatedTo(e);
}
```

Запустите приложение, введите ваше имя в текстовое поле, а затем щелкните ссылку **Click to go to page 2** (Нажмите, чтобы перейти к странице 2). Когда вы вызвали метод `this.Frame.Navigate(typeof(Page2), tb1.Text)` в событии [**Click**](https://msdn.microsoft.com/library/windows/apps/br227737) [**HyperlinkButton**](https://msdn.microsoft.com/library/windows/apps/br242739), свойство `name.Text` было передано `Page2`, а значение из данных события использовалось для сообщения, показанного на странице.

## <a name="cache-a-page"></a>Кэширование страницы

Содержимое и состояние страницы не кэшируются по умолчанию, поэтому кэширование нужно включать для каждой страницы приложения.

В нашем простом примере кнопки возврата нет (мы покажем возможности обратной навигации в разделе [Навигация кнопки «Назад»](navigation-history-and-backwards-navigation.md)), но если вы нажали кнопку возврата на `Page2`, [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) (и любое другое поле) на `Page1` будет установлено в состояние по умолчанию. Один способов обойти эту проблему — использовать свойство [**NavigationCacheMode**](https://msdn.microsoft.com/library/windows/apps/br227506), чтобы указать, что страница будет добавлена в кэш страницы фрейма.

В конструкторе страницы `Page1` установите для [**NavigationCacheMode**](https://msdn.microsoft.com/library/windows/apps/br227506) значение [**Enabled**](https://msdn.microsoft.com/library/windows/apps/br243284). При этом все содержимое и значения состояния страницы сохраняются, пока не будет превышен размер кэша страницы для фрейма.

Установите для [**NavigationCacheMode**](https://msdn.microsoft.com/library/windows/apps/br227506) значение [**Required**](https://msdn.microsoft.com/library/windows/apps/br243284), чтобы игнорировать ограничения размера кэша для фрейма. Однако ограничения размера кэша могут быть критическими в зависимости от ограничений памяти устройства.

> [!NOTE]
> Свойство [**CacheSize**](https://msdn.microsoft.com/library/windows/apps/br242683) определяет число страниц в журнале навигации, которые могут быть помещены в кэш фрейма.

> [!div class="tabbedCodeSnippets"]
```csharp
public Page1()
{
    this.InitializeComponent();
    this.NavigationCacheMode = Windows.UI.Xaml.Navigation.NavigationCacheMode.Enabled;
}
```
```cpp
Page1::Page1()
{
    this->InitializeComponent();
    this->NavigationCacheMode = Windows::UI::Xaml::Navigation::NavigationCacheMode::Enabled;
}
```

## <a name="related-articles"></a>Связанные разделы

* [Основы проектирования навигации для приложений UWP](https://msdn.microsoft.com/library/windows/apps/dn958438)
* [Руководство по элементам управления «Сводка» и вкладкам](https://msdn.microsoft.com/library/windows/apps/dn997788)
* [Руководство по панелям навигации](https://msdn.microsoft.com/library/windows/apps/dn997766)
 

 







<!--HONumber=Dec16_HO2-->


