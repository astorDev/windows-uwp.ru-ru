---
description: В этой статье описывается, как для размещения пользовательского интерфейса UWP XAML в классическом приложении.
title: С помощью XAML UWP, API размещения в приложении рабочего стола
ms.date: 01/11/2019
ms.topic: article
keywords: Windows 10, uwp, windows forms, wpf, win32
ms.localizationpriority: medium
ms.openlocfilehash: efd7dc687b9aba2e3c07b0afefa2e4fa49b882b1
ms.sourcegitcommit: 2d2483819957619b6de21b678caf887f3b1342af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2019
ms.locfileid: "9042316"
---
# <a name="using-the-uwp-xaml-hosting-api-in-a-desktop-application"></a>С помощью XAML UWP, API размещения в приложении рабочего стола

> [!NOTE]
> Острова размещения API и XAML UWP XAML в настоящее время доступны как ознакомительная версия для разработчиков. Несмотря на то, что мы рекомендуем вам оценить их в коде прототип теперь, мы не рекомендуем использовать их в рабочем коде в данный момент. Эти функции продолжит улучшаться и стабилизация в будущих выпусках Windows. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.
>
> Если вы хотите оставить отзыв о XAML, о-ва, создание нового вопроса в [репозитории WindowsCommunityToolkit](https://github.com/windows-toolkit/WindowsCommunityToolkit/issues) и имеется оставлять свои комментарии. Если вы предпочитаете для отправки отзывов в частном порядке, можно отправить его в XamlIslandsFeedback@microsoft.com. Сведения и сценарии являются критически важными для нас.

Начиная с Windows 10 Insider Preview SDK, сборка 17709, не UWP классических приложений (включая приложения WPF, Windows Forms и C++ Win32) можно использовать *API размещения XAML UWP* , стандартные элементы управления UWP в любой элемент пользовательского интерфейса, связанного с (дескриптор окна HWND). Этот API позволяет не UWP Классические приложения для использования новейшие возможности пользовательского интерфейса Windows 10, которые доступны только через элементы управления UWP. Например не UWP Классические приложения могут использовать этот API для размещения элементов управления UWP, с помощью [Системы проектирования Fluent](../design/fluent-design-system/index.md) и поддержка [Рукописного ввода Windows](../design/input/pen-and-stylus-interactions.md).

XAML UWP, API размещения — это основа для широкий набор элементов управления, которые мы предоставляем позволяет разработчикам делают Fluent пользовательского интерфейса не UWP классических приложений. Этот сценарий иногда называют *острова XAML*. Дополнительные сведения об этом сценарии разработчиков см. в разделе [элементы управления UWP в классических приложениях](xaml-host-controls.md).

## <a name="is-the-uwp-xaml-hosting-api-right-for-your-desktop-application"></a>— Это UWP XAML API размещения вправо для классического приложения?

XAML UWP, API размещения инфраструктура для размещения элементов управления UWP в классических приложениях низкого уровня. Некоторые типы Классические приложения имеют возможность использовать альтернативные, более удобными API-интерфейсы для достижения этой цели.  

* Если у вас есть классическое приложение C++ Win32 и вы хотите стандартные элементы управления UWP в приложении, необходимо использовать XAML UWP, API размещения. Существуют нет альтернативы для этих типов приложений.

* Для приложений WPF и Windows Forms мы рекомендуем использовать [оболочку](xaml-host-controls.md#wrapped-controls) и [элементами управления узла](xaml-host-controls.md#host-controls) в наборе средств сообщества Windows вместо использования UWP XAML API размещения. Эти элементы управления использовать XAML UWP, API размещения внутри организации и проще возможности разработки. Тем не менее можно использовать XAML UWP, API непосредственно в приложения такого типа размещения при выборе.

## <a name="related-samples"></a>Связанные примеры

Как в случае размещения API в коде XAML UWP зависит от типа приложения, макет приложения и других факторов. Чтобы проиллюстрировать варианты использования этого API в контексте полного приложения, в этой статье ссылается на код на приведенные ниже примеры.

### <a name="c-win32"></a>C++ Win32

Существует ряд примеров на GitHub, демонстрирующие способы использования XAML UWP, API размещения в приложении C++ Win32.

  * [XamlHostingSample](https://github.com/Microsoft/Windows-appsample-Xaml-Hosting). В этом примере показано, как добавлять элементы управления UWP [InkCanvas](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas), [InkToolbar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar)и [MediaPlayerElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement) в приложении C++ Win32.
  * [XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32). В этом примере показано, как добавить несколько основных элементов управления UWP в приложении C++ Win32 и обработка изменений DPI.

### <a name="wpf-and-windows-forms"></a>WPF и Windows Forms

Элемент управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в наборе средств сообщества Windows выступает в качестве Образцовый пример использования UWP, API размещения в приложениях WPF и Windows Forms. Исходный код доступен в следующих местах:

  * Для элемента управления, [перейдите по ссылке](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Wpf.UI.XamlHost)на версию WPF. Версия WPF является производным от [**System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost).
  * Для версии Windows Forms элемента управления, [перейдите по ссылке](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Forms.UI.XamlHost). Версии Windows Forms является производным от [**System.Windows.Forms.Control**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control).

## <a name="prerequisites"></a>Необходимые условия

XAML UWP, API размещения этих условий.

* Сборка Windows 10 Insider Preview 17709 (или более новой) и соответствующие предварительные сборки пакета Windows SDK. Так как эта функция проверки включают специальные возможности, для его оптимальной работы рекомендуется использовать последние сборки.

* Чтобы использовать XAML UWP, API размещения в классическом приложении, необходимо настроить проект таким образом, вы можете вызвать API-интерфейсов UWP.

    * **C++ Win32:** Мы рекомендуем настроить проект, чтобы использовать [C + +/ WinRT](../cpp-and-winrt-apis/index.md). Инструкции см. в разделе [Изменение проекта приложения рабочего стола Windows для добавления C + +/ WinRT поддержки](/windows/uwp/cpp-and-winrt-apis/get-started#modify-a-windows-desktop-application-project-to-add-cwinrt-support).

    * **Windows Forms и WPF:** Выполните [следующие действия](../porting/desktop-to-uwp-enhance.md).

## <a name="architecture-of-xaml-islands"></a>Архитектура XAML острова

API размещения XAML UWP включает в себя [**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource), [**WindowsXamlManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager)и другие связанные типы в пространстве имен [**Windows.UI.Xaml.Hosting**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting) . Этот API используется Классические приложения для отображения элементов управления UWP и направление перемещения фокуса клавиатуры в и из него элементов. Классические приложения могут также размер и положение элементов управления UWP в случае необходимости.

При создании Рождества XAML, с помощью API размещения в классическом приложении XAML, вы получите следующей иерархии объектов:

* На базовом уровне — это элемент пользовательского интерфейса в приложении, где вы хотите разместить Рождества XAML. Этот элемент пользовательского интерфейса должны иметь дескриптор окна (HWND). Элементы пользовательского интерфейса, в которых можно разместить XAML Рождества примеры [**System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) для приложений WPF, [**System.Windows.Forms.Control**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) для приложений Windows Forms и [окна](https://docs.microsoft.com/windows/desktop/winmsg/about-windows) для приложений C++ Win32.

* На следующем уровне — это объект **DesktopWindowXamlSource** . Этот объект предоставляет инфраструктуру для размещения Рождества XAML. Ваш код отвечает за создание этот объект и подключение его к родительскому элементу пользовательского интерфейса.

* При создании **DesktopWindowXamlSource**, этот объект автоматически создает собственные дочернего окна для размещения вашего элемента управления UWP. Это собственные дочернего окна в основном абстрагировать от кода, но можно получить доступ к его дескриптор (HWND), при необходимости.

* Наконец на верхнем уровне — это элемент управления UWP, который вы хотите разместить в классическом приложении. Это может быть любой объект UWP, который является производным от [**Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), включая любой элемент управления UWP, предоставляемые Windows SDK, а также пользовательских элементов управления.

На следующей схеме показана иерархию объектов в Рождества XAML.

![Архитектура DesktopWindowXamlSource](images/xaml-hosting-api-rev2.png)

## <a name="how-to-host-uwp-xaml-controls"></a>Как разместить элементы управления UWP XAML

Ниже приведены основные шаги для использования XAML UWP, API размещения для размещения элемента управления UWP в приложении.

1. Инициализируйте платформа UWP XAML для текущего потока, перед тем как приложение создаст любых объектов [**Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) , он будет размещаться в [**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource).

    * Если ваше приложение создает объект **DesktopWindowXamlSource** перед созданием любого объекта **Windows.UI.Xaml.UIElement** , эту платформу, чтобы инициализировать для вас при создании экземпляра объекта **DesktopWindowXamlSource** . В этом случае не нужно добавлять никакой код своей собственной для инициализации платформы.

    * Тем не менее если ваше приложение создает объекты **Windows.UI.Xaml.UIElement** , прежде чем он создает объект **DesktopWindowXamlSource** , где будут размещены их, ваше приложение должно вызвать статический [** WindowsXamlManager.InitializeForCurrentThread**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread) метод, чтобы явным образом инициализировать платформа UWP XAML, прежде чем **Windows.UI.Xaml.UIElement** объекты создаются. Приложение обычно должно вызывать этот метод при создании экземпляра родительского элемента пользовательского интерфейса, на котором размещается **DesktopWindowXamlSource** .

    ```cppwinrt
    Windows::UI::Xaml::Hosting::WindowsXamlManager windowsXamlManager =
        Windows::UI::Xaml::Hosting::WindowsXamlManager::InitializeForCurrentThread();
    ```

    ```csharp
    global::Windows.UI.Xaml.Hosting.WindowsXamlManager windowsXamlManager =
        global::Windows.UI.Xaml.Hosting.WindowsXamlManager.InitializeForCurrentThread();
    ```

    > [!NOTE]
    > Этот метод возвращает объект [**WindowsXamlManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) , который содержит ссылку на платформе UWP XAML. Вы можете создать любое количество объектов **WindowsXamlManager** , необходимое для данного потока. Тем не менее так как каждый объект содержит ссылку на платформе UWP XAML, следует освободить память объектов, чтобы обеспечить в конечном итоге освобождение ресурсов XAML.

2. Создание объекта [**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) и присоединения его к родительскому элементу пользовательского интерфейса в приложении, связанного с дескриптор окна.

    Для этого необходимо выполнить следующие действия:

    1. Создание объекта **DesktopWindowXamlSource** и приведите его к интерфейсу **IDesktopWindowXamlSourceNative** COM. Этот интерфейс объявляется в ```windows.ui.xaml.hosting.desktopwindowxamlsource.h``` файл заголовка в Windows SDK. В проекте C++ Win32 можно ссылаться непосредственно файл заголовка. В проекте Windows Forms или WPF необходимо объявить этот интерфейс в коде приложения, используя атрибут [**ComImport**](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.comimportattribute) . Убедитесь, что ваше объявление интерфейса точно соответствует объявление интерфейса в ```windows.ui.xaml.hosting.desktopwindowxamlsource.h```.

    2. Вызов метода **AttachToWindow** интерфейса **IDesktopWindowXamlSourceNative** и передайте дескриптор окна родительского элемента пользовательского интерфейса в приложении.

    3. Задайте исходный размер внутренних дочернее окно, содержащихся в **DesktopWindowXamlSource**. По умолчанию этот внутренний дочернего окна имеет значение ширину и высоту 0. Если вы не зададите размер окна, все элементы управления UWP, добавляемый в **DesktopWindowXamlSource** не будут видны. Для доступа к внутренней дочернего окна в **DesktopWindowXamlSource**, используйте свойство **WindowHandle** **IDesktopWindowXamlSourceNative** интерфейса. Следующие примеры использовать функцию [SetWindowPos интерфейса](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) , чтобы установить размер окна.

    Ниже приведены некоторые примеры кода, демонстрирующие процесс.

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
    // that will act as the parent UI elemnt for your XAML island. It also assumes
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

3. Задайте **Windows.UI.Xaml.UIElement** требуется разместить свойству [**Content**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) **DesktopWindowXamlSource** объекта. В следующем примере задается [**Windows.UI.Xaml.Controls.Grid**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid) с именем ```myGrid``` свойству **Content** .

   ```cppwinrt
   desktopWindowXamlSource.Content(myGrid);
   ```

   ```csharp
   desktopWindowXamlSource.Content = myGrid;
   ```

Полные примеры, демонстрирующие эти задачи в контексте рабочий пример приложения см. следующие файлы кода.

  * **C++ Win32:** См. в разделе файла [Main.cpp](https://github.com/Microsoft/Windows-appsample-Xaml-Hosting/blob/master/XamlHostingSample/Main.cpp) в образце [XamlHostingSample](https://github.com/Microsoft/Windows-appsample-Xaml-Hosting) или [Desktop.cpp](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/Desktop.cpp) в образце [XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32) .
  * **WPF:** См. в файлах [WindowsXamlHostBase.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHost.cs) в наборе средств сообщества Windows.  
  * **Windows Forms:** См. в файлах [WindowsXamlHostBase.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHost.cs) в наборе средств сообщества Windows.


## <a name="how-to-host-custom-uwp-xaml-controls"></a>Для пользовательских узла UWP XAML элементов управления

> [!IMPORTANT]
> В настоящее время пользовательских элементов управления UWP XAML сторонних производителей поддерживаются только в C# WPF и Windows Forms. Таким образом можно выполнить компиляцию от его в своем приложении вам потребуется исходный код для элементов управления.

Если вы хотите разместить пользовательский элемент управления UWP XAML (элемент управления, которые вы определяете самостоятельно или элемент управления, предоставляемые от стороннего разработчика), необходимо выполнить следующие дополнительные задачи в дополнение к процесс, описанный в [предыдущем разделе](#how-to-host-uwp-xaml-controls).

1. Определение пользовательского типа, который является производным от [**Windows.UI.Xaml.Application**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application) , а также реализует [**IXamlMetadataProvider**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.ixamlmetadataprovider). Этот тип выступает в качестве корневого поставщика метаданных для загрузки метаданных для настраиваемых типов UWP XAML в сборках в текущем каталоге приложения.

    В следующем фрагменте показано, как это сделать см. в файле кода [XamlApplication.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Win32.UI.XamlHost/XamlApplication.cs) в наборе средств сообщества Windows. Этот файл является частью общих классов, реализующих **WindowsXamlHost** для WPF и Windows Forms, который поможет показывают, как использовать XAML UWP, API в этих типов приложений размещения.

2. Вызовите метод [**GetXamlType**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.ixamlmetadataprovider.getxamltype) вашего поставщика корневой метаданных присваивается имя типа элемента управления UWP XAML (это могут быть заданы в коде во время выполнения, или вы можете выбрать включите этот параметр назначить в окне Свойства Visual Studio).

    В следующем фрагменте показано, как это сделать см. в файле кода [UWPTypeFactory.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Win32.UI.XamlHost/UWPTypeFactory.cs) в наборе средств сообщества Windows. Этот файл является частью общих классов, реализующих **WindowsXamlHost** для WPF и Windows Forms.

3. Интегрировать исходный код для пользовательского элемента управления UWP XAML в решение приложения узла, создание пользовательского элемента управления и использовать его в приложении, ниже [инструкциям](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost#add-a-custom-uwp-control).

## <a name="how-to-handle-keyboard-focus-navigation"></a>Как обрабатывать перемещение фокуса с помощью клавиатуры

Когда пользователь переходит по элементам пользовательского интерфейса в приложении с помощью клавиатуры (например, нажатие клавиши **Tab** или направление и клавиш со стрелками), вам потребуется программного перемещения фокуса в и из объекта **DesktopWindowXamlSource** . Навигация с помощью клавиатуры пользователя по достижении **DesktopWindowXamlSource**, перемещают фокус на первый объект [**Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) в порядке перехода для пользовательского интерфейса продолжать переместить фокус на следующий ** Windows.UI.Xaml.UIElement** объектов, как пользователь циклический перебор элементов, а затем перемещают фокус обратно за пределы **DesktopWindowXamlSource** и в родительский элемент пользовательского интерфейса.  

Размещение API UWP XAML предоставляет несколько типов и членов, можно выполнить следующие задачи.

1. Когда навигацию с помощью клавиатуры переходит в вашем **DesktopWindowXamlSource**, события [**GotFocus**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.gotfocus) . Обрабатывать это событие и программного перемещения фокуса на первый размещенного **Windows.UI.Xaml.UIElement** с помощью метода [**NavigateFocus**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.navigatefocus) .

2. Когда пользователь находится на последней фокусируемый элемент в вашем **DesktopWindowXamlSource** и нажимает клавишу **Tab** или клавиши со стрелкой, возникает событие [**TakeFocusRequested**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.takefocusrequested) . Обрабатывать это событие и программными средствами переместить фокус на следующий фокусируемый элемент в основное приложение. Например в приложении WPF, где размещена **DesktopWindowXamlSource** в [**System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost), можно использовать метод [**MoveFocus**](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.movefocus) для передачи фокус на следующий фокусируемый элемент в основное приложение.

Примеры, демонстрирующие, как это сделать в контексте рабочий пример приложения см. следующие файлы кода.
  * **WPF:** См. в файле [WindowsXamlHostBase.Focus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs) средств сообщества Windows.  
  * **Windows Forms:** См. в файле [WindowsXamlHostBase.KeyboardFocus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs) средств сообщества Windows.

## <a name="how-to-handle-layout-changes"></a>Как обрабатывать изменения макета

Когда пользователь изменяет размер родительского элемента пользовательского интерфейса, необходимо обрабатывать все изменения необходимые макета, убедитесь, что элементы управления UWP отображения должным образом. Вот некоторые важные сценарии, которые необходимо учитывать.

1. Если необходимо получить размер прямоугольную область, в соответствии с **Windows.UI.Xaml.UIElement** , находящихся на **DesktopWindowXamlSource**родительский элемент пользовательского интерфейса, вызовите метод [**Measure**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) **Windows.UI.Xaml.UIElement **. Пример.
    * В приложениях WPF можно выполнить это из метода [**MeasureOverride**](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.measureoverride) [**HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) , на котором размещается **DesktopWindowXamlSource**.
    * В приложение Windows Forms можно выполнить это из метода [**GetPreferredSize**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.getpreferredsize) элемента [**управления**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , на котором размещается **DesktopWindowXamlSource**.

2. Если размер изменения родительский элемент пользовательского интерфейса, вызовите метод [**Arrange**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) **Windows.UI.Xaml.UIElement** корневого элемента, размещении на **DesktopWindowXamlSource**. Пример.
    * В приложениях WPF можно выполнить это из метода [**ArrangeOverride**](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.arrangeoverride) [**HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) объекта, на котором размещается **DesktopWindowXamlSource**.
    * В приложение Windows Forms можно выполнить это в обработчике событий [**SizeChanged**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.sizechanged) элемента [**управления**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , на котором размещается **DesktopWindowXamlSource**.

Примеры, демонстрирующие, как это сделать в контексте рабочий пример приложения см. следующие файлы кода.
  * **WPF:** См. в файле [WindowsXamlHost.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) средств сообщества Windows.  
  * **Windows Forms:** См. в файле [WindowsXamlHost.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) средств сообщества Windows.

## <a name="how-to-handle-dpi-changes"></a>Как обрабатывать изменения DPI

Если вы будете обрабатывать изменения DPI в окне, на котором размещается UWP управления (например, если пользователь перетащит окно между мониторы с экрана точек на ДЮЙМ), вам потребуется настроить элемент управления UWP с помощью преобразования отрисовки, прослушивать изменения DPI в приложении и обновляет положение окна и отрисовки преобразование элемента управления UWP в ответ на изменения DPI.

Ниже приведены основные один из способов для обработки этого процесса в контексте приложения C++ Win32. Полный пример см. в разделе [Desktop.cpp](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/Desktop.cpp) и [Desktop.h](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/Desktop.h) файлы кода в примере [XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32) на GitHub.

1. Ведение объект [**ScaleTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform) в вашем приложении и назначьте его метод [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform) элемента управления UWP. Следующий пример делает это для элемента управления [**Windows.UI.Xaml.Controls.Grid**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid) в приложении C++ Win32.

    ```cppwinrt
    // Private fields maintained by your app, such as in a window class you have defined.
    Windows::UI::Xaml::Media::ScaleTransform m_scale;
    Windows::UI::Xaml::Controls::Grid m_rootGrid;

    // Code that runs during initialization, such as the constructor for a window class you have defined.
    m_rootGrid.RenderTransform(m_scale);
    ```

2. В функции [**WindowProc**](https://msdn.microsoft.com/library/windows/desktop/ms633573.aspx) прослушивать [**WM_DPICHANGED**](https://docs.microsoft.com/windows/desktop/hidpi/wm-dpichanged) сообщение. В ответ на это сообщение:
    * Используйте функцию [**SetWindowPos интерфейса**](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) для изменения размера окна, которое содержит элемент управления UWP прямоугольник, переданный в сообщение.
    * Обновите коэффициенты масштабирования осей x и y объекта **ScaleTransform** согласно новое значение DPI.
    * Внести любые необходимые изменения внешнего вида и макета элемента управления UWP. В следующем примере кода настраивает [**Padding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid.padding) размещенного элемента управления **Windows.UI.Xaml.Controls.Grid** в ответ на изменения DPI.

    ```cppwinrt
    LRESULT HandleDpiChange(HWND hWnd, WPARAM wParam, LPARAM lParam)
    {
        HWND hWndStatic = GetWindow(hWnd, GW_CHILD);
        if (hWndStatic != nullptr)
        {
            UINT uDpi = HIWORD(wParam);

            // Resize the window
            auto lprcNewScale = reinterpret_cast<RECT*>(lParam);

            SetWindowPos(hWnd, nullptr, lprcNewScale->left, lprcNewScale->top,
                lprcNewScale->right - lprcNewScale->left, lprcNewScale->bottom - lprcNewScale->top,
                SWP_NOZORDER | SWP_NOACTIVATE);

            NewScale(uDpi);
          }
          return 0;
    }

    void NewScale(UINT dpi) {

        auto scaleFactor = (float)dpi / 100;

        if (m_scale != nullptr) {
            m_scale.ScaleX(scaleFactor);
            m_scale.ScaleY(scaleFactor);
        }

        ApplyCorrection(scaleFactor);
    }

    void ApplyCorrection(float scaleFactor) {
        float rightCorrection = (m_rootGrid.Width() * scaleFactor - m_rootGrid.Width()) / scaleFactor;
        float bottomCorrection = (m_rootGrid.Height() * scaleFactor - m_rootGrid.Height()) / scaleFactor;

        m_rootGrid.Padding(Windows::UI::Xaml::ThicknessHelper::FromLengths(0, 0, rightCorrection, bottomCorrection));
    }
    ```

2. Для настройки приложения следует учитывать DPI на мониторе, добавьте [манифест сборки рядом друг с другом](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) в проект и задайте ```<dpiAwareness>``` элемент ```PerMonitorV2```. Дополнительные сведения об этом значении см. описание [**DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2**](https://docs.microsoft.com/windows/desktop/hidpi/dpi-awareness-context).

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

    Полный пример манифест сборки рядом друг с другом см. в файле [XamlIslandsWin32.exe.manifest](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/XamlIslandsWin32.exe.manifest) в образце [XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32) на GitHub.

## <a name="limitations"></a>Ограничения

API размещения XAML предоставляет те же ограничения, что все другие стандартные элементы управления XAML для Windows 10. Подробный список см. в разделе [ограничения управления узла XAML](xaml-host-controls.md#limitations).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="error-using-uwp-xaml-hosting-api-in-a-uwp-app"></a>Ошибка, с помощью XAML UWP, API размещения в приложении UWP

| Проблема | Разрешение |
|-------|------------|
| Ваше приложение получает **COMException** со следующим сообщением: «невозможно активировать DesktopWindowXamlSource. Этот тип не может использоваться в приложении UWP.» или «невозможно активировать WindowsXamlManager. Этот тип не может использоваться в приложении UWP.» | Эта ошибка указывает, вы хотите использовать XAML UWP, API размещения (в частности, вы пытаетесь экземпляры типов, [**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) или [**WindowsXamlManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) ) в приложении UWP. XAML UWP, API размещения предназначена только для использования в классических приложений, не UWP, такие как приложения WPF, Windows Forms и C++ Win32. |

### <a name="error-attaching-to-a-window-on-a-different-thread"></a>Присоединение к окно в другом потоке ошибки

| Проблема | Разрешение |
|-------|------------|
| Ваше приложение получает **COMException** со следующим сообщением: «метод AttachToWindow произошла ошибка, поскольку указанный HWND был создан в другом потоке.» | Эта ошибка означает, что приложение метод **IDesktopWindowXamlSourceNative.AttachToWindow** и передать его HWND окна, который был создан в другом потоке. Необходимо передать этот метод HWND окна, который был создан в том же потоке, что код, из которой вы вызываете метод. |

### <a name="error-attaching-to-a-window-on-a-different-top-level-window"></a>Ошибка, подключение к окно на разных окна верхнего уровня

| Проблема | Разрешение |
|-------|------------|
| Ваше приложение получает **COMException** со следующим сообщением: «метод AttachToWindow произошла ошибка, поскольку указанный HWND потомком другое окно верхнего уровня HWND, который ранее был передан AttachToWindow в одном потоке.» | Эта ошибка означает, что приложение метод **IDesktopWindowXamlSourceNative.AttachToWindow** и передать его HWND окна, происходящего от разных окна верхнего уровня, чем окно, которое указано в предыдущем вызов этого метода в одном потоке.</p></p>После приложение вызывает **IDesktopWindowXamlSourceNative.AttachToWindow** в определенном потоке, другие объекты [**DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) в одном потоке можно подключать только окна, расположенные потомков окно верхнего уровня переданный в первый вызов **IDesktopWindowXamlSourceNative.AttachToWindow**. Когда все объекты **DesktopWindowXamlSource** закрываются для конкретного потока, Далее **DesktopWindowXamlSource** может затем снова подключить все окно.</p></p>Чтобы решить эту проблему, либо закрыть все **DesktopWindowXamlSource** объекты, которые связаны с другими окнами верхнего уровня в этом потоке, или создать новый поток для этой **DesktopWindowXamlSource**. |

## <a name="related-topics"></a>Статьи по теме

* [Элементы управления UWP в классических приложениях](xaml-host-controls.md)
