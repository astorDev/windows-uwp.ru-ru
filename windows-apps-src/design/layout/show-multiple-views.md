---
Description: Просмотр различных частей приложения в отдельных окнах.
title: Отображение нескольких представлений для приложения
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: ee49b5fe5b5956e9069ea196c4d2e029b3a15763
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68729518"
---
# <a name="show-multiple-views-for-an-app"></a>Отображение нескольких представлений для приложения

![Макет, показывающий приложение с несколькими окнами](images/multi-view.gif)

Помогите пользователям работать эффективнее, дав им возможность открывать независимые части приложения в отдельных окнах. Если создать для приложения несколько окон, они будут отображаться отдельно на панели задач. Пользователи могут перемещать, отображать, скрывать окна приложения и менять их размеры независимо друг от друга, а также переключаться между окнами, как будто это разные приложения.

> **Важные API**: [пространство имен Windows.UI.ViewManagement](/uwp/api/windows.ui.viewmanagement), [пространство имен Windows.UI.WindowManagement](/uwp/api/windows.ui.windowmanagement).

## <a name="when-should-an-app-use-multiple-views"></a>Когда приложение должно использовать несколько представлений?

Существует ряд сценариев, для которых может быть удобным использование нескольких представлений. Вот несколько примеров.

- Приложение электронной почты, которое позволяет пользователям просматривать список полученных сообщений при составлении нового письма.
- Приложение адресной книги, которое позволяет пользователям сравнить контактные данные для нескольких пользователей.
- Приложение проигрывателя музыки, которое позволяет пользователям просматривать информацию о воспроизводимой музыке во время просмотра списка другой доступной музыки.
- Приложение заметок, которое позволяет пользователям копировать сведения с одной страницы заметок на другую.
- Приложение для чтения, которое дает пользователям возможность просматривать общий список заголовков и открывать несколько статей для чтения позже.

Так как каждый макет приложения уникален, мы рекомендуем добавить кнопку "Новое окно" в предсказуемое расположение, например в правый верхний угол окна содержимого, которое можно открыть в новом окне. Также следует учесть необходимость добавления параметра [контекстного меню](../controls-and-patterns/menus.md) "Открыть в новом окне".

Чтобы создать отдельные экземпляры приложения (а не отдельные окна одного экземпляра), обратитесь к разделу [Создание нескольких экземпляров приложения UWP](../../launch-resume/multi-instance-uwp.md).

## <a name="windowing-hosts"></a>Окна содержимого

Существует несколько способов размещения содержимого UWP внутри приложения.

- [CoreWindow](/uwp/api/windows.ui.core.corewindow)/[ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview)

     Представление приложения — это совокупность 1:1 потока и окна, которая используется приложением для отображения содержимого. Первое представление, создаваемое при запуске приложения, называется *главным*. Каждый объект CoreWindow и ApplicationView работает в своем собственном потоке. Работа с различными потоками пользовательского интерфейса может усложнить многооконные приложения.

    Основное представление приложения всегда размещается в ApplicationView. Содержимое в дополнительном окне может размещаться в ApplicationView или AppWindow.

    Узнайте, как использовать ApplicationView для отображения дополнительных окон в приложении, ознакомившись с разделом [Использование ApplicationView](application-view.md).
