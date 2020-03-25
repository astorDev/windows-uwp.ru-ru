---
description: В этой статье обсуждаются современные сценарии размещения XAML для C++ приложений Win32.
title: Расширенные сценарии для островов XAML в C++ приложениях Win32
ms.date: 03/23/2020
ms.topic: article
keywords: Windows 10, UWP, CPP, Win32, острова XAML, элементы управления с оболочкой, стандартные элементы управления
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 50ee005fc0de52a3e0217a71fb3d391445c486db
ms.sourcegitcommit: c660def841abc742600fbcf6ed98e1f4f7beb8cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226238"
---
# <a name="advanced-scenarios-for-xaml-islands-in-c-win32-apps"></a>Расширенные сценарии для островов XAML в C++ приложениях Win32

Для размещения [стандартных элементов управления UWP](host-standard-control-with-xaml-islands-cpp.md) и [размещения пользовательских элементов управления UWP](host-custom-control-with-xaml-islands-cpp.md) приводятся инструкции и примеры для размещения островов XAML в C++ приложении Win32. Однако примеры кода в этих статьях не отвечают многим расширенным сценариям, которые могут потребоваться для работы настольных приложений, чтобы обеспечить плавное взаимодействие с пользователем. В этой статье приводятся рекомендации по некоторым из этих сценариев и ссылки на соответствующие примеры кода.

## <a name="keyboard-input"></a>Ввод с клавиатуры

