---
Description: Узнайте, как адаптировать пользовательский интерфейс приложения при отображении или скрытии сенсорной клавиатуры.
title: Реакция на наличие сенсорной клавиатуры
ms.assetid: 70C6130E-23A2-4F9D-88E7-7060062DA988
label: Respond to the presence of the touch keyboard
template: detail.hbs
keywords: клавиатура, специальные возможности, навигация, фокус, текст, ввод, взаимодействия с пользователем
ms.date: 07/13/2018
ms.topic: article
ms.openlocfilehash: 76b468eedd136522a4af9fb5880049278548865d
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970269"
---
# <a name="respond-to-the-presence-of-the-touch-keyboard"></a>Реакция на наличие сенсорной клавиатуры

Узнайте, как адаптировать пользовательский интерфейс приложения при отображении или скрытии сенсорной клавиатуры.

### <a name="important-apis"></a>Важные API

- [AutomationPeer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationPeer)
- [InputPane](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.InputPane)

![Сенсорная клавиатура в режиме раскладки по умолчанию](images/keyboard/default.png)

<sup>Сенсорная клавиатура в режиме макета по умолчанию</sup>

С помощью сенсорной клавиатуры можно вводить текст на устройствах, поддерживающих сенсорный ввод. Элементы управления текстовыми входными данными приложения Windows вызывают сенсорную клавиатуру по умолчанию, когда пользователь касается редактируемого поля ввода. Обычно сенсорная клавиатура остается видимой, когда пользователь переходит по элементам управления в форме, но это поведение может меняться в зависимости от имеющихся в форме элементов управления других типов.

