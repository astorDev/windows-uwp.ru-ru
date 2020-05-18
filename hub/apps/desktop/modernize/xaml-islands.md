---
description: Это руководство поможет вам создать пользовательские интерфейсы UWP на основе принципов проектирования Fluent Design непосредственно в приложениях WPF и Windows Forms.
title: Элементы управления UWP в классических приложениях
ms.date: 03/23/2020
ms.topic: article
keywords: Windows 10, UWP, Windows Forms, WPF, объекты XAML Island
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: high
ms.custom: 19H1
ms.openlocfilehash: d050e2b4a7659f8910ce603ec7e90b703cc7722f
ms.sourcegitcommit: 2571af6bf781a464a4beb5f1aca84ae7c850f8f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82606243"
---
# <a name="host-uwp-xaml-controls-in-desktop-apps-xaml-islands"></a>Элементы управления UWP XAML в классических приложениях (XAML Island)

Начиная с Windows 10 версии 1903, можно размещать элементы управления универсальной платформы Windows (UWP) в классических приложениях, созданных не на платформе UWP, с помощью функции, называемой *объекты XAML Island*. Эта функция позволяет улучшить внешний вид и функции имеющихся приложений (WPF, Windows Forms и Win32 на C++) с помощью последних функций пользовательского интерфейса Windows 10, которые доступны только через элементы управления UWP. Это означает, что в имеющихся приложениях WPF, Windows Forms и Win32 на C++ можно использовать такие функции UWP, как [Windows Ink](/windows/uwp/design/input/pen-and-stylus-interactions), и элементы управления, которые поддерживают систему [Fluent Design](/windows/uwp/design/fluent-design-system/index).

Вы можете разместить любой элемент управления UWP, производный от [Windows.UI.Xaml.UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), в том числе:

* Любой основной элемент управления UWP, предоставляемый пакетом Windows SDK.
* Любой пользовательский элемент управления UWP (например, элемент, состоящий из нескольких элементов управления UWP, которые работают совместно). Чтобы скомпилировать пользовательский элемент управления с приложением, требуется его исходный код.

