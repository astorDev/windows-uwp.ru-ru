---
description: В этой статье описывается размещение пользовательского интерфейса XAML UWP в приложении для настольных систем.
title: Использование API хостинга UWP XAML в классическом приложении
ms.date: 07/26/2019
ms.topic: article
keywords: Windows 10, UWP, Windows Forms, WPF, Win32, о-ва XAML
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 14607dc3e3b32f39a840d623c7a887bb8b3687c5
ms.sourcegitcommit: f6af7aeb8506379a184207035c8e43288cb31453
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601534"
---
# <a name="using-the-uwp-xaml-hosting-api-in-a-desktop-application"></a>Использование API хостинга UWP XAML в классическом приложении

Начиная с Windows 10, версия 1903, не относящаяся к UWP настольные приложения (включая WPF C++ , Windows Forms и приложения Win32) может использовать *API размещения UWP XAML* для размещения элементов управления UWP в любом элементе пользовательского интерфейса, связанном с дескриптором окна (HWND). Этот API позволяет приложениям, не относящимся к UWP, использовать новейшие функции пользовательского интерфейса Windows 10, которые доступны только через элементы управления UWP. Например, приложения, не являющиеся классом UWP, могут использовать этот API для размещения элементов управления UWP, использующих [систему разработки Fluent](/windows/uwp/design/fluent-design-system/index) и поддерживающих [Рукописный ввод Windows](/windows/uwp/design/input/pen-and-stylus-interactions).

API хостинга для UWP XAML предоставляет основу для более широкого набора элементов управления, предоставляемых разработчиками, чтобы разработчики получили доступ к классским приложениям, не относящимся к UWP. Эта функция называется *островами XAML*. Общие сведения об этой функции см. [в разделе элементы управления UWP в классических приложениях](xaml-islands.md).

> [!NOTE]
> Если у вас есть отзывы о островах XAML, создайте новую ошибку в [репозитории Microsoft. Toolkit. Win32](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/issues) и оставьте свои комментарии. Если вы предпочитаете отправить отзыв в частном порядке, его можно отправить в XamlIslandsFeedback@microsoft.com. Ваши ценные сведения и сценарии критически важны для нас.

## <a name="should-you-use-the-uwp-xaml-hosting-api"></a>Следует ли использовать API размещения UWP XAML?

API хостинга UWP XAML предоставляет низкую инфраструктуру для размещения элементов управления UWP в настольных приложениях. Некоторые типы настольных приложений имеют возможность использовать альтернативные, более удобные API для выполнения этой задачи.  

* При наличии классического C++ приложения Win32 и необходимости размещения элементов управления UWP в приложении необходимо использовать API размещения UWP XAML. Для таких типов приложений нет альтернативы.

