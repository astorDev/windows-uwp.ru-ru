---
description: В этой статье описывается размещение пользовательского интерфейса UWP XAML в классическом приложении Win32 на C++.
title: Использование API размещения XAML платформы UWP в приложении Win32 на C++
ms.date: 03/23/2020
ms.topic: article
keywords: Windows 10, UWP, Windows Forms, WPF, Win32, XAML Islands
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 36c3aeb7a51c84e92c5bca461aee7efe50740237
ms.sourcegitcommit: c660def841abc742600fbcf6ed98e1f4f7beb8cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218464"
---
# <a name="using-the-uwp-xaml-hosting-api-in-a-c-win32-app"></a>Использование API размещения XAML платформы UWP в приложении Win32 на C++

Начиная с Windows 10 версии 1903, не относящиеся к платформе UWP классические приложения (в том числе Win32 на C++, WPF и приложения Windows Forms) могут использовать *API размещения элементов универсальной платформы Windows (UWP) XAML* для размещения элементов управления UWP в любом элементе пользовательского интерфейса, связанном с дескриптором окна (HWND). Этот API позволяет классическим приложениям, не относящимся к UWP, использовать новейшие возможности пользовательского интерфейса Windows 10, которые доступны только через элементы управления UWP. Например, классические приложения, не относящиеся к UWP, могут использовать этот API для размещения элементов управления UWP, которые используют [систему Fluent Design](/windows/uwp/design/fluent-design-system/index) и поддерживают [Windows Ink](/windows/uwp/design/input/pen-and-stylus-interactions).

API размещения UWP XAML создает основу с более широким набором элементов управления, который мы предоставляем разработчикам для реализации пользовательского интерфейса Fluent в классических приложениях, не относящихся к UWP. Эта возможность называется *XAML Islands*. Общие сведения об этой возможности см. в статье [Элементы управления UWP XAML в классических приложениях (XAML Islands)](xaml-islands.md).

> [!NOTE]
> Если вы хотите поделиться мнением об XAML Island, создайте запрос в [репозитории Microsoft.Toolkit.Win32](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/issues) и оставьте свои комментарии. Вы также можете отправить частное письмо по адресу XamlIslandsFeedback@microsoft.com. Ваши идеи и сценарии использования очень важны для нас.

## <a name="is-the-uwp-xaml-hosting-api-the-right-choice-for-your-desktop-app"></a>Является ли API размещения UWP XAML правильным выбором для классического приложения?

Этот API предоставляет низкоуровневую инфраструктуру для размещения элементов управления UWP в классических приложениях. Для классических приложений некоторых типов можно использовать альтернативные более удобные API-интерфейсы для выполнения этой задачи.

* Если у вас есть классическое приложение Win32 на C++ и вы хотите разместить элементы управления UWP в приложении, необходимо использовать API размещения UWP XAML. Для таких типов приложений нет альтернативы.