Чтобы правильно обрабатывать ввод с клавиатуры для каждого острова XAML, приложение должно передать все сообщения Windows в платформу XAML UWP, чтобы определенные сообщения могли обрабатываться правильно. Для этого в каком-то месте приложения, которое может получить доступ к циклу обработки сообщений, приведите объект **десктопвиндовксамлсаурце** для каждого острова XAML к интерфейсу **IDesktopWindowXamlSourceNative2** com. Затем вызовите метод **претранслатемессаже** этого интерфейса и передайте текущее сообщение.

  * Win32:: приложение может вызывать **претранслатемессаже** непосредственно в основном цикле обработки сообщений. **C++** Пример см. в файле [ксамлбридже. cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Samples/Win32/SampleCppApp/XamlBridge.cpp#L16) .

  * **WPF:** Приложение может вызвать **претранслатемессаже** из обработчика событий для события [компонентдиспатчер. среадфилтермессаже](https://docs.microsoft.com/dotnet/api/system.windows.interop.componentdispatcher.threadfiltermessage) . Пример см. в файле [WindowsXamlHostBase.Focus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs#L177) в наборе средств сообщества Windows.

  * **Windows Forms:** Приложение может вызвать **претранслатемессаже** из переопределения метода [Control. PreprocessMessage](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.preprocessmessage) . Пример см. в файле [WindowsXamlHostBase.KeyboardFocus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs#L100) в наборе средств сообщества Windows.

## <a name="keyboard-focus-navigation"></a>Навигация фокуса клавиатуры

Когда пользователь переходит по элементам пользовательского интерфейса в приложении, используя клавиатуру (например, нажав клавишу **Tab** или клавишу со стрелкой), необходимо программно переместить фокус в объект **десктопвиндовксамлсаурце** и из него. Когда пользователь переходит к **десктопвиндовксамлсаурце**, переместите фокус на первый объект [Windows. UI. XAML. UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) в порядке навигации для пользовательского интерфейса, продолжайте перемещать фокус на следующие объекты **Windows. UI. XAML. UIElement** , когда пользователь циклически переключается по элементам, а затем перемещает фокус обратно из **десктопвиндовксамлсаурце** в родительский элемент пользовательского интерфейса.  

API хостинга UWP XAML предоставляет несколько типов и членов, помогающих выполнять эти задачи.

* При переходе от клавиатуры к **десктопвиндовксамлсаурце**возникает событие [GotFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.gotfocus) . Обработайте это событие и программным образом перенесите фокус на первый размещенный элемент **Windows. UI. XAML. UIElement** с помощью метода [навигатефокус](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.navigatefocus) .

* Когда пользователь находится на последнем элементе с фокусом в **десктопвиндовксамлсаурце** и нажимает клавишу **Tab** или клавишу со стрелкой, возникает событие [такефокусрекуестед](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.takefocusrequested) . Обработайте это событие и программно перемещайте фокус на следующий сфокусированный элемент в ведущем приложении. Например, в приложении WPF, в котором **десктопвиндовксамлсаурце** размещается в [системе. Windows. Interop. HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost), можно использовать метод [MoveFocus](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.movefocus) для перемещения фокуса на следующий сфокусированный элемент в ведущем приложении.

Примеры, демонстрирующие, как это сделать в контексте рабочего примера приложения, см. в следующих файлах кода:

  * /Win32: см. файл [ксамлбридже. cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Samples/Win32/SampleCppApp/XamlBridge.cpp) .  **C++**

  * **WPF:** См. файл [WindowsXamlHostBase.Focus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs) в наборе средств сообщества Windows.  

  * **Windows Forms:** См. файл [WindowsXamlHostBase.KeyboardFocus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs) в наборе средств сообщества Windows.

## <a name="handle-layout-changes"></a>Обработку изменений макета

Когда пользователь изменяет размер родительского элемента пользовательского интерфейса, необходимо выполнить все необходимые изменения макета, чтобы элементы управления UWP отображались должным образом. Ниже приведены некоторые важные сценарии, которые следует учитывать.

* В приложении C++ Win32, когда приложение обрабатывает WM_SIZE сообщение, оно может переместить размещенный остров XAML с помощью функции [SetWindowPos](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) . Пример см. в файле кода [SampleApp. cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Samples/Win32/SampleCppApp/SampleApp.cpp#L170) .

* Когда родительский элемент пользовательского интерфейса должен получить размер прямоугольной области, необходимой для размещения элемента **Windows. UI. XAML. UIElement** , который вы размещаете в **десктопвиндовксамлсаурце**, вызовите метод [Measure](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) элемента **Windows. UI. XAML. UIElement**. Например:

    * В приложении WPF это можно сделать из метода [MeasureOverride](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.measureoverride) [HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) , на котором размещена **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHostBase.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

    * В Windows Forms приложении это можно сделать из метода [Жетпреферредсизе](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.getpreferredsize) [элемента управления](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , размещающего **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHostBase.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

* При изменении размера родительского элемента пользовательского интерфейса вызовите метод [упорядочения](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) корневого **интерфейса Windows. UI. XAML. UIElement** , который вы размещаете в **десктопвиндовксамлсаурце**. Например:

    * В приложении WPF это можно сделать из метода [ArrangeOverride](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.arrangeoverride) объекта [HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) , в котором размещается **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHost.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

    * В Windows Forms приложении это можно сделать из обработчика для события [SizeChanged](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.sizechanged) [элемента управления](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , размещающего **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHost.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

## <a name="handle-dpi-changes"></a>Изменение количества точек на дюйм

Платформа XAML UWP обрабатывает изменения DPI для размещаемых элементов управления UWP автоматически (например, когда пользователь перетаскивает окно между мониторами с разными DPI на экране). Для оптимальной работы рекомендуется, чтобы Windows Forms, WPF или C++ приложение Win32 были настроены для отслеживания dpi на уровне каждого монитора.

Чтобы настроить приложение для отслеживания DPI на уровне каждого монитора, добавьте в проект [параллельный манифест сборки](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) и задайте для элемента **\<Дпиаваренесс\>** значение **PerMonitorV2**. Дополнительные сведения об этом значении см. в описании для [DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2](https://docs.microsoft.com/windows/desktop/hidpi/dpi-awareness-context).

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

## <a name="related-topics"></a>Связанные разделы

* [Размещение элементов управления XAML UWP в классических приложениях (острова XAML)](xaml-islands.md)
* [Использование API размещения UWP XAML в приложении C++ Win32](using-the-xaml-hosting-api.md)
* [Размещение стандартного элемента управления UWP в приложении C++ Win32](host-standard-control-with-xaml-islands-cpp.md)
* [Размещение пользовательского элемента управления UWP в приложении C++ Win32](host-custom-control-with-xaml-islands-cpp.md)
* [Примеры кода островов XAML](https://github.com/microsoft/Xaml-Islands-Samples)
* [C++Пример XAML-островов Win32](https://github.com/microsoft/Xaml-Islands-Samples/tree/master/Samples/Win32/SampleCppApp)
