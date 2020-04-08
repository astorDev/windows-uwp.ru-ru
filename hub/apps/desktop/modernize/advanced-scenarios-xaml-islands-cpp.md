---
description: В этой статье обсуждаются расширенные сценарии применения XAML Island для приложений C++ на платформе Win32.
title: Расширенные сценарии для XAML Islands в приложениях Win32 на C++
ms.date: 03/23/2020
ms.topic: article
keywords: Windows 10, UWP, CPP, Win32, XAML Islands, заключенные в оболочку элементы управления, стандартные элементы управления
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 50ee005fc0de52a3e0217a71fb3d391445c486db
ms.sourcegitcommit: c660def841abc742600fbcf6ed98e1f4f7beb8cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226238"
---
# <a name="advanced-scenarios-for-xaml-islands-in-c-win32-apps"></a>Расширенные сценарии для XAML Islands в приложениях Win32 на C++

В статьях [о размещении стандартного элемента управления UWP](host-standard-control-with-xaml-islands-cpp.md) и [о размещении пользовательского элемента управления UWP](host-custom-control-with-xaml-islands-cpp.md) представлены инструкции и примеры по размещению XAML Islands в приложениях Win32 на C++. Но примеры кода в этих статьях не подходят для многих расширенных сценариев, которые могут обеспечить эффективное взаимодействие с пользователем в классических приложениях. Эта статья содержит рекомендации по некоторым из таких сценариев и ссылки на соответствующие примеры кода.

## <a name="keyboard-input"></a>Ввод с клавиатуры