* Для приложений WPF и Windows Forms мы настоятельно рекомендуем применять [элементы управления XAML Islands .NET](xaml-islands.md#wpf-and-windows-forms-applications) из набора средств сообщества Windows, а не использовать API размещения UWP XAML. Эти элементы управления используют API размещения UWP XAML на внутреннем уровне и реализуют все типы поведения, которые при использовании этого API напрямую пришлось бы реализовать самостоятельно, в том числе навигацию с помощью клавиатуры и изменение раскладки.

Так как мы рекомендуем, чтобы только приложения Win32 на C++ использовали API размещения UWP XAML, то в этой статье преимущественно приводятся инструкции и примеры для приложений Win32 на C++. Но API размещения UWP XAML можно использовать в приложениях WPF и Windows Forms при необходимости. В этой статье рассматривается соответствующий исходный код для [элементов управления ведущего приложения](xaml-islands.md#host-controls) для WPF и Windows Forms в наборе средств сообщества Windows, чтобы вы могли увидеть, как API размещения UWP XAML используется этими элементами управления.

## <a name="learn-how-to-use-the-xaml-hosting-api"></a>Как использовать API размещения XAML

Чтобы выполнить пошаговые инструкции с примерами кода по использованию API размещения XAML в приложениях Win32 на C++, см. следующие статьи:

* [Размещение стандартного элемента управления UWP в приложении Win32 на C++](host-standard-control-with-xaml-islands-cpp.md)
* [Размещение настраиваемого элемента управления UWP в приложении Win32 на C++](host-custom-control-with-xaml-islands-cpp.md)
* [Расширенные сценарии XAML Islands в приложениях Win32 на C++](advanced-scenarios-xaml-islands-cpp.md)

## <a name="samples"></a>примеры

Способ использования API размещения UWP XAML в коде зависит от типа, структуры приложения и других факторов. Чтобы продемонстрировать, как использовать этот API в контексте целого приложения, в этой статье рассматриваются следующие примеры кода.

### <a name="c-win32"></a>Win32 на C++

В следующих примерах показано, как использовать API размещения UWP XAML в приложении Win32 на C++:

* [Простой пример XAML Islands](https://github.com/microsoft/Xaml-Islands-Samples/tree/master/Standalone_Samples/CppWinRT_Basic_Win32App). Здесь показана базовая реализация размещения элемента управления UWP в неупакованном приложении Win32 на C++ (т. е. в приложении, которое не встроено в пакет MSIX).

* [Пример XAML Islands с настраиваемым элементом управления](https://github.com/microsoft/Xaml-Islands-Samples/tree/master/Samples/Win32). Здесь показана полная реализация размещения пользовательского элемента управления UWP в упакованном приложении Win32 на C++, а также обработка других типов поведения, таких как ввод с клавиатуры и перемещение фокуса.

### <a name="wpf-and-windows-forms"></a>WPF и Windows Forms

Элемент управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в наборе средств сообщества Windows служит эталонным примером использования API размещения UWP в приложениях WPF и Windows Forms. Исходный код доступен по ссылкам ниже.

* Для версии WPF [перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Wpf.UI.XamlHost). Версия WPF является производной от [System.Windows.Interop.HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost).

* Для версии Windows Forms [перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Forms.UI.XamlHost). Версия Windows Forms является производной от [System.Windows.Forms.Control](https://docs.microsoft.com/dotnet/api/system.windows.forms.control).

> [!NOTE]
> Мы настоятельно рекомендуем применять [элементы управления XAML Islands .NET](xaml-islands.md#wpf-and-windows-forms-applications) из набора средств сообщества Windows, а не использовать API размещения UWP XAML напрямую в приложениях WPF и Windows Forms. В этой статье ссылки на примеры WPF и Windows Forms приведены только для наглядности.

## <a name="architecture-of-the-api"></a>Архитектура API

API размещения UWP XAML предусматривает следующие основные типы среды выполнения Windows и COM-интерфейсы.

|  Тип или интерфейс | Описание |
|--------------------|-------------|
| [WindowsXamlManager](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) | Этот класс представляет платформу XAML для элементов UWP. Он предоставляет один статический метод [InitializeForCurrentThread](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread), который инициализирует платформу XAML для UWP в текущем потоке классического приложения. |
| [DesktopWindowXamlSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) | Этот класс представляет экземпляр содержимого UWP XAML, которое размещается в классическом приложении. Самым важным членом этого класса является свойство [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content). Вы назначаете это свойство элементу [Windows.UI.Xaml.UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), который требуется разместить. Этот класс также содержит другие члены для маршрутизации фокуса с клавиатуры на объекты XAML Islands и с них. |
| IDesktopWindowXamlSourceNative | Этот COM-интерфейс предоставляет метод **AttachToWindow**, который используется для присоединения элемента XAML Islands к родительскому элементу пользовательского интерфейса в приложении. Каждый объект **DesktopWindowXamlSource** реализует этот интерфейс. Этот интерфейс объявляется в файле windows.ui.xaml.hosting.desktopwindowxamlsource.h. |
| IDesktopWindowXamlSourceNative2 | Этот COM-интерфейс предоставляет метод **PreTranslateMessage**, который позволяет платформе XAML для UWP правильно обрабатывать определенные сообщения Windows. Каждый объект **DesktopWindowXamlSource** реализует этот интерфейс. Этот интерфейс объявляется в файле windows.ui.xaml.hosting.desktopwindowxamlsource.h. |

На следующей схеме показана иерархия объектов в элементе XAML Islands в классическом приложении.

* На базовом уровне находится элемент пользовательского интерфейса в приложении, в котором требуется разместить элемент XAML Islands. У этого элемента пользовательского интерфейса должен быть HWND. Примеры элементов пользовательского интерфейса, в которых можно разместить XAML Islands, включают в себя [окно](https://docs.microsoft.com/windows/desktop/winmsg/about-windows) — для приложений Win32 на C++, [System.Windows.Interop.HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) — для приложений WPF и [System.Windows.Forms.Control](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) — для приложений Windows Forms.

* На следующем уровне находится объект **DesktopWindowXamlSource**. Этот объект предоставляет инфраструктуру для размещения элемента XAML Islands. Ваш код отвечает за создание этого объекта и его присоединение к родительскому элементу пользовательского интерфейса.

* При создании **DesktopWindowXamlSource** этот объект автоматически создает собственное дочернее окно для размещения элемента управления UWP. Это собственное дочернее окно в основном абстрагировано от кода, но при необходимости можно получить доступ к его HWND.

* Наконец, на верхнем уровне находится элемент управления UWP, который требуется разместить в классическом приложении. Это может быть любой объект UWP, производный от [Windows.UI.Xaml.UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), в том числе любой элемент управления UWP, предоставляемый в пакете Windows SDK, а также пользовательский элемент управления.

![Архитектура DesktopWindowXamlSource](images/xaml-islands/xaml-hosting-api-rev2.png)

> [!NOTE]
> При размещении объектов XAML Island в классическом приложении можно одновременно использовать несколько деревьев содержимого XAML, выполняющихся в одном и том же потоке. Для доступа к корневому элементу дерева содержимого XAML в XAML Island и получения связанных сведений о контексте, в котором он размещен, используйте класс [XamlRoot](https://docs.microsoft.com/uwp/api/windows.ui.xaml.xamlroot). API-интерфейсы [CoreWindow](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow), [ApplicationView](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview) и [Window](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window) не предоставляют правильную информацию об объектах XAML Islands. Дополнительные сведения см. в [этом разделе](xaml-islands.md#window-host-context-for-xaml-islands).

## <a name="troubleshooting"></a>Диагностика

### <a name="error-using-uwp-xaml-hosting-api-in-a-uwp-app"></a>Ошибка использования API размещения UWP XAML в приложении UWP

| Проблема | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: "Cannot activate DesktopWindowXamlSource. This type cannot be used in a UWP app." (Не удалось активировать DesktopWindowXamlSource. Этот тип нельзя использовать в приложении UWP.) или "Cannot activate WindowsXamlManager. This type cannot be used in a UWP app." (Не удалось активировать WindowsXamlManager. Этот тип нельзя использовать в приложении UWP.) | Эта ошибка означает, что вы пытаетесь использовать API размещения UWP XAML (в частности, вы пытаетесь создать экземпляры типов [DesktopWindowXamlSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) или [WindowsXamlManager](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager)) в приложении UWP. API размещения UWP XAML предназначен для использования только в классических приложениях не для UWP, таких как WPF, Windows Forms и приложения Win32 на C++. |

### <a name="error-trying-to-use-the-windowsxamlmanager-or-desktopwindowxamlsource-types"></a>Ошибка при попытке использовать типы WindowsXamlManager или DesktopWindowXamlSource

| Проблема | Разрешение |
|-------|------------|
| Приложение получает исключение со следующим сообщением: "WindowsXamlManager and DesktopWindowXamlSource are supported for apps targeting Windows version 10.0.18226.0 and later. Please check either the application manifest or package manifest and ensure the MaxTestedVersion property is updated." (WindowsXamlManager и DesktopWindowXamlSource поддерживаются для приложений, предназначенных для Windows версии 10.0.18226.0 и более поздних. Проверьте манифест приложения или манифест пакета и убедитесь, что свойство MaxTestedVersion обновлено.) | Эта ошибка означает, что приложение пыталось использовать типы **WindowsXamlManager** или **DesktopWindowXamlSource** в API размещения UWP XAML, но ОС не может определить, создано ли приложение для Windows 10 версии 1903 или более поздних. API размещения UWP XAML впервые появился в виде ознакомительной версии в более ранней версии Windows 10, но поддерживается только начиная с Windows 10 версии 1903.</p></p>Чтобы устранить эту проблему, создайте пакет MSIX для приложения и запустите его из пакета или установите пакет NuGet [Microsoft.Toolkit.Win32.UI.SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK) в проекте.  |

### <a name="error-attaching-to-a-window-on-a-different-thread"></a>Ошибка присоединения к окну в другом потоке

| Проблема | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: "AttachToWindow method failed because the specified HWND was created on a different thread." (Сбой метода AttachToWindow, так как указанный HWND создан в другом потоке.) | Эта ошибка означает, что приложение вызвало метод **IDesktopWindowXamlSourceNative::AttachToWindow** и передало ему HWND окна, созданный в другом потоке. Этот метод необходимо передать в HWND окна, созданный в том же потоке, что и код, из которого вызывается метод. |

### <a name="error-attaching-to-a-window-on-a-different-top-level-window"></a>Ошибка присоединения к окну в другом окне верхнего уровня

| Проблема | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: "AttachToWindow method failed because the specified HWND descends from a different top-level window than the HWND that was previously passed to AttachToWindow on the same thread." (Сбой метода AttachToWindow, так как указанный HWND, производный от другого окна верхнего уровня, отличается от HWND, который ранее передан в AttachToWindow в том же потоке.) | Эта ошибка означает, что приложение вызвало метод **IDesktopWindowXamlSourceNative::AttachToWindow** и передало ему HWND окна, производный от другого окна верхнего уровня, отличного от указанного окна в предыдущем вызове этого метода в этом же потоке.</p></p>После того как приложение вызовет **AttachToWindow** в определенном потоке, все остальные объекты [DesktopWindowXamlSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) в том же потоке могут быть присоединены только к окнам, являющимся потомками того же окна верхнего уровня, которое передано в первый вызов **AttachToWindow**. Когда все объекты **DesktopWindowXamlSource** закрыты для конкретного потока, следующий **DesktopWindowXamlSource** снова можно присоединить к любому окну.</p></p>Чтобы устранить эту проблему, закройте все объекты **DesktopWindowXamlSource**, привязанные к другим окнам верхнего уровня в этом потоке, или создайте другой поток для этого **DesktopWindowXamlSource**. |

## <a name="related-topics"></a>Связанные темы

* [Элементы управления UWP XAML в классических приложениях (XAML Islands)](xaml-islands.md)
* [Размещение стандартного элемента управления UWP в приложении Win32 на C++](host-standard-control-with-xaml-islands-cpp.md)
* [Размещение настраиваемого элемента управления UWP в приложении Win32 на C++](host-custom-control-with-xaml-islands-cpp.md)
* [Расширенные сценарии для XAML Islands в приложениях Win32 на C++](advanced-scenarios-xaml-islands-cpp.md)
* [Примеры кода XAML Islands](https://github.com/microsoft/Xaml-Islands-Samples)
* [Пример XAML Islands на C++ для Win32](https://github.com/microsoft/Xaml-Islands-Samples/tree/master/Samples/Win32/SampleCppApp)
