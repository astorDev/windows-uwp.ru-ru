---
Description: Узнайте, как реализовать обратную навигацию для обхода история переходов данного пользователя в приложении универсальной платформы Windows.
title: Журнал навигации и навигация в обратном направлении (приложения для Windows)
template: detail.hbs
op-migration-status: ready
ms.date: 06/21/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c74d4ebd08dfeddfb4a0149cffcd7bb845ceff11
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57595049"
---
# <a name="navigation-history-and-backwards-navigation-for-uwp-apps"></a>Журнал навигации и навигация в обратном направлении для приложений UWP

> **Важные API**: [Событие BackRequested](https://docs.microsoft.com/uwp/api/Windows.UI.Core.SystemNavigationManager.BackRequested), [класс SystemNavigationManager](https://docs.microsoft.com/uwp/api/Windows.UI.Core.SystemNavigationManager), [OnNavigatedTo](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto#Windows_UI_Xaml_Controls_Page_OnNavigatedTo_Windows_UI_Xaml_Navigation_NavigationEventArgs_)

Универсальная платформа Windows (UWP) обеспечивает единообразие навигации в обратном направлении в виде системы возврата на предыдущий уровень, позволяя пользователю перемещаться по журналу навигации в приложении и, в зависимости от устройства, из приложения в приложение.

Чтобы реализовать обратную навигацию в приложении, разместите [кнопку "Назад"](#Back-button) в верхнем левом углу пользовательского интерфейса приложения. Если в приложении используется элемент управления [NavigationView](../controls-and-patterns/navigationview.md), вы можете использовать [встроенную кнопку "Назад" для NavigationView](../controls-and-patterns/navigationview.md#backwards-navigation).

Пользователь ожидает что кнопка «Назад» позволит ему перейти на предыдущую страницу истории навигации приложения. Обратите внимание, что именно вы решаете, какие действия навигации добавлять в журнал навигации и как реагировать на нажатие кнопки «Назад».

## <a name="back-button"></a>Кнопка "Назад"

Для создания кнопки "Назад", используйте [кнопку](../controls-and-patterns/buttons.md) управления `NavigationBackButtonNormalStyle` параметрами и поместить кнопку в левом верхнем углу пользовательского интерфейса приложения (Дополнительные сведения см. в примерах кода XAML ниже).

![Кнопка "Назад" в верхнем левом углу пользовательского интерфейса приложения](images/back-nav/BackEnabled.png)

```xaml
<Button Style="{StaticResource NavigationBackButtonNormalStyle}"/>
```

Если в верхней части приложения есть [CommandBar](../controls-and-patterns/app-bars.md), элемент управления "Кнопка" в 44px в высоту будет не совпадать по высоте с кнопками AppBarButtons по 48px в высоту. Тем не менее, чтобы избежать несогласованности в размере, выровняйте верхнюю часть элемента управления "Кнопка" по внутренней части границ 48px.

![Кнопка "Назад" на верхней панели команд](images/back-nav/CommandBar.png)

```xaml
<Button VerticalAlignment="Top" HorizontalAlignment="Left" 
Style="{StaticResource NavigationBackButtonNormalStyle}"/>
```

Чтобы свести к минимуму перемещение элементов пользовательского интерфейса в приложении, отобразите отключенную кнопку "Назад" при отсутствии элементов в стеке переходов назад (см. пример кода ниже). Тем не менее если предполагается, что ваше приложение никогда не будет частях, не требуется для отображения кнопки «Назад» вообще.

![Состояния кнопки "Назад"](images/back-nav/BackDisabled.png)

## <a name="code-example"></a>Пример кода

В следующем примере кода показано, как реализовать поведение обратной навигации с помощью кнопки "Назад". Код реагирует на событие [**Click**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.Click) кнопки и отключает/включает видимость кнопки в [**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto#Windows_UI_Xaml_Controls_Page_OnNavigatedTo_Windows_UI_Xaml_Navigation_NavigationEventArgs_), который вызывается при переходе к новой странице. В примере кода также представлена обработка данных, вводимых с помощью аппаратных и программных клавиш "Назад" системы, за счет регистрации прослушивателя для события [**BackRequested**](https://docs.microsoft.com/uwp/api/windows.ui.core.systemnavigationmanager.BackRequested).

```xaml
<!-- MainPage.xaml -->
<Page x:Class="AppName.MainPage">
...
<Button x:Name="BackButton" Click="Back_Click" Style="{StaticResource NavigationBackButtonNormalStyle}"/>
...
<Page/>
```

Поддерживающий код

```csharp
// MainPage.xaml.cs
public MainPage()
{
    KeyboardAccelerator GoBack = new KeyboardAccelerator();
    GoBack.Key = VirtualKey.GoBack;
    GoBack.Invoked += BackInvoked;
    KeyboardAccelerator AltLeft = new KeyboardAccelerator();
    AltLeft.Key = VirtualKey.Left;
    AltLeft.Invoked += BackInvoked;
    this.KeyboardAccelerators.Add(GoBack);
    this.KeyboardAccelerators.Add(AltLeft);
    // ALT routes here
    AltLeft.Modifiers = VirtualKeyModifiers.Menu;
}

protected override void OnNavigatedTo(NavigationEventArgs e)
{
    BackButton.IsEnabled = this.Frame.CanGoBack;
}

private void Back_Click(object sender, RoutedEventArgs e)
{
    On_BackRequested();
}

// Handles system-level BackRequested events and page-level back button Click events
private bool On_BackRequested()
{
    if (this.Frame.CanGoBack)
    {
        this.Frame.GoBack();
        return true;
    }
    return false;
}

private void BackInvoked (KeyboardAccelerator sender, KeyboardAcceleratorInvokedEventArgs args)
{
    On_BackRequested();
    args.Handled = true;
}
```

```cppwinrt
// MainPage.cpp
#include "pch.h"
#include "MainPage.h"

#include "winrt/Windows.System.h"
#include "winrt/Windows.UI.Xaml.Controls.h"
#include "winrt/Windows.UI.Xaml.Input.h"
#include "winrt/Windows.UI.Xaml.Navigation.h"

using namespace winrt;
using namespace Windows::Foundation;
using namespace Windows::UI::Xaml;

namespace winrt::PageNavTest::implementation
{
    MainPage::MainPage()
    {
        InitializeComponent();

        Windows::UI::Xaml::Input::KeyboardAccelerator goBack;
        goBack.Key(Windows::System::VirtualKey::GoBack);
        goBack.Invoked({ this, &MainPage::BackInvoked });
        Windows::UI::Xaml::Input::KeyboardAccelerator altLeft;
        altLeft.Key(Windows::System::VirtualKey::Left);
        altLeft.Invoked({ this, &MainPage::BackInvoked });
        KeyboardAccelerators().Append(goBack);
        KeyboardAccelerators().Append(altLeft);
        // ALT routes here.
        altLeft.Modifiers(Windows::System::VirtualKeyModifiers::Menu);
    }

    void MainPage::OnNavigatedTo(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e)
    {
        BackButton().IsEnabled(Frame().CanGoBack());
    }

    void MainPage::Back_Click(IInspectable const&, RoutedEventArgs const&)
    {
        On_BackRequested();
    }

    // Handles system-level BackRequested events and page-level back button Click events.
    bool MainPage::On_BackRequested()
    {
        if (Frame().CanGoBack())
        {
            Frame().GoBack();
            return true;
        }
        return false;
    }

    void MainPage::BackInvoked(Windows::UI::Xaml::Input::KeyboardAccelerator const& sender,
        Windows::UI::Xaml::Input::KeyboardAcceleratorInvokedEventArgs const& args)
    {
        args.Handled(On_BackRequested());
    }
}
```

Выше мы обрабатываем обратной навигации на одной странице. Если вы хотите исключить определенные страницы из переходов назад, или вы хотите выполнить код уровня страницы перед отображением страницы можно обрабатывать навигации на каждой странице.

Для обработки обратной навигации для всего приложения, будет необходимо зарегистрировать глобальные прослушивателя для [ **BackRequested** ](https://docs.microsoft.com/uwp/api/windows.ui.core.systemnavigationmanager.BackRequested) событие в `App.xaml` файл с выделенным кодом.

Код программной части App.xaml

```csharp
// App.xaml.cs
Windows.UI.Core.SystemNavigationManager.GetForCurrentView().BackRequested += App_BackRequested;
Frame rootFrame = Window.Current.Content;
rootFrame.PointerPressed += On_PointerPressed;

private void App_BackRequested(object sender, Windows.UI.Core.BackRequestedEventArgs e)
{
    e.Handled = On_BackRequested();
}

private void On_PointerPressed(object sender, PointerRoutedEventArgs e)
{
    bool isXButton1Pressed =
        e.GetCurrentPoint(sender as UIElement).Properties.PointerUpdateKind == PointerUpdateKind.XButton1Pressed;

    if (isXButton1Pressed)
    {
        e.Handled = On_BackRequested();
    }
}

private bool On_BackRequested()
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        rootFrame.GoBack();
        return true;
    }
    return false;
}
```

```cppwinrt
// App.cpp
#include <winrt/Windows.UI.Core.h>
#include "winrt/Windows.UI.Input.h"
#include "winrt/Windows.UI.Xaml.Input.h"

#include "App.h"
#include "MainPage.h"

using namespace winrt;
...

    Windows::UI::Core::SystemNavigationManager::GetForCurrentView().BackRequested({ this, &App::App_BackRequested });
    Frame rootFrame{ nullptr };
    auto content = Window::Current().Content();
    if (content)
    {
        rootFrame = content.try_as<Frame>();
    }
    rootFrame.PointerPressed({ this, &App::On_PointerPressed });
...

void App::App_BackRequested(IInspectable const& /* sender */, Windows::UI::Core::BackRequestedEventArgs const& e)
{
    e.Handled(On_BackRequested());
}

void App::On_PointerPressed(IInspectable const& sender, Windows::UI::Xaml::Input::PointerRoutedEventArgs const& e)
{
    bool isXButton1Pressed =
        e.GetCurrentPoint(sender.as<UIElement>()).Properties().PointerUpdateKind() == Windows::UI::Input::PointerUpdateKind::XButton1Pressed;

    if (isXButton1Pressed)
    {
        e.Handled(On_BackRequested());
    }
}

// Handles system-level BackRequested events.
bool App::On_BackRequested()
{
    if (Frame().CanGoBack())
    {
        Frame().GoBack();
        return true;
    }
    return false;
}
```

## <a name="optimizing-for-different-device-and-form-factors"></a>Оптимизация для разных устройств и форм-факторов

Это руководство по реализации обратной навигации применимо ко всем устройствам, однако за счет оптимизации различных устройств и форм-факторов можно достигнуть лучших результатов. Это также зависит от аппаратной кнопки "Назад", поддерживаемой разными оболочками.

- **Телефона или планшета**: Кнопки возврата оборудования или программного обеспечения всегда является отсутствует на мобильных устройствах и планшета, но мы рекомендуем рисования в приложении кнопки "Назад" для ясности.
- **Рабочий стол и узловых**: Нарисуйте "Назад" в приложении в левом верхнем углу пользовательского интерфейса приложения.
- **Xbox и Телевизионных**: Не рисования кнопки возврата, для добавит ненужные элементы пользовательского интерфейса. Вместо этого для обратной навигации используйте кнопку B на геймпаде.

Если ваше приложение будет выполняться на нескольких устройствах, [создайте пользовательский визуальный триггер для Xbox](../devices/designing-for-tv.md#custom-visual-state-trigger-for-xbox) для переключения состояния видимости кнопки. Элемент управления NavigationView будет автоматически переключать состояние видимости кнопки "Назад", если ваше приложение работает на консоли Xbox. 

Мы рекомендуем реализовать поддержку следующих способов ввода для обратной навигации. (Обратите внимание, что некоторые из этих способов ввода не поддерживаются BackRequested системы и должны обрабатываться отдельными событиями.)

| Input | Событие |
| --- | --- |
| Клавиша Backspace в Windows | BackRequested |
| Аппаратная кнопка "Назад" | BackRequested |
| Кнопка "Назад" режима планшета оболочки | BackRequested |
| VirtualKey.XButton1 | PointerPressed |
| VirtualKey.GoBack | KeyboardAccelerator.BackInvoked |
| ALT+СТРЕЛКА ВЛЕВО | KeyboardAccelerator.BackInvoked |

В примерах кода, приведенных выше, показано, как обрабатывать все эти действия ввода.

## <a name="system-back-behavior-for-backward-compatibilities"></a>Обратная навигация системы в целях обеспечения обратной совместимости

Ранее в приложениях UWP использовался API [AppViewBackButtonVisibility](https://docs.microsoft.com/uwp/api/windows.ui.core.appviewbackbuttonvisibility) для обратной навигации. API по-прежнему поддерживаются для обеспечения обратной совместимости, но мы не рекомендуем полагаться на [AppViewBackButtonVisibility](https://docs.microsoft.com/uwp/api/windows.ui.core.appviewbackbuttonvisibility). Вместо этого в приложении следует нарисовать собственную кнопку "Назад".

Если ваше приложение продолжает с помощью [AppViewBackButtonVisibility](https://docs.microsoft.com/uwp/api/windows.ui.core.appviewbackbuttonvisibility), а затем пользовательский Интерфейс системы будут отображаться кнопки возврата системы внутри заголовка окна. (Взаимодействий внешний вид и пользователя для кнопки «Назад» не отличаются от предыдущих сборок.)

![Заголовка окна кнопки "Назад"](images/nav-back-pc.png)

### <a name="system-back-bar"></a>Резервное копирование системы панели

> [!NOTE]
> «Резервное копирование системы панель» приведено только описание, не является официальным именем.

Резервное копирование системы является строка «диапазон», который вставляется между аппаратного контроллера управления вкладки и области содержимого приложения. Полоса проходит по ширине приложения, а кнопка «Назад» находится у левой границы. Полоса содержит высота 32 пикселя, чтобы обеспечить достаточный сенсорной целевой размер для кнопки «Назад».

Системная строка возврата отображается динамически в зависимости от видимости кнопки «Назад». Кнопки «Назад» отображается, если резервное копирование системы вставляется строка, сдвиг содержимое приложения на 32 пикселя ниже аппаратного контроллера управления вкладки. Если скрыт "Назад", резервное копирование системы строке будет динамически удален, сдвигая содержимое приложения 32 пикселя в соответствии с аппаратного контроллера управления tab. Чтобы избежать необходимости shift пользовательского интерфейса приложения вверх или вниз, мы рекомендуем Рисование [кнопки "Назад" в приложении](#back-button).

[Строка настройки заголовка](../shell/title-bar.md) будет перенесено на вкладке приложения и системы обратно строке. Если приложение определяет свойства цвета фона и переднего плана с [ApplicationViewTitleBar](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationviewtitlebar), то цвета, которые будут применяться к задней вкладка и системы панели.

## <a name="guidelines-for-custom-back-navigation-behavior"></a>Рекомендации по пользовательской настройке обратной навигации

Если вы решили предусмотреть собственную навигацию обратного стека, данная функция должна быть совместима с другими приложениями. Рекомендуем использовать описанные ниже шаблоны действий навигации.

<div class="mx-responsive-img">
<table>
<thead>
<tr class="header">
<th align="left">Действие навигации</th>
<th align="left">Добавить в историю навигации?</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="vertical-align:top;"><strong>Страница для разбиения на страницы, различные одноранговые группы</strong></td>
<td style="vertical-align:top;"><strong>Да</strong>
<p>На данной иллюстрации пользователь переходит от уровня 1 к уровню 2 приложения, перемещаясь из одной группы одноранговых элементов к другой, поэтому переходы добавляются в журнал навигации.</p>
<p><img src="images/back-nav/nav-pagetopage-diffpeers-imageonly1.png" alt="Navigation across peer groups" /></p>
<p>На следующей иллюстрации пользователь перемещается между двумя группами одноранговых элементов, находящихся на одном и том же уровне, снова переходя от группы к группе, поэтому его перемещение добавляется в журнал навигации.</p>
<p><img src="images/back-nav/nav-pagetopage-diffpeers-imageonly2.png" alt="Navigation across peer groups" /></p></td>
</tr>
<tr class="even">
<td style="vertical-align:top;"><strong>Страницы на страницу, так же однорангового группы, не на экране элемент навигации</strong>
<p>Пользователь переходит от одной страницы к другой в пределах одной и той же группы одноранговых элементов. Имеется отсутствует экранное элемент навигации (такие как <a href="https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/navigationview">NavigationView</a>), предоставляющий прямой переход к любой из страниц.</p></td>
<td style="vertical-align:top;"><strong>Да</strong>
<p>На следующем рисунке пользователь переходит между двумя страницами в той же группе однорангового узла и навигации должны добавляться в журнал переходов.</p>
<p><img src="images/back-nav/nav-pagetopage-samepeer-noosnavelement.png" alt="Navigation within a peer group" /></p></td>
</tr>
<tr class="odd">
<td style="vertical-align:top;"><strong>Страницы на страницу, одной группе однорангового узла, с помощью на экране элемент навигации</strong>
<p>Пользователь переходит от одной страницы к другой в пределах одной и той же группы одноранговых элементов. Обе страницы отображаются в том же элементе навигации, такие как <a href="https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/navigationview">NavigationView</a>.</p></td>
<td style="vertical-align:top;"><strong>Смотря как</strong>
<p>Да, добавьте в журнал переходов, с двумя исключениями. Если ожидается, что пользователи вашего приложения, для переключения между страницами в одноранговой группы часто или если вы хотите сохранить иерархию навигации, затем не добавляйте в журнал переходов. В этом случае, когда пользователь нажимает кнопку "Назад", выполните переход к последней странице, на которой он находился перед переходом к текущей одноранговой группе. </p>
<p><img src="images/back-nav/nav-pagetopage-samepeer-yesosnavelement.png" alt="Navigation across peer groups when a navigation element is present" /></p></td>
</tr>
<tr class="even">
<td style="vertical-align:top;"><strong>Показать переходной UI</strong>
<p>Приложение отображает всплывающее или дочернее окно, например диалоговое окно, экран-заставку или экранную клавиатуру, или же оно переходит в специальный режим, например режим выбора нескольких элементов.</p></td>
<td style="vertical-align:top;"><strong>Нет</strong>
<p>Если пользователь нажимает кнопку «Назад», скройте промежуточный элемент пользовательского интерфейса (экранную клавиатуру, диалоговое окно и т. д.) и вернитесь к породившей его странице.</p>
<p><img src="images/back-nav/back-transui.png" alt="Showing a transient UI" /></p></td>
</tr>
<tr class="odd">
<td style="vertical-align:top;"><strong>Перечислить элементы</strong>
<p>Приложение отображает содержимое элемента экрана, например информацию о выбранном элементе в основном и подробном списке.</p></td>
<td style="vertical-align:top;"><strong>Нет</strong>
<p>Перечисление элементов сходно с навигацией в пределах одноранговой группы. Если пользователь нажимает кнопку «Назад», выполните переход на страницу, предшествующую текущей, на которой имеется перечисление элементов.</p>
<p><img src="images/back-nav/nav-enumerate.png" alt="Iterm enumeration" /></p></td>
</tr>
</tbody>
</table>
</div>

### <a name="resuming"></a>Возобновление

Если пользователь переходит к другому приложению, а затем возвращается к вашему приложению, мы рекомендуем открывать при этом последнюю страницу в журнале навигации.

## <a name="related-articles"></a>Связанные статьи

- [Основы навигации](navigation-basics.md)