* Для приложений WPF и Windows Forms настоятельно рекомендуется использовать [Упакованные элементы управления](xaml-islands.md#wrapped-controls) и [элементы управления ведущего приложения](xaml-islands.md#host-controls) в наборе средств сообщества Windows, а не напрямую использовать API размещения XAML UWP. Эти элементы управления используют API размещения универсального кода класса UWP для внутреннего использования и реализуют все поведение, которое в противном случае необходимо для самостоятельной работы, если вы используете API размещения в формате UWP XAML напрямую, включая навигацию с помощью клавиатуры и изменение макета. Однако вы можете использовать API размещения UWP XAML непосредственно в этих типах приложений, если вы решили.

В этой статье описывается, как использовать API хостинга UWP XAML непосредственно в приложении вместо элементов управления, предоставляемых набором средств Windows Community Toolkit.

## <a name="prerequisites"></a>предварительные требования

В API размещения XAML для UWP имеются следующие предварительные требования:

* Windows 10, версия 1903 (или более поздняя) и соответствующая сборка Windows SDK.
* Настройте проект для использования среда выполнения Windows API и включите острова XAML, выполнив приведенные ниже [инструкции](xaml-islands.md#requirements).

## <a name="architecture-of-xaml-islands"></a>Архитектура островов XAML

API размещения платформы UWP XAML включает следующие основные типы среда выполнения Windows и COM-интерфейсы:

* [**Виндовсксамлманажер**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager). Этот класс представляет платформу XAML UWP. Этот класс предоставляет один статический метод [**инитиализефоркуррентсреад**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread) , который инициализирует ПЛАТФОРМУ XAML UWP в текущем потоке в классическом приложении.

* [**Десктопвиндовксамлсаурце**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource). Этот класс представляет экземпляр содержимого класса UWP XAML, который размещается в приложении для настольных систем. Самым важным элементом этого класса является свойство [**Content**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) . Это свойство назначается элементу управления [**Windows. UI. XAML. UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) , который требуется разместить. Этот класс также содержит другие члены для маршрутизации навигации фокуса клавиатуры в и из островов XAML.

* COM-интерфейсы **идесктопвиндовксамлсаурценативе** и **IDesktopWindowXamlSourceNative2** . **Идесктопвиндовксамлсаурценативе** предоставляет метод **аттачтовиндов** , который используется для присоединения островического кода XAML в приложении к РОДИТЕЛЬСКОМУ элементу пользовательского интерфейса. **IDesktopWindowXamlSourceNative2** предоставляет метод **претранслатемессаже** , который позволяет инфраструктуре XAML UWP правильно обрабатывать определенные сообщения Windows.
    > [!NOTE]
    > Эти интерфейсы объявляются в файле заголовка **Windows. UI. XAML. Hosting. десктопвиндовксамлсаурце. h** в Windows SDK. По умолчанию этот файл находится в% ProgramFiles (x86)% \ Windows Kits\10\Include\\< номер\>сборки \ум. В проекте C++ Win32 можно ссылаться на этот файл заголовка напрямую. В проекте WPF или Windows Forms необходимо объявить интерфейсы в коде приложения с помощью атрибута [**ComImport**](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.comimportattribute) . Убедитесь, что объявления интерфейса точно соответствуют объявлениям в **Windows. UI. XAML. Hosting. десктопвиндовксамлсаурце. h**.

На следующей схеме показана иерархия объектов в острове XAML, размещенном в классическом приложении.

![Архитектура Десктопвиндовксамлсаурце](images/xaml-islands/xaml-hosting-api-rev2.png)

* На базовом уровне находится элемент пользовательского интерфейса в приложении, где требуется разместить остров XAML. Этот элемент пользовательского интерфейса должен иметь дескриптор окна (HWND). Примеры элементов пользовательского интерфейса, в которых можно разместить остров XAML, включают в себя [**System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) для приложений WPF, [**System.Windows.Forms.Control**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) для Windows Forms приложений и [окно](https://docs.microsoft.com/windows/desktop/winmsg/about-windows) C++ для Win32 приложений.

* На следующем уровне находится объект **десктопвиндовксамлсаурце** . Этот объект предоставляет инфраструктуру для размещения острова XAML. Ваш код отвечает за создание этого объекта и его присоединение к родительскому элементу пользовательского интерфейса.

* При создании **десктопвиндовксамлсаурце**этот объект автоматически создает собственное дочернее окно для размещения элемента управления UWP. Это собственное дочернее окно в основном является абстрактным от кода, но при необходимости вы можете получить доступ к его дескриптору (HWND).

* Наконец, на верхнем уровне находится элемент управления UWP, который требуется разместить в приложении для настольных систем. Это может быть любой объект UWP, производный от [**Windows. UI. XAML. UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), включая любой элемент управления UWP, предоставляемый Windows SDK, а также настраиваемые пользовательские элементы управления.

## <a name="related-samples"></a>Связанные примеры

Способ использования API размещения класса UWP XAML в коде зависит от типа приложения, структуры приложения и других факторов. Чтобы продемонстрировать, как использовать этот API в контексте полного приложения, в этой статье рассматривается код из следующих примеров.

### <a name="c-win32"></a>C++Платформа

[ C++ Пример Win32](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App). В этом примере демонстрируется полная реализация размещения пользовательского элемента управления UWP в неупакованном приложении C++ Win32 (то есть приложение, которое не встроено в пакет MSIX).

### <a name="wpf-and-windows-forms"></a>WPF и Windows Forms

Элемент управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в наборе Windows Community Toolkit выступает в качестве образца для использования API размещения UWP в приложениях WPF и Windows Forms. Исходный код доступен в следующих расположениях:

  * Для версии элемента управления WPF [перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Wpf.UI.XamlHost). Версия WPF является производной от [**System. Windows. Interop. HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost).

  * Для Windows Forms версии элемента управления [перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Forms.UI.XamlHost). Версия Windows Forms является производной от [**System. Windows. Forms. Control**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control).

## <a name="host-uwp-xaml-controls"></a>Размещение элементов управления XAML UWP

Ниже приведены основные шаги по использованию API размещения UWP XAML для размещения элемента управления UWP в приложении.

1. Инициализируйте платформу XAML UWP для текущего потока, прежде чем приложение создаст любой объект [**Windows. UI. XAML. UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) , который будет размещаться в [**десктопвиндовксамлсаурце**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource).

    * Если приложение создает объект **десктопвиндовксамлсаурце** перед созданием любого из объектов **Windows. UI. XAML. UIElement** , эта платформа будет инициализирована при создании экземпляра объекта **десктопвиндовксамлсаурце** . В этом случае вам не нужно добавлять собственный код для инициализации платформы.

    * Однако если приложение создает объекты **Windows. UI. XAML. UIElement** перед созданием объекта **десктопвиндовксамлсаурце** , который будет размещать их, приложение должно вызвать статический [**метод Метод Виндовсксамлманажер. Инитиализефоркуррентсреад**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread) для явной инициализации платформы XAML UWP перед созданием объектов **Windows. UI. XAML. UIElement** . Приложение обычно вызывает этот метод, когда создается родительский элемент пользовательского интерфейса, на котором размещается **десктопвиндовксамлсаурце** .

    ```cppwinrt
    Windows::UI::Xaml::Hosting::WindowsXamlManager windowsXamlManager =
        Windows::UI::Xaml::Hosting::WindowsXamlManager::InitializeForCurrentThread();
    ```

    ```csharp
    global::Windows.UI.Xaml.Hosting.WindowsXamlManager windowsXamlManager =
        global::Windows.UI.Xaml.Hosting.WindowsXamlManager.InitializeForCurrentThread();
    ```

    > [!NOTE]
    > Этот метод возвращает объект [**виндовсксамлманажер**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) , содержащий ссылку на ПЛАТФОРМУ XAML UWP. В определенном потоке можно создать столько объектов **виндовсксамлманажер** , сколько нужно. Однако, поскольку каждый объект содержит ссылку на платформу XAML UWP, следует ликвидировать объекты, чтобы обеспечить окончательное освобождение ресурсов XAML.

2. Создайте объект [**десктопвиндовксамлсаурце**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) и присоедините его к родительскому ЭЛЕМЕНТУ пользовательского интерфейса в приложении, связанному с маркером окна.

    Для этого необходимо выполнить следующие действия.

    1. Создайте объект **десктопвиндовксамлсаурце** и приведите его к com-интерфейсу **идесктопвиндовксамлсаурценативе** или **IDesktopWindowXamlSourceNative2** .

    2. Вызовите метод **аттачтовиндов** интерфейса **идесктопвиндовксамлсаурценативе** или **IDesktopWindowXamlSourceNative2** и передайте в приложение маркер окна родительского элемента пользовательского интерфейса.

    3. Задайте начальный размер внутреннего дочернего окна, содержащегося в **десктопвиндовксамлсаурце**. По умолчанию для этого внутреннего дочернего окна заданы ширина и высота 0. Если размер окна не задан, все элементы управления UWP, добавляемые в **десктопвиндовксамлсаурце** , не будут видны. Чтобы получить доступ к внутреннему дочернему окну в **десктопвиндовксамлсаурце**, используйте свойство **WindowHandle** интерфейса **идесктопвиндовксамлсаурценативе** или **IDesktopWindowXamlSourceNative2** . В следующих примерах для задания размера окна используется функция [SetWindowPos](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) .

    Ниже приведено несколько примеров кода, демонстрирующих этот процесс.

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

3. Задайте свойство **Windows. UI. XAML. UIElement** , которое должно быть размещено в свойстве [**Content**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) объекта **десктопвиндовксамлсаурце** . В следующем примере задается свойство [**Windows. UI. XAML. Controls. Grid**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid) , именуемое ```myGrid``` свойством **Content** .

   ```cppwinrt
   desktopWindowXamlSource.Content(myGrid);
   ```

   ```csharp
   desktopWindowXamlSource.Content = myGrid;
   ```

Полные примеры, демонстрирующие эти задачи в контексте рабочего примера приложения, см. в следующих файлах кода:

  * **C++Платформа** См. файл [ксамлбридже. cpp](https://github.com/marb2000/XamlIslands/blob/master/1903_Samples/CppWinRT_Win32_App/SampleCppApp/XamlBridge.cpp) в [ C++ примере Win32](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App).

  * **WPF** См. файлы [WindowsXamlHostBase.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHost.cs) в наборе средств сообщества Windows.  

  * **Windows Forms:** См. файлы [WindowsXamlHostBase.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHost.cs) в наборе средств сообщества Windows.

## <a name="handle-keyboard-input-and-focus-navigation"></a>Управлять вводом с клавиатуры и навигацией о фокусах

Существует несколько вещей, которые необходимо выполнить приложению для правильной работы с вводом с клавиатуры и навигацией о фокусах при размещении островов XAML.

### <a name="keyboard-input"></a>Ввод с клавиатуры

Чтобы правильно обрабатывать ввод с клавиатуры для каждого острова XAML, приложение должно передать все сообщения Windows в платформу XAML UWP, чтобы определенные сообщения могли обрабатываться правильно. Для этого в каком-то месте приложения, которое может получить доступ к циклу обработки сообщений, приведите объект **десктопвиндовксамлсаурце** для каждого острова XAML к интерфейсу **IDesktopWindowXamlSourceNative2** com. Затем вызовите метод **претранслатемессаже** этого интерфейса и передайте текущее сообщение.

  * Приложение C++ Win32 может вызывать **претранслатемессаже** непосредственно в основном цикле обработки сообщений. Пример см. в файле [ксамлбридже. cpp](https://github.com/marb2000/XamlIslands/blob/master/1903_Samples/CppWinRT_Win32_App/SampleCppApp/XamlBridge.cpp#L6) в [ C++ примере Win32](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App).

  * Приложение WPF может вызвать **претранслатемессаже** из обработчика событий для события [**компонентдиспатчер. среадфилтермессаже**](https://docs.microsoft.com/dotnet/api/system.windows.interop.componentdispatcher.threadfiltermessage?view=netframework-4.7.2) . Пример см. в файле [WindowsXamlHostBase.Focus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs#L177) в наборе средств сообщества Windows.

  * Windows Forms приложение может вызывать **претранслатемессаже** из переопределения метода [**Control. PreprocessMessage**](https://docs.microsoft.com/en-us/dotnet/api/system.windows.forms.control.preprocessmessage?view=netframework-4.7.2) . Пример см. в файле [WindowsXamlHostBase.KeyboardFocus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs#L100) в наборе средств сообщества Windows.

### <a name="keyboard-focus-navigation"></a>Навигация фокуса клавиатуры

Когда пользователь переходит по элементам пользовательского интерфейса в приложении, используя клавиатуру (например, нажав клавишу **Tab** или клавишу со стрелкой), необходимо программно переместить фокус в объект **десктопвиндовксамлсаурце** и из него. Когда переход от клавиатуры пользователя достигает **десктопвиндовксамлсаурце**, переместите фокус на первый объект [**Windows. UI. XAML. UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) в порядке навигации для пользовательского интерфейса, продолжайте перемещать фокус на следующие **элементы. Объекты Windows. UI. XAML. UIElement** , когда пользователь циклически переключается по элементам, а затем перемещает фокус обратно из **десктопвиндовксамлсаурце** и в родительский элемент пользовательского интерфейса.  

API хостинга UWP XAML предоставляет несколько типов и членов, помогающих выполнять эти задачи.

* При переходе от клавиатуры к **десктопвиндовксамлсаурце**возникает событие [**GotFocus**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.gotfocus) . Обработайте это событие и программным образом перенесите фокус на первый размещенный элемент **Windows. UI. XAML. UIElement** с помощью метода [**навигатефокус**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.navigatefocus) .

* Когда пользователь находится на последнем элементе с фокусом в **десктопвиндовксамлсаурце** и нажимает клавишу **Tab** или клавишу со стрелкой, возникает событие [**такефокусрекуестед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.takefocusrequested) . Обработайте это событие и программно перемещайте фокус на следующий сфокусированный элемент в ведущем приложении. Например, в приложении WPF, в котором **десктопвиндовксамлсаурце** размещается в [**системе. Windows. Interop. HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost), можно использовать метод [**MoveFocus**](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.movefocus) для перемещения фокуса на следующий сфокусированный элемент в ведущем приложении.

Примеры, демонстрирующие, как это сделать в контексте рабочего примера приложения, см. в следующих файлах кода:

  * **WPF** См. файл [WindowsXamlHostBase.Focus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs) в наборе средств сообщества Windows.  

  * **Windows Forms:** См. файл [WindowsXamlHostBase.KeyboardFocus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs) в наборе средств сообщества Windows.

  * **C++/Win32**: См. файл [ксамлбридже. cpp](https://github.com/marb2000/XamlIslands/blob/master/1903_Samples/CppWinRT_Win32_App/SampleCppApp/XamlBridge.cpp) в [ C++ примере Win32](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App).

## <a name="handle-layout-changes"></a>Обработку изменений макета

Когда пользователь изменяет размер родительского элемента пользовательского интерфейса, необходимо выполнить все необходимые изменения макета, чтобы элементы управления UWP отображались должным образом. Ниже приведены некоторые важные сценарии, которые следует учитывать.

* В приложении C++ Win32, когда приложение ОБРАБАТЫВАЕТ сообщение WM_SIZE, оно может переместить размещенный остров XAML с помощью функции [SetWindowPos](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) . Пример см. в файле кода [SampleApp. cpp](https://github.com/marb2000/XamlIslands/blob/master/19H1_Insider_Samples/CppWin32App_With_Island/SampleCppApp/SampleApp.cpp#L191) в [ C++ примере Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island).

* Когда родительский элемент пользовательского интерфейса должен получить размер прямоугольной области, необходимой для размещения элемента **Windows. UI. XAML. UIElement** , который вы размещаете в **десктопвиндовксамлсаурце**, вызовите метод [**Measure**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) элемента **Windows. UI. XAML. UIElement.** . Пример:

    * В приложении WPF это можно сделать из метода [**MeasureOverride**](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.measureoverride) [**HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) , на котором размещена **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHostBase.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

    * В Windows Forms приложении это можно сделать из метода [**Жетпреферредсизе**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.getpreferredsize) [**элемента управления**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , размещающего **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHostBase.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

* При изменении размера родительского элемента пользовательского интерфейса вызовите метод [**Arrange**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) упорядочения корневого интерфейса **Windows.UI.Xaml.UIElement**, который вы размещаете в **DesktopWindowXamlSource**. Пример:

    * В приложении WPF это можно сделать из метода [**ArrangeOverride**](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.arrangeoverride) объекта [**HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) , в котором размещается **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHost.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

    * В Windows Forms приложении это можно сделать из обработчика для события [**SizeChanged**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.sizechanged) [**элемента управления**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , размещающего **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHost.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

## <a name="handle-dpi-changes"></a>Изменение количества точек на дюйм

Платформа XAML UWP обрабатывает изменения DPI для размещаемых элементов управления UWP автоматически (например, когда пользователь перетаскивает окно между мониторами с разными DPI на экране). Для оптимальной работы рекомендуется, чтобы Windows Forms, WPF или C++ приложение Win32 были настроены для отслеживания dpi на уровне каждого монитора.

Чтобы настроить приложение для отслеживания dpi на уровне каждого монитора, добавьте в проект [параллельный манифест сборки](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) и задайте для ```<dpiAwareness>``` ```PerMonitorV2```элемента значение. Дополнительные сведения об этом значении см. в описании для [**DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2**](https://docs.microsoft.com/windows/desktop/hidpi/dpi-awareness-context).

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

## <a name="host-custom-uwp-xaml-controls"></a>Размещение настраиваемых элементов управления XAML UWP

Выполните следующие общие действия для размещения пользовательского элемента управления универсального кода пользователя UWP (определенного пользователем элемента управления или элемента управления, предоставляемого сторонним лицом) в области XAML. Необходимо иметь исходный код для пользовательского элемента управления, чтобы его можно было скомпилировать в приложении.

1. В решении ведущего приложения Интегрируйте исходный код для пользовательского элемента управления XAML UWP и создайте пользовательский элемент управления.

2. Добавьте проект приложения UWP в решение ведущего приложения и добавьте в этот проект пакет NuGet [Microsoft. Toolkit. Win32. UI. ксамлаппликатион](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) .

3. В проекте приложения UWP измените `App` класс таким образом, чтобы он из класса **Microsoft. Toolkit. Win32. UI. ксамлаппликатион** , предоставляемого пакетом NuGet [Microsoft. Toolkit. Win32. UI. ксамлаппликатион](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) . Этот тип выступает в качестве корневого поставщика метаданных для загрузки метаданных пользовательских типов универсального кода пользователя UWP в сборки в текущем каталоге приложения.

4. В конструкторе `App` класса вызовите метод **Initialize** базового класса **Microsoft. Toolkit. Win32. UI. ксамлаппликатион** .

5. В проекте ведущего приложения выполните процедуру, описанную в [предыдущем разделе](#host-uwp-xaml-controls) , чтобы разместить пользовательский элемент управления в острове XAML.

Пример для приложения C++ Win32 см. в следующих проектах [ C++ примера Win32](https://github.com/marb2000/XamlIslands/blob/master/1903_Samples/CppWinRT_Win32_App):

* [Самплеусерконтрол](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App/SampleUserControl): В этом проекте реализуется пользовательский элемент управления типа `MyUserControl` UWP XAML с именем, который содержит текстовое поле, несколько кнопок и поле со списком.
* [MyApp](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App/MyApp): Это проект приложения UWP с изменениями, описанными в предыдущих шагах.
* [Самплекппапп](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App/SampleCppApp): Это проект приложения C++ Win32, в котором размещается пользовательский элемент управления XAML UWP в области XAML.

Подробные инструкции и примеры для приложения WPF или Windows Forms см. в [этих инструкциях](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost#add-a-custom-uwp-control).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="error-using-uwp-xaml-hosting-api-in-a-uwp-app"></a>Ошибка при использовании API размещения UWP XAML в приложении UWP

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: "Не удается активировать Десктопвиндовксамлсаурце. Этот тип нельзя использовать в приложении UWP. " или "не удается активировать Виндовсксамлманажер. Этот тип нельзя использовать в приложении UWP. " | Эта ошибка означает, что вы пытаетесь использовать API размещения UWP XAML (в частности, вы пытаетесь создать экземпляр типов [**десктопвиндовксамлсаурце**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) или [**виндовсксамлманажер**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) ) в приложении UWP. API размещения на языке UWP XAML предназначен только для использования в классических приложениях UWP, таких как WPF, Windows Forms и C++ приложения Win32. |

### <a name="error-attaching-to-a-window-on-a-different-thread"></a>Ошибка при присоединении к окну в другом потоке

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: "Сбой метода Аттачтовиндов, так как указанный HWND был создан в другом потоке". | Эта ошибка означает, что приложение вызвало метод **идесктопвиндовксамлсаурценативе:: аттачтовиндов** и передало ему дескриптор HWND окна, созданного в другом потоке. Этот метод необходимо передать в HWND окна, созданного в том же потоке, что и код, из которого вызывается метод. |

### <a name="error-attaching-to-a-window-on-a-different-top-level-window"></a>Ошибка при присоединении к окну в другом окне верхнего уровня

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: "Сбой метода Аттачтовиндов, так как указанный HWND отличается от другого окна верхнего уровня, чем HWND, который ранее был передан в Аттачтовиндов в том же потоке". | Эта ошибка означает, что приложение вызвало метод **идесктопвиндовксамлсаурценативе:: аттачтовиндов** и передало ему HWND окна, которое отличается от окна верхнего уровня, чем окно, указанное в предыдущем вызове этого метода. в том же потоке.</p></p>После того как приложение вызовет **идесктопвиндовксамлсаурценативе:: аттачтовиндов** в определенном потоке, все остальные объекты [**десктопвиндовксамлсаурце**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) в одном потоке могут быть присоединены только к Windows, которые являются потомками того же верхнего уровня. окно, которое было передано при первом вызове метода **идесктопвиндовксамлсаурценативе:: аттачтовиндов**. Когда все объекты **десктопвиндовксамлсаурце** закрываются для конкретного потока, следующий **десктопвиндовксамлсаурце** затем может быть снова подключен к любому окну.</p></p>Чтобы устранить эту проблему, закройте все объекты **десктопвиндовксамлсаурце** , привязанные к другим окнам верхнего уровня в этом потоке, или создайте новый поток для этого **десктопвиндовксамлсаурце**. |

## <a name="related-topics"></a>См. также

* [Элементы управления UWP в классических приложениях](xaml-islands.md)
* [C++Пример XAML-островов Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island)