- [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow)

    AppWindow упрощает создание многооконных приложений UWP, так как работает в том же потоке пользовательского интерфейса, из которого этот экземпляр создается.

    Класс AppWindow и другие интерфейсы API в пространстве имен [WindowManagement](/uwp/api/windows.ui.windowmanagement) доступны, начиная с Windows 10 версии 1903 (пакет SDK 18362). Если приложение нацелено на более ранние версии Windows 10, для создания дополнительных окон необходимо использовать ApplicationView.

    Узнайте, как использовать AppWindow для отображения дополнительных окон в приложении, ознакомившись с разделом [Использование AppWindow](app-window.md).

    > [!NOTE]
    > AppWindow сейчас находится на этапе предварительной версии. Это означает, что вы можете отправлять приложения, использующие AppWindow, в Store, но некоторые компоненты платформ не работают с AppWindow (см. раздел [Ограничения](/uwp/api/windows.ui.windowmanagement.appwindow#limitations)).
- [DesktopWindowXamlSource](/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) (области XAML)

     Содержимое UWP XAML в приложении Win32 (использующем HWND), также известное как области XAML, размещается в DesktopWindowXamlSource.

    Чтобы узнать больше об областях XAML, ознакомьтесь с [использованием API размещения UWP XAML в классическом приложении](/windows/apps/desktop/modernize/using-the-xaml-hosting-api).

### <a name="make-code-portable-across-windowing-hosts"></a>Создание кода, переносимого между окнами содержимого

Когда содержимое XAML отображается в [CoreWindow](/uwp/api/windows.ui.core.corewindow), всегда существуют связанные с ним экземпляры [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview) и [Window](/uwp/api/windows.ui.xaml.window) XAML. Интерфейсы API для этих классов можно использовать для получения таких сведений, как границы окна. Для получения экземпляра этих классов используется статический метод [CoreWindow.GetForCurrentThread](/uwp/api/windows.ui.core.corewindow.getforcurrentthread), метод [ApplicationView.GetForCurrentView](/uwp/api/windows.ui.viewmanagement.applicationview.getforcurrentview) или свойство [Window.Current](/uwp/api/windows.ui.xaml.window.current). Кроме того, существует множество классов, использующих шаблон `GetForCurrentView` для получения экземпляра класса, например [DisplayInformation.GetForCurrentView](/uwp/api/windows.graphics.display.displayinformation.getforcurrentview).

Эти API работают, так как для CoreWindow и ApplicationView существует только одно дерево содержимого XAML, поэтому XAML известен контекст размещения, то есть CoreWindow и ApplicationView.

Когда содержимое XAML выполняется в AppWindow или DesktopWindowXamlSource, можно одновременно выполнять несколько деревьев содержимого XAML в одном и том же потоке. В этом случае эти API не предоставляют нужных сведений, так как содержимое больше не выполняется в текущем экземпляре CoreWindow или ApplicationView (и экземпляре Window XAML).

Чтобы обеспечить правильную работу кода во всех окнах содержимого, необходимо заменить интерфейсы API, использующие [CoreWindow](/uwp/api/windows.ui.core.corewindow), [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview) и [Window](/uwp/api/windows.ui.xaml.window), новыми API, которые получают контекст из класса [XamlRoot](/uwp/api/windows.ui.xaml.xamlroot).
Класс XamlRoot представляет дерево содержимого XAML и сведения о контексте, в котором оно размещено, будь это CoreWindow, AppWindow или DesktopWindowXamlSource. Этот уровень абстракции позволяет писать единый код, не зависящий от того, в каком окне содержимого выполняется XAML.

В этой таблице показан код, который не работает правильно в окнах содержимого, и новый переносимый код, которым можно заменить предыдущий код, а также несколько API, которые не нужно изменять.

| Если вы использовали… | Замените на… |
| - | - |
| CoreWindow.GetForCurrentThread().[Bounds](/uwp/api/windows.ui.core.corewindow.bounds) | _uiElement_.XamlRoot.[Size](/uwp/api/windows.ui.xaml.xamlroot.size) |
| CoreWindow.GetForCurrentThread().[SizeChanged](/uwp/api/windows.ui.core.corewindow.sizechanged) | _uiElement_.XamlRoot.[Changed](/uwp/api/windows.ui.xaml.xamlroot.changed) |
| CoreWindow.[Visible](/uwp/api/windows.ui.core.corewindow.visible) | _uiElement_.XamlRoot.[IsHostVisible](/uwp/api/windows.ui.xaml.xamlroot.ishostvisible) |
| CoreWindow.[VisibilityChanged](/uwp/api/windows.ui.core.corewindow.visibilitychanged) | _uiElement_.XamlRoot.[Changed](/uwp/api/windows.ui.xaml.xamlroot.changed) |
| CoreWindow.GetForCurrentThread().[GetKeyState](/uwp/api/windows.ui.core.corewindow.getkeystate) | Без изменений. Это поддерживается в AppWindow и DesktopWindowXamlSource. |
| CoreWindow.GetForCurrentThread().[GetAsyncKeyState](/uwp/api/windows.ui.core.corewindow.getasynckeystate) | Без изменений. Это поддерживается в AppWindow и DesktopWindowXamlSource. |
| Window.[Current](/uwp/api/windows.ui.xaml.window.current) | Возвращает основной объект Window XAML, который жестко привязан к текущему экземпляру CoreWindow. См. примечание после таблицы. |
| Window.Current.[Bounds](/uwp/api/windows.ui.xaml.window.bounds) | _uiElement_.XamlRoot.[Size](/uwp/api/windows.ui.xaml.xamlroot.size) |
| Window.Current.[Content](/uwp/api/windows.ui.xaml.window.content) | UIElement root =  _uiElement_.XamlRoot.[Content](/uwp/api/windows.ui.xaml.xamlroot.content) |
| Window.Current.[Compositor](/uwp/api/windows.ui.xaml.window.compositor) | Без изменений. Это поддерживается в AppWindow и DesktopWindowXamlSource. |
| VisualTreeHelper.[GetOpenPopups](/uwp/api/windows.ui.xaml.media.visualtreehelper.getopenpopups)<br/>В приложениях с областями XAML это приведет к ошибке. В приложениях AppWindow это приведет к появлению всплывающих окон в главном окне. | VisualTreeHelper.[GetOpenPopupsForXamlRoot](/uwp/api/windows.ui.xaml.media.visualtreehelper.getopenpopupsforxamlroot)(_uiElement_.XamlRoot) |
| FocusManager.[GetFocusedElement](/uwp/api/windows.ui.xaml.input.focusmanager.getfocusedelement) | FocusManager.[GetFocusedElement](/uwp/api/windows.ui.xaml.input.focusmanager.getfocusedelement#Windows_UI_Xaml_Input_FocusManager_GetFocusedElement_Windows_UI_Xaml_XamlRoot_)(_uiElement_.XamlRoot) |
| contentDialog.ShowAsync() | contentDialog.[XamlRoot](/uwp/api/windows.ui.xaml.uielement.xamlroot) = _uiElement_.XamlRoot;<br/>contentDialog.ShowAsync(); |
| menuFlyout.ShowAt(null, new Point(10, 10)); | menuFlyout.[XamlRoot](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.xamlroot) = _uiElement_.XamlRoot;<br/>menuFlyout.ShowAt(null, new Point(10, 10)); |

> [!NOTE]
> Для содержимого XAML в DesktopWindowXamlSource в потоке существует экземпляр CoreWindow или Window, но он всегда невидимый и имеет размер 1x1. Он по-прежнему доступен для приложения, но не возвращает действительные значения границы или видимости.
>
>Для содержимого XAML в AppWindow всегда будет существовать только один экземпляр CoreWindow в одном потоке. При вызове API `GetForCurrentView` или `GetForCurrentThread` возвращается объект, отражающий состояние CoreWindow в потоке, а не экземпляров AppWindow, которые могут выполняться в том же потоке.


## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать

- Укажите явную точку входа в дополнительное представление, используя глиф "Открыть новое окно".
- Обсудите предназначение дополнительного представления для пользователей.
- Убедитесь, что приложение является полностью функциональным в отдельном представлении и пользователи будут открывать дополнительное представление только для удобства.
- Не используйте дополнительное представление для уведомлений и других временных визуальных элементов.

## <a name="related-topics"></a>Связанные темы

- [Использование AppWindow](app-window.md)
- [Использование ApplicationView](application-view.md)
- [ApplicationViewSwitcher](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.ApplicationViewSwitcher)
- [CreateNewView](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.createnewview)
