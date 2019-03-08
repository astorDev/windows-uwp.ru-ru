---
description: В этой статье описывается размещение пользовательского интерфейса XAML UWP в приложении рабочего стола.
title: С помощью XAML UWP, интерфейс API размещения в приложении для настольных систем
ms.date: 01/11/2019
ms.topic: article
keywords: Windows 10, универсальной платформы Windows, windows forms, wpf, win32
ms.localizationpriority: medium
ms.openlocfilehash: efd7dc687b9aba2e3c07b0afefa2e4fa49b882b1
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57618839"
---
# <a name="using-the-uwp-xaml-hosting-api-in-a-desktop-application"></a>С помощью XAML UWP, интерфейс API размещения в приложении для настольных систем

> [!NOTE]
> Острова размещения API и XAML UWP XAML в настоящее время доступны в виде developer preview. Несмотря на то, что мы рекомендуем вам опробовать их в собственном коде прототип сейчас, не рекомендуется использовать их в рабочем коде в данный момент. Эти функции будут продолжать с развитием и стабилизации в будущих выпусках Windows. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.
>
> Если у вас есть отзыв о XAML о-ва, создание нового вопроса в [WindowsCommunityToolkit репозитория](https://github.com/windows-toolkit/WindowsCommunityToolkit/issues) , а также оставить свои комментарии существует. Если вы хотите отправить свой отзыв в частном порядке, вы можете отправить его в XamlIslandsFeedback@microsoft.com. Свои предложения и сценарии критически важны для нас.

Начиная с Windows 10 Insider Preview SDK сборки 17709, классических приложений не UWP (включая приложения WPF, Windows Forms и C++ Win32) можно использовать *XAML UWP, интерфейс API размещения* для размещения элементов управления универсальной платформы Windows в любой элемент пользовательского интерфейса, который связан с помощью дескриптор окна (HWND). Этот API позволяет не UWP настольных приложений для использования новейших функций пользовательского интерфейса Windows 10, которые доступны только через элементы управления универсальной платформы Windows. Например, классических приложений не UWP можно использовать этот API для размещения элементов управления универсальной платформы Windows, использующие [Fluent Design System](../design/fluent-design-system/index.md) и поддерживают [рукописного ввода Windows](../design/input/pen-and-stylus-interactions.md).

XAML UWP, интерфейс API размещения предоставляет основу для более широкого набора элементов управления, предоставляемые нами для предоставления разработчикам возможности перенести пользовательского интерфейса Fluent в не UWP настольных приложений. Этот сценарий называется *XAML о-ва*. Дополнительные сведения об этом сценарии для разработчика см. в разделе [управления универсальной платформы Windows в настольных приложениях](xaml-host-controls.md).

## <a name="is-the-uwp-xaml-hosting-api-right-for-your-desktop-application"></a>Размещается ли XAML универсальной платформы Windows API для своего настольного приложения?

XAML UWP, интерфейс API размещения предоставляет низкоуровневой инфраструктурой для размещения элементов управления универсальной платформы Windows в настольных приложениях. Некоторые типы приложений для настольных систем, имеют возможность использовать альтернативный, более удобный API-интерфейсы для достижения этой цели.  

* Если у вас есть приложение рабочего стола C++ Win32 и требуется для размещения элементов управления универсальной платформы Windows в приложении, необходимо использовать XAML UWP, интерфейс API размещения. Существуют нет альтернативы для таких приложений.

* Для приложений WPF и Windows Forms, мы рекомендуем использовать [элементов управления в оболочку](xaml-host-controls.md#wrapped-controls) и [элементы управления ведущего приложения](xaml-host-controls.md#host-controls) в наборе средств сообщества Windows вместо XAML UWP, интерфейс API размещения. Эти элементы управления использовать XAML UWP, интерфейс API размещения внутренним образом и обеспечивают более простую рабочую среду разработки. Тем не менее можно использовать XAML UWP, размещение API непосредственно в эти типы приложений, если вы выберете.

## <a name="related-samples"></a>Связанные примеры

Способ использования XAML UWP, интерфейс API размещения в коде зависит от типа приложения, приложения и других факторов. Чтобы продемонстрировать способы использования этого API в рамках всего приложения, эта статья относится к коду из приведенные ниже примеры.

### <a name="c-win32"></a>C++ Win32

Существует несколько примеров на GitHub, демонстрирующие способы использования XAML UWP, размещение API в приложении C++ Win32:

  * [XamlHostingSample](https://github.com/Microsoft/Windows-appsample-Xaml-Hosting). В этом примере демонстрируется добавление UWP [InkCanvas](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas), [InkToolbar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar), и [MediaPlayerElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement) элементы управления для приложения C++ Win32.
  * [XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32). В этом примере показано, как добавить несколько основных элементов управления универсальной платформы Windows для приложения C++ Win32 и обрабатывать изменения DPI.

### <a name="wpf-and-windows-forms"></a>WPF и Windows Forms

[WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) элемент управления в наборе средств сообщества Windows работает как пример ссылку с помощью универсальной платформы Windows, интерфейс API размещения в приложениях WPF и Windows Forms. Исходный код доступен в следующих расположениях:

  * Для WPF-версии элемента управления [Перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Wpf.UI.XamlHost). WPF-версии является производным от [ **System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost).
  * Для версии Windows Forms элемента управления [Перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Forms.UI.XamlHost). Версия Windows Forms является производным от [ **System.Windows.Forms.Control**](https://docs.microsoft.com/dotnet/api/system.windows.forms.control).

## <a name="prerequisites"></a>Предварительные условия

XAML UWP, интерфейс API размещения имеет эти условия.

* Сборки Windows 10 Insider Preview 17709 (или более поздней) и соответствующие сборки Insider Preview пакета SDK для Windows. Так как это совершенствующаяся функция, для получения наилучших результатов мы рекомендуем использовать последнюю доступную версию сборки.

* Чтобы использовать XAML UWP, размещение API в приложения для настольных компьютеров, необходимо настроить проект таким образом, можно вызывать API-интерфейсов универсальной платформы Windows.

    * **C++ Win32:** Мы рекомендуем настроить проект для использования [C + +/ WinRT](../cpp-and-winrt-apis/index.md). Инструкции см. в разделе [изменить проект приложения Windows Desktop Добавление C + +/ WinRT поддержка](/windows/uwp/cpp-and-winrt-apis/get-started#modify-a-windows-desktop-application-project-to-add-cwinrt-support).

    * **Windows Forms и WPF:** Выполните [эти инструкции](../porting/desktop-to-uwp-enhance.md).

## <a name="architecture-of-xaml-islands"></a>Архитектура XAML о-ва

Включает в себя XAML UWP, интерфейс API размещения [ **DesktopWindowXamlSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource), [ **WindowsXamlManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager)и других связанных типов в [ **Windows.UI.Xaml.Hosting** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting) пространства имен. Этот API используется настольных приложений отображать элементы управления универсальной платформы Windows и выполнять маршрутизацию навигации с фокусом клавиатуры в и из нее элементы. Классические приложения также можно изменить размер и положение элементов управления универсальной платформы Windows при необходимости.

При создании островам XAML, с помощью XAML, интерфейс API размещения в настольном приложении, имеется следующая иерархия объектов:

* На базовом уровне — это элемент пользовательского интерфейса в приложении, где будет размещаться остров XAML. Этот элемент пользовательского интерфейса должен иметь дескриптор окна (HWND). Примеры элементов пользовательского интерфейса, в которых вы можете разместить островам XAML [ **System.Windows.Interop.HwndHost** ](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) для приложений WPF [ **System.Windows.Forms.Control** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) для приложений Windows Forms и [окно](https://docs.microsoft.com/windows/desktop/winmsg/about-windows) для приложений C++ Win32.

* На следующем уровне — **DesktopWindowXamlSource** объекта. Этот объект предоставляет инфраструктуру для размещения остров XAML. Для создания этого объекта и подключите его к родительскому элементу пользовательского интерфейса отвечает пользовательский код.

* При создании **DesktopWindowXamlSource**, этот объект автоматически создает собственный дочернее окно для размещения элемента управления универсальной платформы Windows. Этот собственный дочернее окно главным образом абстрагируется от кода, но можно получить доступ к его дескриптор (HWND), при необходимости.

* Наконец на верхнем уровне — элемент управления универсальной платформы Windows, который необходимо разместить в приложении рабочего стола. Это может быть любой объект универсальной платформы Windows, который является производным от [ **Windows.UI.Xaml.UIElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), включая любой элемент управления универсальной платформы Windows, предоставляемый пакетом SDK для Windows, а также собственные пользовательские элементы управления.

На следующей схеме показана иерархия объектов в островам XAML.

![Архитектура DesktopWindowXamlSource](images/xaml-hosting-api-rev2.png)

## <a name="how-to-host-uwp-xaml-controls"></a>Как разместить элементы управления XAML UWP

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

    1. Создание **DesktopWindowXamlSource** объекта и приведите его к **IDesktopWindowXamlSourceNative** COM-интерфейса. Этот интерфейс объявляется в ```windows.ui.xaml.hosting.desktopwindowxamlsource.h``` файл заголовка в пакете Windows SDK. В проекте C++ Win32 можно непосредственно ссылаться на файл заголовка. В проекте WPF или Windows Forms, вам потребуется объявлять этот интерфейс в коде приложения с помощью [ **ComImport** ](https://docs.microsoft.com/dotnet/api/system.runtime.interopservices.comimportattribute) атрибута. Убедитесь, что в объявлении интерфейса точно соответствует объявление интерфейса в ```windows.ui.xaml.hosting.desktopwindowxamlsource.h```.

    2. Вызовите **AttachToWindow** метод **IDesktopWindowXamlSourceNative** интерфейса и передает дескриптор окна родительского элемента пользовательского интерфейса в приложении.

    3. Установить исходный размер внутреннего дочернего окна, содержащихся в **DesktopWindowXamlSource**. По умолчанию этот внутренний дочернее окно присваивается ширину и высоту 0. Если не задан размер окна, все элементы управления универсальной платформы Windows, добавляемый **DesktopWindowXamlSource** не будут видны. Для доступа к внутренней дочернее окно в **DesktopWindowXamlSource**, использовать **WindowHandle** свойство **IDesktopWindowXamlSourceNative** интерфейс. В следующих примерах используется [SetWindowPos](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) функцию для задания размера окна.

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

3. Задайте **Windows.UI.Xaml.UIElement** необходимо разместить в [ **содержимого** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) свойство вашей **DesktopWindowXamlSource** объекта. В следующем примере задается [ **Windows.UI.Xaml.Controls.Grid** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid) с именем ```myGrid``` для **содержимого** свойство.

   ```cppwinrt
   desktopWindowXamlSource.Content(myGrid);
   ```

   ```csharp
   desktopWindowXamlSource.Content = myGrid;
   ```

Полные примеры, демонстрирующие эти задачи в контексте рабочий пример приложения см. следующие файлы кода:

  * **C++ Win32:** См. в разделе [Main.cpp](https://github.com/Microsoft/Windows-appsample-Xaml-Hosting/blob/master/XamlHostingSample/Main.cpp) файл в [XamlHostingSample](https://github.com/Microsoft/Windows-appsample-Xaml-Hosting) образец или [Desktop.cpp](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/Desktop.cpp) файл [XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32) образца.
  * **WPF:** См. в разделе [WindowsXamlHostBase.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHost.cs) файлы в наборе средств сообщества Windows.  
  * **Windows Forms:** См. в разделе [WindowsXamlHostBase.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHost.cs) файлы в наборе средств сообщества Windows.


## <a name="how-to-host-custom-uwp-xaml-controls"></a>Для пользовательских узлов XAML UWP элементов управления

> [!IMPORTANT]
> В настоящее время пользовательские элементы управления XAML UWP сторонних производителей, поддерживаются только в C# приложения WPF и Windows Forms. Должен иметь исходный код для элементов управления, чтобы к нему можно скомпилировать в приложении.

Если вы хотите разместить пользовательский элемент управления XAML UWP (элемент управления, можно определить самостоятельно или управление, предоставляемое сторонним), необходимо выполнить следующие дополнительные задачи в дополнение к процессу, описанному в [выше](#how-to-host-uwp-xaml-controls).

1. Определение пользовательского типа, который является производным от [ **Windows.UI.Xaml.Application** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application) и также реализует [ **IXamlMetadataProvider**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.ixamlmetadataprovider). Этот тип выступает в качестве корневого поставщика метаданных для загрузки метаданных для пользовательских типов XAML UWP в сборках в текущем каталоге приложения.

    Пример, в котором показано, как это сделать, см. в разделе [XamlApplication.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Win32.UI.XamlHost/XamlApplication.cs) файл кода в наборе средств сообщества Windows. Этот файл является частью общей реализации **WindowsXamlHost** классы для WPF и Windows Forms, которые помогают продемонстрировать способы использования XAML UWP, размещение API в этих типов приложений.

2. Вызовите [ **GetXamlType** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.ixamlmetadataprovider.getxamltype) метод поставщика метаданных корневой при назначении имя типа элемента управления XAML универсальной платформы Windows (это может назначаться в коде во время выполнения, или можно выбрать для этого необходимо включить назначить в окне Properties в Visual Studio).

    Пример, в котором показано, как это сделать, см. в разделе [UWPTypeFactory.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Win32.UI.XamlHost/UWPTypeFactory.cs) файл кода в наборе средств сообщества Windows. Этот файл является частью общей реализации **WindowsXamlHost** классы для WPF и Windows Forms.

3. Интегрировать в решение приложения узла исходный код для пользовательского элемента управления XAML UWP, построения пользовательского элемента управления и использовать его в приложении, следуя [эти инструкции](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost#add-a-custom-uwp-control).

## <a name="how-to-handle-keyboard-focus-navigation"></a>Как осуществлять навигацию фокуса клавиатуры

Когда пользователь переходит по элементам пользовательского интерфейса в приложении с помощью клавиатуры (например, нажав **вкладке** или направление/стрелка), вам потребуется программное перемещение фокуса в  **DesktopWindowXamlSource** объекта. При достижении пользователя с помощью клавиатуры **DesktopWindowXamlSource**, перемещение фокуса в первый [ **Windows.UI.Xaml.UIElement** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) объектом в порядке навигации для пользовательского интерфейса, по-прежнему переместить фокус на следующее **Windows.UI.Xaml.UIElement** объекты, как пользователь выполняет циклический переход по элементам, а затем повторно перемещения фокуса из **DesktopWindowXamlSource**и в родительский элемент пользовательского интерфейса.  

XAML UWP, интерфейс API размещения предоставляет несколько типов и членов для выполнения этих задач.

1. При навигации с помощью клавиатуры переходит к **DesktopWindowXamlSource**, [ **Получение фокуса** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.gotfocus) события. Обработайте это событие и программное перемещение фокуса к первому размещенных **Windows.UI.Xaml.UIElement** с помощью [ **NavigateFocus** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.navigatefocus) метод.

2. Если пользователь находится на последний элемент, способному получать фокус в вашей **DesktopWindowXamlSource** и нажимает клавишу **вкладке** ключ или клавиша со стрелкой, [ **TakeFocusRequested** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.takefocusrequested) события. Обработайте это событие и программное перемещение фокуса к следующему элементу, способному получать фокус в ведущем приложении. Например, в приложении WPF где **DesktopWindowXamlSource** размещается в [ **System.Windows.Interop.HwndHost**](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost), можно использовать [  **MoveFocus** ](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.movefocus) метод для передачи фокуса к следующему элементу, способному получать фокус в ведущем приложении.

Примеры, демонстрирующие, как это сделать в контексте рабочий пример приложения см. следующие файлы кода:
  * **WPF:** См. в разделе [WindowsXamlHostBase.Focus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs) файла в наборе средств сообщества Windows.  
  * **Windows Forms:** См. в разделе [WindowsXamlHostBase.KeyboardFocus.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs) файла в наборе средств сообщества Windows.

## <a name="how-to-handle-layout-changes"></a>Как обрабатывать изменения макета

Когда пользователь изменяет размер родительского элемента пользовательского интерфейса, необходимо обрабатывать любые необходимые макета изменения и убедиться, что отображения как предполагается, что элементы управления универсальной платформы Windows. Ниже приведено несколько важных сценариев, которые следует учитывать.

1. Когда родительский элемент пользовательского интерфейса необходимо получить размер прямоугольной области, в соответствии **Windows.UI.Xaml.UIElement** , находящихся на **DesktopWindowXamlSource**, вызовите [ **Мер** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) метод **Windows.UI.Xaml.UIElement**. Например:
    * В приложении WPF может сделать это из [ **MeasureOverride** ](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.measureoverride) метод [ **HwndHost** ](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) , на котором размещена  **DesktopWindowXamlSource**.
    * В приложении Windows Forms это можно сделать из [ **GetPreferredSize** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.getpreferredsize) метод [ **управления** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , на котором размещена **DesktopWindowXamlSource**.

2. При изменении размера родительского элемента пользовательского интерфейса, вызовите [ **расположение** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) метод корневого **Windows.UI.Xaml.UIElement** , находящихся на  **DesktopWindowXamlSource**. Например:
    * В приложении WPF может сделать это из [ **ArrangeOverride** ](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.arrangeoverride) метод [ **HwndHost** ](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) объекта, на котором размещена **DesktopWindowXamlSource**.
    * В приложении Windows Forms это можно сделать из обработчика для [ **SizeChanged** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.sizechanged) событие [ **управления** ](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , на котором размещена **DesktopWindowXamlSource**.

Примеры, демонстрирующие, как это сделать в контексте рабочий пример приложения см. следующие файлы кода:
  * **WPF:** См. в разделе [WindowsXamlHost.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) файла в наборе средств сообщества Windows.  
  * **Windows Forms:** См. в разделе [WindowsXamlHost.Layout.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) файла в наборе средств сообщества Windows.

## <a name="how-to-handle-dpi-changes"></a>Как обрабатывать изменения DPI

Если вы хотите обрабатывать изменения DPI в окне, на котором размещены ваши UWP управления (например, если пользователь перетаскивает окна между мониторами с различных разрешений экранов точек на ДЮЙМ), необходимо настроить элемент управления универсальной платформы Windows с преобразование прорисовки, отслеживать изменения DPI в приложении и обновить положение окна и просмотру преобразование элемента управления универсальной платформы Windows в ответ на изменения DPI.

Ниже приведен один из способов обработки этого процесса в контексте приложения C++ Win32. Полный пример см. в разделе [Desktop.cpp](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/Desktop.cpp) и [Desktop.h](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/Desktop.h) файлы в кода [XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32) примером на сайте GitHub.

1. Ведение [ **ScaleTransform** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform) объекта в приложении и назначьте его [ **RenderTransform** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform) метод вашего элемента управления универсальной платформы Windows. Следующий пример делает это для [ **Windows.UI.Xaml.Controls.Grid** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid) элемента управления в приложении C++ Win32.

    ```cppwinrt
    // Private fields maintained by your app, such as in a window class you have defined.
    Windows::UI::Xaml::Media::ScaleTransform m_scale;
    Windows::UI::Xaml::Controls::Grid m_rootGrid;

    // Code that runs during initialization, such as the constructor for a window class you have defined.
    m_rootGrid.RenderTransform(m_scale);
    ```

2. В вашей [ **WindowProc** ](https://msdn.microsoft.com/library/windows/desktop/ms633573.aspx) функции, прослушивать [ **WM_DPICHANGED** ](https://docs.microsoft.com/windows/desktop/hidpi/wm-dpichanged) сообщения. В ответ на это сообщение:
    * Используйте [ **SetWindowPos** ](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) функции, чтобы изменить размер окна, которое содержит элемент управления универсальной платформы Windows в прямоугольник, переданными в сообщение.
    * Обновления, осей x и y масштабирование коэффициентов вашей **ScaleTransform** в соответствии с новым значением точек на ДЮЙМ.
    * Внесите необходимые изменения, чтобы внешний вид и макет элемента управления универсальной платформы Windows. В следующем примере кода задается [ **Padding** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.grid.padding) размещенного **Windows.UI.Xaml.Controls.Grid** элемента управления в ответ на изменения DPI.

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

2. Чтобы настроить приложение, которые следует учитывать DPI для каждого монитора, добавьте [манифест сборки side-by-side](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) для проекта, а также набор ```<dpiAwareness>``` элемент ```PerMonitorV2```. Дополнительные сведения об этом значении см. в описании для [ **DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2**](https://docs.microsoft.com/windows/desktop/hidpi/dpi-awareness-context).

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

    Для манифеста сборки side-by-side полный пример см. в разделе [XamlIslandsWin32.exe.manifest](https://github.com/clarkezone/cppwinrt/blob/master/Desktop/XamlIslandsWin32/XamlIslandsWin32.exe.manifest) файл [XamlIslands32](https://github.com/clarkezone/cppwinrt/tree/master/Desktop/XamlIslandsWin32) примером на сайте GitHub.

## <a name="limitations"></a>Ограничения

XAML, интерфейс API размещения совместно использует те же ограничения, что все другие типы элементов управления ведущего приложения XAML для Windows 10. Подробный список см. в разделе [ограничения управления узла XAML](xaml-host-controls.md#limitations).

## <a name="troubleshooting"></a>Поиск и устранение неисправностей

### <a name="error-using-uwp-xaml-hosting-api-in-a-uwp-app"></a>Ошибка использования XAML UWP, интерфейс API размещения в приложении UWP

| Проблема | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: «Не удается активировать DesktopWindowXamlSource. Этот тип не может использоваться в приложении универсальной платформы Windows.» или «не удается активировать WindowsXamlManager. Этот тип не может использоваться в приложении универсальной платформы Windows.» | Эта ошибка означает, вы пытаетесь использовать XAML UWP, интерфейс API размещения (в частности, вы пытаетесь создать экземпляр [ **DesktopWindowXamlSource** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) или [  **WindowsXamlManager** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) типы) в приложении UWP. XAML UWP, интерфейс API размещения предназначен только для использования в классических приложениях не универсальной платформы Windows, таких как приложения WPF, Windows Forms и C++ Win32. |

### <a name="error-attaching-to-a-window-on-a-different-thread"></a>Ошибка при подключении к окну в другом потоке

| Проблема | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: «Метод AttachToWindow ошибка, так как был создан заданный HWND в другом потоке». | Эта ошибка означает, что приложение будет вызывать **IDesktopWindowXamlSourceNative.AttachToWindow** метод и передать ему HWND окна, который был создан в другом потоке. Этот метод необходимо передать HWND окна, который был создан в том же потоке, что и код, из которого вы вызываете метод. |

### <a name="error-attaching-to-a-window-on-a-different-top-level-window"></a>Ошибка при подключении к окну на другое окно верхнего уровня

| Проблема | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: «Метод AttachToWindow ошибка, так как заданный HWND потомком другое окно верхнего уровня HWND, который ранее был передан AttachToWindow в одном потоке». | Эта ошибка означает, что приложение будет вызывать **IDesktopWindowXamlSourceNative.AttachToWindow** метод и передать ему HWND окна, происходящего от разных окно верхнего уровня, чем период, указанный в Предыдущий вызов этого метода в том же потоке.</p></p>После в приложении вызывается **IDesktopWindowXamlSourceNative.AttachToWindow** в определенном потоке, все остальные [ **DesktopWindowXamlSource** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) объекты на том же поток можно подключать только для windows, которые являются потомками же окно верхнего уровня, который был передан в первом вызове **IDesktopWindowXamlSourceNative.AttachToWindow**. Когда все **DesktopWindowXamlSource** объекты будут закрыты для конкретного потока, следующего **DesktopWindowXamlSource** может затем повторно добавить в любом окне.</p></p>Чтобы устранить эту проблему, либо закрыть все **DesktopWindowXamlSource** объектов, которые привязаны к другим окнам верхнего уровня в данном потоке, или создать новый поток для данного **DesktopWindowXamlSource**. |

## <a name="related-topics"></a>Статьи по теме

* [Элементы управления универсальной платформы Windows в настольных приложениях](xaml-host-controls.md)