По сути, объекты XAML Island создаются с помощью *API размещения UWP XAML*. Этот API состоит из нескольких классов среды выполнения Windows и COM-интерфейсов, которые появились в пакете SDK для Windows 10 версии 1903. Мы также предоставляем в [наборе средств сообщества Windows](https://docs.microsoft.com/windows/uwpcommunitytoolkit/) подмножество элементов управления XAML Island .NET, которые используют API размещения UWP XAML на внутреннем уровне и обеспечивают более удобную среду для разработки приложений WPF и Windows Forms.

Способ использования объектов XAML Island зависит от типа приложения и типов элементов управления UWP, которые требуется разместить.

> [!NOTE]
> Если вы хотите поделиться мнением об XAML Island, создайте запрос в [репозитории Microsoft.Toolkit.Win32](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/issues) и оставьте свои комментарии. Вы также можете отправить частное письмо по адресу XamlIslandsFeedback@microsoft.com. Ваши идеи и сценарии использования очень важны для нас.

## <a name="requirements"></a>Требования

Для XAML Islands действуют следующие требования к среде выполнения:

* ОС Windows 10 версии 1903 или более поздней.
* Если приложение не упаковано для развертывания в [пакет MSIX](https://docs.microsoft.com/windows/msix), на компьютере должна быть установлена [среда выполнения Visual C++](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads).

## <a name="wpf-and-windows-forms-applications"></a>Приложения WPF и Windows Forms

Рекомендуется, чтобы приложения WPF и Windows Forms использовали элементы управления XAML Island .NET, доступные в наборе средств сообщества Windows. Эти элементы управления предоставляют объектную модель, которая имитирует свойства, методы и события соответствующих элементов управления UWP или предоставляет доступ к ним. Они также поддерживают такие действия, как навигация с помощью клавиатуры и изменение раскладки.

Существует два набора элементов управления XAML Island для приложений WPF и Windows Forms: *заключенные в оболочку элементы управления* и *элементы управления ведущего приложения*. 

### <a name="wrapped-controls"></a>Заключенные в оболочку элементы управления

Приложения WPF и Windows Forms могут использовать набор элементов управления XAML Island, которые заключают интерфейс и функциональные возможности определенного элемента управления UWP в оболочку. Можно добавить эти элементы управления напрямую в область проектирования вашего проекта WPF или Windows Forms, а затем использовать их как и любой другой элемент управления WPF или Windows Forms в конструкторе.

Приведенные ниже заключенные в оболочку элементы управления UWP в настоящее время доступны в наборе средств сообщества Windows. 

| Элемент | Минимальная поддерживаемая версия ОС | Описание |
|-----------------|-------------------------------|-------------|
| [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)<br>[InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) | Windows 10 версии 1903 | Предоставляет поверхность и соответствующие панели инструментов для взаимодействия с пользователем на основе Windows Ink в классическом приложении Windows Forms или WPF. |
| [MediaPlayerElement](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/mediaplayerelement) | Windows 10 версии 1903 | Внедряет представление, которое выполняет потоковую передачу и визуализацию мультимедийного содержимого, например видео, в классическом приложении Windows Forms или WPF. |
| [MapControl](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/mapcontrol) | Windows 10 версии 1903 | Позволяет отображать символьную или фотореалистичную карту в классическом приложении Windows Forms или WPF. |

Пошаговое руководство, в котором демонстрируется использование заключенных в оболочку элементов управления UWP, см. в статье [Host a standard UWP control in a WPF app using XAML Islands](host-standard-control-with-xaml-islands.md) (Размещение стандартного элемента управления UWP в приложении WPF с помощью объектов XAML Island).

### <a name="host-controls"></a>Элементы управления ведущего приложения

Для пользовательских элементов управления и других сценариев, не поддерживаемых заключенными в оболочку элементами управления, приложения WPF и Windows Forms могут также использовать элемент управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost), доступный в наборе средств сообщества Windows.

| Элемент | Минимальная поддерживаемая версия ОС | Описание |
|-----------------|-------------------------------|-------------|
| [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) | Windows 10 версии 1903 | Может размещать любой элемент управления UWP, производный от [Windows.UI.Xaml.UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), включая любой основной элемент управления UWP, предоставляемый в пакете Windows SDK, а также пользовательские элементы управления. |

Пошаговые руководства, демонстрирующие использование элемента управления **WindowsXamlHost**, можно найти в статьях о размещении [стандартного](host-standard-control-with-xaml-islands.md) и [пользовательского](host-custom-control-with-xaml-islands.md) элемента управления UWP в приложении WPF с помощью объектов XAML Island.

> [!NOTE]
> Использование элемента управления **WindowsXamlHost** для размещения пользовательских элементов управления UWP поддерживается только в приложениях WPF и Windows Forms, предназначенных для .NET Core 3. Размещение основных элементов управления UWP, предоставляемых пакетом Windows SDK, поддерживается в приложениях, предназначенных для .NET Framework или .NET Core 3.

<span id="requirements" />

### <a name="configure-your-project-to-use-the-xaml-island-net-controls"></a>Настройка проекта для использования элементов управления XAML Island .NET

Для элементов управления XAML Island .NET требуется Windows 10 версии 1903 или более поздней. Чтобы использовать эти элементы управления, установите один из перечисленных ниже пакетов NuGet. Эти пакеты предоставляют все требуемые компоненты для использования заключенных в оболочку элементов управления XAML Island и элементов управления ведущего приложения и содержат другие связанные пакеты NuGet, которые также необходимы.

| Тип элемента управления | Пакет NuGet  | Похожие статьи |
|-----------------|----------------|---------------------|
| [Заключенные в оболочку элементы управления](#wrapped-controls) | Версия 6.0.0 этих пакетов или более поздняя: <ul><li>WPF: [Microsoft.Toolkit.Wpf.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls);</li><li>Windows Forms: [Microsoft.Toolkit.Forms.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls).</li></ul>  | [Host a standard UWP control in a WPF app using XAML Islands](host-standard-control-with-xaml-islands.md) (Размещение стандартного элемента управления UWP в приложении WPF с помощью объектов XAML Island)  |
| [Элемент управления ведущего приложения](#host-controls) | Версия 6.0.0 этих пакетов или более поздняя: <ul><li>WPF: [Microsoft.Toolkit.Wpf.UI.XamlHost](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.XamlHost);</li><li>Windows Forms: [Microsoft.Toolkit.Forms.UI.XamlHost](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.XamlHost).</li></ul>  | [Host a standard UWP control in a WPF app using XAML Islands](host-standard-control-with-xaml-islands.md) (Размещение стандартного элемента управления UWP в приложении WPF с помощью объектов XAML Island)<br/>[Host a custom UWP control in a WPF app using XAML Islands](host-custom-control-with-xaml-islands.md) (Размещение пользовательского элемента управления UWP в приложение WPF с помощью объектов XAML Island)  |

Учтите следующие аспекты.

* Пакеты элементов управления ведущего приложения также включены в пакеты заключенных в оболочку элементов управления. Если необходимо использовать оба набора элементов управления, можно установить пакеты элементов управления, заключенных в оболочку.

* Если вы размещаете пользовательский элемент управления UWP, проект WPF или Windows Forms должен быть предназначен для .NET Core 3. Размещение пользовательских элементов управления UWP не поддерживается в приложениях, предназначенных для .NET Framework. Вам также потребуется выполнить некоторые дополнительные действия, чтобы сослаться на пользовательский элемент управления. Дополнительные сведения см. в статье [Host a custom UWP control in a WPF app using XAML Islands](host-custom-control-with-xaml-islands.md) (Размещение пользовательского элемента управления UWP в приложение WPF с помощью объектов XAML Island).

### <a name="web-view-controls"></a>Элементы управления для просмотра веб-содержимого

Набор средств сообщества Windows Community также предоставляет следующие элементы управления .NET для размещения веб-содержимого в приложениях WPF и Windows Forms. Эти элементы управления часто используются в тех же сценариях модернизации классических приложений, что и элементы управления XAML Island, и они хранятся в том же репозитории ([Microsoft.Toolkit.Win32](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32)), что и элементы управления XAML Island.

| Элемент | Минимальная поддерживаемая версия ОС | Описание |
|-----------------|-------------------------------|-------------|
| [WebView](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/webview) | Windows 10 версии 1803 | Использует механизм визуализации Microsoft Edge для отображения веб-содержимого. |
| [WebViewCompatible](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/webviewcompatible) | Windows 7 | Предоставляет версию **WebView**, которая совместима с другими версиями ОС. Этот элемент управления использует механизм визуализации Microsoft Edge для отображения веб-содержимого в Windows 10 версии 1803 и более поздних и механизм визуализации Internet Explorer для отображения веб-содержимого в более ранних версиях Windows 10, Windows 8.x и Windows 7. |

Чтобы использовать эти элементы управления, установите один из следующих пакетов NuGet:

* WPF: [Microsoft.Toolkit.Wpf.UI.Controls.WebView](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls.WebView);
* Windows Forms: [Microsoft.Toolkit.Forms.UI.Controls.WebView](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls.WebView).

## <a name="c-win32-applications"></a>Приложения Win32 на C++

Элементы управления XAML Island .NET не поддерживаются в приложениях Win32 на C++. Вместо этого такие приложения должны использовать *API размещения UWP XAML*, предоставляемый пакетом SDK для Windows 10 (версии 1903 и более поздних).

API размещения UWP XAML состоит из нескольких классов среды выполнения Windows и COM-интерфейсов, которые приложение Win32 на C++ может использовать для размещения любого элемента управления UWP, производного от [Windows.UI.Xaml.UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement). Элементы управления UWP можно размещать в любом элементе пользовательского интерфейса в приложении с соответствующим дескриптором окна (HWND). Дополнительные сведения об этом API доступны в следующих статьях:

* [Использование API размещения XAML платформы UWP в приложении Win32 на C++](using-the-xaml-hosting-api.md)
* [Размещение стандартного элемента управления UWP в приложении Win32 на C++](host-standard-control-with-xaml-islands-cpp.md)
* [Размещение настраиваемого элемента управления UWP в приложении Win32 на C++](host-custom-control-with-xaml-islands-cpp.md)

> [!NOTE]
> Заключенные в оболочку элементы управления и элементы управления ведущего приложения в наборе средств сообщества Windows используют API размещения UWP XAML на внутреннем уровне и реализуют все типы поведения, которые при использовании API размещения UWP XAML напрямую пришлось бы реализовать самостоятельно, включая навигацию с помощью клавиатуры и изменение раскладки. Для приложений WPF и Windows Forms мы настоятельно рекомендуем применять эти элементы управления, а не использовать API размещения UWP XAML напрямую, так как они устраняют множество сложностей реализации, возникающих при задействовании API.

## <a name="architecture-of-xaml-islands"></a>Архитектура объектов XAML Island

Вот краткий обзор различных типов элементов управления XAML Island с точки зрения архитектуры с базой в виде API размещения UWP XAML.

![Архитектура размещаемых элементов управления](images/xaml-islands/host-controls.png)

API-интерфейсы, представленные в нижней части этой схемы, входят в состав пакета Windows SDK. Заключенные в оболочку элементы управления и элементы управления ведущего приложения доступны в пакетах NuGet в наборе средств сообщества Windows.

## <a name="limitations-and-workarounds"></a>Ограничения и обходные решения

В следующих разделах описаны ограничения и обходные решения для некоторых сценариев разработки UWP в классических приложениях, использующих XAML Islands. 

### <a name="supported-only-with-workarounds"></a>Поддерживаются только при использовании обходных решений

:heavy_check_mark: В текущем выпуске XAML Islands размещение элементов управления UWP из [библиотеки WinUI](https://docs.microsoft.com/uwp/toolkits/winui/) в XAML Island поддерживается условно. Если классическое приложение использует [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания, можно разместить элементы управления WinUI из предварительной или окончательной версии пакета NugGet [Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml). Если классическое приложение не упаковано с помощью MSIX, вы сможете разместить элементы управления WinUI, если только установите последнюю предварительную версию пакета NuGet [Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml).

:heavy_check_mark: Для доступа к корневому элементу дерева содержимого XAML в области XAML и получения связанных сведений о контексте, в котором он размещен, не используйте классы [CoreWindow](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow), [ApplicationView](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview) и [Window](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window). Вместо этого используйте класс [XamlRoot](https://docs.microsoft.com/uwp/api/windows.ui.xaml.xamlroot). Дополнительные сведения см. в [этом разделе](#window-host-context-for-xaml-islands).

:heavy_check_mark: Чтобы обеспечить поддержку [контракта отправки данных](/windows/uwp/app-to-app/share-data) в приложении WPF, Windows Forms или Win32 на C++, оно должно использовать интерфейс [IDataTransferManagerInterop](https://docs.microsoft.com/windows/win32/api/shobjidl_core/nn-shobjidl_core-idatatransfermanagerinterop) для получения объекта [DataTransferManager](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datatransfermanager) с целью инициировать операцию общего доступа для определенного окна. Использование этого интерфейса в приложении WPF демонстрируется в [примере ShareSource](https://github.com/microsoft/Windows-classic-samples/tree/master/Samples/ShareSource).

:heavy_check_mark: Использование `x:Bind` с размещенными элементами управления в XAML Islands не поддерживается. Вам необходимо объявить модель данных в библиотеке .NET Standard.

### <a name="not-supported"></a>Не поддерживается

:no_entry_sign: Использование элемента управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения сторонних элементов управления UWP на языке C# в приложениях WPF и Windows Forms, предназначенных для .NET Framework. Этот сценарий поддерживается только в приложениях, предназначенных для .NET Core 3.

:no_entry_sign: Содержимое XAML UWP в XAML Islands во время выполнения не реагирует на изменения темы Windows с темной на светлую или обратно. Но содержимое реагирует во время выполнения на изменение режима высокой контрастности.

:no_entry_sign: Добавление элемента управления **WebView** в пользовательский элемент управления (в потоке, вне потока или вне процесса).

:no_entry_sign: Элемент управления [MediaPlayer](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer) и основной элемент управления [MediaPlayerElement](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/mediaplayerelement) не поддерживаются в полноэкранном режиме.

:no_entry_sign: Ввод текста в представлении рукописного ввода. Дополнительные сведения об этой возможности см. в [этой статье](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/text-handwriting-view).

:no_entry_sign: Текстовые элементы управления, которые используют ссылки на содержимое `@Places` и `@People`. Дополнительные сведения об этой возможности см. в [этой статье](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/content-links).

:no_entry_sign: XAML Islands не поддерживают размещение класса [ContentDialog](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog), который содержит элемент управления, который принимает ввод текста, например [TextBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox), [RichEditBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox) или [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox). В этом случае элемент управления для ввода не будет правильно реагировать на нажатие клавиш. Для реализации подобной функциональности с помощью XAML Island мы рекомендуем разместить класс [Popup](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.Popup), содержащий элемент управления для ввода.

:no_entry_sign: Сейчас XAML не поддерживает отображение SVG-файлов в размещенном элементе управления [Windows.UI.Xaml.Controls.Image](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image) или с помощью объекта [Windows.UI.Xaml.Media.Imaging.SvgImageSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.svgimagesource). В качестве решения преобразуйте файлы изображений, которые требуется отобразить, в растровые форматы, такие как JPG или PNG.

### <a name="window-host-context-for-xaml-islands"></a>Контекст узла с окном для объектов XAML Island

При размещении объектов XAML Island в классическом приложении можно одновременно использовать несколько деревьев содержимого XAML, выполняющихся в одном и том же потоке. Для доступа к корневому элементу дерева содержимого XAML в XAML Island и получения связанных сведений о контексте, в котором он размещен, используйте класс [XamlRoot](https://docs.microsoft.com/uwp/api/windows.ui.xaml.xamlroot). Классы [CoreWindow](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow), [ApplicationView](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview) и [Window](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window) не предоставляют правильную информацию об объектах XAML Island. Объекты [CoreWindow](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow) и [Window](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window) существуют в потоке и доступны для приложения, но они не возвращают значимые границы или видимость (они всегда невидимы и имеют размер 1 x 1). Дополнительные сведения см. в [этом разделе](/windows/uwp/design/layout/show-multiple-views#windowing-hosts).

Например, чтобы получить ограничивающий прямоугольник окна, содержащего элемент управления UWP, размещенный в XAML Island, используйте свойство [XamlRoot.Size](https://docs.microsoft.com/uwp/api/windows.ui.xaml.xamlroot.size) элемента управления. Поскольку каждый элемент управления UWP, который может размещаться в XAML Island, является производным от [Windows.UI.Xaml.UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), для доступа к объекту **XamlRoot** можно использовать свойство [XamlRoot](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.xamlroot) элемента управления.

```csharp
Size windowSize = myUWPControl.XamlRoot.Size;
```

Не используйте свойство [CoreWindows.Bounds](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.bounds) для получения ограничивающего прямоугольника.

```csharp
// This will return incorrect information for a UWP control that is hosted in a XAML Island.
Rect windowSize = CoreWindow.GetForCurrentThread().Bounds;
```

Таблицу рекомендуемых замен [XamlRoot](https://docs.microsoft.com/uwp/api/windows.ui.xaml.xamlroot) и распространенных, связанных с окнами API-интерфейсов, использования которых следует избегать в контексте объектов XAML Island, см. в [этом разделе](/windows/uwp/design/layout/show-multiple-views#make-code-portable-across-windowing-hosts).

Использование этого интерфейса в приложении WPF демонстрируется в [примере ShareSource](https://github.com/microsoft/Windows-classic-samples/tree/master/Samples/ShareSource).

## <a name="feature-roadmap"></a>Стратегия развития функций

Ниже приведено текущее состояние функций, имеющих отношение к XAML Islands.

* **Приложения Win32 на C++** . API размещения UWP XAML рассматривается как API версии 1.0, начиная с Windows 10 версии 1903.
* **Управляемые приложения, предназначенные для .NET Framework 4.6.2 и более поздних версий**. Элементы управления XAML Island, доступные в [пакетах NuGet версии 6.0.0](#configure-your-project-to-use-the-xaml-island-net-controls), рассматриваются как элементы версии 1.0 для приложений, предназначенных для .NET Framework 4.6.2 и более поздних версий.
* **Управляемые приложения, предназначенные для .NET Core 3.0 и более поздних версий**. Элементы управления, доступные в [пакетах NuGet версии 6.0.0](#configure-your-project-to-use-the-xaml-island-net-controls), еще находятся на стадии предварительной версии для разработчиков для приложений, предназначенных для .NET Core 3.0 и более поздних версий. Выход версии 1.0 этих элементов управления для .NET Core 3.0 и более поздних версий планируется в последующем выпуске.

## <a name="additional-resources"></a>Дополнительные ресурсы

Дополнительные сведения и учебники по использованию элементов управления XAML Island см. в следующих статьях и ресурсах:

* [Учебник по модернизации приложения WPF](modernize-wpf-tutorial.md). В этом учебнике содержатся пошаговые инструкции о том, как использовать заключенные в оболочку элементы управления и элементы управления ведущего приложения в наборе средств сообщества Windows, чтобы добавить элементы управления UWP в существующее бизнес-приложение WPF. В этом учебнике приведен полный код для приложения WPF, а также подробные инструкции по каждому этапу процесса.
* [Примеры кода объектов XAML Island.](https://github.com/microsoft/Xaml-Islands-Samples) Этот репозиторий содержит примеры для Windows Forms, WPF и C++/Win32, в которых демонстрируется использование объектов XAML Island.
* [Обновления и стратегия развития элементов XAML Island версии 1](https://blogs.windows.com/windowsdeveloper/2019/06/13/xaml-islands-v1-updates-and-roadmap). В этой записи блога рассматриваются многие распространенные вопросы об объектах XAML Island, а также приведена подробная схема разработки.