Чтобы правильно обрабатывать ввод с клавиатуры для каждого XAML Island, приложение должно передавать все сообщения Windows платформе UWP XAML, чтобы она правильно обрабатывала нужные сообщения. Для этого создайте для каждого XAML Island объект **DesktopWindowXamlSource**, привязанный к интерфейсу COM **IDesktopWindowXamlSourceNative2** в любом месте приложения, из которого есть доступ к циклу сообщений. Затем вызовите метод **PreTranslateMessage** из этого интерфейса и передайте ему текущее сообщение.

  * **Win32 на C++** : приложение может вызвать **PreTranslateMessage** прямо из главного цикла обработки сообщений. Пример можно найти в файле [XamlBridge.cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Samples/Win32/SampleCppApp/XamlBridge.cpp#L16).

  * **WPF:** приложение может вызывать **PreTranslateMessage** из обработчика события [ComponentDispatcher.ThreadFilterMessage](https://docs.microsoft.com/dotnet/api/system.windows.interop.componentdispatcher.threadfiltermessage). Пример представлен в файле [WindowsXamlHostBase.Focus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs#L177) из набора средств сообщества Windows.

  * **Windows Forms**: приложение может вызывать **PreTranslateMessage** из перегрузки метода [Control.PreprocessMessage](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.preprocessmessage). Пример представлен в файле [WindowsXamlHostBase.KeyboardFocus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs#L100) из набора средств сообщества Windows.

## <a name="keyboard-focus-navigation"></a>Перемещение фокуса с клавиатуры

Когда пользователь перемещается между элементами пользовательского интерфейса в приложении с помощью клавиатуры (например, нажимая клавишу **Tab** или клавиши со стрелками), вам нужно программным образом включать и отключать фокус ввода в объекте **DesktopWindowXamlSource**. Когда пользователь достигнет объекта **DesktopWindowXamlSource**, передайте фокус первому из объектов [Windows.UI.Xaml.UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) в соответствии с порядком навигации в вашем пользовательском интерфейсе, затем поочередно перебирайте объекты **Windows.UI.Xaml.UIElement** при перемещениях пользователя, и наконец уберите фокус из **DesktopWindowXamlSource** в его родительский элемент пользовательского интерфейса.  

API, который размещает UWP XAML, предоставляет вам несколько типов и членов для упрощения этих задач.

* Когда навигация с помощью клавиатуры достигает объекта **DesktopWindowXamlSource**, создается событие [GotFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.gotfocus). Обрабатывайте это сообщение, программным образом перемещая фокус на первый из размещенных объектов **Windows.UI.Xaml.UIElement** с помощью метода [NavigateFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.navigatefocus).

* Когда пользователь достигает последнего из элементов, поддерживающих получение фокуса, в **DesktopWindowXamlSource** и нажимает клавишу **Tab** или клавишу со стрелкой, создается событие [TakeFocusRequested](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.takefocusrequested). Обрабатывайте это сообщение, программным образом перемещая фокус на следующий элемент в базовом приложении, поддерживающий фокус. Например, если в приложении WPF элемент **DesktopWindowXamlSource** размещается в [System.Windows.Interop.HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost), вы можете с помощью метода [MoveFocus](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.movefocus) переместить фокус на следующий элемент в базовом приложении, поддерживающий фокус.

Примеры, демонстрирующие этот механизм в контексте рабочего примера приложения, можно найти в следующих файлах кода.

  * **Win32 на C++:** см. файл [XamlBridge.cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Samples/Win32/SampleCppApp/XamlBridge.cpp).

  * **WPF:** см. файл [WindowsXamlHostBase.Focus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs) из набора средств сообщества Windows.  

  * **Windows Forms:** см. файл [WindowsXamlHostBase.KeyboardFocus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs) из набора средств сообщества Windows.

## <a name="handle-layout-changes"></a>Обработка изменений макета

Когда пользователь изменяет размер родительского элемента пользовательского интерфейса, вам следует обработать все необходимые изменения макета, чтобы элементы управления UWP отображались правильно. Вот несколько важных факторов, которые нужно учитывать.

* В приложении Win32 на C++ при обработке сообщения WM_SIZE можно переместить размещенный XAML Island с помощью функции [SetWindowPos](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos). Пример можно изучить в файле кода [SampleApp.cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Samples/Win32/SampleCppApp/SampleApp.cpp#L170).

* Когда родительскому элементу пользовательского интерфейса потребуется получить размер прямоугольной области для размещения **Windows.UI.Xaml.UIElement**, который находится в вашем **DesktopWindowXamlSource**, вызовите метод [Measure](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) из **Windows.UI.Xaml.UIElement**. Например:

    * В приложении WPF это можно выполнять из метода [MeasureOverride](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.measureoverride) в [HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost), где размещается этот **DesktopWindowXamlSource**. Пример представлен в файле [WindowsXamlHostBase.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) из набора средств сообщества Windows.

    * В приложении Windows Forms это можно выполнять из метода [GetPreferredSize](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.getpreferredsize) в [Control](https://docs.microsoft.com/dotnet/api/system.windows.forms.control), где размещается этот **DesktopWindowXamlSource**. Пример представлен в файле [WindowsXamlHostBase.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) из набора средств сообщества Windows.

* Когда изменится размер родительского элемента пользовательского интерфейса, вызовите метод [Arrange](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) из корневого элемента **Windows.UI.Xaml.UIElement**, который вы размещаете в **DesktopWindowXamlSource**. Например:

    * В приложении WPF это можно выполнять из метода [ArrangeOverride](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.arrangeoverride) объекта [HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost), где размещается этот **DesktopWindowXamlSource**. Пример представлен в файле [WindowsXamlHost.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) из набора средств сообщества Windows.

    * В приложении Windows Forms это можно выполнять из обработчика события [SizeChanged](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.sizechanged) в [Control](https://docs.microsoft.com/dotnet/api/system.windows.forms.control), где размещается этот **DesktopWindowXamlSource**. Пример представлен в файле [WindowsXamlHost.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) из набора средств сообщества Windows.

## <a name="handle-dpi-changes"></a>Обработка изменений количества точек на дюйм

Платформа UWP XAML автоматически обрабатывает изменения количества точек на дюйм для размещенных элементов управления UWP (например, при перетаскивании окна между мониторами с разными значениями количества точек на дюйм). Чтобы оптимизировать взаимодействие с пользователем, мы рекомендуем в приложениях Windows Forms, WPF и Win32 на C++ учитывать значения количества точек на дюйм для каждого монитора.

Чтобы настроить в приложении учет количества точек на дюйм для каждого монитора, добавьте [манифест сборки для параллельного отображения](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) в проект и присвойте элементу **\<dpiAwareness\>** значение **PerMonitorV2**. Дополнительные сведения об этом значении см. в статье с описанием [DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2](https://docs.microsoft.com/windows/desktop/hidpi/dpi-awareness-context).

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
        <windowsSettings>
            <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2</dpiAwareness>
        </windowsSettings>
    </application>
</assembly>
```

## <a name="related-topics"></a>Связанные темы

* [Элементы управления UWP XAML в классических приложениях (XAML Islands)](xaml-islands.md)
* [Использование API размещения XAML платформы UWP в приложении Win32 на C++](using-the-xaml-hosting-api.md)
* [Размещение стандартного элемента управления UWP в приложении Win32 на C++](host-standard-control-with-xaml-islands-cpp.md)
* [Размещение настраиваемого элемента управления UWP в приложении Win32 на C++](host-custom-control-with-xaml-islands-cpp.md)
* [Примеры кода для XAML Islands](https://github.com/microsoft/Xaml-Islands-Samples)
* [Пример XAML Islands на C++ для Win32](https://github.com/microsoft/Xaml-Islands-Samples/tree/master/Samples/Win32/SampleCppApp)
