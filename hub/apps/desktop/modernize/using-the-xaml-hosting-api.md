---
description: В этой статье описывается размещение пользовательского интерфейса XAML UWP в приложении рабочего стола.
title: С помощью XAML UWP, интерфейс API размещения в приложении для настольных систем
ms.date: 04/16/2019
ms.topic: article
keywords: Windows 10, универсальной платформы Windows, windows forms, wpf, win32, о-ва xaml
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: fa38c952d4d46d83ea9b4e9f0db5f516fe09ce59
ms.sourcegitcommit: f9a30bfd1e8eab50d0b1db97dd2f650ce66b5d34
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67690892"
---
# <a name="using-the-uwp-xaml-hosting-api-in-a-desktop-application"></a>С помощью XAML UWP, интерфейс API размещения в приложении для настольных систем

Начиная с Windows 10, версия 1903, классических приложений не UWP (включая WPF, Windows Forms, и C++ приложений Win32) можно использовать *XAML UWP, интерфейс API размещения* для размещения элементов управления универсальной платформы Windows в любой элемент пользовательского интерфейса, с которым связан Дескриптор окна (HWND). Этот API позволяет не UWP настольных приложений для использования новейших функций пользовательского интерфейса Windows 10, которые доступны только через элементы управления универсальной платформы Windows. Например, классических приложений не UWP можно использовать этот API для размещения элементов управления универсальной платформы Windows, использующие [Fluent Design System](/windows/uwp/design/fluent-design-system/index) и поддерживают [рукописного ввода Windows](/windows/uwp/design/input/pen-and-stylus-interactions).

XAML UWP, интерфейс API размещения предоставляет основу для более широкого набора элементов управления, предоставляемые нами для предоставления разработчикам возможности перенести пользовательского интерфейса Fluent в не UWP настольных приложений. Эта функция называется *XAML о-ва*. Общие сведения об этой функции см. в разделе [управления универсальной платформы Windows в настольных приложениях](xaml-islands.md).

> [!NOTE]
> Если у вас есть отзыв о XAML о-ва, создание нового вопроса в [Microsoft.Toolkit.Win32 репозитория](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/issues) , а также оставить свои комментарии существует. Если вы хотите отправить свой отзыв в частном порядке, вы можете отправить его в XamlIslandsFeedback@microsoft.com. Свои предложения и сценарии критически важны для нас.

## <a name="should-you-use-the-uwp-xaml-hosting-api"></a>Следует ли использовать XAML UWP, интерфейс API размещения?

XAML UWP, интерфейс API размещения предоставляет низкоуровневой инфраструктурой для размещения элементов управления универсальной платформы Windows в настольных приложениях. Некоторые типы приложений для настольных систем, имеют возможность использовать альтернативный, более удобный API-интерфейсы для достижения этой цели.  

* Если у вас есть приложение рабочего стола C++ Win32 и требуется для размещения элементов управления универсальной платформы Windows в приложении, необходимо использовать XAML UWP, интерфейс API размещения. Существуют нет альтернативы для таких приложений.