Чтобы поддерживать необходимое поведение сенсорной клавиатуры в пользовательском элементе управления для ввода текста, не являющемся производным от стандартного элемента управления для ввода текста, следует использовать класс <a href="https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationPeer">AutomationPeer</a>. С помощью этого класса можно предоставить ваши элементы управления модели автоматизации пользовательского интерфейса Microsoft, а также реализовать правильные шаблоны элементов управления для этой модели. См. статьи [Специальные возможности клавиатуры](https://docs.microsoft.com/windows/uwp/design/accessibility/keyboard-accessibility) и [Настраиваемые одноранговые элементы автоматизации](https://docs.microsoft.com/windows/uwp/design/accessibility/custom-automation-peers).

После добавления такой поддержки в пользовательский элемент управления приложение сможет правильно реагировать на наличие сенсорной клавиатуры.

**Предварительные условия.**

В данной статье использованы материалы статьи [Взаимодействие с помощью клавиатуры](keyboard-interactions.md).

Для работы со статьей необходимо иметь базовое представление о стандартных взаимодействиях клавиатуры, обработке событий клавиатуры и данных, вводимых с клавиатуры, а также о модели автоматизации пользовательского интерфейса.

Если вы не знакомы с разработкой приложений для приложений Windows, ознакомьтесь с этими разделами, чтобы ознакомиться с технологиями, обсуждаемыми здесь.

- [Создание первого приложения](https://docs.microsoft.com/windows/uwp/get-started/your-first-app)
- Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://docs.microsoft.com/windows/uwp/xaml-platform/events-and-routed-events-overview).

**Рекомендации по работе с пользователем:**

Полезные советы по проектированию полезного и привлекательного приложения, оптимизированного для ввода с клавиатуры, см. в разделе [взаимодействие с клавиатурой](https://docs.microsoft.com/windows/uwp/design/input/keyboard-interactions) .

## <a name="touch-keyboard-and-a-custom-ui"></a>Сенсорная клавиатура и настраиваемый пользовательский интерфейс

Ниже перечислены несколько основных рекомендаций для пользовательских элементов управления для ввода текста.

- Сенсорная клавиатура должна отображаться в течение всего времени взаимодействия пользователя с формой.

- Убедитесь, что пользовательские элементы управления имеют соответствующую [Аутоматионконтролтипе](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType) автоматизации пользовательского интерфейса для клавиатуры, чтобы она сохранялась при переходе фокуса с текстового поля ввода в контексте текстового ввода. Например, если в середине сценария текстового ввода открывается меню, а клавиатура должна по-прежнему отображаться, меню должно принадлежать к типу элемента управления меню **AutomationControlType**.

- Не изменяйте свойства модели автоматизации пользовательского интерфейса для управления сенсорной клавиатурой. От точности настройки этих свойств зависят другие средства специальных возможностей.

- Убедитесь, что пользователю всегда видно поле ввода, с которым он взаимодействует.

    Так как сенсорная клавиатура окклудес крупную часть экрана, Windows гарантирует, что поле ввода с фокусом прокручивается в представление по мере того, как пользователь переходит по элементам управления в форме, включая элементы управления, которые в настоящее время не отображаются.

    При настройке пользовательского интерфейса обеспечьте аналогичную реакцию на появление сенсорной клавиатуры путем обработки событий [Showing](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.inputpane.showing) и [Hiding](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.inputpane.hiding), предоставленных объектом [**InputPane**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.InputPane).

    ![Форма с сенсорной клавиатурой и без нее](images/touch-keyboard-pan1.png)

    В ряде случаев некоторые элементы пользовательского интерфейса должны присутствовать на экране постоянно. При разработке пользовательского интерфейса следите за тем, чтобы элементы управления формы находились в области панорамирования, а важные элементы пользовательского интерфейса были статичными. Пример:

    ![Форма, содержащая области, которые всегда должны быть в поле зрения](images/touch-keyboard-pan2.png)

## <a name="handling-the-showing-and-hiding-events"></a>Обработка событий отображения и скрытия

Ниже приведен пример присоединения обработчиков событий для [отображения](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.inputpane.showing) и [скрытия](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.inputpane.hiding) событий сенсорной клавиатуры.

```csharp
using Windows.UI.ViewManagement;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;
using Windows.Foundation;
using Windows.UI.Xaml.Navigation;

namespace SDKTemplate
{
    /// <summary>
    /// Sample page to subscribe show/hide event of Touch Keyboard.
    /// </summary>
    public sealed partial class Scenario2_ShowHideEvents : Page
    {
        public Scenario2_ShowHideEvents()
        {
            this.InitializeComponent();
        }

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            InputPane currentInputPane = InputPane.GetForCurrentView();
            // Subscribe to Showing/Hiding events
            currentInputPane.Showing += OnShowing;
            currentInputPane.Hiding += OnHiding;
        }

        protected override void OnNavigatedFrom(NavigationEventArgs e)
        {
            InputPane currentInputPane = InputPane.GetForCurrentView();
            // Unsubscribe from Showing/Hiding events
            currentInputPane.Showing -= OnShowing;
            currentInputPane.Hiding -= OnHiding;
        }

        void OnShowing(InputPane sender, InputPaneVisibilityEventArgs e)
        {
            LastInputPaneEventRun.Text = "Showing";
        }
        
        void OnHiding(InputPane sender, InputPaneVisibilityEventArgs e)
        {
            LastInputPaneEventRun.Text = "Hiding";
        }
    }
}
```

```cppwinrt
...
#include <winrt/Windows.UI.ViewManagement.h>
...
private:
    winrt::event_token m_showingEventToken;
    winrt::event_token m_hidingEventToken;
...
Scenario2_ShowHideEvents::Scenario2_ShowHideEvents()
{
    InitializeComponent();
}

void Scenario2_ShowHideEvents::OnNavigatedTo(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e)
{
    auto inputPane{ Windows::UI::ViewManagement::InputPane::GetForCurrentView() };
    // Subscribe to Showing/Hiding events
    m_showingEventToken = inputPane.Showing({ this, &Scenario2_ShowHideEvents::OnShowing });
    m_hidingEventToken = inputPane.Hiding({ this, &Scenario2_ShowHideEvents::OnHiding });
}

void Scenario2_ShowHideEvents::OnNavigatedFrom(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e)
{
    auto inputPane{ Windows::UI::ViewManagement::InputPane::GetForCurrentView() };
    // Unsubscribe from Showing/Hiding events
    inputPane.Showing(m_showingEventToken);
    inputPane.Hiding(m_hidingEventToken);
}

void Scenario2_ShowHideEvents::OnShowing(Windows::UI::ViewManagement::InputPane const& /*sender*/, Windows::UI::ViewManagement::InputPaneVisibilityEventArgs const& /*args*/)
{
    LastInputPaneEventRun().Text(L"Showing");
}

void Scenario2_ShowHideEvents::OnHiding(Windows::UI::ViewManagement::InputPane const& /*sender*/, Windows::UI::ViewManagement::InputPaneVisibilityEventArgs const& /*args*/)
{
    LastInputPaneEventRun().Text(L"Hiding");
}
```

```cpp
#include "pch.h"
#include "Scenario2_ShowHideEvents.xaml.h"

using namespace SDKTemplate;
using namespace Platform;
using namespace Windows::Foundation;
using namespace Windows::UI::ViewManagement;
using namespace Windows::UI::Xaml;
using namespace Windows::UI::Xaml::Controls;
using namespace Windows::UI::Xaml::Navigation;

Scenario2_ShowHideEvents::Scenario2_ShowHideEvents()
{
    InitializeComponent();
}

void Scenario2_ShowHideEvents::OnNavigatedTo(NavigationEventArgs^ e)
{
    auto inputPane = InputPane::GetForCurrentView();
    // Subscribe to Showing/Hiding events
    showingEventToken = inputPane->Showing +=
        ref new TypedEventHandler<InputPane^, InputPaneVisibilityEventArgs^>(this, &Scenario2_ShowHideEvents::OnShowing);
    hidingEventToken = inputPane->Hiding +=
        ref new TypedEventHandler<InputPane^, InputPaneVisibilityEventArgs^>(this, &Scenario2_ShowHideEvents::OnHiding);
}

void Scenario2_ShowHideEvents::OnNavigatedFrom(NavigationEventArgs^ e)
{
    auto inputPane = Windows::UI::ViewManagement::InputPane::GetForCurrentView();
    // Unsubscribe from Showing/Hiding events
    inputPane->Showing -= showingEventToken;
    inputPane->Hiding -= hidingEventToken;
}

void Scenario2_ShowHideEvents::OnShowing(InputPane^ /*sender*/, InputPaneVisibilityEventArgs^ /*args*/)
{
    LastInputPaneEventRun->Text = L"Showing";
}

void Scenario2_ShowHideEvents::OnHiding(InputPane^ /*sender*/, InputPaneVisibilityEventArgs ^ /*args*/)
{
    LastInputPaneEventRun->Text = L"Hiding";
}
```

## <a name="related-articles"></a>Похожие статьи

- [Взаимодействие с клавиатурой](keyboard-interactions.md)
- [Специальные возможности клавиатуры](https://docs.microsoft.com/windows/uwp/accessibility/keyboard-accessibility)
- [Настраиваемые одноранговые элементы автоматизации](https://docs.microsoft.com/windows/uwp/accessibility/custom-automation-peers)

### <a name="samples"></a>Примеры

- [Пример сенсорной клавиатуры](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/TouchKeyboard)

### <a name="archive-samples"></a>Примеры архива

- [Ввод: пример сенсорной клавиатуры](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/TouchKeyboard)
- [Пример реакции на появление экранной клавиатуры](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Responding%20to%20the%20appearance%20of%20the%20on-screen%20keyboard%20sample)
- [Пример редактирования текста на XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BVB%5D-Windows%208%20app%20samples/VB/Windows%208%20app%20samples/XAML%20text%20editing%20sample%20(Windows%208))
- [Пример специальных возможностей XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/XAML%20accessibility%20sample)