* Для приложений WPF и Windows Forms, мы настоятельно рекомендуем использовать [элементов управления в оболочку](xaml-islands.md#wrapped-controls) и [элементы управления ведущего приложения](xaml-islands.md#host-controls) в наборе средств сообщества Windows вместо использования XAML UWP, интерфейс API размещения напрямую. Эти элементы управления использовать XAML UWP, интерфейс API размещения внутренним образом и реализовать все возможности, в противном случае необходимо обработать самостоятельно, если вы использовали XAML UWP, интерфейс API размещения напрямую, включая клавиатуры меняется в навигации и макет. Тем не менее можно использовать XAML UWP, размещение API непосредственно в эти типы приложений, если вы выберете.

В этой статье описывается использование XAML UWP, интерфейс API размещения непосредственно в приложении вместо элементов управления, предоставляемые Windows Community Toolkit.

## <a name="prerequisites"></a>предварительные требования

XAML UWP, интерфейс API размещения имеет следующие предварительные требования:

* Windows 10, версия 1903 (или более поздней версии) и соответствующие сборки пакета SDK для Windows.
* Настройка проекта для использования API среды выполнения Windows и включить XAML о-ва, следуя [эти инструкции](xaml-islands.md#requirements).

## <a name="architecture-of-xaml-islands"></a>Архитектура XAML о-ва

XAML UWP, интерфейс API размещения включает эти основные типы среды выполнения Windows и COM-интерфейсы:

* [**WindowsXamlManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager). Этот класс представляет платформа XAML UWP. Этот класс предоставляет один статический [ **InitializeForCurrentThread** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread) метод, который инициализирует платформу UWP XAML в текущем потоке в приложении.

* [**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource). Этот класс представляет экземпляр содержимого XAML UWP, которое размещается в приложении рабочего стола. Самым важным элементом этого класса является [ **содержимого** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) свойство. Назначить это свойство, чтобы [ **Windows.UI.Xaml.UIElement** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) , необходимая для размещения. Этот класс также содержит другие элементы для маршрутизации навигации с фокусом клавиатуры в и из него острова XAML.

* **IDesktopWindowXamlSourceNative** и **IDesktopWindowXamlSourceNative2** COM-интерфейсов. **IDesktopWindowXamlSourceNative** предоставляет **AttachToWindow** метод, который используется для присоединения островам XAML в приложении к родительскому элементу пользовательского интерфейса. **IDesktopWindowXamlSourceNative2** предоставляет **PreTranslateMessage** метод, который позволяет платформе UWP XAML для обработки определенных сообщений Windows правильно.
    > [!NOTE]
    > Эти интерфейсы объявляются в **windows.ui.xaml.hosting.desktopwindowxamlsource.h** файл заголовка в пакете Windows SDK. По умолчанию этот файл расположен в % programfiles (x86) %\Windows Kits\10\Include\\< номер сборки\>\um. В проекте C++ Win32 можно непосредственно ссылаться на файл заголовка. В проекте WPF или Windows Forms, вам потребуется объявления интерфейсов в коде приложения с помощью [ **ComImport** ](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.comimportattribute) атрибута. Убедитесь, что ваш объявления интерфейса в точности соответствовать объявлениям в **windows.ui.xaml.hosting.desktopwindowxamlsource.h**.

На следующей схеме показана иерархия объектов в остров XAML, который размещен в приложении для настольных систем.

![Архитектура DesktopWindowXamlSource](images/xaml-islands/xaml-hosting-api-rev2.png)

* На базовом уровне — это элемент пользовательского интерфейса в приложении, где будет размещаться остров XAML. Этот элемент пользовательского интерфейса должен иметь дескриптор окна (HWND). Примеры элементов пользовательского интерфейса, в которых вы можете разместить островам XAML [ **System.Windows.Interop.HwndHost** ](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) для приложений WPF [ **System.Windows.Forms.Control** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) для приложений Windows Forms и [окно](https://docs.microsoft.com/windows/desktop/winmsg/about-windows) для C++ приложений Win32.

* На следующем уровне — **DesktopWindowXamlSource** объекта. Этот объект предоставляет инфраструктуру для размещения остров XAML. Для создания этого объекта и подключите его к родительскому элементу пользовательского интерфейса отвечает пользовательский код.

* При создании **DesktopWindowXamlSource**, этот объект автоматически создает собственный дочернее окно для размещения элемента управления универсальной платформы Windows. Этот собственный дочернее окно главным образом абстрагируется от кода, но можно получить доступ к его дескриптор (HWND), при необходимости.

* Наконец на верхнем уровне — элемент управления универсальной платформы Windows, который необходимо разместить в приложении рабочего стола. Это может быть любой объект универсальной платформы Windows, который является производным от [ **Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), включая любой элемент управления универсальной платформы Windows, предоставляемый пакетом SDK для Windows, а также собственные пользовательские элементы управления.

## <a name="related-samples"></a>Связанные примеры

Способ использования XAML UWP, интерфейс API размещения в коде зависит от типа приложения, приложения и других факторов. Чтобы продемонстрировать способы использования этого API в рамках всего приложения, эта статья относится к коду из приведенные ниже примеры.

### <a name="c-win32"></a>C++ Win32

[C++Пример Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island). В этом примере демонстрируется полная реализация, на котором размещается пользовательский элемент управления универсальной платформы Windows в работе C++ приложение Win32 (то есть приложение, не встроен в пакет MSIX).

### <a name="wpf-and-windows-forms"></a>WPF и Windows Forms

[WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) элемент управления в наборе средств сообщества Windows работает как пример ссылку с помощью универсальной платформы Windows, интерфейс API размещения в приложениях WPF и Windows Forms. Исходный код доступен в следующих расположениях:

  * Для WPF-версии элемента управления [Перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Wpf.UI.XamlHost). WPF-версии является производным от [ **System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost).

  * Для версии Windows Forms элемента управления [Перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Forms.UI.XamlHost). Версия Windows Forms является производным от [ **System.Windows.Forms.Control**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control).

## <a name="host-uwp-xaml-controls"></a>Элементы управления XAML UWP узла

Ниже приведены основные действия с использованием XAML UWP, интерфейс API размещения для размещения элемента управления универсальной платформы Windows в приложении.

1. Инициализировать framework XAML UWP для текущего потока, прежде чем приложение создает любой из [ **Windows.UI.Xaml.UIElement** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) объекты, которые в нем будет размещаться в [  **DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource).

    * Если приложение создает **DesktopWindowXamlSource** объекта перед созданием любой из **Windows.UI.Xaml.UIElement** объектов, эта платформа будет инициализироваться автоматически при создании экземпляра **DesktopWindowXamlSource** объекта. В этом случае не нужно добавить код для инициализации платформы собственные.

    * Тем не менее если приложение создает **Windows.UI.Xaml.UIElement** объектов перед созданием **DesktopWindowXamlSource** объект, который будет размещать их, ваше приложение должно вызвать статический [ **WindowsXamlManager.InitializeForCurrentThread** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread) метод, чтобы явно инициализировать XAML UWP framework, выпущенных до **Windows.UI.Xaml.UIElement** объекты создать экземпляр. Приложения обычно этот метод следует вызывать при родительского элемента пользовательского интерфейса, на котором размещена **DesktopWindowXamlSource** создается.

    ```cppwinrt
    Windows::UI::Xaml::Hosting::WindowsXamlManager windowsXamlManager =
        Windows::UI::Xaml::Hosting::WindowsXamlManager::InitializeForCurrentThread();
    ```

    ```csharp
    global::Windows.UI.Xaml.Hosting.WindowsXamlManager windowsXamlManager =
        global::Windows.UI.Xaml.Hosting.WindowsXamlManager.InitializeForCurrentThread();
    ```

    > [!NOTE]
    > Этот метод возвращает [ **WindowsXamlManager** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) , содержащий ссылку на платформу UWP XAML. Можно создать столько **WindowsXamlManager** объектов в данного потока. Тем не менее так как каждый объект содержит ссылку на framework XAML UWP, следует освободить объекты, чтобы убедиться, что в конечном итоге освобождать ресурсы XAML.

2. Создание [ **DesktopWindowXamlSource** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) объекта и присоединить ее к родительскому элементу пользовательского интерфейса в приложении, который связан с дескриптором окна.

    Чтобы сделать это, необходимо выполните следующие действия:

    1. Создание **DesktopWindowXamlSource** объекта и приведите его к **IDesktopWindowXamlSourceNative** или **IDesktopWindowXamlSourceNative2** COM-интерфейса.

    2. Вызовите **AttachToWindow** метод **IDesktopWindowXamlSourceNative** или **IDesktopWindowXamlSourceNative2** интерфейса и передает дескриптор окна родительский элемент пользовательского интерфейса в приложении.

    3. Установить исходный размер внутреннего дочернего окна, содержащихся в **DesktopWindowXamlSource**. По умолчанию этот внутренний дочернее окно присваивается ширину и высоту 0. Если не задан размер окна, все элементы управления универсальной платформы Windows, добавляемый **DesktopWindowXamlSource** не будут видны. Для доступа к внутренней дочернее окно в **DesktopWindowXamlSource**, использовать **WindowHandle** свойство **IDesktopWindowXamlSourceNative** или **IDesktopWindowXamlSourceNative2** интерфейс. В следующих примерах используется [SetWindowPos](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) функцию для задания размера окна.

    Ниже приведены некоторые примеры кода, демонстрирующие этот процесс.

    ```cppwinrt
    // This example assumes you already have an HWND variable named 'parentHwnd' that
    // contains the handle of the parent window.
    Windows::UI::Xaml::Hosting::DesktopWindowXamlSource desktopWindowXamlSource;
    auto interop = desktopWindowXamlSource.as<IDesktopWindowXamlSourceNative>();
    check_hresult(interop->AttachToWindow(parentHwnd));

    HWND childInteropHwnd = nullptr;
    interop->get_WindowHandle(&childInteropHwnd);

    SetWindowPos(childInteropHwnd, 0, 0, 0, 300, 300, SWP_SHOWWINDOW);
    ```

    ```csharp
    // This WPF example assumes you already have an HwndHost named 'parentHwndHost'
    // that will act as the parent UI element for your XAML Island. It also assumes
    // you have used the DllImport attribute to import SetWindowPos from user32.dll
    // as a static method into a class named NativeMethods.
    Windows.UI.Xaml.Hosting.DesktopWindowXamlSource desktopWindowXamlSource =
        new Windows.UI.Xaml.Hosting.DesktopWindowXamlSource();

    IntPtr iUnknownPtr = System.Runtime.InteropServices.Marshal.GetIUnknownForObject(
        desktopWindowXamlSource);
    IDesktopWindowXamlSourceNative desktopWindowXamlSourceNative =
        System.Runtime.InteropServices.Marshal.Marshal.GetTypedObjectForIUnknown(
            iUnknownPtr, typeof(IDesktopWindowXamlSourceNative))
            as IDesktopWindowXamlSourceNative;

    desktopWindowXamlSourceNative.AttachToWindow(parentHwndHost.Handle);

    var childInteropHwnd = desktopWindowXamlSourceNative.WindowHandle;
    NativeMethods.SetWindowPos(childInteropHwnd, HWND_TOP, 0, 0, 300, 300, SWP_SHOWWINDOW);
    ```

3. Задайте **Windows.UI.Xaml.UIElement** необходимо разместить в [ **содержимого** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) свойство вашей **DesktopWindowXamlSource** объекта. В следующем примере задается [ **Windows.UI.Xaml.Controls.Grid** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid) с именем ```myGrid``` для **содержимого** свойство.

   ```cppwinrt
   desktopWindowXamlSource.Content(myGrid);
   ```

   ```csharp
   desktopWindowXamlSource.Content = myGrid;
   ```

Полные примеры, демонстрирующие эти задачи в контексте рабочий пример приложения см. следующие файлы кода:

  * **C++ Win32:** См. в разделе [XamlBridge.cpp](https://github.com/marb2000/XamlIslands/blob/master/19H1_Insider_Samples/CppWin32App_With_Island/SampleCppApp/XamlBridge.cpp) файл [ C++ пример Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island).

  * **WPF:** См. в разделе [WindowsXamlHostBase.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHost.cs) файлы в наборе средств сообщества Windows.  

  * **Windows Forms:** См. в разделе [WindowsXamlHostBase.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHost.cs) файлы в наборе средств сообщества Windows.

## <a name="handle-keyboard-input-and-focus-navigation"></a>Обрабатывать входные данные и фокус клавиатуры

Существует несколько моментов, вашему приложению нужно сделать, чтобы правильно обрабатывать входные данные и фокус клавиатуры, когда он размещает острова XAML.

### <a name="keyboard-input"></a>Ввод с клавиатуры

Правильно обрабатывать ввод с клавиатуры для каждого остров XAML, приложение должно передать все сообщения Windows с платформой UWP XAML таким образом, чтобы некоторые сообщения могут обрабатываться правильно. Чтобы сделать это, в любом месте с доступом к цикл обработки сообщений приложения, приведите **DesktopWindowXamlSource** объект для каждого XAML-Айленд для **IDesktopWindowXamlSourceNative2** COM-интерфейса. Затем вызовите **PreTranslateMessage** метод этот интерфейс и передайте в текущем сообщении.

  * Объект C++ приложение Win32 может вызвать **PreTranslateMessage** непосредственно в его основной цикл обработки сообщений. Например, см. в разделе [SampleApp.cpp](https://github.com/marb2000/XamlIslands/blob/master/19H1_Insider_Samples/CppWin32App_With_Island/SampleCppApp/SampleApp.cpp#L61) файл кода в [ C++ пример Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island).

  * Приложения WPF можно вызвать **PreTranslateMessage** из обработчика событий для [ **ComponentDispatcher.ThreadFilterMessage** ](https://docs.microsoft.com/dotnet/api/system.windows.interop.componentdispatcher.threadfiltermessage?view=netframework-4.7.2) событий. Например, см. в разделе [WindowsXamlHostBase.Focus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs#L177) файла в наборе средств сообщества Windows.

  * Можно вызвать в приложении Windows Forms **PreTranslateMessage** из переопределения для [ **Control.PreprocessMessage** ](https://docs.microsoft.com/en-us/dotnet/api/system.windows.forms.control.preprocessmessage?view=netframework-4.7.2) метод. Например, см. в разделе [WindowsXamlHostBase.KeyboardFocus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs#L100) файла в наборе средств сообщества Windows.

### <a name="keyboard-focus-navigation"></a>Фокус клавиатуры.

Когда пользователь переходит по элементам пользовательского интерфейса в приложении с помощью клавиатуры (например, нажав **вкладке** или направление/стрелка), вам потребуется программное перемещение фокуса в  **DesktopWindowXamlSource** объекта. При достижении пользователя с помощью клавиатуры **DesktopWindowXamlSource**, перемещение фокуса в первый [ **Windows.UI.Xaml.UIElement** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) объектом в порядке навигации для пользовательского интерфейса, по-прежнему переместить фокус на следующее **Windows.UI.Xaml.UIElement** объекты, как пользователь выполняет циклический переход по элементам, а затем повторно перемещения фокуса из **DesktopWindowXamlSource**и в родительский элемент пользовательского интерфейса.  

XAML UWP, интерфейс API размещения предоставляет несколько типов и членов для выполнения этих задач.

* При навигации с помощью клавиатуры переходит к **DesktopWindowXamlSource**, [ **Получение фокуса** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.gotfocus) события. Обработайте это событие и программное перемещение фокуса к первому размещенных **Windows.UI.Xaml.UIElement** с помощью [ **NavigateFocus** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.navigatefocus) метод.

* Если пользователь находится на последний элемент, способному получать фокус в вашей **DesktopWindowXamlSource** и нажимает клавишу **вкладке** ключ или клавиша со стрелкой, [ **TakeFocusRequested** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.takefocusrequested) события. Обработайте это событие и программное перемещение фокуса к следующему элементу, способному получать фокус в ведущем приложении. Например, в приложении WPF где **DesktopWindowXamlSource** размещается в [ **System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost), можно использовать [  **MoveFocus** ](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.movefocus) метод для передачи фокуса к следующему элементу, способному получать фокус в ведущем приложении.

Примеры, демонстрирующие, как это сделать в контексте рабочий пример приложения см. следующие файлы кода:

  * **WPF:** См. в разделе [WindowsXamlHostBase.Focus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs) файла в наборе средств сообщества Windows.  

  * **Windows Forms:** См. в разделе [WindowsXamlHostBase.KeyboardFocus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs) файла в наборе средств сообщества Windows.

## <a name="handle-layout-changes"></a>Обрабатывать изменения макета

Когда пользователь изменяет размер родительского элемента пользовательского интерфейса, необходимо обрабатывать любые необходимые макета изменения и убедиться, что отображения как предполагается, что элементы управления универсальной платформы Windows. Ниже приведено несколько важных сценариев, которые следует учитывать.

* В C++ приложение Win32, если приложение обрабатывает сообщение WM_SIZE, его можно изменить положение размещенной остров XAML с помощью [SetWindowPos](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) функции. Например, см. в разделе [SampleApp.cpp](https://github.com/marb2000/XamlIslands/blob/master/19H1_Insider_Samples/CppWin32App_With_Island/SampleCppApp/SampleApp.cpp#L191) файл кода в [ C++ пример Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island).

* Когда родительский элемент пользовательского интерфейса необходимо получить размер прямоугольной области, в соответствии **Windows.UI.Xaml.UIElement** , находящихся на **DesktopWindowXamlSource**, вызовите [ **Мер** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) метод **Windows.UI.Xaml.UIElement**. Пример:

    * В приложении WPF может сделать это из [ **MeasureOverride** ](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.measureoverride) метод [ **HwndHost** ](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) , на котором размещена  **DesktopWindowXamlSource**. Например, см. в разделе [WindowsXamlHostBase.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) файла в наборе средств сообщества Windows.

    * В приложении Windows Forms это можно сделать из [ **GetPreferredSize** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.getpreferredsize) метод [ **управления** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , на котором размещена **DesktopWindowXamlSource**. Например, см. в разделе [WindowsXamlHostBase.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) файла в наборе средств сообщества Windows.

* При изменении размера родительского элемента пользовательского интерфейса, вызовите [ **расположение** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) метод корневого **Windows.UI.Xaml.UIElement** , находящихся на  **DesktopWindowXamlSource**. Пример:

    * В приложении WPF может сделать это из [ **ArrangeOverride** ](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.arrangeoverride) метод [ **HwndHost** ](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) объекта, на котором размещена **DesktopWindowXamlSource**. Например, см. в разделе [WindowsXamlHost.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) файла в наборе средств сообщества Windows.

    * В приложении Windows Forms это можно сделать из обработчика для [ **SizeChanged** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.sizechanged) событие [ **управления** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , на котором размещена **DesktopWindowXamlSource**. Например, см. в разделе [WindowsXamlHost.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) файла в наборе средств сообщества Windows.

## <a name="handle-dpi-changes"></a>Обрабатывать изменения DPI

Платформа XAML UWP автоматически обрабатывает изменения DPI для размещенные элементы управления универсальной платформы Windows (например, когда пользователь перетаскивает окна между мониторами с различных разрешений экранов точек на ДЮЙМ). Для получения наилучших результатов рекомендуется в Windows Forms, WPF, или C++ Win32 приложение настроено для помните DPI для каждого монитора.

Чтобы настроить приложение, которые следует учитывать DPI для каждого монитора, добавьте [манифест сборки side-by-side](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) для проекта, а также набор ```<dpiAwareness>``` элемент ```PerMonitorV2```. Дополнительные сведения об этом значении см. в описании для [ **DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2**](https://docs.microsoft.com/windows/desktop/hidpi/dpi-awareness-context).

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

## <a name="host-custom-uwp-xaml-controls"></a>Управляет узла пользовательского XAML для UWP

> [!IMPORTANT]
> Чтобы разместить пользовательский элемент управления XAML UWP, должен иметь исходный код для элемента управления, чтобы к нему можно скомпилировать в приложении.

Если вы хотите разместить пользовательский элемент управления XAML UWP (элемент управления, можно определить самостоятельно или управление, предоставляемое сторонним), необходимо выполнить следующие дополнительные задачи в дополнение к процессу, описанному в [выше](#host-uwp-xaml-controls).

1. Определение пользовательского типа, который является производным от [ **Windows.UI.Xaml.Application** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application) и также реализует [ **IXamlMetadataProvider**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.ixamlmetadataprovider). Этот тип выступает в качестве корневого поставщика метаданных для загрузки метаданных для пользовательских типов XAML UWP в сборках в текущем каталоге приложения.

2. Вызовите [ **GetXamlType** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.ixamlmetadataprovider.getxamltype) метод поставщика метаданных корневой при назначении имя типа элемента управления XAML универсальной платформы Windows (это может назначаться в коде во время выполнения, или можно выбрать для этого необходимо включить назначить в окне Properties в Visual Studio).

    Примеры см. следующие файлы кода.
      * **C++ Win32:** См. в разделе [XamlApplication.cpp](https://github.com/marb2000/XamlIslands/blob/master/19H1_Insider_Samples/CppWin32App_With_Island/Microsoft.UI.Xaml.Markup/XamlApplication.cpp) файл кода в [ C++ пример Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island).

      * **WPF и Windows Forms**: См. в разделе [XamlApplication.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Win32.UI.XamlHost/XamlApplication.cs) и [UWPTypeFactory.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Win32.UI.XamlHost/UWPTypeFactory.cs) файлы в наборе средств сообщества Windows кода. Эти файлы являются частью общей реализации **WindowsXamlHost** классы для WPF и Windows Forms, которые помогают продемонстрировать способы использования XAML UWP, размещение API в этих типов приложений.

3. Интегрировать в решение приложения узла исходный код для пользовательского элемента управления XAML UWP, построения пользовательского элемента управления и использовать его в приложении. Инструкции для приложений WPF и Windows Forms, см. в разделе [эти инструкции](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost#add-a-custom-uwp-control). Пример для C++ приложение Win32 см. в разделе [Microsoft.UI.Xaml.Markup](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island/Microsoft.UI.Xaml.Markup) и [MyApp](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island/MyApp) в проекты [ C++ пример Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="error-using-uwp-xaml-hosting-api-in-a-uwp-app"></a>Ошибка использования XAML UWP, интерфейс API размещения в приложении UWP

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: «Не удается активировать DesktopWindowXamlSource. Этот тип не может использоваться в приложении универсальной платформы Windows.» или «не удается активировать WindowsXamlManager. Этот тип не может использоваться в приложении универсальной платформы Windows.» | Эта ошибка означает, вы пытаетесь использовать XAML UWP, интерфейс API размещения (в частности, вы пытаетесь создать экземпляр [ **DesktopWindowXamlSource** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) или [  **WindowsXamlManager** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) типы) в приложении UWP. XAML UWP, интерфейс API размещения предназначен только для использования в классических приложениях не универсальной платформы Windows, таких как приложения WPF, Windows Forms и C++ Win32. |

### <a name="error-attaching-to-a-window-on-a-different-thread"></a>Ошибка при подключении к окну в другом потоке

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: «Метод AttachToWindow ошибка, так как был создан заданный HWND в другом потоке». | Эта ошибка означает, что приложение будет вызывать **IDesktopWindowXamlSourceNative::AttachToWindow** метод и передать ему HWND окна, который был создан в другом потоке. Этот метод необходимо передать HWND окна, который был создан в том же потоке, что и код, из которого вы вызываете метод. |

### <a name="error-attaching-to-a-window-on-a-different-top-level-window"></a>Ошибка при подключении к окну на другое окно верхнего уровня

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: «Метод AttachToWindow ошибка, так как заданный HWND потомком другое окно верхнего уровня HWND, который ранее был передан AttachToWindow в одном потоке». | Эта ошибка означает, что приложение будет вызывать **IDesktopWindowXamlSourceNative::AttachToWindow** метод и передать ему HWND окна, происходящего от разных окно верхнего уровня, чем период, указанный в Предыдущий вызов этого метода в том же потоке.</p></p>После в приложении вызывается **IDesktopWindowXamlSourceNative::AttachToWindow** в определенном потоке, все остальные [ **DesktopWindowXamlSource** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) объекты на том же поток можно подключать только для windows, которые являются потомками же окно верхнего уровня, который был передан в первом вызове **IDesktopWindowXamlSourceNative::AttachToWindow**. Когда все **DesktopWindowXamlSource** объекты будут закрыты для конкретного потока, следующего **DesktopWindowXamlSource** может затем повторно добавить в любом окне.</p></p>Чтобы устранить эту проблему, либо закрыть все **DesktopWindowXamlSource** объектов, которые привязаны к другим окнам верхнего уровня в данном потоке, или создать новый поток для данного **DesktopWindowXamlSource**. |

## <a name="related-topics"></a>См. также

* [Элементы управления универсальной платформы Windows в настольных приложениях](xaml-islands.md)
* [C++Пример Win32 XAML острова](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island)
